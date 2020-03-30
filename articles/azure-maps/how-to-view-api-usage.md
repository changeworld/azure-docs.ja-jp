---
title: Azure Maps API の使用状況メトリックを表示する | Microsoft Azure Maps
description: この記事では、Azure portal で Microsoft Azure Maps API の呼び出しに関するメトリックを表示する方法について説明します。
author: philmea
ms.author: philmea
ms.date: 08/06/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 0eb117af712b3b1f63a3f99c96cba9775f8e3996
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335162"
---
# <a name="view-azure-maps-api-usage-metrics"></a>Azure Maps API の使用状況メトリックを表示する

この記事では、[Azure portal](https://portal.azure.com) で Azure Maps アカウントにおける API 使用状況のメトリックを表示する方法について示します。 メトリックは、カスタマイズ可能な時間の長さに従って便利なグラフ形式で示されます。

## <a name="view-metric-snapshot"></a>メトリックのスナップショットを表示する

Maps アカウントの **[概要]** ページでいくつかの共通メトリックを表示できます。 現在、選択可能な時間の長さにわたって*要求の合計数*、*合計エラー数*、および*可用性*が表示されています。

![Azure Maps の使用状況メトリックの概要](media/how-to-view-api-usage/portal-overview.png)

特定の分析に対してこれらのグラフをカスタマイズする場合は、次のセクションに進みます。

## <a name="view-detailed-metrics"></a>詳細なメトリックを表示する

1. [ポータル](https://portal.azure.com)で Azure サブスクリプションにサインインします。

2. 左側にある **[すべてのリソース]** メニュー アイコンをクリックして、 *[Azure Maps アカウント]* に移動します。

3. ご利用の Maps アカウントが開いたら、左側の **[メトリック]** メニューをクリックします。

4. **[メトリック]** ウィンドウで、次のいずれかのオプションを選びます。

   1. **[可用性]** : 期間にわたって API 可用性の*平均*を示します。
   2. **[使用状況]** : 使用状況におけるご自分のアカウントの*割合*を示します。

      ![Azure Maps 使用状況メトリック ペイン](media/how-to-view-api-usage/portal-metrics.png)

5. 次に、 *[過去 24 時間] (自動)* をクリックして、 **[時間の範囲]** を選択できます。 既定では、時間の範囲は 24 時間に設定されています。 クリックすると、選択可能な時間の範囲がすべて表示されます。 また、 *[時間の粒度]* を選択したり、同じドロップダウン内の *[ローカル]* または *[GMT]* として時間を示すように選んだりすることができます。 **[Apply]** をクリックします。

    ![Azure Maps メトリックの時間の範囲](media/how-to-view-api-usage/time-range.png)

6. メトリックを追加すると、そのメトリックに関連するプロパティから**フィルターを追加**できます。 次に、グラフに反映して表示するプロパティの値を選択します。

    ![Azure Maps 使用状況メトリック フィルター](media/how-to-view-api-usage/filter.png)

7. また、選択したメトリック プロパティに基づいてメトリックに**分割を適用**することもできます。 これにより、グラフをそのプロパティの値ごとに、複数のグラフに分割できます。 次の図にある各グラフの色は、グラフの下に表示されるプロパティの値に対応しています。

    ![Azure Maps 使用状況メトリックの分割](media/how-to-view-api-usage/splitting.png)

8. 上部の **[メトリックの追加]** ボタンをクリックするだけで、同じグラフ上で複数のメトリックを確認することもできます。

## <a name="next-steps"></a>次のステップ

使用状況を追跡する必要がある Azure Maps の API の詳細については、以下を参照してください。
> [!div class="nextstepaction"] 
> [Azure Maps Web SDK のハウツー](how-to-use-map-control.md)

> [!div class="nextstepaction"] 
> [Azure Maps Android SDK のハウツー](how-to-use-android-map-control-library.md)

> [!div class="nextstepaction"]
> [Azure Maps REST API ドキュメント](https://docs.microsoft.com/rest/api/maps)
