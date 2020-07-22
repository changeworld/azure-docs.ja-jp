---
title: 他のサービスとの統合
titleSuffix: Azure Digital Twins
description: Azure Digital Twins をデプロイするときのイングレスとエグレスの要件について説明します。
author: baanders
ms.author: baanders
ms.date: 3/16/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: ba4188445f9bdf791ef760936bd27ac0f507fb4c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "84737174"
---
# <a name="integrate-azure-digital-twins-with-other-services"></a>Azure Digital Twins を他のサービスと統合する

Azure Digital Twins は、通常、他のサービスと共に使用されます。 Azure Digital Twins は、[**イベント ルート** ](concepts-route-events.md)使用して、テレメトリと通知を配信するために使用される [IoT Hub](../iot-hub/about-iot-hub.md) などのアップストリーム サービスからデータを受信します。 Azure Digital Twins は、ストレージ、ワークフロー統合、分析、およびその他の用途のためにデータをダウンストリーム サービスにルーティングすることもできます。 

## <a name="data-ingress"></a>データのイングレス

Azure Digital Twins は、IoT Hub のデータを使用して動作させることができます。 これにより、環境内の物理デバイスからテレメトリを収集し、クラウドの Azure Digital Twins グラフを使用してこのデータを処理することができます。

Azure Digital Twins には IoT Hub が組み込まれていません。 現在運用中の既存の IoT Hub を使用することも、新しいものをデプロイすることもできます。 これにより、IoT Hub のすべてのデバイス管理機能にフル アクセスできます。

IoT Hub から Azure Digital Twins にデータを取り込むには [Azure 関数](../azure-functions/functions-overview.md)を使用します。 このパターンの詳細については、[IoT Hub からテレメトリを取り込む方法](how-to-ingest-iot-hub-data.md)に関するページを参照するか、[Azure Digital Twins チュートリアルのエンドツーエンドのソリューションの接続](tutorial-end-to-end.md)に関するページを参照してください。

## <a name="data-egress-services"></a>データ エグレス サービス

Azure Digital Twins は、接続されている**エンドポイント**にデータを送信できます。 サポートされるエンドポイントは次のとおりです。
* [イベント ハブ](../event-hubs/event-hubs-about.md)
* [Event Grid](../event-grid/overview.md)
* [Service Bus](../service-bus-messaging/service-bus-messaging-overview.md)

エンドポイントは、管理 API または Azure portal を使用して Azure Digital Twins にアタッチされています。 エンドポイントを Azure Digital Twins に接続する方法の詳細については、[エンドポイントとルートを管理する方法](how-to-manage-routes.md)に関するページを参照してください。

他にも、[Azure Storage](../storage/common/storage-introduction.md) や [Time Series Insights](../time-series-insights/time-series-insights-update-overview.md) など、データの最終的な転送先となるサービスが多数あります。 このようなサービスにデータを送信するには、宛先サービスをエンドポイントにアタッチします。

たとえば、[Azure Maps](../azure-maps/about-azure-maps.md) も使用していて、Azure Digital Twins の[ツイン グラフ](concepts-twins-graph.md)と場所を関連付ける必要がある場合は、Azure Functions で Event Grid を使用して、デプロイ内のすべてのサービス間の通信を確立できます。

## <a name="next-steps"></a>次のステップ

エンドポイントの詳細と外部サービスに対するルーティング イベントについては、次を参照してください。
* [概念:Azure Digital Twins イベントのルーティング](concepts-route-events.md)に関するページ

IoT Hub からデータを取り込むように Azure Digital Twins を設定する方法をご覧ください。
* [IoT Hub からテレメトリを取り込む方法](how-to-ingest-iot-hub-data.md)に関するページ
