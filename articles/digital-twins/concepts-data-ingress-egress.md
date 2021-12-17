---
title: データのイングレスとエグレス
titleSuffix: Azure Digital Twins
description: Azure Digital Twins を他のサービスと統合するためのイングレスとエグレスの要件について説明します。
author: baanders
ms.author: baanders
ms.date: 6/1/2021
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 0855663fb60f6e8ae420762cdd23ce237de56c04
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/24/2021
ms.locfileid: "122770583"
---
# <a name="data-ingress-and-egress-for-azure-digital-twins"></a>Azure Digital Twins のデータのイングレスとエグレス

通常、Azure Digital Twins を他のサービスと共に使用して、さまざまな種類の方法でデータを使用する柔軟な接続済みソリューションを作成します。

Azure Digital Twins では、[イベント ルート](concepts-route-events.md)を使用して、テレメトリと通知を配信するために使用される [IoT Hub](../iot-hub/about-iot-hub.md) や [Logic Apps](../logic-apps/logic-apps-overview.md) などのアップストリーム サービスからデータを受信できます。 

Azure Digital Twins では、ストレージ、ワークフロー統合、分析などのためにデータを [Azure Maps](../azure-maps/about-azure-maps.md) や [Time Series Insights](../time-series-insights/overview-what-is-tsi.md) などのダウンストリーム サービスにルーティングすることもできます。 

## <a name="data-ingress"></a>データのイングレス

Azure Digital Twins は、[IoT Hub](../iot-hub/about-iot-hub.md)、[Logic Apps](../logic-apps/logic-apps-overview.md)、独自のカスタム サービスなど、任意のサービスからのデータとイベント使用して動作できます。 この種のデータ フローにより、環境内の物理デバイスからテレメトリを収集し、クラウドの Azure Digital Twins グラフを使用してこのデータを処理することができます。

組み込みの IoT Hub をバックグラウンドで使用する代わりに、Azure Digital Twins を使用すると、サービスで使用するための "独自の" IoT Hub を実現できます。 現在運用中の既存の IoT Hub を使用することも、この目的で使用する新しい IoT Hub をデプロイすることもできます。 この機能により、IoT Hub のすべてのデバイス管理機能にフル アクセスできます。

任意のソースから Azure Digital Twins にデータを取り込むには、[Azure 関数](../azure-functions/functions-overview.md)を使用します。 このパターンの詳細については、「[IoT Hub からテレメトリを取り込む](how-to-ingest-iot-hub-data.md)」を参照するか、Azure Digital Twins の「[エンド ツー エンドのソリューションを接続する](tutorial-end-to-end.md)」でお試しください。 

また、Azure Digital Twins を Azure Logic Apps トリガーに接続する方法について、「[Logic Apps との統合](how-to-integrate-logic-apps.md)」で学ぶこともできます。

## <a name="data-egress-services"></a>データ エグレス サービス

Azure Digital Twins は、接続されている **エンドポイント** にデータを送信できます。 サポートされるエンドポイントは次のとおりです。
* [イベント ハブ](../event-hubs/event-hubs-about.md)
* [Event Grid](../event-grid/overview.md)
* [Service Bus](../service-bus-messaging/service-bus-messaging-overview.md)

エンドポイントは、管理 API または Azure portal を使用して Azure Digital Twins にアタッチされています。 エンドポイントを Azure Digital Twins に接続する方法の詳細については、「[エンドポイントとルートを管理する](how-to-manage-routes.md)」を参照してください。

他にも、[Azure Storage](../storage/common/storage-introduction.md)、[Azure Maps](../azure-maps/about-azure-maps.md)、[Azure Data Explorer](/azure/data-explorer/data-explorer-overview)、[Time Series Insights](../time-series-insights/overview-what-is-tsi.md) など、データの最終的な転送先となるサービスが多数あります。 このようなサービスにデータを送信するには、宛先サービスをエンドポイントにアタッチします。

たとえば、Azure Maps も使用していて、Azure Digital Twins の[ツイン グラフ](concepts-twins-graph.md)と場所を関連付ける必要がある場合は、Azure Functions で Event Grid を使用して、デプロイ内のすべてのサービス間の通信を確立できます。 Azure Maps の統合の詳細については、「[Azure Digital Twins を使用して Azure Maps の屋内マップを更新する](how-to-integrate-maps.md)」を参照してください

また、Time Series Insights と同様の方法でデータをルーティングする方法については、「[Time Series Insights と統合する](how-to-integrate-time-series-insights.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

エンドポイントの詳細と外部サービスに対するルーティング イベントについては、次を参照してください。
* [Azure Digital Twins イベントのルーティング](concepts-route-events.md)

IoT Hub からデータを取り込むように Azure Digital Twins を設定する方法をご覧ください。
* [IoT Hub からテレメトリを取り込む](how-to-ingest-iot-hub-data.md)