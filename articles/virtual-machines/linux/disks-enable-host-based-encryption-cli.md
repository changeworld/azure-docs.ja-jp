---
title: ホストでの暗号化を使用してエンドツーエンドの暗号化を有効にする - Azure CLI - マネージド ディスク
description: ホストでの暗号化を使用して、Azure マネージド ディスクでエンドツーエンドの暗号化を有効にします。
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 07/10/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 705f9f3055d40d23c9ec5e24cfccfc0c96e114a5
ms.sourcegitcommit: f7e160c820c1e2eb57dc480b2a8fd6bef7053e91
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2020
ms.locfileid: "86235742"
---
# <a name="enable-end-to-end-encryption-using-encryption-at-host---azure-cli"></a>ホストでの暗号化を使用してエンドツーエンドの暗号化を有効にする - Azure CLI

ホストでの暗号化を有効にすると、VM ホスト上の格納データは、保存時に暗号化され、暗号化された状態でストレージ サービスに送られます。 ホストでの暗号化とその他のマネージド ディスクの暗号化の概要については、「[ホストでの暗号化 - VM データのエンドツーエンドの暗号化](disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data)」を参照してください。

## <a name="restrictions"></a>制限

[!INCLUDE [virtual-machines-disks-encryption-at-host-restrictions](../../../includes/virtual-machines-disks-encryption-at-host-restrictions.md)]

### <a name="supported-regions"></a>サポートされているリージョン

[!INCLUDE [virtual-machines-disks-encryption-at-host-regions](../../../includes/virtual-machines-disks-encryption-at-host-regions.md)]

### <a name="supported-vm-sizes"></a>サポートされる VM のサイズ

[!INCLUDE [virtual-machines-disks-encryption-at-host-suported-sizes](../../../includes/virtual-machines-disks-encryption-at-host-suported-sizes.md)]

また、プログラムによって VM のサイズを確認することもできます。 プログラムによって取得する方法については、「[サポートされている VM のサイズを確認する](#finding-supported-vm-sizes)」セクションを参照してください。

## <a name="prerequisites"></a>前提条件

VM または仮想マシン スケール セットに対してホストでの暗号化を使用できるようにするには、サブスクリプションで機能が有効になっている必要があります。 サブスクリプション ID と共に encryptionAtHost@microsoft.com に電子メールを送信して、サブスクリプションに対して機能を有効にします。

### <a name="create-an-azure-key-vault-and-diskencryptionset"></a>Azure Key Vault と DiskEncryptionSet の作成

機能が有効になったら、まだ行っていない場合は、Azure Key Vault と DiskEncryptionSet を設定する必要があります。

[!INCLUDE [virtual-machines-disks-encryption-create-key-vault-cli](../../../includes/virtual-machines-disks-encryption-create-key-vault-cli.md)]

## <a name="enable-encryption-at-host-for-disks-attached-to-vm-and-virtual-machine-scale-sets"></a>VM と仮想マシン スケール セットに接続されているディスクに対するホストでの暗号化を有効にする

ホストでの暗号化を有効にするには、API バージョン **2020-06-01** 以降を使用して、VM または仮想マシン スケール セットの securityProfile で新しいプロパティ EncryptionAtHost を設定します。

`"securityProfile": { "encryptionAtHost": "true" }`

## <a name="example-scripts"></a>スクリプトの例

### <a name="enable-encryption-at-host-for-disks-attached-to-a-vm-with-customer-managed-keys"></a>カスタマー マネージド キーを使用して VM に接続されているディスクに対するホストでの暗号化を有効にする

以前に作成した DiskEncryptionSet のリソース URI を使用して、マネージド ディスク付き VM を作成します。

`<yourPassword>`、`<yourVMName>`、`<yourVMSize>`、`<yourDESName>`、`<yoursubscriptionID>`、`<yourResourceGroupName>`、`<yourRegion>` を置き換えてから、スクリプトを実行します。

```azurecli
az group deployment create -g <yourResourceGroupName> \
--template-uri "https://raw.githubusercontent.com/Azure-Samples/managed-disks-powershell-getting-started/master/EncryptionAtHost/CreateVMWithDisksEncryptedAtHostWithCMK.json" \
--parameters "virtualMachineName=<yourVMName>" "adminPassword=<yourPassword>" "vmSize=<yourVMSize>" "diskEncryptionSetId=/subscriptions/<yoursubscriptionID>/resourceGroups/<yourResourceGroupName>/providers/Microsoft.Compute/diskEncryptionSets/<yourDESName>" "region=<yourRegion>"
```

### <a name="enable-encryption-at-host-for-disks-attached-to-a-vm-with-platform-managed-keys"></a>プラットフォーム マネージド キーを使用して VM に接続されているディスクに対するホストでの暗号化を有効にする

`<yourPassword>`、`<yourVMName>`、`<yourVMSize>`、`<yourResourceGroupName>`、`<yourRegion>` を置き換えてから、スクリプトを実行します。

```azurecli
az group deployment create -g <yourResourceGroupName> \
--template-uri "https://raw.githubusercontent.com/Azure-Samples/managed-disks-powershell-getting-started/master/EncryptionAtHost/CreateVMWithDisksEncryptedAtHostWithPMK.json" \
--parameters "virtualMachineName=<yourVMName>" "adminPassword=<yourPassword>" "vmSize=<yourVMSize>" "region=<yourRegion>"
```

## <a name="finding-supported-vm-sizes"></a>サポートされている VM のサイズを確認する

レガシ VM のサイズはサポートされていません。 サポートされている VM サイズの一覧は、次のいずれかで見つけることができます。

[リソースの SKU API](https://docs.microsoft.com/rest/api/compute/resourceskus/list) を呼び出し、`EncryptionAtHostSupported` 機能が **True** に設定されていることを確認しています。

```json
    {
        "resourceType": "virtualMachines",
        "name": "Standard_DS1_v2",
        "tier": "Standard",
        "size": "DS1_v2",
        "family": "standardDSv2Family",
        "locations": [
        "CentralUSEUAP"
        ],
        "capabilities": [
        {
            "name": "EncryptionAtHostSupported",
            "value": "True"
        }
        ]
    }
```

または、[Get-AzComputeResourceSku](https://docs.microsoft.com/powershell/module/az.compute/get-azcomputeresourcesku?view=azps-3.8.0) PowerShell コマンドレットを呼び出します。

```powershell
$vmSizes=Get-AzComputeResourceSku | where{$_.ResourceType -eq 'virtualMachines' -and $_.Locations.Contains('CentralUSEUAP')} 

foreach($vmSize in $vmSizes)
{
    foreach($capability in $vmSize.capabilities)
    {
        if($capability.Name -eq 'EncryptionAtHostSupported' -and $capability.Value -eq 'true')
        {
            $vmSize

        }

    }
}
```

## <a name="next-steps"></a>次のステップ

これらのリソースを作成し、構成したので、これらを使用してマネージド ディスクをセキュリティで保護することができます。 次のリンクには、個々のシナリオごとにスクリプトの例が含まれています。これは、マネージド ディスクをセキュリティで保護するために使用できます。

[Azure Resource Manager テンプレートのサンプル](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/tree/master/EncryptionAtHost)
