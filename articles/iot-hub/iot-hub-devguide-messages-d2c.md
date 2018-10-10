---
title: Azure IoT Hub メッセージ ルーティングについて | Microsoft Docs
description: 開発者ガイド - メッセージ ルーティングを使用して、device-to-cloud メッセージを送信する方法。 テレメトリ データとそれ以外のデータの両方の送信についても説明します。
author: ash2017
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/13/2018
ms.author: asrastog
ms.openlocfilehash: 7c36ab2f0d4d3e5c772f8ef62c13161a2649362f
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2018
ms.locfileid: "46966743"
---
# <a name="use-message-routing-to-send-device-to-cloud-messages-to-different-endpoints"></a>メッセージ ルーティングを使用して、device-to-cloud メッセージを別のエンドポイントに送信する

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

メッセージ ルーティングを使用すると、自動化された、スケーラブルで信頼性の高い方法で、デバイスからクラウド サービスにメッセージを送信することができます。 メッセージ ルーティングは、次の目的で使用できます。 

* **デバイスのテレメトリ メッセージだけでなくイベント** (デバイス ライフサイクル イベントとデバイス ツイン変更イベント) を組み込みのエンドポイントとカスタム エンドポイントに送信する。 ルーティング エンドポイントについては、[こちら](##routing-endpoints)を参照してください。

* リッチ クエリを適用して、**さまざまなエンドポイントにルーティングする前にデータをフィルター処理**する。 メッセージ ルーティングでは、メッセージ プロパティとメッセージ本文に基づいてクエリを実行できるほか、デバイス ツインのタグとプロパティに基づいてクエリを実行することもできます。 メッセージ ルーティングでクエリを使用する方法の詳細については、[こちら](../iot-hub/iot-hub-devguide-routing-query-syntax.md)を参照してください。

IoT Hub でメッセージのルーティングを機能させるには、これらのサービス エンドポイントへの書き込みアクセス許可が必要です。 Azure Portal を使用してエンドポイントを構成する場合、必要なアクセス許可は自動的に追加されます。 必要なスループットをサポートするようにサービスを確実に構成してください。 IoT ソリューションを初めて構成する場合は、追加したエンドポイントを監視し、実際の負荷の調整を行う必要があります。

IoT ハブでは、プロトコル間の相互運用性を確保するために、すべての device-to-cloud メッセージング用に[共通の形式](../iot-hub/iot-hub-devguide-messages-construct.md)が定義されています。 メッセージが、同じエンドポイントを指している複数のルートと一致する場合、IoT Hub はそのエンドポイントにメッセージを 1 回だけ送信します。 そのため、Service Bus キューまたはトピックで重複除去を構成する必要はありません。 パーティション分割されたキューでは、パーティションのアフィニティによってメッセージの順序が保証されます。 このチュートリアルを使用して、[メッセージ ルーティングの構成] (https://docs.microsoft.com/azure/iot-hub/tutorial-routing) の方法について学習してください。

## <a name="routing-endpoints"></a>ルーティング エンドポイント

IoT ハブには、Event Hubs との互換性がある、既定の組み込みのエンドポイント (**messages/events**) があります。 サブスクリプション内の他のサービスを IoT ハブにリンクして、メッセージのルーティング先となる[カスタム エンドポイント](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-endpoints#custom-endpoints)を作成することができます。 現在、IoT Hub では、カスタム エンドポイントとして、次のサービスをサポートしています。

### <a name="built-in-endpoint"></a>組み込みのエンドポイント
標準的な [Event Hubs 統合と SDK](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-read-builtin) を使用して、組み込みのエンドポイント (**messages/events**) から device-to-cloud メッセージを受信することができます。 ルートの作成後、エンドポイントへのルートを作成しないと、その組み込みのエンドポイントへのデータの送信が停止することに注意してください。

### <a name="azure-blob-storage"></a>Azure Blob Storage
IoT Hub では、Azure Blob Storage に [Apache Avro](http://avro.apache.org/) 形式でデータを書き込む処理のみをサポートしています。 IoT Hub は、バッチが特定のサイズに達するか、一定の時間が経過した時点で、メッセージを一括処理して BLOB にデータを書き込みます。

IoT Hub の既定のファイル名前付け規則は次のとおりです。
```
{iothub}/{partition}/{YYYY}/{MM}/{DD}/{HH}/{mm}
```

任意のファイル名前付け規則を使用可能ですが、一覧で示されているすべてのトークンを使う必要があります。 書き込むデータがない場合、IoT ハブは空の BLOB に書き込みます。

### <a name="service-bus-queues-and-service-bus-topics"></a>Service Bus キューと Service Bus トピック
IoT Hub エンドポイントとして使用される Service Bus のキューおよびトピックでは、**セッション**も**重複データ検出**も有効にしないでください。 これらのオプションのいずれかが有効になっている場合、エンドポイントは Azure Portal に**到達不能**として表示されます。

### <a name="event-hubs"></a>Event Hubs
組み込みの Event Hubs 互換エンドポイントとは別に、Event Hubs タイプのカスタム エンドポイントにデータをルーティングすることもできます。 

ルーティングとカスタム エンドポイントを使用すると、メッセージは、規則に一致しない場合、組み込みのエンドポイントにのみ配信されます。 メッセージを組み込みのエンドポイントとカスタム エンドポイントに配信するには、イベント エンドポイントにメッセージを送信するルートを追加します。

## <a name="reading-data-that-has-been-routed"></a>ルーティングされたデータの読み取り
この[チュートリアル](https://docs.microsoft.com/azure/iot-hub/tutorial-routing)に従って、ルートを構成することができます。

エンドポイントからメッセージを読み取る方法については、以下のチュートリアルを参照してください。

* [組み込みのエンドポイント](https://docs.microsoft.com/azure/iot-hub/quickstart-send-telemetry-node)からの読み取り
* [BLOB ストレージ](https://docs.microsoft.com/azure/storage/blobs/storage-blob-event-quickstart)からの読み取り
* [Event Hubs](https://docs.microsoft.com/azure/event-hubs/event-hubs-dotnet-standard-getstarted-send) からの読み取り
* [Service Bus キュー](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-dotnet-get-started-with-queues)からの読み取り
* [Service Bus トピック](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-dotnet-how-to-use-topics-subscriptions)からの読み取り

## <a name="fallback-route"></a>フォールバック ルート
フォールバック ルートを通じて、既存のいずれのルートのクエリ条件も満たさないすべてのメッセージが、[Event Hubs](https://docs.microsoft.com/azure/event-hubs/) との互換性がある組み込みのイベント ハブ (**messages/events**) に送信されます。 メッセージ ルーティングが有効になっている場合は、フォールバック ルート機能を有効にすることができます。 ルートの作成後、エンドポイントへのルートを作成しないと、その組み込みのエンドポイントへのデータの送信が停止することに注意してください。 組み込みのエンドポイントへのルートがなく、フォールバック ルートが有効になっている場合は、ルートのどのクエリ条件とも一致しないメッセージのみが組み込みのエンドポイントに送信されます。 また、既存のすべてのルートを削除する場合は、組み込みのエンドポイントですべてのデータを受信するために、フォールバック ルートを有効にする必要があります。 

Azure portal の [メッセージ ルーティング] ブレードで、フォールバック ルートを有効または無効にすることができます。 また、Azure Resource Manager で [FallbackRouteProperties](https://docs.microsoft.com/rest/api/iothub/iothubresource/createorupdate#fallbackrouteproperties) を使用して、フォールバック ルート用にカスタム エンドポイントが使用されるようにすることもできます。

## <a name="non-telemetry-events"></a>非テレメトリ イベント
メッセージ ルーティングでは、デバイス テレメトリのほかに、デバイス ツイン変更イベントとデバイス ライフサイクル イベントの送信を有効にすることもできます。 たとえば、データ ソースを**デバイス ツイン変更イベント**に設定してルートを作成した場合、IoT Hub は、デバイス ツインの変更が含まれているメッセージをエンドポイントに送信します。 同様に、データ ソースを**デバイス ライフサイクル イベント**に設定してルートを作成した場合、IoT Hub は、デバイスが削除または作成されたかどうかを示すメッセージを送信します。 
[IoT Hub は Azure Event Grid とも統合](iot-hub-event-grid.md)されているため、デバイス イベントを発行して、それらのイベントに基づくワークフローのリアルタイムの統合と自動化をサポートできます。 ご自分のシナリオにどれが最適かについては、[メッセージ ルーティングと Event Grid の主な違い](iot-hub-event-grid-routing-comparison.md)に関するページを参照してください。

## <a name="testing-routes"></a>ルートのテスト
新しいルートを作成したり、既存のルートを編集したりする場合は、サンプル メッセージを使用してルート クエリをテストする必要があります。 個々のルートをテストすることも、すべてのルートを一度にテストすることも可能ですが、テスト中にメッセージがエンドポイントにルーティングされることはありません。 テストには、Azure portal、Azure Resource Manager、Azure PowerShell、および Azure CLI を使用することができます。 結果は、サンプル メッセージがクエリに一致したか、メッセージがクエリに一致しなかったか、サンプル メッセージまたはクエリ構文が正しくないためにテストを実行できなかったかを識別するのに役立ちます。 詳細については、[ルートのテスト](https://docs.microsoft.com/rest/api/iothub/iothubresource/testroute)に関するページと[すべてのルートのテスト](https://docs.microsoft.com/rest/api/iothub/iothubresource/testallroutes)に関するページを参照してください。

## <a name="latency"></a>Latency
組み込みのエンドポイントを使用して device-to-cloud テレメトリ メッセージをルーティングすると、最初のルーティングの作成後にエンド ツー エンドの待機時間が若干上昇します。

ほとんどの場合、待ち時間の増加は平均で 500 ミリ秒未満です。 待ち時間は、**Routing: message latency for messages/events (ルーティング: メッセージ/イベントのメッセージ待機時間)** または **d2c.endpoints.latency.builtIn.events** という IoT Hub メトリックを使用して監視することができます。 最初のルーティング後にルーティングを作成または削除してもエンド ツー エンドの待機時間には影響しません。

## <a name="monitoring-and-troubleshooting"></a>監視とトラブルシューティング
IoT Hub には、ハブの正常性と送信されたメッセージの概要を示す、ルーティングおよびエンドポイント関連のメトリックがいくつか用意されています。 複数のメトリックから得た情報を組み合わせることで、問題の根本原因を特定することができます。 たとえば、メトリック **Routing: telemetry messages dropped (ルーティング: 破棄されたテレメトリ メッセージ)** または **d2c.telemetry.egress.dropped** を使用すると、どのルートのクエリにも一致せず、フォールバック ルートが無効になっていたときに破棄されたメッセージの数を特定することができます。 [IoT Hub メトリック](https://docs.microsoft.com/azure/iot-hub/iot-hub-metrics)に関するページには、IoT Hub で既定で有効になっているすべてのメトリックが一覧で示されています。

Azure Monitor の[診断設定](https://docs.microsoft.com/azure/iot-hub/iot-hub-monitor-resource-health)の**ルート**診断ログを使用すると、IoT Hub に認識されたエンドポイントの正常性とルーティング クエリの評価中に発生したエラーを追跡することができます (エンドポイントが停止した場合など)。 これらの診断ログは、カスタムの処理を実行するために、Log Analytics、Event Hubs、または Azure Storage に送信することができます。

## <a name="next-steps"></a>次の手順
* メッセージ ルートの作成方法については、[ルートを使用した IoT Hub の device-to-cloud メッセージの処理](../iot-hub/tutorial-routing.md)に関するチュートリアルを参照してください。
* [クイック スタート](https://docs.microsoft.com/azure/iot-hub/quickstart-send-telemetry-node)では、シミュレートされたデバイスから device-to-cloud メッセージを送信する方法を示します。
* device-to-cloud メッセージの送信に使用できる SDK については、[Azure IoT SDK](../iot-hub/iot-hub-devguide-sdks.md) に関するページを参照してください。
