<properties
	pageTitle="IoT Hub を使用したクラウドからデバイスへのメッセージの送信 | Microsoft Azure"
	description="このチュートリアルに従って、C# を使用して Azure IoT Hub でクラウドからデバイスへのメッセージを送信する方法を学習します。"
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

# チュートリアル: IoT Hub でクラウドからデバイスへのメッセージを送信する方法

## はじめに

Azure IoT Hub は、何百万もの IoT デバイスとアプリケーション バックエンドの間に信頼性のある保護された双方向通信を確立できる、完全に管理されたサービスです。「[IoT Hub の概要]」チュートリアルには、IoT Hub の作成方法、IoT Hub でデバイス ID をプロビジョニングする方法、およびデバイスからクラウドへのメッセージを送信するシミュレーション済みデバイスをコード化する方法が示されています。

このチュートリアルは「[IoT Hub の概要]」に基づいて作成されており、クラウドからデバイスへのメッセージを単一のデバイスに送信する方法、IoT Hub から送信確認 (*フィードバック*) を要求する方法、およびアプリケーション クラウド バックエンドからそれを受け取る方法を示します。

クラウドからデバイスへのメッセージの詳細については、「[IoT Hub 開発者ガイド][IoT Hub Developer Guide - C2D]」をご覧ください。

このチュートリアルの最後には、次の 2 つの Windows コンソール アプリケーションを実行します。

* **SimulatedDevice**。「[IoT Hub の概要]」で作成されたアプリケーションの修正バージョン。これは、IoT Hub に接続し、クラウドからデバイスへのメッセージを受け取ります。
* **SendCloudToDevice**。これは、クラウドからデバイスへのメッセージを IoT Hub を介してシミュレーション済みデバイスに送信し、その配信確認を受け取ります。

> [AZURE.NOTE]IoT Hub には、Azure IoT デバイス SDK を介した多数のデバイス プラットフォームや言語 (C、Java、Javascript など) に対する SDK サポートがあります。このチュートリアルのコード (一般的には Azure IoT Hub) にデバイスを接続するための詳しい手順については、[Azure IoT デベロッパー センター]を参照してください。Java および Node 用の Azure IoT サービス SDK は、近日リリース予定です。

このチュートリアルを完了するには、以下が必要になります。

+ Microsoft Visual Studio 2015、

+ アクティブな Azure アカウント<br/>アカウントがない場合は、無料の試用アカウントを数分で作成することができます。詳細については、「Azure の無料試用版サイト」(http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fja-JP%2Fdevelop%2Fiot%2Ftutorials%2Fc2d%2F target="\_blank") を参照してください。

[AZURE.INCLUDE [iot-hub-c2d-device-csharp](../../includes/iot-hub-c2d-device-csharp.md)]


[AZURE.INCLUDE [iot-hub-c2d-cloud-csharp](../../includes/iot-hub-c2d-cloud-csharp.md)]

## 次のステップ

このチュートリアルでは、クラウドからデバイスへのメッセージを送受信する方法を学習しました。引き続き、以下のチュートリアルでは IoT Hub の機能やシナリオをさらに詳しく調べることができます。

- [デバイスからクラウドへのメッセージの処理]に関するページには、デバイスから送信されるテレメトリおよび対話型メッセージを確実に処理する方法が示されています。
- 「[デバイスからのファイルのアップロード]」では、デバイスからのファイル アップロードを容易にするためにクラウドからデバイスへのメッセージを活用したパターンについて説明しています。

IoT Hub に関するその他の情報:

* [IoT Hub の概要]
* [IoT Hub 開発者ガイド]
* [IoT Hub のガイダンス]
* [サポートされているデバイスのプラットフォームおよび言語][Supported devices]
* [Azure IoT デベロッパー センター]

<!-- Images. -->

<!-- Links -->

[Get started with IoT Hub]: iot-hub-csharp-csharp-getstarted.md

[IoT Hub Developer Guide - C2D]: iot-hub-devguide.md#c2d

[Azure preview portal]: https://portal.azure.com/

[Send Cloud-to-Device messages with IoT Hub]: iot-hub-csharp-csharp-c2d.md
[デバイスからクラウドへのメッセージの処理]: iot-hub-csharp-csharp-process-d2c.md
[デバイスからのファイルのアップロード]: iot-hub-csharp-csharp-file-upload.md

[IoT Hub の概要]: iot-hub-what-is-iot-hub.md
[IoT Hub のガイダンス]: iot-hub-guidance.md
[IoT Hub 開発者ガイド]: iot-hub-devguide.md
[IoT Hub Supported Devices]: iot-hub-supported-devices.md
[IoT Hub の概要]: iot-hub-csharp-csharp-getstarted.md
[Supported devices]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/tested_configurations.md
[Azure IoT デベロッパー センター]: http://www.azure.com/develop/iot

<!---HONumber=Nov15_HO3-->