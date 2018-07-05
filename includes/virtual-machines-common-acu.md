---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 03/09/2018
ms.author: cynthn;davberg
ms.custom: include file
ms.openlocfilehash: 8f2cc3ce7e19a71422d3087bd78f8176bbff7364
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2018
ms.locfileid: "36338307"
---
Azure コンピューティング ユニット (ACU) の概念は、Azure SKU 間で計算 (CPU) パフォーマンスを比較する手段を提供します。 これは、パフォーマンス ニーズを満たす可能性が最も高い SKU を簡単に見つけるのに役立ちます。  現在、ACU は小さい (Standard_A1) VM を 100 として標準化されており、他のすべての SKU についてはその SKU が標準ベンチマークをそれよりどれくらい速く実行できるかが表されます。 

> [!IMPORTANT]
> ACU はガイドラインに過ぎません。  ワークロードの結果は異なる場合があります。 
> 
> 

<br>

| SKU ファミリ | ACU \ vCPU | vCPU: コア |
| --- | --- |---|
| [A0](../articles/virtual-machines/windows/sizes-general.md) |50 | 1:1 |
| [A1 ～ A4](../articles/virtual-machines/windows/sizes-general.md) |100 | 1:1 |
| [A5 ～ A7](../articles/virtual-machines/windows/sizes-general.md) |100 | 1:1 |
| [A1_v2 ～ A8_v2](../articles/virtual-machines/windows/sizes-general.md) |100 | 1:1 |
| [A2m_v2 ～ A8m_v2](../articles/virtual-machines/windows/sizes-general.md) |100 | 1:1 |
| [A8 ～ A11](../articles/virtual-machines/windows/sizes-hpc.md) |225* | 1:1 |
| [D1 ～ D14](../articles/virtual-machines/windows/sizes-general.md) |160 | 1:1 |
| [D1_v2 ～ D15_v2](../articles/virtual-machines/windows/sizes-general.md) |210 - 250* | 1:1 |
| [DS1 ～ DS14](../articles/virtual-machines/virtual-machines-windows-sizes-memory.md) |160 | 1:1 |
| [DS1_v2 ～ DS15_v2](../articles/virtual-machines/virtual-machines-windows-sizes-memory.md) |210-250* | 1:1 |
| [D_v3](../articles/virtual-machines/virtual-machines-windows-sizes-general.md) |160-190* | 2:1\*\* |
| [Ds_v3](../articles/virtual-machines/virtual-machines-windows-sizes-general.md) |160-190* | 2:1\*\* |
| [E_v3](../articles/virtual-machines/virtual-machines-windows-sizes-memory.md) |160-190* | 2:1\*\* |
| [Es_v3](../articles/virtual-machines/virtual-machines-windows-sizes-memory.md) |160-190* | 2:1\*\* |
| [F2s_v2-F72s_v2](../articles/virtual-machines/windows/sizes-compute.md) |195-210* | 2:1\*\* |
| [F1 ～ F16](../articles/virtual-machines/windows/sizes-compute.md) |210-250* | 1:1 |
| [F1s ～ F16s](../articles/virtual-machines/windows/sizes-compute.md) |210-250* | 1:1 |
| [G1 ～ G5](../articles/virtual-machines/virtual-machines-windows-sizes-memory.md) |180 ～ 240* | 1:1 |
| [GS1 ～ GS5](../articles/virtual-machines/virtual-machines-windows-sizes-memory.md) |180 ～ 240* | 1:1 |
| [H](../articles/virtual-machines/windows/sizes-hpc.md) |290 ～ 300* | 1:1 |
| [L4s ～ L32s](../articles/virtual-machines/windows/sizes-storage.md) |180 ～ 240* | 1:1 |
| [M](../articles/virtual-machines/virtual-machines-windows-sizes-memory.md) | 160-180 | 2:1\*\* |

\* が付いている ACU は、Intel® Turbo テクノロジを使用して CPU 周波数を上げ、パフォーマンスを増強します。  増強量は、VM のサイズ、ワークロード、および同じホストで実行されている他のワークロードによって変化します。

** ハイパー スレッドです。 
