---
title: 名前付きレプリカのセキュリティを構成して分離されたアクセスを許可する
description: ユーザーが他のレプリカではなく、名前付きレプリカにアクセスできるようにするための、名前付きレプリカの構成と管理に関するセキュリティ上の考慮事項について説明します。
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.topic: how-to
author: yorek
ms.author: damauri
ms.reviewer: ''
ms.date: 7/27/2021
ms.openlocfilehash: 88410573dbefdfdedb4628babfffc601b19bed36
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121744864"
---
# <a name="configure-isolated-access-to-a-hyperscale-named-replica"></a>Hyperscale の名前付きレプリカへの分離されたアクセスを構成する
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

この記事では、プライマリ レプリカまたはその他の名前付きレプリカへのアクセス権を付与せずに、Azure SQL Hyperscale の[名前付きレプリカ](service-tier-hyperscale-replicas.md)へのアクセス権を付与する手順について説明します。 このシナリオでは、名前付きレプリカのリソースとセキュリティを分離できます (名前付きレプリカは、独自の計算ノードを使用して実行されます)。これは、Azure SQL Hyperscale データベースに対する読み取り専用の分離されたアクセスが必要な場合に便利です。 このコンテキストでは、分離とは、CPU とメモリがプライマリと名前付きレプリカとの間で共有されないことを意味します。名前付きレプリカで実行されるクエリでは、プライマリまたはその他のレプリカのコンピューティング リソースが使用されません。また、名前付きレプリカにアクセスするプリンシパルでは、プライマリを含む他のレプリカにアクセスできません。

## <a name="create-a-login-in-the-master-database-on-the-primary-server"></a>プライマリ サーバーの master データベースでログインを作成する

"*プライマリ*" データベースをホストしている論理サーバー上の `master` データベースで、次のように実行して、新しいログインを作成します。 独自の強力なパスワードを使用します。

```sql
create login [third-party-login] with password = 'Just4STRONG_PAZzW0rd!';
```

`sys.sql_logins` システム ビューから、作成したログインの SID の 16 進値を取得します。

```sql
select sid from sys.sql_logins where name = 'third-party-login';
```

ログインを無効にします。 これで、このログインでは、プライマリ レプリカをホストしているサーバー上のデータベースにアクセスできなくなります。

```sql
alter login [third-party-login] disable;
```

## <a name="create-a-user-in-the-primary-read-write-database"></a>プライマリ読み取り/書き込みデータベースにユーザーを作成する

ログインが作成されたら、WideWorldImporters など、データベースのプライマリ読み取り/書き込みレプリカに接続して (復元のためのサンプル スクリプトは、[Azure SQL でのデータベースの復元に関するページ](https://github.com/yorek/azure-sql-db-samples/tree/master/samples/01-restore-database)で確認できます)、そのログインのデータベース ユーザーを作成します。

```sql
create user [third-party-user] from login [third-party-login];
```

オプションのステップとして、ログインが何らかの方法で再度有効になることが懸念される場合は、データベース ユーザーが作成されたら、前の手順で作成したサーバー ログインを削除します。 プライマリ データベースをホストしている論理サーバー上の master データベースに接続して、次のように実行します。

```sql
drop login [third-party-login];
```

## <a name="create-a-named-replica-on-a-different-logical-server"></a>別の論理サーバーに名前付きのレプリカを作成する

名前付きレプリカへのアクセスの分離に使用される新しい Azure SQL 論理サーバーを作成します。 「[Azure SQL Database でのサーバーと単一データベースの作成と管理](single-database-manage.md)」にある手順に従ってください。 名前付きレプリカを作成するには、このサーバーが、プライマリ レプリカをホストするサーバーと同じ Azure リージョンに存在している必要があります。

AZ CLI を使用する場合 (例):

```azurecli
az sql server create -g MyResourceGroup -n MyNamedReplicaServer -l MyLocation --admin-user MyAdminUser --admin-password MyStrongADM1NPassw0rd!
```

次に、このサーバー上でプライマリ データベースの名前付きレプリカを作成します。 たとえば、AZ CLI を使用すると、次のようになります。

```azurecli
az sql db replica create -g MyResourceGroup -n WideWorldImporters -s MyPrimaryServer --secondary-type Named --partner-database WideWorldImporters_NR --partner-server MyNamedReplicaServer
```

## <a name="create-a-login-in-the-master-database-on-the-named-replica-server"></a>名前付きレプリカ サーバー上の master データベースでログインを作成する

前の手順で作成した名前付きレプリカをホストする論理サーバー上で `master` データベースに接続します。 プライマリ レプリカから取得した SID を使用してログインを追加します。

```sql
create login [third-party-login] with password = 'Just4STRONG_PAZzW0rd!', sid = 0x0...1234;
```

この時点では、`third-party-login` を使用しているユーザーとアプリケーションは、プライマリ レプリカではなく、名前付きレプリカに接続できます。

## <a name="grant-object-level-permissions-within-the-database"></a>データベース内でオブジェクトレベルのアクセス許可を付与する

説明に従ってログイン認証を設定したら、通常の `GRANT`、`DENY`、`REVOKE` の各ステートメントを使用して、認証またはデータベース内のオブジェクトレベルのアクセス許可を管理できます。 これらのステートメントでは、データベースで作成したユーザーの名前、またはこのユーザーをメンバーとして含むデータベース ロールを参照します。 これらのコマンドは、プライマリ レプリカで実行することに注意してください。 変更はすべてのセカンダリ レプリカに反映されますが、サーバーレベルのログインが作成された名前付きのレプリカでのみ有効になります。

既定では、新しく作成されたユーザーには最小限のアクセス許可が付与されることに注意してください (たとえば、どのユーザー テーブルにもアクセスできません)。 `third-party-user` がテーブル内のデータを読み取ることができるようにするには、`SELECT` 権限を明示的に付与する必要があります。

```sql
grant select on [Application].[Cities] to [third-party-user];
```

すべてのテーブルについて権限を個別に付与する代わりに、ユーザーを `db_datareaders` [データベース ロール](/sql/relational-databases/security/authentication-access/database-level-roles)に追加して、すべてのテーブルへの読み取りアクセスを許可することもできます。また、[スキーマ](/sql/relational-databases/security/authentication-access/create-a-database-schema)を使用して、スキーマ内のすべての既存テーブルと新しいテーブルへの[アクセスを許可](/sql/t-sql/statements/grant-schema-permissions-transact-sql)することもできます。

## <a name="test-access"></a>アクセスをテストする

任意のクライアント ツールを使用してこの構成をテストし、プライマリと名前付きのレプリカへの接続を試行できます。 たとえば、`sqlcmd` を使用すると、`third-party-login` ユーザーを使ってプライマリ レプリカへの接続を試行できます。

```
sqlcmd -S MyPrimaryServer.database.windows.net -U third-party-login -P Just4STRONG_PAZzW0rd! -d WideWorldImporters
```

この場合、ユーザーにはサーバーへの接続が許可されていないため、エラーが発生します。

```
Sqlcmd: Error: Microsoft ODBC Driver 13 for SQL Server : Login failed for user 'third-party-login'. Reason: The account is disabled.
```

名前付きレプリカへの接続の試行は成功します。

```
sqlcmd -S MyNamedReplicaServer.database.windows.net -U third-party-login -P Just4STRONG_PAZzW0rd! -d WideWorldImporters_NR
```

エラーは返されません。また、付与されているオブジェクトレベルのアクセス許可により、名前付きレプリカでクエリを実行できます。

詳細情報:

* Azure SQL 論理サーバーについては、[Azure SQL Database のサーバーに関するページ](logical-servers.md)を参照してください
* データベースへのアクセスとログインの管理については、[SQL Database のセキュリティ (データベースへのアクセスとログインのセキュリティの管理)](logins-create-manage.md) に関するページを参照してください
* データベース エンジンのアクセス許可については、[権限](/sql/relational-databases/security/permissions-database-engine)に関するページを参照してください 
* オブジェクト権限の付与については、[オブジェクト権限の許可](/sql/t-sql/statements/grant-object-permissions-transact-sql)に関するページを参照してください



