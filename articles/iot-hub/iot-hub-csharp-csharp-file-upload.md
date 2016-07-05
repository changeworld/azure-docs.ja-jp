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
     ms.date="06/21/2016"
     ms.author="elioda"/>

# チュートリアル: IoT Hub でデバイスからクラウドにファイルをアップロードする方法

## はじめに

Azure IoT Hub は、何百万もの IoT デバイスとアプリケーション バックエンドの間に信頼性のある保護された双方向通信を確立できる、完全に管理されたサービスです。前のチュートリアル ([IoT Hub の使用]と [IoT Hub を使用したクラウドからデバイスへのメッセージの送信]) では、IoT Hub の基本的なデバイスとクラウド間のメッセージング機能を説明し、[デバイスからクラウドへのメッセージの処理]に関するチュートリアルでは、デバイスからクラウドへのメッセージを Azure BLOB ストレージに確実に格納する方法を示しています。ただし、一部のシナリオでは、デバイスから送信されるデータを、IoT Hub が受け取る、クラウドからデバイスへの比較的小さなメッセージにマッピングすることは簡単ではありません。イメージ、ビデオ、高周波振動データのサンプルが含まれる大きなファイルや、何らかの形式の前処理済みデータが含まれる大きなファイルがその例です。これらのファイルは通常、[Azure Data Factory] や [Hadoop] スタックなどのツールを使用してクラウドでバッチ処理されます。イベントの送信よりもデバイスからのファイルのアップロードの方が好ましい場合は、IoT Hub セキュリティと信頼性機能を引き続き使用できます。

このチュートリアルは、[IoT Hub を使用したクラウドからデバイスへのメッセージの送信]に関するページ内のコードに基づいて作成されており、IoT Hub のファイル アップロード機能を使用する方法を説明します。また、デバイスに Azure BLOB URI を安全に提供してファイルをアップロードする方法と、IoT Hub のファイル アップロード通知を使用してアプリケーションのバックエンドでファイル処理をトリガーする方法を紹介します。

このチュートリアルの最後には、次の 2 つの Windows コンソール アプリケーションを実行します。

* **SimulatedDevice**。[IoT Hub でクラウドからデバイスへのメッセージを送信する方法]に関するチュートリアルで作成したアプリケーションの変更済みバージョンです。このアプリケーションは、IoT Hub によって提供された SAS URI を使用してストレージにファイルをアップロードします。
* **ReadFileUploadNotification**。IoT Hub からファイル アップロード通知を受信します。

> [AZURE.NOTE] IoT Hub は、Azure IoT device SDK を介して多数のデバイス プラットフォームや言語 (C、Java、Javascript など) をサポートしています。このチュートリアルで例示しているコード (一般的には Azure IoT Hub) にデバイスを接続するための詳しい手順については、[Azure IoT デベロッパー センター]を参照してください。

このチュートリアルを完了するには、以下が必要になります。

+ Microsoft Visual Studio 2015、

+ アクティブな Azure アカウント<br/>アカウントがない場合は、無料アカウントを数分で作成できます。詳細については、「Azure の無料試用版サイト」(https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fja-JP%2Fdevelop%2Fiot%2Ftutorials%2Ffile-upload%2F target="\_blank") を参照してください。

[AZURE.INCLUDE [iot-hub-file-upload-device-csharp](../../includes/iot-hub-file-upload-device-csharp.md)]


[AZURE.INCLUDE [iot-hub-file-upload-cloud-csharp](../../includes/iot-hub-file-upload-cloud-csharp.md)]

## アプリケーションの実行

これで、アプリケーションを実行する準備が整いました。

1. Visual Studio でソリューションを右クリックし、**[スタートアップ プロジェクトの設定]** を選択します。**[マルチ スタートアップ プロジェクト]** を選択してから、**ReadFileUploadNotification** と **SimulatedDevice** の **[開始]** アクションを選択します。

2. **F5** キーを押します。両方のアプリケーションが開始されます。1 つのコンソール アプリケーションでアップロードの完了が表示され、もう 1 つのコンソール アプリケーションでアップロード通知メッセージが受信されます。[Azure ポータル]または Visual Studio サーバー エクスプローラーを使用して、ストレージ アカウントにアップロードされたファイルがあるかどうかを確認できます。

  ![][50]


## 次のステップ

このチュートリアルでは、IoT Hub のファイル アップロード機能を活用して、デバイスからのファイルのアップロードを簡素化する方法を学習しました。次のチュートリアルで IoT Hub の機能やシナリオをさらに詳しく調べることができます。

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

[Azure Data Factory]: https://azure.microsoft.com/documentation/services/data-factory/
[Hadoop]: https://azure.microsoft.com/documentation/services/hdinsight/

[Get started with IoT Hub]: iot-hub-csharp-csharp-getstarted.md
[IoT Hub でクラウドからデバイスへのメッセージを送信する方法]: iot-hub-csharp-csharp-c2d.md
[IoT Hub を使用したクラウドからデバイスへのメッセージの送信]: iot-hub-csharp-csharp-c2d.md
[デバイスからクラウドへのメッセージの処理]: iot-hub-csharp-csharp-process-d2c.md
[Uploading files from devices]: iot-hub-csharp-csharp-file-upload.md

[IoT Hub の概要]: iot-hub-what-is-iot-hub.md
[IoT Hub のガイダンス]: iot-hub-guidance.md
[IoT Hub 開発者ガイド]: iot-hub-devguide.md
[IoT Hub Supported Devices]: iot-hub-supported-devices.md
[IoT Hub の使用]: iot-hub-csharp-csharp-getstarted.md
[Supported devices]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/tested_configurations.md
[Azure IoT デベロッパー センター]: http://www.azure.com/develop/iot

<!---HONumber=AcomDC_0622_2016-->