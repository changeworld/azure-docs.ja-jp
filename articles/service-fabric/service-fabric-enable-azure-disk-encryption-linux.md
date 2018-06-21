---
title: Service Fabric Linux クラスターのディスク暗号化の有効化 | Microsoft Docs
description: この記事では、Azure Resource Manager、Azure Key Vault を使用して、Azure で Service Fabric クラスター スケール セットのディスク暗号化を有効にする方法を説明します。
services: service-fabric
documentationcenter: .net
author: v-viban
manager: navya
ms.assetid: 15d0ab67-fc66-4108-8038-3584eeebabaa
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/24/2018
ms.author: v-viban
ms.openlocfilehash: 46f7f88768ab7ae9d84f392f340750865fef3b96
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/01/2018
ms.locfileid: "34660255"
---
# <a name="enable-disk-encryption-for-service-fabric-linux-cluster-nodes"></a>Service Fabric Linux クラスター ノードのディスク暗号化の有効化 
> [!div class="op_single_selector"]
> * [Linux のディスク暗号化](service-fabric-enable-azure-disk-encryption-linux.md)
> * [Windows のディスク暗号化](service-fabric-enable-azure-disk-encryption-windows.md)
>
>

以下の手順に従って、Service Fabric Linux クラスター ノードでディスク暗号化を有効にします。 ノード タイプ/仮想マシン スケール セットごとにこれらの手順を実行する必要があります。 ノードを暗号化するために、ここでは仮想マシン スケール セットで Azure Disk Encryption 機能を利用します。

このガイドでは次の手順について説明します。

* Service Fabric Linux クラスターの仮想マシン スケール セットでディスク暗号化を有効にする場合に、気を付ける必要がある主要な概念。
* Service Fabric Linux クラスターの仮想マシン スケール セットでディスク暗号化を有効にする前に、従う必要がある前提条件の手順。
* Service Fabric Linux クラスターの仮想マシン スケール セットでディスク暗号化を有効にするために、従う必要がある手順。


## <a name="prerequisites"></a>前提条件

1. **自己登録** - 使用するには、仮想マシン スケール セットのディスク暗号化プレビューで自己登録が必要です。
2. 次の手順を実行して、サブスクリプションを自己登録することができます。 
```Powershell
Register-AzureRmProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName "UnifiedDiskEncryption"
```
3. 状態が "登録済み" になるまで約 10 分待ちます。 次のコマンドを実行して、状態を確認することができます。 
```Powershell
Get-AzureRmProviderFeature -ProviderNamespace "Microsoft.Compute" -FeatureName "UnifiedDiskEncryption"
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Compute
```
4. **Azure Key Vault** - 仮想マシン スケール セットと同じサブスクリプションおよびリージョンで KeyVault を作成し、PS コマンドレットを使用して KeyVault に "EnabledForDiskEncryption" というアクセス ポリシーを設定します。 Azure portal で KeyVault UI を使用して、ポリシーを設定することもできます。 
```Powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName $keyVaultName -EnabledForDiskEncryption
```
5. 新しい暗号化コマンドを含む、最新の [Azure CLI 2.0](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest) をインストールします。
6. [Azure PowerShell の Azure SDK](https://github.com/Azure/azure-powershell/releases) リリースの最新バージョンをインストールします。 暗号化の有効化 ([Set](https://docs.microsoft.com/en-us/powershell/module/azurerm.compute/set-azurermvmssdiskencryptionextension?view=azurermps-4.4.1))、暗号化状態の取得 ([Get](https://docs.microsoft.com/en-us/powershell/module/azurerm.compute/get-azurermvmssvmdiskencryption?view=azurermps-4.4.1)) および暗号化の削除 ([disable](https://docs.microsoft.com/en-us/powershell/module/azurerm.compute/disable-azurermvmssdiskencryption?view=azurermps-4.4.1)) をスケール セット インスタンスで行うための VMSS ADE コマンドレットを以下に示します。 

| コマンド | バージョン |  ソース  |
| ------------- |-------------| ------------|
| Get-AzureRmVmssDiskEncryptionStatus   | 3.4.0 以上 | AzureRM.Compute |
| Get-AzureRmVmssVMDiskEncryptionStatus   | 3.4.0 以上 | AzureRM.Compute |
| Disable-AzureRmVmssDiskEncryption   | 3.4.0 以上 | AzureRM.Compute |
| Get-AzureRmVmssDiskEncryption   | 3.4.0 以上 | AzureRM.Compute |
| Get-AzureRmVmssVMDiskEncryption   | 3.4.0 以上 | AzureRM.Compute |
| Set-AzureRmVmssDiskEncryptionExtension   | 3.4.0 以上 | AzureRM.Compute |


## <a name="supported-scenarios-for-disk-encryption"></a>ディスク暗号化のサポートされるシナリオ
* 仮想マシン スケール セットの暗号化はマネージド ディスクで作成されたスケール セットの場合にのみサポートされます。ネイティブ (またはアンマネージド) ディスク スケール セットの場合はサポートされません。
* 仮想マシン スケール セットの暗号化は、Linux 仮想マシン スケール セットのデータ ボリュームの場合にサポートされます。 Linux 用の現在のプレビューでは、OS ディスクの暗号化はサポートされていません。
* 現在のプレビューでは、仮想マシン スケール セット VM の再イメージ操作とアップグレード操作はサポートされていません。


### <a name="create-new-linux-cluster-and-enable-disk-encryption"></a>新しい Linux クラスターを作成してディスク暗号化を有効にする

次のコマンドを使用してクラスターを作成し、Azure Resource Manager テンプレートと自己署名証明書を使って、ディスク暗号化を有効にします。

### <a name="log-in-to-azure"></a>Azure にログインする  

```Powershell

Login-AzureRmAccount
Set-AzureRmContext -SubscriptionId <guid>

```

```CLI

azure login
az account set --subscription $subscriptionId

```

#### <a name="use-the-custom-template-that-you-already-have"></a>既存のカスタム テンプレートを使用する 

ニーズに合わせてカスタム テンプレートを作成する必要がある場合は、Linux クラスター用の [Azure Service Fabric テンプレート サンプル](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master)で入手できるテンプレートのいずれかから始めることを強くお勧めします。 

カスタム テンプレートを既に持っている場合は、次のように、テンプレートとパラメーター ファイルの証明書に関連する 3 つすべてのパラメーターに名前が付けられ、値が null であることを慎重に確認してください。

```Json
   "certificateThumbprint": {
      "value": ""
    },
    "sourceVaultValue": {
      "value": ""
    },
    "certificateUrlValue": {
      "value": ""
    },
```

Linux 仮想マシン スケール セットの場合、データ ディスクの暗号化のみがサポートされるため、Azure Resource Manager テンプレートを使用してデータ ディスクを追加する必要があります。 以下のように、ご利用のテンプレートをデータ ディスクのプロビジョニング用に更新します。

```Json
   
   "storageProfile": { 
            "imageReference": { 
              "publisher": "[parameters('vmImagePublisher')]", 
              "offer": "[parameters('vmImageOffer')]", 
              "sku": "[parameters('vmImageSku')]", 
              "version": "[parameters('vmImageVersion')]" 
            }, 
            "osDisk": { 
              "caching": "ReadOnly", 
              "createOption": "FromImage", 
              "managedDisk": { 
                "storageAccountType": "[parameters('storageAccountType')]" 
              } 
           }, 
                "dataDisks": [ 
                { 
                    "diskSizeGB": 1023, 
                    "lun": 0, 
                    "createOption": "Empty" 
   
```
 

```Powershell


$resourceGroupLocation="westus"
$resourceGroupName="mycluster"
$CertSubjectName="mycluster.westus.cloudapp.azure.com"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$certOutputFolder="c:\certificates"

$parameterFilePath="c:\templates\templateparam.json"
$templateFilePath="c:\templates\template.json"


New-AzureRmServiceFabricCluster -ResourceGroupName $resourceGroupName -CertificateOutputFolder $certOutputFolder -CertificatePassword $certpassword -CertificateSubjectName $CertSubjectName -TemplateFile $templateFilePath -ParameterFile $parameterFilePath 

```

この処理と同等の処理を行う CLI コマンドを次に示します。 宣言ステートメントの値は適切な値に変更してください。 CLI は、上記の PowerShell コマンドがサポートする他のパラメーターをすべてサポートしています。

```CLI

declare certPassword=""
declare resourceGroupLocation="westus"
declare resourceGroupName="mylinux"
declare certSubjectName="mylinuxsecure.westus.cloudapp.azure.com"
declare parameterFilePath="c:\mytemplates\linuxtemplateparm.json"
declare templateFilePath="c:\mytemplates\linuxtemplate.json"
declare certOutputFolder="c:\certificates"


az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --certificate-output-folder $certOutputFolder --certificate-password $certPassword  \
    --certificate-subject-name $certSubjectName \
    --template-file $templateFilePath --parameter-file $parametersFilePath

```

#### <a name="linux-data-disk-mounting"></a>Linux データ ディスクのマウント
Linux 仮想マシン スケール セットでの暗号化に進む前に、追加されたデータ ディスクが正しくマウントされているかどうかを確認する必要があります。 Linux クラスター VM にログインして、LSBLK コマンドを実行します。 出力では、マウント ポイント列に追加されたデータ ディスクが示されます。


#### <a name="deploy-application-to-linux-service-fabric-cluster"></a>Service Fabric クラスターにアプリケーションをデプロイする
[クラスターにアプリケーションをデプロイする](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-quickstart-containers-linux)ための手順とガイダンスに従ってください。


#### <a name="enable-disk-encryption-for-service-fabric-linux-cluster-virtual-machine-scale-set-created-above"></a>先ほど作成した Service Fabric Linux クラスターの仮想マシン スケール セットでディスク暗号化を有効にする
 
```Powershell
$VmssName = "nt1vm"
$vaultName = "mykeyvault"
$resourceGroupName = "mycluster"
$KeyVault = Get-AzureRmKeyVault -VaultName $vaultName -ResourceGroupName $rgName
$DiskEncryptionKeyVaultUrl = $KeyVault.VaultUri
$KeyVaultResourceId = $KeyVault.ResourceId

Set-AzureRmVmssDiskEncryptionExtension -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName -DiskEncryptionKeyVaultUrl $DiskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType All

```

```CLI

az vmss encryption enable -g <resourceGroupName> -n <VMSS name> --disk-encryption-keyvault <KeyVaultResourceId>

```

#### <a name="validate-if-disk-encryption-enabled-for-linux-virtual-machine-scale-set"></a>Linux 仮想マシン スケール セットでディスク暗号化が有効になっていることを確認します。
仮想マシン スケール セット全体またはスケール セットの任意のインスタンス VM の状態を取得します。 以下のコマンドを参照してください。
また、ユーザーは Linux クラスター VM にログインして、LSBLK コマンドを実行することができます。 出力では、マウント ポイント列に追加されたデータ ディスクと、追加されたデータ ディスクの Crypt として Type 列が示されます。

```Powershell

$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Get-AzureRmVmssDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName

Get-AzureRmVmssVMDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName -InstanceId "0"

```

```CLI

az vmss encryption show -g <resourceGroupName> -n <VMSS name>

```



#### <a name="disable-disk-encryption-for-service-fabric-cluster-virtual-machine-scale-set"></a>Service Fabric クラスターの仮想マシン スケール セットでディスク暗号化を無効にする 
ディスク暗号化の無効化は、インスタンス単位ではなく、仮想マシン スケール セット全体に適用されます。 

```Powershell

$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Disable-AzureRmVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $VmssName

```

```CLI

az vmss encryption disable -g <resourceGroupName> -n <VMSS name>

```


## <a name="next-steps"></a>次の手順
この時点で、Linux Service Fabric クラスターの仮想マシン スケール セットでディスク暗号化を有効/無効にする方法により、クラスターはセキュリティ保護されています。 次は、[Windows のディスク暗号化](service-fabric-enable-azure-disk-encryption-windows.md)です。 

