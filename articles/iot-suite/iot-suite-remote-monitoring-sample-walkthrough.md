<properties
 pageTitle="リモート監視の事前構成済みソリューションのチュートリアル | Microsoft Azure"
 description="Azure IoT リモート監視の事前構成済みソリューションとそのアーキテクチャの説明です。"
 services=""
 suite="iot-suite"
 documentationCenter=""
 authors="stevehob"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-suite"
 ms.devlang="na"
 ms.topic="get-started-article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="10/21/2015"
 ms.author="stevehob"/>

# リモート監視の事前構成済みソリューションのチュートリアル

## はじめに

IoT Suite リモート監視の事前構成済みソリューションは、遠隔地にある複数のコンピューターを操作するビジネス シナリオ向けの基本的なエンド ツー エンド監視ソリューションです。このソリューションは、主要な Azure IoT Suite サービスを組み合わせてビジネス シナリオの汎用的な実装を実現します。また、独自のビジネス要件を満たすためにこのタイプの IoT ソリューションの実装を予定している顧客の開始点となります。

## 論理アーキテクチャ

次の図は、事前構成済みソリューションの論理コンポーネントの概要を示したものです。

![](media/iot-suite-remote-monitoring-sample-walkthrough/remote-monitoring-architecture.png)


### シミュレートされたデバイス

事前構成済みソリューションにおいて、シミュレートされたデバイスは冷却装置 (建物のエアコンや施設の空気処理ユニットなど) を表します。シミュレートされたデバイスはそれぞれ、次のテレメトリ メッセージを IoT Hub に送信します。


| メッセージ | 説明 |
|----------|-------------|
| Startup | デバイスは起動時に、デバイス ID、デバイス メタデータ、サポートするコマンドの一覧、現在の構成など、デバイス自体に関する情報を含む **device-info** メッセージを送信します。 |


シミュレートされたデバイスは、次のデバイス プロパティをメタデータとして送信します。

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


シミュレートされたデバイスは、IoT Hub から送信された次のコマンドを処理できます。

| コマンド | 説明 |
|------------------------|-----------------------------------------------------|
| PingDevice | デバイスに _ping_ を送信して、デバイスが起動しているかどうかを確認します。 |
| StartTelemetry | デバイスのテレメトリ送信を開始します。 |
| StopTelemetry | デバイスのテレメトリ送信を停止します。 |
| ChangeSetPointTemp | ランダムなデータが生成される設定ポイントの値を変更します。 |
| DiagnosticTelemetry | デバイス シミュレーターをトリガーし、追加のテレメトリ値 (externalTemp) を送信します。 |
| ChangeDeviceState | デバイスの拡張状態プロパティを変更し、デバイスからデバイス情報メッセージを送信します。 |


デバイス コマンドの受信確認は IoT Hub を介して提供されます。


### Azure Stream Analytics ジョブ

**ジョブ 1: テレメトリ**は、2 つのコマンドを使用して、受信するデバイス テレメトリ ストリームに対して動作します。最初のコマンドは、デバイスからのすべてのテレメトリ メッセージを永続的な BLOB ストレージに送信します。2 番目のコマンドは、5 分間のスライディング ウィンドウで湿度の平均値、最小値、最大値を計算します。このデータも BLOB ストレージに送信されます。

**ジョブ 2: デバイス情報**は、受信するメッセージ ストリームからデバイス情報メッセージをフィルター処理し、フィルター処理されたメッセージをイベント ハブ エンドポイントに送信します。デバイスは、起動時および **SendDeviceInfo** コマンドへの応答時に、デバイス情報メッセージを送信します。

**ジョブ 3: 規則**は、デバイスごとのしきい値に対する温度と湿度の受信テレメトリ値を評価します。しきい値の値は、ソリューションに含まれる規則エディターで設定されます。デバイスと値の各ペアは、**参照データ**として Stream Analytics に読み込まれる BLOB に、タイムスタンプに基づいて格納されます。このジョブでは、空以外の値が、デバイスに設定された設定しきい値と比較されます。">" 条件を超えた場合、ジョブは **alarm** イベントを出力します。このイベントは、しきい値を超えたことを示し、デバイス、値、タイムスタンプ値を表示します。

### イベント プロセッサ

**イベント プロセッサ**は、デバイス情報メッセージとコマンド応答を処理します。イベント プロセッサで使用されるものは次のとおりです。

- 現在のデバイス情報を使用してデバイス レジストリ (DocumentDB データベースに格納されています) を更新する場合はデバイス情報メッセージ。
- デバイス コマンドの履歴 (DocumentDB データベースに格納されています) を更新する場合はコマンド応答メッセージ。

## チュートリアルの開始

このセクションでは、ソリューションのコンポーネントと想定されるユース ケースについて説明し、例を示します。

### リモート監視ダッシュボード
Web アプリのこのページでは、PowerBI JavaScript コントロール ([PowerBI-visuals リポジトリ](https://www.github.com/Microsoft/PowerBI-visuals)を参照) を使用して、BLOB ストレージ内の Stream Analytics ジョブによる出力データを視覚化します。


### デバイス管理ポータル

この Web アプリを使用すると、次のことができます。

- 一意のデバイス ID を設定して認証キーを生成する新しいデバイスをプロビジョニングする
- 既存のプロパティの表示や新しいプロパティによる更新など、デバイスのプロパティを管理する
- デバイスにコマンドを送信する
- デバイスのコマンド履歴を表示する

### クラウド ソリューションの動作を確認する
[Azure ポータル](https://portal.azure.com)にアクセスし、指定したソリューション名の付いたリソース グループに移動すると、プロビジョニングされたリソースを確認できます。

![](media/iot-suite-remote-monitoring-sample-walkthrough/azureportal_01.png)

初めてサンプルを実行する場合は、事前に構成された、シミュレートされたデバイスが 4 つあります。

![](media/iot-suite-remote-monitoring-sample-walkthrough/solutionportal_01.png)

デバイス管理ポータルを使用して、シミュレートされたデバイスを新しく追加できます。

![](media/iot-suite-remote-monitoring-sample-walkthrough/solutionportal_02.png)

最初、デバイス管理ポータルの新しいデバイスの状態は **[保留中]** です。

![](media/iot-suite-remote-monitoring-sample-walkthrough/solutionportal_03.png)

アプリでシミュレートされたデバイスのデプロイが完了すると、次のスクリーン ショットに示すように、デバイス管理ポータルのデバイスの状態は **[実行中]** に変わります。**DeviceInfo** Stream Analytics ジョブは、デバイス状態の情報をデバイスからデバイス管理ポータルに送信します。

![](media/iot-suite-remote-monitoring-sample-walkthrough/solutionportal_04.png)

ソリューション ポータルを使用すると、**ChangeSetPointTemp** などのコマンドをデバイスに送信できます。

![](media/iot-suite-remote-monitoring-sample-walkthrough/solutionportal_05.png)

デバイスからコマンドが正常に実行されたことが報告されると、状態は **[成功]** に変わります。

![](media/iot-suite-remote-monitoring-sample-walkthrough/solutionportal_06.png)

ソリューション ポータルを使用すると、モデル番号などの具体的な特性でデバイスを検索できます。

![](media/iot-suite-remote-monitoring-sample-walkthrough/solutionportal_07.png)

デバイスを無効にし、無効になったデバイスを削除することができます。

![](media/iot-suite-remote-monitoring-sample-walkthrough/solutionportal_08.png)

<!---HONumber=AcomDC_0218_2016-->