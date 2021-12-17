---
title: エフェメラル OS ディスク
description: Azure VM のエフェメラル OS ディスクの詳細について説明します。
author: Aarthi-Vijayaraghavan
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 07/23/2020
ms.author: aarthiv
ms.subservice: disks
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 845bb5f4830c53b4fc4f6688851775afa908af73
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/10/2021
ms.locfileid: "132135294"
---
# <a name="ephemeral-os-disks-for-azure-vms"></a>Azure VM のエフェメラル OS ディスク

**適用対象:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: フレキシブル スケール セット :heavy_check_mark: ユニフォーム スケール セット

エフェメラル OS ディスクは、ローカルの仮想マシン (VM) ストレージで作成され、リモートの Azure Storage に保存されません。 エフェメラル OS ディスクは、ステートレス ワークロードで問題なく動作します。この場合、アプリケーションは個々の VM の障害に耐えられますが、VM のデプロイ時間または個々の VM インスタンスの再イメージ化による影響を、より強く受けます。 エフェメラル OS ディスクでは、OS ディスクへの読み取り/書き込み待機時間が短縮され、VM の再イメージ化が高速化されます。 
 
エフェメラル ディスクの主な機能は次のとおりです。 
- ステートレス アプリケーションに最適です。
- Marketplace、カスタム イメージ、[Azure Compute Gallery](./shared-image-galleries.md) (旧称 Shared Image Gallery) でサポートされます。
- VM およびスケール セット インスタンスを元のブート状態に速やかにリセットまたは再イメージ化することができます。  
- 一時ディスクと同様に、待機時間が短縮されます。 
- エフェメラル OS ディスクは無料で、OS ディスクのストレージ コストはかかりません。
- すべての Azure リージョンで使用可能。  

 
永続 OS ディスクとエフェメラル OS ディスクの主な違いは、次のとおりです。

|   | 永続 OS ディスク | エフェメラル OS ディスク |
|---|---|---|
| **OS ディスクのサイズ制限** | 2 TiB | VM サイズのキャッシュ サイズまたは 2 TiB のうち小さい方。 **GiB 単位のキャッシュ サイズ** については、[DS](sizes-general.md)、[ES](sizes-memory.md)、[M](sizes-memory.md)、[FS](sizes-compute.md)、および [GS](sizes-previous-gen.md#gs-series) に関するページを参照してください |
| **サポート対象の VM サイズ** | All | Premium ストレージをサポートしている DSv1、DSv2、DSv3、Esv3、Fs、FsV2、GS、M、Mdsv2、Bs、Dav4、Eav4 などの VM サイズ |
| **サポート対象のディスクの種類**| マネージド OS ディスクとアンマネージド OS ディスク| マネージド OS ディスクのみ|
| **リージョンのサポート**| すべてのリージョン| すべてのリージョン|
| **データの永続化**| 書き込まれた OS ディスク データは Azure Storage に格納| OS ディスクに書き込まれたデータは、ローカル VM ストレージに格納され、Azure Storage には保存されません。 |
| **停止と割り当て解除**| VM とスケール セット インスタンスの停止と割り当て解除、またその状態からの再起動が可能 | VM とスケール セット インスタンスの停止と割り当て解除は不可|
| **特殊化された OS ディスクのサポート** | はい| いいえ|
| **OS ディスクのサイズ変更**| VM 作成中、VM の停止と割り当て解除後に変更可能| VM 作成時のみ変更可能|
| **VM サイズ変更時の動作**| OS ディスク データを維持| OS ディスクのデータが削除された後、OS が再プロビジョニングされます |
| **Redeploy** | OS ディスク データを維持 | OS ディスクのデータが削除された後、OS が再プロビジョニングされます | 
| **VM の停止/開始** | OS ディスク データを維持 | OS ディスクのデータが削除された後、OS が再プロビジョニングされます | 
| **ページ ファイルの配置**| Windows の場合、ページ ファイルはリソース ディスクに格納されます| Windows の場合、ページ ファイルは OS ディスクに格納されます (OS キャッシュの配置と一時ディスクの配置の両方で)。|


## <a name="size-requirements"></a>サイズの要件

エフェメラル OS ディスクを配置する場所は、VM キャッシュまたは VM 一時ディスクのどちらかを選択できます。
イメージ OS ディスクのサイズは、選択した VM サイズの一時またはキャッシュ ディスクのサイズ以下である必要があります。

たとえば、**OS キャッシュの配置** を選択する場合、Marketplace の標準の Windows Server イメージは約 127 GiB であり、これは 127 GiB 以上のキャッシュを持つ VM サイズが必要であることを意味します。 Standard_DS3_v2 のキャッシュ サイズは 127 GiB であり、十分な大きさです。 この場合、Standard_DS3_v2 が、このイメージで使用できる DSv2 シリーズの最小サイズ です。 

**一時ディスクの配置** を選択する場合、Marketplace の標準の Ubuntu サーバー イメージは約 30 GiB です。 一時ディスクでエフェメラル OS ディスクを有効にするには、一時ディスクのサイズが 30 GiB 以上である必要があります。 Standard_B4ms の一時ディスクのサイズ 32 GiB であり、30 GiB の OS ディスクを収納できます。 VM を作成すると、一時ディスクの領域は 2 GiB になります。 
> [!Important] 
> 一時ディスクの配置を選択した場合、最終的な一時ディスクのサイズは (一時ディスクの初期サイズ - OS イメージのサイズ) です。

`[smallsize]` によって示される、Marketplace の Linux と Windows Server の基本的なイメージは約 30 GiB になることが多く、利用可能な VM サイズのほとんどを使用できます。
また、エフェメラル ディスクでは、VM サイズで **Premium Storage** がサポートされていることも必要です。 通常 (常にではありませんが)、 サイズには DSv2 や EsV3 のように名前に `s` があります。 詳細については、[Azure の VM サイズ](sizes.md)に関する記事で Premium Storage をサポートするサイズの詳細を参照してください。

## <a name="ephemeral-os-disks-can-now-be-stored-on-tempresource-disks"></a>エフェメラル OS ディスクを一時およびリソース ディスクに格納できるようになった
エフェメラル OS ディスクを、VM のキャッシュ ディスクまたは VM の一時およびリソース ディスクに格納できるようになりました。 この機能を使用すると、キャッシュがないか不十分であっても (Dav3、Dav4、Eav4、Eav3 など) エフェメラル OS ディスクをホストするのに十分な一時ディスクがあるすべての VM に、エフェメラル OS ディスクを作成できます。
[DiffDiskPlacement](/rest/api/compute/virtualmachines/list#diffdiskplacement) は新しいプロパティであり、エフェメラル OS ディスクを配置する場所を指定するために使用できます。 この機能を使用すると、Windows VM がプロビジョニングされる場合に、OS ディスクに配置されるページファイルが構成されます。

## <a name="portal"></a>ポータル

Azure portal で、 **[ディスク]** タブの **[詳細設定]** セクションを開いて、仮想マシンまたは仮想マシン スケール セットをデプロイするときに、エフェメラル ディスクの使用を選ぶことができます。エフェメラル OS ディスクの配置を選択するには、 **[OS キャッシュの配置]** または **[一時ディスクの配置]** を選びます。

![エフェメラル OS ディスクの使用を選択するラジオ ボタンを示すスクリーン ショット](./media/virtual-machines-common-ephemeral/ephemeral-portal-temp.png)
 

エフェメラル ディスクの使用、OS キャッシュの配置、または一時ディスクの配置のオプションがグレー表示される場合は、OS イメージより大きいキャッシュまたは一時のサイズがない VM サイズ、または Premium Storage をサポートしていない VM サイズを、選択した可能性があります。 **[基本]** ページに戻り、別の VM サイズを選択してみてください。

## <a name="scale-set-template-deployment"></a>スケール セット テンプレートのデプロイ  
エフェメラル OS ディスクを使用するスケール セットを作成するプロセスでは、テンプレートで `diffDiskSettings` プロパティをリソースの種類 `Microsoft.Compute/virtualMachineScaleSets/virtualMachineProfile` に追加します。 また、エフェメラル OS ディスクに対してキャッシュ ポリシーを `ReadOnly` に設定する必要があります。 OS キャッシュ ディスクの配置では、配置を `CacheDisk` に変更できます。

```json
{ 
  "type": "Microsoft.Compute/virtualMachineScaleSets", 
  "name": "myScaleSet", 
  "location": "East US 2", 
  "apiVersion": "2019-12-01", 
  "sku": { 
    "name": "Standard_DS2_v2", 
    "capacity": "2" 
  }, 
  "properties": { 
    "upgradePolicy": { 
      "mode": "Automatic" 
    }, 
    "virtualMachineProfile": { 
       "storageProfile": { 
        "osDisk": { 
          "diffDiskSettings": { 
            "option": "Local" ,
            "placement": "ResourceDisk"
          }, 
          "caching": "ReadOnly", 
          "createOption": "FromImage" 
        }, 
        "imageReference":  { 
          "publisher": "Canonical", 
          "offer": "UbuntuServer", 
          "sku": "16.04-LTS", 
          "version": "latest" 
        } 
      }, 
      "osProfile": { 
        "computerNamePrefix": "myvmss", 
        "adminUsername": "azureuser", 
        "adminPassword": "P@ssw0rd!" 
      } 
    } 
  } 
}  
```

## <a name="vm-template-deployment"></a>VM テンプレートのデプロイ 
テンプレートを使用して、エフェメラル OS ディスクで VM をデプロイできます。 エフェメラル OS ディスクを使用する VM を作成するプロセスでは、テンプレートで `diffDiskSettings` プロパティをリソースの種類 Microsoft.Compute/virtualMachines に追加します。 また、エフェメラル OS ディスクに対してキャッシュ ポリシーを `ReadOnly` に設定する必要があります。 OS キャッシュ ディスクの配置では、配置オプションを `CacheDisk` に変更できます。

```json
{ 
  "type": "Microsoft.Compute/virtualMachines", 
  "name": "myVirtualMachine", 
  "location": "East US 2", 
  "apiVersion": "2019-12-01", 
  "properties": { 
       "storageProfile": { 
            "osDisk": { 
              "diffDiskSettings": { 
                "option": "Local" ,
                "placement": "ResourceDisk"
              }, 
              "caching": "ReadOnly", 
              "createOption": "FromImage" 
            }, 
            "imageReference": { 
                "publisher": "MicrosoftWindowsServer", 
                "offer": "WindowsServer", 
                "sku": "2016-Datacenter-smalldisk", 
                "version": "latest" 
            }, 
            "hardwareProfile": { 
                 "vmSize": "Standard_DS2_v2" 
             } 
      }, 
      "osProfile": { 
        "computerNamePrefix": "myvirtualmachine", 
        "adminUsername": "azureuser", 
        "adminPassword": "P@ssw0rd!" 
      } 
    } 
 } 
```

> [!NOTE] 
> エフェメラル OS ディスクの配置オプション (VM キャッシュ ディスクまたは VM 一時またはリソース ディスク) は、PowerShell と CLI で間もなく提供される予定です

## <a name="powershell"></a>PowerShell
PowerShell VM デプロイにエフェメラル ディスクを使用するには、VM 構成内で [Set-AzVMOSDisk](/powershell/module/az.compute/set-azvmosdisk) を使用します。 `-DiffDiskSetting` を `Local` に設定し、`-Caching` を `ReadOnly` に設定します。     
```powershell
Set-AzVMOSDisk -DiffDiskSetting Local -Caching ReadOnly
```
スケール セットのデプロイの場合は、構成内で [Set-AzVmssStorageProfile](/powershell/module/az.compute/set-azvmssstorageprofile) コマンドレットを使用します。 `-DiffDiskSetting` を `Local` に設定し、`-Caching` を `ReadOnly` に設定します。

```powershell
Set-AzVmssStorageProfile -DiffDiskSetting Local -OsDiskCaching ReadOnly
```

## <a name="cli"></a>CLI

CLI VM デプロイにエフェメラル ディスクを使用するには、[az vm create](/cli/azure/vm#az_vm_create) 内の `--ephemeral-os-disk` パラメーターを `true` に設定し、`--os-disk-caching` パラメーターを `ReadOnly` に設定します。

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image UbuntuLTS \
  --ephemeral-os-disk true \
  --os-disk-caching ReadOnly \
  --admin-username azureuser \
  --generate-ssh-keys
```

スケール セットの場合は、[az-vmss-create](/cli/azure/vmss#az_vmss_create) に同じ `--ephemeral-os-disk true` パラメーターを使用し、`--os-disk-caching` パラメーターを `ReadOnly` に設定します。

## <a name="reimage-a-vm-using-rest"></a>REST を使用して VM を再イメージ化する
以下で説明するように REST API を使用するか Azure portal で VM の [概要] ウィンドウに移動することで、エフェメラル OS ディスクを使用して仮想マシンを再イメージ化できます。 スケール セットの場合、再イメージ化は PowerShell、CLI、およびポータルを使用して既に利用できます。

```
POST https://management.azure.com/subscriptions/{sub-
id}/resourceGroups/{rgName}/providers/Microsoft.Compute/VirtualMachines/{vmName}/reimage?a pi-version=2019-12-01" 
```
 
## <a name="frequently-asked-questions"></a>よく寄せられる質問

**Q:ローカル OS ディスクのサイズは?**

A: OS キャッシュの配置では VM キャッシュ サイズまで、一時ディスクの配置では一時ディスクのサイズまでの、プラットフォーム、Shared Image Gallery、カスタム イメージをサポートしています。この場合、OS ディスクに対するすべての読み取り/書き込みは、仮想マシンと同じノードのローカル環境で行われます。 

**Q:エフェメラル OS ディスクはサイズ変更できますか?**

A:いいえ。エフェメラル OS ディスクをプロビジョニングした後、OS ディスクをサイズ変更することはできません。 

**Q: VM の作成後に、エフェメラル OS ディスクの配置を変更できますか?**

A: いいえ。エフェメラル OS ディスクをプロビジョニングした後で、OS ディスクの配置を変更することはできません。 ただし、ARM テンプレート デプロイ、PowerShell、または CLI を使用して、選択した OS ディスクの配置を更新することにより、VM を再作成できます。 これにより、OS ディスク上のデータが削除されて VM が再作成され、OS が再プロビジョニングされます。

**Q: イメージ サイズが選択した VM サイズの一時ディスク サイズと等しい場合、一時ディスクは作成されますか?**

A: いいえ。この場合、一時ディスク ドライブは作成されません。

**Q: 優先順位の低い VM とスポット VM では、エフェメラル OS ディスクはサポートされていますか?**

A:はい。 エフェメラル VM には Stop-Deallocate のオプションはありません。ユーザーは、割り当てを解除するのではなく、削除する必要があります。

**Q:エフェメラル VM にマネージド ディスクを接続できますか?**

A:はい。エフェメラル OS ディスクを使用する VM にマネージド データ ディスクをアタッチできます。 

**Q:エフェメラル OS ディスクではすべての VM サイズがサポートされますか?**

A:いいえ。ほとんどの Premium Storage VM サイズがサポートされています (DS、ES、FS、GS、M など)。 特定の VM サイズでエフェメラル OS ディスクがサポートされているかどうかは、次の方法で確認できます。

`Get-AzComputeResourceSku` PowerShell コマンドレットを呼び出します
```azurepowershell-interactive
 
$vmSizes=Get-AzComputeResourceSku | where{$_.ResourceType -eq 'virtualMachines' -and $_.Locations.Contains('CentralUSEUAP')} 

foreach($vmSize in $vmSizes)
{
   foreach($capability in $vmSize.capabilities)
   {
       if($capability.Name -eq 'EphemeralOSDiskSupported' -and $capability.Value -eq 'true')
       {
           $vmSize
       }
   }
}
```
 
**Q:エフェメラル OS ディスクを既存の VM およびスケール セットに適用できますか?**

A:いいえ。エフェメラル OS ディスクは、VM およびスケール セットの作成時にのみ使用できます。 

**Q:スケール セット内でエフェメラル OS ディスクと通常の OS ディスクを混在させることはできますか?**

A:いいえ。同じスケール セット内でエフェメラル OS ディスクと永続 OS ディスクのインスタンスを混在させることはできません。 

**Q:Powershell または CLI を使用してエフェメラル OS ディスクを作成できますか?**

A: はい。REST、テンプレート、PowerShell、CLI を使用して、エフェメラル OS ディスクで VM を作成できます。

**Q:エフェメラル OS ディスクでサポートされていない機能は何ですか?**

A:エフェメラル ディスクでサポートされていない機能は次のとおりです。
- VM イメージのキャプチャ
- ディスクのスナップショット 
- Azure Disk Encryption 
- Azure Backup
- Azure Site Recovery  
- OS ディスクのスワップ 

> [!NOTE]
> 
> エフェメラル ディスクにはポータルからアクセスできません。 予想されるエフェメラル ディスクにアクセスするときに、"リソースが見つかりません" または "404" エラーが表示されます。
> 
 
## <a name="next-steps"></a>次のステップ
[Azure CLI](/cli/azure/vm#az_vm_create) を使用して、エフェメラル OS ディスクで VM を作成できます。
