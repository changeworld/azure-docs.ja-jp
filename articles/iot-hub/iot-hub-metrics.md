---
title: "メトリックを使用して Azure IoT Hub を監視する | Microsoft Docs"
description: "Azure IoT Hub メトリックを使用して、IoT Hub の全体的な正常性を評価および監視する方法。"
services: iot-hub
documentationcenter: 
author: nberdy
manager: timlt
editor: 
ms.assetid: a47108fd-f994-4105-b21d-5b8f697b699c
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/16/2016
ms.author: nberdy
translationtype: Human Translation
ms.sourcegitcommit: 64e69df256404e98f6175f77357500b562d74318
ms.openlocfilehash: e8cac4af4b971320429cc4c76b8d806e314e1143


---
# <a name="iot-hub-metrics"></a>IoT Hub メトリック
IoT Hub メトリックは、Azure サブスクリプション内の Azure IoT リソースの状態に関する有用なデータを提供します。 IoT Hub メトリックにより、ユーザーは IoT Hub サービスとそれに接続されたデバイスの全体的な正常性を評価することができます。 ユーザー向けの統計情報は重要です。この情報は、IoT Hub で起こっていることを確認するのに役立ち、また、Azure サポートに連絡することなく問題の根本原因を特定するための有用な情報となります。

メトリックは既定で有効になっています。 IoT Hub メトリックは Azure Portal で表示できます。

## <a name="how-to-view-iot-hub-metrics"></a>IoT Hub メトリックを表示する方法
1. IoT Hub を作成します。 IoT ハブの作成方法の手順については、[使用開始][lnk-get-started]に関するガイドを参照してください。
2. IoT Hub のブレードを開きます。 そこで **[メトリック]** をクリックします。
   
    ![][1]
3. メトリック ブレードから、IoT Hub のメトリックを表示し、メトリックのカスタム ビューを作成できます。 メトリック データを Event Hubs エンドポイントまたは Azure ストレージ アカウントに送信するように選択するには、**[診断設定]** をクリックします。
   
    ![][2]

## <a name="iot-hub-metrics-and-how-to-use-them"></a>IoT Hub メトリックとその使用方法
IoT Hub には、ハブの正常性の概要と、ハブに接続されているデバイスの総数を示すいくつかのメトリックが用意されています。 複数のメトリックから得た情報を組み合わせることで、IoT Hub の状態をより大局的に把握することができます。 次の表では、各 IoT Hub で追跡されるメトリックを示すと共に、各メトリックが IoT Hub の全体的な状態にどのように関連するかを説明します。

| メトリック | メトリックの説明 | メトリックによって提供される情報 |
| --- | --- | --- |
| d2c.telemetry.ingress.allProtocol | すべてのデバイスで、送信されたメッセージの数 | メッセージ送信に関する概要データ |
| d2c.telemetry.ingress.success | ハブへの取り込みに成功したメッセージの総数 | ハブへの正常なメッセージ取り込みの概要 |
| d2c.telemetry.egress.success | 成功したエンドポイントへの全書き込みの回数 | ユーザーのルートに基づいたメッセージ ファンアウトの概要 |
| d2c.telemetry.egress.invalid | エンドポイントとの互換性がないために配信されなかったメッセージの数 | エンドポイントのユーザーのセットに書き込まれた失敗の数の概要。 値が大きい場合は、不適切に構成されたエンドポイントを示す可能性があります。 |
| d2c.telemetry.egress.dropped | エンドポイントが異常な状態であるために破棄されたメッセージの数 | IoT Hub が現在の構成の場合に、破棄されたメッセージの数の概要 |
| d2c.telemetry.egress.fallback | フォールバック ルートに一致するメッセージの数 | 組み込みエンドポイント以外の他のエンドポイントに、すべてのメッセージをパイプするユーザーの場合、このメトリックにはルーティング セットアップのギャップが表示されます |
| d2c.telemetry.egress.orphaned | フォールバック ルートを含め、すべてのルートと一致しないメッセージの数 | IoT Hub が現在の構成の場合に、孤立したメッセージの数の概要 |
| d2c.endpoints.latency.eventHubs | IoT ハブの受信メッセージとイベント ハブ エンドポイントの受信メッセージの間の平均待機時間 (ミリ秒) | 分散させることで、ユーザーが不適切なエンドポイント構成を特定する際に役立ちます |
| d2c.endpoints.latency.serviceBusQueues | IoT ハブの受信メッセージと Service Bus キュー エンドポイントの受信メッセージの間の平均待機時間 (ミリ秒) | 分散させることで、ユーザーが不適切なエンドポイント構成を特定する際に役立ちます |
| d2c.endpoints.latency.serviceBusTopic | IoT ハブの受信メッセージと Service Bus トピック エンドポイントの受信メッセージの間の平均待機時間 (ミリ秒) | 分散させることで、ユーザーが不適切なエンドポイント構成を特定する際に役立ちます |
| d2c.endpoints.latency.builtIn.events | IoT ハブの受信メッセージと組み込みエンドポイント (メッセージ/イベント) の受信メッセージの間の平均待機時間 (ミリ秒) | 分散させることで、ユーザーが不適切なエンドポイント構成を特定する際に役立ちます |
| c2d.commands.egress.complete.success | すべてのデバイスで、受信側のデバイスが完了したコマンド メッセージの総数 |破棄および拒否に関するメトリックと共に、全体的なクラウドからデバイスへのコマンドの成功率の概要を示す |
| c2d.commands.egress.abandon.success | すべてのデバイスで、受信側のデバイスが正常に破棄したメッセージの総数 |メッセージの破棄される頻度が想定よりも高い場合は、潜在的な問題を強調表示する |
| c2d.commands.egress.reject.success | すべてのデバイスで、受信側のデバイスが正常に拒否したメッセージの総数 |メッセージの拒否される頻度が想定よりも高い場合は、潜在的な問題を強調表示する |
| devices.totalDevices | IoT ハブに登録されたデバイスの数のカウント |ハブに登録されたデバイスの数 |
| devices.connectedDevices.allProtocol | 同時に接続されたデバイス数のカウント |ハブに接続されたデバイス数の概要 |

## <a name="next-steps"></a>次のステップ
IoT Hub メトリックの概要については以上です。Azure IoT Hub の管理に関する詳細については、次のリンク先にアクセスしてください。

* [操作の監視][lnk-monitor]

IoT Hub の機能を詳しく調べるには、次のリンクを使用してください。

* [IoT Hub 開発者ガイド][lnk-devguide]
* [IoT Gateway SDK を使用したデバイスのシミュレーション][lnk-gateway]

<!-- Links and images -->
[1]: media/iot-hub-metrics/enable-metrics-1.png
[2]: media/iot-hub-metrics/enable-metrics-2.png

[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[lnk-operations-monitoring]: iot-hub-operations-monitoring.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-dr]: iot-hub-ha-dr.md

[lnk-monitor]: iot-hub-operations-monitoring.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md



<!--HONumber=Jan17_HO4-->


