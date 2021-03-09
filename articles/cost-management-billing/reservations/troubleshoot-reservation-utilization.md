---
title: Azure 予約使用率のトラブルシューティング
description: この記事は、Azure portal で Azure 予約の使用率がゼロ (0) と表示される問題とそのトラブルシューティングについて説明します。 使用率が一致しない件についても説明します。
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: reservations
ms.author: banders
ms.reviewer: yashar
ms.topic: troubleshooting
ms.date: 10/19/2020
ms.openlocfilehash: 9d5706843e8131110566ad8f955415b6db29f7ba
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/19/2020
ms.locfileid: "92207436"
---
# <a name="troubleshoot-reservation-utilization"></a>予約使用率のトラブルシューティング

この記事は、Azure portal で Azure 予約の使用率がゼロ (0) と表示される問題とそのトラブルシューティングについて説明します。 使用率が一致しない件についても説明します。

## <a name="symptoms"></a>現象

1. [Azure portal](https://portal.azure.com) にサインインし、 **[予約]** に移動します。
1. 予約の一覧で、 **[使用率 (%)]** 列の予約の使用率を確認します。 場合により、0% と示されていることがあります。
1. 予約を選択します。
1. 予約の概要ページで、グラフの使用率が予約一覧に表示されている値と一致しない場合があります。

## <a name="cause"></a>原因

Azure portal の **[使用率 (%)]** 列には、現在の日付の値が表示されます。 値は、リソースが実行されている場所から使用状況データが到着したときに計算されます。 Azure では、この使用状況を使用して使用率が計算されます。

一部のリソースでは、他のリソースよりもゆっくりと使用状況が報告されます。 また、SQL データベースのような一部の製品の種類では、使用状況データを報告するのに時間がかかります。

待ち時間が原因で、使用率の計算で実際の使用量よりも低い値が表示されることがあります。 この違いは、日の境界で顕著になります。 このような場合、Azure では、4 から 8 時間の使用状況データが取得されないと、0% の値が計算されます。 使用状況データが到着しておらず、予約がどのリソースにもベネフィットを適用していないように見えるため、0% の値が表示されます。

使用状況データが到着すると、値は適切な割合に向かって変化します。 すべての使用状況データが収集されると、正しい値が求められ、グラフに正確に表示されます。

## <a name="solution"></a>解決策

使用率の値が想定と一致しない場合は、グラフを確認して、実際の使用状況を把握してください。 2 日以上前のすべてのポイント値は正確であるはずです。 7 日から 30 日の長期平均は正確であるはずです。

## <a name="next-steps"></a>次の手順

- 予約の管理の詳細については、「[Azure リソースに対する予約を管理する](manage-reserved-vm-instance.md)」を参照してください。