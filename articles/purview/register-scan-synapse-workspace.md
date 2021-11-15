---
title: Azure Synapse Analytics ワークスペースに接続して管理する
description: このガイドでは、Azure Purview で Azure Synapse Analytics ワークスペースに接続し、Purview の機能を使用して、Azure Synapse Analytics ワークスペース ソースをスキャンおよび管理する方法について説明します。
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-map
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: a5921e2af4445ad645ce48f6102c311c9478b9e9
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2021
ms.locfileid: "131848023"
---
# <a name="connect-to-and-manage-azure-synapse-analytics-workspaces-in-azure-purview"></a>Azure Purview で Azure Synapse Analytics ワークスペースに接続して管理する

この記事では、Azure Purview で Azure Synapse Analyticsワークスペースを登録する方法と、Azure Synapse Analytics ワークスペースを認証して操作する方法について説明します。 Azure Purview の詳細については、[概要の記事](overview.md)を参照してください。

## <a name="supported-capabilities"></a>サポートされる機能

|**メタデータの抽出**|  **フル スキャン**  |**増分スキャン**|**スコープ スキャン**|**分類**|**アクセス ポリシー**|**系列**|
|---|---|---|---|---|---|---|
| [あり](#register) | [あり](#scan)| [あり](#scan) | [あり](#scan)| [あり](#scan)| いいえ| [はい - Synapse パイプライン](how-to-lineage-azure-synapse-analytics.md)|


<!-- 4. Prerequisites
Required. Add any relevant/source-specific prerequisites for connecting with this source. Authentication/Registration should be covered by the sections below and does not need to be covered here.
-->

## <a name="prerequisites"></a>前提条件

* アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

* アクティブな [Purview リソース](create-catalog-portal.md)。

* Purview Studio でソースを登録して管理するには、データ ソース管理者およびデータ閲覧者である必要があります。 詳細については、[Azure Purview のアクセス許可](catalog-permissions.md)に関するページを参照してください。

## <a name="register"></a>登録

このセクションでは、[Purview Studio](https://web.purview.azure.com/) を使用して、Azure Purview で Azure Synapse Analytics ワークスペースを登録する方法について説明します。

### <a name="authentication-for-registration"></a>登録の認証

Azure Synapse ワークスペースに対して少なくとも "*閲覧者*" ロールを持ち、Azure Purview の "*データ ソース管理者*" でもあるユーザーのみが、Azure Synapse ワークスペースを登録できます。

### <a name="steps-to-register"></a>登録の手順

1. Azure Purview アカウントに移動します。
1. 左側のペインで、 **[ソース]** を選択します。
1. **[登録]** を選択します。
1. **[Register sources]\(ソースの登録\)** で、 **[Azure Synapse Analytics (multiple)]\(Azure Synapse Analytics (複数)\)** を選択します。
1. **[続行]** を選択します。

   :::image type="content" source="media/register-scan-synapse-workspace/register-synapse-source.png" alt-text="Azure Purview のソースの選択 (Azure Synapse Analytics を含む) のスクリーンショット。":::

1. **[Register sources (Azure Synapse Analytics)]\(ソースの登録 (Azure Synapse Analytics)\)** ページで、次の操作を行います。

    a. データ カタログに表示されるデータ ソースの **名前** を入力します。  
    b. 必要に応じて、フィルター処理するために、**サブスクリプション** を選択します。  
    c. **[ワークスペース名]** ドロップダウン リストで、作業しているワークスペースを選択します。  
    d. エンドポイントの各ドロップダウン リストには、ワークスペースの選択に基づいて、SQL エンドポイントが自動的に入力されます。  
    e. **[Select a collection]\(コレクションの選択\)** ドロップダウン リストでは、使用しているコレクションを選択するか、必要に応じて新しく作成します。  
    f. **[登録]** を選択して、データ ソースの登録を完了します。

    :::image type="content" source="media/register-scan-synapse-workspace/register-synapse-source-details.png" alt-text="Azure Synapse ソースに関する詳細を入力するための [Register sources (Azure Synapse Analytics)]\(ソースの登録 (Azure Synapse Analytics)\) ページのスクリーンショット。":::

## <a name="scan"></a>スキャン

次の手順に従って、Azure Synapse Analytics ワークスペースをスキャンして、資産を自動的に識別し、データを分類します。 スキャン全般の詳細については、[スキャンとインジェストの概要](concept-scans-and-ingestion.md)に関するページを参照してください。

まず、[専用](#authentication-for-enumerating-dedicated-sql-database-resources)リソースまたは[サーバーレス](#authentication-for-enumerating-serverless-sql-database-resources) リソースの列挙用に認証を設定する必要があります。 これにより、Purview はワークスペース資産を列挙し、スコープ付きスキャンを実行できます。

次に、[ワークスペースの内容をスキャンするためのアクセス許可を適用する](#apply-permissions-to-scan-the-contents-of-the-workspace)必要があります。

### <a name="authentication-for-enumerating-dedicated-sql-database-resources"></a>専用 SQL データベース リソースを列挙するための認証

1. Azure portal で、Azure Synapse ワークスペース リソースに移動します。  
1. 左側のペインで  **[アクセス制御 (IAM)]** を選択します。

   > [!NOTE]
   > リソースにロールを追加するには、"*所有者*" または "*ユーザー アクセス管理者*" である必要があります。

1. **[追加]** ボタンを選びます。
1. **[閲覧者]** ロールを設定し、Azure Purview アカウント名 (管理サービス ID (MSI) を表すもの) を入力します。
1. **[保存]** を選択して、ロールの割り当てを完了します。

> [!NOTE]
> Azure Purview アカウントで複数の Azure Synapse ワークスペースを登録してスキャンする予定の場合、リソース グループやサブスクリプションなどの上位のレベルからロールを割り当てることもできます。

### <a name="authentication-for-enumerating-serverless-sql-database-resources"></a>サーバーレス SQL データベース リソースを列挙するための認証

Purview でサーバーレス SQL データベース リソースを列挙できるように認証を設定する必要がある場所は、Synapse ワークスペース、関連付けられているストレージ、サーバーレス データベースの 3 つです。 以下の手順では、3 つすべてに対してアクセス許可を設定します。

1. Azure portal で、Azure Synapse ワークスペース リソースに移動します。  
1. 左側のペインで  **[アクセス制御 (IAM)]** を選択します。 

   > [!NOTE]
   > リソースにロールを追加するには、"*所有者*" または "*ユーザー アクセス管理者*" である必要があります。
   
1. **[追加]** ボタンを選びます。   
1. **[閲覧者]** ロールを設定し、Azure Purview アカウント名 (管理サービス ID (MSI) を表すもの) を入力します。
1. **[保存]** を選択して、ロールの割り当てを完了します。
1. Azure portal で、Azure Synapse ワークスペースが属する **リソース グループ** または **サブスクリプション** に移動します。
1. 左側のペインで  **[アクセス制御 (IAM)]** を選択します。 

   > [!NOTE]
   > **[リソース グループ]** または **[サブスクリプション]** フィールドにロールを追加するには、"*所有者*" または "*ユーザー アクセス管理者*"である必要があります。 

1. **[追加]** ボタンを選びます。 
1. **[ストレージ BLOB データ閲覧者]** ロールを設定し、 **[選択]** ボックスに Azure Purview アカウント名 (MSI を表すもの) を入力します。 
1. **[保存]** を選択して、ロールの割り当てを完了します。
1. ご利用の Azure Synapse ワークスペースに移動し、Synapse Studio を開きます。
1. 左側のメニューで **[データ]** タブを選択します。
1. いずれかのデータベースの横にある省略記号 ( **...** ) を選択し、新しい SQL スクリプトを開始します。
1. サーバーレス SQL データベースに Azure Purview アカウント MSI (アカウント名で表されます) を追加します。 これを行うには、SQL スクリプトで次のコマンドを実行します。
    ```sql
    CREATE LOGIN [PurviewAccountName] FROM EXTERNAL PROVIDER;
    ```

### <a name="apply-permissions-to-scan-the-contents-of-the-workspace"></a>ワークスペースの内容をスキャンするためのアクセス許可を適用する

Azure Synapse ソースの認証は、次の 2 つの方法のいずれかで設定できます。

- マネージド ID の使用
- サービス プリンシパルを使用する

> [!IMPORTANT]
> サーバーレス データベースのこれらの手順は、レプリケートされたデータベースには適用 **されません**。 現在、Synapse では、Spark データベースからレプリケートされるサーバーレス データベースは読み取り専用です。 詳細については、[こちら](../synapse-analytics/sql/resources-self-help-sql-on-demand.md#operation-is-not-allowed-for-a-replicated-database)を参照してください。

> [!NOTE]
> 認証は、登録してスキャンする Azure Synapse ワークスペース内の専用 SQL データベースごとに設定する必要があります。 サーバーレス SQL データベースに関する以降のセクションで説明するアクセス許可は、ワークスペース内のすべてのデータベースに適用されます。 つまり、認証を設定する必要があるのは 1 回だけです。

#### <a name="use-a-managed-identity-for-dedicated-sql-databases"></a>専用 SQL データベースでマネージド ID を使用する

1. ご利用の Azure Synapse ワークスペースに移動します。
1. **[データ]** セクションに移動し、専用 SQL データベースの 1 つを探します。
1. その横にある省略記号 ( **...** ) を選択し、新しい SQL スクリプトを開始します。

   > [!NOTE]
   > 次の手順でコマンドを実行するには、ワークスペースの "*Azure Synapse 管理者*" である必要があります。 Azure Synapse Analytics のアクセス許可の詳細については、[Azure Synapse ワークスペースのアクセス制御の設定](../synapse-analytics/security/how-to-set-up-access-control.md)に関するページを参照してください。

1. 専用 SQL データベースに Azure Purview アカウント MSI (アカウント名で表されます) を **db_datareader** として追加します。 これを行うには、SQL スクリプトで次のコマンドを実行します。

    ```sql
    CREATE USER [PurviewAccountName] FROM EXTERNAL PROVIDER
    GO
    
    EXEC sp_addrolemember 'db_datareader', [PurviewAccountName]
    GO
    ```

#### <a name="use-a-managed-identity-for-serverless-sql-databases"></a>サーバーレス SQL データベースでマネージド ID を使用する

1. ご利用の Azure Synapse ワークスペースに移動します。
1. **[データ]** セクションに移動し、**スキャンするデータベースごと** に次の手順に従います。
1. データベースの横にある省略記号 ( **...** ) を選択し、新しい SQL スクリプトを開始します。
1. サーバーレス SQL データベースに Azure Purview アカウント MSI (アカウント名で表されます) を **db_datareader** として追加します。 これを行うには、SQL スクリプトで次のコマンドを実行します。
    ```sql
    CREATE USER [PurviewAccountName] FOR LOGIN [PurviewAccountName];
    ALTER ROLE db_datareader ADD MEMBER [PurviewAccountName]; 
    ```

#### <a name="grant-permission-to-use-credentials-for-external-tables"></a>外部テーブルの資格情報を使用するためのアクセス許可を付与する

Azure Synapse ワークスペースに外部テーブルがある場合、Azure Purview マネージド ID には、外部テーブル スコープの資格情報に対する参照アクセス許可が付与されている必要があります。 参照アクセス許可を使用すると、Azure Purview では外部テーブルからデータを読み取ることができます。

```sql
GRANT REFERENCES ON DATABASE SCOPED CREDENTIAL::[scoped_credential] TO [PurviewAccountName];
```

#### <a name="use-a-service-principal-for-dedicated-sql-databases"></a>専用 SQL データベースでサービス プリンシパルを使用する

> [!NOTE]
> まず、「[Azure Purview でのソース認証用の資格情報](manage-credentials.md)」の手順に従って、種類が *[サービス プリンシパル]* の新しい "*資格情報*" を設定する必要があります。

1. ご利用の **Azure Synapse ワークスペース** に移動します。
1. **[データ]** セクションに移動し、専用 SQL データベースの 1 つを探します。
1. その横にある省略記号 ( **...** ) を選択し、新しい SQL スクリプトを開始します。
1. 専用 SQL データベースに **サービス プリンシパル ID** を **db_datareader** として追加します。 これを行うには、SQL スクリプトで次のコマンドを実行します。

    ```sql
    CREATE USER [ServicePrincipalID] FROM EXTERNAL PROVIDER
    GO
    
    EXEC sp_addrolemember 'db_datareader', [ServicePrincipalID]
    GO
    ```

> [!NOTE]
> Synapse ワークスペース内のすべての専用 SQL データベースに対して、前の手順を繰り返します。 

#### <a name="use-a-service-principal-for-serverless-sql-databases"></a>サーバーレス SQL データベースでサービス プリンシパルを使用する

1. ご利用の Azure Synapse ワークスペースに移動します。
1. **[データ]** セクションに移動し、サーバーレス SQL データベースの 1 つを探します。
1. その横にある省略記号 ( **...** ) を選択し、新しい SQL スクリプトを開始します。
1. サーバーレス SQL データベースに **サービス プリンシパル ID** を追加します。 これを行うには、SQL スクリプトで次のコマンドを実行します。
    ```sql
    CREATE LOGIN [ServicePrincipalID] FROM EXTERNAL PROVIDER;
    ```
    
1. スキャンする各サーバーレス SQL データベースに、**サービス プリンシパル ID** を **db_datareader** として追加します。 これを行うには、SQL スクリプトで次のコマンドを実行します。
   ```sql
    CREATE USER [ServicePrincipalID] FOR LOGIN [ServicePrincipalID];
    ALTER ROLE db_datareader ADD MEMBER [ServicePrincipalID]; 
    ```

### <a name="set-up-azure-synapse-workspace-firewall-access"></a>Azure Synapse ワークスペースのファイアウォール アクセスを設定する

1. Azure portal で、Azure Synapse ワークスペースに移動します。 

1. 左側のペインで、 **[ファイアウォール]** を選択します。

1. **[Azure サービスおよびリソースに、このワークスペースへのアクセスを許可する]** で **[オン]** を選択します。

1. **[保存]** を選択します。

### <a name="create-and-run-scan"></a>スキャンを作成して実行する

新しいスキャンを作成して実行するには、次の操作を行います。

1. [Purview Studio](https://web.purview.azure.com/resource/) の左側のペインで **[Data Map]** タブを選択します。

1. 登録したデータ ソースを選択します。

1. **[詳細の表示]** を選択し、 **[New scan]\(新しいスキャン\)** を選択します。 あるいは、ソース タイルで **スキャン クイック アクション** アイコンを選択することもできます。

1. **[スキャン]** 詳細ウィンドウの **[名前]** ボックスにスキャンの名前を入力します。
1. **[種類]** ドロップダウン リストで、このソース内でスキャンするリソースの種類を選択します。 **SQL Database** が、Azure Synapse ワークスペース内で現在サポートされている唯一の種類です。
   
    :::image type="content" source="media/register-scan-synapse-workspace/synapse-scan-setup.png" alt-text="Azure Synapse ソース スキャンの詳細ウィンドウのスクリーンショット。":::

1. **[資格情報]** ドロップダウン リストで、データ ソース内のリソースに接続するための資格情報を選択します。 
  
1. 各種類内で、すべてのリソースをスキャンするか、名前でそのサブセットをスキャンするかを選択できます。

1.  **[続行]** をクリックして先に進みます。 

1.  種類が **[Azure Synapse SQL]** の **スキャン ルールセット** を選択します。 また、スキャン ルール セットをインラインで作成することもできます。

1. スキャン トリガーを選択します。 これは、**毎週または毎月**、あるいは **1 回** 実行するようにスケジュールできます。

1. スキャンの設定を確認し、 **[保存]** を選択して設定を完了します。  

[!INCLUDE [create and manage scans](includes/view-and-manage-scans.md)]

## <a name="next-steps"></a>次のステップ

ソースの登録が完了したので、以下のガイドに従って Azure Purview とご利用のデータの詳細について学習します。

- [Azure Purview のデータ分析情報](concept-insights.md)
- [Azure Purview のデータ系列](catalog-lineage-user-guide.md)
- [Data Catalog の検索](how-to-search-catalog.md)
