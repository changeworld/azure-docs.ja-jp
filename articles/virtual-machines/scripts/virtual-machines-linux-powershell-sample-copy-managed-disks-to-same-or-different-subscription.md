---
title: マネージド ディスクをサブスクリプションにコピーする - PowerShell のサンプル
description: Azure PowerShell サンプル スクリプト - マネージド ディスクを同じまたは別のサブスクリプションにコピーまたは移動する
services: virtual-machines-linux
documentationcenter: storage
author: ramankumarlive
manager: kavithag
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/06/2017
ms.author: ramankum
ms.openlocfilehash: 35890d7b19451aa8051618871c9ca485aabca2c9
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2020
ms.locfileid: "81458524"
---
# <a name="copy-managed-disks-in-the-same-subscription-or-different-subscription-with-powershell"></a>PowerShell でマネージド ディスクを同じサブスクリプションまたは別のサブスクリプションにコピーする

このスクリプトは、既存のマネージド ディスクのコピーを同じサブスクリプションまたは別のサブスクリプションに作成します。 新しいディスクは、親のマネージド ディスクと同じリージョンに作成されます。

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 

## <a name="sample-script"></a>サンプル スクリプト

[!code-powershell[main](../../../powershell_scripts/virtual-machine/copy-managed-disks-to-same-or-different-subscription/copy-managed-disks-to-same-or-different-subscription.ps1 "Copy managed disk")]

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、コピー元のマネージド ディスクの ID を使ってコピー先のサブスクリプションに新しいマネージド ディスクを作成します。そのために、以下のコマンドが使われています。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| command | メモ |
|---|---|
| [New-AzDiskConfig](https://docs.microsoft.com/powershell/module/az.compute/New-AzDiskConfig) | ディスクの作成に使用するディスク構成を作成します。 この構成には、親ディスクのリソース ID および親ディスクと同じ場所が含まれます。  |
| [New-AzDisk](https://docs.microsoft.com/powershell/module/az.compute/New-AzDisk) | パラメーターとして渡されたディスク構成、ディスク名、リソース グループ名を使ってディスクを作成します。 |

## <a name="next-steps"></a>次のステップ

Azure PowerShell モジュールの詳細については、[Azure PowerShell のドキュメント](/powershell/azure/overview)を参照してください。

その他の仮想マシン用の PowerShell サンプル スクリプトは、[Azure Linux VM のドキュメント](../linux/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)にあります。