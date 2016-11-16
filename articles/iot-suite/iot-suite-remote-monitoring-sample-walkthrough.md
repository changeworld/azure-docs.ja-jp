---
title: "リモート監視の事前構成済みソリューションのチュートリアル | Microsoft Docs"
description: "Azure IoT リモート監視の事前構成済みソリューションとそのアーキテクチャの説明です。"
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 31fe13af-0482-47be-b4c8-e98e36625855
ms.service: iot-suite
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/17/2016
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 6338750446b33269614c404ecaad8f8192bf1ab2


---
# <a name="remote-monitoring-preconfigured-solution-walkthrough"></a>リモート監視の事前構成済みソリューションのチュートリアル
## <a name="introduction"></a>はじめに
IoT Suite のリモート監視の[事前構成済みソリューション][lnk-preconfigured-solutions]は、遠隔地で運用されている複数のコンピューターを対象としたエンド ツー エンドの監視ソリューションの実装です。 このソリューションは、主要な Azure サービスを組み合わせることで、汎用的なビジネス シナリオの実装環境を実現したものです。これを独自の実装の出発点として利用することができます。 ソリューションを[カスタマイズ][lnk-customize]することで、独自のビジネス要件を満たすことができます。

この記事では、リモート監視ソリューションのしくみについて理解しやすいように、その主な構成要素をいくつか取り上げて説明します。 この知識は以下の作業に役立ちます。

* ソリューションの問題のトラブルシューティングを行う。
* 独自の要件を満たすためにソリューションをカスタマイズする方法を計画する。 
* Azure サービスを利用する独自の IoT ソリューションを設計する。

## <a name="logical-architecture"></a>論理アーキテクチャ
次の図は、事前構成済みソリューションの論理コンポーネントの概要を示したものです。

![論理アーキテクチャ](media/iot-suite-remote-monitoring-sample-walkthrough/remote-monitoring-architecture.png)

## <a name="simulated-devices"></a>シミュレートされたデバイス
事前構成済みソリューションにおいて、シミュレートされたデバイスは冷却装置 (建物のエアコンや施設の空気処理ユニットなど) を表します。 事前構成済みのソリューションをデプロイすると、[Azure WebJob][lnk-webjobs] で動作する 4 つのシミュレートされたデバイスが自動的にプロビジョニングされます。 シミュレーション デバイスを使用することで、ソリューションの動作を簡単に調査することができます。物理デバイスをデプロイする必要はありません。 実際の物理デバイスをデプロイするには、[リモート監視構成済みソリューションへのデバイスの接続][lnk-connect-rm]に関するチュートリアルを参照してください。

シミュレーション デバイスはそれぞれ、次の種類のメッセージを IoT Hub に送信することができます。

| メッセージ | Description |
| --- | --- |
| Startup |デバイスは、起動すると、デバイス自体に関する情報が含まれる **device-info** メッセージをバックエンドに送信します。 このデータには、デバイス ID、デバイス メタデータ、デバイスでサポートされているコマンドの一覧、デバイスの現在の構成が含まれます。 |
| プレゼンス |デバイスは定期的に **presence** メッセージを送信して、センサーの存在を感知できるかどうかを報告します。 |
| テレメトリ |デバイスは定期的に **telemetry** メッセージを送信して、デバイスのシミュレーション センサーから収集した温度と湿度のシミュレーション値を報告します。 |

シミュレーション デバイスから送信される **device-info** メッセージには、次のデバイス プロパティが格納されています。

| プロパティ | 目的 |
| --- | --- |
| Device ID |ソリューションでデバイスが作成されるときに指定されるか、割り当てられる ID。 |
| Manufacturer |デバイスの製造元 |
| Model Number |デバイスのモデル番号 |
| Serial Number |デバイスのシリアル番号 |
| Firmware |デバイスのファームウェアの現在のバージョン |
| Platform |デバイスのプラットフォーム アーキテクチャ |
| Processor |デバイスを実行しているプロセッサ |
| Installed RAM |デバイスに搭載されている RAM の量 |
| Hub Enabled State |デバイスの IoT Hub の状態プロパティ |
| Created Time |ソリューションでデバイスが作成された時刻 |
| Updated Time |デバイスのプロパティが最後に更新された時刻 |
| Latitude |緯度で表したデバイスの位置 |
| Longitude |経度で表したデバイスの位置 |

シミュレーターは、シミュレートされたデバイスのこれらのプロパティをサンプル値と共に送信します。  シミュレーターがシミュレートされたデバイスを初期化するたびに、デバイスは事前定義されたメタデータを IoT Hub に投稿します。 これによりデバイス ポータルで行われたすべてのメタデータの更新がどのように上書きされるか、注目してください。

シミュレーション デバイスは、ソリューション ダッシュボードから IoT Hub を介して送信された次のコマンドを処理することができます。

| コマンド | Description |
| --- | --- |
| PingDevice |デバイスに *ping* を送信して、デバイスが起動しているかどうかを確認します。 |
| StartTelemetry |デバイスのテレメトリ送信を開始します。 |
| StopTelemetry |デバイスのテレメトリ送信を停止します。 |
| ChangeSetPointTemp |ランダムなデータが生成される設定ポイントの値を変更します。 |
| DiagnosticTelemetry |デバイス シミュレーターをトリガーし、追加のテレメトリ値 (externalTemp) を送信します。 |
| ChangeDeviceState |デバイスの拡張状態プロパティを変更し、デバイスからデバイス情報メッセージを送信します。 |

ソリューション バックエンドに対するデバイス コマンドの確認応答は、IoT Hub を介して送信されます。

## <a name="iot-hub"></a>IoT Hub
[IoT Hub][lnk-iothub] は、デバイスから送信されたデータをクラウドに取り込んで、Azure Stream Analytics (ASA) ジョブから利用できる状態にします。 また、デバイスには、デバイス ポータルに代わって IoT Hub からコマンドが送信されます。 ストリームの ASA ジョブは、それぞれ別個の IoT Hub コンシューマー グループを使用して、デバイスから送られたメッセージのストリームを読み取ります。

## <a name="azure-stream-analytics"></a>Azure Stream Analytics
リモート監視ソリューションでは、IoT Hub が受け取ったデバイス メッセージは、[Azure Stream Analytics][lnk-asa] (ASA) によって他のバックエンド コンポーネントにディスパッチされて処理されるか、保存されます。 メッセージの内容に基づく特定の機能が、各 ASA ジョブによって実行されます。

**ジョブ 1: デバイス情報** は、受信メッセージ ストリームからデバイス情報メッセージをフィルター処理し、フィルター処理されたメッセージをイベント ハブ エンドポイントに送信します。 デバイスは、起動時および **SendDeviceInfo** コマンドへの応答時に、デバイス情報メッセージを送信します。 このジョブは、次のクエリ定義を使用して **device-info** メッセージを識別します。

```
SELECT * FROM DeviceDataStream Partition By PartitionId WHERE  ObjectType = 'DeviceInfo'
```

このジョブの出力は、後続の処理のためにイベント ハブに出力されます。

**ジョブ 2: 規則**は、デバイスごとのしきい値に対して、温度と湿度の受信テレメトリ値を評価します。 しきい値の値は、ソリューション ダッシュボードから利用できる規則エディターで設定します。 デバイスと値の各ペアは、**参照データ**として Stream Analytics が読み取る BLOB に、タイムスタンプに基づいて格納されます。 このジョブでは、空以外の値が、デバイスに設定された設定しきい値と比較されます。 ">" 条件を超えた場合、ジョブは **alarm** イベントを出力します。このイベントは、しきい値を超えたことを示し、デバイス、値、タイムスタンプ値を表示します。 このジョブは、アラームのトリガーとなるテレメトリ メッセージを次のクエリ定義によって識別します。

```
WITH AlarmsData AS 
(
SELECT
     Stream.IoTHub.ConnectionDeviceId AS DeviceId,
     'Temperature' as ReadingType,
     Stream.Temperature as Reading,
     Ref.Temperature as Threshold,
     Ref.TemperatureRuleOutput as RuleOutput,
     Stream.EventEnqueuedUtcTime AS [Time]
FROM IoTTelemetryStream Stream
JOIN DeviceRulesBlob Ref ON Stream.IoTHub.ConnectionDeviceId = Ref.DeviceID
WHERE
     Ref.Temperature IS NOT null AND Stream.Temperature > Ref.Temperature

UNION ALL

SELECT
     Stream.IoTHub.ConnectionDeviceId AS DeviceId,
     'Humidity' as ReadingType,
     Stream.Humidity as Reading,
     Ref.Humidity as Threshold,
     Ref.HumidityRuleOutput as RuleOutput,
     Stream.EventEnqueuedUtcTime AS [Time]
FROM IoTTelemetryStream Stream
JOIN DeviceRulesBlob Ref ON Stream.IoTHub.ConnectionDeviceId = Ref.DeviceID
WHERE
     Ref.Humidity IS NOT null AND Stream.Humidity > Ref.Humidity
)

SELECT *
INTO DeviceRulesMonitoring
FROM AlarmsData

SELECT *
INTO DeviceRulesHub
FROM AlarmsData
```

このジョブの出力は、後続の処理のためにイベント ハブに送信され、各アラートの詳細は Blob Storage に保存されます。ソリューション ダッシュボードは、ここからアラート情報を読み取ることができます。

**ジョブ 3: テレメトリ** は、2 とおりの方法で、受信デバイス テレメトリ ストリームに対して動作します。 1 つ目の方法では、デバイスから送信されたすべてのテレメトリ メッセージを長期保管を目的とした永続的な Blob Storage に送信します。 2 つ目の方法では、5 分間のスライディング ウィンドウで湿度の平均値、最小値、最大値を計算し、そのデータを Blob Storage に送信します。 ソリューション ダッシュボードは、Blob Storage からテレメトリ データを読み取ってグラフに反映します。 このジョブは、次のクエリ定義を使用します。

```
WITH 
    [StreamData]
AS (
    SELECT
        *
    FROM [IoTHubStream]
    WHERE
        [ObjectType] IS NULL -- Filter out device info and command responses
) 

SELECT
    IoTHub.ConnectionDeviceId AS DeviceId,
    Temperature,
    Humidity,
    ExternalTemperature,
    EventProcessedUtcTime,
    PartitionId,
    EventEnqueuedUtcTime,
    * 
INTO
    [Telemetry]
FROM
    [StreamData]

SELECT
    IoTHub.ConnectionDeviceId AS DeviceId,
    AVG (Humidity) AS [AverageHumidity],
    MIN(Humidity) AS [MinimumHumidity],
    MAX(Humidity) AS [MaxHumidity],
    5.0 AS TimeframeMinutes 
INTO
    [TelemetrySummary]
FROM [StreamData]
WHERE
    [Humidity] IS NOT NULL
GROUP BY
    IoTHub.ConnectionDeviceId,
    SlidingWindow (mi, 5)
```

## <a name="event-hubs"></a>Event Hubs
ASA ジョブである**デバイス情報**と**規則**は、そのデータを Event Hubs に出力し、WebJob で実行されている**イベント プロセッサ**へと確実に転送します。

## <a name="azure-storage"></a>Azure Storage
このソリューションは、対象となるデバイスから未加工のまま集約されたすべてのテレメトリ データを Azure Blob Storage に永続化します。 ダッシュボードは、Blob Storage からテレメトリ データを読み取ってグラフに反映します。 ダッシュボードにアラートを表示する際は、しきい値の設定をテレメトリ値がいつ上回ったのかを記録した Blob Storage からデータが読み取られます。 また、ダッシュボードで設定したしきい値を記録するときにも Blob Storage が使用されます。

## <a name="webjobs"></a>Web ジョブ
このソリューションの WebJobs は、デバイス シミュレーターに加え、Azure WebJobs 内で実行される**イベント プロセッサ**をホストします。デバイス情報メッセージとコマンドの応答が、このイベント プロセッサによって処理されます。 イベント プロセッサで使用されるものは次のとおりです。

* 現在のデバイス情報を使用してデバイス レジストリ (DocumentDB データベースに格納されています) を更新する場合はデバイス情報メッセージ。
* デバイス コマンドの履歴 (DocumentDB データベースに格納されています) を更新する場合はコマンド応答メッセージ。

## <a name="documentdb"></a>DocumentDB
このソリューションでは、ソリューションに接続されているデバイスに関する情報を格納するために DocumentDB データベースを使用します。 この情報には、デバイス メタデータのほか、ダッシュボードからデバイスに送信されたコマンドの履歴が含まれています。

## <a name="web-apps"></a>Web Apps
### <a name="remote-monitoring-dashboard"></a>リモート監視ダッシュボード
Web アプリケーションのこのページでは、デバイスから送信されたテレメトリ データを、PowerBI JavaScript コントロール ( [PowerBI-visuals リポジトリ](https://www.github.com/Microsoft/PowerBI-visuals)を参照) で視覚化します。 このソリューションは、ASA テレメトリ ジョブを使用して Blob Storage にテレメトリ データを書き込みます。

### <a name="device-administration-portal"></a>デバイス管理ポータル
この Web アプリを使用すると、次のことができます。

* 新しいデバイスをプロビジョニングする。 このアクションによって一意のデバイス ID が設定され、認証キーが生成されます。 デバイスに関する情報は、IoT Hub の ID レジストリとソリューション固有の DocumentDB データベースの両方に書き込まれます。
* デバイスのプロパティを管理する。 このアクションでは、既存のプロパティを表示したり、新しいプロパティで更新したりすることができます。
* デバイスにコマンドを送信する
* デバイスのコマンド履歴を表示する
* デバイスの有効と無効を切り替える。

## <a name="next-steps"></a>次のステップ
以下の TechNet ブログ投稿には、リモート監視の構成済みソリューションについての詳細情報が記載されています。

* [IoT Suite - Under The Hood - Remote Monitoring (IoT スイート - 内部のしくみ - リモート監視)](http://social.technet.microsoft.com/wiki/contents/articles/32941.iot-suite-under-the-hood-remote-monitoring.aspx)
* [IoT Suite - Remote Monitoring - Adding Live and Simulated Devices (IoT スイート - リモート監視 - ライブおよびシミュレートされたデバイスの追加)](http://social.technet.microsoft.com/wiki/contents/articles/32975.iot-suite-remote-monitoring-adding-live-and-simulated-devices.aspx)

引き続き IoT Suite の概要について学習するには、次の記事を参照してください。

* [デバイスをリモート監視構成済みソリューションに接続する][lnk-connect-rm]
* [azureiotsuite.com サイトでのアクセス許可][lnk-permissions]

[lnk-preconfigured-solutions]: iot-suite-what-are-preconfigured-solutions.md
[lnk-customize]: iot-suite-guidance-on-customizing-preconfigured-solutions.md
[lnk-iothub]: https://azure.microsoft.com/documentation/services/iot-hub/
[lnk-asa]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-webjobs]: https://azure.microsoft.com/documentation/articles/websites-webjobs-resources/
[lnk-connect-rm]: iot-suite-connecting-devices.md
[lnk-permissions]: iot-suite-permissions.md



<!--HONumber=Nov16_HO2-->


