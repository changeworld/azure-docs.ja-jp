---
title: Azure PowerShell のサンプル - 単一ゾーン スケール セット | Microsoft Docs
description: Azure PowerShell のサンプル
services: virtual-machine-scale-sets
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 4e4f6d26c119a113bab0220828c847f438e7bc7a
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2018
---
# <a name="create-a-single-zone-virtual-machine-scale-set-with-powershell"></a>PowerShell を使用して単一ゾーン仮想マシン スケール セットを作成する
このスクリプトでは、単一可用性ゾーンで Windows Server 2016 を実行する仮想マシン スケール セットを作成します。 スクリプトを実行すると、RDP 経由で仮想マシンにアクセスできるようになります。

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>サンプル スクリプト
[!code-powershell[main](../../../powershell_scripts/virtual-machine-scale-sets/create-single-availability-zone/create-single-availability-zone.ps1 "Create single-zone scale set")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ
次のコマンドを実行して、リソース グループ、スケール セット、すべての関連リソースを削除します。

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>スクリプトの説明
このスクリプトでは、以下のコマンドを実行してデプロイを作成します。 表内の各項目は、コマンドごとのドキュメントにリンクされています。

| コマンド | メモ |
|---|---|
| [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | すべてのリソースを格納するリソース グループを作成します。 |
| [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) | 仮想ネットワーク サブネットを定義する構成オブジェクトを作成します。 |
| [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) | 仮想ネットワークとサブネットを作成します。 |
| [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress) | 静的パブリック IP アドレスを作成します。 |
| [New-AzureRmLoadBalancerFrontendIpConfig](/powershell/module/azurerm.network/new-azurermloadbalancerfrontendipconfig) | パブリック IP アドレスなど、ロード バランサーのフロントエンドを定義する構成オブジェクトを作成します。 |
| [New-AzureRmLoadBalancerBackendAddressPoolConfig](/powershell/module/azurerm.network/new-azurermloadbalancerbackendaddresspoolconfig) | ロード バランサーのバックエンドを定義する構成オブジェクトを作成します。 |
| [New-AzureRmLoadBalancer](/powershell/module/azurerm.network/new-azurermloadbalancer) | フロントエンドおよびバックエンド構成オブジェクトに基づいて、ロード バランサーを作成します。 |
| [Add-AzureRmLoadBalancerProbeConfig](/powershell/module/azurerm.network/new-azurermloadbalancerprobeconfig) | TCP ポート 80 のトラフィックを監視するために、ロード バランサーの正常性プローブを作成します。 15 秒間隔で 2 回連続して失敗した場合、エンドポイントは異常と判断されます。 |
| [Add-AzureRmLoadBalancerRuleConfig](/powershell/module/azurerm.network/new-azurermloadbalancerruleconfig) | フロントエンド プールからバックエンド プールへの TCP ポート 80 でのトラフィックを分散するロード バランサー規則を定義する構成オブジェクトを作成します。 |
| [Set-AzureRmLoadBalancer](/powershell/module/azurerm.network/set-azurermloadbalancer) | 正常性プローブとロード バランサー規則でロード バランサーを更新します。 |
| [New-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/new-azurermnetworksecurityruleconfig) | TCP ポート 80 のトラフィックを許可するようにネットワーク セキュリティ グループの規則を定義する構成オブジェクトを作成します。 |
| [New-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup) | ネットワーク セキュリティ グループと規則を作成します。 |
| [Set-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig) | ネットワーク セキュリティ グループに関連付けられるように仮想ネットワーク サブネットを更新します。 サブネットに接続されているすべての VM インスタンスは、ネットワーク セキュリティ グループ規則を継承します。 |
| [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/set-azurermvirtualnetwork) | サブネットおよびネットワーク セキュリティ グループ構成で仮想ネットワークを更新します。 |
| [New-AzureRmVmssIpConfig](/powershell/module/azurerm.compute/new-azurermvmssipconfig) | VM インスタンスをロード バランサーのバックエンド プールと受信 NAT プールに接続するための仮想マシン スケール セットの IP アドレス情報を定義する構成オブジェクトを作成します。
| [New-AzureRmVmssConfig](/powershell/module/azurerm.compute/new-azurermvmssconfig) | スケール セット内の VM インスタンスの数、VM インスタンスのサイズ、およびアップグレード ポリシー モードを定義する構成オブジェクトを作成します。 |
| [Set-AzureRmVmssStorageProfile](/powershell/module/azurerm.compute/set-azurermvmssstorageprofile) | VM インスタンスのために使用する VM イメージを定義する構成オブジェクトを作成します。 |
| [Set-AzureRmVmssOsProfile](/powershell/module/azurerm.compute/set-azurermvmssosprofile) | VM インスタンス名とユーザー資格情報を定義する構成プロファイルを作成します。 |
| [Add-AzureRmVmssNetworkInterfaceConfiguration](/powershell/module/azurerm.compute/add-azurermvmssnetworkinterfaceconfiguration) | 各 VM インスタンスの仮想ネットワーク インターフェイス カードを定義する構成オブジェクトを作成します。 |
| [New-AzureRmVmss](/powershell/module/azurerm.compute/new-azurermvmss) | すべての構成オブジェクトを結合して、仮想マシン スケール セットを作成します。 |
| [Get-AzureRmVmss](/powershell/module/azurerm.compute/get-azurermvmss) | 仮想マシン スケール セットに関する情報を取得します。 |
| [Add-AzureRmVmssExtension](/powershell/module/azurerm.compute/add-azurermvmssextension) | 基本的な Web アプリケーションをインストールするために、カスタム スクリプト用の VM 拡張機能を追加します。 |
| [Update-AzureRmVmss](/powershell/module/azurerm.compute/update-azurermvmss) | VM 拡張機能を適用するために、仮想マシン スケール セット モデルを更新します。 |
| [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) | ロード バランサーによって使用される、割り当てられたパブリック IP アドレスについての情報を取得します。 |
|[Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | リソース グループと、それに含まれているすべてのリソースを削除します。 |

## <a name="next-steps"></a>次の手順
Azure PowerShell モジュールの詳細については、[Azure PowerShell のドキュメント](/powershell/azure/overview)を参照してください。

その他の仮想マシン スケール セット用の PowerShell サンプル スクリプトは、[Azure 仮想マシン スケール セットのドキュメント](../powershell-samples.md)にあります。