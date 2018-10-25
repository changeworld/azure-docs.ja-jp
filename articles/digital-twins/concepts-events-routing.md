---
title: Azure Digital Twins でのイベントとメッセージのルーティング | Microsoft Docs
description: Azure Digital Twins でのサービス エンドポイントへのイベントとメッセージのルーティングの概要を説明します
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/08/2018
ms.author: alinast
ms.openlocfilehash: 9b861f0991b11637c7b27b645d4506e658ea53b3
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2018
ms.locfileid: "49324035"
---
# <a name="routing-events-and-messages"></a>ルーティング イベントおよびメッセージ

IoT ソリューションでは、多くの場合、ストレージ、分析、その他の複数の強力なサービスが結合されます。 この記事では、Azure Digital Twins アプリを Azure 分析、AI、およびストレージ サービスに接続して、より深い分析情報と機能で強化する方法について説明します。

## <a name="route-types"></a>ルートの種類

Digital Twins には、IoT イベントを他の Azure サービスやビジネス アプリケーションに統合するための 2 つの方法が用意されています。

* **Digital Twins イベントのルーティング**: 空間グラフ内のオブジェクトが変化したとき、テレメトリ データを受信したとき、またはユーザー定義関数が事前定義された条件に基づく通知を作成したときに、Azure Digital Twins イベントをトリガーできます。 ユーザーは、さらに処理するために、これらのイベントを [Event Hubs](https://azure.microsoft.com/services/event-hubs/)、[Service Bus トピック](https://azure.microsoft.com/services/service-bus/)、または [Event Grid](https://azure.microsoft.com/services/event-grid/) に送信できます。

* **デバイス テレメトリのルーティング**: イベントのルーティングだけでなく、Azure Digital Twins では生デバイス テレメトリ メッセージを Event Hubs にルーティングして、さらに洞察と分析を行うことができます。 この種のメッセージは、Digital Twins では処理されず、**Event Hub** に転送されるだけです。

ユーザーは、イベントの送信またはメッセージの転送のためのエグレス エンドポイントを 1 つ以上指定できます。 イベントとメッセージは、これらの定義済みのルーティング設定に従って、エンドポイントに送信されます。 つまり、ユーザーは、グラフ操作イベントの受信用に特定の 1 つのエンドポイントを指定し、デバイスのテレメトリ イベントの受信用に別のエンドポイントを指定できます。

![Digital Twins のイベントのルーティング][1]

**Event Hubs** へのルーティングではテレメトリ メッセージが送信された順序が維持されるので、元の受信と同じ順序でエンドポイントに到着します。 **Event Grid** と **Service Bus** では、エンドポイントがその発生順にイベントを取得することは保証されません。 ただし、イベント スキーマにはタイムスタンプが含まれており、これを使用してイベントがエンドポイントに到着した後に順序を特定できます。

## <a name="route-implementation"></a>ルートの実装

Azure Digital Twins サービスでは、現在、以下の **EndpointTypes** がサポートされています。

* **EventHub**: Event Hub の接続文字列エンドポイントです。
* **ServiceBus**: Service Bus の接続文字列エンドポイントです。
* **EventGrid**: Event Grid の接続文字列エンドポイントです。

現在、Azure Digital Twins では、選択したエンドポイントに送信される以下の **EventTypes** がサポートされています。

* **DeviceMessages**: ユーザーのデバイスから送信され、システムによって転送される、テレメトリ メッセージです。
* **TopologyOperation**: グラフまたはグラフのメタデータを変更する操作です。 たとえば、スペースなどのエンティティの追加や削除などです。
* **SpaceChange**: デバイス テレメトリ メッセージの結果としての、スペースの計算値の変化です。
* **SensorChange**: デバイス テレメトリ メッセージの結果としての、センサーの計算値の変化です。
* **UdfCustom**: ユーザー定義関数からのカスタム通知です。

> [!IMPORTANT]
> すべての **EndpointTypes** がすべての **EventTypes** をサポートしているわけではありません。
> 各 **EndpointType** で許容される **EventTypes** については、次の表をご覧ください。

|             | DeviceMessages | TopologyOperation | SpaceChange | SensorChange | UdfCustom |
| ----------- | -------------- | ----------------- | ----------- | ------------ | --------- |
| **EventHub**|     ○          |         ○         |     ○       |      ○       |   ○       |
| **ServiceBus**|              |         ○         |     ○       |      ○       |   ○       |
| **EventGrid**|               |         ○         |     ○       |      ○       |   ○       |

>[!NOTE]
>エンドポイントの作成方法と、イベントのスキーマの例については、[エンドポイントとエグレス](how-to-egress-endpoints.md)に関するページをご覧ください。

## <a name="next-steps"></a>次の手順

パブリック プレビューの制限については、[Azure Digital Twins のプレビューの制限](concepts-service-limits.md)に関するページをご覧ください。

Azure Digital Twins のサンプルを試すには、[使用可能な部屋を検索するクイック スタート](quickstart-view-occupancy-dotnet.md)のページをご覧ください。

<!-- Images -->
[1]: media/concepts/digital-twins-events-routing.png