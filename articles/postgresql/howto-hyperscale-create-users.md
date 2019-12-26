---
title: ユーザーの作成 - Hyperscale (Citus) - Azure Database for PostgreSQL
description: この記事では、Azure Database for PostgreSQL - Hyperscale (Citus) と対話する新しいユーザー アカウントを作成する方法について説明します。
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: d093d4c23fcc44e7e9f3461f875607926f4b612d
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2019
ms.locfileid: "74977575"
---
# <a name="create-users-in-azure-database-for-postgresql---hyperscale-citus"></a>Azure Database for PostgreSQL - Hyperscale (Citus) でユーザーを作成する

この記事では、Hyperscale (Citus) サーバー グループ内にユーザーを作成する方法について説明します。 Azure サブスクリプションのユーザーとその特権について確認する場合は、[Azure ロール ベース アクセス制御 (RBAC) に関する記事](../role-based-access-control/built-in-roles.md)を参照するか、または[ロールのカスタマイズ方法](../role-based-access-control/custom-roles.md)について確認してください。

## <a name="the-server-admin-account"></a>サーバー管理者アカウント

新しく作成された Hyperscale (Citus) サーバー グループには、いくつかのロールが事前に定義されています。

* [既定の PostgreSQL ロール](https://www.postgresql.org/docs/current/default-roles.html)
* *azure_pg_admin*
* *postgres*
* *citus*

[PostgreSQL 製品のドキュメント](https://www.postgresql.org/docs/current/static/sql-createrole.html)で説明されているように、PostgreSQL エンジンは特権を使ってデータベース オブジェクトへのアクセスを制御します。
サーバーの管理者ユーザー *citus* は、*azure_pg_admin* ロールのメンバーです。
ただし、*postgres* (スーパー ユーザー) ロールの一部ではありません。  Hyperscale はマネージド PaaS サービスであるため、Microsoft だけがスーパー ユーザー ロールの一部になります。 *citus* ユーザーのアクセス許可は制限されており、たとえば、データベースを新規作成できないことがあります。

## <a name="how-to-create-additional-users"></a>追加のユーザーを作成する方法

*citus* 管理者アカウントには、追加のユーザーを作成するアクセス許可がありません。 ユーザーを追加するには、Azure portal インターフェイスを使用します。

1. Hyperscale サーバー グループの **[ロール]** ページに移動して、 **[+ 追加]** をクリックします。

   ![[ロール] ページ](media/howto-hyperscale-create-users/1-role-page.png)

2. ロール名とパスワードを入力します。 **[Save]** をクリックします。

   ![ロールの追加](media/howto-hyperscale-create-users/2-add-user-fields.png)

ユーザーは、サーバー グループのコーディネーター ノード上に作成され、すべてのワーカー ノードに伝達されます。

## <a name="how-to-delete-a-user-or-change-their-password"></a>ユーザーの削除またはパスワードの変更方法

Hyperscale サーバー グループの **[ロール]** ページに移動して、ユーザーの横にある省略記号 **[...]** をクリックします。 省略記号をクリックすると、ユーザーの削除やパスワードのリセットのためのメニューが表示されます。

   ![ロールを編集する](media/howto-hyperscale-create-users/edit-role.png)

*citus* ロールには特権が与えられているため、削除できません。

## <a name="how-to-modify-privileges-for-role"></a>ロールの特権を変更する方法

新しいロールは一般に、制限された特権でデータベースへのアクセスを提供するために使用されます。 ユーザーの特権を変更するには、PgAdmin や psql などのツールを使用して、標準的な PostgreSQL コマンドを使用します。 (Hyperscale (Citus) クイックスタートにある [psql を使用した接続](quickstart-create-hyperscale-portal.md#connect-to-the-database-using-psql)に関するセクションを参照してください。)

たとえば、*db_user* に *mytable* の読み取りを許可するには、次のようにアクセス許可を付与します。

```sql
GRANT SELECT ON mytable TO db_user;
```

Hyperscale (Citus) で、単一テーブルの GRANT ステートメントがクラスター全体に伝達され、すべてのワーカー ノードに適用されます。 ただし、システム全体 (スキーマ内のすべてのテーブルなど) の GRANT の場合は、すべての日付ノード上で実行する必要があります。  *run_command_on_workers()* ヘルパー関数を使用します。

```sql
-- applies to the coordinator node
GRANT SELECT ON ALL TABLES IN SCHEMA public TO db_user;

-- make it apply to workers as well
SELECT run_command_on_workers(
  'GRANT SELECT ON ALL TABLES IN SCHEMA public TO db_user;'
);
```

## <a name="next-steps"></a>次の手順

新しいユーザーのマシンの IP アドレスについてファイアウォールを開き、接続できるようにします。[Azure portal を使用して Hyperscale (Citus) ファイアウォール規則を作成し管理します](howto-hyperscale-manage-firewall-using-portal.md)。

データベース ユーザー アカウントの管理の詳細については、PostgreSQL の製品マニュアルを参照してください。

* [データベース ロールと特権](https://www.postgresql.org/docs/current/static/user-manag.html)
* [GRANT 構文](https://www.postgresql.org/docs/current/static/sql-grant.html)
* [特権](https://www.postgresql.org/docs/current/static/ddl-priv.html)
