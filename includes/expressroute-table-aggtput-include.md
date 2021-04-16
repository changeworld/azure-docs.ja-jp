---
title: インクルード ファイル
description: インクルード ファイル
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: include
ms.date: 12/05/2019
ms.author: duau
ms.custom: include file
ms.openlocfilehash: b1465b9bf855d592d195277b616c860a23c179f6
ms.sourcegitcommit: c2a41648315a95aa6340e67e600a52801af69ec7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2021
ms.locfileid: "106504698"
---
| ゲートウェイ SKU | 1 秒あたりの接続数 | フローの数 | 1 秒あたりのメガビット数 | パケット数/秒 | 回線帯域幅 | ゲートウェイによってアドバタイズされる (MSEE への) ルートの数 | ゲートウェイによって学習される (MSEE からの) ルートの数 | 仮想ネットワーク内の VM の数 |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| **Basic SKU (非推奨)** | 該当なし | 該当なし | 500 | 該当なし | 該当なし | 該当なし | 該当なし | 該当なし |
| **Standard SKU/ErGw1AZ** | 7,000 | 400,000 | 1,000 超 | 100,000 超 | 1 Gbps |  500 | 4,000 | 2,000 (メンテナンス中に 1,000 まで減らし、後で復元します) | 
| **High Performance SKU/ErGw2AZ** | 14,000 | 840,000 | 2,000 超 | 250,000 | 1 Gbps | 500 | 9,500 以下 (仮想ネットワーク内に 6,500 を超える VM がある場合は、4,000 に減らします) | 4,500 |
| **Ultra Performance SKU/ErGw3AZ** | 16,000 | 950,000 | 10,000 以下 | 1,000,000 | 1 Gbps | 500 | 9,500 以下 | 11,000 |
