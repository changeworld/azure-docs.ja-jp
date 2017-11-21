---
title: "Azure Linux VM のサイズ - GPU | Microsoft Docs"
description: "Azure の Linux 仮想マシンで使用できるさまざまな GPU 最適化済みのサイズを一覧表示します。"
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
ms.date: 11/08/2017
ms.author: jonbeck
ms.openlocfilehash: 645f69e71c5a13bb70edabfd22f51ed5df619693
ms.sourcegitcommit: 93902ffcb7c8550dcb65a2a5e711919bd1d09df9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/09/2017
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

## <a name="next-steps"></a>次のステップ
[Azure コンピューティング ユニット (ACU)](acu.md) を確認することで、Azure SKU 全体の処理性能を比較できます。