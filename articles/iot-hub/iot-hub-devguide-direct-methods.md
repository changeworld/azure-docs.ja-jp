---
title: Azure IoT Hub のデバイス メソッドについて | Microsoft Docs
description: 開発者ガイド - ダイレクト メソッドを使用して、サービス アプリからデバイス上のコードを呼び出す。
author: nberdy
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/17/2018
ms.author: nberdy
ms.openlocfilehash: 0b84d7b0e7bbd2021ea4d3e3e804c739be59b48a
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/20/2018
ms.locfileid: "39186881"
---
# <a name="understand-and-invoke-direct-methods-from-iot-hub"></a>IoT Hub からのダイレクト メソッドの呼び出しについて
IoT Hub には、クラウドからデバイス上のダイレクト メソッドを呼び出す機能が備わっています。 ダイレクト メソッドは、デバイスとの要求/応答型通信を表し、すぐに要求の成功または失敗が確定する (ユーザーが指定したタイムアウト後) という点で HTTP 呼び出しに似ています。 この方法は、デバイスが応答できるかどうかに応じて即座に実行するアクションが異なるシナリオで便利です。

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

各デバイス メソッドは、1 つのデバイスをターゲットとします。 [Jobs][lnk-devguide-jobs] を使用すると、複数のデバイス上のダイレクト メソッドを呼び出すことができ、接続されていないデバイスに対するメソッドの呼び出しをスケジュール設定できます。

IoT Hub で**サービス接続**のアクセス許可を持っていれば、誰でもデバイスでメソッドを呼び出すことができます。

ダイレクト メソッドは要求/応答型パターンに従うメソッドであり、結果をすぐに確認する必要がある通信向けです。 たとえばデバイスを対話式で制御する (例: ファンをオンにする) ときに使用されます。

必要とされるプロパティ、ダイレクト メソッド、または C2D メッセージの使用方法の詳細については、「[Cloud-to-device communication guidance][lnk-c2d-guidance]」 (C2D 通信のガイダンス) を参照してください。

## <a name="method-lifecycle"></a>メソッドのライフサイクル
ダイレクト メソッドはデバイス上に実装され、正しくインスタンス化するには、メソッドのペイロードに 0 個以上の入力が必要になります。 ダイレクト メソッドは、サービス向け URI (`{iot hub}/twins/{device id}/methods/`) を通じて呼び出します。 デバイスは、デバイス固有の MQTT トピック (`$iothub/methods/POST/{method name}/`) または AMQP リンク (`IoThub-methodname` および `IoThub-status` アプリケーション プロパティ) を通じてダイレクト メソッドを受け取ります。 

> [!NOTE]
> デバイス上のダイレクト メソッドを呼び出す場合、プロパティ名と値には ``{'$', '(', ')', '<', '>', '@', ',', ';', ':', '\', '"', '/', '[', ']', '?', '=', '{', '}', SP, HT}`` を除く US-ASCII 印刷可能英数字のみを使用できます。
> 
> 

ダイレクト メソッドは同期型であり、タイムアウト期間 (既定では 30 秒、最長 3600 秒に設定可能) 後に成功または失敗します。 ダイレクト メソッドは、デバイスがオンラインでコマンドを受け取っている場合のみデバイスを操作する対話型のシナリオで便利です。 たとえば、携帯電話からの照明を点灯させる場合です。 このようなシナリオでは、成功か失敗かを即座に確認し、クラウド サービスがその結果に対してできるだけ早く対応することが必要になります。 デバイスはメッセージの結果として何らかのメッセージ本文を返すことがありますが、メソッドにはその機能は必要ありません。 順番の保証や、メソッドの呼び出しの同時実行セマンティクスはありません。

ダイレクト メソッドは、クラウド側からは HTTPS のみに、デバイス側からは MQTT または AMQP になります。

メソッドの要求および応答のペイロードは、最大 128 KB の JSON ドキュメントになります。

## <a name="invoke-a-direct-method-from-a-back-end-app"></a>バックエンド アプリからダイレクト メソッドを呼び出す
### <a name="method-invocation"></a>メソッドの呼び出し
デバイスでのダイレクト メソッドの呼び出しは HTTPS 呼び出しであり、次の項目で構成されます。

* [API バージョン](/rest/api/iothub/service/invokedevicemethod)が適合するデバイスに固有の*要求の URI*:

    ```http
    https://fully-qualified-iothubname.azure-devices.net/twins/{deviceId}/methods?api-version=2018-06-30
    ```

* POST *メソッド*
* *ヘッダー*。承認、要求 ID、コンテンツの種類、コンテンツのエンコーディングを含む
* 透過的な JSON *本文*。次の形式になります。

    ```json
    {
        "methodName": "reboot",
        "responseTimeoutInSeconds": 200,
        "payload": {
            "input1": "someInput",
            "input2": "anotherInput"
        }
    }
    ```

タイムアウトは秒単位です。 タイムアウトが設定されていない場合の既定値は 30 秒です。

#### <a name="example"></a>例

`curl` を使用したベアボーンの例については、以下を参照してください。 

```bash
curl -X POST \
  https://iothubname.azure-devices.net/twins/myfirstdevice/methods?api-version=2018-06-30 \
  -H 'Authorization: SharedAccessSignature sr=iothubname.azure-devices.net&sig=x&se=x&skn=iothubowner' \
  -H 'Content-Type: application/json' \
  -d '{
    "methodName": "reboot",
    "responseTimeoutInSeconds": 200,
    "payload": {
        "input1": "someInput",
        "input2": "anotherInput"
    }
}'
```

### <a name="response"></a>Response
バックエンド アプリは次の項目で構成される応答を受け取ります。

* *HTTP 状態コード*。接続されていないデバイスの 404 エラーを含む、IoT Hub からのエラーに使用される
* *ヘッダー*。ETag、要求 ID、コンテンツの種類、コンテンツのエンコーディングを含む
* JSON *本文*。次の形式になります。

    ```json
    {
        "status" : 201,
        "payload" : {...}
    }
    ```

    `status` と `body` の両方ともデバイスによって提供され、デバイス自身の状態コードまたは説明とともに応答する場合に使用されます。

### <a name="method-invocation-for-iot-edge-modules"></a>IoT Edge モジュールのメソッド呼び出し
モジュール ID を使用したダイレクト メソッドの呼び出しは、プレビュー版の C# SDK ([ここ](https://www.nuget.org/packages/Microsoft.Azure.Devices/1.16.0-preview-004)で入手できます) でサポートされています。

この目的で、`ServiceClient.InvokeDeviceMethodAsync()` メソッドを使用し、`deviceId` と `moduleId` をパラメーターとして渡します。

## <a name="handle-a-direct-method-on-a-device"></a>デバイスでダイレクト メソッドを処理する
### <a name="mqtt"></a>MQTT
#### <a name="method-invocation"></a>メソッドの呼び出し
デバイスは MQTT トピックに関するダイレクト メソッド要求を受け取ります。`$iothub/methods/POST/{method name}/?$rid={request id}`

デバイスが受け取る本文は次の形式になります。

```json
{
    "input1": "someInput",
    "input2": "anotherInput"
}
```

メソッドの要求は QoS 0 です。

#### <a name="response"></a>Response
デバイスは応答を `$iothub/methods/res/{status}/?$rid={request id}` に送信します。この場合、

* `status` プロパティは、デバイスから提供される、メソッド実行の状態です。
* `$rid` プロパティは、IoT Hub から受け取るメソッド呼び出しの要求 ID です。

本文はデバイスごとに設定され、任意の状態を設定できます。

### <a name="amqp"></a>AMQP
#### <a name="method-invocation"></a>メソッドの呼び出し
デバイスは、アドレス `amqps://{hostname}:5671/devices/{deviceId}/methods/deviceBound` に受信リンクを作成することによってダイレクト メソッド要求を受け取ります。

AMQP メッセージは、メソッド要求を表す受信リンクに到着します。 これには以下が含まれます。
* 関連付け ID プロパティ。対応するメソッド応答で戻される要求 ID が含まれています。
* `IoThub-methodname` という名前のアプリケーション プロパティ。呼び出されるメソッドの名前が含まれています。
* AMQP メッセージ本文。JSON としてメソッド ペイロードが含まれています。

#### <a name="response"></a>Response
デバイスは、メソッド応答を返す送信リンクをアドレス `amqps://{hostname}:5671/devices/{deviceId}/methods/deviceBound` に作成します。

メソッドの応答は送信リンクで返され、以下から構成されています。
* 関連付け ID プロパティ。メソッドの要求メッセージで渡される要求 ID が含まれています。
* `IoThub-status` という名前のアプリケーション プロパティ。ユーザーが指定したメソッド状態が含まれています。
* AMQP メッセージ本文。JSON としてメソッド応答が含まれています。

## <a name="additional-reference-material"></a>参考資料
IoT Hub 開発者ガイド内の他の参照トピックは次のとおりです。

* [IoT Hub エンドポイント][lnk-endpoints]: 各 IoT Hub でランタイムと管理の操作のために公開される、さまざまなエンドポイントについて説明します。
* [調整とクォータ][lnk-quotas]: IoT Hub の使用時に適用されるクォータと想定される調整の動作について説明します。
* [Azure IoT device SDK とサービス SDK][lnk-sdks]: IoT Hub とやりとりするデバイスとサービス アプリの両方を開発する際に使用できるさまざまな言語の SDK を紹介します。
* [デバイス ツイン、ジョブ、メッセージ ルーティングの IoT Hub クエリ言語][lnk-query]: デバイス ツインとジョブに関する情報を IoT Hub から取得するために使用できる IoT Hub クエリ言語について説明します。
* [IoT Hub の MQTT サポート][lnk-devguide-mqtt]: IoT Hub での MQTT プロトコルのサポートについて詳しく説明します。

## <a name="next-steps"></a>次の手順
ダイレクト メソッドの使用方法を理解できたら、次の IoT Hub 開発者ガイドの記事も参考にしてください。

* [複数デバイスでのジョブをスケジュール設定する][lnk-devguide-jobs]

この記事で説明した概念を試す場合は、次の IoT Hub のチュートリアルをご利用ください。

* [ダイレクト メソッドを使用する][lnk-methods-tutorial]

<!-- links and images -->

[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md

[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-methods-tutorial]: quickstart-control-device-node.md
[lnk-devguide-messages]: iot-hub-devguide-messaging.md
[lnk-c2d-guidance]: iot-hub-devguide-c2d-guidance.md
