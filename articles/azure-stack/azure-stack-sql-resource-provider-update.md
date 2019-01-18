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
ms.date: 01/08/2019
ms.author: jeffgilb
ms.reviewer: georgel
ms.openlocfilehash: b39cc799218a4c6f865acac8b98f5fb977c83bdc
ms.sourcegitcommit: 818d3e89821d101406c3fe68e0e6efa8907072e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/09/2019
ms.locfileid: "54117794"
---
# <a name="update-the-sql-resource-provider"></a>SQL リソース プロバイダーの更新

*適用対象: Azure Stack 統合システム*

Azure Stack が新しいビルドに更新される際に、新しい SQL リソース プロバイダーがリリースされることがあります。 既存のアダプターが動作し続けている場合でも、できるだけ早く最新のビルドに更新することをお勧めします。

> [!IMPORTANT]
> 更新プログラムは、リリースされた順序でインストールする必要があります。 バージョンをスキップすることはできません。 [リソース プロバイダーを展開するための前提条件](./azure-stack-sql-resource-provider-deploy.md#prerequisites)の一覧を参照してください。

## <a name="overview"></a>概要

リソース プロバイダーの更新には *UpdateSQLProvider.ps1* スクリプトを使用します。 このスクリプトは、新しい SQL リソース プロバイダーのダウンロードに含まれています。 更新プロセスは、[リソース プロバイダーを展開する](./azure-stack-sql-resource-provider-deploy.md)ために使用されるプロセスに似ています。 更新スクリプトは DeploySqlProvider.ps1 スクリプトと同じ引数を使用し、証明書情報を提供する必要があります。

### <a name="update-script-processes"></a>更新スクリプトのプロセス

*UpdateSQLProvider.ps1* スクリプトは、最新のリソース プロバイダーのコードで新しい仮想マシン (VM) を作成します。

> [!NOTE]
> Marketplace の管理から最新の Windows Server 2016 Core のイメージをダウンロードすることをお勧めします。 更新プログラムをインストールする必要がある場合は、ローカルの依存関係のパスに MSU パッケージを **1 つ**配置できます。 この場所に複数の MSU ファイルがある場合、スクリプトは失敗します。

*UpdateSQLProvider.ps1* スクリプトは、新しい VM を作成した後、古いプロバイダー VM から次の設定を移行します。

* データベース情報
* ホスティング サーバー情報
* 必要な DNS レコード

### <a name="update-script-powershell-example"></a>PowerShell 更新スクリプトの例

管理者特権の PowerShell ISE セッションで次のスクリプトを編集して実行できます。 

必要に応じて、環境に適したアカウント情報とパスワードに変更することを忘れないでください。

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

## <a name="updatesqlproviderps1-parameters"></a>UpdateSQLProvider.ps1 パラメーター

スクリプトを実行するときに、コマンド ラインから次のパラメーターを指定できます。 必須パラメーターの指定がない場合、またはいずれかのパラメーターの検証が失敗した場合は、指定することを求められます。

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

## <a name="next-steps"></a>次の手順

[SQL リソース プロバイダーの維持](azure-stack-sql-resource-provider-maintain.md)
