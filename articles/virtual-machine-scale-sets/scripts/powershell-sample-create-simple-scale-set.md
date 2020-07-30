---
title: Azure PowerShell のサンプル - 基本的な仮想マシン スケール セットを作成する
description: このスクリプトでは、RDP を使用してアクセスできる、Windows Server 2016 を実行する Azure 仮想マシン スケール セットを作成します。
author: mimckitt
ms.author: mimckitt
ms.topic: sample
ms.service: virtual-machine-scale-sets
ms.subservice: powershell
ms.date: 03/27/2018
ms.reviewer: jushiman
ms.custom: mimckitt
ms.openlocfilehash: 1093d6df3bef052df2dbe6df04f52d6a119357db
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2020
ms.locfileid: "87087086"
---
# <a name="create-a-basic-virtual-machine-scale-set-with-powershell"></a>PowerShell を使用して基本的な仮想マシン スケール セットを作成する
このスクリプトは、Windows Server 2016 を実行する仮想マシン スケール セットを作成します。 スクリプトを実行すると、RDP で VM インスタンスにアクセスできるようになります。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="sample-script"></a>サンプル スクリプト


[!code-powershell[main](../../../powershell_scripts/virtual-machine-scale-sets/simple-scale-set/simple-scale-set.ps1 "Create a simple virtual machine scale set")]

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
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | リソース グループと、それに含まれているすべてのリソースを削除します。 |

## <a name="next-steps"></a>次のステップ
Azure PowerShell モジュールの詳細については、[Azure PowerShell のドキュメント](/powershell/azure/)を参照してください。
