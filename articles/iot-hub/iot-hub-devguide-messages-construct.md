---
title: Azure IoT Hub メッセージの形式について | Microsoft Docs
description: 開発者ガイド - IoT Hub メッセージの形式と予期される内容について説明します。
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/18/2018
ms.author: dobett
ms.openlocfilehash: 7c08848698f07d64bbbff429682c18525659f7bf
ms.sourcegitcommit: f94f84b870035140722e70cab29562e7990d35a3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/30/2018
ms.locfileid: "43286519"
---
# <a name="create-and-read-iot-hub-messages"></a>IoT Hub メッセージを作成し、読み取る

プロトコル間でのシームレスな相互運用性をサポートするために、IoT Hub では、すべてのデバイス用プロトコルの共通のメッセージ形式が定義されています。 このメッセージ形式は、[device-to-cloud][lnk-d2c] メッセージと [cloud-to-device][lnk-c2d] メッセージの両方で使用されます。 

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

[IoT Hub メッセージ][lnk-messaging]は、次のような構成です。

* 事前定義された一連の "*システム プロパティ*" を以下に示します。
* 一連の *アプリケーション プロパティ*。 メッセージ本文を逆シリアル化しなくてもアプリケーションが定義してアクセスできる、文字列プロパティのディクショナリです。 IoT Hub によってこれらのプロパティが変更されることはありません。
* 非透過的なバイナリ本文。

以下の場合は、プロパティ名と値に含めることができるのは、ASCII 英数字と ```{'!', '#', '$', '%, '&', "'", '*', '+', '-', '.', '^', '_', '`', '|', '~'}``` のみです。  

* HTTPS プロトコルを使用して device-to-cloud メッセージを送信します。
* クラウドからデバイスへのメッセージを送信します。

各種プロトコルを使用したメッセージのエンコードとデコードの方法の詳細については、[Azure IoT SDK][lnk-sdks] に関するページをご覧ください。

次のテーブルは、IoT Hub メッセージ内の一連のシステム プロパティです。

| プロパティ | 説明 | ユーザーが設定可能 |
| --- | --- | --- |
| MessageId |要求/応答パターンに使用する、メッセージのユーザー設定 ID。 形式: ASCII 7 ビット英数字の大文字と小文字が区別される文字列 (最大 128 文字) + `{'-', ':',’.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '$', '''}`。 | はい |
| Sequence number |IoT Hub によって各 C2D メッセージに割り当てられる数値 (デバイスとキューごとに一意)。 | C2D メッセージの場合は「いいえ」。それ以外の場合は「はい」。 |
| ターゲット |[C2D][lnk-c2d] メッセージで指定される宛先。 | C2D メッセージの場合は「いいえ」。それ以外の場合は「はい」。 |
| ExpiryTimeUtc |メッセージの有効期限の日時。 | はい |
| EnqueuedTime |IoT Hub が[クラウドからデバイス][lnk-c2d]へのメッセージを受信した日時。 | C2D メッセージの場合は「いいえ」。それ以外の場合は「はい」。 |
| CorrelationId |通常、要求/応答パターンで要求の MessageId を格納する、応答メッセージの文字列プロパティ。 | はい |
| UserId |メッセージの送信元を指定するために使用される ID。 IoT Hub でメッセージが生成されると、 `{iot hub name}`に設定されます。 | いいえ  |
| Ack |フィードバック メッセージのジェネレーター。 このプロパティは、デバイスがメッセージを使用した結果としてのフィードバック メッセージの生成を IoT Hub に要求するために、C2D メッセージで使用されます。 使用可能な値: **none** (既定値): フィードバック メッセージは生成されません。**positive**: メッセージが完了した場合にフィードバック メッセージを受信します。**negative**: デバイスでメッセージが完了しないまま、メッセージの有効期限が切れた場合 (または最大配信数に達した場合) にフィードバック メッセージを受信します。**full**: positive と negative の両方の値を意味します。 詳細については、「[メッセージのフィードバック][lnk-feedback]を参照してください。 | [はい] |
| ConnectionDeviceId |IoT Hub で D2C メッセージに対して設定される ID。 メッセージを送信したデバイスの **deviceId** が含まれます。 | D2C メッセージの場合は「いいえ」。それ以外の場合は「はい」。 |
| ConnectionDeviceGenerationId |IoT Hub で D2C メッセージに対して設定される ID。 メッセージを送信したデバイスの **generationId** (「[デバイス ID のプロパティ][lnk-device-properties]」を参照) が含まれています。 | D2C メッセージの場合は「いいえ」。それ以外の場合は「はい」。 |
| ConnectionAuthMethod |IoT Hub で D2C メッセージに対して設定される認証方法。 このプロパティには、メッセージを送信するデバイスの認証に使用する認証方法に関する情報が含まれます。 詳細については、[D2C のなりすまし対策][lnk-antispoofing]に関するセクションを参照してください。 | D2C メッセージの場合は「いいえ」。それ以外の場合は「はい」。 |
| CreationTimeUtc | デバイスでメッセージが作成された日時。 デバイスでこの値を明示的に設定する必要があります。 | はい |

## <a name="message-size"></a>メッセージ サイズ

IoT Hub では、メッセージのサイズは、プロトコルに関係なく実際のペイロードのみを考慮して測定されます。 バイト単位のサイズは次の和として計算されます。

* 本文のサイズ (バイト単位)
* メッセージ システム プロパティの全値のサイズ (バイト単位)。
* すべてのユーザー プロパティの名前と値のサイズ (バイト単位)

プロパティの名前と値は ASCII 文字に制限されているため、文字列の長さがバイト単位のサイズと等しくなります。

## <a name="next-steps"></a>次の手順

IoT Hub でのメッセージ サイズの制限については、[IoT Hub のクォータと調整][lnk-quotas]に関するページをご覧ください。

IoT Hub メッセージをさまざまなプログラミング言語で作成し、読み取る方法については、[クイック スタート][lnk-get-started]をご覧ください。

[lnk-messaging]: iot-hub-devguide-messaging.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-get-started]: quickstart-send-telemetry-node.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-c2d]: iot-hub-devguide-messages-c2d.md
[lnk-d2c]: iot-hub-devguide-messages-d2c.md
[lnk-feedback]: iot-hub-devguide-messages-c2d.md#message-feedback
[lnk-device-properties]: iot-hub-devguide-identity-registry.md#device-identity-properties
[lnk-antispoofing]: iot-hub-devguide-messages-d2c.md#anti-spoofing-properties
