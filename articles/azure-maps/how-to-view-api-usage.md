---
title: Azure Maps の API 使用状況を表示する方法 | Microsoft Docs
description: ポータルで Azure Maps の API 呼び出しに対するメトリックを表示する方法について説明します。
author: dsk-2015
ms.author: dkshir
ms.date: 08/06/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: e62d2ff1fdd6bc94244511a2de95c4268a58d6f9
ms.sourcegitcommit: 0fcd6e1d03e1df505cf6cb9e6069dc674e1de0be
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/14/2018
ms.locfileid: "42141751"
---
# <a name="how-to-view-the-azure-maps-api-usage"></a>Azure Maps の API 使用状況を表示する方法
この記事では、[ポータル](https://portal.azure.com)で Azure Maps アカウントにおける API 使用状況のメトリックを表示する方法について示します。 メトリックは、カスタマイズ可能な時間の長さに従って便利なグラフ形式で示されます。 

## <a name="view-metric-snapshot"></a>メトリックのスナップショットを表示する 

Maps アカウントの **[概要]** ページでいくつかの共通メトリックを表示できます。 現在、選択可能な時間の長さにわたって*要求の合計数*、*合計エラー数*、および*可用性*が表示されています。 

![Azure Maps メトリックの概要](media/how-to-view-api-usage/portal-overview.png)

特定の分析に対してこれらのグラフをカスタマイズする場合は、次のセクションに進みます。


## <a name="view-detailed-metrics"></a>詳細なメトリックを表示する

1. [ポータル](https://portal.azure.com)で Azure サブスクリプションにサインインします。 

2. 左側にある **[すべてのリソース]** メニュー アイコンをクリックして、*[Azure Maps アカウント]* に移動します。

3. ご利用の Maps アカウントが開いたら、左側の **[メトリック]** メニューをクリックします。

4. **[メトリック]** ウィンドウで、次のいずれかを選びます。

    1. **[可用性]**: 期間にわたって API 可用性の*平均*を示します。 
    2. **[使用状況]**: 使用状況におけるアカウントの*割合*を示します。 

    ![Azure Maps メトリック ウィンドウ](media/how-to-view-api-usage/portal-metrics.png)

5. メトリックを選択したら、既定値である **[過去 12 時間 (自動)]** を選択することによって、*[時間の範囲]* を選択できます。 また、*[時間の粒度]* を選択したり、同じドロップダウン内の *[ローカル]* または *[GMT]* として時間を示すように選んだりすることもできます。 **[Apply]** をクリックします。

    ![Azure Maps メトリックの時間の範囲](media/how-to-view-api-usage/time-range.png)
 
6. メトリックを追加すると、そのメトリックに関連するプロパティから**フィルターを追加**して、グラフを表示するプロパティの値を選択することができます。 

    ![Azure Maps メトリックのフィルター](media/how-to-view-api-usage/filter.png)

7. また、選択したメトリック プロパティに基づいてメトリックに**分割を適用**することもできます。 これにより、グラフを複数のグラフに分割できます。プロパティの値ごとにグラフ 1 つです。 たとえば、次の図にある各グラフの色は、グラフの下に表示されるプロパティの値に対応しています。

    ![Azure Maps メトリックの分割](media/how-to-view-api-usage/splitting.png)
 
8. 上部の **[メトリックの追加]** ボタンをクリックするだけで、同じグラフ上で複数のメトリックを確認することもできます。


## <a name="next-steps"></a>次の手順

これで、Azure Maps の使用状況を追跡する方法を確認できました。使用している API の詳細を学ぶために次に進むことができます。

* [Azure Maps REST API ドキュメント](https://docs.microsoft.com/rest/api/maps)
