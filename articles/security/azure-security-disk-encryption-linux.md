---
title: Linux IaaS VM の Azure Disk Encryption | Microsoft Docs
description: この記事では、Linux IaaS VM で Microsoft Azure Disk Encryption を有効にする手順を説明します。
services: security
documentationcenter: na
author: mestew
manager: MBaldwin
ms.assetid: 071ee0c3-2e6c-4ea9-bfc7-908865629144
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/30/2018
ms.author: mstewart
ms.openlocfilehash: 92c9fce39a0fe068e29cf0a75b3c786891876dee
ms.sourcegitcommit: e3d5de6d784eb6a8268bd6d51f10b265e0619e47
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2018
ms.locfileid: "39392999"
---
# <a name="enable-azure-disk-encryption-for-linux-iaas-vms"></a>Linux Iaas VM で Azure Disk Encryption を有効にする 

有効にできるディスク暗号化シナリオは多数あり、手順はシナリオによって異なる場合があります。 以下のセクションでは、Linux IaaS VM 用のシナリオについて詳しく説明します。 ディスク暗号化を使用する前に、[Azure Disk Encryption の前提条件](azure-security-disk-encryption-prerequisites.md)を満たし、「[Additional prerequisites for Linux IaaS VMs](azure-security-disk-encryption-prerequisites.md#bkmk_LinuxPrereq)」(Linux IaaS VM の追加の前提条件) を確認しておく必要があります。

ディスクを暗号化する前に、[スナップショット](../virtual-machines/windows/snapshot-copy-managed-disk.md)またはバックアップを作成します。 バックアップがあると、暗号化中に予期しないエラーが発生した場合に、回復オプションを使用できるようになります。 マネージド ディスクを含む VM では、暗号化する前にバックアップが必要になります。 バックアップを作成すると、Set-AzureRmVMDiskEncryptionExtension コマンドレットを使用し、-skipVmBackup パラメーターを指定してマネージド ディスクを暗号化できます。 暗号化された VM のバックアップと復元方法の詳細については、[Azure Backup](../backup/backup-azure-vms-encryption.md) に関する記事を参照してください。 

>[!WARNING]
 >暗号化シークレットがリージョンの境界を超えないようにするため、Azure Disk Encryption では Key Vault と VM を同じリージョンに併置する必要があります。 Key Vault を作成し、暗号化する VM と同じリージョン内で使用します。</br></br>

> Linux OS ボリュームの暗号化には数時間かかる場合があります。 通常は、データ ボリュームの暗号化よりも Linux OS ボリュームの暗号化に時間がかかります。 

>Linux VM での暗号化の無効化は、データ ボリュームに対してのみサポートされます。 OS ボリュームが暗号化されている場合、暗号化の無効化はデータ ボリュームまたは OS ボリュームではサポートされません。  


## <a name="bkmk_NewLinux"></a> ディスク暗号化が有効な新しい Linux IaaS VM をデプロイする 

1. [Resource Manager テンプレート](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-full-disk-encrypted-rhel)を使用して、暗号化された Linux IaaS VM を新しく作成します。 このテンプレートでは、マネージド ディスクを使用して、200 GB の RAID-0 アレイとディスク全体の暗号化を含む RedHat Linux 7.2 VM が新しく作成されます。 [FAQ](azure-security-disk-encryption-faq.md#bkmk_LinuxOSSupport) の記事に示すとおり、データ ディスクの暗号化は一部の Linux ディストリビューションでのみサポートされています。 ただし、このテンプレートは、テンプレートのデプロイ方法および複数の手段による暗号化の状態の確認方法について理解する機会を提供します。 
 
1. Azure Resource Manager テンプレートで、**[Azure に配置する]** をクリックします。

2. サブスクリプション、リソース グループ、リソース グループの場所、パラメーター、法律条項、および契約を選択します。 **[作成]** をクリックして、既存または実行中の IaaS VM で暗号化を有効にします。

3. テンプレートをデプロイした後、任意の方法を使用して、VM の暗号化の状態を確認します。
     - Azure CLI で [az vm encryption show](/cli/azure/vm/encryption#az-vm-encryption-show) コマンドを使用して確認します。 

         ```azurecli-interactive 
         az vm encryption show --name "MySecureVM" --resource-group "MySecureRg"
         ```

     - Azure PowerShell で [Get-AzureRmVmDiskEncryptionStatus](/powershell/module/azurerm.compute/get-azurermvmdiskencryptionstatus) コマンドレットを使用して確認します。 

         ```azurepowershell-interactive
         Get-AzureRmVmDiskEncryptionStatus -ResourceGroupName 'MySecureRg' -VMName 'MySecureVM'
         ```

     - VM を選択し、**[設定]** 見出しの下にある **[ディスク]** をクリックして、ポータルで暗号化の状態を確認します。 **[暗号化]** の下のグラフには、暗号化が有効かどうかが表示されます。 

| パラメーター | 説明 |
| --- | --- |
| AAD クライアント ID | Key Vault にシークレットを書き込むためのアクセス許可を持つ Azure AD アプリケーションのクライアント ID。 |
| AAD クライアント シークレット | Key Vault にシークレットを書き込むためのアクセス許可を持つ Azure AD アプリケーションのクライアント シークレット。 |
| Key Vault 名 | キーの配置先となる Key Vault の名前。 |
| Key Vault リソース グループ | Key Vault のリソース グループ。 |


## <a name="bkmk_RunningLinux"> </a> 既存または実行中の IaaS Linux VM で暗号化を有効にする
このシナリオでは、Resource Manager テンプレート、PowerShell コマンドレット、または CLI コマンドを使用して、暗号化を有効にすることができます。 

>[!IMPORTANT]
 >Azure Disk Encryption を有効にする前に、Azure Disk Encryption 以外を使用して、マネージド ディスク ベースの VM インスタンスのスナップショットまたはバックアップ (またはその両方) を作成する必要があります。 マネージド ディスクのスナップショットは、ポータルから作成できます。また、[Azure Backup](../backup/backup-azure-vms-encryption.md) を使用できます。 バックアップがあると、暗号化中に予期しないエラーが発生した場合に、回復オプションを使用できるようになります。 バックアップを作成すると、Set-AzureRmVMDiskEncryptionExtension コマンドレットを使用し、-skipVmBackup パラメーターを指定してマネージド ディスクを暗号化できます。 バックアップが作成されておらず、このパラメーターを指定していない場合、マネージド ディスク ベースの VM に対して、Set-AzureRmVMDiskEncryptionExtension コマンドを実行するとエラーが発生します。 
>
>暗号化を有効または無効にすると、VM が再起動する場合があります。 
>

### <a name="bkmk_RunningLinuxCLI"> </a>Azure CLI を使用して既存または実行中の Linux VM で暗号化を有効にする 
暗号化された VHD でのディスク暗号化は、[Azure CLI 2.0](/cli/azure) コマンド ライン ツールをインストールして使用することで有効化できます。 これは、[Azure Cloud Shell](../cloud-shell/overview.md) を使用してブラウザーで使用することも、ローカル コンピューターにインストールして PowerShell セッションで使用することもできます。 Azure 内にある既存または実行中の IaaS Linux VM で暗号化を有効にするには、次の CLI コマンドを使用します。

[az vm encryption enable](/cli/azure/vm/encryption#az-vm-encryption-enable) コマンドを使用して、Azure で実行中の IaaS 仮想マシンで暗号化を有効にします。

-  **クライアント シークレットを使用して実行中の VM を暗号化する:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MySecureRg" --name "MySecureVM" --aad-client-id "<my spn created with CLI/my Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault "MySecureVault" --volume-type [All|OS|Data]
     ```

- **クライアント シークレットをラップするために KEK を使用して実行中の VM を暗号化する:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MySecureRg" --name "MySecureVM" --aad-client-id "<my spn created with CLI which is the Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type [All|OS|Data]
     ```

    >[!NOTE]
    > disk-encryption-keyvault パラメーターの値の構文は、/subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name] という完全な識別子の文字列です。</br> </br>
key-encryption-key パラメーターの値の構文は、https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] という KEK への完全な URI です。 

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

-  **クライアント シークレットを使用して実行中の VM を暗号化する:** 次のスクリプトでは変数を初期化し、Set-AzureRmVMDiskEncryptionExtension コマンドレットを実行します。 前提条件として、リソース グループ、VM、キー コンテナー、AAD アプリ、およびクライアント シークレットが既に作成されている必要があります。 MySecureRg、MySecureVM、MySecureVault、My-AAD-client-ID、My-AAD-client-secret をそれぞれ実際の値に置き換えます。 OS ディスクではなく、データ ディスクを暗号化する場合は、-VolumeType パラメーターの追加が必要になることがあります。 

     ```azurepowershell-interactive
      $rgName = 'MySecureRg';
      $vmName = 'MySecureVM';
      $aadClientID = 'My-AAD-client-ID';
      $aadClientSecret = 'My-AAD-client-secret';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;

      Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $rgname -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId;
    ```
- **クライアント シークレットをラップするために KEK を使用して実行中の VM を暗号化する:** Azure Disk Encryption では、ご利用のキー コンテナーで既存のキーを指定し、暗号化を有効にしたときに生成されたディスク暗号化シークレットをラップすることができます。 キー暗号化キーが指定されている場合、Azure Disk Encryption では、Key Vault に書き込まれる前に、そのキーを使用して暗号化シークレットがラップされます。 OS ディスクではなく、データ ディスクを暗号化する場合は、-VolumeType パラメーターの追加が必要になることがあります。 

     ```azurepowershell-interactive
     $rgName = 'MySecureRg';
     $vmName = 'MyExtraSecureVM';
     $aadClientID = 'My-AAD-client-ID';
     $aadClientSecret = 'My-AAD-client-secret';
     $KeyVaultName = 'MySecureVault';
     $keyEncryptionKeyName = 'MyKeyEncryptionKey';
     $KeyVault = Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname;
     $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
     $keyEncryptionKeyUrl = (Get-AzureKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;

     Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $rgname -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId;

     ```

    >[!NOTE]
    > disk-encryption-keyvault パラメーターの値の構文は、/subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name] という完全な識別子の文字列です。</br> key-encryption-key パラメーターの値の構文は、https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] という KEK への完全な URI です。 
    
- **ディスクが暗号化されていることを確認する:** IaaS VM の暗号化の状態を確認するには、[Get-AzureRmVmDiskEncryptionStatus](/powershell/module/azurerm.compute/get-azurermvmdiskencryptionstatus) コマンドレットを使用します。 
    
     ```azurepowershell-interactive 
     Get-AzureRmVmDiskEncryptionStatus -ResourceGroupName MySecureRg -VMName MySecureVM
     ```
    
- **ディスク暗号化を無効にする:** 暗号化を無効にするには、[Disable-AzureRmVMDiskEncryption](/powershell/module/azurerm.compute/disable-azurermvmdiskencryption) コマンドレットを使用します。 Linux VM 用のデータ ボリュームでのみ、暗号化を無効にすることができます。
     
     ```azurepowershell-interactive 
     Disable-AzureRmVMDiskEncryption -ResourceGroupName 'MySecureRG' -VMName 'MySecureVM'
     ```


### <a name="bkmk_RunningLinux"> </a> テンプレートを使用して既存または実行中の IaaS Linux VM で暗号化を有効にする

Azure 内にある既存または実行中の IaaS Linux VM でのディスク暗号化は、[Resource Manager テンプレート](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm)を使用して有効化できます。

1. Azure クイックスタート テンプレートで、**[Azure に配置する]** をクリックします。

2. サブスクリプション、リソース グループ、リソース グループの場所、パラメーター、法律条項、および契約を選択します。 **[作成]** をクリックして、既存または実行中の IaaS VM で暗号化を有効にします。

次の表は、Azure AD のクライアント ID を使用する既存または実行中の VM に対する、Resource Manager テンプレートのパラメーターをまとめたものです。

| パラメーター | 説明 |
| --- | --- |
| AADClientID | Key Vault にシークレットを書き込むためのアクセス許可を持つ Azure AD アプリケーションのクライアント ID。 |
| AADClientSecret | Key Vault にシークレットを書き込むためのアクセス許可を持つ Azure AD アプリケーションのクライアント シークレット。 |
| KeyVaultName | キーのアップロード先となる Key Vault の名前。 これは、Azure CLI コマンド `az keyvault show --name "MySecureVault" --query resourceGroup` を使用して取得できます。 |
|  keyEncryptionKeyURL | 生成されたキーの暗号化に使用されるキー暗号化キーの URL。 このパラメーターは、UseExistingKek ドロップダウン リストで **nokek** を選択した場合には省略可能です。 UseExistingKek ドロップダウン リストで **kek** を選択した場合は、_keyEncryptionKeyURL_ 値を入力する必要があります。 |
| volumeType | 暗号化操作が実行されるボリュームの種類。 サポートされている有効な値は _OS_ または _All_ です (OS ディスクおよびデータ ディスクに対してサポートされる Linux ディストリビューションとそのバージョンについては、前述の前提条件セクションを参照してください)。 |
| sequenceVersion | BitLocker 操作のシーケンス バージョン。 ディスク暗号化操作が同じ VM で実行されるたびに、このバージョン番号をインクリメントします。 |
| vmName | 暗号化操作が実行される VM の名前。 |
| パスフレーズ | データ暗号化キーとして強力なパスフレーズを入力します。 |



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
- マウントされている


### <a name="bkmk_EFATemplate"> </a> EncryptFormatAll パラメーターをテンプレートで使用する
EncryptFormatAll オプションを使用するには、Linux VM を暗号化する既存の Azure Resource Manager テンプレートを使用して、AzureDiskEncryption リソースの **EncryptionOperation** フィールドを変更します。

1. たとえば、[実行中の Linux IaaS VM を暗号化する Resource Manager テンプレート](https://github.com/vermashi/azure-quickstart-templates/tree/encrypt-format-running-linux-vm/201-encrypt-running-linux-vm)を使用します。 
2. Azure クイックスタート テンプレートで、**[Azure に配置する]** をクリックします。
3. **EncryptionOperation** を **EnableEncryption** から **EnableEncryptionFormatAl** に変更します。
4. サブスクリプション、リソース グループ、リソース グループの場所、その他のパラメーター、法律条項、および契約を選択します。 **[作成]** をクリックして、既存または実行中の IaaS VM で暗号化を有効にします。


### <a name="bkmk_EFAPSH"> </a> EncryptFormatAll パラメーターを PowerShell コマンドレットで使用する
[Set-AzureRmVMDiskEncryptionExtension](/powershell/module/azurerm.compute/set-azurermvmdiskencryptionextension) コマンドレットを [EncryptFormatAll パラメーター](https://www.powershellgallery.com/packages/AzureRM/5.0.0)と共に使用します。 

**クライアント シークレットと EncryptFormatAll を使用して実行中の VM を暗号化する:** たとえば、次のスクリプトでは変数を初期化し、EncryptFormatAll パラメーターを指定して Set-AzureRmVMDiskEncryptionExtension コマンドレットを実行します。 前提条件として、リソース グループ、VM、キー コンテナー、AAD アプリ、およびクライアント シークレットが既に作成されている必要があります。 MySecureRg、MySecureVM、MySecureVault、My-AAD-client-ID、My-AAD-client-secret をそれぞれ実際の値に置き換えます。
  
   ```azurepowershell-interactive
     $rgName = 'MySecureRg';
     $vmName = 'MySecureVM';
     $aadClientID = 'My-AAD-client-ID';
     $aadClientSecret = 'My-AAD-client-secret';
     $KeyVaultName = 'MySecureVault';
     $KeyVault = Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname;
     $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
      
     Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $rgname -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -EncryptFormatAll
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
         Set-AzureRmVMDiskEncryptionExtension -ResouceGroupName "MySecureGroup" -VMName "MySecureVM" -DiskEncryptionKeyVaultUrl "https://mykeyvault.vault.azure.net/" -EncryptFormatAll
         ```
    5. これらの新しいディスク上に LVM を設定します。 VM の起動が完了した後で、暗号化されたドライブのロックが解除されます。 そのため、LVM のマウントをその後に遅延させる必要があります。




## <a name="bkmk_VHDpre"> </a>お客様が暗号化した VHD と暗号化キーから作成された新しい IaaS VM
このシナリオでは、Resource Manager テンプレート、PowerShell コマンドレット、または CLI コマンドを使用して、暗号化を有効にすることができます。 以下のセクションでは、Resource Manager テンプレートと CLI コマンドについて詳しく説明します。 

付録の手順を使用して、Azure で使用できる事前に暗号化されたイメージを準備します。 イメージを作成したら、次のセクションの手順に従って、暗号化された Azure VM を作成できます。

* [事前に暗号化された Windows VHD を準備する](azure-security-disk-encryption-appendix.md#bkmk_preWin)
* [事前に暗号化された Linux VHD を準備する](azure-security-disk-encryption-appendix.md#bkmk_preLinux)

>[!IMPORTANT]
 >Azure Disk Encryption を有効にする前に、Azure Disk Encryption 以外を使用して、マネージド ディスク ベースの VM インスタンスのスナップショットまたはバックアップ (またはその両方) を作成する必要があります。 マネージド ディスクのスナップショットは、ポータルから作成できます。また、[Azure Backup](../backup/backup-azure-vms-encryption.md) を使用できます。 バックアップがあると、暗号化中に予期しないエラーが発生した場合に、回復オプションを使用できるようになります。 バックアップを作成すると、Set-AzureRmVMDiskEncryptionExtension コマンドレットを使用し、-skipVmBackup パラメーターを指定してマネージド ディスクを暗号化できます。 バックアップが作成されておらず、このパラメーターを指定していない場合、マネージド ディスク ベースの VM に対して、Set-AzureRmVMDiskEncryptionExtension コマンドを実行するとエラーが発生します。 
>
>暗号化を有効または無効にすると、VM が再起動する場合があります。 



### <a name="bkmk_VHDprePSH"> </a> Azure PowerShell を使用して事前に暗号化された VHD で IaaS VM を暗号化する 
PowerShell コマンドレット [Set-AzureRmVMOSDisk](/powershell/module/azurerm.compute/set-azurermvmosdisk#examples) を使用して、暗号化された VHD でディスク暗号化を有効にすることができます。 次の例では、一般的ないくつかのパラメーターを示します。 

```powershell
$VirtualMachine = New-AzureRmVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Windows -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzureRmVM -VM $VirtualMachine -ResouceGroupName "MySecureRG"
```

### <a name="bkmk_VHDpreRM"> </a>Resource Manager テンプレートを使用して事前に暗号化された VHD で IaaS VM を暗号化する 
暗号化された VHD でのディスク暗号化は、[Resource Manager テンプレート](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-pre-encrypted-vm)を使用して有効化できます。

1. Azure クイックスタート テンプレートで、**[Azure に配置する]** をクリックします。

2. サブスクリプション、リソース グループ、リソース グループの場所、パラメーター、法律条項、および契約を選択します。 **[作成]** をクリックして、新しい IaaS VM で暗号化を有効にします。

次の表は、暗号化された VHD に対する Resource Manager テンプレートのパラメーターをまとめたものです。

| パラメーター | 説明 |
| --- | --- |
| newStorageAccountName | 暗号化された OS VHD を格納するためのストレージ アカウントの名前。 このストレージ アカウントは、VM と同じリソース グループおよび同じ場所に既に作成されている必要があります。 |
| osVhdUri | ストレージ アカウントからの OS VHD の URI。 |
| osType | OS 製品の種類 (Windows/Linux)。 |
| virtualNetworkName | VM NIC が属している必要がある VNet の名前。 この名前は、VM と同じリソース グループおよび同じ場所に既に作成されている必要があります。 |
| subnetName | VM NIC が属している必要がある VNet のサブネットの名前。 |
| vmSize | VM のサイズ。 現時点では、Standard A、D、および G シリーズのみがサポートされています。 |
| keyVaultResourceID | Azure Resource Manager 内で Key Vault リソースを識別するためのリソース ID。 これは、PowerShell コマンドレット `(Get-AzureRmKeyVault -VaultName &lt;MyKeyVaultName&gt; -ResourceGroupName &lt;MyResourceGroupName&gt;).ResourceId` または Azure CLI コマンド `az keyvault show --name "MySecureVault" --query id` を使用して取得できます。|
| keyVaultSecretUrl | Key Vault 内にセットアップされたディスク暗号化キーの URL。 |
| keyVaultKekUrl | 生成されたディスク暗号化キーの暗号化に使用されるキー暗号化キーの URL。 |
| vmName | IaaS VM の名前。 |
## <a name="enable-encryption-on-a-newly-added-data-disk"></a>新しく追加されたデータ ディスクで暗号化を有効にする
[az vm disk attach](../virtual-machines/linux/add-disk.md) を使用して、または [Azure Portal](../virtual-machines/linux/attach-disk-portal.md) から新しいデータ ディスクを追加できます。 暗号化の前に、新しく接続されたデータ ディスクをマウントする必要があります。 暗号化の実行中はデータ ドライブが使用できなくなるため、そのドライブの暗号化を要求する必要があります。 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-cli"></a>Azure CLI を使用して新しく追加されたディスクで暗号化を有効にする
 暗号化を有効にするコマンドを実行する際に、Azure CLI コマンドによって、新しいシーケンス バージョンが自動的に提供されます。 
-  **クライアント シークレットを使用して実行中の VM を暗号化する:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MySecureRg" --name "MySecureVM" --aad-client-id "<my spn created with CLI/my Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault "MySecureVault" --volume-type "Data"
     ```

- **クライアント シークレットをラップするために KEK を使用して実行中の VM を暗号化する:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MySecureRg" --name "MySecureVM" --aad-client-id "<my spn created with CLI which is the Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "Data"
     ```

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>Azure PowerShell を使用して新しく追加されたディスクで暗号化を有効にする
 Powershell を使用して Linux 用の新しいディスクを暗号化する場合は、新しいシーケンス バージョンを指定する必要があります。 シーケンス バージョンは一意である必要があります。 次のスクリプトでは、シーケンス バージョン用の GUID が生成されます。 
 

-  **クライアント シークレットを使用して実行中の VM を暗号化する:** 次のスクリプトでは変数を初期化し、Set-AzureRmVMDiskEncryptionExtension コマンドレットを実行します。 前提条件として、リソース グループ、VM、キー コンテナー、AAD アプリ、およびクライアント シークレットが既に作成されている必要があります。 MySecureRg、MySecureVM、MySecureVault、My-AAD-client-ID、My-AAD-client-secret をそれぞれ実際の値に置き換えます。 -VolumeType パラメーターは、OS ディスクではなく、データ ディスクに設定されます。 

     ```azurepowershell-interactive
      $sequenceVersion = [Guid]::NewGuid();
      $rgName = 'MySecureRg';
      $vmName = 'MySecureVM';
      $aadClientID = 'My-AAD-client-ID';
      $aadClientSecret = 'My-AAD-client-secret';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;

      Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $rgname -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType 'data' –SequenceVersion $sequenceVersion;
    ```
- **クライアント シークレットをラップするために KEK を使用して実行中の VM を暗号化する:** Azure Disk Encryption では、ご利用のキー コンテナーで既存のキーを指定し、暗号化を有効にしたときに生成されたディスク暗号化シークレットをラップすることができます。 キー暗号化キーが指定されている場合、Azure Disk Encryption では、Key Vault に書き込まれる前に、そのキーを使用して暗号化シークレットがラップされます。 OS ディスクではなく、データ ディスクを暗号化する場合は、-VolumeType パラメーターの追加が必要になることがあります。 

     ```azurepowershell-interactive
     $rgName = 'MySecureRg';
     $vmName = 'MyExtraSecureVM';
     $aadClientID = 'My-AAD-client-ID';
     $aadClientSecret = 'My-AAD-client-secret';
     $KeyVaultName = 'MySecureVault';
     $keyEncryptionKeyName = 'MyKeyEncryptionKey';
     $KeyVault = Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname;
     $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
     $keyEncryptionKeyUrl = (Get-AzureKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;

     Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $rgname -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId;

     ```

    >[!NOTE]
    > disk-encryption-keyvault パラメーターの値の構文は、/subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name] という完全な識別子の文字列です。</br> key-encryption-key パラメーターの値の構文は、https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] という KEK への完全な URI です。 


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
- **Resource Manager テンプレートを使用して暗号化を無効にする:** 暗号化を無効にするには、[実行中の Linux VM での暗号化の無効化](https://aka.ms/decrypt-linuxvm)テンプレートを使用します。
     1. **[Azure へのデプロイ]** をクリックします。
     2. サブスクリプション、リソース グループ、場所、VM、法律条項、および契約を選択します。
     3.  **[購入]** をクリックして、実行中の Windows VM でディスク暗号化を無効にします。 


## <a name="next-steps"></a>次の手順
> [!div class="nextstepaction"]
> [Windows で Azure Disk Encryption を有効にする](azure-security-disk-encryption-windows.md)
