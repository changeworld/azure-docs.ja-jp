<properties
	pageTitle="IoT Hub のデバイスからクラウドへのメッセージの処理 | Microsoft Azure"
	description="このチュートリアルに従って、IoT Hub のデバイスからクラウドへのメッセージの処理に便利なパターンを学習します。"
	services="iot-hub"
	documentationCenter=".net"
	authors="dominicbetts"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="csharp"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="04/29/2016"
     ms.author="dobett"/>

# チュートリアル: IoT Hub のデバイスからクラウドへのメッセージを処理する方法

## はじめに

Azure IoT Hub は、何百万もの IoT デバイスとアプリケーション バックエンドの間に信頼性のある保護された双方向通信を確立できる、完全に管理されたサービスです。他のチュートリアル ([IoT Hub の概要]と [IoT Hub を使用したクラウドからデバイスへのメッセージの送信]に関するチュートリアル) では、IoT Hub のデバイスからクラウドおよびクラウドからデバイスへのメッセージングについて、基本的な機能の使用方法を説明しています。

このチュートリアルは、[IoT Hub の概要]に関するチュートリアルに示されているコードを基に作成されており、デバイスからクラウドへのメッセージを処理するために使用できる 2 つのスケーラブルなパターンについて説明しています。

- [Azure BLOB Storage] のデバイスからクラウドへのメッセージの信頼性の高いストレージ。*コールド パス*分析は、非常に一般的なシナリオです。このシナリオでは、分析プロセスへの入力として使用するテレメトリ データを BLOB に格納します。これらのプロセスは、[Azure Data Factory] や [HDInsight (Hadoop)] スタックなどのツールによって実行できます。

- *対話型*のデバイスからクラウドへのメッセージの信頼性の高い処理。デバイスからクラウドへのメッセージは、アプリケーション バックエンドでの一連のアクションの即時トリガーである場合に対話型となります。たとえば、デバイスは、CRM システムへのチケットの挿入をトリガーするアラーム メッセージを送信する場合があります。これに対して、*データ ポイント* メッセージは、分析エンジンにフィードされるだけです。たとえば、後で分析するために保存される、デバイスの温度テレメトリはデータ ポイント メッセージです。

IoT Hub はデバイスからクラウドへのメッセージを受信するために [Event Hubs][lnk-event-hubs] と互換性のあるエンドポイントを公開することから、このチュートリアルでは [EventProcessorHost] インスタンスを使用します。このインスタンスは次のような性質があります。

* Azure Blob Storage に*データ ポイント* メッセージを確実に保存する。
* *対話型*のデバイスからクラウドへのメッセージを Azure [Service Bus キュー]に転送して直ちに処理する。

Service Bus は、メッセージごとのチェックポイントと期間ベースの重複除去機能を提供するため、対話型メッセージの信頼性の高い処理を保証できます。

> [AZURE.NOTE] **EventProcessorHost** インスタンスは、対話型のメッセージを処理する方法の 1 つにすぎません。他にも、[Azure Service Fabric][lnk-service-fabric] や [Azure Stream Analytics][lnk-stream-analytics] を使用できます。

このチュートリアルの最後に、次の 3 つの Windows コンソール アプリケーションを実行します。

* **SimulatedDevice**: [IoT Hub の概要]に関するチュートリアルで作成したアプリを変更したものです。デバイスからクラウドへのデータ ポイント メッセージを 1 秒ごとに送信し、デバイスからクラウドへの対話型メッセージを 10 秒ごとに送信します。このアプリでは、IoT Hub との通信に AMQPS プロトコルを使用します。
* **ProcessDeviceToCloudMessages**: [EventProcessorHost] クラスを使用して、Event Hubs と互換性のあるエンドポイントからメッセージを取得します。その後、確実にデータ ポイント メッセージを Azure Blob Storage に格納し、対話型メッセージを Service Bus キューに転送します。
* **ProcessD2CInteractiveMessages**: Service Bus キューから対話型メッセージをデキューします。

> [AZURE.NOTE] IoT Hub には、多数のデバイス プラットフォームや言語 (C、Java、JavaScript など) に対する SDK サポートがあります。このチュートリアルでシミュレートしたデバイスを物理デバイスに置き換える方法と、IoT Hub にデバイスを接続する一般的な方法の詳しい手順については、[Azure IoT デベロッパー センター]を参照してください。

このチュートリアルは、[HDInsight (Hadoop)] プロジェクトなど、Event Hubs と互換性のあるメッセージを使用する他の方法に直接適用できます。詳細については、「Azure IoT Hub 開発者ガイド」の「[D2C (デバイスからクラウド)]」を参照してください。

このチュートリアルを完了するには、次のものが必要です。

+ Microsoft Visual Studio 2015

+ アクティブな Azure アカウント<br/>Azure サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を数分で作成できます。

[Azure Storage] と [Azure Service Bus] について、ある程度の基礎知識が必要です。


[AZURE.INCLUDE [iot-hub-process-d2c-device-csharp](../../includes/iot-hub-process-d2c-device-csharp.md)]


[AZURE.INCLUDE [iot-hub-process-d2c-cloud-csharp](../../includes/iot-hub-process-d2c-cloud-csharp.md)]

## アプリケーションの実行

これで、アプリケーションを実行する準備が整いました。

1.	Visual Studio のソリューション エクスプローラーでソリューションを右クリックし、**[スタートアップ プロジェクトの設定]** を選択します。**[マルチ スタートアップ プロジェクト]** を選択し、**ProcessDeviceToCloudMessages**、**SimulatedDevice**、**ProcessD2CInteractiveMessages** の各プロジェクトのアクションとして **[開始]** を選択します。

2.	**F5** キーを押して 3 つのコンソール アプリケーションを起動します。**ProcessD2CInteractiveMessages** アプリケーションは、**SimulatedDevice** アプリケーションから送信されたすべての対話型メッセージを処理します。

  ![Three console applicatons][50]

> [AZURE.NOTE] BLOB ファイルの更新内容を表示するために、**StoreEventProcessor** クラスの **MAX\_BLOCK\_SIZE** 定数を、**1024** などのより小さい値に設定することが必要になる場合があります。これは、シミュレーション対象デバイスによって送信されたデータのブロック サイズの制限に達するまでに時間がかかるためです。より小さいブロック サイズであれば、作成および更新される BLOB を表示するために長時間待機する必要はありません。ただし、より大きなブロック サイズを使用すると、アプリケーションの拡張性が向上します。

## 次のステップ

このチュートリアルでは、[EventProcessorHost] クラスを使用して、データ ポイント メッセージと、デバイスからクラウドへの対話型メッセージを確実に処理する方法について学習しました。

[デバイスからのファイルのアップロード]に関するチュートリアルは、同様のメッセージ処理ロジックを使用し、このチュートリアルに基づいて作成されています。さらに、デバイスからのファイル アップロードを容易にするためにクラウドからデバイスへのメッセージを活用するパターンについても説明しています。

IoT Hub に関するその他の情報:

* [IoT Hub の概要]
* [IoT Hub 開発者ガイド]
* [IoT Hub のガイダンス]
* [サポートされているデバイスのプラットフォームおよび言語][Supported devices]
* [Azure IoT デベロッパー センター]

<!-- Images. -->
[50]: ./media/iot-hub-csharp-csharp-process-d2c/run1.png


<!-- Links -->

[Azure Blob storage]: ../storage/storage-dotnet-how-to-use-blobs.md
[Azure Data Factory]: https://azure.microsoft.com/documentation/services/data-factory/
[HDInsight (Hadoop)]: https://azure.microsoft.com/documentation/services/hdinsight/
[Service Bus Queue]: ../service-bus/service-bus-dotnet-how-to-use-queues/
[EventProcessorHost]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventprocessorhost(v=azure.95).aspx



[D2C (デバイスからクラウド)]: iot-hub-devguide.md#d2c

[Azure Storage]: https://azure.microsoft.com/documentation/services/storage/
[Azure Service Bus]: https://azure.microsoft.com/documentation/services/service-bus/



[IoT Hub を使用したクラウドからデバイスへのメッセージの送信]: iot-hub-csharp-csharp-c2d.md
[デバイスからのファイルのアップロード]: iot-hub-csharp-csharp-file-upload.md

[IoT Hub の概要]: iot-hub-what-is-iot-hub.md
[IoT Hub のガイダンス]: iot-hub-guidance.md
[IoT Hub 開発者ガイド]: iot-hub-devguide.md
[IoT Hub の概要]: iot-hub-csharp-csharp-getstarted.md
[IoT Hub の概要]: iot-hub-csharp-csharp-getstarted.md
[Supported devices]: iot-hub-tested-configurations.md
[Azure IoT デベロッパー センター]: https://azure.microsoft.com/develop/iot
[lnk-service-fabric]: https://azure.microsoft.com/documentation/services/service-fabric/
[lnk-stream-analytics]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-event-hubs]: https://azure.microsoft.com/documentation/services/event-hubs/

<!---HONumber=AcomDC_0601_2016-->