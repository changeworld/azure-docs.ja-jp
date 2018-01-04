---
title: "Azure Linux VM のサイズ - GPU | Microsoft Docs"
description: "Azure の Linux 仮想マシンで使用できるさまざまな GPU 最適化済みのサイズを一覧表示します。 このシリーズのストレージのスループットとネットワーク帯域幅に加え、vCPU、データ ディスク、NIC の数に関する情報を一覧表示します。"
services: virtual-machines-linux
documentationcenter: 
author: jonbeck7
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 12/14/2017
ms.author: jonbeck
ms.openlocfilehash: 9e8b682d8d7e409f97144d344ec58e710327df7a
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/16/2017
---
# <a name="gpu-optimized-virtual-machine-sizes"></a>GPU 最適化済み仮想マシンのサイズ

[!INCLUDE [virtual-machines-common-sizes-gpu](../../../includes/virtual-machines-common-sizes-gpu.md)]


[!INCLUDE [virtual-machines-common-sizes-table-defs](../../../includes/virtual-machines-common-sizes-table-defs.md)]

[!INCLUDE [virtual-machines-n-series-linux-support](../../../includes/virtual-machines-n-series-linux-support.md)]

ドライバーのインストールと検証手順については、「[Linux を実行している N シリーズ VM に NVIDIA GPU ドライバーをインストールする](n-series-driver-setup.md)」を参照してください。

[!INCLUDE [virtual-machines-n-series-considerations](../../../includes/virtual-machines-n-series-considerations.md)]

* X サーバーや、`Nouveau` ドライバーを使用するその他のシステムを Ubuntu NC VM にインストールしないでください。 NVIDIA GPU ドライバーをインストールする前に、`Nouveau` ドライバーを無効にする必要があります。  

## <a name="other-sizes"></a>その他のサイズ
- [汎用](sizes-general.md)
- [コンピューティングの最適化](sizes-compute.md)
- [メモリの最適化](sizes-memory.md)
- [ストレージの最適化](sizes-storage.md)
- [ハイ パフォーマンス コンピューティング](sizes-hpc.md)

## <a name="next-steps"></a>次の手順
[Azure コンピューティング ユニット (ACU)](acu.md) を確認することで、Azure SKU 全体の処理性能を比較できます。