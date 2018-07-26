---
title: Azure IoT Hub cloud-to-device オプション | Microsoft Docs
description: 開発者ガイド - ダイレクト メソッド、デバイス ツインの必要なプロパティ、または cloud-to-device 通信用の cloud-to-device メッセージを使用するタイミングに関するガイダンス。
author: fsautomata
manager: ''
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.author: elioda
ms.openlocfilehash: 2cc9bd39371741caaa3ae025df494e225dc754b0
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/20/2018
ms.locfileid: "39187038"
---
# <a name="cloud-to-device-communications-guidance"></a>cloud-to-device 通信に関するガイダンス
IoT Hub では、デバイス アプリがバックエンド アプリに機能を公開するための 3 つのオプションがあります。

* [ダイレクト メソッド][lnk-methods]: 結果を即座に確認することが求められる通信用。 ダイレクト メソッドは、対話型のデバイス制御によく使用されます (ファンの電源を投入するなど)。
* [デバイス ツインの必要なプロパティ][lnk-twins]: デバイスを特定の状態に置いておくために長時間実行されるコマンド用。 たとえば、テレメトリの送信間隔を 30 分に設定します。
* [cloud-to-device メッセージ][lnk-c2d]: デバイス アプリに対する一方向の通知用。

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

さまざまな cloud-to-device 通信オプションの詳細な比較を次に示します。

|  | ダイレクト メソッド | デバイス ツインの必要なプロパティ | クラウドからデバイスへのメッセージ |
| ---- | ------- | ---------- | ---- |
| シナリオ | すぐに確認する必要があるコマンド (例: ファンをオンにする)。 | デバイスを特定の状態に置いておくために長時間実行されるコマンド。 たとえば、テレメトリの送信間隔を 30 分に設定します。 | デバイス アプリに対する一方向の通知です。 |
| データ フロー | 双方向。 デバイス アプリは、メソッドにすぐに応答できます。 ソリューション バックエンドは、コンテキストから要求の結果を受信します。 | 一方向。 デバイス アプリは、プロパティが変更された通知を受信します。 | 一方向。 デバイス アプリは、メッセージを受信します。
| 持続性 | 切断されているデバイスとは通信しません。 ソリューション バックエンドには、デバイスが接続されていないことが通知されます。 | プロパティの値は、デバイス ツインに保持されます。 デバイスは、次の再接続時にそれを読み取ります。 プロパティの値は [IoT Hub クエリ言語][lnk-query]を使用して取得できます。 | メッセージは、IoT Hub によって最大 48 時間保持できます。 |
| ターゲット | **deviceId** を使用する場合は 1 台のデバイス、[jobs][lnk-jobs] を使用する場合は複数台のデバイス。 | **deviceId** を使用する場合は 1 台のデバイス、[jobs][lnk-jobs] を使用する場合は複数台のデバイス。 | **deviceId** による 1 台のデバイス。 |
| サイズ | ダイレクト メソッドの最大ペイロード サイズは 128 KB。 | 必要なプロパティの最大サイズは 8 KB です。 | 最大 64 KB のメッセージ。 |
| 頻度 | 高。 詳細については、[IoT Hub の制限][lnk-quotas]に関するページを参照してください。 | 中。 詳細については、[IoT Hub の制限][lnk-quotas]に関するページを参照してください。 | 低。 詳細については、[IoT Hub の制限][lnk-quotas]に関するページを参照してください。 |
| プロトコル | MQTT または AMQP を使用して利用できます。 | MQTT または AMQP を使用して利用できます。 | すべてのプロトコルで使用できます。 HTTPS を使う場合、デバイスはポーリングを行う必要があります。 |

ダイレクト メソッド、必要なプロパティ、および cloud-to-device メッセージの使用方法については、次のチュートリアルを参照してください。

* ダイレクト メソッド: [ダイレクト メソッドを使用する][lnk-methods-tutorial]
* デバイス ツインの必要なプロパティ: [必要なプロパティを使用してデバイスを構成する][lnk-twin-properties] 
* cloud-to-device メッセージ: [cloud-to-device メッセージを送信する][lnk-c2d-tutorial]

[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-jobs]: iot-hub-devguide-jobs.md
[lnk-c2d]: iot-hub-devguide-messages-c2d.md
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-methods-tutorial]: quickstart-control-device-node.md
[lnk-twin-properties]: tutorial-device-twins.md
[lnk-c2d-tutorial]: iot-hub-node-node-c2d.md
