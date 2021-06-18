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
ms.openlocfilehash: adc6f5f707835c954f3524fb2217ed2b8dd3a505
ms.sourcegitcommit: 8651d19fca8c5f709cbb22bfcbe2fd4a1c8e429f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/14/2021
ms.locfileid: "112071851"
---
Azure PowerShell、Azure CLI、または Resource Manager テンプレートを使用して暗号化を無効にすることができます。 

>[!IMPORTANT]
>Linux VM での Azure Disk Encryption による暗号化の無効化は、データ ボリュームに対してのみサポートされます。 OS ボリュームが暗号化されている場合、暗号化の無効化はデータ ボリュームまたは OS ボリュームではサポートされません。  

- **Azure PowerShell を使用してディスク暗号化を無効にする:** 暗号化を無効にするには、[Disable-AzVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) コマンドレットを使用します。 
     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM' [-VolumeType DATA]
     ```

- **Azure CLI を使用して暗号化を無効にする:** 暗号化を無効にするには、[az vm encryption disable](/cli/azure/vm/encryption#az_vm_encryption_disable) コマンドを使用します。 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type DATA
     ```
- **Resource Manager テンプレートを使用して暗号化を無効にする:** 暗号化を無効にするには、「[Disable encryption on a running Linux VM](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.compute/decrypt-running-linux-vm-without-aad)」 (実行中の Linux VM での暗号化を無効にする) のテンプレートを使用します。
     1. **[Azure へのデプロイ]** をクリックします。
     2. サブスクリプション、リソース グループ、場所、VM、法律条項、および契約を選択します。
