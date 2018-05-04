---
title: Azure IoT Hub のカスタム エンドポイントについて | Microsoft Docs
description: 開発者ガイド - カスタム エンドポイントへの device-to-cloud メッセージのルーティングにルーティング ルールを使用します。
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: ''
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/09/2018
ms.author: dobett
ms.openlocfilehash: 3d54da43141dc2bdf34c9f71adc41dc7cf24ff10
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2018
---
# <a name="use-message-routes-and-custom-endpoints-for-device-to-cloud-messages"></a>device-to-cloud メッセージにメッセージ ルートとカスタム エンドポイントを使用する

IoT Hub では、メッセージ プロパティに基づいて、IoT Hub サービス向けのエンドポイントに [device-to-cloud メッセージ][lnk-device-to-cloud]をルーティングできます。 ルーティング ルールによってメッセージ送信を柔軟に実行できるため、メッセージにサービスやカスタム コードを追加する必要はありません。 各ルーティング ルールには、次のプロパティがあります。

| プロパティ      | [説明] |
| ------------- | ----------- |
| **名前**      | ルールを識別する一意の名前。 |
| **ソース**    | 処理するデータ ストリームの元データ。 たとえば、デバイス テレメトリです。 |
| **Condition** | メッセージのヘッダーと本文に対して実行され、エンドポイントと一致するかどうかを確認するルーティング ルールのクエリ式。 ルート条件の構築の詳細については、[リファレンス - デバイス ツインとジョブのクエリ言語][lnk-devguide-query-language]に関する記事を参照してください。 |
| **エンドポイント**  | IoT Hub が条件に一致するメッセージを送信するエンドポイントの名前。 エンドポイントは、IoT Hub と同じリージョン内に存在する必要があり、そうでない場合は、リージョン間の書き込みに対して課金されるおそれがあります。 |

1 つのメッセージが複数のルーティング ルールの条件と一致することがあり、その場合、IoT Hub は一致する各ルールに関連するエンドポイントにメッセージを送信します。 IoT Hub はメッセージ配信の重複を自動的に排除するため、あるメッセージが複数のルールに一致し、それらのルールの配信先が同じであった場合は、配信先には 1 度のみメッセージが書き込まれます。

## <a name="endpoints-and-routing"></a>エンドポイントとルーティング

IoT hub は、既定の[組み込みのエンドポイント][lnk-built-in]を持ちます。 サブスクリプション内の他のサービスをハブにリンクして、メッセージをルーティングするカスタム エンドポイントを作成できます。 IoT Hub は現在、カスタム エンドポイントとして、Azure Storage コンテナー、Event Hubs、Service Bus キュー、Service Bus トピックをサポートします。

ルーティングとカスタム エンドポイントを使用すると、メッセージは、規則に一致しない場合、組み込みのエンドポイントにのみ配信されます。 メッセージを組み込みのエンドポイントとカスタム エンドポイントに配信するには、**イベント** エンドポイントにメッセージを送信するルートを追加します。

> [!NOTE]
> IoT Hub は、Azure Storage コンテナーに BLOB としてデータを書き込む処理のみをサポートしています。

> [!WARNING]
> **セッション**または**重複データ検出**が有効になっている Service Bus のキューおよびトピックは、カスタム エンドポイントとしてはサポートされていません。

IoT Hub でのカスタム エンドポイントの作成の詳細については、[IoT Hub エンドポイント][lnk-devguide-endpoints]に関するページを参照してください。

カスタム エンドポイントからの読み取りの詳細については、以下をご覧ください。

* [Azure Storage コンテナー][lnk-getstarted-storage]から読み取ります。
* [Event Hubs][lnk-getstarted-eh] から読み取ります。
* [Service Bus キュー][lnk-getstarted-queue]から読み取ります。
* [Service Bus トピック][lnk-getstarted-topic]から読み取ります。

## <a name="latency"></a>Latency

組み込みのエンドポイントを使用して device-to-cloud テレメトリ メッセージをルーティングすると、最初のルーティングの作成後にエンド ツー エンドの待機時間が若干上昇します。

ほとんどの場合、待機時間の平均増加は 1 秒未満です。 待機時間は、**d2c.endpoints.latency.builtIn.events** [IoT Hub metric](https://docs.microsoft.com/azure/iot-hub/iot-hub-metrics) を使用して監視できます。 最初のルーティング後にルーティングを作成または削除してもエンド ツー エンドの待機時間には影響しません。

### <a name="next-steps"></a>次の手順

IoT Hub のエンドポイントの詳細については、[IoT Hub エンドポイント][lnk-devguide-endpoints]に関するページをご覧ください。

ルーティング ルールの定義に使用するクエリ言語の詳細については、[デバイス ツイン、ジョブ、およびメッセージ ルーティングの IoT Hub クエリ言語][lnk-devguide-query-language]に関するページをご覧ください。

[ルートを使用した IoT Hub の device-to-cloud メッセージの処理][lnk-d2c-tutorial]に関するチュートリアルでは、ルーティング ルールおよびカスタム エンドポイントを使用する方法を説明します。

[lnk-built-in]: iot-hub-devguide-messages-read-builtin.md
[lnk-device-to-cloud]: iot-hub-devguide-messages-d2c.md
[lnk-devguide-query-language]: iot-hub-devguide-query-language.md
[lnk-devguide-endpoints]: iot-hub-devguide-endpoints.md
[lnk-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md
[lnk-getstarted-eh]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[lnk-getstarted-queue]: ../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md
[lnk-getstarted-topic]: ../service-bus-messaging/service-bus-dotnet-how-to-use-topics-subscriptions.md
[lnk-getstarted-storage]: ../storage/blobs/storage-blobs-introduction.md
