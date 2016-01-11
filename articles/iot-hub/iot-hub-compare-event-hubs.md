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

デバイスからのテレメトリ データの収集は、Azure IoT Hub の主なユース ケースの 1 つです。このため、IoT Hub はしばしば [Azure Event Hubs][] と比較されます。Event Hubs は、低遅延の動作と高い信頼性を確保しながら、クラウドに対する膨大なイベントとテレメトリ データの入口として利用できるイベント処理サービスです。

ただし、サービスには異なる点も多数あります。以下のセクションで、この違いについて詳しく説明します。

| 領域 | IoT Hub | Event Hubs |
| ---- | ------- | ---------- |
| 通信パターン | デバイスからクラウドへのイベント イングレスとクラウドからデバイスへのメッセージングを提供します。 | イベント イングレスのみ提供します (通常はデバイスからクラウドへのシナリオで考慮されます)。 |
| セキュリティ | デバイスごとの ID と取り消し可能なアクセス制御を提供します。IoT Hub 開発者ガイドの「[セキュリティ]」セクションを参照してください。 | Event Hubs 全体の[共有アクセス ポリシー][Event Hub - security]を提供します。[発行元のポリシー][Event Hub publisher policies]による取り消しが限定的にサポートされます。モノのインターネット (IoT) ソリューションでは、デバイスごとの資格情報となりすまし対策をサポートするために、カスタム ソリューションの実装が求められることがよくあります。 |
| スケール | 同時接続された数百万のデバイスをサポートするように最適化されています。 | 制限された数の同時接続をサポートできます。[Azure Service Bus のクォータ][]に基づいて最大 5,000 の AMQP 接続をサポートします。一方、Event Hubs を使用すると、送信される各メッセージのパーティションを指定できます。 |
| デバイスの SDK | さまざまなプラットフォームと言語を対象とした[デバイス SDK][Azure IoT Hub SDKs] が用意されています。 | .NET と C でサポートされます。AMQP と HTTP の送信インターフェイスも提供します。 |

まとめると、唯一のユース ケースがデバイスからクラウドへのテレメトリ イングレスである場合でも、IoT Hub は IoT デバイス接続用に特に設計されたサービスを提供します。これらのシナリオのための価値提案は IoT 固有の機能によって引き続き拡張されます。Event Hubs は、大規模なイベント イングレス向けに設計されており、データ センター内およびデータ センター間の両方のシナリオに対応できます。

IoT Hub と Event Hubs を組み合わせて使用することも珍しいことではありません。この場合、IoT Hub がデバイスからクラウドへの通信を処理し、Event Hubs が後半のリアルタイム処理エンジンへのイベント イングレスを処理します。

## 次のステップ

Azure IoT Hub についてさらに学習するには、次のリンクを使用してください。

- [Azure IoT Hub の使用 (チュートリアル)][lnk-get-started]
- [What is Azure IoT Hub? (Azure IoT Hub とは)][]

[Azure Event Hubs]: ../event-hubs/event-hubs-what-is-event-hubs.md
[セキュリティ]: iot-hub-devguide.md#security
[Event Hub - security]: ../event-hubs/event-hubs-authentication-and-security-model-overview.md
[Event Hub publisher policies]: ../event-hubs/event-hubs-overview.md#common-publisher-tasks
[Azure Service Bus のクォータ]: ../service-bus/service-bus-quotas.md
[Azure IoT Hub SDKs]: https://github.com/Azure/azure-iot-sdks/blob/master/readme.md
[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[What is Azure IoT Hub? (Azure IoT Hub とは)]: iot-hub-what-is-iot-hub.md

<!---HONumber=AcomDC_1223_2015-->