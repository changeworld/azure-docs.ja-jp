---
title: Azure IoT Hub メッセージ ルーティングでのクエリ | Microsoft Docs
description: 開発者ガイド - Azure IoT Hub でのメッセージ ルーティングのクエリ構文。
author: ash2017
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/13/2018
ms.author: asrastog
ms.openlocfilehash: 8e9321e72727c1a3149ff2e78b8cb1248734cb88
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2018
ms.locfileid: "46978507"
---
# <a name="iot-hub-message-routing-query-syntax"></a>IoT Hub メッセージ ルーティングのクエリ構文

メッセージ ルーティングを使用すると、ユーザーは種類の異なるデータ (デバイス テレメトリ メッセージ、デバイス ライフサイクル イベント、デバイス ツイン変更イベント) を、さまざまなエンドポイントにルーティングできます。 さらにルーティング前には、お客様にとって重要なデータを受け取れるよう、上記のデータに高度なクエリを適用することもできます。 この記事では、IoT Hub メッセージ ルーティングのクエリ言語について説明するほか、一般的なクエリ パターンをいくつか紹介します。 

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

メッセージ ルーティングでは、メッセージ プロパティとメッセージ本文に基づいてクエリを実行できるほか、デバイス ツインのタグとプロパティに基づいてクエリを実行することもできます。 メッセージ本文が JSON でない場合、メッセージ ルーティングを使用してメッセージをルーティングできるものの、メッセージ本文にクエリを適用することはできません。  クエリはブール式として表されます。このブール式では、ブール値が true の場合にはクエリが成功し、受信データがすべてルーティングされます。また、ブール値が false の場合にはクエリが失敗し、データはルーティングされません。 式が null 値または未定義に評価されると、false として処理され、失敗時に診断ログでエラーが生成されます。 ルートが保存および評価されるようにするには、正しいクエリ構文を使用する必要があります。  

## <a name="message-routing-query-based-on-message-properties"></a>メッセージ プロパティに基づいたメッセージ ルーティング クエリ 

IoT ハブでは、プロトコル間の相互運用性を確保するために、すべての device-to-cloud メッセージング用に[共通の形式](../iot-hub/iot-hub-devguide-messages-construct.md)が定義されています。 IoT Hub メッセージでは、次の JSON 表現のメッセージが想定されます。 すべてのユーザーのシステム プロパティが追加され、メッセージのコンテンツが特定されます。 ユーザーは、アプリケーション プロパティをメッセージに選択的に追加できます。 IoT Hub の device-to-cloud メッセージでは大文字と小文字が区別されないため、一意のプロパティ名を使用することをお勧めします。 たとえば、名前が同じプロパティが複数ある場合は、いずれか 1 つのプロパティだけが IoT Hub によって送信されることになります。  

```json
{ 
  "message": { 
    "systemProperties": { 
      "contentType": "application/json", 
      "contentEncoding": "UTF-8", 
      "iothub-message-source": "deviceMessages", 
      "iothub-enqueuedtime": "2017-05-08T18:55:31.8514657Z" 
    }, 
    "appProperties": { 
      "processingPath": "{cold | warm | hot}", 
      "verbose": "{true, false}", 
      "severity": 1-5, 
      "testDevice": "{true | false}" 
    }, 
    "body": "{\"Weather\":{\"Temperature\":50}}" 
  } 
} 
```
### <a name="system-properties"></a>システム プロパティ

システム プロパティは、メッセージのコンテンツとソースを特定するのに役立ちます。 

| プロパティ | type | 説明 |
| -------- | ---- | ----------- |
| contentType | string | ユーザーはメッセージのコンテンツの種類を指定します。 メッセージ本文に基づいてクエリを実行するには、この値を application/json に設定する必要があります。 |
| contentEncoding | string | ユーザーはメッセージのエンコードの種類を指定します。 contentType が application/json に設定されている場合に使用できる値は、UTF-8、UTF-16、UTF-32 です。 |
| connectionDeviceId | string | この値は IoT Hub によって設定されます。この値によって、メッセージのソースが特定されます。 これは、デバイス テレメトリ メッセージ、デバイス ツイン変更通知、またはデバイス ライフサイクル イベントの場合があります。 これに対してクエリを実行することはできません。 |
| iothub-enqueuedtime | string | この値は IoT Hub によって設定されます。この値によって、メッセージがエンキューされた実際の時刻が UTC で表されます。 クエリを実行するには、`'enqueuedTime'` を使用します。 |

[IoT Hub のメッセージ](iot-hub-devguide-messages-construct.md)に関するページで説明されているように、メッセージには他にもシステム プロパティがあります。 **contentType**、**contentEncoding**、**enqueuedTime** に加えて、**connectionDeviceId** と **connectionModuleId** に対してもクエリを実行できます。

### <a name="application-properties"></a>Application properties

アプリケーション プロパティは、メッセージに追加できるユーザー定義の文字列です。 これらのフィールドは省略できます。  

### <a name="query-expressions"></a>クエリ式

メッセージ システム プロパティに基づくクエリには、前に `'$'` 記号を付ける必要があります。 アプリケーション プロパティに基づくクエリは、それらの名前を使用してアクセスされるので、前に `'$'` 記号を付ける必要がありません。 アプリケーション プロパティ名の先頭に `'$'` がある場合は、IoT Hub によってそのアプリケーション プロパティ名がシステム プロパティ内で検索されますが、見つからないため、アプリケーション プロパティ内が検索されることになります。 例:  

システム プロパティ contentEncoding に基づいてクエリを実行するには 

```sql
$contentEncoding = 'UTF-8'
```

アプリケーション プロパティ processingPath に基づいてクエリを実行するには
```sql
processingPath = 'hot'
```

これらのクエリを結合するために、ブール式と関数を使用することができます。 
```sql
$contentEncoding = 'UTF-8' AND processingPath = 'hot'
```

サポートされている演算子と関数の完全な一覧については、[式と条件](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-query-language#expressions-and-conditions
)に関するページを参照してください。

## <a name="message-routing-query-based-on-message-body"></a>メッセージ本文に基づいたメッセージ ルーティング クエリ 

メッセージ本文に基づいてクエリを実行するには、メッセージが UTF-8、UTF-16、UTF-32 のいずれかでエンコードされた JSON である必要があります。 システム プロパティで、`contentType` は `application/JSON` に、`contentEncoding` はサポートされているいずれかの UTF エンコードに設定する必要があります。 これらのプロパティが指定されていないと、IoT Hub でクエリ式がメッセージ本文に基づいて評価されません。 

次の例では、正しい形式でエンコードされた JSON 本文を持つメッセージを作成する方法を示します。 

```javascript
var messageBody = JSON.stringify(Object.assign({}, {
    "Weather": {
        "Temperature": 50,
        "Time": "2017-03-09T00:00:00.000Z",
        "PrevTemperatures": [
            20,
            30,
            40
        ],
        "IsEnabled": true,
        "Location": {
            "Street": "One Microsoft Way",
            "City": "Redmond",
            "State": "WA"
        },
        "HistoricalData": [
            {
                "Month": "Feb",
                "Temperature": 40
            },
            {
                "Month": "Jan",
                "Temperature": 30
            }
        ]
    }
}));

// Encode message body using UTF-8  
var messageBytes = Buffer.from(messageBody, "utf8");

var message = new Message(messageBytes);

// Set message body type and content encoding 
message.contentEncoding = "utf-8";
message.contentType = "application/json";

// Add other custom application properties   
message.properties.add("Status", "Active");

deviceClient.sendEvent(message, (err, res) => {
    if (err) console.log('error: ' + err.toString());
    if (res) console.log('status: ' + res.constructor.name);
});
```

### <a name="query-expressions"></a>クエリ式

メッセージ本文に基づくクエリには、前に `$body` を付ける必要があります。 クエリ式では、本文の参照、本文配列の参照、または複数の本文の参照を使用できます。 さらにクエリ式では、本文の参照を、メッセージ システム プロパティおよびメッセージ アプリケーション プロパティの参照と組み合わせることもできます。 たとえば、以下はすべて有効なクエリ式です。 

```sql
$body.Weather.HistoricalData[0].Month = 'Feb' 
```
```sql
$body.Weather.Temperature = 50 AND $body.Weather.IsEnabled 
```
```sql
length($body.Weather.Location.State) = 2 
```
```sql
$body.Weather.Temperature = 50 AND processingPath = 'hot'
```

## <a name="message-routing-query-based-on-device-twin"></a>デバイス ツインに基づいたメッセージ ルーティング クエリ 

メッセージ ルーティングでは、JSON オブジェクトである[デバイス ツイン](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-device-twins)のタグとプロパティに基づいてクエリを実行できます。 モジュール ツインに基づいたクエリ実行はサポートされていないことに注意してください。 デバイス ツインのタグとプロパティのサンプルを以下に示します。

```JSON
{
    "tags": { 
        "deploymentLocation": { 
            "building": "43", 
            "floor": "1" 
        } 
    }, 
    "properties": { 
        "desired": { 
            "telemetryConfig": { 
                "sendFrequency": "5m" 
            }, 
            "$metadata" : {...}, 
            "$version": 1 
        }, 
        "reported": { 
            "telemetryConfig": { 
                "sendFrequency": "5m", 
                "status": "success" 
            },
            "batteryLevel": 55, 
            "$metadata" : {...}, 
            "$version": 4 
        } 
    } 
} 
```

### <a name="query-expressions"></a>クエリ式

メッセージ本文に基づくクエリには、前に `$twin` を付ける必要があります。 さらにクエリ式では、ツインのタグまたはプロパティの参照を、本文の参照、メッセージ システム プロパティの参照、およびメッセージ アプリケーション プロパティの参照と組み合わせることもできます。 クエリでは大文字と小文字が区別されないため、タグとプロパティには一意の名前を使用することをお勧めします。 また、`twin`、`$twin`、`body`、または `$body` をプロパティ名として使用することも避けてください。 たとえば、以下はすべて有効なクエリ式です。 

```sql
$twin.properties.desired.telemetryConfig.sendFrequency = '5m'
```
```sql
$body.Weather.Temperature = 50 AND $twin.properties.desired.telemetryConfig.sendFrequency = '5m'
```
```sql
$twin.tags.deploymentLocation.floor = 1 
```

## <a name="next-steps"></a>次の手順

* [メッセージ ルーティング](iot-hub-devguide-messages-d2c.md)について確認する。
* [メッセージ ルーティングのチュートリアル](tutorial-routing.md)を試す。