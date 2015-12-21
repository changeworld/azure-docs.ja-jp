<properties
   pageTitle="Azure IoT プロトコル ゲートウェイ | Microsoft Azure"
   description="Azure IoT プロトコル ゲートウェイを使用して Azure IoT Hub の機能とプロトコル サポートを拡張する方法を説明します。"
   services="iot-hub"
   documentationCenter=""
   authors="kdotchkoff"
   manager="timlt"
   editor=""/>

<tags
   ms.service="iot-hub"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/29/2015"
   ms.author="kdotchko"/>

# IoT Hub に対するその他のプロトコルのサポート

IoT Hub は HTTPS および AMQP プロトコルを介した通信をネイティブでサポートします。場合によっては、デバイスまたはフィールド ゲートウェイでこれらの標準プロトコルをいずれも使用できず、プロトコルの適応が必要になることがあります。そのような場合は、カスタム ゲートウェイによって IoT Hub に対するトラフィックをブリッジすることにより、IoT Hub エンドポイントにプロトコルを適応できます。Azure IoT プロトコル ゲートウェイを使用して、IoT Hub にプロトコルを適応し、MQTT プロトコル アダプターを実装して MQTT プロトコルを介した IoT デバイスと IoT Hub の通信を有効にすることができます。

## Azure IoT プロトコル ゲートウェイ

Azure IoT プロトコル ゲートウェイは、IoT Hub との大規模双方型デバイス通信用に設計された、プロトコル適応用フレームワークです。プロトコル ゲートウェイは、特定のプロトコルを介して、デバイスの接続を受け入れ、AMQP 1.0 を介して IoT Hub へのトラフィックをブリッジするパススルー コンポーネントです。IoT プロトコル ゲートウェイはさまざまなプロトコルやプロトコル バージョンのサポートを追加するための柔軟性を提供する、オープン ソース ソフトウェア (OSS) のプロジェクトとして使用できます。

プロトコル ゲートウェイは、Cloud Services の worker ロールを使用して、スケーラブルな方法で Azure にデプロイできます。さらに、プロトコル ゲートウェイはフィールド ゲートウェイなどのオンプレミス環境にデプロイできます。

Azure IoT プロトコル ゲートウェイには、MQTT v3.1.1 プロトコルを介してデバイスとの通信を容易にするための MQTT アダプターが含まれています。必要に応じて、実装をカスタマイズするための柔軟性を得るために、OSS プロジェクトとしてプロトコル ゲートウェイと MQTT 実装が提供されています。

MQTT アダプターは、その他のプロトコルのプロトコル アダプターを構築するためのプログラミング モデルにもなっています。さらに、IoT プロトコル ゲートウェイのプログラミング モデルでは、カスタム認証、メッセージの変換、圧縮と圧縮解除、またはデバイスと IoT Hub 間のトラフィックの暗号化/暗号化解除などの特殊な処理を行うために、カスタム コンポーネントを接続することができます。

## 次のステップ

Azure IoT プロトコル ゲートウェイについて、およびそれを IoT ソリューションの一部として使用およびデプロイする方法については、以下をご覧ください。

* [Azure IoT protocol gateway repository on GitHub (GitHub の Azure IoT プロトコル ゲートウェイ リポジトリ)](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/README.md)
* [Azure IoT protocol gateway developer guide (Microsoft Azure IoT プロトコル ゲートウェイ開発者ガイド)](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/docs/DeveloperGuide.md)

<!---HONumber=AcomDC_1210_2015-->