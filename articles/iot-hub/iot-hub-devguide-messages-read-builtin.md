---
title: Azure IoT Hub 組み込みエンドポイントについて | Microsoft Docs
description: 開発者ガイド - デバイスからクラウドへのメッセージを読むためにイベント ハブと互換性のある組み込みのエンドポイントを使用する方法を説明します。
author: wesmc7777
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/16/2021
ms.custom:
- amqp
- 'Role: Cloud Development'
ms.openlocfilehash: 1a3bae398a5991bbaf79e362e5ebb3942fd2f53e
ms.sourcegitcommit: 557ed4e74f0629b6d2a543e1228f65a3e01bf3ac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/05/2021
ms.locfileid: "129458607"
---
# <a name="read-device-to-cloud-messages-from-the-built-in-endpoint"></a>デバイスからクラウドへのメッセージを組み込みのエンドポイントから読み取る

既定では、メッセージは [Event Hubs](https://azure.microsoft.com/documentation/services/event-hubs/) と互換性のある、サービスに接続された組み込みエンドポイント (**messages/events**) にルーティングされます。 このエンドポイントは、現在、ポート 5671 で [AMQP](https://www.amqp.org/) プロトコルを使用して、またポート 443 で [AMQP over WebSockets](http://docs.oasis-open.org/amqp-bindmap/amqp-wsb/v1.0/cs01/amqp-wsb-v1.0-cs01.html) を使用して限定的に公開されています。 IoT Hub は、Event Hub と互換性のある組み込みのメッセージング エンドポイント **messages/events** を制御するための以下のプロパティを公開しています。

| プロパティ            | 説明 |
| ------------------- | ----------- |
| **パーティション数** | このプロパティは作成時に設定し、device-to-cloud イベントを取り込む場合の[パーティション](../event-hubs/event-hubs-features.md#partitions)数を定義します。 |
| **リテンション期間**  | このプロパティは、IoT Hub によってメッセージが保持される期間を日数で指定します。 既定は 1 日ですが、7 日間に増やすことができます。 |

IoT Hub では、組み込みの Event Hubs に最大 7 日間データを保持できます。 保持期間は IoT Hub の作成時に設定できます。 IoT Hub でのデータ保持期間は、お使いの IoT Hub のレベルとユニットの種類によって異なります。 サイズに関しては、組み込みの Event Hubs では、最大メッセージ サイズのメッセージを少なくとも 24 時間のクォータまで保持できます。 たとえば、1 つの S1 ユニットの IoT Hub では、それぞれ 4K サイズのメッセージを少なくとも 400,000 件保持できるだけのストレージが提供されます。 デバイスが送信するメッセージのサイズが小さい場合、使用されているストレージ容量に応じて、メッセージが保持される期間が長くなる (最大 7 日間) 可能性があります。 少なくとも指定された保持期間はデータが保持されることが保証されています。 メッセージは期限切れになり、リテンション期間が経過するとアクセスできなくなります。 

IoT Hub では、組み込みの D2C 受信エンドポイントでコンシューマー グループを管理ができます。 IoT Hub ごとに最大 20 個のコンシューマー グループを持つことができます。

[メッセージ ルーティング](iot-hub-devguide-messages-d2c.md)を使用していて、[フォールバック ルート](iot-hub-devguide-messages-d2c.md#fallback-route)が有効になっている場合、ルートに関するクエリに一致しないメッセージはすべて、組み込みのエンドポイントに送信されます。 このフォールバック ルートを無効にすると、どのクエリにも一致しないメッセージは破棄されます。

保持期間は、[IoT Hub のリソース プロバイダー REST API](/rest/api/iothub/iothubresource) を使用してプログラムにより変更するか、[Azure portal](https://portal.azure.com) を使用して変更できます。

IoT Hub は、**messages/events** 組み込みエンドポイントをバックエンド サービスに公開して、Hub で受信した D2C メッセージを読み取ります。 このエンドポイントには Event Hubs との互換性があり、Event Hubs のサービスがメッセージを読み取るためにサポートするすべてのメカニズムを使用できます。

## <a name="read-from-the-built-in-endpoint"></a>組み込みエンドポイントから読み取る

一部の製品統合と Event Hubs SDK は IoT Hub を認識するので、IoT Hub サービスの接続文字列を使用して、組み込みのエンドポイントに接続できます。

IoT Hub を認識しない Event Hubs SDK や製品統合を使用している場合は、イベント ハブ互換エンドポイントとイベント ハブ互換名が必要です。 次のように、ポータルからこれらの値を取得できます。

1. [Azure Portal](https://portal.azure.com) にサインインし、IoT Hub に移動します。

2. **[組み込みのエンドポイント]** をクリックします。

3. **[イベント]** セクションには、 **[パーティション]** 、 **[イベント ハブ互換名]** 、 **[イベント ハブ互換エンドポイント]** 、 **[保持期間]** 、 **[コンシューマー グループ]** の各値が含まれています。

    ![Device-to-cloud settings](./media/iot-hub-devguide-messages-read-builtin/eventhubcompatible.png)

ポータルの [イベント ハブ互換エンドポイント] フィールドには、Event Hubs の完全な接続文字列 (例: **Endpoint=sb://abcd1234namespace.servicebus.windows.net/;SharedAccessKeyName=iothubowner;SharedAccessKey=keykeykeykeykeykey=;EntityPath=iothub-ehub-abcd-1234-123456**) が含まれています。 使用している SDK で他の値が必要な場合、値は次のようになります。

| 名前 | 値 |
| ---- | ----- |
| エンドポイント | sb://abcd1234namespace.servicebus.windows.net/ |
| hostname | abcd1234namespace.servicebus.windows.net |
| 名前空間 | abcd1234namespace |

上記のスクリーンショットに示されているように、ドロップダウンから任意の共有アクセス ポリシーを選択できます。 指定したイベント ハブに接続するための **ServiceConnect** のアクセス許可を持つポリシーのみが表示されます。

IoT Hub が公開している組み込みのイベント ハブ互換エンドポイントに接続する際に使用できる SDK は次のとおりです。

| Language | SDK | 例 |
| -------- | --- | ------ |
| .NET | https://www.nuget.org/packages/Azure.Messaging.EventHubs | [クイックスタート](../iot-develop/quickstart-send-telemetry-iot-hub.md?pivots=programming-language-csharp) |
| Java | https://mvnrepository.com/artifact/com.azure/azure-messaging-eventhubs | [クイックスタート](../iot-develop/quickstart-send-telemetry-iot-hub.md?pivots=programming-language-java) |
| Node.js | https://www.npmjs.com/package/@azure/event-hubs | [クイックスタート](../iot-develop/quickstart-send-telemetry-iot-hub.md?pivots=programming-language-nodejs) |
| Python | https://pypi.org/project/azure-eventhub/ | [クイックスタート](../iot-develop/quickstart-send-telemetry-iot-hub.md?pivots=programming-language-python) |

IoT Hub が公開している組み込みのイベント ハブ互換エンドポイントで使用できる製品統合は次のとおりです。

* [Azure Functions](../azure-functions/index.yml)。 「[Azure Functions の Azure IoT Hub バインド](../azure-functions/functions-bindings-event-iot.md)」を参照してください。
* [Azure Stream Analytics](../stream-analytics/index.yml)。 「[Stream Analytics に入力としてデータをストリーム配信する](../stream-analytics/stream-analytics-define-inputs.md#stream-data-from-iot-hub)」をご覧ください。
* [Time Series Insights](../time-series-insights/index.yml)。 「[Time Series Insights 環境に IoT Hub イベント ソースを追加する](../time-series-insights/how-to-ingest-data-iot-hub.md)」をご覧ください。
* [Apache Storm のスパウト](../hdinsight/storm/apache-storm-develop-csharp-event-hub-topology.md)。 [スパウトのソース](https://github.com/apache/storm/tree/master/external/storm-eventhubs) は GitHub で確認できます。
* [Apache Spark 統合](../hdinsight/spark/apache-spark-ipython-notebook-machine-learning.md)。
* [Azure Databricks](/azure/azure-databricks/)。

## <a name="use-amqp-ws-or-a-proxy-with-event-hubs-sdks"></a>Event Hubs SDK で AMQP-WS またはプロキシを使用する

AMQP over WebSockets またはプロキシ経由での読み取りが必要な環境では Event Hubs SDK を使用して組み込みのエンドポイントから読み取ることができます。 詳しくは、次のサンプルを参照してください。

| 言語 | サンプル |
| -------- | ------ |
| .NET | [ReadD2cMessages .NET](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/main/iot-hub/Quickstarts/ReadD2cMessages) |
| Java | [read-d2c-messages Java](https://github.com/Azure-Samples/azure-iot-samples-java/tree/master/iot-hub/Quickstarts/read-d2c-messages) |
| Node.js | [read-d2c-messages Node.js](https://github.com/Azure-Samples/azure-iot-samples-node/tree/master/iot-hub/Quickstarts/read-d2c-messages) |
| Python | [read-dec-messages Python](https://github.com/Azure-Samples/azure-iot-samples-python/tree/master/iot-hub/Quickstarts/read-d2c-messages) |

## <a name="next-steps"></a>次のステップ

* IoT Hub のエンドポイントの詳細については、[IoT Hub エンドポイント](iot-hub-devguide-endpoints.md)に関するページをご覧ください。

* [クイック スタート](../iot-develop/quickstart-send-telemetry-iot-hub.md?pivots=programming-language-nodejs)では、device-to-cloud メッセージの送信をシミュレートされたデバイスから行い、組み込みエンドポイントからそのメッセージを読み取る方法を説明します。 

詳細については、[ルートを使用した IoT Hub の device-to-cloud メッセージの処理](tutorial-routing.md)に関するチュートリアルを参照してください。

* デバイスからクラウドへのメッセージをカスタム エンドポイントにルーティングする場合は、「[device-to-cloud メッセージにメッセージ ルートとカスタム エンドポイントを使用する](iot-hub-devguide-messages-read-custom.md)」を参照してください。