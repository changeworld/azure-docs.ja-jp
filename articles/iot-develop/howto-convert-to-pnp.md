---
title: IoT プラグ アンド プレイを使用するように既存のデバイスを変換する | Microsoft Docs
description: この記事では、デバイス モデルを作成し、デバイスの接続時にモデル ID を送信することで、IoT プラグ アンド プレイを使用して機能するように既存のデバイス コードを変換する方法について説明します。
author: dominicbetts
ms.author: dobett
ms.date: 05/14/2021
ms.topic: how-to
ms.service: iot-develop
services: iot-develop
ms.openlocfilehash: 225b999053824513e41d29d80a47a596fdedf991
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/22/2021
ms.locfileid: "114442571"
---
# <a name="how-to-convert-an-existing-device-to-be-an-iot-plug-and-play-device"></a>既存のデバイスを IoT プラグ アンド プレイ デバイスに変換する方法

この記事では、既存のデバイスを IoT プラグ アンド プレイ デバイスに変換する手順の概要について説明します。 また、すべての IoT プラグ アンド プレイ デバイスに必要なモデルを作成する方法と、デバイスを IoT プラグ アンド プレイ デバイスとして機能させるために必要なコードの変更について説明します。

この記事では、コード サンプルとして、MQTT ライブラリを使用して IoT ハブに接続する C コードを紹介しています。 この記事で説明する変更は、他の言語や SDK を使用して実装されたデバイスにも適用できます。

既存のデバイスを IoT プラグ アンド プレイ デバイスに変換するには:

1. デバイス コードを確認し、実装されているテレメトリ、プロパティ、コマンドを理解します。
1. デバイスに実装するテレメトリ、プロパティ、コマンドを記述したモデルを作成します。
1. サービスへの接続時にモデル ID が通知されるようにデバイス コードを変更します。

## <a name="review-your-device-code"></a>デバイス コードを確認する

デバイスのモデルを作成する前に、デバイスの既存の機能を理解しておく必要があります。

- デバイスから定期的に送信されるテレメトリ。
- デバイスがサービスと同期する読み取り専用プロパティと書き込み可能なプロパティ。
- デバイスが応答するサービスから呼び出されるコマンド。

たとえば、さまざまなデバイス機能を実装する次のデバイス コード スニペットを確認してください。 これらの例は、前の [PnPMQTTWin32-Before](https://github.com/Azure-Samples/IoTMQTTSample/tree/master/src/Windows/PnPMQTTWin32-Before) のサンプルに基づいています。

次のスニペットは、温度テレメトリを送信するデバイスを示しています。

```c
#define TOPIC "devices/" DEVICEID "/messages/events/"

// ...

void Thermostat_SendCurrentTemperature()
{
  char msg[] = "{\"temperature\":25.6}";
  int msgId = rand();
  int rc = mosquitto_publish(mosq, &msgId, TOPIC, sizeof(msg) - 1, msg, 1, true);
  if (rc != MOSQ_ERR_SUCCESS)
  {
    printf("Error: %s\r\n", mosquitto_strerror(rc));
  }
}
```

テレメトリ フィールドの名前は `temperature` であり、型は float または double です。 このテレメトリの種類のモデル定義は、次の JSON のようになります。 モードについては、次の「[モデルを設計する](#design-a-model)」を参照してください。

```json
{
  "@type": [
    "Telemetry"
  ],
  "name": "temperature",
  "displayName": "Temperature",
  "description": "Temperature in degrees Celsius.",
  "schema": "double"
}
```

次のスニペットは、プロパティ値を報告するデバイスを示しています。

```c
#define DEVICETWIN_MESSAGE_PATCH "$iothub/twin/PATCH/properties/reported/?$rid=patch_temp"

static void SendMaxTemperatureSinceReboot()
{
  char msg[] = "{\"maxTempSinceLastReboot\": 42.500}";
  int msgId = rand();
  int rc = mosquitto_publish(mosq, &msgId, DEVICETWIN_MESSAGE_PATCH, sizeof(msg) - 1, msg, 1, true);
  if (rc != MOSQ_ERR_SUCCESS)
  {
    printf("Error: %s\r\n", mosquitto_strerror(rc));
  }
}
```

プロパティの名前は `maxTempSinceLastReboot` であり、型は float または double です。 このプロパティはデバイスから報告されますが、デバイスがサービスからこの値の更新を受け取ることはありません。 このプロパティのモデル定義は、次の JSON のようになります。 モードについては、次の「[モデルを設計する](#design-a-model)」を参照してください。

```json
{
  "@type": [
    "Property"
  ],
  "name": "maxTempSinceLastReboot",
  "schema": "double",
  "displayName": "Max temperature since last reboot.",
  "description": "Returns the max temperature since last device reboot."
}
```

次のスニペットは、サービスからのメッセージに応答するデバイスを示しています。

```c
void message_callback(struct mosquitto* mosq, void* obj, const struct mosquitto_message* message)
{
  printf("Message received: %s payload: %s \r\n", message->topic, (char*)message->payload);
  
  if (strncmp(message->topic, "$iothub/methods/POST/getMaxMinReport/?$rid=1",37) == 0)
  {
    char* pch;
    char* context;
    int msgId = 0;
    pch = strtok_s((char*)message->topic, "=",&context);
    while (pch != NULL)
    {
      pch = strtok_s(NULL, "=", &context);
      if (pch != NULL) {
        char * pEnd;
        msgId = strtol(pch,&pEnd,16 );
      }
    }
    char topic[64];
    sprintf_s(topic, "$iothub/methods/res/200/?$rid=%d", msgId);
    char msg[] = "{\"maxTemp\":83.51,\"minTemp\":77.68}";
    int rc = mosquitto_publish(mosq, &msgId, topic, sizeof(msg) - 1, msg, 1, true);
    if (rc != MOSQ_ERR_SUCCESS)
    {
      printf("Error: %s\r\n", mosquitto_strerror(rc));
    }
    delete pch;
  }

  if (strncmp(message->topic, "$iothub/twin/PATCH/properties/desired/?$version=1", 38) == 0)
  {
    char* pch;
    char* context;
    int version = 0; 
    pch = strtok_s((char*)message->topic, "=", &context);
    while (pch != NULL)
    {
      pch = strtok_s(NULL, "=", &context);
      if (pch != NULL) {
        char* pEnd;
        version = strtol(pch, &pEnd, 10);
      }
    }
    // To do: Parse payload and extract target value
    char msg[128];
    int value = 46;
    sprintf_s(msg, "{\"targetTemperature\":{\"value\":%d,\"ac\":200,\"av\":%d,\"ad\":\"success\"}}", value, version);
    int rc = mosquitto_publish(mosq, &version, DEVICETWIN_MESSAGE_PATCH, strlen(msg), msg, 1, true);
    if (rc != MOSQ_ERR_SUCCESS)
    {
      printf("Error: %s\r\n", mosquitto_strerror(rc));
    }
    delete pch;
  }
}
```

`$iothub/methods/POST/getMaxMinReport/` トピックは、サービスから `getMaxMinReport` というコマンドの要求を受け取ります。この要求には、コマンド パラメーターと共にペイロードが含まれる可能性があります。 デバイスからは、`maxTemp` と `minTemp` の値を含むペイロードを含む応答が送信されます。

`$iothub/twin/PATCH/properties/desired/` トピックは、サービスからプロパティの更新を受け取ります。 この例では、プロパティの更新が `targetTemperature` プロパティに対するものであると仮定しています。 `{\"targetTemperature\":{\"value\":46,\"ac\":200,\"av\":12,\"ad\":\"success\"}}` のような受信確認で応答します。

要約すると、このサンプルには次の機能が実装されています。

| 名前                   | 機能の種類   | 詳細 |
| ---------------------- | ----------------- | ------- |
| 温度            | テレメトリ         | データ型が double であると仮定します |
| maxTempSinceLastReboot | プロパティ          | データ型が double であると仮定します |
| targetTemperature      | 書き込み可能なプロパティ | データ型は整数です。 |
| getMaxMinReport        | command           | double 型の `maxTemp` フィールドと `minTemp` フィールドがある JSON を返します |

## <a name="design-a-model"></a>モデルを設計する

すべての IoT プラグ アンド プレイ デバイスには、デバイスの特徴と機能を記述したモデルがあります。 モデルには、デバイスの機能を記述するために[デジタル ツイン定義言語 (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) が使用されます。

デバイスの既存の機能をマップする簡単なモデルには、*Telemetry*、*Property*、*Command* の各 DTDL 要素を使用します。

前のセクションで説明したサンプルの DTDL モデルは、次の例のようになります。

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:ConvertSample;1",
  "@type": "Interface",
  "displayName": "Simple device",
  "description": "Example that shows model for simple device converted to act as an IoT Plug and Play device.",
  "contents": [
    {
      "@type": [
        "Telemetry",
        "Temperature"
      ],
      "name": "temperature",
      "displayName": "Temperature",
      "description": "Temperature in degrees Celsius.",
      "schema": "double",
      "unit": "degreeCelsius"
    },
    {
      "@type": [
        "Property",
        "Temperature"
      ],
      "name": "targetTemperature",
      "schema": "double",
      "displayName": "Target Temperature",
      "description": "Allows to remotely specify the desired target temperature.",
      "unit": "degreeCelsius",
      "writable": true
    },
    {
      "@type": [
        "Property",
        "Temperature"
      ],
      "name": "maxTempSinceLastReboot",
      "schema": "double",
      "unit": "degreeCelsius",
      "displayName": "Max temperature since last reboot.",
      "description": "Returns the max temperature since last device reboot."
    },
    {
      "@type": "Command",
      "name": "getMaxMinReport",
      "displayName": "Get Max-Min report.",
      "description": "This command returns the max and min temperature.",
      "request": {
      },
      "response": {
        "name": "tempReport",
        "displayName": "Temperature Report",
        "schema": {
          "@type": "Object",
          "fields": [
            {
              "name": "maxTemp",
              "displayName": "Max temperature",
              "schema": "double"
            },
            {
              "name": "minTemp",
              "displayName": "Min temperature",
              "schema": "double"
            }
          ]
        }
      }
    }
  ]
}
```

このモデルの内容:

- `name` と `schema` の値は、デバイスが送信および受信するデータにマップされます。
- すべての機能は 1 つのインターフェイスにまとめられています。
- `@type` フィールドには、**Property** や **Command** などの DTDL 型が指定されています。
- `unit`、`displayName`、`description` などのフィールドには、サービスに使用する追加情報が指定されています。 たとえば、IoT Central には、デバイスのダッシュボードにデータを表示するときにこれらの値が使用されます。

詳細については、「[IoT プラグ アンド プレイ規則](concepts-convention.md)」と「[IoT プラグ アンド プレイ モデリング ガイド](concepts-modeling-guide.md)」を参照してください。

## <a name="update-the-code"></a>コードを更新する

デバイスが既に IoT Hub または IoT Central と連携している場合は、テレメトリ、プロパティ、コマンドの各機能の実装に変更を加える必要はありません。 デバイスを IoT プラグ アンド プレイ規則に準拠させるには、デバイスがサービスに接続する方法を変更して、作成したモデルの ID を通知するようにします。 これで、サービスからこのモデルを使用し、デバイスの機能を認識できるようになります。 たとえば、IoT Central にモデル ID を使用して、リポジトリからモデルを自動的に取得し、デバイス用のデバイス テンプレートを生成することができます。

IoT デバイスは、デバイス プロビジョニング サービス (DPS) を介して、または接続文字列を使用して直接、IoT サービスに接続します。

デバイスに DPS を使用して接続する場合は、デバイスを登録するときに送信するペイロードにモデル ID を含めます。 前述のモデルの例では、ペイロードは次のようになります。

```json
{
  "modelId" : "dtmi:com:example:ConvertSample;1"
}
```

詳細については、「[ランタイムの登録 - デバイスの登録](/rest/api/iot-dps/device/runtime-registration/register-device)」を参照してください。

デバイスに DPS を使用して接続する場合や、接続文字列を使用して直接接続する場合は、コードから IoT Hub に接続するときにモデル ID を含めます。 次に例を示します。

```c
#define USERNAME IOTHUBNAME ".azure-devices.net/" DEVICEID "/?api-version=2020-09-30&model-id=dtmi:com:example:ConvertSample;1"

// ...

mosquitto_username_pw_set(mosq, USERNAME, PWD);

// ...

rc = mosquitto_connect(mosq, HOST, PORT, 10);
```

## <a name="next-steps"></a>次のステップ

ここでは、既存のデバイスを IoT プラグ アンド プレイ デバイスに変換する方法を確認したので、お勧めする次のステップとして、「[IoT プラグ アンド プレイ モデリング ガイド](concepts-modeling-guide.md)」を参照してください。
