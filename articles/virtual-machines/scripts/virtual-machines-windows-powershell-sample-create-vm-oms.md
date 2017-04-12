---
title: "Azure PowerShell のサンプル スクリプト - OMS | Microsoft Docs"
description: "Azure PowerShell のサンプル スクリプト - OMS"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 03/14/2017
ms.author: nepeters
translationtype: Human Translation
ms.sourcegitcommit: 6ea03adaabc1cd9e62aa91d4237481d8330704a1
ms.openlocfilehash: e02754627c4247e41e7a88c07ac58d2f93c3d927
ms.lasthandoff: 04/06/2017

---

# <a name="create-an-operations-management-suite-monitored-vm-with-powershell"></a>PowerShell を使用して Operations Management Suite によって監視される VM を作成する

このスクリプトでは、Azure 仮想マシンを作成し、Operations Management Suite (OMS) エージェントをインストールして、システムを OMS ワークスペースに登録します。 このスクリプトを実行すると、仮想マシンが OMS コンソールに表示されるようになります。 また、OMS ワークスペース ID とワークスペース キーを更新する必要があります。

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>サンプル スクリプト

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-vm-monitor-oms/create-windows-vm-detailed-oms.ps1 "VM OMS を作成する")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ 

次のコマンドを実行して、リソース グループ、VM、すべての関連リソースを削除します。

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、以下のコマンドを実行してデプロイを作成します。 表内の各項目は、コマンドごとのドキュメントにリンクされています。

| コマンド | メモ |
|---|---|
| [New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/resourcemanager/azurerm.resources/v3.2.0/new-azurermresourcegroup) | すべてのリソースを格納するリソース グループを作成します。 |
| [New-AzureRmVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/resourcemanager/azurerm.network/v2.1.0/new-azurermvirtualnetworksubnetconfig) | サブネット構成を作成します。 この構成は、仮想ネットワークの作成プロセスで使用されます。 |
| [New-AzureRmVirtualNetwork](https://docs.microsoft.com/powershell/resourcemanager/azurerm.network/v1.0.13/new-azurermvirtualnetwork) | 仮想ネットワークを作成します。 |
| [New-AzureRmPublicIpAddress](https://docs.microsoft.com/powershell/resourcemanager/azurerm.network/v2.1.0/new-azurermpublicipaddress) | パブリック IP アドレスを作成します。 |
| [New-AzureRmNetworkSecurityRuleConfig](https://docs.microsoft.com/powershell/resourcemanager/azurerm.network/v2.1.0/new-azurermnetworksecurityruleconfig) | ネットワーク セキュリティ グループ規則の構成を作成します。 この構成は、NSG の作成時に NSG 規則を作成するために使用されます。 |
| [New-AzureRmNetworkSecurityGroup](https://docs.microsoft.com/powershell/resourcemanager/azurerm.network/v3.1.0/new-azurermnetworksecuritygroup) | ネットワーク セキュリティ グループを作成します。 |
| [Get-AzureRmVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/resourcemanager/azurerm.network/v1.0.13/get-azurermvirtualnetworksubnetconfig) | サブネット情報を取得します。 この情報は、ネットワーク インターフェイスを作成するときに使用されます。 |
| [New-AzureRmNetworkInterface](https://docs.microsoft.com/powershell/resourcemanager/azurerm.network/v3.4.0/new-azurermnetworkinterface) | ネットワーク インターフェイスを作成します。 |
| [New-AzureRmVMConfig](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v1.3.4/new-azurermvmconfig) | VM 構成を作成します。 この構成には、VM 名、オペレーティング システム、管理資格情報などの情報が含まれます。 この構成は、VM の作成時に使用されます。 |
| [New-AzureRmVM](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v1.3.4/new-azurermvm) | 仮想マシンを作成します。 |
| [Set-AzureRmVMExtension](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.2.0/set-azurermvmextension) | 仮想マシンに VM 拡張機能を追加します。 このサンプルでは、Operations Management Suite エージェント拡張機能を使用して、OMS エージェントをインストールし OMS ワークスペースに VM を登録します。 |
|[Remove-AzureRmResourceGroup](https://docs.microsoft.com/powershell/resourcemanager/azurerm.resources/v3.5.0/remove-azurermresourcegroup) | リソース グループと、それに含まれているすべてのリソースを削除します。 |

## <a name="next-steps"></a>次のステップ

Azure PowerShell モジュールの詳細については、[Azure PowerShell のドキュメント](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/)を参照してください。

その他の仮想マシン用の PowerShell サンプル スクリプトは、[Azure Windows VM のドキュメント](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)にあります。

