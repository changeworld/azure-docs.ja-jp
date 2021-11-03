---
title: IoT コネクタでのデバイス マッピング テンプレート - Azure Healthcare APIs
description: この記事では、 のデバイス マッピング テンプレートを使用する方法について説明IoT Connector。
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 10/26/2021
ms.author: jasteppe
ms.openlocfilehash: ef22404d96599768ad55c3c3687e3df9fb4bbf35
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131040473"
---
# <a name="how-to-use-device-mapping"></a>デバイス マッピングを使用する方法

> [!IMPORTANT]
> Azure Healthcare APIs は現在プレビュー段階です。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」に記載されています。

IoT コネクタには、2 種類の JSON ベースのマッピングが必要です。 1 種類目の **デバイス マッピング** は、`devicedata` Azure Event Hub エンドポイントに送信されるデバイス ペイロードのマッピングを担います。 型、デバイス識別子、測定日時、測定値を抽出します。 

2 番目の型 **である 高速ヘルスケア相互運用性リソース (FHIR&#174;) 変換** 先マッピングは、FHIR リソースのマッピングを制御します。 これにより、観察期間の長さ、値の格納に使用される FHIR データ型、用語コードを構成できます。 

2 種類のマッピングは、その型に基づいて JSON ドキュメントに構成されます。 次に、これらの JSON ドキュメントは、IoT コネクタに追加されます。このドキュメントは、Azure portal。 [デバイス マッピング] ドキュメントは、[デバイス **マッピング]** ページと [宛先] ページの FHIR 変換先マッピング ドキュメントを通 **じて追加** されます。

> [!NOTE]
> マッピングは基になる BLOB ストレージに格納され、コンピューティングの実行ごとに BLOB から読み込まれます。 更新された場合は、すぐに有効になります。 

> [!TIP]
> IoT コネクタの Device と FHIR の変換先マッピングの編集、テスト、トラブルシューティングを行う [IoMT](https://github.com/microsoft/iomt-fhir/tree/master/tools/data-mapper) コネクタ データ マッパー ツールを確認してください。 Azure portal で IoT コネクタにアップロードしたり、オープン ソース バージョンの IoT コネクタで使用したりするために [マッピングをエクスポート](https://github.com/microsoft/iomt-fhir) します。

## <a name="device-mapping"></a>デバイス マッピング

デバイス マッピングには、さらなる評価のためにデバイス コンテンツを共通の形式に抽出するマッピング機能が用意されています。 受信した各メッセージが、すべてのテンプレートに対して評価されます。 この方法によって、1 つの受信メッセージを複数の送信メッセージに投影し、後で FHIR の異なる観察にマップすることができます。 結果は、テンプレートによって解析された 1 つまたは複数の値を表す、正規化されたデータ オブジェクトです。 正規化されたデータ モデルには、検出して抽出する必要がある、いくつかの必須プロパティがあります。

| プロパティ             | 説明                                                                                                                                                                                                                                                   |
|----------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Type**             | 測定を分類するための名前または種類。 この値は、必要な FHIR 変換先マッピングにバインドするために使用されます。  複数のマッピングを同じ種類に出力することで、複数のデバイス間で異なる表現を 1 つの一般的な出力にマップできます。 |
| **OccurenceTimeUtc** | 測定が発生した時刻。                                                                                                                                                                                                                            |
| **DeviceId**         | デバイスの識別子。 この値は、宛先 FHIR サービスに存在するデバイス リソースの識別子と一致する必要があります。                                                                                                                       |
| **Properties**       | 作成された Observation リソースに値を保存できるように、少なくとも 1 つのプロパティを抽出します。  プロパティは、正規化中に抽出されたキーと値のペアのコレクションです。                                                                                  |

次に、正規化時の動作の概念を表す例を示します。

![正規化の例](media/concepts-iot-mapping-templates/normalization-example.png#lightbox)

コンテンツ ペイロード自体は、次の 3 つの部分で構成される Azure Event Hub メッセージです: Body、Properties、SystemProperties。 `Body` は、UTF-8 でエンコードされた文字列を表すバイト配列です。 テンプレートの評価中に、バイト配列は自動的に文字列値に変換されます。 `Properties` は、メッセージの作成者が使用するキー値のコレクションです。 `SystemProperties` も、Azure Event Hub フレームワークによって予約され、エントリが自動的に入力されるキー値コレクションです。

```json
{
    "Body": {
        "content": "value"
    },
    "Properties": {
        "key1": "value1",
        "key2": "value2"
    },
    "SystemProperties": {
        "x-opt-sequence-number": 1,
        "x-opt-enqueued-time": "2021-02-01T22:46:01.8750000Z",
        "x-opt-offset": 1,
        "x-opt-partition-key": "1"
    }
}
```
### <a name="mapping-with-json-path"></a>JSON パスを使用したマッピング

現在サポートされている 3 つのデバイス コンテンツ マッピングの種類は、必要なマッピングと抽出された値の両方に一致する JSON パスに依存しています。 JSON パスの詳細については、[こちら](https://goessner.net/articles/JsonPath/)を参照してください。 3 種類のテンプレートすべてで、JSON パス式を解決するために、[JSON .NET 実装](https://www.newtonsoft.com/json/help/html/QueryJsonSelectTokenJsonPath.htm)が使用されます。

#### <a name="jsonpathcontenttemplate"></a>JsonPathContentTemplate

JsonPathContentTemplate では、JSON パスを使用して、イベント ハブ メッセージの値の照合と抽出を行うことができます。

| プロパティ | 説明 |例 |
| --- | --- | --- |
|**TypeName**|テンプレートに一致する測定値に関連付ける型。|`heartrate`
|**TypeMatchExpression**|イベント ハブのペイロードに対して評価される JSON パス式。 一致する JToken が見つかった場合、テンプレートは一致と見なされます。 後続のすべての式は、ここで一致した抽出済みの JToken に対して評価されます。|`$..[?(@heartRate)]`
|**TimestampExpression**|測定の OccurenceTimeUtc のタイムスタンプ値を抽出する JSON パス式。|`$.endDate`
|**DeviceIdExpression**|デバイス識別子を抽出する JSON パス式。|`$.deviceId`
|**PatientIdExpression**|*省略可能*:患者識別子を抽出する JSON パス式。|`$.patientId`
|**EncounterIdExpression**|*省略可能*:面接識別子を抽出する JSON パス式。|`$.encounterId`
|**Values[].ValueName**|後続の式によって抽出された値に関連付ける名前。 FHIR 変換先マッピングで必要な値/コンポーネントをバインドするために使用されます。 |`hr`
|**Values[].ValueExpression**|必要な値を抽出する JSON パス式。|`$.heartRate`
|**Values[].Required**|ペイロード内に値が存在する必要があります。  見つからない場合は、測定値が生成されず、InvalidOperationException がスローされます。|`true`

##### <a name="examples"></a>例

**心拍数**

*メッセージ*

```json
{
    "Body": {
        "heartRate": "78",
        "endDate": "2021-02-01T22:46:01.8750000Z",
        "deviceId": "device123"
    },
    "Properties": {},
    "SystemProperties": {}
}
```
*テンプレート*
```json
{
    "templateType": "JsonPathContent",
    "template": {
        "typeName": "heartrate",
        "typeMatchExpression": "$..[?(@heartRate)]",
        "deviceIdExpression": "$.deviceId",
        "timestampExpression": "$.endDate",
        "values": [
            {
                "required": "true",
                "valueExpression": "$.heartRate",
                "valueName": "hr"
            }
        ]
    }
}
```
**血圧**

*メッセージ*

```json
{
    "Body": {
        "systolic": "123",
        "diastolic" : "87",
        "endDate": "2021-02-01T22:46:01.8750000Z",
        "deviceId": "device123"
    },
    "Properties": {},
    "SystemProperties": {}
}
```
*テンプレート*

```json
{
    "typeName": "bloodpressure",
    "typeMatchExpression": "$..[?(@systolic && @diastolic)]",
    "deviceIdExpression": "$.deviceId",
    "timestampExpression": "$.endDate",
    "values": [
        {
            "required": "true",
            "valueExpression": "$.systolic",
            "valueName": "systolic"
        },
        {
            "required": "true",
            "valueExpression": "$.diastolic",
            "valueName": "diastolic"
        }
    ]
}
```
**1 つのメッセージから複数の測定値を投影する**

*メッセージ*

```json
{
    "Body": {
        "heartRate": "78",
        "steps": "2",
        "endDate": "2021-02-01T22:46:01.8750000Z",
        "deviceId": "device123"
    },
    "Properties": {},
    "SystemProperties": {}
}
```
*テンプレート 1*

```json
{
    "templateType": "JsonPathContent",
    "template": {
        "typeName": "heartrate",
        "typeMatchExpression": "$..[?(@heartRate)]",
        "deviceIdExpression": "$.deviceId",
        "timestampExpression": "$.endDate",
        "values": [
            {
                "required": "true",
                "valueExpression": "$.heartRate",
                "valueName": "hr"
            }
        ]
    }
}
```

*テンプレート 2*

```json
{
    "templateType": "JsonPathContent",
    "template": {
        "typeName": "stepcount",
        "typeMatchExpression": "$..[?(@steps)]",
        "deviceIdExpression": "$.deviceId",
        "timestampExpression": "$.endDate",
        "values": [
            {
                "required": "true",
                "valueExpression": "$.steps",
                "valueName": "steps"
            }
        ]
    }
}
```
**メッセージ内の配列から複数の測定値を投影する**

*メッセージ*

```json
{
    "Body": [
        {
            "heartRate": "78",
            "endDate": "2021-02-01T22:46:01.8750000Z",
            "deviceId": "device123"
        },
        {
            "heartRate": "81",
            "endDate": "2021-02-01T23:46:01.8750000Z",
            "deviceId": "device123"
        },
        {
            "heartRate": "72",
            "endDate": "2021-02-01T24:46:01.8750000Z",
            "deviceId": "device123"
        }
    ],
    "Properties": {},
    "SystemProperties": {}
}
```
*テンプレート*

```json
{
    "templateType": "JsonPathContent",
    "template": {
        "typeName": "heartrate",
        "typeMatchExpression": "$..[?(@heartRate)]",
        "deviceIdExpression": "$.deviceId",
        "timestampExpression": "$.endDate",
        "values": [
            {
                "required": "true",
                "valueExpression": "$.heartRate",
                "valueName": "hr"
            }
        ]
    }
}
```
#### <a name="iotjsonpathcontenttemplate"></a>IotJsonPathContentTemplate

IotJsonPathContentTemplate は JsonPathContentTemplate に似ていますが、DeviceIdExpression と TimestampExpression は必要ありません。

このテンプレートを使用する場合、評価対象メッセージが [Azure IoT Hub デバイス SDK](../../iot-hub/iot-hub-devguide-sdks.md#azure-iot-hub-device-sdks) または [Azure IoT Central](../../iot-central/core/overview-iot-central.md) の[データのエクスポート (レガシ)](../../iot-central/core/howto-export-data-legacy.md) 機能を使用して送信されたことが前提になります。 これらの SDK を使用する場合、デバイス ID (Azure Iot Hub/Central のデバイス識別子が宛先 FHIR サービス上のデバイス リソースの識別子として登録されている場合) と、メッセージのタイムスタンプが確認されます。 Azure IoT Hub デバイス SDK を使用しているが、メッセージ本文でデバイス ID または測定タイムスタンプにカスタム プロパティを使用している場合でも、JsonPathContentTemplate を使用できます。

> [!NOTE]
> `IotJsonPathContentTemplate` を使用するときは、`TypeMatchExpression` が JToken としてメッセージ全体に解決される必要があります。 詳細については、次の例を参照してください。

##### <a name="examples"></a>例

**心拍数**

*メッセージ*

```json
{
    "Body": {
        "heartRate": "78"        
    },
    "Properties": {
        "iothub-creation-time-utc" : "2021-02-01T22:46:01.8750000Z"
    },
    "SystemProperties": {
        "iothub-connection-device-id" : "device123"
    }
}
```
*テンプレート*

```json

    "templateType": "JsonPathContent",
    "template": {
        "typeName": "heartrate",
        "typeMatchExpression": "$..[?(@Body.heartRate)]",
        "deviceIdExpression": "$.deviceId",
        "timestampExpression": "$.endDate",
        "values": [
            {
                "required": "true",
                "valueExpression": "$.Body.heartRate",
                "valueName": "hr"
            }
        ]
    }
}
```

**血圧**

*メッセージ*

```json
{
    "Body": {
        "systolic": "123",
        "diastolic" : "87"
    },
    "Properties": {
        "iothub-creation-time-utc" : "2021-02-01T22:46:01.8750000Z"
    },
    "SystemProperties": {
        "iothub-connection-device-id" : "device123"
    }
}
```
*テンプレート*

```json
{
    "typeName": "bloodpressure",
    "typeMatchExpression": "$..[?(@Body.systolic && @Body.diastolic)]",
    "values": [
        {
            "required": "true",
            "valueExpression": "$.Body.systolic",
            "valueName": "systolic"
        },
        {
            "required": "true",
            "valueExpression": "$.Body.diastolic",
            "valueName": "diastolic"
        }
    ]
}
```
#### <a name="iotcentraljsonpathcontenttemplate"></a>IotCentralJsonPathContentTemplate

IotCentralJsonPathContentTemplate でも、DeviceIdExpression と TimestampExpression は必要ありません。 これは、評価対象メッセージが [Azure IoT Central](../../iot-central/core/overview-iot-central.md) の[データのエクスポート](../../iot-central/core/howto-export-data.md)機能を使用して送信されるときに使用されます。 この機能を使用する場合、デバイス ID (Azure Iot Central からのデバイス識別子が宛先 FHIR サーバー上のデバイス リソースの識別子として登録されていることを前提としています) とメッセージのタイムスタンプは既知です。 Azure IoT Central のデータ エクスポート機能を使用しているが、メッセージ本文でデバイス ID または測定タイムスタンプにカスタム プロパティを使用している場合でも、JsonPathContentTemplate を使用できます。

> [!NOTE]
> IotCentralJsonPathContentTemplate を使用する場合は、TypeMatchExpression でメッセージ全体を JToken として解決する必要があります。 詳細については、次の例を参照してください。
 
##### <a name="examples"></a>例

**心拍数**

*メッセージ*

```json
{
    "applicationId": "1dffa667-9bee-4f16-b243-25ad4151475e",
    "messageSource": "telemetry",
    "deviceId": "1vzb5ghlsg1",
    "schema": "default@v1",
    "templateId": "urn:qugj6vbw5:___qbj_27r",
    "enqueuedTime": "2021-08-05T22:26:55.455Z",
    "telemetry": {
        "HeartRate": "88",
    },
    "enrichments": {
      "userSpecifiedKey": "sampleValue"
    },
    "messageProperties": {
      "messageProp": "value"
    }
}
```
*テンプレート*

```json
{
    "templateType": "IotCentralJsonPathContent",
    "template": {
        "typeName": "heartrate",
        "typeMatchExpression": "$..[?(@telemetry.HeartRate)]",
        "values": [
            {
                "required": "true",
                "valueExpression": "$.telemetry.HeartRate",
                "valueName": "hr"
            }
        ]
    }
}
```

**血圧**

*メッセージ*

```json
{
    "applicationId": "1dffa667-9bee-4f16-b243-25ad4151475e",
    "messageSource": "telemetry",
    "deviceId": "1vzb5ghlsg1",
    "schema": "default@v1",
    "templateId": "urn:qugj6vbw5:___qbj_27r",
    "enqueuedTime": "2021-08-05T22:26:55.455Z",
    "telemetry": {
        "BloodPressure": {
            "Diastolic": "87",
            "Systolic": "123"
        }
    },
    "enrichments": {
      "userSpecifiedKey": "sampleValue"
    },
    "messageProperties": {
      "messageProp": "value"
    }
}
```
*テンプレート*

```json
{
    "templateType": "IotCentralJsonPathContent",
    "template": {
        "typeName": "bloodPressure",
        "typeMatchExpression": "$..[?(@telemetry.BloodPressure.Diastolic && @telemetry.BloodPressure.Systolic)]",
        "values": [
            {
                "required": "true",
                "valueExpression": "$.telemetry.BloodPressure.Diastolic",
                "valueName": "bp_diastolic"
            },
            {
                "required": "true",
                "valueExpression": "$.telemetry.BloodPressure.Systolic",
                "valueName": "bp_systolic"
            }
        ]
    }
}
```

## <a name="next-steps"></a>次のステップ

>[!div class="nextstepaction"]
>[FHIR 変換先マッピングの使用方法](how-to-use-fhir-mapping-iot.md)

(FHIR&#174;) [HL7](https://hl7.org/fhir/) の登録商標であり、HL7 のアクセス許可と共に使用されます。