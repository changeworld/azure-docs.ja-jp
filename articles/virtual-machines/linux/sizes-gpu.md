---
title: "Azure Linux VM のサイズ - GPU | Microsoft Docs"
description: "Azure の Linux 仮想マシンで使用できるさまざまな GPU 最適化済みのサイズを一覧表示します。"
services: virtual-machines-linux
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/22/2017
ms.author: cynthn
ms.translationtype: Human Translation
ms.sourcegitcommit: e155891ff8dc736e2f7de1b95f07ff7b2d5d4e1b
ms.openlocfilehash: e0cd2a14b4102797024925a72783ecaf56919aec
ms.contentlocale: ja-jp
ms.lasthandoff: 05/02/2017


---

# <a name="gpu-linux-vm-sizes"></a>GPU Linux VM のサイズ

[!INCLUDE [virtual-machines-common-sizes-gpu](../../../includes/virtual-machines-common-sizes-gpu.md)]

## <a name="supported-operating-systems"></a>サポートされているオペレーティング システム

サポートされているオペレーティング システムとドライバーの要件については、[Linux 用の N シリーズ ドライバーのセットアップ](n-series-driver-setup.md)に関するページを参照してください。

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../../includes/virtual-machines-common-sizes-table-defs.md)]

[!INCLUDE [virtual-machines-n-series-considerations](../../../includes/virtual-machines-n-series-considerations.md)]

* X サーバーや、nouveau ドライバーを使用するその他のシステムを Ubuntu NC VM にインストールすることはお勧めしません。 NVIDIA GPU ドライバーをインストールする前に、nouveau ドライバーを無効にする必要があります。  

## <a name="other-sizes"></a>その他のサイズ
- [汎用](sizes-general.md)
- [コンピューティングの最適化](sizes-compute.md)
- [メモリの最適化](sizes-memory.md)
- [ストレージの最適化](sizes-storage.md)
- [ハイ パフォーマンス コンピューティング](sizes-hpc.md)

## <a name="next-steps"></a>次のステップ
[Azure コンピューティング ユニット (ACU)](acu.md) を確認することで、Azure SKU 全体の処理性能を比較できます。
