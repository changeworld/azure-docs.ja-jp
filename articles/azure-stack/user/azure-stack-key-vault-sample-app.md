---
title: アプリケーションに Azure Stack Key Vault のシークレットの取得を許可する | Microsoft Docs
description: サンプル アプリを使用して Azure Stack Key Vault を操作する
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 3748b719-e269-4b48-8d7d-d75a84b0e1e5
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/27/2018
ms.author: sethm
ms.openlocfilehash: 09a31103b2a29bc335653dd92e6b11a5f8294eda
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/10/2019
ms.locfileid: "54192572"
---
# <a name="a-sample-application-that-uses-keys-and-secrets-stored-in-a-key-vault"></a>Key Vault に格納されているキーとシークレットを使用するサンプル アプリケーション

*適用対象:Azure Stack 統合システムと Azure Stack Development Kit*

この記事の手順に従って、Azure Stack のキー コンテナーからキーとシークレットを取得する、**HelloKeyVault** という名前のサンプル アプリケーションを実行します。

## <a name="prerequisites"></a>前提条件

Azure Stack [開発キット](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop)から、または [VPN 経由で接続](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn)している場合は Windows ベースの外部クライアントから、次の前提条件をインストールできます。

* [Azure Stack と互換性のある Azure PowerShell モジュール](azure-stack-powershell-install.md)をインストールします。
* [Azure Stack を操作するために必要なツール](azure-stack-powershell-download.md)をダウンロードします。

## <a name="create-and-get-the-key-vault-and-application-settings"></a>キー コンテナーの作成と取得およびアプリケーションの設定

サンプル アプリケーションを準備するには:

* Azure Stack でキー コンテナーを作成します。
* Azure Active Directory (Azure AD) でアプリケーションを登録します。

Azure portal または PowerShell を使用して、サンプル アプリケーションを準備できます。 この記事では、PowerShell を使用してキー コンテナーを作成し、アプリケーションを登録する方法について示します。

>[!NOTE]
>既定では、この PowerShell スクリプトによって、Active Directory に新しいアプリケーションが作成されます。 ただし、既存のアプリケーションのいずれかを登録できます。

次のスクリプトを実行する前に、必ず `aadTenantName` 変数と `applicationPassword` 変数の値を指定します。 `applicationPassword` の値を指定しないと、このスクリプトによりランダムなパスワードが生成されます。

```powershell
$vaultName           = 'myVault'
$resourceGroupName   = 'myResourceGroup'
$applicationName     = 'myApp'
$location            = 'local'

# Password for the application. If not specified, this script will generate a random password during app creation.
$applicationPassword = ''

# Function to generate a random password for the application.
Function GenerateSymmetricKey()
{
    $key = New-Object byte[](32)
    $rng = [System.Security.Cryptography.RNGCryptoServiceProvider]::Create()
    $rng.GetBytes($key)
    return [System.Convert]::ToBase64String($key)
}

Write-Host 'Please log into your Azure Stack user environment' -foregroundcolor Green

$tenantARM = "https://management.local.azurestack.external"
$aadTenantName = "FILL THIS IN WITH YOUR AAD TENANT NAME. FOR EXAMPLE: myazurestack.onmicrosoft.com"

# Configure the Azure Stack operator’s PowerShell environment.
Add-AzureRMEnvironment `
  -Name "AzureStackUser" `
  -ArmEndpoint $tenantARM

Set-AzureRmEnvironment `
  -Name "AzureStackAdmin" `
  -GraphAudience "https://graph.windows.net/"

$TenantID = Get-AzsDirectoryTenantId `
  -AADTenantName $aadTenantName `
  -EnvironmentName AzureStackUser

# Sign in to the user portal.
Add-AzureRmAccount `
  -EnvironmentName "AzureStackUser" `
  -TenantId $TenantID `

$now = [System.DateTime]::Now
$oneYearFromNow = $now.AddYears(1)

$applicationPassword = GenerateSymmetricKey

# Create a new Azure AD application.
$identifierUri = [string]::Format("http://localhost:8080/{0}",[Guid]::NewGuid().ToString("N"))
$homePage = "http://contoso.com"

Write-Host "Creating a new AAD Application"
$ADApp = New-AzureRmADApplication `
  -DisplayName $applicationName `
  -HomePage $homePage `
  -IdentifierUris $identifierUri `
  -StartDate $now `
  -EndDate $oneYearFromNow `
  -Password $applicationPassword

Write-Host "Creating a new AAD service principal"
$servicePrincipal = New-AzureRmADServicePrincipal `
  -ApplicationId $ADApp.ApplicationId

# Create a new resource group and a key vault in that resource group.
New-AzureRmResourceGroup `
  -Name $resourceGroupName `
  -Location $location

Write-Host "Creating vault $vaultName"
$vault = New-AzureRmKeyVault -VaultName $vaultName `
  -ResourceGroupName $resourceGroupName `
  -Sku standard `
  -Location $location

# Specify full privileges to the vault for the application.
Write-Host "Setting access policy"
Set-AzureRmKeyVaultAccessPolicy -VaultName $vaultName `
  -ObjectId $servicePrincipal.Id `
  -PermissionsToKeys all `
  -PermissionsToSecrets all

Write-Host "Paste the following settings into the app.config file for the HelloKeyVault project:"
'<add key="VaultUrl" value="' + $vault.VaultUri + '"/>'
'<add key="AuthClientId" value="' + $servicePrincipal.ApplicationId + '"/>'
'<add key="AuthClientSecret" value="' + $applicationPassword + '"/>'
Write-Host
```

次の画像は、キー コンテナーの作成に使用されたスクリプトからの出力を示しています。

![キー コンテナーとアクセス キー](media/azure-stack-key-vault-sample-app/settingsoutput.png)

上のスクリプトによって返される **VaultUrl**、**AuthClientId**、**AuthClientSecret** の値をメモしておきます。 これらの値は、HelloKeyVault アプリケーションを実行するために使用します。

## <a name="download-and-configure-the-sample-application"></a>サンプル アプリケーションのダウンロードと構成

「[Azure Key Vault client samples (Azure Key Vault クライアントのサンプル)](https://www.microsoft.com/download/details.aspx?id=45343)」ページから、キー コンテナーのサンプルをダウンロードします。 .zip ファイルの内容を自分の開発ワークステーションに抽出します。 samples フォルダーには 2 つのアプリケーションがあります。 この記事では、**HelloKeyVault** を使用します。

**HelloKeyVault** サンプルを読み込むには:

* **Microsoft.Azure.KeyVault.Samples** > **samples** > **HelloKeyVault** フォルダーを参照します。
* **HelloKeyVault** アプリケーションを Visual Studio で開きます。

### <a name="configure-the-sample-application"></a>サンプル アプリケーションを構成する

Visual Studio で次の操作を行います。

* HelloKeyVault\App.config ファイルを開き、&lt;**appSettings**&gt; 要素を見つけます。
* キー コンテナーの作成に使用され、返された値を使用して、**VaultUrl**、**AuthClientId**、**AuthClientSecret** を更新します。 既定では、App.config ファイルには `AuthCertThumbprint` のプレースホルダーが含まれます。 このプレースホルダーを `AuthClientSecret` に置き換えます。

  ![アプリケーション設定](media/azure-stack-key-vault-sample-app/appconfig.png)

* ソリューションをリビルドします。

## <a name="run-the-application"></a>アプリケーションの実行

HelloKeyVault を実行するとき、アプリケーションは Azure AD にサインインし、AuthClientSecret トークンを使用して、Azure Stack のキー コンテナーに対する認証を行います。

HelloKeyVault サンプルを使用して、以下の操作を行います。

* キーおよびシークレットで作成、暗号化、ラップ、削除などの基本操作を行います。
* `encrypt`、`decrypt` などのパラメーターを HelloKeyVault に渡して、指定した変更をキー コンテナーに適用します。

## <a name="next-steps"></a>次の手順

- [Key Vault パスワードを使用して VM をデプロイする](azure-stack-key-vault-deploy-vm-with-secret.md)
- [Key Vault 証明書を使って VM をデプロイする](azure-stack-key-vault-push-secret-into-vm.md)
