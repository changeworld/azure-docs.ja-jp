---
title: インクルード ファイル
description: インクルード ファイル
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: include
ms.date: 04/05/2021
ms.author: duau
ms.custom: include file
ms.openlocfilehash: 27f5755ce8b7d204cad6cdc2281d7992bf86615a
ms.sourcegitcommit: c2a41648315a95aa6340e67e600a52801af69ec7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2021
ms.locfileid: "106504697"
---
仮想ネットワーク ゲートウェイを作成する場合、使用するゲートウェイの SKU を指定する必要があります。 選択するゲートウェイ SKU が上位になるほど、ゲートウェイに割り当てられる CPU やネットワーク帯域幅が増えます。その結果、ゲートウェイで仮想ネットワークに対してより高いネットワーク スループットをサポートできます。 

ExpressRoute の仮想ネットワーク ゲートウェイでは、次の SKU を使用できます。

|     | VPN Gateway と ExpressRoute の共存 | FastPath | 回線接続の最大数 |
| --- | --- | --- | --- |
| **Standard** | はい | いいえ | 4 |
| **HighPerformance** | はい | いいえ | 4 |
| **UltraPerformance** | はい | はい | 16 |