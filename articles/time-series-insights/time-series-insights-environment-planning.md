---
title: Azure Time Series Insights 環境の規模を計画する | Microsoft Docs
description: この記事では、ストレージ容量、データ保有期間、イングレス容量、監視、ビジネスのディザスター リカバリー (BCDR) など、Azure Time Series Insights 環境を計画する際のベスト プラクティスに従う方法について説明します。
services: time-series-insights
ms.service: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 04/29/2019
ms.custom: seodec18
ms.openlocfilehash: 1c6e8ef9effdb5cf31aee1603de28bed0d894cbb
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/27/2019
ms.locfileid: "66239083"
---
# <a name="plan-your-azure-time-series-insights-ga-environment"></a>Azure Time Series Insights GA 環境の計画

この記事では、予想されるイングレス レートとデータ リテンション期間の要件に基づいて、Azure Time Series Insights 一般公開 (GA) 環境を計画する方法について説明します。

## <a name="video"></a>ビデオ

### <a name="learn-more-about-data-retention-in-azuretime-series-insights-and-how-to-plan-for-itbr"></a>Azure Time Series Insights でのデータ リテンション期間と、その計画方法を説明します。</br>

> [!VIDEO https://www.youtube.com/embed/03x6zKDQ6DU]

## <a name="best-practices"></a>ベスト プラクティス

Time Series Insights の使用を開始するときは、分単位でプッシュすることが予想されるデータの量と、データを保存する必要がある期間を把握しておくことをお勧めします。  

Time Series Insights の 2 つの SKU の容量とリテンション期間の詳細については、「[Time Series Insights の価格](https://azure.microsoft.com/pricing/details/time-series-insights/)」をご覧ください。

長期的な成功に向けて環境を最適に計画するには、次の属性を検討します。

- <a href="#understand-storage-capacity">[ストレージ容量]</a>
- <a href="#understand-data-retention">データ リテンション期間</a>
- <a href="#understand-ingress-capacity">イングレス容量</a>
- <a href="#shape-your-events">イベントの整形</a>
- <a href="#ensure-you-have-reference-data">参照データの配置確認</a>

## <a name="understand-storage-capacity"></a>ストレージ容量について

既定では、Azure Time Series Insights は、プロビジョニング済みのストレージの容量 (ユニット数とユニットあたりのストレージ容量の積) とイングレスに基づいてデータを保持します。

## <a name="understand-data-retention"></a>データ リテンション期間について

Time Series Insight 環境の **[データ リテンション期間]** 設定を構成できます。最大 400 日のリテンション期間を設定できます。 Time Series Insights には 2 つのモードがあります。1 つは、環境で最新のデータを確保するように最適化するモードです (既定で有効)。もう 1 つは、リテンション期間の制限が満たされるように最適化するモードです。このモードでは、環境の全ストレージ容量に達した場合にイングレスが一時停止されます。  Azure Portal の環境の構成ページで、リテンション期間を調整し、2 つのモードを切り替えることができます。

Time Series Insights 環境では、最大 400 日のデータ リテンション期間を構成できます。

### <a name="configure-data-retention"></a>データ リテンション期間の構成

1. [Azure Portal](https://portal.azure.com) で、Time Series Insights 環境を選択します。

1. **[Time Series Insights 環境]** ページで、 **[設定]** の **[構成]** を選択します。

1. **[データ リテンション期間 (日)]** ボックスに、1 ～ 400 の値を入力します。

   [![リテンション期間の構成](media/environment-mitigate-latency/configure-retention.png)](media/environment-mitigate-latency/configure-retention.png#lightbox)

> [!TIP]
> 適切なデータ リテンション ポリシーの実装の詳細については、[リテンション期間の構成方法](./time-series-insights-how-to-configure-retention.md)に関するページを参照してください。

## <a name="understand-ingress-capacity"></a>イングレス容量について

計画を立てる際に重視すべきもう 1 つの領域は、1 分あたりの割り当ての派生物であるイングレス容量です。

調整の観点から、パケット サイズが 32 KB のイングレス データ パケットは、それぞれ 1 KB のサイズの 32 個のイベントとして扱われます。 イベントの最大許容サイズは 32 KB です。32 KB を超えるデータ パケットは切り捨てられます。

各 SKU のイングレス容量を次の表に示します。

|SKU  |イベント数/月/ユニット  |イベント サイズ/月/ユニット  |イベント数/分/ユニット  | サイズ/分/ユニット   |
|---------|---------|---------|---------|---------|
|S1     |   30,000,000     |  30 GB     |  720    |  720 KB   |
|S2     |   300,000,000    |   300 GB   | 7,200   | 7,200 KB  |

1 つの環境で、S1 SKU または S2 SKU の容量を 10 ユニットまで増やすことができます。 S1 環境から S2 環境、または S2 環境から S1 環境に移行することはできません。

イングレス容量については、1 か月あたりに必要なイングレスの合計数をまず決定する必要があります。 次に、1 分あたりのニーズを決定します。これには、調整と待機時間が関与するためです。

データ イングレスのスパイクの持続が 24 時間未満の場合、Time Series Insights は上記のレートの 2 倍のイングレス レートで "キャッチアップ" できます。

たとえば、単一の S1 SKU を使用し、1 分あたり 720 イベントのレートでデータをイングレスしている場合、1440 イベント以下のレートでスパイクの持続が 1 時間未満であれば、環境に顕著な待機時間は発生しません。 ただし、1 分あたり 1440 イベントを超える状態が 1 時間以上続いた場合、環境内で視覚化され、クエリに使用できるデータに待機時間が発生する可能性があります。

プッシュすることが予想されるデータの量が事前にわからない場合があります。 この場合、Azure Portal で [Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-metrics) と [Azure Event Hubs](https://blogs.msdn.microsoft.com/cloud_solution_architect/2016/05/25/using-the-azure-rest-apis-to-retrieve-event-hub-metrics/) のデータ テレメトリを確認できます。 このテレメトリは、環境をプロビジョニングする方法を決定する際に役立ちます。 Azure Portal で、それぞれのイベント ソースの **[メトリック]** ページを使用してテレメトリを表示します。 イベント ソースのメトリックを理解すると、Time Series Insights 環境をより効果的に計画し、プロビジョニングできます。

### <a name="calculate-ingress-requirements"></a>イングレス要件の計算

- イングレス容量が 1 分あたりの平均レートを上回っていることを確認し、環境が、現在の容量の 2 倍に相当する予想されるイングレスを 1 時間未満で十分に処理できる規模であることを確認します。

- 1 時間以上続くイングレス スパイクが発生した場合は、スパイク レートを平均値として使用し、スパイク レートに対応する容量を備えた環境をプロビジョニングします。

### <a name="mitigate-throttling-and-latency"></a>調整と待機時間の緩和

調整と待ち時間が発生しないようにする方法については、[待ち時間と調整の緩和](time-series-insights-environment-mitigate-latency.md)に関するページを参照してください。

## <a name="shape-your-events"></a>イベントの調整

イベントを TSI に送信する方法がプロビジョニングする環境のサイズに対応していることを確認することが重要です (逆に言えば、TSI で読み込まれるイベントの数と各イベントのサイズに、環境のサイズをマッピングできます)。 同様に、データにクエリを実行するときのスライスとフィルターの基準にする属性について考えることが重要です。

> [!TIP]
> [イベントの送信](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-send-events)に関するページで、JSON の整形のドキュメントを参照してください。

## <a name="ensure-you-have-reference-data"></a>参照データの確認

**参照データ セット**は、イベント ソースからのイベントを増幅する項目の集まりです。 イベント ソースから受信した各イベントは、Time Series Insights のイングレス エンジンによって、指定した参照データ セット内の対応するデータ行と結合されます。 こうして増幅されたイベントをクエリで利用することができます。 この結合操作は、参照データ セットに定義されている主キー列に基づいて行われます。

参照データは遡及的に結合されないことにご注意ください。 つまり、データが構成されてアップロードされると、現在および将来のイングレス データのみが対応付けられ、参照日付セットに結合されます。  大量の履歴データを TSI に送信する予定のとき、TSI で最初に参照データをアップロードまたは作成しない場合、作業をやり直す必要があるかもしれません (それは楽しい作業ではないかもしれません)。  

TSI での参照データの作成、アップロード、管理方法についての詳細は、Microsoft の[参照データ](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-add-reference-data-set)に関する文書をご覧ください。

[!INCLUDE [business-disaster-recover](../../includes/time-series-insights-business-recovery.md)]

## <a name="next-steps"></a>次の手順

- [Azure portal で新しい Time Series Insights 環境](time-series-insights-get-started.md)を作成することから開始します。

- Time Series Insights に[イベント ハブ イベント ソースを追加する](time-series-insights-how-to-add-an-event-source-eventhub.md)方法を学習します。

- [IoT Hub イベント ソースを構成する](time-series-insights-how-to-add-an-event-source-iothub.md)方法を参照します。
