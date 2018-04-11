---
title: Azure Monitor での使用量と推定コストの監視 | Microsoft Docs
description: Azure Monitor の [使用量と推定コスト] ページの利用プロセスの概要
author: dalekoetke
manager: carmonmills
editor: mrbullwinkle
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/02/2018
ms.author: Dale.Koetke;mbullwin
ms.openlocfilehash: f87705ebbdd14b1bbf7cade481a7dbe7dd3d5131
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/03/2018
---
# <a name="monitoring-usage-and-estimated-costs"></a>使用量と推定コストの監視

Azure Portal の [監視] ハブでは、**[使用量と推定コスト]** ページで、[アラート、メトリック、通知](https://azure.microsoft.com/pricing/details/monitor/)[Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/)、[Application Insights](https://azure.microsoft.com/pricing/details/application-insights/) などの中心的な監視機能の使用量を理解できるように設計されています。 2018 年 4 月より前の料金プランを利用しているお客様の場合は、Insights と Analytics 経由で購入した Log Analytics の使用量も含まれています。

このページでは、これらのリソースのサブスクリプションごとに集計された過去 31 日間の使用量を表示でき、この期間中の使用量の傾向も確認できます。 集計して推定を行うために必要なデータが大量にあるため、ページが完全に読み込まれるまでしばらくお待ちください。
監視された使用量とその推定コストの表示例を次に示します。

![使用量と推定コストを表示しているポータルのスクリーンショット](./media/monitoring-usage-and-estimated-costs/001.png)

[月間使用量] 列にあるリンクをクリックすると、過去 31 日間にわたる使用量の傾向を示すグラフが表示されます。

![ノードごとに含まれる 671.47 GB を示しているスクリーンショット](./media/monitoring-usage-and-estimated-costs/002.png)

次に示すのは同じような使用量とコストの概要ですが、これは 2018 年 4 月から適用される新しい使用量ベースの価格モデルを使用しているサブスクリプションのものです。 ノード ベースの課金がなくなり、Log Analytics と Application Insights のデータの取り込みと保持が新しい共通メーターで報告されるようになっていることに注意してください。

![使用量と推定コストを表示しているポータルのスクリーンショット](./media/monitoring-usage-and-estimated-costs/003.png)

## <a name="new-pricing-model"></a>新しい価格モデル

2018 年 4 月に新しい監視の価格モデルがリリースされました。  これは、クラウドに適した使用量に基づく料金であることを特徴としています。 ノード ベースに支払うのではなく、使用した分だけ支払います。 新しい価格モデルは、[アラート、メトリック、通知](https://azure.microsoft.com/pricing/details/monitor/)、[Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/)、および [Application Insights](https://azure.microsoft.com/pricing/details/application-insights/) で利用できます。

2018 年 4 月 2 日より後に Log Analytics または Application Insights の利用を開始するお客様は、この新しい料金プランのみを選択できます。 これらのサービスを既に利用しているお客様は、新しい価格モデルに移行するかどうかを選択できます。

## <a name="assessing-the-impact-of-the-new-pricing-model"></a>新しい価格モデルの影響の評価

新しい価格モデルがお客様に与える影響は、お客様の監視の使用パターンによって異なります。 2018 年 4 月 2 日より前に既に Log Analytics または Application Insights を利用しているお客様は、Azure Monitor の **[使用量と推定コスト]** ページで、新しい価格モデルに移行した場合のコストの変動の推定と、サブスクリプションの新しいモデルへの移行を実行できます。 ほとんどのお客様にとって新しい価格モデルのほうがメリットがありますが、これは、データの使用量が非常に多いお客様やコストが高いリージョンのお客様には当てはまらないことがあります。

**[使用量と推定コスト]** ページで選択したサブスクリプションのコストの推定値を確認するには、ページの上部近くにある青いバナーをクリックします。 新しい価格モデルを採用できるレベルはサブスクリプションであるため、この操作は 1 度に 1 つのサブスクリプションに対して実行することをお勧めします。

![価格モデル選択のスクリーン ショット](./media/monitoring-usage-and-estimated-costs/004.png)

次に、似たようなページが表示されますが、このページには緑色のバナーが表示されています。

![価格モデル選択のスクリーン ショット](./media/monitoring-usage-and-estimated-costs/005.png)

ここには、新しい価格モデルに対応する別のメーター セットが表示されます。 たとえば、次のようなデータ インジェスト メーターがあります。

1. Insight and Analytics\ノードごとの超過分
2. Insight and Analytics\ノードごとに含まれる
3. Application Insights\Basic 超過データ
4. Application Insights\含まれるデータ

新しい価格モデルにはノード ベースで含まれるデータの割り当てがないため、これらは、**共有サービス\データ インジェスト**という名前の新しい共通データ インジェスト メーターに結合されます。

確認できる別の変更として、コストが高いリージョンで Log Analytics または Application Insights に取り込まれたデータは、それを適切に反映するために、新しいリージョン メーター (たとえば **"データ インジェスト (米国中西部)"**) を使用して表示されます。

> [!NOTE]
> Operations Management Suite (OMS) サブスクリプションがある場合は、購入した各ノードの Log Analytics と Application Insights のデータ インジェストの割り当てが表示されます。 これは、サブスクリプションではなくアカウント レベルで適用されるため、この推定では、これらの割り当ての影響を示すことはできません。 この場合は、アカウント担当者と新しい価格モデルについて、詳しく検討してください。

## <a name="changes-when-moving-to-the-new-pricing-model"></a>新しい価格モデルに移行するときの変更

サブスクリプションを新しい価格モデルに移行すると、Log Analytics の価格レベルが新しい 1 GB あたりのレベルに変更されます (Azure Resource Manager では "pergb2018" と呼ばれます)。 この移行を行うと、Application Insights リソースの Enterprise プランが Basic プランに変更されます。 これらの影響は、上記で説明したコストの推定に反映されます。 

## <a name="moving-to-the-new-pricing-model"></a>新しい価格モデルへの移行

サブスクリプションに対して新しい価格モデルを採用する場合は、**[使用量と推定コスト]** ページの上部にある **[価格モデルの選択]** オプションをクリックします。

![Monitor の [使用量と推定コスト] ページの新しい価格モデル選択のスクリーンショット](./media/monitoring-usage-and-estimated-costs/006.png)

**[価格モデルの選択]** ページが開き、前のページに表示されていたサブスクリプションが表示されます。

![価格モデル選択のスクリーン ショット](./media/monitoring-usage-and-estimated-costs/007.png)

サブスクリプションを新しい価格モデルに移行するには、チェックボックスをオンにし、**[保存]** をクリックします。  同じ方法で、古い価格モデルに戻すことができます。 価格モデルを変更するには、サブスクリプションの所有者または共同作成者のアクセス許可が必要であることに注意してください。