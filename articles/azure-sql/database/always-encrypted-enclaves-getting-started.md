---
title: チュートリアル:セキュリティで保護されたエンクレーブが設定された Always Encrypted の使用を Azure SQL Database で開始する
description: このチュートリアルでは、セキュリティで保護されたエンクレーブが設定された Always Encrypted の基本的な環境を Azure SQL Database で作成する方法と、SQL Server Management Studio (SSMS) を使用して、データのインプレース暗号化を行い、暗号化された列に対して高度な機密クエリを実行する方法について説明します。
keywords: データの暗号化, sql 暗号化, データベース暗号化, 機密データ, Always Encrypted, セキュリティで保護されたエンクレーブ, SGX, 構成証明
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.devlang: ''
ms.topic: tutorial
author: jaszymas
ms.author: jaszymas
ms.reviwer: vanto
ms.date: 01/15/2021
ms.openlocfilehash: 94923b13181290a290f13339da5b05f6fdddff38
ms.sourcegitcommit: 25d1d5eb0329c14367621924e1da19af0a99acf1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/16/2021
ms.locfileid: "98252165"
---
# <a name="tutorial-getting-started-with-always-encrypted-with-secure-enclaves-in-azure-sql-database"></a>チュートリアル:セキュリティで保護されたエンクレーブが設定された Always Encrypted の使用を Azure SQL Database で開始する

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

> [!NOTE]
> Azure SQL Database のセキュリティで保護されたエンクレーブが設定された Always Encrypted は、現在、**パブリック プレビュー** 段階にあります。

このチュートリアルでは、[セキュリティで保護されたエンクレーブが設定された Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-enclaves) の使用を Azure SQL Database で開始する方法について説明します。 次のことを示します。

> [!div class="checklist"]
> - セキュリティで保護されたエンクレーブが設定された Always Encrypted をテストおよび評価するための環境を作成する方法。
> - SQL Server Management Studio (SSMS) を使用して、データのインプレース暗号化を行い、暗号化された列に対して高度な機密クエリを実行する方法。

## <a name="prerequisites"></a>前提条件

このチュートリアルでは、Azure PowerShell と [SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) が必要です。

### <a name="powershell-requirements"></a>PowerShell の要件

Azure PowerShell をインストールして実行する方法については、[Azure PowerShell の概要](https://docs.microsoft.com/powershell/azure)に関するページを参照してください。 

構成証明の操作をサポートするうえで必要な Az モジュールの最小バージョン:

- Az 4.5.0
- Az.Accounts 1.9.2
- Az.Attestation 0.1.8

以下のコマンドを実行して、すべての Az モジュールのインストール済みのバージョンを確認します。

```powershell
Get-InstalledModule
```

バージョンが最小要件を満たしていない場合は、`Update-Module` コマンドを実行します。

PowerShell ギャラリーでは、トランスポート層セキュリティ (TLS) バージョン 1.0 と 1.1 が非推奨になっています。 TLS 1.2 以降のバージョンが推奨されます。 1\.2 より前の TLS バージョンを使用している場合は、次のエラーが発生する可能性があります。

- `WARNING: Unable to resolve package source 'https://www.powershellgallery.com/api/v2'`
- `PackageManagement\Install-Package: No match was found for the specified search criteria and module name.`

PowerShell ギャラリーの操作を続行するには、Install-Module コマンドの前に次のコマンドを実行してください。

```powershell
[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
```

### <a name="ssms-requirements"></a>SSMS の要件

SSMS をダウンロードする方法については、「[SQL Server Management Studio (SSMS) のダウンロード](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)」を参照してください。

SSMS の必要な最小バージョンは 18.8 です。


## <a name="step-1-create-a-server-and-a-dc-series-database"></a>手順 1: サーバーと DC シリーズ データベースを作成する

 この手順では、DC シリーズのハードウェア構成を使用して、新しい Azure SQL Database 論理サーバーと新しいデータベースを作成します。 Azure SQL Database のセキュリティで保護されたエンクレーブが設定された Always Encrypted では、DC シリーズのハードウェア構成でサポートされている Intel SGX エンクレーブが使用されます。 詳細については、「[DC シリーズ](service-tiers-vcore.md#dc-series)」を参照してください。

1. PowerShell コンソールを開き、Azure にサインインします。 必要に応じて、このチュートリアルに使用する[サブスクリプションに切り替えます](https://docs.microsoft.com/powershell/azure/manage-subscriptions-azureps)。

  ```PowerShell
  Connect-AzAccount
  $subscriptionId = <your subscription ID>
  Set-AzContext -Subscription $serverSubscriptionId
  ```

2. データベース サーバーを格納するリソース グループを作成します。 

  ```powershell
  $serverResourceGroupName = "<server resource group name>"
  $serverLocation = "<Azure region that supports DC-series in SQL Database>"
  New-AzResourceGroup -Name $serverResourceGroupName -Location $serverLocation 
  ```

  > [!IMPORTANT]
  > リソース グループは、DC シリーズのハードウェア構成がサポートされているリージョンに作成する必要があります。 現在サポートされているリージョンの一覧については、[DC シリーズの提供状況](service-tiers-vcore.md#dc-series-1)に関するページを参照してください。

3. データベース サーバーを作成します。 プロンプトが表示されたら、サーバー管理者名とパスワードを入力します。

  ```powershell
  $serverName = "<server name>" 
  New-AzSqlServer -ServerName $serverName -ResourceGroupName $serverResourceGroupName -Location $serverLocation
  ```

4. 指定された IP 範囲からのアクセスを許可するサーバー ファイアウォール規則を作成します。
  
  ```powershell
  # The ip address range that you want to allow to access your server
  $startIp = "<start of IP range>"
  $endIp = "<end of IP range>"
  $serverFirewallRule = New-AzSqlServerFirewallRule -ResourceGroupName $resourceGroupName `
    -ServerName $serverName `
    -FirewallRuleName "AllowedIPs" -StartIpAddress $startIp -EndIpAddress $endIp
  ```

5. マネージド システム ID をサーバーに割り当てます。 これは、Microsoft Azure Attestation へのアクセスをサーバーに許可するために後で必要になります。

  ```powershell
  Set-AzSqlServer -ServerName $serverName -ResourceGroupName $serverResourceGroupName -AssignIdentity 
  ```

6. サーバーに割り当てられた ID のオブジェクト ID を取得します。 取得したオブジェクト ID を保存します。 この ID は後のセクションで必要になります。

  > [!NOTE]
  > 新しく割り当てられたマネージド システム ID が Azure Active Directory に反映されるまでに数秒かかることがあります。 以下のスクリプトで空の結果が返された場合は、再試行してください。

  ```PowerShell
  $server = Get-AzSqlServer -ServerName $serverName -ResourceGroupName $serverResourceGroupName 
  $serverObjectId = $server.Identity.PrincipalId
  $serverObjectId
  ```

7. DC シリーズ データベースを作成します。

  ```powershell
  $databaseName = "ContosoHR"
  $edition = "GeneralPurpose"
  $vCore = 2
  $generation = "DC"
  New-AzSqlDatabase -ResourceGroupName $serverResourceGroupName -ServerName $serverName -DatabaseName $databaseName -Edition $edition -Vcore $vCore -ComputeGeneration $generation
  ```

## <a name="step-2-configure-an-attestation-provider"></a>手順 2:構成証明プロバイダーを構成する

この手順では、Microsoft Azure Attestation で構成証明プロバイダーを作成して構成します。 これは、データベース サーバーのセキュリティで保護されたエンクレーブの構成証明を行うために必要となります。

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

2. `Az.Accounts` と `Az.Attestation` の必要なバージョンをインポートします。  

  ```powershell
  Import-Module "Az.Accounts" -MinimumVersion "1.9.2"
  Import-Module "Az.Attestation" -MinimumVersion "0.1.8"
  ```

3. 構成証明プロバイダー用のリソース グループを作成します。

  ```powershell
  $attestationLocation = $serverLocation
  $attestationResourceGroupName = "<attestation provider resource group name>"
  New-AzResourceGroup -Name $attestationResourceGroupName -Location $location  
  ```

4. 構成証明プロバイダーを作成します。 

  ```powershell
  $attestationProviderName = "<attestation provider name>" 
  New-AzAttestation -Name $attestationProviderName -ResourceGroupName $attestationResourceGroupName -Location $attestationLocation
  ```

5. 構成証明ポリシーを構成します。
  
  ```powershell
  $policyFile = "<the pathname of the file from step 1 in this section"
  $teeType = "SgxEnclave"
  $policyFormat = "Text"
  $policy=Get-Content -path $policyFile -Raw
  Set-AzAttestationPolicy -Name $attestationProviderName -ResourceGroupName $attestationResourceGroupName -Tee $teeType -Policy $policy -PolicyFormat  $policyFormat
  ```

6. 作成した構成証明プロバイダーへのアクセスを Azure SQL 論理サーバーに許可します。 この手順では、前にサーバーに割り当てたマネージド サービス ID のオブジェクト ID を使用します。

  ```powershell
  New-AzRoleAssignment -ObjectId $serverObjectId -RoleDefinitionName "Attestation Reader" -ResourceGroupName $attestationResourceGroupName  
  ```

7. 構成証明 URL を取得します。

  ```powershell
  $attestationProvider = Get-AzAttestation -Name $attestationProviderName -ResourceGroupName $attestationResourceGroupName 
  $attestationUrl = $attestationProvider.AttestUri + “/attest/SgxEnclave”
  Write-Host "Your attestation URL is: " $attestationUrl 
  ```

8.  SGX エンクレーブ用に構成した構成証明ポリシーを指す、取得した構成証明 URL を保存します。 この情報は後で必要になります。 この構成証明 URL は、`https://contososqlattestation.uks.attest.azure.net/attest/SgxEnclave` のようになります。

## <a name="step-3-populate-your-database"></a>手順 3: データベースを設定する

この手順では、テーブルを作成し、後で暗号化してクエリを実行するデータを設定します。

1. SSMS を開き、データベース接続で Always Encrypted を **有効にせずに**、作成した Azure SQL 論理サーバーの **ContosoHR** データベースに接続します。
    1. **[サーバーに接続]** ダイアログで、サーバー名 (例: *myserver123.database.windows.net*) を指定し、先ほど構成したユーザー名とパスワードを入力します。
    2. **[オプション >>]** をクリックし、 **[接続プロパティ]** タブを選択します。(既定の master データベースではなく) 必ず **ContosoHR** データベースを選択します。 
    3. **[Always Encrypted]** タブを選択します。
    4. **[Always Encrypted を有効にする (列の暗号化)]** チェック ボックスがオンになって **いない** ことを確認します。

        ![Always Encrypted なしで接続する](media/always-encrypted-enclaves/connect-without-always-encrypted-ssms.png)

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

        ![エンクレーブ計算を許可する](media/always-encrypted-enclaves/allow-enclave-computations.png)

1. 新しいエンクレーブ対応の列暗号化キーを作成します。

    1. **[Always Encrypted キー]** を右クリックし、 **[新しい列の暗号化キー]** を選択します。
    2. 新しい列暗号化キーの名前「**CEK1**」を入力します。
    3. **[列マスター キー]** ドロップダウンで、前の手順で作成した列マスター キーを選択します。
    4. **[OK]** を選択します。

## <a name="step-5-encrypt-some-columns-in-place"></a>手順 5:一部の列のインプレース暗号化を行う

この手順では、サーバー側エンクレーブ内の **SSN** および **Salary** 列に格納されたデータを暗号化し、そのデータに対して SELECT クエリをテストします。

1. 新しい SSMS インスタンスを開き、データベース接続で Always Encrypted を **有効にして** データベースに接続します。
    1. SSMS の新しいインスタンスを開始します。
    2. **[サーバーへの接続]** ダイアログで、サーバーの名前を指定し、認証方法を選択して、資格情報を指定します。
    3. **[オプション >>]** をクリックし、 **[接続プロパティ]** タブを選択します。(既定の master データベースではなく) 必ず **ContosoHR** データベースを選択します。 
    4. **[Always Encrypted]** タブを選択します。
    5. **[Always Encrypted を有効にする (列の暗号化)]** チェック ボックスがオンになっていることを確認します。
    6. 「[手順 2: 構成証明プロバイダーを構成する](#step-2-configure-an-attestation-provider)」の手順に従って取得したエンクレーブ構成証明 URL を指定します。 次のスクリーンショットを参照してください。

        ![構成証明を使用して接続する](media/always-encrypted-enclaves/connect-to-server-configure-attestation.png)

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
- [チュートリアル:セキュリティで保護されたエンクレーブが設定された Always Encrypted を使用する .NET アプリケーションの開発](https://docs.microsoft.com/sql/connect/ado-net/sql/tutorial-always-encrypted-enclaves-develop-net-apps)」をご覧ください。
- [チュートリアル:セキュリティで保護されたエンクレーブが設定された Always Encrypted を使用する .NET Framework アプリケーションの開発](https://docs.microsoft.com/sql/relational-databases/security/tutorial-always-encrypted-enclaves-develop-net-framework-apps)
- [チュートリアル:ランダム化された暗号化を使用してエンクレーブ対応の列でインデックスを作成して使用する](https://docs.microsoft.com/sql/relational-databases/security/tutorial-creating-using-indexes-on-enclave-enabled-columns-using-randomized-encryption)

## <a name="see-also"></a>参照

- [セキュリティで保護されたエンクレーブが設定された Always Encrypted を構成して使用する](https://docs.microsoft.com/sql/relational-databases/security/encryption/configure-always-encrypted-enclaves)