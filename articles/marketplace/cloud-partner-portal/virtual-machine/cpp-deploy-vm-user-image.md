---
title: ユーザー VHD から Azure VM をデプロイする | Azure Marketplace
description: ユーザー VHD イメージをデプロイして Azure VM インスタンスを作成する方法について説明します。
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/29/2018
ms.author: dsindona
ms.openlocfilehash: 79754b4ce7c3dfe2a5c549f4a39ef3160be423d8
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273887"
---
# <a name="deploy-an-azure-vm-from-a-user-vhd"></a>ユーザー VHD から Azure VM をデプロイする

> [!IMPORTANT]
> 2020 年 4 月 13 日以降、Azure 仮想マシン オファーの管理のパートナー センターへの移行が開始されます。 移行後は、パートナー センターにてオファーを作成・管理することになります。 移行されたオファーを管理するには、「[Azure VM イメージ証明](https://aks.ms/CertifyVMimage)」の手順に従ってください。

この記事では、提供された Azure Resource Manager テンプレートおよび Azure PowerShell スクリプトを使用することにより、汎用化された VHD イメージをデプロイして新しい Azure VM リソースを作成する方法について説明します。

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="vhd-deployment-template"></a>VHD デプロイ テンプレート

[VHD 展開](cpp-deploy-json-template.md)用の Azure Resource Manager テンプレートを、`VHDtoImage.json` という名前のローカル ファイルにコピーします。  このファイルを編集して、次のパラメーターに値を指定します。 

|  **パラメーター**             |   **説明**                                                              |
|  -------------             |   ---------------                                                              |
| ResourceGroupName          | 既存の Azure リソース グループ名。  通常は、ご利用のキー コンテナーに関連付けられているのと同じ RG を使用します。  |
| TemplateFile               | ファイル `VHDtoImage.json` の完全なパス名                                    |
| userStorageAccountName     | ストレージ アカウントの名前                                                    |
| sNameForPublicIP           | パブリック IP の DNS 名。 小文字にする必要があります。                                  |
| subscriptionId             | Azure サブスクリプションの識別子。                                                  |
| 場所                   | リソース グループの標準的な Azure 地理的場所                       |
| vmName                     | 仮想マシンの名前                                                    |
| vaultName                  | Key Vault の名前                                                          |
| vaultResourceGroup         | Key Vault のリソース グループ
| certificateUrl             | キー コンテナーに格納されているバージョンを含む証明書の URL (例: `https://testault.vault.azure.net/secrets/testcert/b621es1db241e56a72d037479xab1r7`) |
| vhdUrl                     | 仮想ハード ディスクの URL                                                   |
| vmSize                     | 仮想マシン インスタンスのサイズ                                           |
| publicIPAddressName        | パブリック IP アドレスの名前                                                  |
| virtualNetworkName         | 仮想ネットワーク名                                                    |
| nicName                    | 仮想ネットワーク用のネットワーク インターフェイス カードの名前                     |
| adminUserName              | 管理者アカウントのユーザー名                                          |
| adminPassword              | 管理者パスワード                                                          |
|  |  |


## <a name="powershell-script"></a>PowerShell スクリプト

次のスクリプトをコピーし、編集することで、`$storageaccount` 変数と `$vhdUrl` 変数に値を指定します。  それを実行して、汎用化された既存の VHD から Azure VM リソースを作成します。

```powershell
# storage account of existing generalized VHD 
$storageaccount = "testwinrm11815"

# generalized VHD URL
$vhdUrl = "https://testwinrm11815.blob.core.windows.net/vhds/testvm1234562016651857.vhd"

echo "New-AzResourceGroupDeployment -Name "dplisvvm$postfix" -ResourceGroupName "$rgName" -TemplateFile "C:\certLocation\VHDtoImage.json" -userStorageAccountName "$storageaccount" -dnsNameForPublicIP "$vmName" -subscriptionId "$mysubid" -location "$location" -vmName "$vmName" -vaultName "$kvname" -vaultResourceGroup "$rgName" -certificateUrl $objAzureKeyVaultSecret.Id  -vhdUrl "$vhdUrl" -vmSize "Standard_A2" -publicIPAddressName "myPublicIP1" -virtualNetworkName "myVNET1" -nicName "myNIC1" -adminUserName "isv" -adminPassword $pwd"

#deploying VM with existing VHD
New-AzResourceGroupDeployment -Name "dplisvvm$postfix" -ResourceGroupName "$rgName" -TemplateFile "C:\certLocation\VHDtoImage.json" -userStorageAccountName "$storageaccount" -dnsNameForPublicIP "$vmName" -subscriptionId "$mysubid" -location "$location" -vmName "$vmName" -vaultName "$kvname" -vaultResourceGroup "$rgName" -certificateUrl $objAzureKeyVaultSecret.Id  -vhdUrl "$vhdUrl" -vmSize "Standard_A2" -publicIPAddressName "myPublicIP1" -virtualNetworkName "myVNET1" -nicName "myNIC1" -adminUserName "isv" -adminPassword $pwd 

```

## <a name="next-steps"></a>次のステップ

使用する VM がデプロイされたら、[VM イメージを認定](./cpp-certify-vm.md)する準備が整います。
