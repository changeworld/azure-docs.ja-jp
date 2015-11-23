<properties
	pageTitle="IoT Hub のデバイスからクラウドへのメッセージの処理 | Microsoft Azure"
	description="このチュートリアルに従って、IoT Hub のデバイスからクラウドへのメッセージの処理に便利なパターンを学習します。"
	services="iot-hub"
	documentationCenter=".net"
	authors="fsautomata"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="csharp"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="09/29/2015"
     ms.author="elioda"/>

# チュートリアル: IoT Hub のデバイスからクラウドへのメッセージを処理する方法

## はじめに

Azure IoT Hub は、何百万もの IoT デバイスとアプリケーション バックエンドの間に信頼性のある保護された双方向通信を確立できる、完全に管理されたサービスです。前のチュートリアル (「[IoT Hub の概要]」と「[IoT Hub を使用したクラウドからデバイスへのメッセージの送信]」) では、IoT Hub の基本的なデバイスとクラウド間のメッセージング機能と、デバイスとクラウド コンポーネントからのアクセス方法を示しました。

このチュートリアルでは、「[IoT Hub の概要]」に示されているコードに基づいて、デバイスからクラウドへのメッセージを処理するための 2 つのパターンを示します。

1 つ目のパターンは、[Azure BLOB] のデバイスからクラウドへのメッセージの信頼性の高いストレージです。*コールド パス*分析を実装する場合、このシナリオは非常に一般的なものです。このような場合は、BLOB に格納されたデータが [Azure Data Factory] や [Hadoop] スタックなどのツールに駆動される分析への入力として使用されます。

2 つ目のパターンは、*対話型*のデバイスからクラウドへのメッセージの信頼性の高い処理です。デバイスからクラウドへのメッセージは、アプリケーション バックエンドでの一連のアクションの即時トリガーである場合、*対話型*と呼ばれ、分析エンジンにフィードされる*データ ポイント* メッセージとは異なります。たとえば、CRM システムでチケットの挿入をトリガーする必要のあるデバイスから送信されるアラームは*対話型*のデバイスからクラウドへのメッセージで、*データ ポイント* メッセージである温度サンプルを含むテレメトリ メッセージとは異なります。

IoT Hub はデバイスからクラウドへのメッセージを受信するために Event Hubs と互換性のあるエンドポイントを公開するため、このチュートリアルでは [EventProcessorHost] を使用して、以下を行うイベント プロセッサ クラスをホストします。

* Azure BLOB に*データ ポイント* メッセージを確実に格納する。
* *対話型*のデバイスからクラウドへのメッセージを [Service Bus キュー]に転送して直ちに処理する。

[Service Bus][Service Bus Queue] は、メッセージごとのチェックポイントと期間ベースの重複除去機能を提供するため、対話型メッセージの信頼性の高い処理を保証する優れた方法です。

このチュートリアルの最後には、次の 3 つの Windows コンソール アプリケーションを実行します。

* **SimulatedDevice**。「[IoT Hub の概要]」で作成したアプリを変更したものです。*データ ポイント*のデバイスからクラウドへのメッセージを 1 秒ごとに送信し、*対話型*のデバイスからクラウドへのメッセージを 10 秒ごとに送信します。
* **ProcessDeviceToCloudMessages**。[EventProcessorHost] を使用して、Azure BLOB に*データ ポイント* メッセージを確実に格納し、*対話型*メッセージを Service Bus キューに転送します。
* **ProcessD2cInteractiveMessages**。キューからメッセージをデキューします。

> [AZURE.NOTE]IoT Hub には、Azure IoT デバイス SDK を介した多数のデバイス プラットフォームや言語 (C、Java、Javascript など) に対する SDK サポートがあります。このチュートリアルのコード (一般的には Azure IoT Hub) にデバイスを接続するための詳しい手順については、[Azure IoT デベロッパー センター]を参照してください。

> [AZURE.NOTE]このチュートリアルの内容は、Storm のような [Hadoop] プロジェクトなど、Event Hubs と互換性のあるメッセージを使用する他の方法に直接適用できます。詳細については、[IoT Hub のガイダンスの Event Hubs との互換性]に関する記述を参照してください。

このチュートリアルを完了するには、以下が必要になります。

+ Microsoft Visual Studio 2015、

+ アクティブな Azure アカウント<br/>アカウントがない場合は、無料の試用アカウントを数分で作成することができます。詳細については、「Azure の無料試用版サイト」(http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fja-JP%2Fdevelop%2Fiot%2Ftutorials%2Fprocess-d2c%2F target="\_blank") を参照してください。

また、[Azure Storage] と [Azure Service Bus] について、ある程度の知識があることを前提とします。


[AZURE.INCLUDE [iot-hub-process-d2c-device-csharp](../../includes/iot-hub-process-d2c-device-csharp.md)]


[AZURE.INCLUDE [iot-hub-process-d2c-cloud-csharp](../../includes/iot-hub-process-d2c-cloud-csharp.md)]

## アプリケーションの実行

これで、アプリケーションを実行する準備が整いました。

1.	Visual Studio 内でソリューションを右クリックし、**[スタートアップ プロジェクトの設定...]** を選択します。**[マルチ スタートアップ プロジェクト]** を選択してから、**ProcessDeviceToCloudMessages**、**SimulatedDevice**、**ProcessD2cInteractiveMessages** アプリの **[開始]** アクションを選択します。

2.	**F5** キーを押すと、すべてのアプリケーションが開始され、シミュレーション対象デバイスによって送信されたすべての対話型メッセージが対話型メッセージ プロセッサで処理されていることを確認できます。

  ![][50]

> [AZURE.NOTE]BLOB ファイルが更新されていることを確認するために、`StoreEventProcessor` の `MAX_BLOCK_SIZE` 定数をより小さい値 (つまり、`1024`) に減らす必要がある場合があります。これは、シミュレーション対象デバイスによって送信されたデータのブロック サイズの制限に達するまでに時間がかかるためです。このように編集することで、ストレージ コンテナーで BLOB が作成され、更新されたことを確認できます。

## 次のステップ

このチュートリアルでは、[EventProcessorHost] を使用して、*データ ポイント*と*対話型*のデバイスからクラウドへのメッセージを確実に処理する方法を学習しました。同じようなメッセージ処理ロジックを、次のようにして実装することができます。

- 「[デバイスからのファイルのアップロード]」では、デバイスからのファイル アップロードを容易にするためにクラウドからデバイスへのメッセージを活用したパターンについて説明しています。

IoT Hub に関するその他の情報:

* [IoT Hub の概要]
* [IoT Hub 開発者ガイド]
* [IoT Hub のガイダンス]
* [サポートされているデバイスのプラットフォームおよび言語][Supported devices]
* [Azure IoT デベロッパー センター]

<!-- Images. -->
[50]: ./media/iot-hub-csharp-csharp-process-d2c/run1.png


<!-- Links -->

[Azure BLOB]: https://azure.microsoft.com/ja-JP/documentation/articles/storage-dotnet-how-to-use-blobs/
[Azure Data Factory]: https://azure.microsoft.com/ja-JP/documentation/services/data-factory/
[Hadoop]: https://azure.microsoft.com/ja-JP/documentation/services/hdinsight/
[Service Bus Queue]: https://azure.microsoft.com/ja-JP/documentation/articles/service-bus-dotnet-how-to-use-queues/
[Service Bus キュー]: https://azure.microsoft.com/ja-JP/documentation/articles/service-bus-dotnet-how-to-use-queues/
[EventProcessorHost]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventprocessorhost(v=azure.95).aspx

[Transient Fault Handling]: https://msdn.microsoft.com/ja-JP/library/hh680901(v=pandp.50).aspx

[IoT Hub のガイダンスの Event Hubs との互換性]: iot-hub-guidance.md#eventhubcompatible

[Azure Storage]: https://azure.microsoft.com/ja-JP/documentation/services/storage/
[Azure Service Bus]: https://azure.microsoft.com/ja-JP/documentation/services/service-bus/

[Azure preview portal]: https://portal.azure.com/

[IoT Hub を使用したクラウドからデバイスへのメッセージの送信]: iot-hub-csharp-csharp-c2d.md
[Process Device-to-Cloud messages]: iot-hub-csharp-csharp-process-d2c.md
[デバイスからのファイルのアップロード]: iot-hub-csharp-csharp-file-upload.md

[IoT Hub の概要]: iot-hub-what-is-iot-hub.md
[IoT Hub のガイダンス]: iot-hub-guidance.md
[IoT Hub 開発者ガイド]: iot-hub-devguide.md
[IoT Hub Supported Devices]: iot-hub-supported-devices.md
[IoT Hub の概要]: iot-hub-csharp-csharp-getstarted.md
[Supported devices]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/tested_configurations.md
[Azure IoT デベロッパー センター]: http://www.azure.com/develop/iot

<!---HONumber=Nov15_HO3-->