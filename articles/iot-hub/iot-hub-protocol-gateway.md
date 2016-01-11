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

Azure IoT Hub は HTTPS および AMQP プロトコルを介した通信をネイティブでサポートします。場合によっては、デバイスまたはフィールド ゲートウェイでこれらの標準プロトコルをいずれも使用できず、プロトコルの適応が必要になることがあります。このような場合は、カスタム ゲートウェイを使用できます。カスタム ゲートウェイによって IoT Hub に対するトラフィックをブリッジすることにより、IoT Hub エンドポイントにプロトコルを適応できます。Azure Internet of Things (IoT) プロトコル ゲートウェイは、IoT Hub に対するプロトコルの適応を可能にします。さらに、IoT デバイスと IoT Hub 間の MQTT プロトコル経由の通信を可能にする MQTT プロトコル アダプターを実装します。

## Azure IoT プロトコル ゲートウェイ

Azure IoT プロトコル ゲートウェイは、IoT Hub との大規模双方型デバイス通信用に設計された、プロトコル適応用フレームワークです。プロトコル ゲートウェイは、特定のプロトコルを介してデバイスの接続を受け入れるパススルー コンポーネントです。AMQP 1.0 経由の IoT Hub へのトラフィックをブリッジします。IoT プロトコル ゲートウェイは、さまざまなプロトコルとプロトコルのバージョンに対するサポートを追加する柔軟性を提供するためのオープン ソース ソフトウェア プロジェクトとして使用できます。

プロトコル ゲートウェイは、Azure Cloud Services の worker ロールを使用することで、高度にスケーラブルな方法で Azure にデプロイできます。さらに、プロトコル ゲートウェイはフィールド ゲートウェイなどのオンプレミス環境にデプロイできます。

Azure IoT プロトコル ゲートウェイには、MQTT v3.1.1 プロトコルを介してデバイスとの通信を容易にするための MQTT アダプターが含まれています。プロトコル ゲートウェイと MQTT の実装は、柔軟性を追加するためのオープン ソース ソフトウェア プロジェクトとして提供されます。これにより、必要に応じて実装をカスタマイズできます。

MQTT アダプターは、その他のプロトコルのプロトコル アダプターを構築するためのプログラミング モデルにもなっています。さらに、IoT プロトコル ゲートウェイのプログラミング モデルでは、カスタム認証、メッセージの変換、圧縮と圧縮解除、デバイスと IoT Hub 間のトラフィックの暗号化/暗号化解除などの特殊な処理を行うために、カスタム コンポーネントを接続することができます。

## 次のステップ

Azure IoT プロトコル ゲートウェイについて、およびそれを IoT ソリューションの一部として使用およびデプロイする方法については、以下をご覧ください。

* [Azure IoT protocol gateway repository on GitHub (GitHub の Azure IoT プロトコル ゲートウェイ リポジトリ)](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/README.md)
* [Azure IoT protocol gateway developer guide (Microsoft Azure IoT プロトコル ゲートウェイ開発者ガイド)](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/docs/DeveloperGuide.md)

<!---HONumber=AcomDC_1223_2015-->