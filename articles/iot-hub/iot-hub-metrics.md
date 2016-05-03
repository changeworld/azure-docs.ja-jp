<properties
 pageTitle="IoT Hub の診断メトリック"
 description="Azure IoT Hub メトリックの概要 (ユーザーは自身のリソースの全体的な正常性を評価することができる)"
 services="iot-hub"
 documentationCenter=""
 authors="nberdy"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="04/18/2016"
 ms.author="nberdy"/>

# 診断メトリックの概要

診断メトリックは、サブスクリプション内の Azure リソースの状態に関する有用なデータを提供するためにプッシュされるAzure 全体のメトリックの一部です。メトリックにより、ユーザーはサービスとそれに接続されたデバイスの全体的な正常性を評価することができます。ユーザー向けの統計情報は重要です。この情報は、IoT Hub で起こっていることを確認するのに役立ち、また、Azure サポートに連絡することなく問題の根本原因を特定するための有用な情報となります。

診断メトリックは Azure ポータルから有効にすることができます。

## 診断メトリックを有効にする方法

1. IoT Hub を作成します。IoT Hub の作成方法の手順については、[使用開始][lnk-get-started]に関するガイドを参照してください。

2. IoT Hub のブレードを開きます。ブレードの **[すべての設定]** をクリックし、**[診断]** をクリックします。

    ![][1]

3. 状態を **[オン]** に設定し、診断データを格納するためのストレージ アカウントを選択することで、診断を構成します。メトリックのチェック ボックスをオンにし、**[保存]** をクリックします。事前にストレージ アカウントを作成しておく必要があります。なお、ストレージについては別途課金されます。

    ![][2]

4. 診断をセットアップしたら、IoT Hub のメイン ブレードに戻ります。メトリック情報は、ブレードの **[監視]** セクションに表示されます。グラフをクリックするとメトリック ウィンドウが開きます。ここでは、IoT Hub のメトリック情報の概要を確認し、グラフに表示するメトリックの内容を編集することができます。メトリック値に基づいてアラートを構成することもできます。

    ![][3]

## メトリックとその使用方法

IoT Hub では、ハブの正常性の概要と、ハブに接続されているデバイスの総数を示すためにいくつかのメトリックを提供します。複数のメトリックから得た情報を組み合わせることで、IoT Hub の状態をより大局的に把握することができます。次の表では、各 IoT Hub で追跡されるメトリックを示すと共に、各メトリックが IoT Hub の全体的な状態にどのように関連するかを説明します。

| メトリック | メトリックの説明 | メトリックによって提供される情報 |
| ---- | ---- | ---- |
| d2c.telemetry.ingress.allProtocol | すべてのデバイスで、送信されたメッセージの数 | メッセージ送信に関する概要データ |
| d2c.telemetry.ingress.success | ハブへの取り込みに成功したメッセージの総数 | ハブへの正常なメッセージ取り込みの概要 |
| c2d.commands.egress.complete.success | すべてのデバイスで、受信側のデバイスが完了したコマンド メッセージの総数 | 破棄および拒否に関するメトリックと共に、全体的なC2D コマンドの成功率の概要を示す |
| c2d.commands.egress.abandon.success | すべてのデバイスで、受信側のデバイスが正常に破棄したメッセージの総数 | メッセージの破棄される頻度が想定よりも高い場合は、潜在的な問題を強調表示する |
| c2d.commands.egress.reject.success | すべてのデバイスで、受信側のデバイスが正常に拒否したメッセージの総数 | メッセージの拒否される頻度が想定よりも高い場合は、潜在的な問題を強調表示する |
| devices.totalDevices | IoT Hub に登録されたデバイス数の平均値、最小値、および最大値 | ハブに登録されたデバイスの数 |
| devices.connectedDevices.allProtocol | 同時に接続されたデバイス数の平均値、最小値、および最大値 | ハブに接続されたデバイス数の概要 |

## 次のステップ

診断メトリックの概要については以上です。詳細については、以下のリンク先にアクセスしてください。

- [IoT Hub 操作の監視][lnk-operations-monitoring]
- [IoT Hub のスケーリング][lnk-scaling]
- [IoT Hub の高可用性と障害復旧][lnk-dr]

<!-- Links and images -->
[1]: media/iot-hub-metrics/enable-metrics-1.png
[2]: media/iot-hub-metrics/enable-metrics-2.png
[3]: media/iot-hub-metrics/enable-metrics-3.png

[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[lnk-operations-monitoring]: iot-hub-operations-monitoring.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-dr]: iot-hub-ha-dr.md

<!---HONumber=AcomDC_0420_2016-->