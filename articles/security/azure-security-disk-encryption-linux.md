---
title: Linux IaaS VM で Azure Disk Encryption を有効にする
description: この記事では、Linux IaaS VM で Microsoft Azure Disk Encryption を有効にする手順を説明します。
author: mestew
ms.service: security
ms.subservice: Azure Disk Encryption
ms.topic: article
ms.author: mstewart
ms.date: 12/12/2018
ms.custom: seodec18
ms.openlocfilehash: d0c9d2084e3dd6b0b6b4bd85a60ad998b54f45a7
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/12/2018
ms.locfileid: "53317493"
---
# <a name="enable-azure-disk-encryption-for-linux-iaas-vms"></a>Linux IaaS VM で Azure Disk Encryption を有効にする 

有効にできるディスク暗号化シナリオは多数あり、手順はシナリオによって異なる場合があります。 以下のセクションでは、Linux IaaS VM 用のシナリオについて詳しく説明します。 ディスク暗号化を使用する前に、[Azure Disk Encryption の前提条件](azure-security-disk-encryption-prerequisites.md)を満たし、「[Additional prerequisites for Linux IaaS VMs](azure-security-disk-encryption-prerequisites.md#bkmk_LinuxPrereq)」(Linux IaaS VM の追加の前提条件) を確認しておく必要があります。

ディスクを暗号化する前に、[スナップショット](../virtual-machines/windows/snapshot-copy-managed-disk.md)またはバックアップを作成します。 バックアップがあると、暗号化中に予期しないエラーが発生した場合に、回復オプションを使用できるようになります。 マネージド ディスクを含む VM では、暗号化する前にバックアップが必要になります。 バックアップを作成すると、Set-AzureRmVMDiskEncryptionExtension コマンドレットを使用し、-skipVmBackup パラメーターを指定してマネージド ディスクを暗号化できます。 暗号化された VM のバックアップと復元方法の詳細については、[Azure Backup](../backup/backup-azure-vms-encryption.md) に関する記事を参照してください。 

>[!WARNING]
 >Azure Disk Encryption では、Key Vault と VM が同じリージョンに配置されている必要があります。 暗号化する VM と同じリージョン内に Key Vault を作成し、使用します。</br></br>

> Linux OS ボリュームの暗号化には数時間かかる場合があります。 通常は、データ ボリュームの暗号化よりも Linux OS ボリュームの暗号化に時間がかかります。 

>Linux VM での暗号化の無効化は、データ ボリュームに対してのみサポートされます。 OS ボリュームが暗号化されている場合、暗号化の無効化はデータ ボリュームまたは OS ボリュームではサポートされません。  


## <a name="bkmk_RunningLinux"> </a> 既存または実行中の IaaS Linux VM で暗号化を有効にする
このシナリオでは、Resource Manager テンプレート、PowerShell コマンドレット、または CLI コマンドを使用して、暗号化を有効にすることができます。 仮想マシンを拡張するためのスキーマの情報が必要な場合は、[Linux 用 Azure Disk Encryption 拡張機能](../virtual-machines/extensions/azure-disk-enc-linux.md)に関する記事を参照してください。

>[!IMPORTANT]
 >Azure Disk Encryption を有効にする前に、Azure Disk Encryption 以外を使用して、マネージド ディスク ベースの VM インスタンスのスナップショットまたはバックアップ (またはその両方) を作成する必要があります。 マネージド ディスクのスナップショットは、ポータルから作成できます。[Azure Backup](../backup/backup-azure-vms-encryption.md) を使用することもできます。 バックアップがあると、暗号化中に予期しないエラーが発生した場合に、回復オプションを使用できるようになります。 バックアップを作成すると、Set-AzureRmVMDiskEncryptionExtension コマンドレットを使用し、-skipVmBackup パラメーターを指定してマネージド ディスクを暗号化できます。 バックアップが作成されておらず、このパラメーターを指定していない場合、マネージド ディスク ベースの VM に対して、Set-AzureRmVMDiskEncryptionExtension コマンドを実行するとエラーが発生します。 
>
>暗号化を有効または無効にすると、VM が再起動する場合があります。 
>

### <a name="bkmk_RunningLinuxCLI"> </a>Azure CLI を使用して既存または実行中の Linux VM で暗号化を有効にする 
暗号化された VHD でのディスク暗号化は、[Azure CLI 2.0](/cli/azure) コマンド ライン ツールをインストールして使用することで有効化できます。 これは、[Azure Cloud Shell](../cloud-shell/overview.md) を使用してブラウザーで使用することも、ローカル コンピューターにインストールして PowerShell セッションで使用することもできます。 Azure 内にある既存または実行中の IaaS Linux VM で暗号化を有効にするには、次の CLI コマンドを使用します。

[az vm encryption enable](/cli/azure/vm/encryption#az-vm-encryption-enable) コマンドを使用して、Azure で実行中の IaaS 仮想マシンで暗号化を有効にします。

-  **実行中の VM を暗号化する:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MySecureRg" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type [All|OS|Data]
     ```

- **KEK を使用して実行中の VM を暗号化する:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MySecureRg" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type [All|OS|Data]
     ```

    >[!NOTE]
    > disk-encryption-keyvault パラメーターの値の構文は、/subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name] という完全な識別子の文字列です。</br> </br>
key-encryption-key パラメーターの値の構文は、 https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] という KEK への完全な URI です。 

- **ディスクが暗号化されていることを確認する:** IaaS VM の暗号化の状態を確認するには、[az vm encryption show](/cli/azure/vm/encryption#az-vm-encryption-show) コマンドを使用します。 

     ```azurecli-interactive
     az vm encryption show --name "MySecureVM" --resource-group "MySecureRg"
     ```

- **暗号化を無効にする:** 暗号化を無効にするには、[az vm encryption disable](/cli/azure/vm/encryption#az-vm-encryption-disable) コマンドを使用します。 Linux VM 用のデータ ボリュームでのみ、暗号化を無効にすることができます。

     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MySecureRg" --volume-type DATA
     ```

### <a name="bkmk_RunningLinuxPSH"> </a>PowerShell を使用して既存または実行中の Linux VM で暗号化を有効にする
[Set-AzureRmVMDiskEncryptionExtension](/powershell/module/azurerm.compute/set-azurermvmdiskencryptionextension) コマンドレットを使用して、Azure で実行中の IaaS 仮想マシンで暗号化を有効にします。 

-  **実行中の VM を暗号化する:** 以下のスクリプトでは変数を初期化し、Set-AzureRmVMDiskEncryptionExtension コマンドレットを実行します。 前提条件として、リソース グループ、VM、およびキー コンテナーが既に作成されている必要があります。 MySecureRg、MySecureVM、および MySecureVault を実際の値に置き換えます。 OS ディスクではなく、データ ディスクを暗号化する場合は、-VolumeType パラメーターの追加が必要になることがあります。 

     ```azurepowershell-interactive
      $rgName = 'MySecureRg';
      $vmName = 'MySecureVM';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;

      Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $rgname -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId;
    ```
- **KEK を使用して実行中の VM を暗号化する:** OS ディスクではなく、データ ディスクを暗号化する場合は、-VolumeType パラメーターの追加が必要になることがあります。 

     ```azurepowershell-interactive
     $rgName = 'MySecureRg';
     $vmName = 'MyExtraSecureVM';
     $KeyVaultName = 'MySecureVault';
     $keyEncryptionKeyName = 'MyKeyEncryptionKey';
     $KeyVault = Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname;
     $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
     $keyEncryptionKeyUrl = (Get-AzureKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;

     Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $rgname -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId;

     ```

    >[!NOTE]
    > disk-encryption-keyvault パラメーターの値の構文は、/subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name] という完全な識別子の文字列です。</br> key-encryption-key パラメーターの値の構文は、 https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] という KEK への完全な URI です。 
    
- **ディスクが暗号化されていることを確認する:** IaaS VM の暗号化の状態を確認するには、[Get-AzureRmVmDiskEncryptionStatus](/powershell/module/azurerm.compute/get-azurermvmdiskencryptionstatus) コマンドレットを使用します。 
    
     ```azurepowershell-interactive 
     Get-AzureRmVmDiskEncryptionStatus -ResourceGroupName MySecureRg -VMName MySecureVM
     ```
    
- **ディスク暗号化を無効にする:** 暗号化を無効にするには、[Disable-AzureRmVMDiskEncryption](/powershell/module/azurerm.compute/disable-azurermvmdiskencryption) コマンドレットを使用します。 Linux VM 用のデータ ボリュームでのみ、暗号化を無効にすることができます。
     
     ```azurepowershell-interactive 
     Disable-AzureRmVMDiskEncryption -ResourceGroupName 'MySecureRG' -VMName 'MySecureVM'
     ```


### <a name="bkmk_RunningLinux"> </a> テンプレートを使用して既存または実行中の IaaS Linux VM で暗号化を有効にする

Azure 内にある既存または実行中の IaaS Linux VM でのディスク暗号化は、[Resource Manager テンプレート](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm-without-aad)を使用して有効化できます。

1. Azure クイックスタート テンプレートで、**[Azure に配置する]** をクリックします。

2. サブスクリプション、リソース グループ、リソース グループの場所、パラメーター、法律条項、および契約を選択します。 **[作成]** をクリックして、既存または実行中の IaaS VM で暗号化を有効にします。

次の表は、既存または実行中の VM に対する、Resource Manager テンプレートのパラメーターをまとめたものです。

| パラメーター | 説明 |
| --- | --- |
| vmName | 暗号化操作を実行する VM の名前。 |
| KeyVaultName | BitLocker キーのアップロード先となる Key Vault の名前。 これは、コマンドレット `(Get-AzureRmKeyVault -ResourceGroupName <MyResourceGroupName>). Vaultname` または Azure CLI コマンド `az keyvault list --resource-group "MySecureGroup"  |ConvertFrom-JSON` を使用して取得できます。|
| keyVaultResourceGroup | キー コンテナーが含まれているリソース グループの名前|
|  keyEncryptionKeyURL | 生成された BitLocker キーの暗号化に使用されるキー暗号化キーの URL。 このパラメーターは、UseExistingKek ドロップダウン リストで **nokek** を選択した場合には省略可能です。 UseExistingKek ドロップダウン リストで **kek** を選択した場合は、_keyEncryptionKeyURL_ 値を入力する必要があります。 |
| volumeType | 暗号化操作が実行されるボリュームの種類。 有効な値は _OS_、_Data_、および _All_ です。 
| forceUpdateTag | 操作が強制実行である必要があるたびに、GUID のような一意の値を渡します。 |
| resizeOSDisk | OS パーティションは、システム ボリュームを分割する前に、完全な OS VHD が占有できるようにサイズ変更する必要があります。 |
| location | すべてのリソースの場所。 |



## <a name="encrypt-virtual-machine-scale-sets"></a>仮想マシン スケール セットを暗号化する
[Azure Virtual Machine Scale Sets](../virtual-machine-scale-sets/overview.md) では、負荷分散が行われる同一の VM のグループを作成して管理することができます。 需要または定義されたスケジュールに応じて、VM インスタンスの数を自動的に増減させることができます。 CLI または Azure PowerShell を使用して仮想マシン スケール セットを暗号化します。

Linux スケール セット データ ディスクの暗号化のバッチ ファイルの例については、[こちら](https://github.com/Azure-Samples/azure-cli-samples/tree/master/disk-encryption/vmss)を参照してください。 この例では、リソース グループ、Linux スケール セットを作成し、5 GB のデータ ディスクをマウントし、仮想マシン スケール セットを暗号化します。

### <a name="register-for-disk-encryption-preview-using-azure-cli"></a>Azure CLI を使用してディスク暗号化プレビューの登録をする

仮想マシン スケール セットのプレビューのための Azure ディスク暗号化では、 [az feature register](/cli/azure/feature#az-feature-register) を使用してサブスクリプションを自己登録する必要があります。 ディスク暗号化のプレビュー機能を初めて利用する場合は､以下の手順を行えばよいだけです｡

```azurecli-interactive
az feature register --name UnifiedDiskEncryption --namespace Microsoft.Compute
```

登録要求が伝達されるのに最大 10 分時間がかかることがあります｡ 登録状態は､[az feature show](/cli/azure/feature#az-feature-show) で確認できます｡ `State` から *Registered* と報告されたら､[az provider register](/cli/azure/provider#az-provider-register) を使って *Microsoft.Compute* プロバイダーを登録し直します。

```azurecli-interactive
az provider register --namespace Microsoft.Compute
```

###  <a name="encrypt-virtual-machine-scale-sets-with-azure-cli"></a>Azure CLI を使用して仮想マシン スケール セットを暗号化する
[az vmss encryption enable](/cli/azure/vmss/encryption#az-vmss-encryption-enable) を使用して、Windows 仮想マシン スケール セットで暗号化を有効にします。 スケール セットのアップグレード ポリシーが manual に設定されている場合は、[az vmss update-instances](/cli/azure/vmss#az-vmss-update-instances) を使用して暗号化を開始します。 前提条件として、リソース グループ、VM、およびキー コンテナーが既に作成されている必要があります。 

-  **実行中の仮想マシン スケール セットを暗号化する**
    ```azurecli-interactive
     az vmss encryption enable --resource-group "MySecureRG" --name "MySecureVmss" --disk-encryption-keyvault "MySecureVault" 
    ```

-  **キーをラップするために KEK を使用して、実行中の仮想マシン スケール セットを暗号化する**
    ```azurecli-interactive
     az vmss encryption enable --resource-group "MySecureRG" --name "MySecureVmss" --disk-encryption-keyvault "MySecureVault" --key-encryption-key "MyKEK" --key-encryption-keyvault "MySecureVault" 

     ```
- **仮想マシン スケール セットの暗号化状態を取得する:**[az vmss encryption show](/cli/azure/vmss/encryption#az-vmss-encryption-show) を使用します

    ```azurecli-interactive
     az vmss encryption show --resource-group "MySecureRG" --name "MySecureVmss"
    ```

- **仮想マシン スケール セットで暗号化を無効化する**:[az vmss encryption disable](/cli/azure/vmss/encryption#az-vmss-encryption-disable) を使用します
    ```azurecli-interactive
     az vmss encryption disable --resource-group "MySecureRG" --name "MySecureVmss"
    ```

### <a name="register-for-disk-encryption-preview-using-azure-powershell"></a>Azure PowerShell を使用してディスク暗号化プレビューの登録をする

仮想マシン スケール セットのプレビューのための Azure ディスク暗号化では、[Register-AzureRmProviderFeature](/powershell/module/azurerm.resources/register-azurermproviderfeature) を使用してサブスクリプションを自己登録する必要があります。 ディスク暗号化のプレビュー機能を初めて利用する場合は、以下の手順を行えばよいだけです｡

```azurepowershell-interactive
Register-AzureRmProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName "UnifiedDiskEncryption"
```

登録要求が伝達されるのに最大 10 分時間がかかることがあります｡ 登録状態は、[Get-AzureRmProviderFeature](/powershell/module/AzureRM.Resources/Get-AzureRmProviderFeature) を使用して確認できます。 `RegistrationState` から *Registered* と報告されたら、[Register-AzureRmResourceProvider](/powershell/module/AzureRM.Resources/Register-AzureRmResourceProvider) を使用して *Microsoft.Compute* プロバイダーを登録し直します。

```azurepowershell-interactive
Get-AzureRmProviderFeature -ProviderNamespace "Microsoft.Compute" -FeatureName "UnifiedDiskEncryption"
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Compute
```

### <a name="encrypt-virtual-machine-scale-sets-with-azure-powershell"></a>Azure PowerShell を使用して仮想マシン スケール セットを暗号化する

[Set-AzureRmVmssDiskEncryptionExtension](/powershell/module/azurerm.compute/set-azurermvmssdiskencryptionextension) コマンドレットを使用して、Windows 仮想マシン スケール セットで暗号化を有効にします。 前提条件として、リソース グループ、VM、およびキー コンテナーが既に作成されている必要があります。

-  **実行中の仮想マシン スケール セットを暗号化する**:
    ```powershell
     $rgName= "MySecureRg";
     $VmssName = "MySecureVmss";
     $KeyVaultName= "MySecureVault";
     $KeyVault = Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgName;
     $DiskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
     Set-AzureRmVmssDiskEncryptionExtension -ResourceGroupName $rgName -VMScaleSetName $VmssName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId;


-  **Encrypt a running virtual machine scale set using KEK to wrap the key**:
    ```powershell
     $rgName= "MySecureRg";
     $VmssName = "MySecureVmss";
     $KeyVaultName= "MySecureVault";
     $keyEncryptionKeyName = "MyKeyEncryptionKey";
     $KeyVault = Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgName;
     $DiskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
     Set-AzureRmVmssDiskEncryptionExtension -ResourceGroupName $rgName -VMScaleSetName $VmssName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId;
    ```

- **仮想マシン スケール セットの暗号化状態を取得する:**[Get-AzureRmVmssVMDiskEncryption](/powershell/module/azurerm.compute/get-azurermvmssvmdiskencryption) コマンドレットを使用します。
    
    ```powershell
    get-AzureRmVmssVMDiskEncryption -ResourceGroupName "MySecureRG" -VMScaleSetName "MySecureVmss"
    ```

- **仮想マシン スケール セットで暗号化を無効化する**:[Disable-AzureRmVmssDiskEncryption](/powershell/module/azurerm.compute/disable-azurermvmssdiskencryption) コマンドレットを使用します。 

    ```powershell
    Disable-AzureRmVmssDiskEncryption -ResourceGroupName "MySecureRG" -VMScaleSetName "MySecureVmss"
    ```

### <a name="azure-resource-manager-templates-for-linux-virtual-machine-scale-sets"></a>Linux 仮想マシン スケール セット用の Azure Resource Manager テンプレート

Linux 仮想マシン スケール セットを暗号化または暗号化解除するには、Azure Resource Manager テンプレートと次の手順を使用します:

- [Linux 仮想マシン スケール セットでの暗号化を有効にする](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-vmss-linux)
- [ジャンプボックスで Linux VM の VM スケール セットをデプロイし、Linux VM スケール セットでの暗号化を有効にする](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-linux-jumpbox)
- [Linux VM スケール セットでの暗号化を無効にする](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-vmss-linux)

     1. **[Azure へのデプロイ]** をクリックします。
     2. 必須のフィールドに入力し、使用条件に同意します。
     3. **[購入]** をクリックしてテンプレートをデプロイします。

## <a name="bkmk_EFA"> </a>Linux IaaS VM 上のデータ ディスクに対して EncryptFormatAll 機能を使用する
**EncryptFormatAll** パラメーターを使用すると、Linux データ ディスクを暗号化する時間が短縮されます。 特定の条件を満たすパーティションが (現在のファイル システムと共に) フォーマットされます。 続いて、コマンドの実行前にパーティションが元の場所に再マウントされます。 条件を満たすデータ ディスクを除外する場合は、コマンドの実行前にそのディスクをマウント解除できます。

 このコマンドを実行すると、以前にマウントされたドライブがフォーマットされます。 これで、空になったドライブ上で暗号化レイヤーが開始されます。 このオプションを選択すると、VM に接続されている一時的なリソース ディスクも暗号化されます。 リセットされた一時的なドライブは、次の機会に Azure Disk Encryption ソリューションによって、VM 用に再フォーマットおよび再暗号化されます。

>[!WARNING]
> VM のデータ ボリュームに必要なデータがある場合は、EncryptFormatAll を使用しないでください。 ディスクをマウント解除することで、そのディスクを暗号化の対象から除外できます。 運用環境の VM で EncryptFormatAll を使用する前に、EncryptFormatAll をテスト用 VM で試し、機能パラメーターとその意味について理解しておく必要があります。 EncryptFormatAll オプションはデータ ディスクをフォーマットするため、ディスク上のデータはすべて失われます。 手順を進める前に、除外するディスクが適切にマウント解除されていることを確認してください。 </br></br>
 >暗号化の設定の更新中にこのパラメーターを設定すると、実際の暗号化の前に再起動が行われる可能性があります。 その場合、フォーマットしないディスクを fstab ファイルから削除する必要があります。 同様に、暗号化操作を開始する前に、暗号化フォーマットするパーティションを fstab ファイルに追加する必要があります。 

### <a name="bkmk_EFACriteria"> </a> EncryptFormatAll 条件
パーティションが以下の条件を**すべて**満たしている場合に限り、このパラメーターはすべてのパーティションを確認して暗号化します。 
- ルート/OS/ブート パーティションではない
- まだ暗号化されていない
- BEK ボリュームではない
- RAID ボリュームではない
- LVM ボリュームではない
- マウントされている

RAID または LVM ボリュームではなく RAID または LVM ボリュームを構成するディスクを暗号化します。

### <a name="bkmk_EFAPSH"> </a> Azure CLI で EncryptFormatAll パラメーターを使用する
[az vm encryption enable](/cli/azure/vm/encryption#az-vm-encryption-enable) コマンドを使用して、Azure で実行中の IaaS 仮想マシンで暗号化を有効にします。

-  **EncryptFormatAll を使用して、実行中の VM を暗号化する:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MySecureRg" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --encrypt-format-all
     ```

### <a name="bkmk_EFAPSH"> </a> EncryptFormatAll パラメーターを PowerShell コマンドレットで使用する
[Set-AzureRmVMDiskEncryptionExtension](/powershell/module/azurerm.compute/set-azurermvmdiskencryptionextension) コマンドレットを [EncryptFormatAll パラメーター](https://www.powershellgallery.com/packages/AzureRM/5.0.0)と共に使用します。 

**EncryptFormatAll を使用して、実行中の VM を暗号化する:** たとえば、以下のスクリプトでは変数を初期化し、EncryptFormatAll パラメーターを指定して Set-AzureRmVMDiskEncryptionExtension コマンドレットを実行します。 前提条件として、リソース グループ、VM、およびキー コンテナーが既に作成されている必要があります。 MySecureRg、MySecureVM、および MySecureVault を実際の値に置き換えます。
  
   ```azurepowershell-interactive
     $rgName = 'MySecureRg';
     $vmName = 'MySecureVM';
     $KeyVaultName = 'MySecureVault';
     $KeyVault = Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname;
     $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
      
     Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $rgname -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -EncryptFormatAll
   ```


### <a name="bkmk_EFALVM"> </a> EncryptFormatAll パラメーターを論理ボリューム マネージャー (LVM) で使用する 
LVM-on-crypt のセットアップをお勧めします。 以下に示すすべての例では、デバイス パスとマウント ポイントをユース ケースに適した値に置き換えます。 このセットアップは次の手順で行うことができます。

- VM を構成するデータ ディスクを追加します。
- それらのディスクをフォーマットおよびマウントして fstab ファイルに追加します。

    1. 新しく追加されたディスクをフォーマットします。 ここでは、Azure によって生成されたシンボリック リンクを使用します。 シンボリック リンクを使用すると、デバイス名の変更に関連する問題を回避できます。 詳細については、[デバイス名の問題のトラブルシューティング](../virtual-machines/linux/troubleshoot-device-names-problems.md)に関する記事を参照してください。
    
         `mkfs -t ext4 /dev/disk/azure/scsi1/lun0`
    
    2. ディスクをマウントします。
         
         `mount /dev/disk/azure/scsi1/lun0 /mnt/mountpoint`
    
    3. fstab に追加します。
         
        `echo "/dev/disk/azure/scsi1/lun0 /mnt/mountpoint ext4 defaults,nofail 1 2" >> /etc/fstab`
    
    4. -EncryptFormatAll を指定して Set-AzureRmVMDiskEncryptionExtension PowerShell コマンドレットを実行し、これらのディスクを暗号化します。
         ```azurepowershell-interactive
         Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName "MySecureGroup" -VMName "MySecureVM" -DiskEncryptionKeyVaultUrl "https://mykeyvault.vault.azure.net/" -EncryptFormatAll
         ```
    5. これらの新しいディスク上に LVM を設定します。 VM の起動が完了した後で、暗号化されたドライブのロックが解除されます。 そのため、LVM のマウントをその後に遅延させる必要があります。


## <a name="bkmk_VHDpre"> </a>お客様が暗号化した VHD と暗号化キーから作成された新しい IaaS VM
このシナリオでは、PowerShell コマンドレットまたは CLI コマンドを使用して、暗号化を有効にすることができます。 

付録の手順を使用して、Azure で使用できる事前に暗号化されたイメージを準備します。 イメージを作成したら、次のセクションの手順に従って、暗号化された Azure VM を作成できます。

* [事前に暗号化された Windows VHD を準備する](azure-security-disk-encryption-appendix.md#bkmk_preWin)
* [事前に暗号化された Linux VHD を準備する](azure-security-disk-encryption-appendix.md#bkmk_preLinux)

>[!IMPORTANT]
 >Azure Disk Encryption を有効にする前に、Azure Disk Encryption 以外を使用して、マネージド ディスク ベースの VM インスタンスのスナップショットまたはバックアップ (またはその両方) を作成する必要があります。 マネージド ディスクのスナップショットは、ポータルから作成できます。[Azure Backup](../backup/backup-azure-vms-encryption.md) を使用することもできます。 バックアップがあると、暗号化中に予期しないエラーが発生した場合に、回復オプションを使用できるようになります。 バックアップを作成すると、Set-AzureRmVMDiskEncryptionExtension コマンドレットを使用し、-skipVmBackup パラメーターを指定してマネージド ディスクを暗号化できます。 バックアップが作成されておらず、このパラメーターを指定していない場合、マネージド ディスク ベースの VM に対して、Set-AzureRmVMDiskEncryptionExtension コマンドを実行するとエラーが発生します。 
>
>暗号化を有効または無効にすると、VM が再起動する場合があります。 



### <a name="bkmk_VHDprePSH"> </a> Azure PowerShell を使用して事前に暗号化された VHD で IaaS VM を暗号化する 
PowerShell コマンドレット [Set-AzureRmVMOSDisk](/powershell/module/azurerm.compute/set-azurermvmosdisk#examples) を使用して、暗号化された VHD でディスク暗号化を有効にすることができます。 次の例では、一般的ないくつかのパラメーターを示します。 

```azurepowershell-interactive
$VirtualMachine = New-AzureRmVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Windows -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzureRmVM -VM $VirtualMachine -ResourceGroupName "MySecureRG"
```

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>新しく追加されたデータ ディスクで暗号化を有効にする

[az vm disk attach](../virtual-machines/linux/add-disk.md) を使用して、または [Azure Portal](../virtual-machines/linux/attach-disk-portal.md) から新しいデータ ディスクを追加できます。 暗号化の前に、新しく接続されたデータ ディスクをマウントする必要があります。 暗号化の実行中はデータ ドライブが使用できなくなるため、そのドライブの暗号化を要求する必要があります。 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-cli"></a>Azure CLI を使用して新しく追加されたディスクで暗号化を有効にする

 VM が以前に "All" で暗号化された場合、--volume-type パラメーターは All のままになっているはずです。 All には OS とデータ ディスクの両方が含まれます。 VM が以前にボリュームの種類 "OS" で暗号化された場合は、--volume-type パラメーターを All に変更して、OS と新しいデータ ディスクの両方が含まれるようにする必要があります。 VM がボリュームの種類 "Data" でのみ暗号化された場合、以下に示すように VM は "Data" のままになっている可能性があります。 新しいデータ ディスクを VM に追加してアタッチするだけでは、暗号化の準備としては不十分です。 暗号化を有効にする前に、新しくアタッチしたディスクをフォーマットして、VM 内で適切にマウントする必要もあります。 Linux では、[永続的なブロック デバイス名](https://docs.microsoft.com/azure/virtual-machines/linux/troubleshoot-device-names-problems)を使用して /etc/fstab にディスクをマウントする必要があります。  

PowerShell 構文とは異なり、CLI では暗号化を有効にする際にユーザーが一意のシーケンス バージョンを指定する必要はありません。 CLI では独自の一意のシーケンス バージョン値が自動的に生成され、使用されます。

-  **実行中の VM のデータ ボリュームを暗号化する:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MySecureRg" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type "Data"
     ```

- **KEK を使用し、実行中の VM のデータ ボリュームを暗号化する:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MySecureRg" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "Data"
     ```

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>Azure PowerShell を使用して新しく追加されたディスクで暗号化を有効にする
 Powershell を使用して Linux 用の新しいディスクを暗号化する場合は、新しいシーケンス バージョンを指定する必要があります。 シーケンス バージョンは一意である必要があります。 次のスクリプトでは、シーケンス バージョン用の GUID が生成されます。 
 

-  **実行中の VM のデータ ボリュームを暗号化する:** 以下のスクリプトでは変数を初期化し、Set-AzureRmVMDiskEncryptionExtension コマンドレットを実行します。 前提条件として、リソース グループ、VM、およびキー コンテナーが既に作成されている必要があります。 MySecureRg、MySecureVM、および MySecureVault を実際の値に置き換えます。 -VolumeType パラメーターに使用できる値は、All、OS、Data です。 VM が以前にボリュームの種類 "OS" または "All" で暗号化された場合は、-VolumeType パラメーターを All に変更して、OS と新しいデータ ディスクの両方が含まれるようにする必要があります。

     ```azurepowershell-interactive
      $sequenceVersion = [Guid]::NewGuid();
      $rgName = 'MySecureRg';
      $vmName = 'MySecureVM';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;

      Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $rgname -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType 'data' –SequenceVersion $sequenceVersion;
    ```
- **KEK を使用し、実行中の VM のデータ ボリュームを暗号化する:**-VolumeType パラメーターに使用できる値は、All、OS、Data です。 VM が以前にボリュームの種類 "OS" または "All" で暗号化された場合は、-VolumeType パラメーターを All に変更して、OS と新しいデータ ディスクの両方が含まれるようにする必要があります。

     ```azurepowershell-interactive
     $rgName = 'MySecureRg';
     $vmName = 'MyExtraSecureVM';
     $KeyVaultName = 'MySecureVault';
     $keyEncryptionKeyName = 'MyKeyEncryptionKey';
     $KeyVault = Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname;
     $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
     $keyEncryptionKeyUrl = (Get-AzureKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;

     Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $rgname -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId -VolumeType 'data';

     ```

    >[!NOTE]
    > disk-encryption-keyvault パラメーターの値の構文は、/subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name] という完全な識別子の文字列です。</br> key-encryption-key パラメーターの値の構文は、 https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] という KEK への完全な URI です。 


## <a name="disable-encryption-for-linux-vms"></a>Linux VM に対して暗号化を無効にする
Azure PowerShell、Azure CLI、または Resource Manager テンプレートを使用して暗号化を無効にすることができます。 

>[!IMPORTANT]
>Linux VM での Azure Disk Encryption による暗号化の無効化は、データ ボリュームに対してのみサポートされます。 OS ボリュームが暗号化されている場合、暗号化の無効化はデータ ボリュームまたは OS ボリュームではサポートされません。  

- **Azure PowerShell を使用してディスク暗号化を無効にする:** 暗号化を無効にするには、[Disable-AzureRmVMDiskEncryption](/powershell/module/azurerm.compute/disable-azurermvmdiskencryption) コマンドレットを使用します。 
     ```azurepowershell-interactive
     Disable-AzureRmVMDiskEncryption -ResourceGroupName 'MySecureRG' -VMName 'MySecureVM' [--volume-type {ALL, DATA, OS}]
     ```

- **Azure CLI を使用して暗号化を無効にする:** 暗号化を無効にするには、[az vm encryption disable](/cli/azure/vm/encryption#az-vm-encryption-disable) コマンドを使用します。 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MySecureRg" --volume-type [ALL, DATA, OS]
     ```
- **Resource Manager テンプレートを使用して暗号化を無効にする:** 暗号化を無効にするには、「[Disable encryption on a running Linux VM](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-linux-vm-without-aad)」 (実行中の Linux VM での暗号化を無効にする) のテンプレートを使用します。
     1. **[Azure へのデプロイ]** をクリックします。
     2. サブスクリプション、リソース グループ、場所、VM、法律条項、および契約を選択します。
     3.  **[購入]** をクリックして、実行中の Windows VM でディスク暗号化を無効にします。 


## <a name="next-steps"></a>次の手順
> [!div class="nextstepaction"]
> [Windows で Azure Disk Encryption を有効にする](azure-security-disk-encryption-windows.md)
