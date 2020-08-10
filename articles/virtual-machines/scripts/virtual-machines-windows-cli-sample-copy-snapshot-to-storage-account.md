---
title: スナップショットを別のリージョンのストレージ アカウントにコピーする - Windows CLI サンプル
description: Azure CLI サンプル スクリプト - 同じまたは別のリージョンのストレージ アカウントに、スナップショットを VHD としてエクスポート/コピーします。
services: virtual-machines-windows
documentationcenter: storage
author: ramankumarlive
manager: kavithag
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/19/2017
ms.author: ramankum
ms.custom: mvc,seodec18
ms.openlocfilehash: 2bad4f5f3bb85f062d4c17eb2d9e77cb51ab2779
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/20/2020
ms.locfileid: "86501111"
---
# <a name="exportcopy-a-snapshot-to-a-storage-account-in-different-region-with-cli"></a>CLI を使用して別のリージョンのストレージ アカウントにスナップショットをエクスポート/コピーする

このスクリプトでは、管理対象のスナップショットを別のリージョンのストレージ アカウントにエクスポートします。 最初にスナップショットの SAS URI を生成し、それを使用して別のリージョンのストレージ アカウントにコピーします。 このスクリプトを使用して、別のリージョンでマネージド ディスクのバックアップを維持し、ディザスター リカバリーに備えます。

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>サンプル スクリプト

[!code-azurecli[main](../../../cli_scripts/virtual-machine/copy-snapshots-to-storage-account/copy-snapshots-to-storage-account.sh "Copy snapshot")]

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用して管理されているスナップショットの SAS URI を生成し、SAS URI を使用してスナップショットをストレージ アカウントにコピーします。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| command | メモ |
|---|---|
| [az snapshot grant-access](/cli/azure/snapshot) | 基になる VHD ファイルのストレージ アカウントへのコピー、またはそのオンプレミスへのダウンロードに使用する、読み取り専用の SAS を生成します  |
| [az storage blob copy start](/cli/azure/storage/blob/copy) | BLOB を、あるストレージ アカウントから別のストレージ アカウントに非同期的にコピーします |

## <a name="next-steps"></a>次のステップ

[VHD からマネージド ディスクを作成する](virtual-machines-windows-cli-sample-create-managed-disk-from-vhd.md?toc=%2fcli%2fmodule%2ftoc.json)

Azure CLI の詳細については、[Azure CLI のドキュメント](/cli/azure)のページをご覧ください。

その他の仮想マシンとマネージド ディスクの CLI サンプル スクリプトは、[Azure Windows VM のドキュメント](../windows/cli-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)にあります。
