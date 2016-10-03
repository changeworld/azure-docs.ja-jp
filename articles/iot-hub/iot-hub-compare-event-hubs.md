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
 ms.date="06/06/2016"
 ms.author="elioda"/>

# IoT Hub と Event Hubs の比較

デバイスからのテレメトリ データの収集は、Azure IoT Hub の主なユース ケースの 1 つです。このため、IoT Hub はしばしば [Azure Event Hubs][] と比較されます。IoT Hub のように、Event Hubs は、低遅延の動作と高い信頼性を確保しながら、クラウドに対する膨大なイベントとテレメトリ データの入口として利用できるイベント処理サービスです。

ただし、サービスには異なる点も多数あります。以下の表で、この違いについて詳しく説明します。

| 領域 | IoT Hub | Event Hubs |
| ---- | ------- | ---------- |
| 通信パターン | デバイスからクラウドおよびクラウドからデバイスへのメッセージを有効にします。 | イベント イングレスのみを有効にします (通常はデバイスからクラウドへのシナリオの対象になります)。 |
| デバイスのプロトコルのサポート | MQTT、AMQP、AMQP over WebSockets、および HTTP/1 をサポートします。さらに、IoT Hub は [Azure IoT プロトコル ゲートウェイ][lnk-azure-protocol-gateway] \(カスタム プロトコルをサポートする、カスタマイズ可能なプロトコル ゲートウェイの実装) でも動作します。 | AMQP、AMQP over WebSockets、および HTTP/1 をサポートします。 |
| セキュリティ | デバイスごとの ID と取り消し可能なアクセス制御を提供します。IoT Hub 開発者ガイドの「[セキュリティ]」セクションを参照してください。 | Event Hubs 全体の[共有アクセス ポリシー][Event Hub - security]を提供します。[発行元のポリシー][Event Hub publisher policies]による取り消しが限定的にサポートされます。IoT ソリューションでは、デバイスごとの資格情報となりすまし対策をサポートするために、カスタム ソリューションの実装が求められることがよくあります。 |
| 操作の監視 | デバイス ID 管理および接続の豊富なイベント (個々のデバイス認証エラー、スロットル、および不適切な形式の例外など) を、IoT ソリューションでサブスクライブできるようにします。これらのイベントを使用すれば、個々のデバイス レベルで接続の問題をすばやく識別することができます。 | 集計メトリックのみを公開します。 |
| スケール | 同時接続された数百万のデバイスをサポートするように最適化されています。 | 制限された数の同時接続をサポートできます。[Azure Service Bus のクォータ][]に基づいて最大 5,000 の AMQP 接続をサポートします。一方、Event Hubs を使用すると、送信される各メッセージのパーティションを指定できます。 |
| デバイスの SDK | さまざまなプラットフォームと言語を対象とした[デバイス SDK][Azure IoT Hub SDKs] が用意されています。 | .NET と C でサポートされます。AMQP と HTTP の送信インターフェイスも提供します。 |
| ファイルのアップロード | IoT ソリューションでデバイスからクラウドにファイルをアップロードできるようにします。ワークフロー統合用のファイル通知エンドポイントと、デバッグ サポート用の操作監視カテゴリが含まれます。 | 要求チェック パターンを使用して、デバイスにファイルを手動で要求し、トランザクションのストレージ キーをデバイスに提供します。 |

まとめると、唯一のユース ケースがデバイスからクラウドへのテレメトリ イングレスである場合でも、IoT Hub は IoT デバイス接続用に特に設計されたサービスを提供します。これらのシナリオのための価値提案は IoT 固有の機能によって引き続き拡張されます。Event Hubs は、大規模なイベント イングレス向けに設計されており、データ センター内およびデータ センター間の両方のシナリオに対応できます。

IoT Hub と Event Hubs の両方を同じ解決方法で使用することも珍しいことではありません。この場合、IoT Hub がデバイスからクラウドへの通信を処理し、Event Hubs が後半のリアルタイム処理エンジンへのイベント イングレスを処理します。

## 次のステップ

IoT Hub のデプロイの計画に関する詳細については、[HA と DR のスケーリング][lnk-scaling]に関するページをご覧ください。

IoT Hub の機能を詳しく調べるには、次のリンクを使用してください。

- [開発者ガイド][lnk-devguide]
- [サンプル UI を使用したデバイス管理の探求][lnk-dmui]
- [Gateway SDK を使用したデバイスのシミュレーション][lnk-gateway]
- [Azure ポータルを使用した IoT Hub の管理][lnk-portal]

[Azure Event Hubs]: ../event-hubs/event-hubs-what-is-event-hubs.md
[セキュリティ]: iot-hub-devguide.md#security
[Event Hub - security]: ../event-hubs/event-hubs-authentication-and-security-model-overview.md
[Event Hub publisher policies]: ../event-hubs/event-hubs-overview.md#common-publisher-tasks
[Azure Service Bus のクォータ]: ../service-bus/service-bus-quotas.md
[Azure IoT Hub SDKs]: https://github.com/Azure/azure-iot-sdks/blob/master/readme.md
[lnk-azure-protocol-gateway]: iot-hub-protocol-gateway.md

[lnk-scaling]: iot-hub-scaling.md
[lnk-devguide]: iot-hub-devguide.md
[lnk-dmui]: iot-hub-device-management-ui-sample.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
[lnk-portal]: iot-hub-manage-through-portal.md

<!---HONumber=AcomDC_0907_2016-->