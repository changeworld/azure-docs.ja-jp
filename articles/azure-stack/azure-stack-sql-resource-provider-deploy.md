---
title: "Azure Stack での SQL データベースの使用 | Microsoft Docs"
description: "SQL データベースを Azure Stack にサービスとしてデプロイする方法と、SQL Server リソース プロバイダー アダプターの簡単なデプロイ手順について説明します。"
services: azure-stack
documentationCenter: 
author: JeffGoldner
manager: bradleyb
editor: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2018
ms.author: JeffGo
ms.openlocfilehash: bf52ed4986b4e0930b57721c0e38bbf748045a36
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/01/2018
---
# <a name="use-sql-databases-on-microsoft-azure-stack"></a>Microsoft Azure Stack で SQL データベースを使用する

*適用先: Azure Stack 統合システムと Azure Stack Development Kit*

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

## <a name="deploy-the-resource-provider"></a>リソース プロバイダーのデプロイ

1. まだ開発キットを登録していない場合は登録して、Marketplace の管理からダウンロードできる Windows Server 2016 Datacenter Core イメージをダウンロードします。 Windows Server 2016 Core イメージを使用する必要があります。 スクリプトを使用して [Windows Server 2016 イメージ](https://docs.microsoft.com/azure/azure-stack/azure-stack-add-default-image)を作成することもできます  (コア オプションを必ず選択してください)。 .NET 3.5 ランタイムは必要なくなりました。

2. 特権エンドポイント VM にアクセスできるホストにサインインします。

    - Azure Stack Development Kit のインストールで、物理ホストにサインインします。

    - マルチノード システムでは、ホストは特権エンドポイントにアクセスできるシステムである必要があります。
    
    >[!NOTE]
    > スクリプトが実行されるシステムは、最新バージョンの .NET ランタイムがインストールされている Windows 10 または Windows Server 2016 システムである*必要があります*。 それ以外の場合、インストールは失敗します。 Azure Stack SDK ホストはこの条件を満たしています。


3. SQL リソース プロバイダー バイナリをダウンロードします。 次に、自己解凍ツールを実行して、その内容を一時ディレクトリに抽出します。

    >[!NOTE] 
    > リソース プロバイダーのビルドは、Azure Stack のビルドに対応します。 必ず実行されている Azure Stack のバージョンに適合する正しいバイナリをダウンロードしてください。

    | Azure Stack のビルド | SQL リソース プロバイダー インストーラー |
    | --- | --- |
    |1.0.180102.3, 1.0.180103.2 または 1.0.180106.1 (マルチノード) | [SQL RP バージョン 1.1.14.0](https://aka.ms/azurestacksqlrp1712) |
    | 1.0.171122.1 | [SQL RP バージョン 1.1.12.0](https://aka.ms/azurestacksqlrp1711) |
    | 1.0.171028.1 | [SQL RP バージョン 1.1.8.0](https://aka.ms/azurestacksqlrp1710) |
  

4. Azure Stack のルート証明書は、特権エンドポイントから取得されます。 Azure Stack SDK では、このプロセスの一環として自己署名証明書が作成されます。 マルチノードの場合は、適切な証明書を提供する必要があります。

   独自の証明書を提供するには、次のように .pfx ファイルを **DependencyFilesLocalPath** に配置します。

    - \*.dbadapter.\<region\>.\<external fqdn\> のワイルドカード証明書、または sqladapter.dbadapter.\<region\>.\<external fqdn\> の一般名を持つ 1 つのサイト証明書のどちらか。

    - この証明書は信頼できる必要があります。 つまり、信頼チェーンが中間証明書の必要なしに存在する必要があります。

    - DependencyFilesLocalPath には 1 つの証明書ファイルしか存在しません。

    - このファイル名に特殊文字を含めることはできません。


5. **新しい**管理者特権の (管理の) PowerShell コンソールを開き、ファイルを抽出したディレクトリに変更します。 新しいウィンドウを使用すると、システムに不適切な PowerShell モジュールが既に読み込まれている場合に発生する可能性のある問題を回避できます。

6. [Azure PowerShell バージョン 1.2.11 をインストールします](azure-stack-powershell-install.md)。

7. DeploySqlProvider.ps1 スクリプトを実行します。このスクリプトは次の手順を実行します。

    - Azure Stack 上のストレージ アカウントに証明書とその他のアーティファクトをアップロードします。
    - ギャラリー パッケージを発行して、ギャラリーを通じて SQL データベースをデプロイできるようにします。
    - ホスティング サーバーをデプロイするためのギャラリー パッケージを発行します。
    - 手順 1 で作成した Windows Server 2016 イメージを使用して VM をデプロイした後、リソース プロバイダーをインストールします。
    - リソース プロバイダー VM にマップされるローカル DNS レコードを登録します。
    - リソース プロバイダーをローカル Azure Resource Manager (ユーザーと管理者) に登録します。

> [!NOTE]
> インストールが 90 分以上かかっている場合は、失敗している可能性があります。 インストールが失敗した場合は、画面とログ ファイルにエラー メッセージが表示されますが、デプロイは失敗した手順から再試行されます。 推奨されるメモリと vCPU 仕様を満たしていないシステムは、SQL リソース プロバイダーをデプロイできない場合があります。
>

PowerShell プロンプトから実行できる例を示します  (必要に応じてアカウント情報とパスワードを変更してください)。

```
# Install the AzureRM.Bootstrapper module, set the profile, and install the AzureRM and AzureStack modules.
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2017-03-09-profile
Install-Module -Name AzureStack -RequiredVersion 1.2.11 -Force

# Use the NetBIOS name for the Azure Stack domain. On the Azure Stack SDK, the default is AzureStack and the default prefix is AzS.
# For integrated systems, the domain and the prefix are the same.
$domain = "AzureStack"
$prefix = "AzS"
$privilegedEndpoint = "$prefix-ERCS01"

# Point to the directory where the resource provider installation files were extracted.
$tempDir = 'C:\TEMP\SQLRP'

# The service admin account (can be Azure Active Directory or Active Directory Federation Services).
$serviceAdmin = "admin@mydomain.onmicrosoft.com"
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass)

# Set credentials for the new Resource Provider VM.
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

| パラメーター名 | 説明 | コメントまたは既定値 |
| --- | --- | --- |
| **CloudAdminCredential** | 特権エンドポイントへのアクセスに必要な、クラウド管理者の資格情報。 | _必須_ |
| **AzCredential** | Azure Stack サービス管理者アカウントの資格情報。 Azure Stack のデプロイに使用したのと同じ資格情報を使用します。 | _必須_ |
| **VMLocalCredential** | SQL リソース プロバイダー VM のローカル Administrator アカウントの資格情報。 | _必須_ |
| **PrivilegedEndpoint** | 特権エンドポイントの IP アドレスまたは DNS 名。 |  _必須_ |
| **DependencyFilesLocalPath** | 証明書 .pfx ファイルはこのディレクトリにも配置する必要があります。 | _省略可能_ (マルチノードでは_必須_) |
| **DefaultSSLCertificatePassword** | .pfx 証明書のパスワード。 | _必須_ |
| **MaxRetryCount** | 障害がある場合に各操作を再試行する回数。| 2 |
| **RetryDuration** | 再試行間のタイムアウト間隔 (秒単位)。 | 120 |
| **アンインストール** | リソース プロバイダーと関連付けられているすべてのリソースを削除します (以下のメモを参照してください)。 | いいえ |
| **DebugMode** | 障害発生時に自動クリーンアップが行われないようにします。 | いいえ |


## <a name="verify-the-deployment-using-the-azure-stack-portal"></a>Azure Stack ポータルを使用してデプロイを確認する

> [!NOTE]
>  インストール スクリプトの実行が終了したら、ポータルを最新の情報に更新して [管理者] ブレードを表示する必要があります。


1. 管理ポータルにサービス管理者としてサインインします。

2. デプロイが成功したことを確認します。 **リソース グループ**に移動します。 **system.\<location\>.sqladapter** リソース グループを選択します。 4 つのすべてのデプロイが成功したことを確認します。

      ![SQL リソース プロバイダーのデプロイの確認](./media/azure-stack-sql-rp-deploy/sqlrp-verify.png)


## <a name="update-the-sql-resource-provider-adapter-multi-node-only-builds-1710-and-later"></a>SQL リソース プロバイダー アダプターを更新する (マルチノードのみ、ビルド 1710 以降)
Azure Stack ビルドが更新されると、新しい SQL リソース プロバイダー アダプターが毎回リリースされます。 既存のアダプターが動作し続ける場合があります。 ただし、Azure Stack を更新した後、最新のビルドにできるだけ早く更新することをお勧めします。 

更新プロセスは、既に説明したインストール プロセスに似ています。 最新のリソース プロバイダー コードを使用して新しい VM を作成します。 さらに、データベースとホスト サーバーの情報を含む設定をこの新しいインスタンスに移行します。 必要な DNS レコードも移行します。

すでに説明したのと同じ引数を使用して UpdateSQLProvider.ps1 スクリプトを使用します。 ここでも証明書を指定する必要があります。

> [!NOTE]
> この更新プロセスは、マルチノード システムでのみサポートされています。

```
# Install the AzureRM.Bootstrapper module, set the profile, and install the AzureRM and AzureStack modules.
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2017-03-09-profile
Install-Module -Name AzureStack -RequiredVersion 1.2.11 -Force

# Use the NetBIOS name for the Azure Stack domain. On the Azure Stack SDK, the default is AzureStack and the default prefix is AzS.
# For integrated systems, the domain and the prefix are the same.
$domain = "AzureStack"
$prefix = "AzS"
$privilegedEndpoint = "$prefix-ERCS01"

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

| パラメーター名 | 説明 | コメントまたは既定値 |
| --- | --- | --- |
| **CloudAdminCredential** | 特権エンドポイントへのアクセスに必要な、クラウド管理者の資格情報。 | _必須_ |
| **AzCredential** | Azure Stack サービス管理者アカウントの資格情報。 Azure Stack のデプロイに使用したのと同じ資格情報を使用します。 | _必須_ |
| **VMLocalCredential** | SQL リソース プロバイダー VM のローカル Administrator アカウントの資格情報。 | _必須_ |
| **PrivilegedEndpoint** | 特権エンドポイントの IP アドレスまたは DNS 名。 |  _必須_ |
| **DependencyFilesLocalPath** | 証明書 .pfx ファイルはこのディレクトリにも配置する必要があります。 | _省略可能_ (マルチノードでは_必須_) |
| **DefaultSSLCertificatePassword** | .pfx 証明書のパスワード。 | _必須_ |
| **MaxRetryCount** | 障害がある場合に各操作を再試行する回数。| 2 |
| **RetryDuration** |再試行間のタイムアウト間隔 (秒単位)。 | 120 |
| **アンインストール** | リソース プロバイダーと関連付けられているすべてのリソースを削除します (以下のメモを参照してください)。 | いいえ |
| **DebugMode** | 障害発生時に自動クリーンアップが行われないようにします。 | いいえ |



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
