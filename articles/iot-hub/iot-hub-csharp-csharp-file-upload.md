<properties
	pageTitle="IoT Hub を使用したデバイスからのファイルのアップロード |Microsoft Azure"
	description="このチュートリアルに従って、C# を使用して Azure IoT Hub でデバイスからファイルをアップロードする方法を学習します。"
	services="iot-hub"
	documentationCenter=".net"
	authors="fsautomata"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="dotnet"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="09/29/2015"
     ms.author="elioda"/>

# チュートリアル: IoT Hub でデバイスからクラウドにファイルをアップロードする方法

## はじめに

Azure IoT Hub は、何百万もの IoT デバイスとアプリケーション バックエンドの間に信頼性のある保護された双方向通信を確立できる、完全に管理されたサービスです。前のチュートリアル (「[IoT Hub の概要]」と「[IoT Hub を使用したクラウドからデバイスへのメッセージの送信]」) では、IoT Hub の基本的なデバイスとクラウド間のメッセージング機能と、デバイスとクラウド コンポーネントからのアクセス方法を示しました。[デバイスからクラウドへのメッセージの処理]に関するページでは、Azure BLOB ストレージにデバイスからクラウドへのメッセージを確実に格納する方法を説明しました。ただし、デバイスから送信されるデータが比較的小さなデバイスからクラウドへのメッセージに容易にマップされない場合があります。たとえば、大きなファイルには、イメージ、ビデオ、高周波振動データのサンプルが含まれるものや、何らかの形式の前処理済みデータが含まれるものがあります。これらのファイルは通常、[Azure Data Factory] や [Hadoop] スタックなどのツールを使用してバッチ処理されます。イベントの送信よりデバイスからのファイルのアップロードが好ましい場合は、IoT Hub セキュリティと信頼性機能を引き続き使用できます。

このチュートリアルでは、「[IoT Hub を使用したクラウドからデバイスへのメッセージの送信]」に示されているコードに基づき、クラウドからデバイスへのメッセージを使用して、ファイルのアップロードに使用される Azure BLOB URI をデバイスに安全に提供する方法と、IoT Hub の配信確認を使用して、アプリ バックエンドからのファイル処理のトリガー方法を示します。この方法の利点は、IoT Hub のデバイス ID と、クラウドからデバイスへのメッセージの配信確認を再利用して、ファイルが正常にアップロードされたことをアプリ バックエンドに通知できることです。

> [AZURE.NOTE]ここで使用するのと同じ方法を使用することで、クラウドからデバイスにファイルを安全にダウンロードできます。

クラウドからデバイスへのメッセージおよび IoT Hub のセキュリティの詳細については、「[IoT Hub 開発者ガイド]」を参照してください。

このチュートリアルの最後には、次の 2 つの Windows コンソール アプリケーションを実行します。

* **SimulatedDevice**。「[IoT Hub を使用したクラウドからデバイスへのメッセージの送信]」で作成したアプリケーションの修正バージョン。これは IoT Hub に接続し、Azure BLOB URI を含むクラウドからデバイスへのメッセージを受信します。受信したクラウドからデバイスへのメッセージごとに、指定された BLOB URI へのファイルのアップロードがトリガーされます。
* **SendCloudToDevice**。Azure BLOB URI を作成し (「[BLOB サービスによる SAS の作成および使用](../storage/storage-dotnet-shared-access-signature-part-2.md)」の説明を参照)、IoT Hub 経由でシミュレーション対象デバイスにクラウドからデバイスへのメッセージを送信してから、その配信確認を受信します。

> [AZURE.NOTE]IoT Hub には、Azure IoT デバイス SDK を介した多数のデバイス プラットフォームや言語 (C、Java、Javascript など) に対する SDK サポートがあります。このチュートリアルのコード (一般的には Azure IoT Hub) にデバイスを接続するための詳しい手順については、[Azure IoT デベロッパー センター]を参照してください。Java および Node 用の Azure IoT サービス SDK は、近日リリース予定です。

このチュートリアルを完了するには、以下が必要になります。

+ Microsoft Visual Studio 2015、

+ アクティブな Azure アカウント。<br/>アカウントがない場合は、無料の試用アカウントを数分で作成することができます。詳細については、「Azure の無料試用版サイト」(http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fja-JP%2Fdevelop%2Fiot%2Ftutorials%2Ffile-upload%2F target="\_blank") を参照してください。


[AZURE.INCLUDE [iot-hub-file-upload-cloud-csharp](../../includes/iot-hub-file-upload-cloud-csharp.md)]


[AZURE.INCLUDE [iot-hub-file-upload-device-csharp](../../includes/iot-hub-file-upload-device-csharp.md)]

## アプリケーションの実行

これで、アプリケーションを実行する準備が整いました。

1.  Visual Studio 内でソリューションを右クリックし、**[スタートアップ プロジェクトの設定...]** を選択します。**[マルチ スタートアップ プロジェクト]** を選択してから、**SimulatedDevice** と **SendCloudToDevice** の両方のアプリの **[開始]** アクションを選択します。

2.  **F5** キーを押すと、すべてのアプリケーションが開始されていることを確認できます。**[SendCloudToDevice]** ウィンドウを選択してキーを押します。シミュレーション対象デバイスによってファイルがアップロードされるとメッセージが出力され、**SendCloudToDevice** アプリは正常にフィードバックが受信されたことを示します。[Azure ポータル]または Visual Studio サーバー エクスプローラーを使用して、ストレージ アカウントにファイルがあるかどうかを確認できます。

  ![][50]


## 次のステップ

このチュートリアルでは、クラウドからデバイスへのメッセージを活用して、デバイスからのファイルのアップロードを簡素化する方法を学習しました。次のチュートリアルで IoT Hub の機能やシナリオをさらに詳しく調べることができます。

- [デバイスからクラウドへのメッセージの処理]に関するページには、デバイスから送信されるテレメトリおよび対話型メッセージを確実に処理する方法が示されています。

IoT Hub に関するその他の情報:

* [IoT Hub の概要]
* [IoT Hub 開発者ガイド]
* [IoT Hub のガイダンス]
* [サポートされているデバイスのプラットフォームおよび言語][Supported devices]
* [Azure IoT デベロッパー センター]

<!-- Images. -->

[50]: ./media/iot-hub-csharp-csharp-file-upload/run-apps1.png

<!-- Links -->

[Send Cloud-to-Device messages with IoT Hub]: iot-hub-csharp-csharp-c2d.md

[Azure ポータル]: https://portal.azure.com/

[Azure Data Factory]: https://azure.microsoft.com/ja-JP/documentation/services/data-factory/
[Hadoop]: https://azure.microsoft.com/ja-JP/documentation/services/hdinsight/

[Get started with IoT Hub]: iot-hub-csharp-csharp-getstarted.md
[IoT Hub を使用したクラウドからデバイスへのメッセージの送信]: iot-hub-csharp-csharp-c2d.md
[デバイスからクラウドへのメッセージの処理]: iot-hub-csharp-csharp-process-d2c.md
[Uploading files from devices]: iot-hub-csharp-csharp-file-upload.md

[IoT Hub の概要]: iot-hub-what-is-iot-hub.md
[IoT Hub のガイダンス]: iot-hub-guidance.md
[IoT Hub 開発者ガイド]: iot-hub-devguide.md
[IoT Hub Supported Devices]: iot-hub-supported-devices.md
[IoT Hub の概要]: iot-hub-csharp-csharp-getstarted.md
[Supported devices]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/tested_configurations.md
[Azure IoT デベロッパー センター]: http://www.azure.com/develop/iot

<!---HONumber=AcomDC_1223_2015-->