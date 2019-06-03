---
title: Azure IoT Hub の AMQP サポートについて | Microsoft Docs
description: 開発者ガイド - AMQP プロトコルを使用して IoT Hub デバイスに接続されているエンドポイントとサービスに接続されているエンドポイントに接続するデバイスをサポートします。 Azure IoT device SDK での組み込み AMQP サポートについての情報が含まれます。
author: rezasherafat
manager: ''
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/30/2019
ms.author: rezas
ms.openlocfilehash: 703e2c842fb42bad8aa112d84c516a29c2327378
ms.sourcegitcommit: 399db0671f58c879c1a729230254f12bc4ebff59
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/09/2019
ms.locfileid: "65473435"
---
# <a name="communicate-with-your-iot-hub-using-the-amqp-protocol"></a>AMQP プロトコルを使用した IoT Hub との通信

IoT Hub では、デバイスに接続されているエンドポイントとサービスに接続されているエンドポイントからさまざまな機能を提供するために、[AMQP バージョン 1.0](http://docs.oasis-open.org/amqp/core/v1.0/os/amqp-core-complete-v1.0-os.pdf) をサポートします。 このドキュメントでは、IoT Hub の機能を使用するために、IoT Hub に接続する AMQP クライアントの使用について説明します。

## <a name="service-client"></a>サービス クライアント

### <a name="connection-and-authenticating-to-iot-hub-service-client"></a>IoT Hub への接続と認証 (サービス クライアント)
AMQP を使用して IoT Hub に接続するには、クライアントは[クレーム ベース セキュリティ (CBS)](https://www.oasis-open.org/committees/download.php/60412/amqp-cbs-v1.0-wd03.doc) または[簡易認証およびセキュリティ層 (SASL) の認証](https://en.wikipedia.org/wiki/Simple_Authentication_and_Security_Layer)を使用できます。

サービス クライアントでは、次の情報が必要になります。

| 情報 | 値 | 
|-------------|--------------|
| IoT Hub ホスト名 | `<iot-hub-name>.azure-devices.net` |
| キー名 | `service` |
| アクセス キー | サービスに関連付けられたプライマリ キーまたはセカンダリ キー |
| Shared Access Signature | 次の形式での存続期間が短い SAS: `SharedAccessSignature sig={signature-string}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}` (この署名を生成するコードは[ここ](./iot-hub-devguide-security.md#security-token-structure)で見つけることができます)


以下のコード スニペットでは、[Python で uAMQP ライブラリ](https://github.com/Azure/azure-uamqp-python)を使用して、送信者リンク経由で IoT ハブに接続します。

```python
import uamqp
import urllib
import time

# Use generate_sas_token implementation available here: https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security#security-token-structure
from helper import generate_sas_token

iot_hub_name = '<iot-hub-name>'
hostname = '{iot_hub_name}.azure-devices.net'.format(iot_hub_name=iot_hub_name)
policy_name = 'service'
access_key = '<primary-or-secondary-key>'
operation = '<operation-link-name>' # e.g., '/messages/devicebound'

username = '{policy_name}@sas.root.{iot_hub_name}'.format(iot_hub_name=iot_hub_name, policy_name=policy_name)
sas_token = generate_sas_token(hostname, access_key, policy_name)
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username), urllib.quote_plus(sas_token), hostname, operation)

# Create a send or receive client
send_client = uamqp.SendClient(uri, debug=True)
receive_client = uamqp.ReceiveClient(uri, debug=True)
```

### <a name="invoking-cloud-to-device-messages-service-client"></a>cloud-to-device メッセージを呼び出す (サービス クライアント)
サービスと IoT Hub との間、およびデバイスと IoT Hub との間での cloud-to-device メッセージ交換は、[ここ](iot-hub-devguide-messages-c2d.md)に説明されています。 サービス クライアントでは、メッセージを送信して、以前にデバイスから送信されたメッセージに対するフィードバックを受信するために、以下に示された 2 つのリンクを使用します。

| 作成者 | リンクの種類 | リンク パス | 説明 |
|------------|-----------|-----------|-------------|
| Service | 送信者リンク | `/messages/devicebound` | デバイスが宛先の C2D メッセージは、サービスによってこのリンクに送信されます。 このリンク経由で送信されたメッセージには、ターゲット デバイスの受信者リンク パス (つまり `/devices/<deviceID>/messages/devicebound`) に設定された `To` プロパティがあります。 |
| Service | 受信者リンク | `/messages/serviceBound/feedback` | デバイスから取得する完了、拒否、破棄のフィードバック メッセージは、サービスによってこのリンクで受信されます。 フィードバック メッセージの詳細については、[こちら](./iot-hub-devguide-messages-c2d.md#message-feedback)を参照してください。 |

以下のコード スニペットでは、[Python の uAMQP ライブラリ](https://github.com/Azure/azure-uamqp-python)を使用して、C2D メッセージを作成し、デバイスに送信する方法について示します。

```python
import uuid
# Create a message and set message property 'To' to the devicebound link on device
msg_id = str(uuid.uuid4())
msg_content = b"Message content goes here!"
device_id = '<device-id>'
to = '/devices/{device_id}/messages/devicebound'.format(device_id=device_id)
ack = 'full' # Alternative values are 'positive', 'negative', and 'none'
app_props = { 'iothub-ack': ack }
msg_props = uamqp.message.MessageProperties(message_id=msg_id, to=to)
msg = uamqp.Message(msg_content, properties=msg_props, application_properties=app_props)

# Send the message using the send client created and connected IoT Hub earlier
send_client.queue_message(msg)
results = send_client.send_all_messages()

# Close the client if not needed
send_client.close()
```

フィードバックを受信するために、サービス クライアントによって受信者リンクが作成されます。 以下のコード スニペットでは、[Python の uAMQP ライブラリ](https://github.com/Azure/azure-uamqp-python)を使用してこれを行う方法を示します。

```python
import json

operation = '/messages/serviceBound/feedback'

# ...
# Recreate the URI using the feedback path above and authenticate
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username), urllib.quote_plus(sas_token), hostname, operation)

receive_client = uamqp.ReceiveClient(uri, debug=True)
batch = receive_client.receive_message_batch(max_batch_size=10)
for msg in batch:
  print('received a message')
  # Check content_type in message property to identify feedback messages coming from device
  if msg.properties.content_type == 'application/vnd.microsoft.iothub.feedback.json':
    msg_body_raw = msg.get_data()
    msg_body_str = ''.join(msg_body_raw)
    msg_body = json.loads(msg_body_str)
    print(json.dumps(msg_body, indent=2))
    print('******************')
    for feedback in msg_body:
      print('feedback received')
      print('\tstatusCode: ' + str(feedback['statusCode']))
      print('\toriginalMessageId: ' + str(feedback['originalMessageId']))
      print('\tdeviceId: ' + str(feedback['deviceId']))
      print
  else:
    print('unknown message:', msg.properties.content_type)
```

上述のように、C2D フィードバック メッセージには、`application/vnd.microsoft.iothub.feedback.json` のコンテンツの種類と元のメッセージにおける配信の状態を推論するために使用できるその JSON 本体のプロパティが含まれます。
* フィードバックの本文のキー `statusCode` には、これらの値のいずれかが含まれます: `['Success', 'Expired', 'DeliveryCountExceeded', 'Rejected', 'Purged']`。
* フィードバックの本文のキー `deviceId` には、ターゲット デバイスの ID が含まれます。
* フィードバックの本文のキー `originalMessageId` には、サービスによって送信された元の C2D メッセージの ID が含まれます。 これはフィードバックを C2D メッセージに関連付けるために使用できます。

### <a name="receive-telemetry-messages-service-client"></a>テレメトリのメッセージの受信 (サービス クライアント)


### <a name="additional-notes"></a>その他のメモ
* AMQP 接続は、ネットワーク障害、または (コード内で生成された) 認証トークンの有効期限のために、中断される可能性があります。 サービス クライアントでは、これらの状況に対処し、必要に応じて、接続とリンクを再確立する必要があります。 認証トークンの有効期限の場合、接続を解除しないように、クライアントによって先を見越して、有効期限の前にトークンを更新することもできます。
* 場合によっては、クライアントは正常にリンクのリダイレクトを処理できる必要があります。 これを行う方法については、AMQP クライアントのドキュメントを参照してください。

### <a name="receive-cloud-to-device-messages-device-and-module-client"></a>cloud-to-device メッセージを受信する (デバイスとモジュールのクライアント)
デバイス側で使用される AMQP のリンクは次のとおりです。

| 作成者 | リンクの種類 | リンク パス | 説明 |
|------------|-----------|-----------|-------------|
| デバイス | 受信者リンク | `/devices/<deviceID>/messages/devicebound` | デバイスが宛先の C2D メッセージは、各宛先デバイスによってこのリンクで受信されます。 |
| デバイス | 送信者リンク | `/messages/serviceBound/feedback` | C2D メッセージのフィードバックは、デバイスによってこのリンク経由でサービスに送信されます。 |
| モジュール | 受信者リンク | `/devices/<deviceID>/modules/<moduleID>/messages/devicebound` | モジュールが宛先の C2D メッセージは、各宛先モジュールによってこのリンクで受信されます。 |
| モジュール | 送信者リンク | `/messages/serviceBound/feedback` | C2D メッセージのフィードバックは、モジュールによってこのリンク経由でサービスに送信されます。 |


## <a name="next-steps"></a>次の手順

AMQP プロトコルの詳細については、[AMQP v1.0 の仕様](http://www.amqp.org/sites/amqp.org/files/amqp.pdf)を参照してください。

IoT Hub のメッセージングの詳細については、以下を参照してください。

* [cloud-to-device メッセージ](./iot-hub-devguide-messages-c2d.md)
* [その他のプロトコルのサポート](iot-hub-protocol-gateway.md)
* [MQTT プロトコルのサポート](./iot-hub-mqtt-support.md)
