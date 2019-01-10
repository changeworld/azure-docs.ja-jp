---
title: Azure Stack に安全に格納されたパスワードで VM をデプロイする | Microsoft Docs
description: Azure Stack Key Vault に格納されているパスワードを使って VM をデプロイする方法を説明します
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/28/2018
ms.author: mabrigg
ms.openlocfilehash: 8410c6fd2dd40656152209bc9951b6526c68dca6
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/10/2019
ms.locfileid: "54192569"
---
# <a name="create-a-virtual-machine-using-a-secure-password-stored-in-azure-stack-key-vault"></a>Azure Stack Key Vault に格納されているセキュリティ保護されたパスワードを使用して仮想マシンを作成する

*適用対象:Azure Stack 統合システムと Azure Stack Development Kit*

この記事では、Azure Stack Key Vault に格納されたパスワードを使用して、Windows Server 仮想マシンをデプロイする手順を説明します。 Key Vault パスワードを使用する方が、プレーンテキスト パスワードを渡すよりも安全性が高くなります。

## <a name="overview"></a>概要

Azure Stack Key Vault にはパスワードなどの値をシークレットとして格納できます。 シークレットの作成後、そのシークレットは Azure Resource Manager テンプレートで参照できます。 Resource Manager でシークレットを使用する利点は次のとおりです。

* リソースをデプロイするたびにシークレットを手動で入力する必要がありません。
* シークレットにアクセスできるユーザーまたはサービス プリンシパルを指定できます。

## <a name="prerequisites"></a>前提条件

* ユーザーは、Key Vault サービスを含むプランをサブスクライブする必要があります。
* [PowerShell for Azure Stack のインストール。](azure-stack-powershell-install.md)
* [PowerShell 環境の構成。](azure-stack-powershell-configure-user.md)

次の手順では、Key Vault に格納されているパスワードを取得することによって仮想マシンを作成するために必要なプロセスについて説明します。

1. Key Vault シークレットを作成します。
2. azuredeploy.parameters.json ファイルを更新します。
3. テンプレートをデプロイします。

> [!NOTE]  
> この手順は、Azure Stack Development Kit から、または VPN 経由で接続している場合は外部クライアントから実行できます。

## <a name="create-a-key-vault-secret"></a>Key Vault シークレットを作成する

次のスクリプトは、Key Vault を作成し、パスワードをシークレットとして Key Vault に格納します。 Key Vault を作成するときは、`-EnabledForDeployment` パラメーターを使います。 このパラメーターを指定すると、Azure Resource Manager テンプレートから Key Vault を参照できるようになります。

```PowerShell

$vaultName = "contosovault"
$resourceGroup = "contosovaultrg"
$location = "local"
$secretName = "MySecret"

New-AzureRmResourceGroup `
  -Name $resourceGroup `
  -Location $location

New-AzureRmKeyVault `
  -VaultName $vaultName `
  -ResourceGroupName $resourceGroup `
  -Location $location
  -EnabledForTemplateDeployment

$secretValue = ConvertTo-SecureString -String '<Password for your virtual machine>' -AsPlainText -Force

Set-AzureKeyVaultSecret `
  -VaultName $vaultName `
  -Name $secretName `
  -SecretValue $secretValue

```

前のスクリプトを実行すると、出力にはシークレットの URI が含まれます。 この URI を書き留めておきます。 それを、[Key Vault のパスワードでの Windows 仮想マシンのデプロイ テンプレート](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-vm-windows-create-passwordfromkv)で参照する必要があります。 開発用コンピューターに [101-vm-secure-password](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-vm-windows-create-passwordfromkv) フォルダーをダウンロードします。 このフォルダーには、次の手順で必要な `azuredeploy.json` ファイルと `azuredeploy.parameters.json` ファイルが含まれます。

環境の値に従って `azuredeploy.parameters.json` ファイルを変更します。 特に重要なパラメーターは、コンテナー名、コンテナー リソース グループ、およびシークレットの URI (前のスクリプトによって生成されたもの) です。 パラメーター ファイルの例を次に示します。

## <a name="update-the-azuredeployparametersjson-file"></a>azuredeploy.parameters.json ファイルを更新する

環境に従って仮想マシンの値の KeyVault URI、secretName、adminUsername で azuredeploy.parameters.json ファイルを更新します。 テンプレート パラメーター ファイルの JSON ファイルの例を次に示します。

```json
{
    "$schema":  "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion":  "1.0.0.0",
    "parameters":  {
       "adminUsername":  {
         "value":  "demouser"
          },
         "adminPassword":  {
           "reference":  {
              "keyVault":  {
                "id":  "/subscriptions/xxxxxx/resourceGroups/RgKvPwd/providers/Microsoft.KeyVault/vaults/KvPwd"
                },
              "secretName":  "MySecret"
           }
         },
       "dnsLabelPrefix":  {
          "value":  "mydns123456"
        },
        "windowsOSVersion":  {
          "value":  "2016-Datacenter"
        }
    }
}

```

## <a name="template-deployment"></a>テンプレートのデプロイ

次の PowerShell スクリプトを使用して、テンプレートをデプロイします。

```PowerShell  
New-AzureRmResourceGroupDeployment `
  -Name KVPwdDeployment `
  -ResourceGroupName $resourceGroup `
  -TemplateFile "<Fully qualified path to the azuredeploy.json file>" `
  -TemplateParameterFile "<Fully qualified path to the azuredeploy.parameters.json file>"
```

テンプレートが正常にデプロイされると、次の出力が表示されます。

![デプロイの出力](media/azure-stack-key-vault-deploy-vm-with-secret/deployment-output.png)

## <a name="next-steps"></a>次の手順

* [Key Vault を使ってサンプル アプリをデプロイする](azure-stack-key-vault-sample-app.md)
* [Key Vault 証明書を使って VM をデプロイする](azure-stack-key-vault-push-secret-into-vm.md)
