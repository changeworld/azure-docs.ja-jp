<properties
 pageTitle="Azure IoT の構成済みソリューション | Microsoft Azure"
 description="Azure IoT の構成済みソリューションとそのアーキテクチャ (追加リソースのリンクを含む) の説明。"
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
 ms.date="02/19/2016"
 ms.author="dobett"/>

# Azure IoT Suite の構成済みソリューションとは

Azure IoT Suite の構成済みソリューションとは、Azure サブスクリプションを使用して Microsoft Azure にデプロイできる一般的な IoT ソリューション パターンの実装です。構成済みソリューションは次の用途で使用できます。

- 独自の IoT ソリューションの開始点として。
- IoT ソリューションの設計と開発で一般的なパターンについて学習するため。

構成済みの各ソリューションには、一般的な IoT シナリオが実装されており、シミュレートされたデバイスを使用してテレメトリを生成することができる、完全なエンドツーエンドの実装です。

Azure にソリューションをデプロイして実行するだけでなく、完全なソース コードをダウンロードして、特定の IoT 要件を満たすようにソリューションをカスタマイズして拡張することができます。

> [AZURE.NOTE] 記事「[IoT 事前構成済みソリューションの使用][lnk-preconf-get-started]」では、ソリューションのいずれかをデプロイして実行する方法について説明しています。

次の表は、ソリューションが特定の IoT 機能にどのようにマップされるかを示しています。

| 解決策 | データの取り込み | デバイス ID | コマンドと制御 | ルールとアクション | 予測分析 |
|------------------------|-----|-----|-----|-----|-----|
| [リモート監視][lnk-remote-monitoring] | あり | あり | あり | あり | - | | [予測的なメンテナンス][lnk-predictive-maintenance] | あり | あり | あり | あり | あり |

## リモート監視の構成済みソリューションの概要

この記事では、リモート監視の構成済みソリューションについて説明します。このソリューションを選択したのは、最も単純なソリューションであり、他のソリューションが共有する一般的な設計要素が使用されているためです。

次の図は、リモート監視ソリューションの主な要素を示しています。以降のセクションでは、これらの要素について詳しく説明します。

![リモート監視の構成済みソリューションのアーキテクチャ][img-remote-monitoring-arch]

## デバイス

リモート監視の構成済みソリューションをデプロイすると、冷却デバイスをシミュレートする 4 つのシミュレートされたデバイスがソリューション内で事前にプロビジョニングされます。これらのシミュレートされたデバイスには、テレメトリを出力する温度と湿度モデルが組み込まれています。

デバイスがリモート監視の構成済みソリューションの IoT Hub に初めて接続すると、IoT Hub に送信されるデバイス情報メッセージに、デバイスが応答できるコマンドの一覧が列挙されます。リモート監視の構成済みソリューションには、次のコマンドが用意されています。

- *デバイスの ping*。デバイスは、確認応答を伴って、このコマンドに応答します。これは、デバイスがまだアクティブでリッスンしていることを確認するのに便利です。
- *テレメトリの開始*。テレメトリの送信を開始するようデバイスに指示します。
- *テレメトリの停止*。テレメトリの送信を停止するようデバイスに指示します。
- *設定点温度の変更*。デバイスが送信するシミュレートされた温度テレメトリ値を制御します。これは、テストに便利です。
- *診断テレメトリ*。デバイスが外部温度をテレメトリとして送信するかどうかを制御します。
- *デバイス状態の変更*。デバイスが報告するデバイスの状態のメタデータ プロパティを設定します。これは、テストに便利です。

同じテレメトリを出力し、同じコマンドに応答するシミュレートされたデバイスをソリューションに追加できます。

## IoT Hub

IoT Hub は、1 つのエンドポイントの冷却デバイスからテレメトリを受信します。また、IoT Hub は、デバイス固有のエンドポイントも保守します。各デバイスは、送信されたコマンドをエンドポイントで取得できます。

IoT Hub は、受信したテレメトリを、コンシューマー グループ エンドポイント経由で利用できるようにします。

この構成済みソリューションの IoT Hub インスタンスは、一般的な [IoT ソリューション アーキテクチャ][lnk-what-is-azure-iot]の*クラウド ゲートウェイ*に相当します。

## Azure Stream Analytics

構成済みソリューションでは、3 つの [Azure Stream Analytics][lnk-asa] (ASA) ジョブを使用して、冷却デバイスのテレメトリ ストリームをフィルターします。


- *DeviceInfo ジョブ* - ソリューションのデバイス レジストリ (DocumentDB データベース) にデバイス登録固有のメッセージを送信します。
- *Telemetry ジョブ* - Azure Blob Storage にコールド ストレージの未加工のテレメトリをすべて送信し、ソリューションのダッシュボードに表示されるテレメトリの集計を行います。
- *Rules ジョブ* - テレメトリ ストリームをフィルターして、いずれかのルールのしきい値を超える値を絞り込みます。ルールが実行されると、ソリューション ポータルのダッシュボード ビューにこのイベントがアラーム履歴テーブルの新しい行として表示され、ソリューション ポータルのルールとアクション ビューで定義した設定に基づいてアクションがトリガーされます。

この構成済みソリューションでは、ASA ジョブは一般的な [IoT ソリューション アーキテクチャ][lnk-what-is-azure-iot]の *IoT ソリューション バックエンド*の一部です。

## イベント プロセッサ

[Web ジョブ][lnk-web-job]で実行される [EventPocessorHost][lnk-event-processor] インスタンスでは、DeviceInfo ジョブと Rules ジョブからの出力を処理します。

この構成済みソリューションでは、イベント プロセッサは一般的な [IoT ソリューション アーキテクチャ][lnk-what-is-azure-iot]の *IoT ソリューション バックエンド*の一部です。

## ソリューション ポータル

![ソリューションのダッシュボード][img-dashboard]

ソリューション ポータルは、構成済みソリューションの一部としてクラウドにデプロイされている Web ベースの UI です。ソリューション ポータルでは、次の操作を実行できます。

- ダッシュボードにテレメトリとアラームの履歴を表示します。
- 新しいデバイスをプロビジョニングします。
- デバイスを管理し、監視します。
- 特定のデバイスにコマンドを送信します。
- ルールとアクションを管理します。

> [AZURE.NOTE] この構成済みソリューションでは、豊富なデバイス メタデータを格納するソリューションのデバイス レジストリ (DocumentDB データベース) と同期した IoT Hub [デバイス ID レジストリ][lnk-identity-registry]を保持します。

この構成済みソリューションでは、ソリューション ポータルは *IoT ソリューション バックエンド*の一部です。また、一般的な [IoT ソリューション アーキテクチャ][lnk-what-is-azure-iot]の*処理とビジネスの接続*に含まれています。

## 次のステップ

IoT の構成済みソリューションの詳細については、次のリソースをご覧ください。

- [IoT 事前構成済みソリューションの使用][lnk-preconf-get-started]
- [予測的なメンテナンスの構成済みソリューションの概要][lnk-predictive-maintenance]

[img-remote-monitoring-arch]: ./media/iot-suite-what-are-preconfigured-solutions/remote-monitoring-arch1.png
[img-dashboard]: ./media/iot-suite-what-are-preconfigured-solutions/dashboard.png
[lnk-remote-monitoring]: iot-suite-remote-monitoring-sample-walkthrough.md
[lnk-what-is-azure-iot]: iot-suite-what-is-azure-iot.md
[lnk-asa]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-event-processor]: event-hubs-programming-guide.md#event-processor-host
[lnk-web-job]: web-sites-create-web-jobs.md
[lnk-document-db]: https://azure.microsoft.com/documentation/services/documentdb/
[lnk-identity-registry]: iot-hub-devguide.md#device-identity-registry
[lnk-suite-overview]: iot-suite-overview.md
[lnk-preconf-get-started]: iot-suite-getstarted-preconfigured-solutions.md
[lnk-predictive-maintenance]: iot-suite-predictive-overview.md

<!---HONumber=AcomDC_0224_2016-->