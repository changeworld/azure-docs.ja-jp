---
title: 'チュートリアル: セキュア エンクレーブを使用する Always Encrypted の概要'
description: このチュートリアルでは、セキュリティで保護されたエンクレーブが設定された Always Encrypted の基本的な環境を Azure SQL Database で作成する方法と、SQL Server Management Studio (SSMS) を使用して、データのインプレース暗号化を行い、暗号化された列に対して高度な機密クエリを実行する方法について説明します。
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.devlang: ''
ms.topic: tutorial
author: jaszymas
ms.author: jaszymas
ms.reviwer: vanto
ms.date: 07/14/2021
ms.openlocfilehash: 31c9f128c1e98ce3b5a3e6a50f11e4afea33ecbc
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121747670"
---
# <a name="tutorial-getting-started-with-always-encrypted-with-secure-enclaves-in-azure-sql-database"></a>チュートリアル:セキュリティで保護されたエンクレーブが設定された Always Encrypted の使用を Azure SQL Database で開始する

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

このチュートリアルでは、[セキュリティで保護されたエンクレーブが設定された Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-enclaves) の使用を Azure SQL Database で開始する方法について説明します。 次のことを示します。

> [!div class="checklist"]
> - セキュリティで保護されたエンクレーブが設定された Always Encrypted をテストおよび評価するための環境を作成する方法。
> - SQL Server Management Studio (SSMS) を使用して、データのインプレース暗号化を行い、暗号化された列に対して高度な機密クエリを実行する方法。

## <a name="prerequisites"></a>前提条件

- 有効な Azure サブスクリプション アカウントがない場合は、[無料アカウントを作成](https://azure.microsoft.com/free/)してください。 リソースを作成して構成証明ポリシーを構成できるようにするには、サブスクリプションの共同作成者ロールまたは所有者ロールのメンバーである必要があります。

- SQL Server Management Studio (SSMS) バージョン 18.9.1 以降。 SSMS をダウンロードする方法については、「[SQL Server Management Studio (SSMS) のダウンロード](/sql/ssms/download-sql-server-management-studio-ssms)」を参照してください。

### <a name="powershell-requirements"></a>PowerShell の要件

> [!NOTE]
> このセクションに記載されている前提条件は、このチュートリアルの一部の手順に PowerShell を使用する場合にのみ適用されます。 代わりに Azure portal の使用を予定している場合は、このセクションを省略できます。

次の PowerShell モジュールがお使いのマシンにインストールされていることを確認します。

1. Az バージョン 5.6 以降。 Az PowerShell モジュールのインストール方法の詳細については、「[Azure Az PowerShell モジュールのインストール](/powershell/azure/install-az-ps)」を参照してください。 お使いのマシンにインストールされている Az モジュールのバージョンを確認するには、PowerShell セッションから次のコマンドを実行します。

    ```powershell
    Get-InstalledModule -Name Az
    ```

1. Az.Attestation 0.1.8 以降。 Az.Attestation PowerShell モジュールのインストール方法の詳細については、「[Az.Attestation PowerShell モジュールのインストール](../../attestation/quickstart-powershell.md#install-azattestation-powershell-module)」を参照してください。 お使いのマシンにインストールされている Az.Attestation モジュールのバージョンを確認するには、PowerShell セッションから次のコマンドを実行します。

    ```powershell
    Get-InstalledModule -Name Az.Attestation
    ```

バージョンが最小要件を満たしていない場合は、`Update-Module` コマンドを実行します。

PowerShell ギャラリーでは、トランスポート層セキュリティ (TLS) バージョン 1.0 と 1.1 が非推奨になっています。 TLS 1.2 以降のバージョンが推奨されます。 1\.2 より前の TLS バージョンを使用している場合は、次のエラーが発生する可能性があります。

- `WARNING: Unable to resolve package source 'https://www.powershellgallery.com/api/v2'`
- `PackageManagement\Install-Package: No match was found for the specified search criteria and module name.`

PowerShell ギャラリーの操作を続行するには、Install-Module コマンドの前に次のコマンドを実行してください。

```powershell
[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
```

## <a name="step-1-create-and-configure-a-server-and-a-dc-series-database"></a>手順 1: サーバーと DC シリーズ データベースを作成、構成する

この手順では、セキュア エンクレーブによる Always Encrypted に必要な DC シリーズのハードウェア世代を使用して、新しい Azure SQL Database 論理サーバーと新しいデータベースを作成します。 詳細については、「[DC シリーズ](service-tiers-sql-database-vcore.md#dc-series)」を参照してください。

# <a name="portal"></a>[ポータル](#tab/azure-portal)

1. [[Select SQL deployment option]\(SQL デプロイ オプションの選択\)](https://portal.azure.com/#create/Microsoft.AzureSQL) ページを参照します。
1. まだ Azure portal にサインインしていない場合は、求められたらサインインします。
1. **[SQL データベース]** で、 **[リソースの種類]** を **[単一データベース]** に設定し、 **[作成]** を選択します。

   :::image type="content" source="./media/single-database-create-quickstart/select-deployment.png" alt-text="Azure SQL に追加する":::

1. **[SQL データベースの作成]** フォームの **[基本]** タブにある **[プロジェクトの詳細]** で、目的の Azure **[サブスクリプション]** を選択します。
1. **[リソース グループ]** の **[新規作成]** を選択し、リソース グループの名前を入力し、 **[OK]** を選択します。
1. **[データベース名]** に、「*ContosoHR*」と入力します。
1. **[サーバー]** で、 **[新規作成]** を選択し、 **[新しいサーバー]** フォームに次の値を入力します。
   - **[サーバー名]** : 「*mysqlserver*」と入力し、一意にするためにいくつかの文字を追加します。 サーバー名は、サブスクリプション内で一意ではなく、Azure のすべてのサーバーに対してグローバルに一意である必要があるため、正確なサーバー名をここに示すことはできません。 mysqlserver135 のように入力してから、利用可能かどうかをポータルで確認できます。
   - **[サーバー管理者ログイン]** : 管理者のログイン名を入力します (例: *azureuser*)。
   - **パスワード**:要件を満たすパスワードを入力し、 **[パスワードの確認入力]** フィールドにもう一度入力します。
   - **[場所]** :ドロップダウン リストから場所を選択します。
      > [!IMPORTANT]
      > DC シリーズのハードウェア世代と Microsoft Azure Attestation の両方をサポートする場所 (Azure リージョン) を選択する必要があります。 DC シリーズをサポートするリージョンの一覧については、[DC シリーズの提供リージョン](service-tiers-sql-database-vcore.md#dc-series)に関する記述を参照してください。 Microsoft Azure Attestation が提供されるリージョンは、[こちら](https://azure.microsoft.com/global-infrastructure/services/?products=azure-attestation)でご覧いただけます。

   **[OK]** を選択します。
1. **[SQL エラスティック プールを使用しますか?]** を **[いいえ]** に設定したままにします。
1. **[コンピューティングとストレージ]** で、 **[データベースの構成]** を選択し、 **[構成の変更]** をクリックします。

   :::image type="content" source="./media/always-encrypted-enclaves/portal-configure-database.png" alt-text="データベースを構成する" lightbox="./media/always-encrypted-enclaves/portal-configure-database.png":::

1. **[DC シリーズ]** ハードウェア構成を選択し、 **[OK]** を選択します。

   :::image type="content" source="./media/always-encrypted-enclaves/portal-configure-dc-series-database.png" alt-text="DC シリーズ データベースの構成":::

1. **[適用]** を選択します。 
1. **[基本]** タブに戻り、 **[コンピューティングとストレージ]** が **[General Purpose]** 、 **[DC、2 仮想コア、32 GB ストレージ]** に設定されていることを確認します。
1. **ページの下部にある [Next: Networking]\(次へ: ネットワーク\)** を選択します。

   :::image type="content" source="./media/always-encrypted-enclaves/portal-configure-dc-series-database-basics.png" alt-text="DC シリーズ データベースの構成 - 基本":::

1. **[ネットワーク]** タブの **[接続方法]** で、 **[パブリック エンドポイント]** を選択します。
1. **[ファイアウォール規則]** で、 **[現在のクライアント IP アドレスを追加する]** を **[はい]** に設定します。 **[Azure サービスおよびリソースにこのサーバー グループへのアクセスを許可する]** を **[いいえ]** に設定したままにします。
1. ページ下部にある **[確認と作成]** を選択します。

   :::image type="content" source="./media/always-encrypted-enclaves/portal-configure-database-networking.png" alt-text="新しい SQL データベース - ネットワーク":::

1. **[確認と作成]** ページで、確認後、 **[作成]** を選択します。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. PowerShell コンソールを開き、必要なバージョンの Az をインポートします。

   ```PowerShell
   Import-Module "Az" -MinimumVersion "5.6.0"
   ```

1. Azure にサインインします。 必要に応じて、このチュートリアルに使用する[サブスクリプションに切り替えます](/powershell/azure/manage-subscriptions-azureps)。

   ```PowerShell
   Connect-AzAccount
   $subscriptionId = "<your subscription ID>"
   $context = Set-AzContext -Subscription $subscriptionId
   ```

1. 新しいリソース グループを作成する。

   > [!IMPORTANT]
   > リソース グループは、DC シリーズのハードウェア世代と Microsoft Azure Attestation の両方をサポートするリージョン (場所) に作成する必要があります。 DC シリーズをサポートするリージョンの一覧については、[DC シリーズの提供リージョン](service-tiers-sql-database-vcore.md#dc-series)に関する記述を参照してください。 Microsoft Azure Attestation が提供されるリージョンは、[こちら](https://azure.microsoft.com/global-infrastructure/services/?products=azure-attestation)でご覧いただけます。

   ```powershell
   $resourceGroupName = "<your new resource group name>"
   $location = "<Azure region supporting DC-series and Microsoft Azure Attestation>"
   New-AzResourceGroup -Name $resourceGroupName -Location $location
   ```

1. Azure SQL 論理サーバーを作成します。 プロンプトが表示されたら、サーバー管理者名とパスワードを入力します。 管理者の名前とパスワードを忘れないようにしてください。後でサーバーに接続するときに必要になります。 

   ```powershell
   $serverName = "<your server name>" 
   New-AzSqlServer -ServerName $serverName -ResourceGroupName $resourceGroupName -Location $location 
   ```

1. 指定された IP 範囲からのアクセスを許可するサーバー ファイアウォール規則を作成します。
  
   ```powershell
   $startIp = "<start of IP range>"
   $endIp = "<end of IP range>"
   $serverFirewallRule = New-AzSqlServerFirewallRule -ResourceGroupName $resourceGroupName `
    -ServerName $serverName `
    -FirewallRuleName "AllowedIPs" -StartIpAddress $startIp -EndIpAddress $endIp
   ```

1. DC シリーズ データベースを作成します。

   ```powershell
   $databaseName = "ContosoHR"
   $edition = "GeneralPurpose"
   $vCore = 2
   $generation = "DC"
   New-AzSqlDatabase -ResourceGroupName $resourceGroupName `
    -ServerName $serverName `
    -DatabaseName $databaseName `
    -Edition $edition `
    -Vcore $vCore `
    -ComputeGeneration $generation
   ```

---

## <a name="step-2-configure-an-attestation-provider"></a>手順 2:構成証明プロバイダーを構成する

この手順では、Microsoft Azure Attestation で構成証明プロバイダーを作成して構成します。 これは、データベースが使用するセキュア エンクレーブの構成証明を行うために必要となります。

# <a name="portal"></a>[ポータル](#tab/azure-portal)

1. [[構成証明プロバイダーの作成]](https://ms.portal.azure.com/#create/Microsoft.Attestation) ページに移動します。
1. **[Create attestation provider]\(構成証明プロバイダーの作成\)** ページで、次の情報を入力します。

   - **[サブスクリプション]** : Azure SQL 論理サーバーを作成したのと同じサブスクリプションを選択します。
   - **[リソース グループ]** : Azure SQL 論理サーバーで作成したのと同じリソース グループを選択します。
   - **[名前]** : 「*myattestprovider*」と入力し、一意にするためにいくつか文字を追加します。 名前はグローバルに一意である必要があるため、正確な構成証明プロバイダー名を指定して使用することはできません。 そのため、myattestprovider12345 のように入力して、それが利用可能かどうかをポータルで確認できます。
   - **[場所]** : Azure SQL 論理サーバーを作成した場所を選択します。
   - **[ポリシー署名者証明書ファイル]** : 署名されていないポリシーを構成する場合は、このフィールドは空のままにします。

1. 必要な情報を入力したら、 **[確認と作成]** を選択します。

   :::image type="content" source="./media/always-encrypted-enclaves/portal-create-attestation-provider-basics.png" alt-text="構成証明プロバイダーの作成":::

1. **［作成］** を選択します
1. 構成証明プロバイダーが作成されたら、 **[リソースに移動]** をクリックします。
1. 構成証明プロバイダーの **[概要]** タブで、**Attest URI** プロパティの値をクリップボードにコピーし、ファイルに保存します。 これは構成証明の URL です。後の手順で必要になります。  

   :::image type="content" source="./media/always-encrypted-enclaves/portal-attest-uri.png" alt-text="構成証明の URL":::

1. 下部のペインまたはウィンドウの左側のリソース メニューで **[ポリシー]** を選択します。
1. **[構成証明の種類]** を **[SGX-IntelSDK]** に設定します。
1. 上部のメニューにある **[構成]** を選択します。

   :::image type="content" source="./media/always-encrypted-enclaves/portal-configure-attestation-policy.png" alt-text="構成証明ポリシーの構成":::

1. **[ポリシーの形式]** を **[テキスト]** に 設定します。 **[ポリシー オプション]** は **[ポリシーの入力]** に設定されたままにします。
1. **[ポリシーのテキスト]** フィールドで、既定のポリシーを下のポリシーに置き換えます。 次のポリシーについては、「[構成証明プロバイダーを作成して構成する](always-encrypted-enclaves-configure-attestation.md#create-and-configure-an-attestation-provider)」を参照してください。

    ```output
    version= 1.0;
    authorizationrules 
    {
           [ type=="x-ms-sgx-is-debuggable", value==false ]
            && [ type=="x-ms-sgx-product-id", value==4639 ]
            && [ type=="x-ms-sgx-svn", value>= 0 ]
            && [ type=="x-ms-sgx-mrsigner", value=="e31c9e505f37a58de09335075fc8591254313eb20bb1a27e5443cc450b6e33e5"] 
        => permit();
    };
    ```

1. **[保存]** をクリックします。

   :::image type="content" source="./media/always-encrypted-enclaves/portal-edit-attestation-policy.png" alt-text="構成証明ポリシーの編集":::

1. 上部のメニューにある **[最新の情報に更新]** をクリックして、構成済みのポリシーを表示します。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. 次の構成証明ポリシーをコピーし、そのポリシーをテキストファイル (txt) に保存します。 次のポリシーについては、「[構成証明プロバイダーを作成して構成する](always-encrypted-enclaves-configure-attestation.md#create-and-configure-an-attestation-provider)」を参照してください。

    ```output
    version= 1.0;
    authorizationrules 
    {
           [ type=="x-ms-sgx-is-debuggable", value==false ]
            && [ type=="x-ms-sgx-product-id", value==4639 ]
            && [ type=="x-ms-sgx-svn", value>= 0 ]
            && [ type=="x-ms-sgx-mrsigner", value=="e31c9e505f37a58de09335075fc8591254313eb20bb1a27e5443cc450b6e33e5"] 
        => permit();
    };
    ```

1. 必要なバージョンの `Az.Attestation` をインポートします。  

   ```powershell
   Import-Module "Az.Attestation" -MinimumVersion "0.1.8"
   ```
  
1. 構成証明プロバイダーを作成します。

   ```powershell
   $attestationProviderName = "<your attestation provider name>" 
   New-AzAttestation -Name $attestationProviderName -ResourceGroupName $resourceGroupName -Location $location
   ```
1. 構成証明プロバイダーの構成証明の共同作成者ロールを自分に割り当てて、構成証明ポリシーを構成する権限があることを確認します。

   ```powershell
   New-AzRoleAssignment -SignInName $context.Account.Id `
    -RoleDefinitionName "Attestation Contributor" `
    -ResourceName $attestationProviderName `
    -ResourceType "Microsoft.Attestation/attestationProviders" `
    -ResourceGroupName $resourceGroupName
   ```
   
3. 構成証明ポリシーを構成します。
  
   ```powershell
   $policyFile = "<the pathname of the file from step 1 in this section>"
   $teeType = "SgxEnclave"
   $policyFormat = "Text"
   $policy=Get-Content -path $policyFile -Raw
   Set-AzAttestationPolicy -Name $attestationProviderName `
    -ResourceGroupName $resourceGroupName `
    -Tee $teeType `
    -Policy $policy `
    -PolicyFormat  $policyFormat
   ```

1. 構成証明 URL (構成証明プロバイダーの構成証明 URI) を取得します。

   ```powershell
   $attestationUrl = (Get-AzAttestation -Name $attestationProviderName -ResourceGroupName $resourceGroupName).AttestUri
   Write-Host "Your attestation URL is: $attestationUrl"
   ```

   この構成証明 URL は、`https://myattestprovider12345.eus.attest.azure.net` のようになります。

---


## <a name="step-3-populate-your-database"></a>手順 3: データベースを設定する

この手順では、テーブルを作成し、後で暗号化してクエリを実行するデータを設定します。

1. SSMS を開き、データベース接続で Always Encrypted を **有効にせずに**、作成した Azure SQL 論理サーバーの **ContosoHR** データベースに接続します。
    1. **[サーバーに接続]** ダイアログで、サーバーの完全修飾名 (例: *myserver135.database.windows.net*) を指定し、サーバーの作成時に指定した管理者のユーザー名とパスワードを入力します。
    2. **[オプション >>]** をクリックし、 **[接続プロパティ]** タブを選択します。(既定の master データベースではなく) 必ず **ContosoHR** データベースを選択します。 
    3. **[Always Encrypted]** タブを選択します。
    4. **[Always Encrypted を有効にする (列の暗号化)]** チェック ボックスがオンになって **いない** ことを確認します。

        :::image type="content" source="./media/always-encrypted-enclaves/connect-without-always-encrypted-ssms.png" alt-text="Always Encrypted なしで接続する":::

    5. **[Connect]** をクリックします。

2. **Employees** という名前の新しいテーブルを作成します。

    ```sql
    CREATE SCHEMA [HR];
    GO

    CREATE TABLE [HR].[Employees]
    (
        [EmployeeID] [int] IDENTITY(1,1) NOT NULL,
        [SSN] [char](11) NOT NULL,
        [FirstName] [nvarchar](50) NOT NULL,
        [LastName] [nvarchar](50) NOT NULL,
        [Salary] [money] NOT NULL
    ) ON [PRIMARY];
    GO
    ```

3. いくつかの従業員レコードを **Employees** テーブルに追加します。

    ```sql
    INSERT INTO [HR].[Employees]
            ([SSN]
            ,[FirstName]
            ,[LastName]
            ,[Salary])
        VALUES
            ('795-73-9838'
            , N'Catherine'
            , N'Abel'
            , $31692);

    INSERT INTO [HR].[Employees]
            ([SSN]
            ,[FirstName]
            ,[LastName]
            ,[Salary])
        VALUES
            ('990-00-6818'
            , N'Kim'
            , N'Abercrombie'
            , $55415);
    ```

## <a name="step-4-provision-enclave-enabled-keys"></a>手順 4:エンクレーブ対応キーをプロビジョニングする

この手順では、エンクレーブ計算を可能にする列マスター キーと列暗号化キーを作成します。

1. 前の手順の SSMS インスタンスを使用し、**オブジェクト エクスプローラー** でデータベースを展開して、 **[セキュリティ]**  >  **[Always Encrypted キー]** に移動します。
1. エンクレーブ対応の列マスター キーをプロビジョニングします。
    1. **[Always Encrypted キー]** を右クリックし、 **[新しい列マスター キー...]** を選択します。
    2. 列マスター キー名 **CMK1** を選択します。
    3. **[Windows 証明書ストア -現在のユーザー] または [Windows 証明書ストア - ローカル コンピューター]** か、 **[Azure Key Vault]** を選択します。
    4. **[エンクレーブ計算を許可する]** を選択します。
    5. [Azure Key Vault] を選択した場合は、Azure にサインインし、キー コンテナーを選択します。 Always Encrypted のキー コンテナーを作成する方法について詳しくは、「[Manage your key vaults from Azure portal](/archive/blogs/kv/manage-your-key-vaults-from-new-azure-portal)」(Azure portal からキー コンテナーを管理する) を参照してください。
    6. 証明書または Azure Key Value キーが既に存在する場合はそれを選択します。または、 **[証明書の生成]** ボタンをクリックして新しい証明書を作成します。
    7. **[OK]** を選択します。

        :::image type="content" source="./media/always-encrypted-enclaves/allow-enclave-computations.png" alt-text="エンクレーブ計算を許可する":::

1. 新しいエンクレーブ対応の列暗号化キーを作成します。

    1. **[Always Encrypted キー]** を右クリックし、 **[新しい列の暗号化キー]** を選択します。
    2. 新しい列暗号化キーの名前「**CEK1**」を入力します。
    3. **[列マスター キー]** ドロップダウンで、前の手順で作成した列マスター キーを選択します。
    4. **[OK]** を選択します。

## <a name="step-5-encrypt-some-columns-in-place"></a>手順 5:一部の列のインプレース暗号化を行う

この手順では、サーバー側エンクレーブ内の **SSN** および **Salary** 列に格納されたデータを暗号化し、そのデータに対して SELECT クエリをテストします。

1. 新しい SSMS インスタンスを開き、データベース接続で Always Encrypted を **有効にして** データベースに接続します。
    1. SSMS の新しいインスタンスを開始します。
    2. **[サーバーに接続]** ダイアログで、サーバーの完全修飾名 (例: *myserver135.database.windows.net*) を指定し、サーバーの作成時に指定した管理者のユーザー名とパスワードを入力します。
    3. **[オプション >>]** をクリックし、 **[接続プロパティ]** タブを選択します。(既定の master データベースではなく) 必ず **ContosoHR** データベースを選択します。 
    4. **[Always Encrypted]** タブを選択します。
    5. **[Always Encrypted を有効にする (列の暗号化)]** チェック ボックスが **オン** になっていることを確認します。
    6. 「[手順 2: 構成証明プロバイダーを構成する](#step-2-configure-an-attestation-provider)」の手順に従って取得したエンクレーブ構成証明 URL を指定します。 次のスクリーンショットを参照してください。

        :::image type="content" source="./media/always-encrypted-enclaves/connect-to-server-configure-attestation.png" alt-text="構成証明を使用して接続する":::

    7. **[接続]** を選択します。
    8. Always Encrypted クエリのパラメーター化を有効にするよう求められたら、 **[有効]** を選択します。

1. 同じ SSMS インスタンス (Always Encrypted が有効) を使用して、新しいクエリ ウィンドウを開き、次のステートメントを実行して **SSN** および **Salary** 列を暗号化します。

    ```sql
    ALTER TABLE [HR].[Employees]
    ALTER COLUMN [SSN] [char] (11) COLLATE Latin1_General_BIN2
    ENCRYPTED WITH (COLUMN_ENCRYPTION_KEY = [CEK1], ENCRYPTION_TYPE = Randomized, ALGORITHM = 'AEAD_AES_256_CBC_HMAC_SHA_256') NOT NULL
    WITH
    (ONLINE = ON);

    ALTER TABLE [HR].[Employees]
    ALTER COLUMN [Salary] [money]
    ENCRYPTED WITH (COLUMN_ENCRYPTION_KEY = [CEK1], ENCRYPTION_TYPE = Randomized, ALGORITHM = 'AEAD_AES_256_CBC_HMAC_SHA_256') NOT NULL
    WITH
    (ONLINE = ON);

    ALTER DATABASE SCOPED CONFIGURATION CLEAR PROCEDURE_CACHE;
    ```

    > [!NOTE]
    > 上記のスクリプト内でデータベース用のクエリ プラン キャッシュをクリアする ALTER DATABASE SCOPED CONFIGURATION CLEAR PROCEDURE_CACHE ステートメントに注目してください。 テーブルを変更したら、テーブルにアクセスするすべてのバッチおよびストアド プロシージャのプランをクリアして、パラメーター暗号化情報を更新する必要があります。 

1. **SSN** 列と **Salary** 列が暗号化されたことを確認するには、データベース接続の Always Encrypted が有効になって **いない** SSMS インスタンスで新しいクエリ ウィンドウを開き、下のステートメントを実行します。 クエリ ウィンドウで、**SSN** 列と **Salary** 列に暗号化された値が返される必要があります。 Always Encrypted が有効な SSMS インスタンスを使用して同じクエリを実行した場合は、復号化されたデータが表示されます。

    ```sql
    SELECT * FROM [HR].[Employees];
    ```

## <a name="step-6-run-rich-queries-against-encrypted-columns"></a>手順 6:暗号化された列に対して高度なクエリを実行する

暗号化された列に対して高度なクエリを実行できます。 いくつかのクエリ処理は、サーバー側エンクレーブ内で実行されます。 

1. Always Encrypted が有効になって **いる** SSMS インスタンスで、Always Encrypted のパラメーター化も有効になっていることを確認します。
    1. SSMS のメイン メニューから **[ツール]** を選択します。
    2. **[オプション...]** を選択します。
    3. **[クエリ実行]**  >  **[SQL Server]**  >  **[詳細]** の順に移動します。
    4. **[Always Encrypted のパラメーター化を有効にする]** がオンであることを確認します。
    5. **[OK]** を選択します。
2. 新しいクエリ ウィンドウを開き、下のクエリを貼り付けて実行します。 クエリでは、指定した検索条件を満たすプレーンテキスト値と行が返されます。

    ```sql
    DECLARE @SSNPattern [char](11) = '%6818';
    DECLARE @MinSalary [money] = $1000;
    SELECT * FROM [HR].[Employees]
    WHERE SSN LIKE @SSNPattern AND [Salary] >= @MinSalary;
    ```

3. Always Encrypted が有効になっていない SSMS インスタンスで同じクエリをもう一度試します。 エラーが発生します。

## <a name="next-steps"></a>次の手順

このチュートリアルを完了すると、次のいずれかのチュートリアルに進むことができます。
- [チュートリアル:セキュリティで保護されたエンクレーブが設定された Always Encrypted を使用する .NET アプリケーションの開発](/sql/connect/ado-net/sql/tutorial-always-encrypted-enclaves-develop-net-apps)」をご覧ください。
- [チュートリアル:セキュリティで保護されたエンクレーブが設定された Always Encrypted を使用する .NET Framework アプリケーションの開発](/sql/relational-databases/security/tutorial-always-encrypted-enclaves-develop-net-framework-apps)
- [チュートリアル:ランダム化された暗号化を使用してエンクレーブ対応の列でインデックスを作成して使用する](/sql/relational-databases/security/tutorial-creating-using-indexes-on-enclave-enabled-columns-using-randomized-encryption)

## <a name="see-also"></a>関連項目

- [セキュリティで保護されたエンクレーブが設定された Always Encrypted を構成して使用する](/sql/relational-databases/security/encryption/configure-always-encrypted-enclaves)
