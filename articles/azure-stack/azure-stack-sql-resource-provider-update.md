---
title: Azure Stack SQL リソース プロバイダーの更新 | Microsoft Docs
description: Azure Stack SQL リソース プロバイダーの更新方法について説明します。
services: azure-stack
documentationCenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/11/2019
ms.author: jeffgilb
ms.reviewer: jiahan
ms.lastreviewed: 01/11/2019
ms.openlocfilehash: 051c34c631795479cb5e5d0f67209bae89e82940
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/06/2019
ms.locfileid: "55766743"
---
# <a name="update-the-sql-resource-provider"></a>SQL リソース プロバイダーの更新

*適用対象: Azure Stack 統合システム*

Azure Stack が新しいビルドに更新される際に、新しい SQL リソース プロバイダーがリリースされることがあります。 既存のリソース プロバイダーが動作し続けている場合でも、できるだけ早く最新のビルドに更新することをお勧めします。 

SQL リソース プロバイダー バージョン 1.1.33.0 リリース以降の更新プログラムは累積的であり、バージョン 1.1.24.0 以降から開始する限り、リリースされた順序でインストールする必要はありません。 たとえば、バージョン 1.1.24.0 の SQL リソース プロバイダーを実行している場合、バージョン 1.1.33.0 以降にアップグレードできます。最初にバージョン 1.1.30.0 をインストールする必要はありません。 使用可能なリソース プロバイダーのバージョンと、それらがサポートされる Azure Stack のバージョンを確認するには、[リソース プロバイダーの展開の前提条件](./azure-stack-sql-resource-provider-deploy.md#prerequisites)に関するページで、バージョンの一覧を参照してください。

リソース プロバイダーの更新には *UpdateSQLProvider.ps1* スクリプトを使用します。 このスクリプトは、新しい SQL リソース プロバイダーのダウンロードに含まれています。 更新プロセスは、[リソース プロバイダーを展開する](./azure-stack-sql-resource-provider-deploy.md)ために使用されるプロセスに似ています。 更新スクリプトは DeploySqlProvider.ps1 スクリプトと同じ引数を使用し、証明書情報を提供する必要があります。

 > [!IMPORTANT]
 > リソース プロバイダーをアップグレードする前に、新しい機能、修正、およびデプロイに影響を与える可能性のある既知の問題に関する詳細についてリリース ノートを確認してください。

## <a name="update-script-processes"></a>更新スクリプトのプロセス

*UpdateSQLProvider.ps1* スクリプトは、最新のリソース プロバイダーのコードで新しい仮想マシン (VM) を作成します。

> [!NOTE]
> Marketplace の管理から最新の Windows Server 2016 Core のイメージをダウンロードすることをお勧めします。 更新プログラムをインストールする必要がある場合は、ローカルの依存関係のパスに MSU パッケージを **1 つ**配置できます。 この場所に複数の MSU ファイルがある場合、スクリプトは失敗します。

*UpdateSQLProvider.ps1* スクリプトは、新しい VM を作成した後、古いプロバイダー VM から次の設定を移行します。

* データベース情報
* ホスティング サーバー情報
* 必要な DNS レコード

## <a name="update-script-parameters"></a>更新スクリプトのパラメーター

**UpdateSQLProvider.ps1** PowerShell スクリプトを実行するときに、コマンド ラインから以下のパラメーターを指定できます。 必須パラメーターの指定がない場合、またはいずれかのパラメーターの検証が失敗した場合は、指定することを求められます。

| パラメーター名 | 説明 | コメントまたは既定値 |
| --- | --- | --- |
| **CloudAdminCredential** | 特権エンドポイントへのアクセスに必要な、クラウド管理者の資格情報。 | _必須_ |
| **AzCredential** | Azure Stack サービス管理者アカウントの資格情報。 Azure Stack のデプロイに使用したのと同じ資格情報を使用します。 | _必須_ |
| **VMLocalCredential** | SQL リソース プロバイダー VM のローカル Administrator アカウントの資格情報。 | _必須_ |
| **PrivilegedEndpoint** | 特権エンドポイントの IP アドレスまたは DNS 名。 |  _必須_ |
| **AzureEnvironment** | Azure Stack のデプロイに使用したサービス管理者アカウントの Azure 環境。 Azure AD のデプロイでのみ必須です。 サポートされている環境名は **AzureCloud**、**AzureUSGovernment**、または中国の Azure AD を使用している場合は **AzureChinaCloud** です。 | AzureCloud |
| **DependencyFilesLocalPath** | このディレクトリには、証明書 .pfx ファイルも配置する必要があります。 | "_1 つのノードの場合は省略可能。複数のノードの場合は必須_" |
| **DefaultSSLCertificatePassword** | .pfx 証明書のパスワード。 | _必須_ |
| **MaxRetryCount** | エラーが 発生した場合に各操作を再試行する回数。| 2 |
| **RetryDuration** |再試行間のタイムアウト間隔 (秒単位)。 | 120 |
| **アンインストール** | リソース プロバイダーと関連付けられているすべてのリソースを削除します。 | いいえ  |
| **DebugMode** | 障害発生時に自動クリーンアップが行われないようにします。 | いいえ  |

## <a name="update-script-powershell-example"></a>PowerShell 更新スクリプトの例
次に、管理者特権の PowerShell コンソールから実行できる *UpdateSQLProvider.ps1* スクリプトの使用例を示します。 必要に応じて変数情報とパスワードを変更してください。  

> [!NOTE]
> この更新プロセスは、Azure Stack 統合システムにのみ適用されます。

```powershell
# Install the AzureRM.Bootstrapper module, set the profile and install the AzureStack module
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2018-03-01-hybrid -Force
Install-Module -Name AzureStack -RequiredVersion 1.5.0

# Use the NetBIOS name for the Azure Stack domain. On the Azure Stack SDK, the default is AzureStack but this might have been changed at installation.
$domain = "AzureStack"

# For integrated systems, use the IP address of one of the ERCS virtual machines.
$privilegedEndpoint = "AzS-ERCS01"

# Provide the Azure environment used for deploying Azure Stack. Required only for Azure AD deployments. Supported values for the <environment name> parameter are AzureCloud, AzureChinaCloud or AzureUSGovernment depending which Azure subscription you are using. 
$AzureEnvironment = "<EnvironmentName>"

# Point to the directory where the resource provider installation files were extracted.
$tempDir = 'C:\TEMP\SQLRP'

# The service administrator account (this can be Azure AD or AD FS).
$serviceAdmin = "admin@mydomain.onmicrosoft.com"
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass)

# Set the credentials for the new resource provider VM.
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("sqlrpadmin", $vmLocalAdminPass)

# Add the cloudadmin credential required for privileged endpoint access.
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
  -AzureEnvironment $AzureEnvironment `
  -DefaultSSLCertificatePassword $PfxPass `
  -DependencyFilesLocalPath $tempDir\cert `

 ```

## <a name="next-steps"></a>次の手順

[SQL リソース プロバイダーの維持](azure-stack-sql-resource-provider-maintain.md)
