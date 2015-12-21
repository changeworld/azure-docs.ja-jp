<properties
 pageTitle="Azure IoT Hub と Azure Event Hubs の比較 | Microsoft Azure"
 description="機能の相違点とユース ケースを中心とした、Azure IoT Hub サービスと Azure Event Hubs サービスの比較です。"
 services="iot-hub"
 documentationCenter=""
 authors="fsautomata"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="10/02/2015"
 ms.author="elioda"/>

# IoT Hub と Event Hubs の比較

デバイスからのテレメトリ データの収集は、Azure IoT Hub の主なユース ケースの 1 つです。このため、IoT Hub は、低遅延の動作と高い信頼性を確保しながら、クラウドに対する膨大なイベントとテレメトリ データの入り口として利用できるイベント取り込みサービスである [Event Hubs][] とよく比較されます。

ただし、サービスには異なる点も多数あります。次のセクションでは、この違いについて詳しく説明します。

| 領域 | IoT Hub | Event Hubs |
| ---- | ------- | ---------- |
| 通信パターン | D2C イベント イングレスと C2D メッセージング。 | イベント イングレスのみ (通常、D2C シナリオのみ)。 |
| セキュリティ | デバイスごとの ID と取り消し可能なアクセス制御。[IoT Hub 開発者ガイド: セキュリティ]に関するページを参照してください。 | Event Hub 全体の[共有アクセス ポリシー][Event Hub - security]。[発行元のポリシー][Event Hub publisher policies]を使用した取り消しが限定的にサポートされています。IoT ソリューションでは、デバイスごとの資格情報となりすまし対策をサポートするために、カスタム ソリューションの実装が求められることがよくあります。 |
| スケール | IoT Hub は、同時接続された数百万のデバイスをサポートするように最適化されています。 | Event Hubs では、同時接続数をさらに制限でき、[Service Bus のクォータ][]に基づいて最大 5,000 の AMQP 接続がサポートされます。一方、Event Hubs を使用すると、送信される各メッセージのパーティションを指定できます。 |
| デバイスの SDK | IoT Hub には、さまざまなプラットフォームと言語を対象とした[デバイス SDK][Azure IoT Hub SDKs] が用意されています。 | Event Hubs は .NET、C でサポートされ、AMQP と HTTP の送信インターフェイスを提供します。 |

まとめると、唯一のユース ケースが D2C テレメトリ イングレスである場合でも、IoT Hub によって IoT デバイス接続用に設計されたサービスを提供し、IoT 固有の機能を使ったシナリオの価値提案を展開し続けることができます。Event Hubs は、大規模なイベント イングレス向けに設計されており、データ センター間およびデータ センター内の両方のシナリオに対応できます。

IoT Hub と Event Hubs を組み合わせて使用し、IoT Hub が D2C 通信を処理してから、Event Hubs が後でリアルタイム処理エンジンへのイベント イングレスを処理できるようにすることはよくあります。

## 次のステップ

Azure IoT Hub についてさらに学習するには、次のリンクを使用してください。

- [IoT Hubs の使用 (チュートリアル)][lnk-get-started]
- [What is Azure IoT Hub? (Azure IoT Hub とは)][]

[Event Hubs]: ../event-hubs/event-hubs-what-is-event-hubs.md
[IoT Hub 開発者ガイド: セキュリティ]: iot-hub-devguide.md#security
[Event Hub - security]: ../event-hubs/event-hubs-authentication-and-security-model-overview.md
[Event Hub publisher policies]: ../event-hubs/event-hubs-overview.md#common-publisher-tasks
[Service Bus のクォータ]: ../service-bus/service-bus-quotas.md
[Azure IoT Hub SDKs]: https://github.com/Azure/azure-iot-sdks/blob/master/readme.md
[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[What is Azure IoT Hub? (Azure IoT Hub とは)]: iot-hub-what-is-iot-hub.md

<!---HONumber=AcomDC_1210_2015-->