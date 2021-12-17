---
title: Azure 混乱 Studio サービスの制限
description: Azure 混乱 Studio のスロットルと使用制限について
author: johnkemnetz
ms.author: johnkem
ms.service: chaos-studio
ms.date: 11/01/2021
ms.topic: reference
ms.custom: ignite-fall-2021
ms.openlocfilehash: f9b5352597afb00b379363a2f151615a902dff8f
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131091662"
---
# <a name="azure-chaos-studio-service-limits"></a>Azure 混乱 Studio サービスの制限
この記事では、Azure 混乱 Studio のサービスの制限について説明します。 
## <a name="experiment-and-target-limits"></a>実験とターゲットの制限

混乱 Studio では、オブジェクトの数、アクティビティの期間、およびデータの保持期間に制限が適用されます。

| 制限 | 値 |
| -- | -- |
| 実験ごとのアクション数 | 9 |
| 実験ごとの分岐数 | 9 |
| 実験ごとのアクション数 | 4 |
| アクティブ化期間 (時間単位) | 12 |
| リージョンごとの同時実行実験とサブスクリプション | 5 |
| 合計実験期間 (時間) | 12 |
| リージョンごとの実験数とサブスクリプション | 500 |
| アクションごとのターゲットの数 | 50 |
| ターゲットごとのアクティブなエージェントの数 | 1,000 |
| リージョンおよびサブスクリプションごとのターゲットの数 | 10,000 |

## <a name="api-throttling-limits"></a>スロットルの制限

混乱 Studio は、すべての ARM 操作に制限を適用します。 制限に対して行われた要求は調整されます。 特に指定がない限り、すべての要求制限は5分間隔で適用されます。

| Operation | Requests |
| -- | -- |
| Microsoft.Chaos/experiments/write | 100 |
| Microsoft.Chaos/experiments/read | 300 |
| Microsoft.Chaos/experiments/delete | 100 |
| Microsoft.Chaos/experiments/start/action | 20 |
| Microsoft.Chaos/experiments/cancel/action | 100 |
| Microsoft.Chaos/experiments/statuses/Read | 100 |
| Microsoft.Chaos/experiments/executionDetails/Read | 100 |
| Microsoft の混乱/ターゲット/書き込み | 200 |
| Microsoft の混乱/ターゲット/書き込み | 600 |
| Microsoft の混乱/ターゲット/書き込み | 200 |
| Microsoft の混乱/ターゲット/機能/書き込み | 600 |
| Microsoft の混乱/ターゲット/機能/書き込み | 1800 |
| Microsoft の混乱/ターゲット/機能/書き込み | 600 |
| Microsoft.Chaos/locations/targetTypes/Read | 50 |
| Microsoft.Chaos/locations/targetTypes/capabilityTypes/Read | 50 |
