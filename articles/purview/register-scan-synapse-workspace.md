---
title: Azure Synapse Analytics ワークスペースを登録してスキャンする方法
description: Azure Purview データ カタログで Azure Synapse ワークスペースをスキャンする方法について説明します。
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-map
ms.topic: how-to
ms.date: 09/27/2021
ms.openlocfilehash: 8a7b23089e9b17e35b56b04991c76b37baedf231
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/29/2021
ms.locfileid: "129207794"
---
# <a name="register-and-scan-azure-synapse-analytics-workspaces"></a>Azure Synapse Analytics ワークスペースを登録してスキャンする

この記事では、Azure Purview に Azure Synapse Analytics ワークスペースを登録し、そのスキャンを設定する方法の概要について説明します。

## <a name="supported-capabilities"></a>サポートされる機能

Azure Synapse Analytics ワークスペースのスキャンは、内部の専用およびサーバーレス SQL データベースのメタデータとスキーマの取得をサポートしています。 また、ワークスペース スキャンでは、システムおよびカスタムの分類規則に基づいてデータが自動的に分類されます。

## <a name="prerequisites"></a>前提条件

- データ ソースを登録する前に、Azure Purview アカウントを作成します。 詳細については、[クイック スタート: Azure Purview アカウントの作成](create-catalog-portal.md)に関するページを参照してください。
- Azure Purview データ ソース管理者である必要があります。
- 以下のセクションの説明に従って認証を設定します。

## <a name="register-and-scan-an-azure-synapse-workspace"></a>Azure Synapse ワークスペースを登録してスキャンする

> [!IMPORTANT]
> ワークスペースを正常にスキャンするには、次のセクションで説明されている手順に正確に従って、アクセス許可を適用してください。

### <a name="step-1-register-your-source"></a>**手順 1**: ソースを登録する

> [!NOTE]
> Azure Synapse ワークスペースに対して少なくとも "*閲覧者*" ロールを持ち、Azure Purview の "*データ ソース管理者*" でもあるユーザーのみがこの手順を実行できます。

新しい Azure Synapse のソースをデータ カタログに登録するには、次の手順を行います。

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


### <a name="step-2-apply-permissions-to-enumerate-the-contents-of-the-azure-synapse-workspace"></a>**手順 2**: Azure Synapse ワークスペースの内容を列挙するためのアクセス許可を適用する

#### <a name="set-up-authentication-for-enumerating-dedicated-sql-database-resources"></a>専用 SQL データベース リソースを列挙するための認証を設定する

1. Azure portal で、Azure Synapse ワークスペース リソースに移動します。  
1. 左側のペインで  **[アクセス制御 (IAM)]** を選択します。 

   > [!NOTE]
   > リソースにロールを追加するには、"*所有者*" または "*ユーザー アクセス管理者*" である必要があります。
   
1. **[追加]** ボタンを選びます。   
1. **[閲覧者]** ロールを設定し、Azure Purview アカウント名 (管理サービス ID (MSI) を表すもの) を入力します。
1. **[保存]** を選択して、ロールの割り当てを完了します。

> [!NOTE]
> Azure Purview アカウントで複数の Azure Synapse ワークスペースを登録してスキャンする予定の場合、リソース グループやサブスクリプションなどの上位のレベルからロールを割り当てることもできます。 

#### <a name="set-up-authentication-for-enumerating-serverless-sql-database-resources"></a>サーバーレス SQL データベース リソースを列挙するための認証を設定する

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

### <a name="step-3-apply-permissions-to-scan-the-contents-of-the-workspace"></a>**手順 3**: ワークスペースの内容をスキャンするためのアクセス許可を適用する

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

### <a name="step-4-set-up-azure-synapse-workspace-firewall-access"></a>**手順 4**: Azure Synapse ワークスペースのファイアウォール アクセスを設定する

1. Azure portal で、Azure Synapse ワークスペースに移動します。 

1. 左側のペインで、 **[ファイアウォール]** を選択します。

1. **[Azure サービスおよびリソースに、このワークスペースへのアクセスを許可する]** で **[オン]** を選択します。

1. **[保存]** を選択します。

### <a name="step-5-set-up-a-scan-on-the-workspace"></a>**手順 5**: ワークスペースに対するスキャンを設定する

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

#### <a name="view-your-scans-and-scan-runs"></a>スキャンとスキャンの実行を表示する

1. ソース セクションの下にあるタイルで **[詳細の表示]** を選択して、ソースの詳細を表示します。 

      :::image type="content" source="media/register-scan-synapse-workspace/synapse-source-details.png" alt-text="Azure Synapse Analytics のソースの詳細ページのスクリーンショット。"::: 

1. **スキャンの詳細** ページに移動して、スキャン実行の詳細を表示します。

    * **[状態] バー** には、子リソースの実行状態の概要が表示されます。 状態は、ワークスペース レベルのスキャンに対して表示されます。  
    * 緑はスキャン実行の成功を示し、赤はスキャン実行の失敗を示し、灰色はスキャン実行がまだ進行中であることを示します。  
    * スキャンの実行に関するより詳細な情報を表示するには、それを選択します。

      :::image type="content" source="media/register-scan-synapse-workspace/synapse-scan-details.png" alt-text="Azure Synapse Analytics のスキャンの詳細ページのスクリーンショット。" lightbox="media/register-scan-synapse-workspace/synapse-scan-details.png"::: 

    * **ソースの詳細** ページの下部に、最近失敗したスキャン実行の概要が表示されます。 ここでも、スキャンの実行に関するより詳細な情報を表示するには、それを選択します。

#### <a name="manage-your-scans"></a>スキャンを管理する

スキャンを編集、削除、またはキャンセルするには、次の操作を行います。

1. 管理センターに移動します。 **[Sources and scanning]\(ソースとスキャン\)** セクションで **[データ ソース]** を選択し、管理するデータ ソースを選択します。

1. 管理するスキャンを選択し、 **[編集]** を選択します。

   - スキャンを削除するには、 **[削除]** を選択します。
   - スキャンが現在実行されている場合は、それをキャンセルできます。

## <a name="next-steps"></a>次のステップ

- [Azure Purview Data Catalog の参照](how-to-browse-catalog.md)
- [Azure Purview データ カタログを検索する](how-to-search-catalog.md)   
