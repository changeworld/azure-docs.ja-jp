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
ms.date: 04/09/2018
ms.author: Dale.Koetke;mbullwin
ms.openlocfilehash: f25c39b602449be3ab9d1cd7e67d6fcfc78afb17
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/23/2018
---
# <a name="monitoring-usage-and-estimated-costs"></a>使用量と推定コストの監視

Azure Portal の [監視] ハブの **[使用量と推定コスト]** ページには、[アラート、メトリック、通知](https://azure.microsoft.com/pricing/details/monitor/)[Azure Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/)、[Azure Application Insights](https://azure.microsoft.com/pricing/details/application-insights/) などの中心的な監視機能の使用量が説明されています。 2018 年 4 月より前の価格プランを利用しているお客様の場合は、Insights と Analytics 経由で購入した Log Analytics の使用量も含まれています。

ユーザーは、サブスクリプションごとに集計された過去 31 日間のリソース使用量をこのページで確認できます。 ドリルインには、その 31 日間の使用傾向が表示されます。 この推定を行うためには大量のデータを集計する必要があるため、ページが完全に読み込まれるまでしばらくお待ちください。

監視された使用量とその推定コストの表示例を次に示します。

![使用量と推定コストを表示しているポータルのスクリーンショット](./media/monitoring-usage-and-estimated-costs/001.png)

[月間使用量] 列にあるリンクをクリックすると、過去 31 日間にわたる使用量の傾向を示すグラフが表示されます。

![[Included per node]\(ノードごとに含まれる\) の棒グラフのスクリーンショット](./media/monitoring-usage-and-estimated-costs/002.png)

次に示すのは同じような使用量とコストの概要です。 この例は 2018 年 4 月から適用される新しい使用量ベースの価格モデルを使用しているサブスクリプションを示しています。 ノード ベースの課金がなくなっていることに注意してください。 Log Analytics と Application Insights のデータの取り込みと保持が新しい共通メーターで報告されるようになりました。

![使用量と推定コストを表示しているポータルのスクリーンショット - 2018 年 4 月の価格](./media/monitoring-usage-and-estimated-costs/003.png)

## <a name="new-pricing-model"></a>新しい価格モデル

2018 年 4 月に新しい監視の価格モデルがリリースされました。 これは、クラウドに適した使用量に基づく料金であることを特徴としています。 ノード ベースに支払うのではなく、使用した分だけ支払います。 新しい価格モデルは、[アラート、メトリック、通知](https://azure.microsoft.com/pricing/details/monitor/)、[Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/)、および [Application Insights](https://azure.microsoft.com/pricing/details/application-insights/) で利用できます。

2018 年 4 月 2 日より後に Log Analytics または Application Insights の利用を開始するお客様は、この新しい料金プランのみを選択できます。 これらのサービスを既に利用しているお客様は、新しい価格モデルに移行するかどうかを選択できます。

## <a name="assessing-the-impact-of-the-new-pricing-model"></a>新しい価格モデルの影響の評価

新しい価格モデルがお客様に与える影響は、お客様の監視の使用パターンによって異なります。 2018 年 4 月 2 日より前に既に Log Analytics または Application Insights を利用しているお客様は、Azure Monitor の **[使用量と推定コスト]** ページで、新しい価格モデルに移行した場合のコストの変動の推定を確認できます。 サブスクリプションを新しいモデルに移行する方法が示されます。 新しい価格モデルにより、ほとんどのお客様は、メリットが得られることになるでしょう。 これは、データの使用量が非常に多いお客様やコストが高いリージョンのお客様には当てはまらない可能性があります。

**[使用量と推定コスト]** ページで選択したサブスクリプションのコストの推定値を確認するには、ページの上部近くにある青いバナーを選択します。 新しい価格モデルを採用できるレベルはサブスクリプションであるため、この操作は 1 度に 1 つのサブスクリプションに対して実行することをお勧めします。

![Monitor の [使用量と推定コスト] ページの新しい価格モデル選択のスクリーンショット](./media/monitoring-usage-and-estimated-costs/004.png)

新しいページは以前と似ていますが、緑色のバナーが表示されています。

![Monitor の [使用量と推定コスト] ページの現在の価格モデル選択のスクリーンショット](./media/monitoring-usage-and-estimated-costs/005.png)

また、メーターも異なります。このページには、新しい価格モデルに対応する一連のメーターが表示されます。 次に示したのはその例です。

- Insight and Analytics\ノードごとの超過分
- Insight and Analytics\ノードごとに含まれる
- Application Insights\Basic 超過データ
- Application Insights\含まれるデータ

新しい価格モデルにはノード ベースで含まれるデータの割り当てがありません。 そのため、これらのデータ インジェスト メーターは、**共有サービス\データ インジェスト**という名前の新しい共通データ インジェスト メーターに結合されます。 

コストが高いリージョンで Log Analytics または Application Insights に取り込まれたデータには、別の変更があります。 こうしたコストが高いリージョンのデータは、新しいリージョン メーターを使用して表示されます。 たとえば、"**データ インジェスト (米国中西部)**" のように表示されます。

> [!NOTE]
> サブスクリプションあたりの推定コストは、Operations Management Suite (OMS) サブスクリプションのアカウントレベルでのノード単位の権利には考慮されません。 この場合は、アカウント担当者と新しい価格モデルについて、詳しく検討してください。

## <a name="new-pricing-model-and-operations-management-suite-subscription-entitlements"></a>新しい価格モデルと Operations Management Suite のサブスクリプションの権利

Microsoft Operations Management Suite E1 および E2 を購入されたお客様は、[Log Analytics](https://www.microsoft.com/en-us/cloud-platform/operations-management-suite) と [Application Insights](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-pricing#the-price-plans) でのノード単位のデータ インジェストが可能です。 特定のサブスクリプションで Log Analytics ワークスペースまたは Application Insights リソースの権利を得るには、そのサブスクリプションの価格モデルが、2018 年 4 月以前の価格モデルである必要があります。 この場合は、Log Analytics "ノード単位 (OMS)" 価格レベルと Application Insights "Enterprise" 価格プランが利用できます。 お客様の組織が購入されたスイートのノード数によっては、一部のサブスクリプションを新しい価格格モデルへ移行した方が有利な場合があります。 ただし、その点については慎重に検討する必要があります。

## <a name="changes-when-youre-moving-to-the-new-pricing-model"></a>新しい価格モデルに移行するときの変更

サブスクリプションを新しい価格モデルに移行すると、Log Analytics の価格レベルが新しい 1 GB あたりのレベルに変更されます (Azure Resource Manager では "pergb2018" と呼ばれます)。 この移行を行うと、Application Insights リソースの Enterprise プランが Basic プランに変更されます。 コスト見積もりには、こうした変更の影響が示されます。

## <a name="moving-to-the-new-pricing-model"></a>新しい価格モデルへの移行

サブスクリプションに対して新しい価格モデルを採用する場合は、**[使用量と推定コスト]** ページの上部にある **[価格モデルの選択]** オプションを選択します。

![Monitor の [使用量と推定コスト] ページの新しい価格モデル選択のスクリーンショット](./media/monitoring-usage-and-estimated-costs/006.png)

**[価格モデルの選択]** ページが表示されます。 前のページで表示した各サブスクリプションが一覧表示されます。

![価格モデル選択のスクリーン ショット](./media/monitoring-usage-and-estimated-costs/007.png)

サブスクリプションを新しい価格モデルに移行するには、チェックボックスをオンにし、**[保存]** を選択します。 同じ方法で、古い価格モデルに戻すことができます。 価格モデルを変更するには、サブスクリプションの所有者または共同作成者のアクセス許可が必要であることに注意してください。
