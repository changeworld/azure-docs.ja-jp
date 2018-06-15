---
title: Azure IoT Central のデバイス接続機能 | Microsoft Docs
description: この記事では、Azure IoT Central のデバイス接続機能に関連する主な概念を紹介します。
author: dominicbetts
ms.author: dobett
ms.date: 11/30/2017
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: timlt
ms.openlocfilehash: dc9fe144c2258f33ce59c61ce63c15835cc3fa53
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/01/2018
ms.locfileid: "34628336"
---
# <a name="device-connectivity-in-azure-iot-central"></a>Azure IoT Central のデバイス接続機能

この記事では、Microsoft Azure IoT Central のデバイス接続機能に関連する主な概念を紹介します。

Azure IoT Central アプリケーションに接続するすべてのデバイスは、IoT hub Azure IoT Central を使用して公開される[エンドポイント](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-endpoints)に接続します。 IoT Hub は、接続されたデバイスからスケーラブルでセキュリティ上安全な、信頼性の高い接続を可能にします。

## <a name="sdk-support"></a>SDK のサポート

Azure Device SDK を使用すると、Azure IoT Central アプリケーションに接続するデバイス上にコードを簡単に実装できます。 次のデバイス SDK が使用できます。

- [Azure IoT SDK for C](https://github.com/azure/azure-iot-sdk-c)
- [Azure IoT SDK for Python](https://github.com/azure/azure-iot-sdk-python)
- [Azure IoT SDK for Node.js](https://github.com/azure/azure-iot-sdk-node)
- [Azure IoT SDK for Java](https://github.com/azure/azure-iot-sdk-java)
- [Azure IoT SDK for .NET](https://github.com/azure/azure-iot-sdk-csharp)

各デバイスは、デバイスを識別する一意の接続文字列を使用して接続します。 デバイスは登録されている IoT ハブにのみ接続できます。 Azure IoT Central アプリケーションで実際のデバイスを作成すると、アプリケーションは、使用する接続文字列を生成します。

## <a name="sdk-features-and-iot-hub-connectivity"></a>SDK の機能と IoT Hub の接続機能

IoT Hub を使用するすべてのデバイス通信では、次の IoT Hub 接続オプションを使用します。

- [デバイスからクラウドへのメッセージ](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c)
- [デバイス ツイン](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-device-twins)

次の表では、Azure IoT Central デバイスの機能が IoT Hub の機能にどのように対応しているかをまとめたものです。

| Azure IoT Central | Azure IoT Hub |
| ----------- | ------- |
| 測定: 利用統計情報 | デバイスからクラウドへのメッセージ |
| デバイスのプロパティ | デバイス ツインの報告されるプロパティ |
| 設定 | デバイス ツインの目的および報告されるプロパティ |

デバイス SDK の使用の詳細については、次に関連する記事のコード例を参照してください。

- [汎用の Node.js クライアント を Azure IoT Central アプリケーションに接続する](howto-connect-nodejs.md)
- [Raspberry Pi デバイスを Azure IoT Central アプリケーションに接続する ](howto-connect-raspberry-pi-python.md)
- [DevDiv キット デバイスを Azure IoT Central アプリケーションに接続する ](howto-connect-devkit.md)

次の動画では、Azure IoT Central に実際のデバイスを接続する方法を紹介しています。

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Connect-real-devices-to-Microsoft-IoT-Central/Player]

## <a name="protocols"></a>プロトコル

デバイス SDK は、次のネットワーク プロトコルを使った IoT Hub への接続をサポートしています。

- MQTT
- AMQP
- HTTPS

これらプロトコルの相違点や、プロトコルを選択する上でのガイダンスについては、「[通信プロトコルの選択](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-protocols)」を参照してください。

デバイスでサポートされているプロトコルのいずれも使用できない場合は、Azure IoT Edge を使用して、プロトコルを変換します。 IoT Edge は、Azure IoT Central アプリケーションの処理負荷を軽減するための他のエッジ上のインテリジェンス シナリオをサポートします。

## <a name="security"></a>セキュリティ

デバイスと、Azure IoT Central 間で交換されるすべてのデータは暗号化されます。 IoT Hub は IoT Hub エンドポイントに接続するデバイスからのすべての要求を認証します。 有線上で資格情報を交換することを避けるため、デバイスでは、認証に署名入りトークンが使用されます。 詳細については、「[IoT Hub へのアクセスの制御](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security)」を参照してください。

> [!NOTE]
> 現時点では、Azure IoT Central に接続するデバイスは、SAS トークンを使用する必要があります。 Azure IoT Central に接続するデバイスでは、X.509 証明書はサポートされていません。

## <a name="next-steps"></a>次の手順

ここでは、Azure IoT Central のデバイス接続機能について説明しました。推奨される次の手順は次のとおりです。

- [DevKit デバイスを準備して接続する](howto-connect-devkit.md)
- [Raspberry Pi を準備して接続する](howto-connect-raspberry-pi-python.md)
- [汎用の Node.js クライアント を Azure IoT Central アプリケーションに接続する](howto-connect-nodejs.md)
