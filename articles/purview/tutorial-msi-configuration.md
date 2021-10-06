---
title: Azure Purview MSI のデータ ソースへのアクセスを大規模に構成する
description: このチュートリアルでは、Azure データ ソースのサブスクリプションで Azure MSI の設定を構成します。
author: zeinam
ms.author: zeinam
ms.service: purview
ms.subservice: purview-data-map
ms.topic: tutorial
ms.date: 09/27/2021
ms.openlocfilehash: 101d18cdecdc4fc7d4fb33b824500350f53b7b3f
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/29/2021
ms.locfileid: "129213396"
---
# <a name="tutorial-configure-access-to-data-sources-for-azure-purview-msi-at-scale"></a>チュートリアル: Azure Purview MSI のデータ ソースへのアクセスを大規模に構成する

データ ソースをスキャンするには、Azure Purview からアクセスできるようにする必要があります。 このチュートリアルは、Azure サブスクリプションの所有者と Azure Purview データ ソース管理者を対象としています。 これにより、必要なアクセスを特定し、Azure データ ソース全体で Azure Purview に必要な認証およびネットワーク規則を設定することができます。

このチュートリアル シリーズのパート 2 では、次のことを行います。

> [!div class="checklist"]
>
> * データ ソースを特定し、データ ソースのサブスクリプションの一覧を準備します。
> * スクリプトを実行して、不足しているロールベースのアクセス制御 (RBAC) や必要なネットワーク構成を Azure のデータ ソースで構成します。
> * 出力レポートを確認します。

## <a name="prerequisites"></a>前提条件

* データ ソースが配置されている Azure サブスクリプション。 Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)してください。
* [Azure Purview アカウント](create-catalog-portal.md)。
* Azure SQL Database、Azure Synapse Analytics、Azure SQL Managed Instance などのデータ ソースがある各サブスクリプションの Azure Key Vault リソース。
* [Azure Purview MSI Configuration](https://github.com/Azure/Purview-Samples/tree/master/Data-Source-MSI-Configuration) スクリプト。

> [!NOTE]
> Azure Purview MSI Configuration スクリプトは、Windows でのみ使用できます。
> このスクリプトは、現在、Azure Purview Managed Identity (MSI) でサポートされています。

> [!IMPORTANT]
> 運用環境にデプロイする前に、スクリプトによって Azure 環境で実行されるすべての変更をテストおよび検証することを強くお勧めします。

## <a name="prepare-azure-subscriptions-list-for-data-sources"></a>データ ソース用の Azure サブスクリプションの一覧の準備 

スクリプトを実行する前に、次の 4 つの列を持つ .csv ファイル (たとえば、"C:\temp\Subscriptions.csv) を作成します。
   
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

次の手順に従って、Windows コンピューターからスクリプトを実行します。

1. [Azure Purview MSI Configuration](https://github.com/Azure/Purview-Samples/tree/master/Data-Source-MSI-Configuration) スクリプトを任意の場所にダウンロードします。

2. ご使用のコンピューターで、Windows タスク バーの検索ボックスに「**PowerShell**」と入力します。 検索一覧で、 **[Windows PowerShell]** を選択したまま (または、右クリックして) **[管理者として実行]** を選択します。

3. PowerShell ウィンドウに、次のコマンドを入力します。 (`<path-to-script>` は、抽出したスクリプト ファイルがあるフォルダーのパスに置き換えてください)。

   ```powershell
   dir -Path <path-to-script> | Unblock-File
   ```

4. 次のコマンドを入力して、Azure コマンドレットをインストールします。

   ```powershell
   Install-Module -Name Az -AllowClobber -Scope CurrentUser
   ```
5. *[続行するには NuGet プロバイダーが必要です]* というプロンプトが表示された場合は、 **[Y]** と入力し、**Enter** を押します。

6. *[信頼されていないリポジトリ]* というプロンプトが表示された場合は、 **[A]** と入力し、**Enter** を押します。

7. 前の手順を繰り返して、`Az.Synapse` と `AzureAD` モジュールをインストールします。

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

ユーザーが次のロールとアクセス許可を持っていることを確認します。

Azure 環境でスクリプトを実行するには、少なくとも次のアクセス許可が必要です。

ロール | スコープ | 必要な理由 |
|-------|--------|--------|
| **グローバル閲覧者** | Azure AD テナント | Azure SQL 管理者ユーザー グループ メンバーシップと Azure Purview MSI を読み込むため |
| **全体管理者** | Azure AD テナント | Azure SQL マネージド インスタンスに **ディレクトリ閲覧者** ロールを割り当てるため |
| **Contributor** | Azure Purview アカウントが作成されたサブスクリプションまたはリソース グループ | Azure Purview のアカウント リソースを読み込んで、Key Vault のリソースとシークレットを作成するため |
| **所有者またはユーザー アクセス管理者** | Azure データ ソースが配置されている管理グループまたはサブスクリプション | RBAC を割り当てるため |
| **Contributor** | Azure データ ソースが配置されている管理グループまたはサブスクリプション | ネットワーク構成を設定するため |
| **SQL 管理者** (Azure AD Authentication) | Azure SQL Server インスタンスまたは Azure SQL マネージド インスタンス | Azure Purview に **db_datareader** ロールを割り当てるため |
| Azure キー コンテナーへのアクセス | Azure SQL Database、Azure SQL Managed Instance、または Azure Synapse 認証の Key Vault シークレットの取得または一覧表示へのアクセス |  


## <a name="run-the-client-side-readiness-script"></a>クライアント側の準備スクリプトを実行する

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
   .\purview-msi-configuration.ps1 -AzureDataType <DataType> -PurviewAccount <PurviewName> -PurviewSub <SubscriptionID>
   ```

   このコマンドを実行すると、ポップアップ ウィンドウが 2 回表示され、Azure Active Directory の資格情報を使用して Azure と Azure AD にサインインするように求められる場合があります。

環境内の Azure サブスクリプションとリソースの数によっては、レポートの作成に数分かかることがあります。 

キー コンテナーの資格情報が一致しない場合は、Azure SQL Server インスタンスにサインインするように求められることがあります。 特定のサーバーをスキップするには、資格情報を入力するか、**Enter** を押します。 

処理が完了したら、出力レポートを表示して変更内容を確認します。 


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

### <a name="what-configurations-are-included-in-the-script"></a>スクリプトにはどのような構成が含まれますか?

このスクリプトを使用すると、次のタスクを自動的に完了させることができます。

#### <a name="azure-blob-storage-blobstorage"></a>Azure Blob Storage (BlobStorage)

- RBAC。 選択したスコープで Azure Purview MSI に Azure RBAC **閲覧者** ロールを割り当てます。 割り当てを確認します。 
- RBAC。 選択したスコープの下の各サブスクリプションで、Azure RBAC **ストレージ BLOB データ閲覧者** ロールを Azure Purview MSI に割り当てます。 割り当てを確認します。
- ネットワーク。 プライベート エンドポイントがストレージ用に作成され、Blob Storage に対して有効になっているかどうかを報告します。
- サービス エンドポイント。 プライベート エンドポイントがオフの場合、サービス エンドポイントがオンになっているかどうかを確認し、 **[信頼された Microsoft サービスによるこのストレージ アカウントに対するアクセスを許可します]** を有効にします。

#### <a name="azure-data-lake-storage-gen2-adlsgen2"></a>Azure Data Lake Storage Gen2 (ADLSGen2)

- RBAC。 選択したスコープで Azure Purview MSI に Azure RBAC **閲覧者** ロールを割り当てます。 割り当てを確認します。 
- RBAC。 選択したスコープの下の各サブスクリプションで、Azure RBAC **ストレージ BLOB データ閲覧者** ロールを Azure Purview MSI に割り当てます。 割り当てを確認します。
- ネットワーク。 プライベート エンドポイントがストレージ用に作成され、Blob Storage に対して有効になっているかどうかを報告します。
- サービス エンドポイント。 プライベート エンドポイントがオフの場合、サービス エンドポイントがオンになっているかどうかを確認し、 **[信頼された Microsoft サービスによるこのストレージ アカウントに対するアクセスを許可します]** を有効にします。

#### <a name="azure-data-lake-storage-gen1-adlsgen1"></a>Azure Data Lake Storage Gen1 (ADLSGen1)

- ネットワーク。 サービス エンドポイントがオンになっていることを確認し、Data Lake Storage で **[すべての Azure サービスによるこの Data Lake Storage Gen1 アカウントへのアクセスを許可します]** を有効にします。
- アクセス許可。 Azure Purview MSI への読み取りまたは実行アクセス許可を割り当てます。 アクセスを確認します。 

#### <a name="azure-sql-database-azuresqldb"></a>Azure SQL Database (AzureSQLDB)

- SQL Server インスタンス
  - ネットワーク。 パブリック エンドポイントまたはプライベート エンドポイントが有効になっているかどうかを報告します。
  - ファイアウォール。 プライベート エンドポイントがオフの場合は、ファイアウォール規則を確認し、 **[Azure サービスおよびリソースにこのサーバーへのアクセスを許可する]** を有効にします。
  - Azure AD の管理。 Azure SQL Database に対する Azure AD 認証を有効にします。

- SQL データベース
  - SQL ロール。 Azure Purview MSI に **db_datareader** ロールを割り当てます。

#### <a name="azure-sql-managed-instance-azuresqlmi"></a>Azure SQL Managed Instance (AzureSQLMI)

- SQL Managed Instance サーバー:
  - ネットワーク。 パブリック エンドポイントまたはプライベート エンドポイントがオンになっていることを確認します。 パブリック エンドポイントがオフの場合は報告します。
  - ProxyOverride。 Azure SQL Managed Instance がプロキシまたはリダイレクトとして構成されていることを確認します。
  - ネットワーク。 NSG ルールを更新して、必要なポートを使用して SQL Server インスタンスへの AzureCloud 受信アクセスを許可します。  
    - リダイレクト: 1433 および 11000-11999
    
    または 
    - プロキシ: 3342
    
    このアクセスを確認します。 
  - Azure AD の管理。 Azure SQL Managed Instance に対する Azure AD 認証を有効にします。
  
- SQL データベース
  - SQL ロール。 Azure Purview MSI に **db_datareader** ロールを割り当てます。

#### <a name="azure-synapse-synapse-dedicated-pool"></a>Azure Synapse (Synapse) 専用プール

- RBAC。 選択したスコープで Azure Purview MSI に Azure RBAC **閲覧者** ロールを割り当てます。 割り当てを確認します。 
- RBAC。 選択したスコープの下の各サブスクリプションで、Azure RBAC **ストレージ BLOB データ閲覧者** ロールを Azure Purview MSI に割り当てます。 割り当てを確認します。
- SQL Server インスタンス (専用プール):
  - ネットワーク。 パブリック エンドポイントまたはプライベート エンドポイントがオンかどうかを報告します。
  - ファイアウォール。 プライベート エンドポイントがオフの場合は、ファイアウォール規則を確認し、 **[Azure サービスおよびリソースにこのサーバーへのアクセスを許可する]** を有効にします。
  - Azure AD の管理。 Azure SQL Database に対する Azure AD 認証を有効にします。

- SQL データベース
  - SQL ロール。 Azure Purview MSI に **db_datareader** ロールを割り当てます。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、以下の内容を学習しました。
> [!div class="checklist"]
>
> * 必要なアクセスを特定し、Azure データ ソース全体で Azure Purview に必要な認証およびネットワーク規則を設定します。

次のチュートリアルに進み、[Azure Purview で複数のソースを登録してスキャンする](register-scan-azure-multiple-sources.md)方法について確認します。
