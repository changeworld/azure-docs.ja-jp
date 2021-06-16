---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 06/19/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 517bde8d365793906c24d0f242d4adc1b6241e7c
ms.sourcegitcommit: 8651d19fca8c5f709cbb22bfcbe2fd4a1c8e429f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/14/2021
ms.locfileid: "112084032"
---
Azure PowerShell、Azure CLI、または Resource Manager テンプレートを使用して暗号化を無効にすることができます。 OS とデータ ディスクの両方が暗号化されている場合は、Windows VM におけるデータ ディスクの暗号化の無効化が想定どおりに機能しません。 代わりに、すべてのディスクで暗号化を無効にしてください。

- **Azure PowerShell を使用してディスク暗号化を無効にする:** 暗号化を無効にするには、[Disable-AzVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) コマンドレットを使用します。 
     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM' -VolumeType "all"
     ```

- **Azure CLI を使用して暗号化を無効にする:** 暗号化を無効にするには、[az vm encryption disable](/cli/azure/vm/encryption#az_vm_encryption_disable) コマンドを使用します。 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type "all"
     ```
- **Resource Manager テンプレートを使用して暗号化を無効にする:** 

    1. [実行中の Windows VM でディスク暗号化を無効にする](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.compute/decrypt-running-windows-vm-without-aad)ためのテンプレートで **[Azure に配置する]** をクリックします。
    2. サブスクリプション、リソース グループ、場所、VM、ボリュームの種類、法律条項、および契約を選択します。
    3.  **[購入]** をクリックして、実行中の Windows VM でディスク暗号化を無効にします。 
