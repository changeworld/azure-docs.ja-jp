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
     ms.date="01/05/2016"
     ms.author="dobett"/>

# チュートリアル: IoT Hub のデバイスからクラウドへのメッセージを処理する方法

## はじめに

Azure IoT Hub は、何百万もの IoT デバイスとアプリケーション バックエンドの間に信頼性のある保護された双方向通信を確立できる、完全に管理されたサービスです。その他のチュートリアル (「[IoT Hub の概要]」と「[IoT Hub を使用したクラウドからデバイスへのメッセージの送信]」) では、IoT Hub の基本的なデバイスとクラウド間のメッセージング機能の使用方法を示しました。

このチュートリアルは、「[IoT Hub の概要]」チュートリアルに示されているコードを基に作成しており、デバイスとクラウド間のメッセージを処理するために使用できる 2 つの拡張性の高いパターンについて説明しています。

- [Azure BLOB Storage] のデバイスからクラウドへのメッセージの信頼性の高いストレージ。これは*コールド パス*を実装する場合の非常に一般的なシナリオであり、[Azure Data Factory] または [HDInsight (Hadoop)] スタックなどのツールで活用される分析処理への入力として使用する BLOB のデータを保存します。

- *対話型*のデバイスからクラウドへのメッセージの信頼性の高い処理。デバイスからクラウドへのメッセージは、アプリケーション バックエンドでの一連のアクションの即時トリガーである場合、対話型と呼ばれ、分析エンジンにフィードされる*データ ポイント* メッセージと比較されます。たとえば、CRM システムへのチケットの挿入をトリガーする必要のあるデバイスから送信されるアラームは対話型のデバイスからクラウドへのメッセージで、データ ポイント デバイスからクラウドへのメッセージである温度サンプルなどのテレメトリと比較されます。

IoT Hub はデバイスからクラウドへのメッセージを受信するために Event Hubs と互換性のあるエンドポイントを公開するため、このチュートリアルでは [EventProcessorHost] インスタンスを使用して、以下を行います。

* Azure BLOB に*データ ポイント* メッセージを確実に格納する。
* *対話型*のデバイスからクラウドへのメッセージを [Service Bus キュー]に転送して直ちに処理する。

Service Bus は、メッセージごとのチェックポイントと期間ベースの重複除去機能を提供するため、対話型メッセージの信頼性の高い処理を保証する優れた方法です。

このチュートリアルの最後には、次の 3 つの Windows コンソール アプリケーションを実行します。

* **SimulatedDevice**。「[IoT Hub の概要]」チュートリアルで作成したアプリを変更したものです。データ ポイントのデバイスからクラウドへのメッセージを 1 秒ごとに送信し、対話型のデバイスからクラウドへのメッセージを 10 秒ごとに送信します。
* **ProcessDeviceToCloudMessages**。[EventProcessorHost] クラスを使用して、Event Hubs と互換性のあるエンドポイントからメッセージを受信し、Azure BLOB にデータ ポイント メッセージを確実に格納し、対話型メッセージを Service Bus キューに転送します。
* **ProcessD2CInteractiveMessages**。Service Bus キューから対話型メッセージをデキューします。

> [AZURE.NOTE] IoT Hub には、多数のデバイス プラットフォームや言語 (C、Java、JavaScript など) に対する SDK サポートがあります。このチュートリアルでシミュレートされたデバイスと物理デバイスを交換する方法と Azure IoT Hub にデバイスを接続する一般的な方法の詳しい手順については、[Azure IoT デベロッパー センター]を参照してください。

このチュートリアルは、[HDInsight (Hadoop)] プロジェクトなど、Event Hubs と互換性のあるメッセージを使用する他の方法に直接適用できます。詳細については、「[Azure IoT Hub 開発者ガイド - デバイスからクラウド]」を参照してください。

このチュートリアルを完了するには、以下が必要になります。

+ Microsoft Visual Studio 2015

+ アクティブな Azure アカウント<br/>アカウントがない場合は、無料の試用アカウントを数分で作成することができます。詳細については、「Azure の無料試用版サイト」(https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fja-JP%2Fdevelop%2Fiot%2Ftutorials%2Fprocess-d2c%2F target="\_blank") を参照してください。

[Azure Storage] と [Azure Service Bus] について、ある程度の基本的な知識が必要です。


[AZURE.INCLUDE [iot-hub-process-d2c-device-csharp](../../includes/iot-hub-process-d2c-device-csharp.md)]


[AZURE.INCLUDE [iot-hub-process-d2c-cloud-csharp](../../includes/iot-hub-process-d2c-cloud-csharp.md)]

## アプリケーションの実行

これで、アプリケーションを実行する準備が整いました。

1.	Visual Studio のソリューション エクスプローラーでソリューションを右クリックし、**[スタートアップ プロジェクトの設定]** を選択します。**[マルチ スタートアップ プロジェクト]** を選択してから、**[開始]** を **ProcessDeviceToCloudMessages**、**SimulatedDevice**、**ProcessD2CInteractiveMessages** プロジェクトのアクションとして選択します。

2.	**F5** キーを押して 3 つのコンソール アプリケーションを起動します。**ProcessD2CInteractiveMessages** アプリケーションは、**SimulatedDevice** アプリケーションから送信されたすべての対話型メッセージを処理します。

  ![][50]

> [AZURE.NOTE] BLOB ファイルの更新内容を表示するために **MAX\_BLOCK\_SIZE** 定数を **StoreEventProcessor** クラスで **1024**などのより小さい値に削減することが必要になる場合があります。これは、シミュレーション対象デバイスによって送信されたデータのブロック サイズの制限に達するまでに時間がかかるためです。より小さいブロック サイズであれば、作成および更新される BLOB を表示するために長時間待機する必要はありません。ただし、より大きなブロック サイズを使用すると、アプリケーションの拡張性が向上します。

## 次のステップ

このチュートリアルでは、[EventProcessorHost] クラスを使用して、データ ポイントと対話型のデバイスからクラウドへのメッセージを確実に処理する方法を学習しました。

「[デバイスからのファイルのアップロード]」チュートリアルは、同じようなメッセージ処理ロジックを使用するこのチュートリアルを基に作成されており、デバイスからのファイルのアップロードを容易にするためにクラウドからデバイスへのメッセージを活用したパターンについて説明しています。

IoT Hub に関するその他の情報:

* [IoT Hub の概要]
* [IoT Hub 開発者ガイド]
* [IoT Hub のガイダンス]
* [サポートされているデバイスのプラットフォームおよび言語][Supported devices]
* [Azure IoT デベロッパー センター]

<!-- Images. -->
[50]: ./media/iot-hub-csharp-csharp-process-d2c/run1.png


<!-- Links -->

[Azure Blob storage]: https://azure.microsoft.com/ja-JP/documentation/articles/storage-dotnet-how-to-use-blobs/
[Azure Data Factory]: https://azure.microsoft.com/ja-JP/documentation/services/data-factory/
[HDInsight (Hadoop)]: https://azure.microsoft.com/ja-JP/documentation/services/hdinsight/
[Service Bus キュー]: https://azure.microsoft.com/ja-JP/documentation/articles/service-bus-dotnet-how-to-use-queues/
[EventProcessorHost]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventprocessorhost(v=azure.95).aspx



[Azure IoT Hub 開発者ガイド - デバイスからクラウド]: https://azure.microsoft.com/ja-JP/documentation/articles/iot-hub-devguide/#d2c

[Azure Storage]: https://azure.microsoft.com/ja-JP/documentation/services/storage/
[Azure Service Bus]: https://azure.microsoft.com/ja-JP/documentation/services/service-bus/



[IoT Hub を使用したクラウドからデバイスへのメッセージの送信]: iot-hub-csharp-csharp-c2d.md
[デバイスからのファイルのアップロード]: iot-hub-csharp-csharp-file-upload.md

[IoT Hub の概要]: iot-hub-what-is-iot-hub.md
[IoT Hub のガイダンス]: iot-hub-guidance.md
[IoT Hub 開発者ガイド]: iot-hub-devguide.md
[IoT Hub の概要]: iot-hub-csharp-csharp-getstarted.md
[Supported devices]: iot-hub-tested-configurations.md
[Azure IoT デベロッパー センター]: https://azure.microsoft.com/develop/iot

<!---HONumber=AcomDC_0224_2016-->