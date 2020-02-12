---
title: Azure 予約の推奨事項を作成する方法
description: 仮想マシンに関する Azure 予約の推奨事項を作成する方法について説明します。
author: banders
ms.reviewer: yashar
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/28/2020
ms.author: banders
ms.openlocfilehash: 2f7b09c14553fdb5d642080d286ce123176b997f
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/04/2020
ms.locfileid: "76991050"
---
# <a name="how-reservation-recommendations-are-created"></a>予約の推奨事項を作成する方法
Azure 予約インスタンス (RI) の購入に関する推奨事項は、Azure Consumption [Reservation Recommendation API](/rest/api/consumption/reservationrecommendations)、[Azure Advisor](../..//advisor/advisor-cost-recommendations.md#buy-reserved-virtual-machine-instances-to-save-money-over-pay-as-you-go-costs)、Azure portal の予約購入エクスペリエンスを通じて提供されます。

次の手順では、推奨事項の計算方法を定義しています。 
1. 推奨エンジンが、指定されたスコープにおけるリソースの時間単位の使用量を、過去 7 日、30 日、60 日にわたって評価します。
2. エンジンが、予約の有無にかかわらず、使用状況データに基づいてコストをシミュレートします。
3. コストはさまざまな数量に対してシミュレートされ、節約の額が最も大きくなる数量が推奨されます。
4. ご自分のリソースが定期的にシャットダウンされる場合、シミュレーションでは節約の額が特定されず、購入に関する推奨事項は提供されません。

## <a name="recommendations-in-azure-advisor"></a>Azure Advisor における推奨事項
仮想マシンに関する予約購入の推奨事項は、Azure Advisor で提供されます。 以下の点に注意してください。 
- Advisor で提供されるのは、単一サブスクリプションのスコープでの推奨事項のみです。
- Advisor では、30 日間のルックバック期間で計算される推奨事項が提供されます。
- 共有スコープの予約を購入した場合、Advisor の予約購入に関する推奨事項が非表示になるまでに最大で 30 日間かかることがあります。

## <a name="other-expected-api-behavior"></a>API の予想されるその他の動作
- 7 日間のルックバック期間を使用する場合は、VM が 1 日以上シャットダウンすると、推奨事項を取得できないことがあります。

## <a name="next-steps"></a>次のステップ
- [Azure の予約割引が仮想マシンに適用されるしくみ](../manage/understand-vm-reservation-charges.md)について説明します。
