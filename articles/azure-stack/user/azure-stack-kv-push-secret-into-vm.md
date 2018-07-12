---
title: Azure Stack に安全に格納された証明書で仮想マシンをデプロイする | Microsoft Docs
description: Azure Stack の Key Vault を使って、仮想マシンをデプロイして証明書を仮想マシンにプッシュする方法について説明します。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 46590eb1-1746-4ecf-a9e5-41609fde8e89
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/28/2018
ms.author: mabrigg
ms.openlocfilehash: 05278ee4b0dc1f2c22f40bfcff4f9d7342017c0f
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/29/2018
ms.locfileid: "37108758"
---
# <a name="create-a-virtual-machine-and-install-a-certificate-retrieved-from-an-azure-stack-key-vault"></a>仮想マシンを作成して、Azure Stack Key Vault から取得した証明書をインストールする

*適用先: Azure Stack 統合システムと Azure Stack 開発キット*

Key Vault 証明書がインストールされた Azure Stack 仮想マシン (VM) を作成する方法について説明します。

## <a name="overview"></a>概要

Active Directory への認証、Web トラフィックの暗号化など、多くのシナリオで証明書が使用されます。 証明書はシークレットとして Azure Stack Key Vault に安全に格納できます。 Azure Stack Key Vault を使用する利点は次のとおりです。

* 証明書がスクリプト、コマンド ラインの履歴、またはテンプレートに公開されません。
* 証明書管理プロセスが合理化されます。
* 証明書にアクセスするキーを制御できます。

### <a name="process-description"></a>プロセスの説明

次の手順では、仮想マシンに証明書をプッシュするために必要なプロセスについて説明します。

1. Key Vault シークレットを作成します。
2. azuredeploy.parameters.json ファイルを更新します。
3. テンプレートのデプロイ

> [!NOTE]
> この手順は、Azure Stack Development Kit から、または VPN 経由で接続している場合は外部クライアントから実行できます。

## <a name="prerequisites"></a>前提条件

* ユーザーは、Key Vault サービスを含むプランをサブスクライブする必要があります。
* [PowerShell for Azure Stack のインストール。](azure-stack-powershell-install.md)
* [Azure Stack ユーザーの PowerShell 環境の構成](azure-stack-powershell-configure-user.md)

## <a name="create-a-key-vault-secret"></a>Key Vault シークレットを作成する

次のスクリプトは、.pfx 形式で証明書を作成し、Key Vault を作成して、Key Vault にシークレットとして証明書を格納します。

> [!IMPORTANT]
> キー コンテナーを作成するときは、`-EnabledForDeployment` パラメーターを使う必要があります。 このパラメーターにより、Azure Resource Manager テンプレートから Key Vault を確実に参照できます。

```powershell

# Create a certificate in the .pfx format
New-SelfSignedCertificate `
  -certstorelocation cert:\LocalMachine\My `
  -dnsname contoso.microsoft.com

$pwd = ConvertTo-SecureString `
  -String "<Password used to export the certificate>" `
  -Force `
  -AsPlainText

Export-PfxCertificate `
  -cert "cert:\localMachine\my\<Certificate Thumbprint that was created in the previous step>" `
  -FilePath "<Fully qualified path where the exported certificate can be stored>" `
  -Password $pwd

# Create a key vault and upload the certificate into the key vault as a secret
$vaultName = "contosovault"
$resourceGroup = "contosovaultrg"
$location = "local"
$secretName = "servicecert"
$fileName = "<Fully qualified path where the exported certificate can be stored>"
$certPassword = "<Password used to export the certificate>"

$fileContentBytes = get-content $fileName `
  -Encoding Byte

$fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
$jsonObject = @"
{
"data": "$filecontentencoded",
"dataType" :"pfx",
"password": "$certPassword"
}
"@
$jsonObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
$jsonEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)

New-AzureRmResourceGroup `
  -Name $resourceGroup `
  -Location $location

New-AzureRmKeyVault `
  -VaultName $vaultName `
  -ResourceGroupName $resourceGroup `
  -Location $location `
  -sku standard `
  -EnabledForDeployment

$secret = ConvertTo-SecureString `
  -String $jsonEncoded `
  -AsPlainText -Force

Set-AzureKeyVaultSecret `
  -VaultName $vaultName `
  -Name $secretName `
   -SecretValue $secret

```

前のスクリプトを実行すると、出力にはシークレットの URI が含まれます。 この URI を書き留めておきます。 [Windows Resource Manager に証明書をプッシュするテンプレート](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/201-vm-windows-pushcertificate)でそれを参照する必要があります。 開発用コンピューターに [vm-push-certificate-windows template](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/201-vm-windows-pushcertificate) フォルダーをダウンロードします。 このフォルダーには、次の手順で必要な `azuredeploy.json` ファイルと `azuredeploy.parameters.json` ファイルが含まれます。

環境の値に従って `azuredeploy.parameters.json` ファイルを変更します。 特に重要なパラメーターは、コンテナー名、コンテナー リソース グループ、およびシークレットの URI (前のスクリプトによって生成されたもの) です。 パラメーター ファイルの例を次に示します。

## <a name="update-the-azuredeployparametersjson-file"></a>azuredeploy.parameters.json ファイルを更新する

環境に従って、vaultName、シークレットの URI、VmName、他の値で azuredeploy.parameters.json ファイルを更新します。 テンプレート パラメーター ファイルの JSON ファイルの例を次に示します。

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "newStorageAccountName": {
      "value": "kvstorage01"
    },
    "vmName": {
      "value": "VM1"
    },
    "vmSize": {
      "value": "Standard_D1_v2"
    },
    "adminUserName": {
      "value": "demouser"
    },
    "adminPassword": {
      "value": "demouser@123"
    },
    "vaultName": {
      "value": "contosovault"
    },
    "vaultResourceGroup": {
      "value": "contosovaultrg"
    },
    "secretUrlWithVersion": {
      "value": "https://testkv001.vault.local.azurestack.external/secrets/testcert002/82afeeb84f4442329ce06593502e7840"
    }
  }
}
```

## <a name="deploy-the-template"></a>テンプレートのデプロイ

次の PowerShell スクリプトを使用して、テンプレートをデプロイします。

```powershell
# Deploy a Resource Manager template to create a VM and push the secret onto it
New-AzureRmResourceGroupDeployment `
  -Name KVDeployment `
  -ResourceGroupName $resourceGroup `
  -TemplateFile "<Fully qualified path to the azuredeploy.json file>" `
  -TemplateParameterFile "<Fully qualified path to the azuredeploy.parameters.json file>"
```

テンプレートが正常にデプロイされると、次の出力が表示されます。

![Template deployment の結果](media/azure-stack-kv-push-secret-into-vm/deployment-output.png)

証明書は、デプロイ中に Azure Stack によって仮想マシンにプッシュされします。 証明書の場所は、VM のオペレーティング システムによって異なります。

* Windows では、証明書はユーザー指定の証明書ストアで LocalMachine の証明書の場所に追加されます。
* Linux では、証明書は、X509 証明書ファイルの場合は &lt;UppercaseThumbprint&gt;.crt、秘密キーの場合は &lt;UppercaseThumbprint&gt;.prv というファイル名で、/var/lib/waagent ディレクトリに配置されます。

## <a name="retire-certificates"></a>証明書の使用を終了する

証明書の使用は、証明書管理プロセスの一環として終了します。 以前のバージョンの証明書は削除できませんが、`Set-AzureKeyVaultSecretAttribute` コマンドレットを使って無効にすることはできます。

次の例は、証明書を無効にする方法を示しています。 **VaultName**、**Name**、**Version** の各パラメーターにはご自身の値を使用してください。

```powershell
Set-AzureKeyVaultSecretAttribute -VaultName contosovault -Name servicecert -Version e3391a126b65414f93f6f9806743a1f7 -Enable 0
```

## <a name="next-steps"></a>次の手順

* [Key Vault パスワードを使用して VM をデプロイする](azure-stack-kv-deploy-vm-with-secret.md)
* [アプリケーションが Key Vault にアクセスできるようにする](azure-stack-kv-sample-app.md)
