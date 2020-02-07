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
ms.openlocfilehash: a43e0c4d86bd47c953b50ab9fb1fd8df00e7df3d
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/31/2020
ms.locfileid: "76851353"
---
# <a name="how-reservation-recommendations-are-created"></a>予約の推奨事項を作成する方法

Azure 予約インスタンス (RI) の購入に関する推奨事項は、Azure Consumption [Reservation Recommendation API](/rest/api/consumption/reservationrecommendations) によって生成されます。 この API によって生成された推奨事項は、[Azure Advisor](../..//advisor/advisor-cost-recommendations.md#buy-reserved-virtual-machine-instances-to-save-money-over-pay-as-you-go-costs) でも使用されます。 Advisor は、Azure portal に推奨事項を表示します。

Azure で実行している VM がある場合、RI の割引価格と VM の前払いを利用できます。 Microsoft Consumption Recommendation API は、7 日、30 日、60 日間の使用量を評価して、RI の最適な構成を推奨します。 これは、RI で支払うコストに対して RI を購入しなかった場合に支払うコストを計算して、節約を最適化します。

Azure Advisor は、30 日間の使用状況に基づいた推奨事項を表示します。

わかりやすくするために、次の図では、7 日間の推奨事項の場合に発生する計算を示します。 30 日間または 60 日間の推奨事項を計算する場合も同じ方法が適用されます。

## <a name="calculation-method-example"></a>計算方法の例

特定の SKU とリージョンの 1 時間あたりの Windows VM 使用量は、7 日間 (168 時間) にわたって変動するものとします。 7 日間での最小使用量は 65 ユニットで、最大使用量は 127 ユニットです。 この例では、79 時間で 80 個の VM を使用し、75 個の RI を購入済みです。

75 個の予約インスタンスを購入した場合は、79 時間分として次のコストを支払います。

- 75 個の予約インスタンス。 このコストは、RI の購入時に前払いします。
- 予約インスタンスは、実行中の VM のハードウェア コストを対象とするため、75 時間分のソフトウェアのみの価格を支払います。
- 79 時間分の使用量は 80 であるため、Windows とハードウェアを合わせて 5 時間分のメーター価格を支払います。 この組み合わせ価格は、Enterprise Agreement (EA) または従量課金制の料金に基づきます。

75 個の RI を購入する場合、前の時間単位のコストを加算して合計コストを計算することができます。 また、自分のレートを使用して現在のコストを計算することができます。 この 2 つの金額の差が、この例の 7 日間の節約になります。

API は、特定の使用時点ごとに計算を実行します。 その後、API は、節約額が最大になる推奨数量を返します。 次の例のグラフは、節約のピークが 68 個であることを示しています。 その後は節約額が減少するため、API は 68 個を推奨します。

![節約のピークを示す図](./media/reserved-instance-purchase-recommendations/peak-savings.png)

## <a name="other-expected-api-behavior"></a>API の予想されるその他の動作

- API は、Windows の [Azure ハイブリッド特典](https://azure.microsoft.com/pricing/hybrid-benefit/)が利用される場合に、この特典で可能な節約を表示します。 特典が利用されない場合、API の推奨事項は、Windows のコア コストに基づきます。 利用可能な場合は、Azure ハイブリッド特典を利用して節約額を増やすことを検討してください。
- 7 日間のルックバック期間を使用する場合は、VM が 1 日以上シャットダウンすると、推奨事項を取得できないことがあります。

## <a name="next-steps"></a>次のステップ
- [Azure の予約割引が仮想マシンに適用されるしくみ](../manage/understand-vm-reservation-charges.md)について説明します。
