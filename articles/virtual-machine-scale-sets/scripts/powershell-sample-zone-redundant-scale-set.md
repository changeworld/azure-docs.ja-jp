---
title: Azure PowerShell サンプル - ゾーン冗長スケール セット
description: このスクリプトでは、複数の可用性ゾーンにわたって Windows Server 2016 を実行する仮想マシン スケール セットを作成します。
author: cynthn
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.topic: sample
ms.date: 04/05/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 996701f554858a503d5b8fe8a8e2711c7f968c2e
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/19/2020
ms.locfileid: "76276452"
---
# <a name="create-a-zone-redundant-virtual-machine-scale-set-with-powershell"></a>PowerShell を使用してゾーン冗長仮想マシン スケール セットを作成する
このスクリプトでは、複数の可用性ゾーンにわたって Windows Server 2016 を実行する仮想マシン スケール セットを作成します。 スクリプトを実行すると、RDP 経由で仮想マシンにアクセスできるようになります。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="sample-script"></a>サンプル スクリプト

[!code-powershell[main](../../../powershell_scripts/virtual-machine-scale-sets/create-zone-redundant-scale-set/create-zone-redundant-scale-set.ps1 "Create zone-redundant scale set")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ
次のコマンドを実行して、リソース グループ、スケール セット、すべての関連リソースを削除します。

```powershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>スクリプトの説明
このスクリプトでは、以下のコマンドを実行してデプロイを作成します。 表内の各項目は、コマンドごとのドキュメントにリンクされています。

| command | メモ |
|---|---|
| [New-AzVmss](/powershell/module/az.compute/new-azvmss) | 仮想マシン スケール セットと、仮想ネットワーク、ロード バランサー、NAT 規則などのすべてのサポート リソースを作成します。 |
| [Get-AzVmss](/powershell/module/az.compute/get-azvmss) | 仮想マシン スケール セットに関する情報を取得します。 |
| [Add-AzVmssExtension](/powershell/module/az.compute/add-azvmssextension) | 基本的な Web アプリケーションをインストールするために、カスタム スクリプト用の VM 拡張機能を追加します。 |
| [Update-AzVmss](/powershell/module/az.compute/update-azvmss) | VM 拡張機能を適用するために、仮想マシン スケール セット モデルを更新します。 |
| [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) | ロード バランサーによって使用される、割り当てられたパブリック IP アドレスについての情報を取得します。 |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | リソース グループと、それに含まれているすべてのリソースを削除します。 |


## <a name="next-steps"></a>次のステップ
Azure PowerShell モジュールの詳細については、[Azure PowerShell のドキュメント](/powershell/azure/overview)を参照してください。

その他の仮想マシン スケール セット用の PowerShell サンプル スクリプトは、[Azure 仮想マシン スケール セットのドキュメント](../powershell-samples.md)にあります。
