---
title: Azure IoT Hub 組み込みエンドポイントについて | Microsoft Docs
description: 開発者ガイド - デバイスからクラウドへのメッセージを読むためにイベント ハブと互換性のある組み込みのエンドポイントを使用する方法を説明します。
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/18/2018
ms.author: dobett
ms.openlocfilehash: 912bb0dd3e48e53134ad848119ae7428b380b88d
ms.sourcegitcommit: b9786bd755c68d602525f75109bbe6521ee06587
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/18/2018
ms.locfileid: "39124938"
---
# <a name="read-device-to-cloud-messages-from-the-built-in-endpoint"></a>デバイスからクラウドへのメッセージを組み込みのエンドポイントから読み取る

既定では、メッセージは [Event Hubs][lnk-event-hubs] と互換性のある組み込みサービス向けエンドポイント (**messages/events**) にルーティングされます。 このエンドポイントは、現在、[AMQP][lnk-amqp] プロトコルを使用してポート 5671 のみで公開されています。 IoT Hub は、Event Hub と互換性のある組み込みのメッセージング エンドポイント **messages/events** を制御するための以下のプロパティを公開しています。

| プロパティ            | 説明 |
| ------------------- | ----------- |
| **パーティション数** | このプロパティは作成時に設定し、D2C イベントを取り込む場合の[パーティション][lnk-event-hub-partitions]数を定義します。 |
| **リテンション期間**  | このプロパティは、IoT Hub によってメッセージが保持される期間を日数で指定します。 既定は 1 日ですが、7 日間に増やすことができます。 |

IoT Hub では、組み込みの D2C 受信エンドポイントでコンシューマー グループを管理ができます。

既定では、メッセージのルーティング ルールと明示的に一致しないメッセージはすべて、組み込みエンドポイントに書き込まれます。 このフォールバック ルートを無効にした場合は、どのメッセージのルーティング ルールにも明示的に一致しないメッセージは破棄されます。

リテンション期間は、[IoT Hub のリソース プロバイダー REST API][lnk-resource-provider-apis] を使用してプログラムにより変更するか、[Azure Portal][lnk-management-portal] を使用して変更できます。

IoT Hub は、**messages/events** 組み込みエンドポイントをバックエンド サービスに公開して、ハブで受信した D2C メッセージを読み取ります。 このエンドポイントには Event Hubs との互換性があり、Event Hubs のサービスがメッセージを読み取るためにサポートするすべてのメカニズムを使用できます。

## <a name="read-from-the-built-in-endpoint"></a>組み込みエンドポイントから読み取る

[Azure Service Bus SDK for .NET][lnk-servicebus-sdk] または [Event Hubs - イベント プロセッサ ホスト][lnk-eventprocessorhost]を使用する場合、適切なアクセス許可があれば任意の IoT Hub の接続文字列を使用することができます。 これにより、 **メッセージやイベント** を Event Hub の名前として使用できます。

IoT Hub を認識しない SDK (または製品統合) を使用する場合は、イベント ハブ互換性エンドポイントとイベント ハブ互換名を取得する必要があります。

1. [Azure Portal][lnk-management-portal] にサインインし、IoT ハブに移動します。
1. **[エンドポイント]** をクリックします。
1. **[Built-in endpoints]** (組み込みエンドポイント) セクションで、**[イベント]** をクリックします。 
1. プロパティ ページが開き、**[イベント ハブ互換エンドポイント]**、**[イベント ハブ互換名]**、**[パーティション]**、**[保持期間]**、**[コンシューマー グループ]** の各値が表示されます。

    ![Device-to-cloud settings][img-eventhubcompatible]

IoT Hub SDK には、IoT Hub エンドポイント名 (**[エンドポイント]** に表示されている **messages/events**) が必要です。

SDK で **[Hostname]** (ホスト名) または **[Namespace]** (名前空間) の値が必要な場合は、**[Event Hub-compatible endpoint]** (イベント ハブと互換性のあるエンドポイント) からスキームを削除します。 たとえば、イベント ハブ互換エンドポイントが **sb://iothub-ns-myiothub-1234.servicebus.windows.net/** の場合、**ホスト名** は **iothub-ns-myiothub-1234.servicebus.windows.net** になります。 **名前空間**は **iothub-ns-myiothub-1234** になります。

この場合、指定したイベント ハブに接続するための **ServiceConnect** のアクセス許可を持つ、共有アクセス ポリシーを使用できます。

前の情報を使用してイベント ハブの接続文字列を作成する必要がある場合は、次のパターンを使用できます。

`Endpoint={Event Hub-compatible endpoint};SharedAccessKeyName={iot hub policy name};SharedAccessKey={iot hub policy key}`

IoT Hub から公開されている、Event Hub 互換のエンドポインで使用できる SDK と統合には、次のリストの項目が含まれます。

* [Event Hubs の Java クライアント](https://github.com/Azure/azure-event-hubs-java)。
* [Apache Storm のスパウト](../hdinsight/storm/apache-storm-develop-csharp-event-hub-topology.md)。 [スパウトのソース](https://github.com/apache/storm/tree/master/external/storm-eventhubs) は GitHub で確認できます。
* [Apache Spark 統合](../hdinsight/spark/apache-spark-eventhub-streaming.md)。

## <a name="next-steps"></a>次の手順

IoT Hub のエンドポイントの詳細については、[IoT Hub エンドポイント][lnk-endpoints]に関するページをご覧ください。

[クイック スタート][lnk-get-started]では、デバイスからクラウドへのメッセージをシミュレートされたデバイスから送信し、組み込みエンドポイントからそのメッセージを読み取る方法を説明します。 詳細については、[ルートを使用した IoT Hub の device-to-cloud メッセージの処理][lnk-d2c-tutorial]に関するチュートリアルをご覧ください。

デバイスからクラウドへのメッセージをカスタム エンドポイントにルーティングする場合は、「[デバイスからクラウドへのメッセージにメッセージ ルートとカスタム エンドポイントを使用する][lnk-custom]」を参照してください。

[img-eventhubcompatible]: ./media/iot-hub-devguide-messages-read-builtin/eventhubcompatible.png

[lnk-custom]: iot-hub-devguide-messages-read-custom.md
[lnk-get-started]: quickstart-send-telemetry-node.md
[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-resource-provider-apis]: https://docs.microsoft.com/rest/api/iothub/iothubresource
[lnk-event-hubs]: http://azure.microsoft.com/documentation/services/event-hubs/
[lnk-management-portal]: https://portal.azure.com
[lnk-d2c-tutorial]: tutorial-routing.md
[lnk-event-hub-partitions]: ../event-hubs/event-hubs-features.md#partitions
[lnk-servicebus-sdk]: https://www.nuget.org/packages/WindowsAzure.ServiceBus
[lnk-eventprocessorhost]: http://blogs.msdn.com/b/servicebus/archive/2015/01/16/event-processor-host-best-practices-part-1.aspx
[lnk-amqp]: https://www.amqp.org/
