---
title: Service Fabric マネージド クラスター ノードのディスク暗号化を有効にする
description: ARM テンプレートを使用して、Windows の Azure Service Fabric 管理対象クラスター ノードのディスク暗号化を有効にする方法について説明します。
ms.topic: how-to
ms.date: 11/8/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: d1d1db17d6914904d9995883903c50982de84daa
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2021
ms.locfileid: "132486946"
---
# <a name="enable-disk-encryption-for-service-fabric-managed-cluster-nodes"></a>Service Fabric 管理対象クラスター ノードのディスク暗号化を有効にする

Service Fabric マネージド クラスターは、データ保護を促進する 2 つのディスク暗号化オプションをサポートすることで、セキュリティとコンプライアンスに対する組織のコミットメントに対応します。 推奨されるオプションはホストでの暗号化ですが、Azure Disk Encryption もサポートされています。 [ディスク暗号化オプション](../virtual-machines/disk-encryption-overview.md)を再確認して、実際のニーズを満たすオプションを選ぶようにしてください。


## <a name="enable-encryption-at-host-preview"></a>ホストでの暗号化を有効にする (プレビュー)

この暗号化方法は、データを暗号化して Azure ストレージ サービスに格納することにより、VM に関してすべての OS タイプとイメージ (カスタム イメージを含む) に対応することで、[Azure Disk Encryption](how-to-managed-cluster-enable-disk-encryption.md) を強化したものです。 この方法では VM の CPU は使用されず、また VM のパフォーマンスにも影響が生じないため、ワークロードは、VM の SKU で提供されるリソースをすべて使用することができます。

> [!Note]
> 既存のノード タイプで有効にすることはできません。 新しいノード タイプをプロビジョニングしてワークロードを移行する必要があります。

> [!Note]
> 現時点では、ホストでの暗号化を使用しているとき、Azure Security Center のディスク暗号化ステータスが "異常" と表示されます。

ホストでの暗号化を有効にして新しい Service Fabric マネージド クラスターをデプロイするには、以下の手順に従います。また、こちらの[サンプル テンプレート](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/SF-Managed-Standard-SKU-2-NT-HostEncryption)も参照してください。

1. 次の[制限事項](../virtual-machines/windows/disks-enable-host-based-encryption-powershell.md#restrictions)を参照し、自分の要件を満たしていることを確認します。

2. クラスターのデプロイ前に、必要な[前提条件](../virtual-machines/windows/disks-enable-host-based-encryption-powershell.md#prerequisites)をセットアップします。

3. ディスク暗号化を必要とする各ノード タイプについて、マネージド クラスター テンプレートで `enableEncryptionAtHost` プロパティを構成します。 サンプルはあらかじめ構成済みです。

   * Service Fabric マネージド クラスター リソースの apiVersion は **2021-11-01-preview** 以降である必要があります。

   ```json
        {
               "apiVersion": "[variables('sfApiVersion')]",
               "type": "Microsoft.ServiceFabric/managedclusters/nodetypes",
               "name": "[concat(parameters('clusterName'), '/', parameters('nodeTypeName'))]",
               "location": "[resourcegroup().location]",
               "properties": {
                   "enableEncryptionAtHost": true
                   ...
               }
       }
   ```

4. デプロイして検証します。

   ホストでの暗号化を有効にして構成したマネージド クラスターをデプロイします。

   ```powershell
   $clusterName = "<clustername>" 
   $resourceGroupName = "<rg-name>"

   New-AzResourceGroupDeployment -Name $resourceGroupName -ResourceGroupName $resourceGroupName -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json -Debug -Verbose 
   ```

   `Get-AzVmss` コマンドを使用して、ノードの種類の基になるスケール セットでディスク暗号化の状態を確認できます。 まず、マネージド クラスターのサポート リソースグループ (基になる仮想ネットワーク、ロード バランサー、パブリック IP、NSG、スケール セット、ストレージ アカウントを含む) の名前を確認する必要があります。 `NodeTypeNAme` は必ず、確認するクラスター ノードの種類の名前 (デプロイ テンプレートで指定) に変更してください。

   ```powershell
   $NodeTypeName = "NT2"
   $clustername = <clustername>
   $resourceGroupName = "<rg-name>"
   $supportResourceGroupName = "SFC_" + (Get-AzServiceFabricManagedCluster -ResourceGroupName $resourceGroupName -Name $clustername).ClusterId
   $VMSS = Get-AzVmss -ResourceGroupName $supportResourceGroupName -Name $NodeTypeName
   $VMSS.VirtualMachineProfile.SecurityProfile.EncryptionAtHost
   ```

   返される出力はこのようになります。

   ```console
   $VMSS.VirtualMachineProfile.SecurityProfile.EncryptionAtHost
   True
   ```

## <a name="enable-azure-disk-encryption"></a>Azure Disk Encryption を有効にする
Azure Disk Encryption は、Linux の DM-Crypt 機能または Windows の BitLocker 機能を使用して、Azure 仮想マシン (VM) の OS とデータ ディスクのボリュームを暗号化するものです。 ADE は、ディスクの暗号化キーとシークレットを制御および管理できるように、Azure Key Vault と統合されています。

このガイドでは、Azure Resource Manager (ARM) テンプレートを使用し、[仮想マシン スケール セット](../virtual-machine-scale-sets/disk-encryption-azure-resource-manager.md)用の [Azure Disk Encryption](../virtual-machines/windows/disk-encryption-overview.md) 機能を使用して、Windows の Service Fabric 管理対象クラスター ノードでディスク暗号化を有効にする方法について説明します。

1. Azure Disk Encryption での登録

   仮想マシン スケール セットのディスク暗号化プレビューには、自己登録が必要です。 次のコマンドを実行します。

   ```powershell
   Register-AzProviderFeature -FeatureName "UnifiedDiskEncryption" -ProviderNamespace "Microsoft.Compute"
   ```

   以下を実行して、登録の状態を確認します。

   ```powershell
   Get-AzProviderFeature -ProviderNamespace "Microsoft.Compute" -FeatureName "UnifiedDiskEncryption"
   ```

   状態が *Registered* に変われば、先に進むことができます。

2. ディスク暗号化のために Key Vault をプロビジョニングする

   Azure Disk Encryption では、ディスク暗号化キーとシークレットを制御および管理するために、Azure Key Vault が必要です。 Key Vault と Service Fabric 管理対象クラスターは、同じ Azure リージョンとサブスクリプションに存在している必要があります。 これらの要件が満たされていれば、新規または既存の Key Vault をディスク暗号化用に有効にして使用することができます。

3. ディスク暗号化を有効にして Key Vault を作成する

   ディスク暗号化用の新しい Key Vault を作成するには、次のコマンドを実行します。 ご利用の Key Vault がクラスターと同じリージョンに存在することを確認してください。

   # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

   ```powershell
   $resourceGroupName = "<rg-name>" 
   $keyvaultName = "<kv-name>" 

   New-AzResourceGroup -Name $resourceGroupName -Location eastus2 
   New-AzKeyVault -ResourceGroupName $resourceGroupName -Name $keyvaultName -Location eastus2 -EnabledForDiskEncryption
   ```

   # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

   ```azurecli
   $resourceGroupName = "<rg-name>" 
   $keyvaultName = "<kv-name>" 

   az keyvault create --resource-group $resourceGroupName --name $keyvaultName --enabled-for-disk-encryption
   ```

   ---

4. 既存の Key Vault を更新してディスク暗号化を有効にする

   既存の Key Vault でディスク暗号化を有効にするには、次のコマンドを実行します。

   # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

   ```powershell
   Set-AzKeyVaultAccessPolicy -ResourceGroupName $resourceGroupName -VaultName $keyvaultName -EnabledForDiskEncryption
   ```

   # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

   ```azurecli
   az keyvault update --name keyvaultName --enabled-for-disk-encryption 
   ```

   ---

### <a name="update-the-template-and-parameters-files-for-disk-encryption"></a>ディスク暗号化のテンプレートとパラメーター ファイルを更新する

次の手順では、[既存の管理対象クラスター](tutorial-managed-cluster-deploy.md)でディスク暗号化を有効にするために必要なテンプレートの変更について説明します。 または、テンプレート https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/SF-Managed-Standard-SKU-1-NT-DiskEncryption を使用して、ディスク暗号化を有効にした新しい Service Fabric 管理対象クラスターをデプロイすることもできます。

1. `keyVaultResourceId` の下にはご自身のサブスクリプション、リソース グループ名、コンテナー名を代入して、次のパラメーターをテンプレートに追加します。

   ```json
   "parameters": {
    "keyVaultResourceId": { 
      "type": "string", 
      "defaultValue": "/subscriptions/########-####-####-####-############/resourceGroups/<rg-name>/providers/Microsoft.KeyVault/vaults/<kv-name>", 
      "metadata": { 
      "description": "Full resource id of the Key Vault used for disk encryption." 
   } 
    },
    "volumeType": { 
     "type": "string", 
     "defaultValue": "All", 
     "metadata": { 
      "description": "Type of the volume OS or Data to perform encryption operation" 
   }
   }
   }, 
   ```

2. 次に、テンプレート内の管理対象クラスター ノードの種類に `AzureDiskEncryption` VM 拡張機能を追加します。

   ```json
   "properties": { 
   "vmExtensions": [ 
   { 
   "name": "AzureDiskEncryption", 
   "properties": { 
     "publisher": "Microsoft.Azure.Security", 
     "type": "AzureDiskEncryption", 
     "typeHandlerVersion": "2.2", 
     "autoUpgradeMinorVersion": true, 
     "settings": {      
           "EncryptionOperation": "EnableEncryption", 
           "KeyVaultURL": "[reference(parameters('keyVaultResourceId'),'2016-10-01').vaultUri]", 
        "KeyVaultResourceId": "[parameters('keyVaultResourceID')]",
        "VolumeType": "[parameters('volumeType')]" 
        } 
      } 
   } 
   ] 
   } 
   ```

3. 最後に、*keyVaultResourceId* にご自身のサブスクリプション、リソース グループ、キー コンテナー名を代入して、パラメーター ファイルを更新します。

   ```json
   "parameters": { 
   ...
    "keyVaultResourceId": { 
     "value": "/subscriptions/########-####-####-####-############/resourceGroups/<rg-name>/providers/Microsoft.KeyVault/vaults/<kv-name>" 
    },   
    "volumeType": { 
     "value": "All" 
    }    
   } 
   ```

4. 変更をデプロイして確認する

   準備ができたら、変更をデプロイして管理対象クラスターでディスク暗号化を有効にします。

   ```powershell
   $clusterName = "<clustername>" 

   New-AzResourceGroupDeployment -Name $resourceGroupName -ResourceGroupName $resourceGroupName .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json -Debug -Verbose 
   ```

   `Get-AzVmssDiskEncryption` コマンドを使用して、ノードの種類の基になるスケール セットでディスク暗号化の状態を確認できます。 まず、管理対象クラスターのサポート リソースグループ (基になる仮想ネットワーク、ロード バランサー、パブリック IP、NSG、スケール セット、ストレージ アカウントを含む) の名前を確認する必要があります。 `VmssName` は必ず、確認するクラスター ノードの種類の名前 (デプロイ テンプレートで指定) に変更してください。

   ```powershell
   $VmssName = "NT1"
   $clustername = <clustername>
   $supportResourceGroupName = "SFC_" + (Get-AzServiceFabricManagedCluster -ResourceGroupName $resourceGroupName -Name $clustername).ClusterId
   Get-AzVmssDiskEncryption -ResourceGroupName $supportResourceGroupName -VMScaleSetName $VmssName
   ```

   出力はこのようになります。

   ```console
   ResourceGroupName            : SFC_########-####-####-####-############
   VmScaleSetName               : NT1
   EncryptionEnabled            : True
   EncryptionExtensionInstalled : True
   ```

## <a name="next-steps"></a>次のステップ

[サンプル: Standard SKU Service Fabric マネージド クラスター、ディスク暗号化を有効にした 1 つのノードの種類](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/SF-Managed-Standard-SKU-1-NT-DiskEncryption)

[Windows VM 用の Azure Disk Encryption](../virtual-machines/windows/disk-encryption-overview.md)

[Azure Resource Manager を使用して仮想マシン スケール セットを暗号化する](../virtual-machine-scale-sets/disk-encryption-azure-resource-manager.md)