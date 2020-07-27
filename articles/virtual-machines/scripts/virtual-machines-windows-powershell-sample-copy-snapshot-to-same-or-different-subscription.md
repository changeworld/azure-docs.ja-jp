---
title: マネージド ディスクのスナップショットをサブスクリプションにコピーする (Windows) - PowerShell
description: Azure PowerShell サンプル スクリプト - マネージド ディスクのスナップショットを同じまたは別のサブスクリプションにコピー (移動) する
services: virtual-machines-windows
documentationcenter: storage
author: ramankumarlive
manager: kavithag
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 02/28/2019
ms.author: ramankum
ms.openlocfilehash: 0dadb4102a16d63b028e7202e2d7a94f135d06bb
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/20/2020
ms.locfileid: "86501066"
---
# <a name="copy-snapshot-of-a-managed-disk-in-same-subscription-or-different-subscription-with-powershell-windows"></a>マネージド ディスクのスナップショットを PowerShell で同じサブスクリプションまたは別のサブスクリプションにコピーする (Windows)

このスクリプトは、同じまたは別のサブスクリプションに、マネージド ディスクのスナップショットをコピーします。 次のシナリオでこのスクリプトを使用します。

1. Premium ストレージ (Premium_LRS) のスナップショットを Standard ストレージ (Standard_LRS または Standard_ZRS) に移行してコストを削減する。
1. 信頼性が高い ZRS ストレージを利用するために、ローカル冗長ストレージ (Premium_LRS、Standard_LRS) からゾーンの冗長ストレージ (Standard_ZRS) にスナップショットを移行する。
1. 長期間の保存のために、スナップショットを同じリージョン内の別のサブスクリプションに移動する。

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 

## <a name="sample-script"></a>サンプル スクリプト

[!code-powershell[main](../../../powershell_scripts/virtual-machine/copy-snapshot-to-same-or-different-subscription/copy-snapshot-to-same-or-different-subscription.ps1 "Copy snapshot")]

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトは、コピー元スナップショットの ID を使って、コピー先のサブスクリプションにスナップショットを作成します。そのために、以下のコマンドが使われています。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| command | メモ |
|---|---|
| [New-AzSnapshotConfig](/powershell/module/az.compute/new-azsnapshotconfig) | スナップショットの作成に使用するスナップショット構成を作成します。 この構成には、親スナップショットのリソース ID および親スナップショットと同じ場所が含まれます。  |
| [New-AzSnapshot](/powershell/module/az.compute/new-azsnapshot) | パラメーターとして渡されたスナップショット構成、スナップショット名、リソース グループ名を使ってスナップショットを作成します。 |

## <a name="next-steps"></a>次のステップ

[スナップショットから仮想マシンを作成する](./virtual-machines-windows-powershell-sample-create-vm-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

Azure PowerShell モジュールの詳細については、[Azure PowerShell のドキュメント](/powershell/azure/overview)を参照してください。

その他の仮想マシン用の PowerShell サンプル スクリプトは、[Azure Windows VM のドキュメント](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)にあります。
