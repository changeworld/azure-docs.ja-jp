<properties
 pageTitle="リモート監視の事前構成済みソリューションのチュートリアル | Microsoft Azure"
 description="Azure IoT リモート監視の事前構成済みソリューションとそのアーキテクチャの説明です。"
 services=""
 suite="iot-suite"
 documentationCenter=""
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-suite"
 ms.devlang="na"
 ms.topic="get-started-article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="07/18/2016"
 ms.author="dobett"/>

# リモート監視の事前構成済みソリューションのチュートリアル

## はじめに

IoT Suite リモート監視の[事前構成済みソリューション][lnk-preconfigured-solutions]は、遠隔地で実行されている複数のコンピューターを対象としたエンド ツー エンドの監視ソリューションの実装です。このソリューションは、主要な Azure サービスを組み合わせることで、汎用的なビジネス シナリオの実装環境を実現したものです。これを独自の実装の出発点として利用することができます。ソリューションを[カスタマイズ][lnk-customize]することで、独自のビジネス要件を満たすことができます。

この記事では、リモート監視ソリューションのしくみについて理解しやすいように、その主な構成要素をいくつか取り上げて説明します。その知識は、ソリューションに関する問題のトラブルシューティングや、独自の要件に合わせたソリューションのカスタマイズ計画、Azure サービスを使用する独自の IoT ソリューションの計画に役立てることができます。

## 論理アーキテクチャ

次の図は、事前構成済みソリューションの論理コンポーネントの概要を示したものです。

![論理アーキテクチャ](media/iot-suite-remote-monitoring-sample-walkthrough/remote-monitoring-architecture.png)


## シミュレートされたデバイス

事前構成済みソリューションにおいて、シミュレートされたデバイスは冷却装置 (建物のエアコンや施設の空気処理ユニットなど) を表します。事前構成済みのソリューションをデプロイすると、[Azure WebJob][lnk-webjobs] で動作する 4 つのシミュレーション デバイスが自動的にプロビジョニングされます。シミュレーション デバイスを使用することで、ソリューションの動作を簡単に調査することができます。物理デバイスをデプロイする必要はありません。実際の物理デバイスをデプロイするには、チュートリアル「[デバイスをリモート監視構成済みソリューションに接続する][lnk-connectyourdevice]」を参照してください。

シミュレーション デバイスはそれぞれ、次の種類のメッセージを IoT Hub に送信することができます。

| メッセージ | 説明 |
|----------|-------------|
| Startup | デバイスは起動時に、デバイス ID、デバイス メタデータ、サポートするコマンドの一覧、現在の構成など、デバイス自体に関する情報を含む **device-info** メッセージを送信します。 |
| プレゼンス | デバイスは定期的に **presence** メッセージを送信して、センサーの存在を感知できるかどうかを報告します。 |
| テレメトリ | デバイスは定期的に **telemetry** メッセージを送信して、シミュレーション デバイスに接続されているシミュレーション センサーから収集した温度と湿度のシミュレーション値を報告します。 |


シミュレーション デバイスから送信される **device-info** メッセージには、次のデバイス プロパティが格納されています。

| プロパティ | 目的 |
|------------------------|--------- |
| Device ID | ソリューションでデバイスが作成されるときに指定されるか、割り当てられる ID。 |
| Manufacturer | デバイスの製造元 |
| Model Number | デバイスのモデル番号 |
| Serial Number | デバイスのシリアル番号 |
| Firmware | デバイスのファームウェアの現在のバージョン |
| Platform | デバイスのプラットフォーム アーキテクチャ |
| Processor | デバイスを実行しているプロセッサ |
| Installed RAM | デバイスに搭載されている RAM の量 |
| Hub Enabled State | デバイスの IoT Hub の状態プロパティ |
| Created Time | ソリューションでデバイスが作成された時刻 |
| Updated Time | デバイスのプロパティが最後に更新された時刻 |
| Latitude | 緯度で表したデバイスの位置 |
| Longitude | 経度で表したデバイスの位置 |

シミュレーターは、シミュレートされたデバイスのこれらのプロパティをサンプル値と共に送信します。シミュレーターがシミュレートされたデバイスを初期化するたびに、デバイスは事前定義されたメタデータを IoT Hub に投稿します。これにより、デバイス ポータルで行われたすべてのメタデータの更新が上書きされることに注意してください。


シミュレーション デバイスは、ソリューション ダッシュボードから IoT Hub を介して送信された次のコマンドを処理することができます。

| コマンド | 説明 |
|------------------------|-----------------------------------------------------|
| PingDevice | デバイスに _ping_ を送信して、デバイスが起動しているかどうかを確認します。 |
| StartTelemetry | デバイスのテレメトリ送信を開始します。 |
| StopTelemetry | デバイスのテレメトリ送信を停止します。 |
| ChangeSetPointTemp | ランダムなデータが生成される設定ポイントの値を変更します。 |
| DiagnosticTelemetry | デバイス シミュレーターをトリガーし、追加のテレメトリ値 (externalTemp) を送信します。 |
| ChangeDeviceState | デバイスの拡張状態プロパティを変更し、デバイスからデバイス情報メッセージを送信します。 |

ソリューション バックエンドに対するデバイス コマンドの確認応答は、IoT Hub を介して送信されます。

## IoT Hub

[IoT Hub][lnk-iothub] は、デバイスから送信されたデータをクラウドに取り込んで、Azure Stream Analytics (ASA) ジョブから利用できる状態にします。また、デバイスには、デバイス ポータルに代わって IoT Hub からコマンドが送信されます。ストリームの ASA ジョブは、それぞれ別個の IoT Hub コンシューマー グループを使用して、デバイスから送られたメッセージのストリームを読み取ります。

## Azure Stream Analytics

リモート監視ソリューションでは、IoT Hub がデバイスから受け取ったメッセージは、[Azure Stream Analytics][lnk-asa] (ASA) によって他のバックエンド コンポーネントにディスパッチされて処理されるか、保存されます。メッセージの内容に基づく特定の機能が、各 ASA ジョブによって実行されます。

**ジョブ 1: デバイス情報**は、受信するメッセージ ストリームからデバイス情報メッセージをフィルター処理し、フィルター処理されたメッセージをイベント ハブ エンドポイントに送信します。デバイスは、起動時および **SendDeviceInfo** コマンドへの応答時に、デバイス情報メッセージを送信します。このジョブは、次のクエリ定義を使用して **device-info** メッセージを識別します。

```
SELECT * FROM DeviceDataStream Partition By PartitionId WHERE  ObjectType = 'DeviceInfo'
```

このジョブの出力は、後続の処理のためにイベント ハブに出力されます。

**ジョブ 2: 規則**は、デバイスごとのしきい値に対する温度と湿度の受信テレメトリ値を評価します。しきい値の値は、ソリューション ダッシュボードから利用できる規則エディターで設定します。デバイスと値の各ペアは、**参照データ**として Stream Analytics に読み込まれる BLOB に、タイムスタンプに基づいて格納されます。このジョブでは、空以外の値が、デバイスに設定された設定しきい値と比較されます。">" 条件を超えた場合、ジョブは **alarm** イベントを出力します。このイベントは、しきい値を超えたことを示し、デバイス、値、タイムスタンプ値を表示します。このジョブは、アラームのトリガーとなるテレメトリ メッセージを次のクエリ定義によって識別します。

```
WITH AlarmsData AS 
(
SELECT
     Stream.DeviceID,
     'Temperature' as ReadingType,
     Stream.Temperature as Reading,
     Ref.Temperature as Threshold,
     Ref.TemperatureRuleOutput as RuleOutput,
     Stream.EventEnqueuedUtcTime AS [Time]
FROM IoTTelemetryStream Stream
JOIN DeviceRulesBlob Ref ON Stream.DeviceID = Ref.DeviceID
WHERE
     Ref.Temperature IS NOT null AND Stream.Temperature > Ref.Temperature

UNION ALL

SELECT
     Stream.DeviceID,
     'Humidity' as ReadingType,
     Stream.Humidity as Reading,
     Ref.Humidity as Threshold,
     Ref.HumidityRuleOutput as RuleOutput,
     Stream.EventEnqueuedUtcTime AS [Time]
FROM IoTTelemetryStream Stream
JOIN DeviceRulesBlob Ref ON Stream.DeviceID = Ref.DeviceID
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

**ジョブ 3: テレメトリ**は、2 つの方法で、受信するデバイス テレメトリ ストリームに対して動作します。1 つ目の方法では、デバイスから送信されたすべてのテレメトリ メッセージを長期保管を目的とした永続的な Blob Storage に送信します。2 つ目の方法では、5 分間のスライディング ウィンドウで湿度の平均値、最小値、最大値を計算し、そのデータを Blob Storage に送信します。ソリューション ダッシュボードは、Blob Storage からテレメトリ データを読み取ってグラフに反映します。このジョブは、次のクエリ定義を使用します。

```
WITH 
    [StreamData]
AS (
    SELECT
        *
    FROM 
      [IoTHubStream] 
    WHERE
        [ObjectType] IS NULL -- Filter out device info and command responses
) 

SELECT
    *
INTO
    [Telemetry]
FROM
    [StreamData]

SELECT
    DeviceId,
    AVG (Humidity) AS [AverageHumidity], 
    MIN(Humidity) AS [MinimumHumidity], 
    MAX(Humidity) AS [MaxHumidity], 
    5.0 AS TimeframeMinutes 
INTO
    [TelemetrySummary]
FROM
    [StreamData]
WHERE
    [Humidity] IS NOT NULL
GROUP BY
    DeviceId, 
    SlidingWindow (mi, 5)
```

## Event Hubs

ASA ジョブである "**デバイス情報**" と "**規則**" は、そのデータを Event Hubs に出力し、WebJob で実行されている**イベント プロセッサ**へと確実に転送します。

## Azure Storage

このソリューションは、対象となるデバイスから未加工のまま集約されたすべてのテレメトリ データを Azure Blob Storage に永続化します。ダッシュボードは、Blob Storage からテレメトリ データを読み取ってグラフに反映します。ダッシュボードにアラートを表示する際は、しきい値の設定をテレメトリ値がいつ上回ったのかを記録した Blob Storage からデータが読み取られます。また、ユーザーがダッシュボードで設定したしきい値を記録するときにも Blob Storage が使用されます。

## Web ジョブ

このソリューションの WebJobs は、デバイス シミュレーターに加え、Azure WebJobs 内で実行される**イベント プロセッサ**をホストします。デバイス情報メッセージとコマンドの応答が、このイベント プロセッサによって処理されます。イベント プロセッサで使用されるものは次のとおりです。

- 現在のデバイス情報を使用してデバイス レジストリ (DocumentDB データベースに格納されています) を更新する場合はデバイス情報メッセージ。
- デバイス コマンドの履歴 (DocumentDB データベースに格納されています) を更新する場合はコマンド応答メッセージ。

## DocumentDB

このソリューションに接続されたデバイスの情報 (ダッシュボードからデバイスに送信されたコマンドの履歴やデバイスのメタデータなど) は、DocumentDB データベースに格納されます。

## Web Apps

### リモート監視ダッシュボード
Web アプリでは、デバイスから送信されたテレメトリ データを、PowerBI JavaScript コントロール ([PowerBI-visuals リポジトリ](https://www.github.com/Microsoft/PowerBI-visuals)を参照) で視覚化して、このページに表示します。このソリューションは、ASA テレメトリ ジョブを使用して Blob Storage にテレメトリ データを書き込みます。


### デバイス管理ポータル

この Web アプリを使用すると、次のことができます。

- 新しいデバイスをプロビジョニングする。これによって一意のデバイス ID が設定され、認証キーが生成されます。デバイスに関する情報は、IoT Hub の ID レジストリとソリューション固有の DocumentDB データベースの両方に書き込まれます。
- デバイスのプロパティを管理する。たとえば既存のプロパティを表示したり、新しいプロパティで更新したりすることができます。
- デバイスにコマンドを送信する
- デバイスのコマンド履歴を表示する
- デバイスの有効と無効を切り替える。

## 次のステップ

以下の TechNet ブログ投稿には、リモート監視の構成済みソリューションについての詳細情報が記載されています。

- [IoT Suite - Under The Hood - Remote Monitoring (IoT スイート - 内部のしくみ - リモート監視)](http://social.technet.microsoft.com/wiki/contents/articles/32941.iot-suite-under-the-hood-remote-monitoring.aspx)
- [IoT Suite - Remote Monitoring - Adding Live and Simulated Devices (IoT スイート - リモート監視 - ライブおよびシミュレートされたデバイスの追加)](http://social.technet.microsoft.com/wiki/contents/articles/32975.iot-suite-remote-monitoring-adding-live-and-simulated-devices.aspx)


[lnk-preconfigured-solutions]: iot-suite-what-are-preconfigured-solutions.md
[lnk-customize]: iot-suite-guidance-on-customizing-preconfigured-solutions.md
[lnk-connectyourdevice]: iot-suite-connecting-devices.md
[lnk-iothub]: https://azure.microsoft.com/documentation/services/iot-hub/
[lnk-asa]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-webjobs]: https://azure.microsoft.com/documentation/articles/websites-webjobs-resources/

<!---HONumber=AcomDC_0720_2016-->