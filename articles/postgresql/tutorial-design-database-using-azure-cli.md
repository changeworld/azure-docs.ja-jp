---
title: "Azure CLI を使用して最初の Azure Database for PostgreSQL を設計する | Microsoft Docs"
description: "このチュートリアルでは、Azure CLI を使用して最初の Azure Database for PostgreSQL を設計する方法を説明します。"
services: postgresql
author: SaloniSonpal
ms.author: salonis
manager: jhubbard
editor: jasonh
ms.assetid: 
ms.service: postgresql-database
ms.custom: tutorial
ms.tgt_pltfrm: portal
ms.devlang: na
ms.topic: tutorial
ms.date: 05/10/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: fbac141243f0f36aab1a2ef9f28b4e107fd2d390
ms.contentlocale: ja-jp
ms.lasthandoff: 05/10/2017

---
# <a name="design-your-first-azure-database-for-postgresql-using-azure-cli"></a>Azure CLI を使用して最初の Azure Database for PostgreSQL を設計する 
このチュートリアルでは、Azure CLI (コマンド ライン インターフェイス) とその他のユーティリティを使用して、次のことを行う方法を説明します。
> [!div class="checklist"]
> * Azure Database for PostgreSQL の作成
> * サーバーのファイアウォールの構成
> * [**psql**](https://www.postgresql.org/docs/9.6/static/app-psql.html) ユーティリティを使用したデータベースの作成
> * サンプル データの読み込み
> * データのクエリを実行する
> * データの更新
> * データの復元

[!INCLUDE [sample-cli-install](../../includes/sample-cli-install.md)]

## <a name="log-in-to-azure"></a>Azure へのログイン

[az login](/cli/azure/#login) コマンドで Azure サブスクリプションにログインし、画面上の指示に従います。
```azurecli
az login
```

複数のサブスクリプションをお持ちの場合は、リソースが存在するか、課金の対象となっている適切なサブスクリプションを選択してください。 [az account set](/cli/azure/account#set) コマンドを使用して、アカウントの特定のサブスクリプション ID を選択します。
```azurecli
az account set --subscription 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>リソース グループの作成

[az group create](/cli/azure/group#create) コマンドで [Azure リソース グループ](../azure-resource-manager/resource-group-overview.md)を作成します。 リソース グループとは、複数の Azure リソースをまとめてデプロイ、管理する際の論理コンテナーです。 次の例では、`myresourcegroup` という名前のリソース グループを `westus` の場所に作成します。
```azurecli
az group create --name myresourcegroup --location westus
```

## <a name="create-an-azure-database-for-postgresql-server"></a>Azure Database for PostgreSQL サーバーの作成

**az postgres server create** コマンドを使用して、[Azure Database for PostgreSQL サーバー](overview.md)を作成します。 サーバーには、ひとまとめにして管理される一連のデータベースが含まれています。 

次の例では、サーバー管理者ログイン `mylogin` を使用して、リソース グループ `myresourcegroup` に `mypgserver-20170401` という名前のサーバーを作成します。 サーバーの名前は DNS 名にマップするため、Azure でグローバルに一意である必要があります。 `<server_admin_password>` は独自の値に置き換えます。
```azurecli
az postgres server create --resource-group myresourcegroup --name mypgserver-20170401 --location westus --admin-user mylogin --admin-password <server_admin_password> --performance-tier Basic --compute-units 50 --version 9.6
```

> [!IMPORTANT]
> ここで指定するサーバー管理者ログインとパスワードは、このクイック スタートの後の方でサーバーとそのデータベースにログインするために必要です。 後で使用するために、この情報を覚えておくか、記録しておきます。

既定では、**postgres** データベースがサーバーに作成されます。 [postgres](https://www.postgresql.org/docs/9.6/static/app-initdb.html) は既定のデータベースで、ユーザー、ユーティリティ、およびサード パーティ製のアプリケーションが使用するためのものです。 


## <a name="configure-a-server-level-firewall-rule"></a>サーバーレベルのファイアウォール規則の構成

**az postgres server firewall-rule create** コマンドで、Azure PostgreSQL サーバーレベルのファイアウォール規則を作成します。 サーバーレベルのファイアウォール規則により、[psql](https://www.postgresql.org/docs/9.2/static/app-psql.html) や [PgAdmin](https://www.pgadmin.org/) などの外部アプリケーションが、Azure PostgreSQL サービスのファイアウォールを経由してサーバーに接続できるようになります。 

ネットワークからの接続が可能な IP 範囲を指定するファイアウォール規則を設定することができます。 次の例では、**az postgres server firewall-rule create** を使用して、IP アドレスの範囲を指定するファイアウォール規則 `AllowAllIps` を作成します。 すべての IP アドレスを開放するには、開始 IP アドレスとして 0.0.0.0 を、終了アドレスとして 255.255.255.255 を使用します。
```azurecli
az postgres server firewall-rule create --resource-group myresourcegroup --server mypgserver-20170401 --name AllowAllIps --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255
```

> [!NOTE]
> Azure PostgreSQL サーバーはポート 5432 を介して通信します。 企業ネットワーク内から接続しようとしても、ポート 5432 での送信トラフィックがネットワークのファイアウォールで禁止されている場合があります。 その場合、会社の IT 部門によってポート 5432 が開放されない限り、Azure SQL Database サーバーに接続することはできません。
>

## <a name="get-the-connection-information"></a>接続情報の取得

サーバーに接続するには、ホスト情報とアクセス資格情報を提供する必要があります。
```azurecli
az postgres server show --resource-group myresourcegroup --name mypgserver-20170401
```

結果は JSON 形式です。 **administratorLogin** と **fullyQualifiedDomainName** の値を書き留めておきます。
```json
{
  "administratorLogin": "mylogin",
  "fullyQualifiedDomainName": "mypgserver-20170401.postgres.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforPostgreSQL/servers/mypgserver-20170401",
  "location": "westus",
  "name": "mypgserver-20170401",
  "resourceGroup": "myresourcegroup",
  "sku": {
    "capacity": 50,
    "family": null,
    "name": "PGSQLS2M50",
    "size": null,
    "tier": "Basic"
  },
  "sslEnforcement": null,
  "storageMb": 51200,
  "tags": null,
  "type": "Microsoft.DBforPostgreSQL/servers",
  "userVisibleState": "Ready",
  "version": "9.6"
}
```

## <a name="connect-to-azure-database-for-postgresql-database-using-psql"></a>psql を使用して Azure Database for PostgreSQL に接続する

クライアント コンピューターに PostgreSQL がインストールされている場合は、[psql](https://www.postgresql.org/docs/9.6/static/app-psql.html) のローカル インスタンスを使用して Azure PostgreSQL サーバーに接続できます。 ここでは psql コマンド ライン ユーティリティを使用して、Azure PostgreSQL サーバーに接続しましょう。

1. 次の psql コマンドを実行して Azure Database for PostgreSQL に接続します。
```bash
psql --host=<servername> --port=<port> --username=<user@servername> --dbname=<dbname>
```

  たとえば次のコマンドは、アクセス資格情報を使用して、PostgreSQL サーバー **mypgserver 20170401.postgres.database.azure.com** にある既定のデータベース **postgres** に接続します。 パスワードの入力を求められたら、選択した `<server_admin_password>` を入力します。
  
  ```bash
psql --host=mypgserver-20170401.postgres.database.azure.com --port=5432 --username=mylogin@mypgserver-20170401 ---dbname=postgres
```

2.  サーバーに接続したら、プロンプトで空のデータベースを作成します。
```bash
CREATE DATABASE mypgsqldb;
```

3.  プロンプトで次のコマンドを実行し、新しく作成したデータベース **mypgsqldb** に接続を切り替えます。
```bash
\c mypgsqldb
```

## <a name="create-tables-in-the-database"></a>データベースのテーブルを作成する
Azure Database for PostgreSQL に接続する方法を説明したので、次は基本的なタスクを行う方法をいくつか説明します。

最初に、テーブルを作成してデータを読み込みます。 インベントリ情報を追跡するテーブルを作成しましょう。
```sql
CREATE TABLE inventory (
    id serial PRIMARY KEY, 
    name VARCHAR(50), 
    quantity INTEGER
);
```

次のように入力すると、新しく作成されたテーブルが一覧に表示されます。
```bash
\dt
```

## <a name="load-data-into-the-tables"></a>テーブルにデータを読み込む
テーブルを作成したので、次はデータを挿入します。 開いているコマンド プロンプト ウィンドウで、次のクエリを実行してデータ行を挿入します。
```sql
INSERT INTO inventory (id, name, quantity) VALUES (1, 'banana', 150); 
INSERT INTO inventory (id, name, quantity) VALUES (2, 'orange', 154);
```

これで、先ほど作成したテーブルにサンプル データが 2 行挿入されました。

## <a name="query-and-update-the-data-in-the-tables"></a>クエリを実行し、テーブル内のデータを更新する
次のクエリを実行して、データベース テーブルから情報を取得します。 
```sql
SELECT * FROM inventory;
```

さらに、テーブル内のデータを更新することもできます。
```sql
UPDATE inventory SET quantity = 200 WHERE name = 'banana';
```

データを取得するとき、それに応じてデータ行が更新されます。
```sql
SELECT * FROM inventory;
```

## <a name="restore-a-database-to-a-previous-point-in-time"></a>データベースを以前の状態に復元する
テーブルを誤って削除した場合を想定してください。 データの復元は容易なことではありません。 Azure Database for PostgreSQL では、過去最長 7 日間 (Basic) または 35 日間 (Standard) の任意の時点に戻り、新しいデータベースに過去のデータを復元できます。 この新しいサーバーを使用して、削除されたデータを復元することができます。 次の手順を実行して、テーブルを追加する前の状態にサンプル データベースを復元します。

復元するには、次の情報が必要です。
- **復元ポイント**: サーバーが変更される前の日時を選択します。 ソース データベースの最も古いバックアップと同じか、それよりも前の値にする必要があります。
- **対象サーバー:** 復元先の新しいサーバー名を指定します。
- **ソース サーバー:** 復元するサーバーの名前を指定します。
- **場所:** リージョンを選択することはできません。既定では、ソース サーバーと同じ場所になります。
- **価格レベル:** サーバーを復元するときは、この値を変更することはできません。 ソース サーバーと同じレベルになります。 

```azurecli
az postgres server restore --resource-group myResourceGroup --name mypgserver-20170401-restored --restore-point-in-time "2017-04-13 03:10" --source-server-name mypgserver-20170401
```

[テーブルが削除される前の時点にサーバーを復元](./howto-restore-server-portal.md)します。 異なる時点にサーバーを復元すると、[サービス レベル](./concepts-service-tiers.md)の保有期間内であれば、指定した時点の元サーバーと同じサーバー内に、新しいサーバーが複製されます。

## <a name="next-steps"></a>次のステップ
このチュートリアルでは、Azure CLI (コマンド ライン インターフェイス) とその他のユーティリティを使用して、次のことを行う方法を説明しました。
> [!div class="checklist"]
> * Azure Database for PostgreSQL の作成
> * サーバーのファイアウォールの構成
> * [**psql**](https://www.postgresql.org/docs/9.6/static/app-psql.html) ユーティリティを使用したデータベースの作成
> * サンプル データの読み込み
> * データのクエリを実行する
> * データの更新
> * データの復元

次は、Azure Portal を使用して同様のタスクを行う方法を学習しましょう。チュートリアル「[Design your first Azure Database for PostgreSQL using the Azure portal (Azure ポータルを使用して最初の Azure Database for PostgreSQL を設計する)](tutorial-design-database-using-azure-portal.md)」に進んでください。

