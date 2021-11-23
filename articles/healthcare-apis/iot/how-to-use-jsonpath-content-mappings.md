---
title: IoT コネクタのデバイスマッピングの JsonPathContentTemplate マッピング-Azure の医療 Api
description: この記事では、IoT コネクタのデバイスマッピングテンプレートで JsonPathContentTemplate マッピングを使用する方法について説明します。
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: how-to
ms.date: 11/22/2021
ms.author: jasteppe
ms.openlocfilehash: 9c8ce02be3301bdfaf8d151ef84e1475edaa6b03
ms.sourcegitcommit: 3d04177023a3136832adb561da831ccc8e9910c7
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/23/2021
ms.locfileid: "132937262"
---
# <a name="how-to-use-jsonpathcontenttemplate-mappings"></a>JsonPathContentTemplate マッピングの使用方法

> [!IMPORTANT]
> Azure Healthcare APIs は現在プレビュー段階です。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」に記載されています。

> [!TIP]
> IoT コネクタデバイスと FHIR の宛先マッピングの編集、テスト、トラブルシューティングについては、 [IoMT コネクタデータマッパー](https://github.com/microsoft/iomt-fhir/tree/master/tools/data-mapper) ツールをご覧ください。 Azure portal で IoT コネクタにアップロードするか、 [オープンソースバージョン](https://github.com/microsoft/iomt-fhir) の iot コネクタで使用するためのマッピングをエクスポートします。

この記事では、IoT コネクタのデバイスマッピングテンプレートで JsonPathContentTemplate マッピングを使用する方法について説明します。

## <a name="jsonpathcontenttemplate"></a>JsonPathContentTemplate

JsonPathContentTemplate を使用すると、JSONPath を使用して Azure イベントハブメッセージの値の照合と抽出を行うことができます。

|プロパティ|[説明]|例|
|--------|-----------|-------|
|TypeName|テンプレートに一致する測定値に関連付ける型|`heartrate`|
|TypeMatchExpression|EventData ペイロードに対して評価される JSONPath 式。 一致する JToken が見つかった場合、テンプレートは一致と見なされます。 それ以降のすべての式は、ここで一致した抽出済みの JToken に対して評価されます。|`$..[?(@heartRate)]`|
|TimestampExpression|測定の OccurrenceTimeUtc のタイムスタンプ値を抽出する JSONPath 式。|`$.matchedToken.endDate`|
|DeviceIdExpression|デバイス id を抽出する JSONPath 式。|`$.matchedToken.deviceId`|
|PatientIdExpression|指定された場合に指定する *必要* があります、**作成** モードで、指定する場合は *オプション*、**参照** モードでの場合。 患者の識別子を抽出する式。|`$.matchedToken.patientId`|
|EncounterIdExpression|*省略可能*: 検出された識別子を抽出する式。|`$.matchedToken.encounterId`|
|Correlationide の場合|*省略可能*: 相関 id を抽出する式。 この出力を使用して、FHIR 変換先マッピングの1つの監視に値をグループ化できます。|`$.matchedToken.correlationId`|
|Values[].ValueName|次の式によって抽出された値に関連付ける名前。 FHIR 変換先マッピングテンプレートで必要な値またはコンポーネントをバインドするために使用されます。|`hr`|
|Values[].ValueExpression|必要な値を抽出する JSONPath 式。|`$.matchedToken.heartRate`|
|Values[].Required|ペイロード内に値が存在する必要があります。 見つからない場合は、測定値が生成されず、InvalidOperationException が作成されます。|`true`|

### <a name="examples"></a>例

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

> [!TIP]
> 一般的なエラーと問題の解決については、「IoT コネクタの [トラブルシューティングガイド](./iot-troubleshoot-guide.md) 」を参照してください。

## <a name="next-steps"></a>次のステップ

この記事では、デバイスマッピングの使用方法について説明しました。 FHIR 変換先マッピングの使用方法については、「」を参照してください。

>[!div class="nextstepaction"]
>[FHIR 変換先マッピングの使用方法](how-to-use-fhir-mappings.md)

(FHIR&#174;) [HL7](https://hl7.org/fhir/) の登録商標であり、HL7 のアクセス許可と共に使用されます。
