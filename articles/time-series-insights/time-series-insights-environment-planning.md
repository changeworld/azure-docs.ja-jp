---
title: "Azure Time Series Insights 環境の規模を計画する | Microsoft Docs"
description: "この記事では、ストレージ容量、データ リテンション期間、イングレス容量、監視など、Azure Time Series Insights 環境を計画する際のベスト プラクティスに従う方法について説明します。"
services: time-series-insights
ms.service: time-series-insights
author: jasonwhowell
ms.author: jasonh
manager: jhubbard
editor: MicrosoftDocs/tsidocs
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.devlang: csharp
ms.workload: big-data
ms.topic: article
ms.date: 11/15/2017
ms.openlocfilehash: 5fb158ba162dd199f419f9568de08a7a18c833dd
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2017
---
# <a name="plan-your-azure-time-series-insights-environment"></a>Azure Time Series Insights 環境の計画

この記事では、予想されるイングレス レートとデータ リテンション期間の要件に基づいて、Azure Time Series Insights 環境を計画する方法について説明します。

## <a name="best-practices"></a>ベスト プラクティス

Time Series Insights の使用を開始するときは、分単位でプッシュすることが予想されるデータの量と、データを保存する必要がある期間を把握しておくことをお勧めします。  

Time Series Insights の 2 つの SKU の容量とリテンション期間の詳細については、「[Time Series Insights の価格](https://azure.microsoft.com/pricing/details/time-series-insights/)」をご覧ください。

長期的な成功に向けて環境を最適に計画するには、次の属性を検討します。 
- ストレージの容量
- データ保持期間
- イングレス容量 

## <a name="understand-storage-capacity"></a>ストレージ容量について
既定では、Time Series Insight は、プロビジョニング済みのストレージの容量 (ユニットごとのストレージの単位時間あたりの容量) とイングレスに基づいてデータを保持します。

## <a name="understand-data-retention"></a>データ リテンション期間について
Time Series Insight 環境の **[データ リテンション期間]** 設定を構成できます。最大 400 日のリテンション期間を設定できます。  Time Series Insights には 2 つのモードがあります。1 つは、環境で最新のデータを確保するように最適化するモードです (既定で有効)。もう 1 つは、リテンション期間の制限が満たされるように最適化するモードです。このモードでは、環境の全ストレージ容量に達した場合にイングレスが一時停止されます。  Azure Portal の環境の構成ページで、リテンション期間を調整し、2 つのモードを切り替えることができます。

Time Series Insights 環境では、最大 400 日のデータ リテンション期間を構成できます。

## <a name="configure-data-retention"></a>データ リテンション期間の構成

1. [Azure Portal](https://portal.azure.com) で、Time Series Insights 環境を選択します。

2. **[Time Series Insights 環境]** ページで、**[設定]** の **[構成]** を選択します。 

3. **[データ リテンション期間 (日)]** ボックスに、1 ～ 400 の値を入力します。

   ![リテンション期間の構成](media/environment-mitigate-latency/configure-retention.png)

## <a name="understand-ingress-capacity"></a>イングレス容量について

計画を立てる際に重視すべきもう 1 つの領域は、1 分あたりの割り当ての派生物であるイングレス容量です。 

調整の観点から、パケット サイズが 32 KB のイングレス データ パケットは、それぞれ 1 KB のサイズの 32 個のイベントとして扱われます。 イベントの最大許容サイズは 32 KB です。32 KB を超えるデータ パケットは切り捨てられます。

各 SKU のイングレス容量を次の表に示します。

|SKU  |ユニットあたりのイベント数/月  |ユニットあたりのイベント サイズ/月  |ユニットあたりのイベント数/分  | ユニットあたりのサイズ/分   |
|---------|---------|---------|---------|---------|
|S1     |   30,000,000     |  30 GB     |  700    |  700 KB   |
|S2     |   300,000,000    |   300 GB   | 7,000   | 7,000 KB  |

1 つの環境で、S1 SKU または S2 SKU の容量を 10 ユニットまで増やすことができます。 S1 環境から S2 環境、または S2 環境から S1 環境に移行することはできません。 

イングレス容量については、1 か月あたりに必要なイングレスの合計数をまず決定する必要があります。 次に、1 分あたりのニーズを決定します。これには、調整と待機時間が関与するためです。

データ イングレスのスパイクの持続が 24 時間未満の場合、Time Series Insights は上記のレートの 2 倍のイングレス レートで "キャッチアップ" できます。 

たとえば、単一の S1 SKU を使用し、1 分あたり 700 イベントのレートでデータをイングレスしている場合、1400 イベント以下のレートでスパイクの持続が 1 時間未満であれば、環境に顕著な待機時間は発生しません。 ただし、1 分あたり 1400 イベントを超える状態が 1 時間以上続いた場合、環境内で視覚化され、クエリに使用できるデータに待機時間が発生する可能性があります。 

プッシュすることが予想されるデータの量が事前にわからない場合があります。 この場合、Azure Portal で [Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-metrics) と [Azure Event Hubs](https://blogs.msdn.microsoft.com/cloud_solution_architect/2016/05/25/using-the-azure-rest-apis-to-retrieve-event-hub-metrics/) のデータ テレメトリを確認できます。 このテレメトリは、環境をプロビジョニングする方法を決定する際に役立ちます。 Azure Portal で、それぞれのイベント ソースの **[メトリック]** ページを使用してテレメトリを表示します。 イベント ソースのメトリックを理解すると、Time Series Insights 環境をより効果的に計画し、プロビジョニングできます。

## <a name="calculate-ingress-requirements"></a>イングレス要件の計算

- イングレス容量が 1 分あたりの平均レートを上回っていることを確認し、環境が、現在の容量の 2 倍に相当する予想されるイングレスを 1 時間未満で十分に処理できる規模であることを確認します。

- 1 時間以上続くイングレス スパイクが発生した場合は、スパイク レートを平均値として使用し、スパイク レートに対応する容量を備えた環境をプロビジョニングします。
 
## <a name="mitigate-throttling-and-latency"></a>調整と待機時間の緩和

調整と待機時間が発生しないようにする方法については、[待機時間と調整の緩和](time-series-insights-environment-mitigate-latency.md)に関する記事をご覧ください。 

## <a name="next-steps"></a>次のステップ
- [イベント ハブ イベント ソースを追加する方法](time-series-insights-how-to-add-an-event-source-eventhub.md)
- [IoT Hub イベント ソースを追加する方法](time-series-insights-how-to-add-an-event-source-iothub.md)
