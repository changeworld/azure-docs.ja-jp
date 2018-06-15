---
title: Azure PowerShell のサンプル スクリプト - OMS | Microsoft Docs
description: Azure PowerShell のサンプル スクリプト - OMS
services: virtual-machines-windows
documentationcenter: virtual-machines
author: neilpeterson
manager: jeconnoc
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 12/12/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: dc6cda6490740488e60f380d99952fa3f8d0c3fb
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/01/2018
ms.locfileid: "34655964"
---
# <a name="create-an-operations-management-suite-monitored-vm-with-powershell"></a>PowerShell を使用して Operations Management Suite によって監視される VM を作成する

このスクリプトでは、Azure 仮想マシンを作成し、Operations Management Suite (OMS) エージェントをインストールして、システムを OMS ワークスペースに登録します。 このスクリプトを実行すると、仮想マシンが OMS コンソールに表示されるようになります。 また、OMS ワークスペース ID とワークスペース キーを更新する必要があります。

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>サンプル スクリプト

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-vm-monitor-oms/create-windows-vm-detailed-oms.ps1 "Create VM OMS")]

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
| [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) | 仮想マシンを作成し、作成したマシンをネットワーク カード、仮想ネットワーク、サブネット、およびネットワーク セキュリティ グループに接続します。 また、このコマンドでは、ポート 80 を開いたり、管理者の資格情報を設定したりします。 |
| [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension) | 仮想マシンに VM 拡張機能を追加します。 このサンプルでは、Operations Management Suite エージェント拡張機能を使用して、OMS エージェントをインストールし OMS ワークスペースに VM を登録します。 |
|[Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | リソース グループと、それに含まれているすべてのリソースを削除します。 |

## <a name="next-steps"></a>次の手順

Azure PowerShell モジュールの詳細については、[Azure PowerShell のドキュメント](/powershell/azure/overview)を参照してください。

その他の仮想マシン用の PowerShell サンプル スクリプトは、[Azure Windows VM のドキュメント](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)にあります。
