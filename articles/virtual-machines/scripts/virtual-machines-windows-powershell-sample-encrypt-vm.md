---
title: Azure PowerShell のサンプル スクリプト - Windows VM の暗号化 | Microsoft Docs
description: Azure PowerShell のサンプル スクリプト - Windows VM の暗号化
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 12/12/2017
ms.author: cynthn
ms.openlocfilehash: af12630839d31bd5e3baa4c67cdd881f936e9d19
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2018
ms.locfileid: "37927531"
---
# <a name="encrypt-a-windows-virtual-machine-with-azure-powershell"></a>Azure PowerShell を使用した Windows 仮想マシンの暗号化

このスクリプトは、セキュリティで保護された Azure Key Vault、暗号化キー、Azure Active Directory サービス プリンシパル、Windows 仮想マシン (VM) を作成します。 その後、Key Vault から取得した暗号化キーとサービス プリンシパルの資格情報を使って、作成した VM を暗号化します。

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>サンプル スクリプト

[!code-powershell[main](../../../powershell_scripts/virtual-machine/encrypt-vm/encrypt-windows-vm.ps1 "Encrypt VM disks")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ 

次のコマンドを実行して、リソース グループ、VM、すべての関連リソースを削除します。

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、以下のコマンドを実行してデプロイを作成します。 表内の各項目は、コマンドごとのドキュメントにリンクされています。

| コマンド | メモ |
|---|---|
| [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | すべてのリソースを格納するリソース グループを作成します。 |
| [New-AzureRmKeyVault](/powershell/module/azurerm.keyvault/new-azurermkeyvault) | セキュリティで保護されたデータ (暗号化キーなど) を格納する Azure Key Vault を作成します。 |
| [Add-AzureKeyVaultKey](/powershell/module/azurerm.keyvault/add-azurekeyvaultkey) | Key Vault に暗号化キーを作成します。 |
| [New-AzureRmADServicePrincipal](/powershell/module/azurerm.resources/new-azurermadserviceprincipal) | 暗号化キーへのアクセスを安全に認証して制御する Azure Active Directory サービス プリンシパルを作成します。 |
| [Set-AzureRmKeyVaultAccessPolicy](/powershell/module/azurerm.keyvault/set-azurermkeyvaultaccesspolicy) | サービス プリンシパルに暗号化キーへのアクセス権を付与するために、Key Vault に対するアクセス許可を設定します。 |
| [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) | 仮想マシンを作成し、作成したマシンをネットワーク カード、仮想ネットワーク、サブネット、およびネットワーク セキュリティ グループに接続します。 また、このコマンドでは、ポート 80 を開いたり、管理者の資格情報を設定したりします。 |
| [Get-AzureRmKeyVault](/powershell/module/azurerm.keyvault/get-azurermkeyvault) | Key Vault に必要な情報を取得します。 |
| [Set-AzureRmVMDiskEncryptionExtension](/powershell/module/azurerm.compute/set-azurermvmdiskencryptionextension) | サービス プリンシパルの資格情報と暗号化キーを使って VM に対する暗号化を有効にします。 |
| [Get-AzureRmVmDiskEncryptionStatus](/powershell/module/azurerm.compute/get-azurermvmdiskencryptionstatus) | VM の暗号化処理の状態を表示します。 |
| [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | リソース グループと、それに含まれているすべてのリソースを削除します。 |

## <a name="next-steps"></a>次の手順

Azure PowerShell モジュールの詳細については、[Azure PowerShell のドキュメント](/powershell/azure/overview)を参照してください。

その他の仮想マシン用の PowerShell サンプル スクリプトは、[Azure Windows VM のドキュメント](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)にあります。
