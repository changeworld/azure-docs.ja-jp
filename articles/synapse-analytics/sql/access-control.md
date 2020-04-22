---
title: ワークスペース、データ、およびパイプラインへのアクセスを管理する
description: Azure Synapse Analytics ワークスペース (プレビュー) でワークスペース、データ、およびパイプラインへのアクセス制御を管理する方法について説明します。
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: 89d2105ab080309639c4341072c3f5f36608dfce
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2020
ms.locfileid: "81421106"
---
# <a name="manage-access-to-workspaces-data-and-pipelines"></a>ワークスペース、データ、およびパイプラインへのアクセスを管理する

Azure Synapse Analytics ワークスペース (プレビュー) でワークスペース、データ、およびパイプラインへのアクセス制御を管理する方法について説明します。

> [!NOTE]
> GA 向けに、Synapse 固有の Azure RBAC ロールの導入によりさらなる RBAC の開発が行われる予定です

## <a name="access-control-for-workspace"></a>ワークスペースのアクセス制御

Azure Synapse ワークスペースへの運用環境のデプロイでは、お使いの環境を整理して、ユーザーと管理者を簡単にプロビジョニングできるようにすることをお勧めします。

> [!NOTE]
> ここで採用するアプローチでは、いくつかのセキュリティ グループを作成し、それらを一貫して使用するようにワークスペースを構成します。 グループの設定後、管理者はセキュリティ グループ内のメンバーシップを管理するだけで済みます。

### <a name="step-1-set-up-security-groups-with-names-following-this-pattern"></a>手順 1:次のパターンに従う名前のセキュリティ グループを設定する

1. `Synapse_WORKSPACENAME_Users` という名前のセキュリティ グループを作成します
2. `Synapse_WORKSPACENAME_Admins` という名前のセキュリティ グループを作成します
3. `Synapse_WORKSPACENAME_Admins` を `ProjectSynapse_WORKSPACENAME_Users` に追加しました

### <a name="step-2-prepare-the-default-adls-gen2-account"></a>手順 2:既定の ADLS Gen2 アカウントを準備する

ワークスペースをプロビジョニングしたときに、ADLSGEN2 アカウントと、ワークスペースで使用するファイルシステムのコンテナーを選択する必要がありました。

1. [Azure portal](https://portal.azure.com) を開きます。
2. ADLSGEN2 アカウントに移動します
3. Azure Synapse ワークスペース用に選択したコンテナー (ファイルシステム) に移動します
4. **[アクセス制御 (IAM)]** をクリックします
5. 次のロールを割り当てます。
   1. **閲覧者**ロール: `Synapse_WORKSPACENAME_Users`
   2. **ストレージ BLOB データ所有者**ロール: `Synapse_WORKSPACENAME_Admins`
   3. **ストレージ BLOB データ共同作成者**`Synapse_WORKSPACENAME_Users`
   4. **ストレージ BLOB データ所有者**ロール: `WORKSPACENAME`
  
### <a name="step-3-configure-the-workspace-admin-list"></a>手順 3:ワークスペース管理者リストを構成する

1. [**Azure Synapse Web UI**](https://web.azuresynapse.net) に移動します
2. **[管理]**   >  **[セキュリティ]**  >  **[アクセス制御]** に移動します
3. **[管理者の追加]** をクリックし、[`Synapse_WORKSPACENAME_Admins`] を選択します

### <a name="step-4-configure-sql-admin-access-for-the-workspace"></a>手順 4:ワークスペースの SQL 管理者アクセスを構成する

1. [Azure portal](https://portal.azure.com) に移動します
2. ワークスペースに移動します
3. **[設定]**  >  **[Active Directory 管理者]** に移動します
4. **[管理者の設定]** をクリックします
5. `Synapse_WORKSPACENAME_Admins` を選択
6. **[選択]** をクリックします
7. **[保存]** をクリックします

### <a name="step-5-add-and-remove-users-and-admins-to-security-groups"></a>手順 5:セキュリティ グループのユーザーと管理者を追加および削除する

1. 管理アクセス権を必要とするユーザーを `Synapse_WORKSPACENAME_Admins` に追加します
2. 他のすべてのユーザーを `Synapse_WORKSPACENAME_Users` に追加します

## <a name="access-control-to-data"></a>データに対するアクセス制御

基礎となるデータへのアクセス制御は、次の 3 つの部分に分かれています。

- ストレージ アカウントへのデータプレーン アクセス (上の手順 2 で既に構成済み)
- SQL データベースへのデータプレーン アクセス (SQL プールと SQL オンデマンドの両方)
- ストレージ アカウントを介した SQL オンデマンド データベースの資格情報の作成

## <a name="access-control-to-sql-databases"></a>SQL データベースに対するアクセス制御

> [!TIP]
> ユーザーにすべての SQL データベースへのアクセスを許可するには、**各** SQL データベースに対して次の手順を実行する必要があります。

### <a name="sql-on-demand"></a>SQL オンデマンド

ユーザーに**単一の** SQL オンデマンド データベースへのアクセスを許可するには、次の例の手順に従います。

1. ログインを作成します

    ```sql
    use master
    go
    CREATE LOGIN [John.Thomas@microsoft.com] FROM EXTERNAL PROVIDER;
    go
    ```

2. ユーザーを作成します

    ```sql
    use yourdb -- Use your DB name
    go
    CREATE USER john FROM LOGIN [John.Thomas@microsoft.com];
    ```

3. 指定したロールのメンバーにユーザーを追加します

    ```sql
    use yourdb -- Use your DB name
    go
    alter role db_owner Add member john -- Type USER name from step 2
    ```

### <a name="sql-pools"></a>SQL プール

ユーザーに**単一の** SQL データベースへのアクセスを許可するには、次の手順に従います。

1. コンテキスト セレクター (データベースを選択するためのドロップダウン) で目的のデータベースをターゲットとする次のコマンドを実行して、データベースにユーザーを作成します。

    ```sql
    --Create user in SQL DB
    CREATE USER [<alias@domain.com>] FROM EXTERNAL PROVIDER;
    ```

2. データベースにアクセスするためのロールをユーザーに付与します。

    ```sql
    --Create user in SQL DB
    EXEC sp_addrolemember 'db_owner', '<alias@domain.com>';
    ```

> [!IMPORTANT]
> *db_owner* アクセス許可を付与することが望ましくない場合は、*db_datareader* および *db_datawriter* を読み取りまたは書き込みアクセス許可のために使用することができます。
> Spark ユーザーが Spark と SQL プールとの間で直接読み取りまたは書き込みを行うには、*db_owner* アクセス許可が必要です。

ユーザーを作成した後、SQL オンデマンドでストレージ アカウントに対してクエリを実行できることを検証します。

- SQL オンデマンドの **master** データベースをターゲットとする次のコマンドを実行します。

    ```sql
    CREATE CREDENTIAL [https://<storageaccountname>.dfs.core.windows.net]
    WITH IDENTITY='User Identity';
    ```

## <a name="access-control-to-workspace-pipeline-runs"></a>ワークスペースのパイプラインの実行に対するアクセス制御

### <a name="workspace-managed-identity"></a>ワークスペースのマネージド ID

> [!IMPORTANT]
> SQL プールを参照するデータセットまたはアクティビティを含むパイプラインを正常に実行するには、ワークスペース ID に SQL プールへの直接アクセスを許可する必要があります。

各 SQL プールで次のコマンドを実行して、ワークスペース マネージド ID が SQL プール データベースでパイプラインを実行できるようにします。

```sql
--Create user in DB
CREATE USER [<workspacename>] FROM EXTERNAL PROVIDER;

--Granting permission to the identity
GRANT CONTROL ON DATABASE::<SQLpoolname> TO <workspacename>;
```

このアクセス許可を削除するには、同じ SQL プールで次のスクリプトを実行します。

```sql
--Revoking permission to the identity
REVOKE CONTROL ON DATABASE::<SQLpoolname> TO <workspacename>;

--Deleting the user in the DB
DROP USER [<workspacename>];
```

## <a name="next-steps"></a>次のステップ

Synapse SQL でのアクセスおよび制御の概要については、[Synapse SQL のアクセス制御](../sql/access-control.md)に関するページを参照してください。 データベース プリンシパルの詳細については、[プリンシパル](https://msdn.microsoft.com/library/ms181127.aspx)に関するページを参照してください。 データベース ロールの詳細については、[データベース ロール](https://msdn.microsoft.com/library/ms189121.aspx)に関する記事を参照してください。
