---
title: Azure Stack での SQL データベースの使用 | Microsoft Docs
description: SQL データベースを Azure Stack にサービスとしてデプロイする方法と、SQL Server リソース プロバイダー アダプターの簡単なデプロイ手順について説明します。
services: azure-stack
documentationCenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2018
ms.author: mabrigg
ms.reviewer: jeffgo
ms.openlocfilehash: bd3618367f91fe043cc8412481b38a9c996a5275
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2018
---
# <a name="use-sql-databases-on-microsoft-azure-stack"></a>Microsoft Azure Stack で SQL データベースを使用する

*適用先: Azure Stack 統合システムと Azure Stack 開発キット*

SQL Server リソースプロバイダー アダプターを使用して、SQL データベースを [Azure Stack](azure-stack-poc.md) のサービスとして公開します。 リソースプロバイダーをインストールして 1 つまたは複数の SQL Server インスタンスに接続すると、御社および御社のユーザーは次のものを作成できます。
- クラウドネイティブ アプリ向けデータベース。
- SQL に基づいた Web サイト。
- SQL に基づいたワークロード。
SQL Server をホストする仮想マシン (VM) を毎回プロビジョニングする必要はありません。

リソース プロバイダーでは、[Azure SQL Database](https://azure.microsoft.com/services/sql-database/) のすべてのデータベース管理機能がサポートされるわけではありません。 たとえば、エラスティック データベース プールと、データベースのパフォーマンスを自動的に増減する機能は使用できません。 ただし、リソース プロバイダーでは、同様の作成、読み取り、更新、および削除 (CRUD) 操作がサポートされます。 API は、SQL Database と互換性がありません。

## <a name="sql-resource-provider-adapter-architecture"></a>SQL リソースプロバイダー アダプターのアーキテクチャ
リソース プロバイダーは、次の 3 つのコンポーネントで構成されています。

- **SQL リソース プロバイダー アダプター VM**。これはプロバイダー サービスを実行する Windows 仮想マシンです。
- **リソース プロバイダー自体**。これはプロビジョニング要求を処理し、データベース リソースを公開します。
- **SQL Server をホストするサーバー**。これはデータベースに容量を提供し、ホスティング サーバーと呼ばれます。

1 つ (以上) の SQL Server インスタンスを作成する、または外部 SQL Server インスタンスへのアクセスを提供する、あるいはその両方が必要です。

> [!NOTE]
> Azure Stack 統合システムにインストールされたホスティング サーバーは、テナント サブスクリプションから作成する必要があります。 既定のプロバイダー サブスクリプションからは作成できません。 それらは、テナント ポータルから、または適切なサインインで PowerShell セッションから作成する必要があります。 すべてのホスティング サーバーは課金対象の仮想マシンであり、適切なライセンスを必要とします。 サービス管理者は、テナント サブスクリプションの所有者になることができます。

## <a name="deploy-the-resource-provider"></a>リソース プロバイダーのデプロイ

1. まだ開発キットを登録していない場合は登録して、Marketplace の管理からダウンロードできる Windows Server 2016 Datacenter Core イメージをダウンロードします。 Windows Server 2016 Core イメージを使用する必要があります。 スクリプトを使用して [Windows Server 2016 イメージ](https://docs.microsoft.com/azure/azure-stack/azure-stack-add-default-image)を作成することもできます。 (コア オプションを必ず選択してください)

2. 特権エンドポイント VM にアクセスできるホストにサインインします。

    - Azure Stack Development Kit のインストールで、物理ホストにサインインします。

    - マルチノード システムでは、ホストは特権エンドポイントにアクセスできるシステムである必要があります。
    
    >[!NOTE]
    > スクリプトが実行されるシステムは、最新バージョンの .NET ランタイムがインストールされている Windows 10 または Windows Server 2016 システムである*必要があります*。 それ以外の場合、インストールは失敗します。 Azure Stack SDK ホストはこの条件を満たしています。


3. SQL リソース プロバイダー バイナリをダウンロードします。 次に、自己解凍ツールを実行して、その内容を一時ディレクトリに抽出します。

    >[!NOTE] 
    > リソース プロバイダーには、対応する最低限の Azure Stack ビルドがあります。 必ず実行されている Azure Stack のバージョンに適合する正しいバイナリをダウンロードしてください。

    | Azure Stack のビルド | SQL リソース プロバイダー インストーラー |
    | --- | --- |
    | 1802: 1.0.180302.1 | [SQL RP バージョン 1.1.18.0](https://aka.ms/azurestacksqlrp1802) |
    | 1712: 1.0.180102.3、1.0.180103.2 または 1.0.180106.1 (マルチノード) | [SQL RP バージョン 1.1.14.0](https://aka.ms/azurestacksqlrp1712) |
    | 1711: 1.0.171122.1 | [SQL RP バージョン 1.1.12.0](https://aka.ms/azurestacksqlrp1711) |
    | 1710: 1.0.171028.1 | [SQL RP バージョン 1.1.8.0](https://aka.ms/azurestacksqlrp1710) |
  

4. Azure Stack SDK では、このプロセスの一環として自己署名証明書が作成されます。 統合システムの場合は、適切な証明書を提供する必要があります。

   独自の証明書を提供するには、次のように .pfx ファイルを **DependencyFilesLocalPath** に配置します。

    - \*.dbadapter.\<region\>.\<external fqdn\> のワイルドカード証明書、または sqladapter.dbadapter.\<region\>.\<external fqdn\> の一般名を持つ 1 つのサイト証明書のどちらか。

    - この証明書は信頼できる必要があります。 つまり、信頼チェーンが中間証明書の必要なしに存在する必要があります。

    - DependencyFilesLocalPath パラメーターによって示されるディレクトリには証明書ファイル 1 つのみが存在できます。

    - このファイル名に特殊文字やスペースを含めることはできません。


5. **新しい**管理者特権の (管理の) PowerShell コンソールを開き、ファイルを抽出したディレクトリに変更します。 新しいウィンドウを使用すると、システムに不適切な PowerShell モジュールが既に読み込まれている場合に発生する可能性のある問題を回避できます。

6. [Azure PowerShell バージョン 1.2.11 をインストールします](azure-stack-powershell-install.md)。

7. DeploySqlProvider.ps1 スクリプトを実行します。このスクリプトは次の手順を実行します。

    - Azure Stack 上のストレージ アカウントに証明書とその他のアーティファクトをアップロードします。
    - ギャラリー パッケージを発行して、ギャラリーを通じて SQL データベースをデプロイできるようにします。
    - ホスティング サーバーをデプロイするためのギャラリー パッケージを発行します。
    - 手順 1 で作成した Windows Server 2016 イメージを使用して VM をデプロイした後、リソース プロバイダーをインストールします。
    - リソース プロバイダー VM にマップされるローカル DNS レコードを登録します。
    - リソース プロバイダーをローカル Azure Resource Manager (ユーザーと管理者) に登録します。
    - RP のインストール時に、必要に応じて、1 つの Windows 更新プログラムをインストールする

8. Marketplace の管理から最新の Windows Server 2016 Core のイメージをダウンロードすることをお勧めします。 更新プログラムをインストールする必要がある場合は、ローカルの依存関係のパスに .MSU パッケージを 1 つ配置できます。 MSU ファイルが複数検出されると、スクリプトは失敗します。


PowerShell プロンプトから実行できる例を示します。 (必要に応じてアカウント情報とパスワードを変更してください)。

```
# Install the AzureRM.Bootstrapper module, set the profile, and install the AzureRM and AzureStack modules.
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2017-03-09-profile
Install-Module -Name AzureStack -RequiredVersion 1.2.11 -Force

# Use the NetBIOS name for the Azure Stack domain. On the Azure Stack SDK, the default is AzureStack but could have been changed at install time.
$domain = "AzureStack"

# For integrated systems, use the IP address of one of the ERCS virtual machines
$privilegedEndpoint = "AzS-ERCS01"

# Point to the directory where the resource provider installation files were extracted.
$tempDir = 'C:\TEMP\SQLRP'

# The service admin account (can be Azure Active Directory or Active Directory Federation Services).
$serviceAdmin = "admin@mydomain.onmicrosoft.com"
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass)

# Set credentials for the new resource provider VM local administrator account
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("sqlrpadmin", $vmLocalAdminPass)

# And the cloudadmin credential that's required for privileged endpoint access.
$CloudAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$CloudAdminCreds = New-Object System.Management.Automation.PSCredential ("$domain\cloudadmin", $CloudAdminPass)

# Change the following as appropriate.
$PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force

# Change directory to the folder where you extracted the installation files.
# Then adjust the endpoints.
. $tempDir\DeploySQLProvider.ps1 -AzCredential $AdminCreds `
  -VMLocalCredential $vmLocalAdminCreds `
  -CloudAdminCredential $cloudAdminCreds `
  -PrivilegedEndpoint $privilegedEndpoint `
  -DefaultSSLCertificatePassword $PfxPass `
  -DependencyFilesLocalPath $tempDir\cert
 ```

### <a name="deploysqlproviderps1-parameters"></a>DeploySqlProvider.ps1 パラメーター
これらのパラメーターをコマンド ラインで指定できます。 必須パラメーターの指定がない場合、またはいずれかのパラメーターの検証が失敗した場合は、指定することを求められます。

| パラメーター名 | [説明] | コメントまたは既定値 |
| --- | --- | --- |
| **CloudAdminCredential** | 特権エンドポイントへのアクセスに必要な、クラウド管理者の資格情報。 | _必須_ |
| **AzCredential** | Azure Stack サービス管理者アカウントの資格情報。 Azure Stack のデプロイに使用したのと同じ資格情報を使用します。 | _必須_ |
| **VMLocalCredential** | SQL リソース プロバイダー VM のローカル Administrator アカウントの資格情報。 | _必須_ |
| **PrivilegedEndpoint** | 特権エンドポイントの IP アドレスまたは DNS 名。 |  _必須_ |
| **DependencyFilesLocalPath** | 証明書 .pfx ファイルはこのディレクトリにも配置する必要があります。 | _省略可能_ (マルチノードでは_必須_) |
| **DefaultSSLCertificatePassword** | .pfx 証明書のパスワード。 | _必須_ |
| **MaxRetryCount** | 障害がある場合に各操作を再試行する回数。| 2 |
| **RetryDuration** | 再試行間のタイムアウト間隔 (秒単位)。 | 120 |
| **アンインストール** | リソース プロバイダーと関連付けられているすべてのリソースを削除します (以下のメモを参照してください)。 | いいえ  |
| **DebugMode** | 障害発生時に自動クリーンアップが行われないようにします。 | いいえ  |


## <a name="verify-the-deployment-using-the-azure-stack-portal"></a>Azure Stack ポータルを使用してデプロイを確認する

> [!NOTE]
>  インストール スクリプトの実行が終了したら、ポータルを最新の情報に更新して [管理者] ブレードを表示する必要があります。


1. 管理ポータルにサービス管理者としてサインインします。

2. デプロイが成功したことを確認します。 **リソース グループ**に移動します。 **system.\<location\>.sqladapter** リソース グループを選択します。 4 つのすべてのデプロイが成功したことを確認します。

      ![SQL リソース プロバイダーのデプロイの確認](./media/azure-stack-sql-rp-deploy/sqlrp-verify.png)


## <a name="update-the-sql-resource-provider-adapter-multi-node-only-builds-1710-and-later"></a>SQL リソース プロバイダー アダプターを更新する (マルチノードのみ、ビルド 1710 以降)
Azure Stack ビルドの更新時に、新しい SQL リソース プロバイダー アダプターがリリースされる場合があります。 既存のアダプターが動作し続けている場合でも、できるだけ早く最新のビルドに更新することをお勧めします。 更新プログラムは指定された順序でインストールする必要があります: バージョンを省略することはできません (手順 3. [リソース プロバイダーをデプロイする](#deploy-the-resource-provider)の表を参照してください)。

リソース プロバイダーの更新には *UpdateSQLProvider.ps1* スクリプトを使用します。 プロセスは、この記事の[リソース プロバイダーをデプロイする](#deploy-the-resource-provider)セクションに記述されている、リソース プロバイダーをインストールするプロセスと類似しています。 スクリプトはリソース プロバイダーのダウンロードに含まれています。

*UpdateSQLProvider.ps1* スクリプトは、最新のリソース プロバイダーのコードを使って新しい VM を作成し、古い VM から新しい VM に設定を移行します。 移行される設定には、データベースおよびホスティング サーバーの情報や、必要な DNS レコードが含まれます。

スクリプトでは、DeploySqlProvider.ps1 スクリプト用に記述されているものと同じ引数の使用が必要になります。 証明書も同様に指定します。 

Marketplace の管理から最新の Windows Server 2016 Core のイメージをダウンロードすることをお勧めします。 更新プログラムをインストールする必要がある場合は、ローカルの依存関係のパスに .MSU パッケージを 1 つ配置できます。 MSU ファイルが複数検出されると、スクリプトは失敗します。

次に、PowerShell プロンプトから実行することができる *UpdateSQLProvider.ps1* スクリプトの例を示します。 必要に応じてアカウント情報とパスワードを変更してください。 

> [!NOTE]
> 更新プロセスは、統合システムにのみに適用されます。

```
# Install the AzureRM.Bootstrapper module, set the profile, and install the AzureRM and AzureStack modules.
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2017-03-09-profile
Install-Module -Name AzureStack -RequiredVersion 1.2.11 -Force

# Use the NetBIOS name for the Azure Stack domain. On the Azure Stack SDK, the default is AzureStack but could have been changed at install time.
$domain = "AzureStack"

# For integrated systems, use the IP address of one of the ERCS virtual machines
$privilegedEndpoint = "AzS-ERCS01"

# Point to the directory where the resource provider installation files were extracted.
$tempDir = 'C:\TEMP\SQLRP'

# The service admin account (can be Azure AD or AD FS).
$serviceAdmin = "admin@mydomain.onmicrosoft.com"
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass)

# Set credentials for the new Resource Provider VM.
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("sqlrpadmin", $vmLocalAdminPass)

# And the cloudadmin credential required for privileged endpoint access.
$CloudAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$CloudAdminCreds = New-Object System.Management.Automation.PSCredential ("$domain\cloudadmin", $CloudAdminPass)

# Change the following as appropriate.
$PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force

# Change directory to the folder where you extracted the installation files.
# Then adjust the endpoints.
. $tempDir\UpdateSQLProvider.ps1 -AzCredential $AdminCreds `
  -VMLocalCredential $vmLocalAdminCreds `
  -CloudAdminCredential $cloudAdminCreds `
  -PrivilegedEndpoint $privilegedEndpoint `
  -DefaultSSLCertificatePassword $PfxPass `
  -DependencyFilesLocalPath $tempDir\cert
 ```

### <a name="updatesqlproviderps1-parameters"></a>UpdateSQLProvider.ps1 パラメーター
これらのパラメーターをコマンド ラインで指定できます。 必須パラメーターの指定がない場合、またはいずれかのパラメーターの検証が失敗した場合は、指定することを求められます。

| パラメーター名 | [説明] | コメントまたは既定値 |
| --- | --- | --- |
| **CloudAdminCredential** | 特権エンドポイントへのアクセスに必要な、クラウド管理者の資格情報。 | _必須_ |
| **AzCredential** | Azure Stack サービス管理者アカウントの資格情報。 Azure Stack のデプロイに使用したのと同じ資格情報を使用します。 | _必須_ |
| **VMLocalCredential** | SQL リソース プロバイダー VM のローカル Administrator アカウントの資格情報。 | _必須_ |
| **PrivilegedEndpoint** | 特権エンドポイントの IP アドレスまたは DNS 名。 |  _必須_ |
| **DependencyFilesLocalPath** | 証明書 .pfx ファイルはこのディレクトリにも配置する必要があります。 | _省略可能_ (マルチノードでは_必須_) |
| **DefaultSSLCertificatePassword** | .pfx 証明書のパスワード。 | _必須_ |
| **MaxRetryCount** | 障害がある場合に各操作を再試行する回数。| 2 |
| **RetryDuration** |再試行間のタイムアウト間隔 (秒単位)。 | 120 |
| **アンインストール** | リソース プロバイダーと関連付けられているすべてのリソースを削除します (以下のメモを参照してください)。 | いいえ  |
| **DebugMode** | 障害発生時に自動クリーンアップが行われないようにします。 | いいえ  |


## <a name="collect-diagnostic-logs"></a>診断ログの収集
SQL リソース プロバイダーは、ロック ダウンされた仮想マシンです。 仮想マシンからログを収集することが必要になった場合は、、そのために PowerShell Just Enough Administration (JEA) エンドポイント _DBAdapterDiagnostics_ が提供されています。 このエンドポイントで使用できる 2 つのコマンドが 2 つあります。

* Get-AzsDBAdapterLog - RP 診断ログを含む zip パッケージを準備し、セッション ユーザー ドライブに配置します。 このコマンドは、パラメーターなしで呼び出すことができます。過去 4 時間分のログが収集されます。
* Remove-AzsDBAdapterLog - リソース プロバイダー VM 上の既存のログ パッケージを削除します。

RP ログを抽出する診断エンドポイントに接続するために、RP 展開中または更新中に _dbadapterdiag_ というユーザー アカウントが作成されます。 このアカウントのパスワードは、展開/更新中に指定したローカル管理者アカウントのパスワードと同じです。

これらのコマンドを使用するには、リソース プロバイダーの仮想マシンにリモート PowerShell セッションを作成し、コマンドを呼び出す必要があります。 必要に応じて、FromDate および ToDate パラメーターを指定できます。 これらの一方または両方を指定しない場合、FromDate は現在の時刻より 4 時間前になり、ToDate は現在の時刻になります。

このサンプル スクリプトは、これらのコマンドの使用方法を示します。

```
# Create a new diagnostics endpoint session.
$databaseRPMachineIP = '<RP VM IP>'
$diagnosticsUserName = 'dbadapterdiag'
$diagnosticsUserPassword = '<see above>'

$diagCreds = New-Object System.Management.Automation.PSCredential `
        ($diagnosticsUserName, $diagnosticsUserPassword)
$session = New-PSSession -ComputerName $databaseRPMachineIP -Credential $diagCreds `
        -ConfigurationName DBAdapterDiagnostics

# Sample captures logs from the previous one hour
$fromDate = (Get-Date).AddHours(-1)
$dateNow = Get-Date
$sb = {param($d1,$d2) Get-AzSDBAdapterLog -FromDate $d1 -ToDate $d2}
$logs = Invoke-Command -Session $session -ScriptBlock $sb -ArgumentList $fromDate,$dateNow

# Copy the logs
$sourcePath = "User:\{0}" -f $logs
$destinationPackage = Join-Path -Path (Convert-Path '.') -ChildPath $logs
Copy-Item -FromSession $session -Path $sourcePath -Destination $destinationPackage

# Cleanup logs
$cleanup = Invoke-Command -Session $session -ScriptBlock {Remove- AzsDBAdapterLog }
# Close the session
$session | Remove-PSSession
```

## <a name="maintenance-operations-integrated-systems"></a>保守操作 (統合システム)
SQL リソース プロバイダーは、ロック ダウンされた仮想マシンです。 リソース プロバイダー仮想マシンのセキュリティの更新は、PowerShell Just Enough Administration (JEA) エンドポイント _DBAdapterMaintenance_ から実行できます。

これらの操作のためのスクリプトが、RP のインストール パッケージで提供されています。

### <a name="update-the-virtual-machine-operating-system"></a>仮想マシンのオペレーティング システムの更新
Windows Server VM を更新するには、いくつかの方法があります。
* 現在パッチが適用された Windows Server 2016 Core イメージを使用して最新のリソース プロバイダーのパッケージをインストールする
* RP の更新またはインストール中に Windows 更新プログラム パッケージをインストールする


### <a name="update-the-virtual-machine-windows-defender-definitions"></a>仮想マシンの Windows Defender の定義を更新する

これらの手順に従って Defender の定義を更新します。

1. [Windows Defender の定義](https://www.microsoft.com/en-us/wdsi/definitions)から Windows Defender 定義の更新をダウンロードします。

    そのページの [Manually download and install the definitions]\(定義を手動でダウンロードしてインストールする\) から "Windows Defender Antivirus for Windows 10 および Windows 8.1" 64 ビット ファイルをダウンロードします。 
    
    直接リンク: https://go.microsoft.com/fwlink/?LinkID=121721&arch=x64

2. SQL RP アダプター仮想マシンのメンテナンス エンドポイントに対し PowerShell セッションを作成します。
3. メンテナンス エンドポイントのセッションを使用して DB アダプター コンピューターに、定義更新ファイルをコピーします。
4. メンテナンス PowerShell セッションで _Update-DBAdapterWindowsDefenderDefinitions_ コマンドを呼び出します。
5. インストール後に使用される定義更新ファイルを削除することをお勧めします。 定義更新ファイルは、メンテナンス セッションで _Remove-ItemOnUserDrive_ コマンドを使用して削除できます。


Defender の定義を更新するサンプル スクリプトを次に示します (仮想マシンのアドレスまたは名前を実際の値に置き換えてください)。

```
# Set credentials for the diagnostic user
$diagPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$diagCreds = New-Object System.Management.Automation.PSCredential `
    ("dbadapterdiag", $vmLocalAdminPass)$diagCreds = Get-Credential

# Public IP Address of the DB adapter machine
$databaseRPMachine  = "XX.XX.XX.XX"
$localPathToDefenderUpdate = "C:\DefenderUpdates\mpam-fe.exe"
 
# Download Windows Defender update definitions file from https://www.microsoft.com/en-us/wdsi/definitions. 
Invoke-WebRequest -Uri https://go.microsoft.com/fwlink/?LinkID=121721&arch=x64 `
    -Outfile $localPathToDefenderUpdate 

# Create session to the maintenance endpoint
$session = New-PSSession -ComputerName $databaseRPMachine `
    -Credential $diagCreds -ConfigurationName DBAdapterMaintenance
# Copy defender update file to the db adapter machine
Copy-Item -ToSession $session -Path $localPathToDefenderUpdate `
     -Destination "User:\mpam-fe.exe"
# Install the update file
Invoke-Command -Session $session -ScriptBlock `
    {Update-AzSDBAdapterWindowsDefenderDefinitions -DefinitionsUpdatePackageFile "User:\mpam-fe.exe"}
# Cleanup the definitions package file and session
Invoke-Command -Session $session -ScriptBlock `
    {Remove-AzSItemOnUserDrive -ItemPath "User:\mpam-fe.exe"}
$session | Remove-PSSession
```

## <a name="remove-the-sql-resource-provider-adapter"></a>SQL リソースプロバイダー アダプターを削除する

リソース プロバイダーを削除するには、最初にすべての依存関係を削除する必要があります。

1. このバージョンの SQL リソースプロバイダー アダプターに対してダウンロードした元のデプロイ パッケージがあることを確認します。

2. すべてのユーザー データベースをリソース プロバイダーから削除する必要があります  (ユーザー データベースを削除してもデータは削除されません。)このタスクは、ユーザー自身で実行する必要があります。

3. 管理者は、SQL リソース プロバイダー アダプターからホスティング サーバーを削除する必要があります。

4. 管理者は、SQL リソース プロバイダー アダプターを参照するプランをすべて削除する必要があります。

5. 管理者は、SQL リソース プロバイダー アダプターに関連付けられているすべての SKU とクォータを削除する必要があります。

6. 次の要素を使用してデプロイ スクリプトを再実行します。
    - -Uninstall パラメーター
    - Azure Resource Manager エンドポイント
    - DirectoryTenantID
    - サービス管理者アカウントの資格情報


## <a name="next-steps"></a>次の手順

[ホスティング サーバーを追加](azure-stack-sql-resource-provider-hosting-servers.md)し、[データベースを作成](azure-stack-sql-resource-provider-databases.md)します。

[MySQL Server リソース プロバイダー](azure-stack-mysql-resource-provider-deploy.md)や [App Services リソース プロバイダー](azure-stack-app-service-overview.md)のような他の [PaaS サービス](azure-stack-tools-paas-services.md)を試します。
