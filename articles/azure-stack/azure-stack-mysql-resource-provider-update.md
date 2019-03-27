---
title: Azure Stack MySQL リソース プロバイダーの更新 | Microsoft Docs
description: Azure Stack MySQL リソース プロバイダーの更新方法について説明します。
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
ms.openlocfilehash: f38d27c6f82533265705ff5483bfe835c81c9ce6
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/07/2019
ms.locfileid: "55817293"
---
# <a name="update-the-mysql-resource-provider"></a>MySQL リソース プロバイダーを更新する 

*適用対象: Azure Stack 統合システム*

Azure Stack ビルドの更新時に、新しい MySQL リソース プロバイダー アダプターがリリースされる場合があります。 既存のアダプターが動作し続けている場合でも、できるだけ早く最新のビルドに更新することをお勧めします。 

MySQL リソース プロバイダー バージョン 1.1.33.0 リリース以降の更新プログラムは累積的であり、バージョン 1.1.24.0 以降から開始する限り、リリースされた順序でインストールする必要はありません。 たとえば、バージョン 1.1.24.0 の MySQL リソース プロバイダーを実行している場合、最初にバージョン 1.1.30.0 をインストールしなくても、バージョン 1.1.33.0 以降にアップグレードできます。 使用可能なリソース プロバイダーのバージョンと、それらがサポートされる Azure Stack のバージョンを確認するには、[リソース プロバイダーのデプロイの前提条件に関する記事](./azure-stack-mysql-resource-provider-deploy.md#prerequisites)で、バージョンの一覧を参照してください。

リソース プロバイダーの更新には **UpdateMySQLProvider.ps1** スクリプトを使用します。 プロセスは、この記事のリソース プロバイダーのデプロイに関するセクションに記述されている、リソース プロバイダーをインストールするプロセスと類似しています。 スクリプトはリソース プロバイダーのダウンロードに含まれています。 

 > [!IMPORTANT]
 > リソース プロバイダーをアップグレードする前に、リリース ノートを確認し、新しい機能、修正、およびデプロイに影響を与える可能性のある既知の問題について確認してください。

## <a name="update-script-processes"></a>更新スクリプトのプロセス

**UpdateMySQLProvider.ps1** スクリプトは、最新のリソース プロバイダーのコードを使って新しい VM を作成し、古い VM から新しい VM に設定を移行します。 移行される設定には、データベースおよびホスティング サーバーの情報や、必要な DNS レコードが含まれます。 

>[!NOTE]
>Marketplace の管理から最新の Windows Server 2016 Core のイメージをダウンロードすることをお勧めします。 更新プログラムをインストールする必要がある場合は、ローカルの依存関係のパスに MSU パッケージを **1 つ**配置できます。 この場所に複数の MSU ファイルがある場合、スクリプトは失敗します。

スクリプトでは、DeployMySqlProvider.ps1 スクリプト用に記述されているものと同じ引数の使用が必要になります。 証明書も同様に指定します。  


## <a name="update-script-parameters"></a>更新スクリプトのパラメーター 
**UpdateMySQLProvider.ps1** PowerShell スクリプトを実行するときに、コマンド ラインから以下のパラメーターを指定できます。 必須パラメーターの指定がない場合、またはいずれかのパラメーターの検証が失敗した場合は、指定することを求められます。 

| パラメーター名 | 説明 | コメントまたは既定値 | 
| --- | --- | --- | 
| **CloudAdminCredential** | 特権エンドポイントへのアクセスに必要な、クラウド管理者の資格情報。 | _必須_ | 
| **AzCredential** | Azure Stack サービス管理者アカウントの資格情報。 Azure Stack のデプロイに使用したのと同じ資格情報を使用します。 | _必須_ | 
| **VMLocalCredential** |SQL リソース プロバイダー VM のローカル Administrator アカウントの資格情報。 | _必須_ | 
| **PrivilegedEndpoint** | 特権エンドポイントの IP アドレスまたは DNS 名。 |  _必須_ | 
| **AzureEnvironment** | Azure Stack のデプロイに使用したサービス管理者アカウントの Azure 環境。 Azure AD のデプロイでのみ必須です。 サポートされている環境名は **AzureCloud**、**AzureUSGovernment**、または中国の Azure AD を使用している場合は **AzureChinaCloud** です。 | AzureCloud |
| **DependencyFilesLocalPath** | 証明書 .pfx ファイルはこのディレクトリにも配置する必要があります。 | _省略可能_ (マルチノードでは _必須_) | 
| **DefaultSSLCertificatePassword** | .pfx 証明書のパスワード。 | _必須_ | 
| **MaxRetryCount** | 障害がある場合に各操作を再試行する回数。| 2 | 
| **RetryDuration** | 再試行間のタイムアウト間隔 (秒単位)。 | 120 | 
| **アンインストール** | リソース プロバイダーと関連付けられているすべてのリソースを削除します (以下のメモを参照してください)。 | いいえ  | 
| **DebugMode** | 障害発生時に自動クリーンアップが行われないようにします。 | いいえ  | 
| **AcceptLicense** | GPL ライセンスに同意するためのプロンプトをスキップします。  (https://www.gnu.org/licenses/old-licenses/gpl-2.0.html) | | 

## <a name="update-script-example"></a>更新スクリプトの例
管理者特権の PowerShell コンソールから実行できる *UpdateMySQLProvider.ps1* スクリプトの使用例を次に示します。 変数情報とパスワードは、必要に応じて変更してください。  

> [!NOTE] 
> 更新プロセスは、統合システムにのみに適用されます。 

```powershell 
# Install the AzureRM.Bootstrapper module, set the profile and install the AzureStack module
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2018-03-01-hybrid -Force
Install-Module -Name AzureStack -RequiredVersion 1.5.0

# Use the NetBIOS name for the Azure Stack domain. On the Azure Stack SDK, the default is AzureStack but could have been changed at install time. 
$domain = "AzureStack" 

# For integrated systems, use the IP address of one of the ERCS virtual machines 
$privilegedEndpoint = "AzS-ERCS01" 

# Provide the Azure environment used for deploying Azure Stack. Required only for Azure AD deployments. Supported environment names are AzureCloud, AzureUSGovernment, or AzureChinaCloud. 
$AzureEnvironment = "<EnvironmentName>"

# Point to the directory where the resource provider installation files were extracted. 
$tempDir = 'C:\TEMP\MYSQLRP' 

# The service admin account (can be Azure Active Directory or Active Directory Federation Services). 
$serviceAdmin = "admin@mydomain.onmicrosoft.com" 
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force 
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass) 
 
# Set credentials for the new resource provider VM. 
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force 
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("sqlrpadmin", $vmLocalAdminPass) 
 
# And the cloudadmin credential required for privileged endpoint access. 
$CloudAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force 
$CloudAdminCreds = New-Object System.Management.Automation.PSCredential ("$domain\cloudadmin", $CloudAdminPass) 

# Change the following as appropriate. 
$PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force 
 
# Change directory to the folder where you extracted the installation files. 
# Then adjust the endpoints. 
$tempDir\UpdateMySQLProvider.ps1 -AzCredential $AdminCreds ` 
-VMLocalCredential $vmLocalAdminCreds ` 
-CloudAdminCredential $cloudAdminCreds ` 
-PrivilegedEndpoint $privilegedEndpoint ` 
-AzureEnvironment $AzureEnvironment `
-DefaultSSLCertificatePassword $PfxPass ` 
-DependencyFilesLocalPath $tempDir\cert ` 
-AcceptLicense 
```  

## <a name="next-steps"></a>次の手順
[MySQL リソース プロバイダーの維持](azure-stack-mysql-resource-provider-maintain.md)
