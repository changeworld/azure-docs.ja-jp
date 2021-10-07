---
title: データ ソースの準備状況を大規模に確認する
description: このチュートリアルでは、Azure データ ソースを Azure Purview に登録してスキャンする前に、その準備状況を確認します。
author: zeinam
ms.author: zeinam
ms.service: purview
ms.subservice: purview-data-map
ms.topic: tutorial
ms.date: 09/27/2021
ms.openlocfilehash: 1eaca951a5abe43bda2621043321537cee2daa13
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/29/2021
ms.locfileid: "129213415"
---
# <a name="tutorial-check-data-source-readiness-at-scale"></a>チュートリアル: データ ソースの準備状況を大規模に確認する

データ ソースをスキャンするには、Azure Purview がそれらにアクセスする必要があります。 このアクセス権を取得するために、資格情報が使用されます。 "*資格情報*" は、Azure Purview で、登録済みのデータ ソースに対する認証に使用できる認証情報です。 Azure Purview の資格情報を設定するには、次のようないくつかの方法があります。 
- Azure Purview アカウントに割り当てられたマネージド ID。
- Azure Key Vault に格納されたシークレット。 
- サービス プリンシパル。

この 2 部構成のチュートリアル シリーズでは、ご自分の Azure サブスクリプション全体で、さまざまな Azure データ ソースに必要な Azure のロールの割り当てとネットワーク アクセスを大規模に確認および構成する方法について説明します。 その後、Azure Purview で Azure データ ソースを登録してスキャンできます。 

Azure Purview アカウントをデプロイした後、Azure データ ソースを登録してスキャンする前に、[Azure Purview データ ソースの準備状況チェックリスト](https://github.com/Azure/Purview-Samples/tree/master/Data-Source-Readiness) スクリプトを実行します。 

このチュートリアル シリーズのパート 1 では、次のことを行います。

> [!div class="checklist"]
>
> * データ ソースを特定し、データ ソースのサブスクリプションの一覧を準備します。
> * 準備状況チェックリスト スクリプトを実行して、Azure の全データ ソースを対象に、不足しているロールベースのアクセス制御 (RBAC) やネットワーク構成を見つけます。
> * 出力レポートで、Azure Purview マネージド ID (MSI) に必要な、不足しているネットワーク構成とロールの割り当てを確認します。 
> * レポートをデータの Azure サブスクリプションの所有者と共有して、提案されたアクションを所有者が実行できるようにします。

## <a name="prerequisites"></a>前提条件

* データ ソースが配置されている Azure サブスクリプション。 Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)してください。
* [Azure Purview アカウント](create-catalog-portal.md)。
* Azure SQL Database、Azure Synapse Analytics、Azure SQL Managed Instance などのデータ ソースがある各サブスクリプションの Azure Key Vault リソース。
* [Azure Purview データ ソースの準備状況チェックリスト](https://github.com/Azure/Purview-Samples/tree/master/Data-Source-Readiness) スクリプト。

> [!NOTE]
> Azure Purview データ ソースの準備状況チェックリストは、Windows でのみ使用できます。
> この準備状況チェックリスト スクリプトは、現在 Azure Purview MSI でサポートされています。

## <a name="prepare-azure-subscriptions-list-for-data-sources"></a>データ ソースに対応する Azure サブスクリプションの一覧を準備する

スクリプトを実行する前に、次の 4 つの列がある .csv ファイル (たとえば、C:\temp\Subscriptions.csv) を作成します。

|列名|説明|例|
|----|----|----|
|`SubscriptionId`|データ ソースの Azure サブスクリプション ID。|12345678-aaaa-bbbb-cccc-1234567890ab|
|`KeyVaultName`|データ ソース サブスクリプションにデプロイされている既存のキー コンテナーの名前。|ContosoDevKeyVault|
|`SecretNameSQLUserName`|Azure AD 認証を使用して Azure Synapse、Azure SQL Database、または Azure SQL Managed Instance にサインインできる Azure Active Directory (Azure AD) ユーザー名を含む既存の Azure Key Vault シークレットの名前。|ContosoDevSQLAdmin|
|`SecretNameSQLPassword`|Azure AD 認証を使用して Azure Synapse、Azure SQL Database、または Azure SQL Managed Instance にサインインできる Azure AD ユーザー パスワードを含む既存の Azure Key Vault シークレットの名前。|ContosoDevSQLPassword|
   

**サンプル .csv ファイル:**
    
:::image type="content" source="./media/tutorial-data-sources-readiness/subscriptions-input.png" alt-text="サンプルのサブスクリプションの一覧を示すスクリーンショット。" lightbox="./media/tutorial-data-sources-readiness/subscriptions-input.png":::

> [!NOTE] 
> 必要に応じて、コード内のファイル名とパスを更新できます。



## <a name="run-the-script-and-install-the-required-powershell-modules"></a>スクリプトを実行して必要な PowerShell モジュールをインストールする 

これらの手順に従って、Windows コンピューターからスクリプトを実行します。

1. [Azure Purview データ ソースの準備状況チェックリスト](https://github.com/Azure/Purview-Samples/tree/master/Data-Source-Readiness) スクリプトを任意の場所にダウンロードします。

2. ご使用のコンピューターで、Windows タスク バーの検索ボックスに「**PowerShell**」と入力します。 検索一覧で、 **[Windows PowerShell]** を選択したまま (または、右クリックして) **[管理者として実行]** を選択します。

3. PowerShell ウィンドウに、次のコマンドを入力します。 (`<path-to-script>` は、抽出したスクリプト ファイルがあるフォルダーのパスに置き換えてください)。

   ```powershell
   dir -Path <path-to-script> | Unblock-File
   ```

4. 次のコマンドを入力して、Azure コマンドレットをインストールします。

   ```powershell
   Install-Module -Name Az -AllowClobber -Scope CurrentUser
   ```
6. *[続行するには NuGet プロバイダーが必要です]* というプロンプトが表示された場合は、 **[Y]** と入力し、**Enter** を押します。

7. *[信頼されていないリポジトリ]* というプロンプトが表示された場合は、 **[A]** と入力し、**Enter** を押します。

5. 前の手順を繰り返して、`Az.Synapse` と `AzureAD` モジュールをインストールします。

PowerShell によって必要なモジュールがインストールされるまでに 1 分ほどかかる場合があります。


## <a name="collect-other-data-needed-to-run-the-script"></a>スクリプトの実行に必要なその他のデータを収集する

PowerShell スクリプトを実行してデータ ソース サブスクリプションの準備状況を確認する前に、スクリプトで使用する次の引数の値を取得します。

- `AzureDataType`: データソースの種類として次のオプションのいずれかを選択して、サブスクリプション全体でのそのデータ型の準備状況を確認します。 
    
    - `BlobStorage`

    - `AzureSQLMI`

    - `AzureSQLDB`
    
    - `ADLSGen2`
    
    - `ADLSGen1`
    
    - `Synapse`
    
    - `All`

- `PurviewAccount`: 既存の Azure Purview アカウントのリソース名。

- `PurviewSub`: Azure Purview アカウントがデプロイされているサブスクリプション ID。

## <a name="verify-your-permissions"></a>アクセス許可を確認する

ユーザーに次のロールとアクセス許可が付与されていることを確認します。

ロールまたはアクセス許可 | スコープ |
|-------|--------|
| **グローバル閲覧者** | Azure AD テナント |
| **Reader** | Azure データ ソースが配置されている Azure サブスクリプション |
| **Reader** | Azure Purview アカウントが作成されたサブスクリプション |
| **SQL 管理者** (Azure AD Authentication) | Azure Synapse 専用プール、Azure SQL データベース インスタンス、Azure SQL マネージド インスタンス |
| Azure キー コンテナーへのアクセス | キー コンテナーのシークレットまたは Azure Key Vault シークレット ユーザーを取得または一覧表示するためのアクセス |  


## <a name="run-the-client-side-readiness-script"></a>クライアント側で準備状況スクリプトを実行する

次の手順を実行して、スクリプトを実行します。

1. 次のコマンドを使用して、スクリプトのフォルダーにアクセスします。 `<path-to-script>` は、抽出したファイルがあるフォルダーのパスに置き換えてください。

   ```powershell
   cd <path-to-script>
   ```

2. 次のコマンドを実行して、ローカル コンピューターの実行ポリシーを設定します。 実行ポリシーを変更するように求められたら、 *[すべてはい]* を示す **[A]** を入力します。

   ```powershell
   Set-ExecutionPolicy -ExecutionPolicy Unrestricted
   ```

3. 次のパラメーターを使用してスクリプトを実行します。 `DataType` 、 `PurviewName` 、および `SubscriptionID` の各プレースホルダーを置き換えます。

   ```powershell
   .\purview-data-sources-readiness-checklist.ps1 -AzureDataType <DataType> -PurviewAccount <PurviewName> -PurviewSub <SubscriptionID>
   ```

   このコマンドを実行すると、ポップアップ ウィンドウが 2 回表示され、Azure Active Directory の資格情報を使用して Azure と Azure AD にサインインするように求められる場合があります。


環境内の Azure サブスクリプションとリソースの数によっては、レポートの作成に数分かかることがあります。 

この処理が完了したら、出力レポートを確認します。このレポートには、Azure サブスクリプションまたはリソースで検出された不足している構成が示されています。 結果は、"_Passed (合格)_ "、"_Not Passed (不合格)_ "、または "_Awareness (認識)_ " として表示されます。 サブスクリプション管理者が必要な設定を構成できるように、結果を組織内の対応するサブスクリプション管理者と共有することができます。


## <a name="more-information"></a>説明を見る

### <a name="what-data-sources-are-supported-by-the-script"></a>どのようなデータ ソースがスクリプトでサポートされていますか?

現在、次のデータ ソースがスクリプトでサポートされています。

- Azure Blob Storage (BlobStorage)
- Azure Data Lake Storage Gen2 (ADLSGen2)
- Azure Data Lake Storage Gen1 (ADLSGen1)
- Azure SQL Database (AzureSQLDB)
- Azure SQL Managed Instance (AzureSQLMI)
- Azure Synapse (Synapse) 専用プール

スクリプトを実行するときに、これらのデータ ソースのすべてまたはいずれかを入力パラメーターとして選択できます。

### <a name="what-checks-are-included-in-the-results"></a>結果にはどのようなチェックが含まれますか?

#### <a name="azure-blob-storage-blobstorage"></a>Azure Blob Storage (BlobStorage)

- RBAC。 選択したスコープの下の各サブスクリプションで、Azure Purview MSI に **ストレージ BLOB データ閲覧者** ロールが割り当てられているかどうかを確認します。
- RBAC。 選択したスコープで Azure Purview MSI に **閲覧者** ロールが割り当てられているかどうかを確認します。
- サービス エンドポイント。 サービス エンドポイントがオンになっているかどうかを確認し、 **[信頼された Microsoft サービスによるこのストレージ アカウントに対するアクセスを許可します]** が有効になっているかどうかを確認します。
- ネットワーク: プライベート エンドポイントがストレージ用に作成され、Blob Storage に対して有効になっているかどうかを確認します。

#### <a name="azure-data-lake-storage-gen2-adlsgen2"></a>Azure Data Lake Storage Gen2 (ADLSGen2)

- RBAC。 選択したスコープの下の各サブスクリプションで、Azure Purview MSI に **ストレージ BLOB データ閲覧者** ロールが割り当てられているかどうかを確認します。
- RBAC。 選択したスコープで Azure Purview MSI に **閲覧者** ロールが割り当てられているかどうかを確認します。
- サービス エンドポイント。 サービス エンドポイントがオンになっているかどうかを確認し、 **[信頼された Microsoft サービスによるこのストレージ アカウントに対するアクセスを許可します]** が有効になっているかどうかを確認します。
- ネットワーク: プライベート エンドポイントがストレージ用に作成され、Blob Storage に対して有効になっているかどうかを確認します。

#### <a name="azure-data-lake-storage-gen1-adlsgen1"></a>Azure Data Lake Storage Gen1 (ADLSGen1)

- ネットワーク。 サービス エンドポイントがオンになっているかどうかを確認し、 **[すべての Azure サービスによるこの Data Lake Storage Gen1 アカウントへのアクセスを許可します]** が有効になっているかどうかを確認します。
- アクセス許可。 Azure Purview MSI に読み取りまたは実行アクセス許可が付与されているかどうかを確認します。

#### <a name="azure-sql-database-azuresqldb"></a>Azure SQL Database (AzureSQLDB)

- SQL Server インスタンス
  - ネットワーク。 パブリック エンドポイントまたはプライベート エンドポイントが有効になっているかどうかを確認します。
  - ファイアウォール。 **[Azure サービスおよびリソースにこのサーバーへのアクセスを許可する]** が有効になっているかどうかを確認します。
  - Azure AD の管理。 Azure SQL Server で Azure AD 認証が使用されているかどうかを確認します。
  - Azure AD の管理。 Azure SQL Server の Azure AD 管理者ユーザーまたはグループを入力します。

- SQL データベース:
  - SQL ロール。 Azure Purview MSI に **db_datareader** ロールが割り当てられているかどうかを確認します。

#### <a name="azure-sql-managed-instance-azuresqlmi"></a>Azure SQL Managed Instance (AzureSQLMI)

- SQL Managed Instance サーバー:
  - ネットワーク。 パブリック エンドポイントまたはプライベート エンドポイントが有効になっているかどうかを確認します。
  - ProxyOverride。 Azure SQL Managed Instance がプロキシまたはリダイレクトとして構成されているかどうかを確認します。
  - ネットワーク。 必要なポート経由での AzureCloud のアクセスを許可する受信規則が NSG にあるかどうかを確認します。  
    - リダイレクト: 1433 および 11000-11999  
    または
    - プロキシ: 3342
  - Azure AD の管理。 Azure SQL Server で Azure AD 認証が使用されているかどうかを確認します。
  - Azure AD の管理。 Azure SQL Server の Azure AD 管理者ユーザーまたはグループを入力します。

- SQL データベース:
  - SQL ロール。 Azure Purview MSI に **db_datareader** ロールが割り当てられているかどうかを確認します。

#### <a name="azure-synapse-synapse-dedicated-pool"></a>Azure Synapse (Synapse) 専用プール

- RBAC。 選択したスコープの下の各サブスクリプションで、Azure Purview MSI に **ストレージ BLOB データ閲覧者** ロールが割り当てられているかどうかを確認します。
- RBAC。 選択したスコープで Azure Purview MSI に **閲覧者** ロールが割り当てられているかどうかを確認します。
- SQL Server インスタンス (専用プール):
  - ネットワーク: パブリック エンドポイントまたはプライベート エンドポイントが有効になっているかどうかを確認します。
  - ファイアウォール: **[Azure サービスおよびリソースにこのサーバーへのアクセスを許可する]** が有効になっているかどうかを確認します。
  - Azure AD 管理: Azure SQL Server で Azure AD 認証が使用されているかどうかを確認します。
  - Azure AD 管理: Azure SQL Server の Azure AD 管理者ユーザーまたはグループを入力します。

- SQL データベース:
  - SQL ロール。 Azure Purview MSI に **db_datareader** ロールが割り当てられているかどうかを確認します。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、以下の内容を学習しました。
> [!div class="checklist"]
>
> * Azure Purview に登録してスキャンする前に、Azure Purview の準備状況チェックリストを実行して、Azure サブスクリプションで構成が不足していないかどうかを大規模に確認します。

次のチュートリアルに進んで、全 Azure データ ソースで Azure Purview に必要なアクセスを識別し、必要な認証とネットワーク規則を設定する方法について学習します。

> [!div class="nextstepaction"]
> [Azure Purview MSI のデータ ソースへのアクセスを大規模に構成する](tutorial-msi-configuration.md)
