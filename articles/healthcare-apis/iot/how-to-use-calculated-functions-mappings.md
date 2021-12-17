---
title: IoT Connector デバイスマッピングでの CalculatedContentTemplate マッピング-Azure の医療 api
description: この記事では、IoT コネクタのデバイスマッピングテンプレートで CalculatedContentTemplate マッピングを使用する方法について説明します。
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: how-to
ms.date: 11/22/2021
ms.author: jasteppe
ms.openlocfilehash: 6e92acd7cc537b9723b1b114b11d8408cf941443
ms.sourcegitcommit: 3d04177023a3136832adb561da831ccc8e9910c7
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/23/2021
ms.locfileid: "132940987"
---
# <a name="how-to-use-calculatedcontenttemplate-mappings"></a>CalculatedContentTemplate マッピングの使用方法

> [!IMPORTANT]
> Azure Healthcare APIs は現在プレビュー段階です。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」に記載されています。

> [!TIP]
> IoT コネクタデバイスと FHIR の宛先マッピングの編集、テスト、トラブルシューティングについては、 [IoMT コネクタデータマッパー](https://github.com/microsoft/iomt-fhir/tree/master/tools/data-mapper) ツールをご覧ください。 Azure portal で IoT コネクタにアップロードするか、 [オープンソースバージョン](https://github.com/microsoft/iomt-fhir) の iot コネクタで使用するためのマッピングをエクスポートします。

この記事では、IoT コネクタのデバイスマッピングテンプレートで CalculatedContentTemplate マッピングを使用する方法について説明します。

## <a name="calculatedcontenttemplate"></a>CalculatedContentTemplate

IoT コネクタでは、必要なテンプレートと値を抽出するために、式ベースのコンテンツテンプレートが提供されます。 **式** は、jsonpath または Jのパスのいずれかで使用できます。 テンプレート内の各式は、独自の式言語を選択できます。 

> [!NOTE]
> 式言語が定義されていない場合は、テンプレート用に構成された既定の式言語が使用されます。 既定値は JSONPath ですが、必要に応じて上書きできます。

式は次のように定義されます。

```json
<name of expression> : {
        "value" : <the expression>,
        "language": <the expression language>
    }
```

次の例では、 *typeMatchExpression* は次のように定義されています。

```json
"templateType": "CalculatedContent",
    "template": {
        "typeName": "heartrate",
        "typeMatchExpression": {
            "value" : "$..[?(@heartRate)]",
            "language": "JsonPath"
        },
        ...
    }
```
> [!TIP]
> デバイスマッピングテンプレートに使用する既定の式言語は、JsonPath です。 JsonPath を使用する場合は、式だけを指定できます。

```json
"templateType": "CalculatedContent",
    "template": {
        "typeName": "heartrate",
        "typeMatchExpression": "$..[?(@heartRate)]",
        ...
    }
```

テンプレートに使用する既定の式言語は、パラメーターを使用して明示的に設定でき `defaultExpressionLanguage` ます。

```json
"templateType": "CalculatedContent",
    "template": {
        "typeName": "heartrate",
        "defaultExpressionLanguage": "JsonPath",
        "typeMatchExpression": "$..[?(@heartRate)]",
        ...
    }
```

CalculatedContentTemplate では、以下に定義されている **式** を使用して、Azure イベントハブメッセージの値の照合と抽出を行うことができます。

|プロパティ|[説明]|例|
|--------|-----------|-------|
|TypeName|テンプレートに一致する測定値に関連付ける型|`heartrate`|
|TypeMatchExpression|EventData ペイロードに対して評価される式。 一致する JToken が見つかった場合、テンプレートは一致と見なされます。 それ以降のすべての式は、ここで一致した抽出済みの JToken に対して評価されます。|`$..[?(@heartRate)]`|
|TimestampExpression|測定の OccurrenceTimeUtc のタイムスタンプ値を抽出する式。|`$.matchedToken.endDate`|
|DeviceIdExpression|デバイス id を抽出する式。|`$.matchedToken.deviceId`|
|PatientIdExpression|指定された場合に指定する *必要* があります、**作成** モードで、指定する場合は *オプション*、**参照** モードでの場合。 患者の識別子を抽出する式。|`$.matchedToken.patientId`|
|EncounterIdExpression|*省略可能*: 検出された識別子を抽出する式。|`$.matchedToken.encounterId`|
|Correlationide の場合|*省略可能*: 相関 id を抽出する式。 この出力を使用して、FHIR 変換先マッピングの1つの監視に値をグループ化できます。|`$.matchedToken.correlationId`|
|Values[].ValueName|次の式によって抽出された値に関連付ける名前。 FHIR 変換先マッピングテンプレートで必要な値またはコンポーネントをバインドするために使用されます。|`hr`|
|Values[].ValueExpression|目的の値を抽出する式。|`$.matchedToken.heartRate`|
|Values[].Required|ペイロード内に値が存在する必要があります。 見つからない場合は、測定値が生成されず、InvalidOperationException が作成されます。|`true`|

### <a name="expression-languages"></a>式言語

式に使用する言語を指定すると、次の値が有効になります。

| 式言語 | 値        |
|---------------------|--------------|
| JSONPath            | **JsonPath** |
| JmesPath            | **JmesPath** |

>[!TIP]
>JSONPath の詳細については、「 [jsonpath](https://goessner.net/articles/JsonPath/)」を参照してください。 [CalculatedContentTemplate](#calculatedcontenttemplate)は、jsonpath 式を解決するために[JSON .net 実装](https://www.newtonsoft.com/json/help/html/QueryJsonSelectTokenJsonPath.htm)を使用します。
>
>Jのパスの詳細については、 [Jのパス](https://jmespath.org/specification.html)を参照してください。 [CalculatedContentTemplate は j](#calculatedcontenttemplate) [path .net 実装](https://github.com/jdevillard/JmesPath.Net)を使用して j path 式を解決します。

### <a name="custom-functions"></a>カスタム関数

一連の IoT コネクタカスタム関数も使用できます。 これらのカスタム関数は、Jのパスの仕様の一部として提供されている関数の外部にあります。 カスタム関数の詳細については、「 [IoT コネクタのカスタム関数](./how-to-use-custom-functions.md)」を参照してください。

### <a name="matched-token"></a>一致するトークン

**TypeMatchExpression** は、受信 EventData ペイロードに対して評価されます。 一致する JToken が見つかった場合、テンプレートは一致と見なされます。 

それ以降のすべての式は、新しい JToken に対して評価されます。 この新しい JToken には、元の EventData ペイロードと、ここで見つかった抽出した JToken の両方が含まれています。 

これにより、元のペイロードと一致したオブジェクトが、後の各式で使用できるようになります。 抽出された JToken は、プロパティ **Matchedtoken** として使用できます。

このメッセージの例を次に示します。

*メッセージ*

```json
{
  "Body": {
    "deviceId": "device123",
    "data": [
      {
        "systolic": "120", // Match
        "diastolic": "80", // Match 
        "date": "2021-07-13T17:29:01.061144Z"
      },
      {
        "systolic": "122", // Match
        "diastolic": "82", // Match
        "date": "2021-07-13T17:28:01.061122Z"
      }
    ]
  },
  "Properties": {},
  "SystemProperties": {}
}
```

*テンプレート*

```json
{
  "templateType": "CollectionContent",
  "template": [
    {
      "templateType": "CalculatedContent",
      "template": {
        "typeName": "heartrate",
        "typeMatchExpression": "$..[?(@systolic && @diastolic)]", // Expression
        "deviceIdExpression": "$.Body.deviceId", // This accesses the attribute 'deviceId' which belongs to the original event data
        "timestampExpression": "$.matchedToken.date", 
        "values": [
          {
            "required": "true",
            "valueExpression": "$.matchedToken.systolic",
            "valueName": "systolic"
          },
          {
            "required": "true",
            "valueExpression": "$.matchedToken.diastolic",
            "valueName": "diastolic"
          }
        ]
      }
    }
  ]
}
```

上記の式を使用して2つの一致が抽出され、JTokens の作成に使用されます。 後の式は、次の JTokens を使用して評価されます。

```json
{
  "Body": {
    "deviceId": "device123",
    "data": [
      {
        "systolic": "120", 
        "diastolic": "80",
        "date": "2021-07-13T17:29:01.061144Z"
      },
      {
        "systolic": "122",
        "diastolic": "82",
        "date": "2021-07-13T17:28:01.061122Z"
      }
    ]
  },
  "Properties": {},
  "SystemProperties": {},
  "matchedToken" : {
      "systolic": "120",
      "diastolic": "80",
      "date": "2021-07-13T17:29:01.061144Z"
  }
}
```

および

```json
{
  "Body": {
    "deviceId": "device123",
    "data": [
      {
        "systolic": "120",
        "diastolic": "80",
        "date": "2021-07-13T17:29:01.061144Z"
      },
      {
        "systolic": "122", 
        "diastolic": "82", 
        "date": "2021-07-13T17:28:01.061122Z"
      }
    ]
  },
  "Properties": {},
  "SystemProperties": {},
  "matchedToken" : {
      "systolic": "122",
      "diastolic": "82",
      "date": "2021-07-13T17:28:01.061122Z"
    }
  }
}
```

### <a name="examples"></a>例

**ハートレート**

*メッセージ*

```json
{
  "Body": {
    "heartRate": "78",
    "endDate": "2019-02-01T22:46:01.8750000Z",
    "deviceId": "device123"
  },
  "Properties": {},
  "SystemProperties": {}
}
```

*テンプレート*

```json
    {
      "templateType": "CalculatedContent",
      "template": {
        "typeName": "heartrate",
        "typeMatchExpression": "$..[?(@heartRate)]",
        "deviceIdExpression": "$.matchedToken.deviceId",
        "timestampExpression": "$.matchedToken.endDate",
        "values": [
          {
            "required": "true",
            "valueExpression": "$.matchedToken.heartRate",
            "valueName": "hr"
          }
        ]
      }
    }
```

**血圧の圧力**

*メッセージ*

```json
{
    "Body": {
        "systolic": "123", // Match
        "diastolic" : "87", // Match
        "endDate": "2019-02-01T22:46:01.8750000Z",
        "deviceId": "device123"
    },
    "Properties": {},
    "SystemProperties": {}
}
```

*テンプレート*

```json
    {
      "templateType": "CalculatedContent",
      "template": {
        "typeName": "bloodpressure",
        "typeMatchExpression": "$..[?(@systolic && @diastolic)]", // Expression
        "deviceIdExpression": "$.matchedToken.deviceId",
        "timestampExpression": "$.matchedToken.endDate",
        "values": [
          {
            "required": "true",
            "valueExpression": "$.matchedToken.systolic",
            "valueName": "systolic"
          },
          {
            "required": "true",
            "valueExpression": "$.matchedToken.diastolic",
            "valueName": "diastolic"
          }
        ]
      }
    }
```

**1つのメッセージから複数の測定値を Project する**

*メッセージ*

```json
{
    "Body": {
        "heartRate": "78", // Match (Template 1)
        "steps": "2", // Match (Template 2)
        "endDate": "2019-02-01T22:46:01.8750000Z",
        "deviceId": "device123"
    },
    "Properties": {},
    "SystemProperties": {}
}
```

*テンプレート 1*

```json
    {
      "templateType": "CalculatedContent",
      "template": {
        "typeName": "heartrate",
        "typeMatchExpression": "$..[?(@heartRate)]", // Expression
        "deviceIdExpression": "$.matchedToken.deviceId",
        "timestampExpression": "$.matchedToken.endDate",
        "values": [
          {
            "required": "true",
            "valueExpression": "$.matchedToken.heartRate",
            "valueName": "hr"
          }
        ]
      }
    },
```

*テンプレート 2*

```json
    {
      "templateType": "CalculatedContent",
      "template": {
        "typeName": "stepcount",
        "typeMatchExpression": "$..[?(@steps)]", // Expression
        "deviceIdExpression": "$.matchedToken.deviceId",
        "timestampExpression": "$.matchedToken.endDate",
        "values": [
          {
            "required": "true",
            "valueExpression": "$.matchedToken.steps",
            "valueName": "steps"
          }
        ]
      }
    }
```

**メッセージ内の配列から複数の測定値を Project する**

*メッセージ*

```json
{
  "Body": [
    {
      "heartRate": "78", // Match
      "endDate": "2019-02-01T20:46:01.8750000Z",
      "deviceId": "device123"
    },
    {
      "heartRate": "81", // Match
      "endDate": "2019-02-01T21:46:01.8750000Z",
      "deviceId": "device123"
    },
    {
      "heartRate": "72", // Match
      "endDate": "2019-02-01T22:46:01.8750000Z",
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
      "templateType": "CalculatedContent",
      "template": {
        "typeName": "heartrate",
        "typeMatchExpression": "$..[?(@heartRate)]", // Expression
        "deviceIdExpression": "$.matchedToken.deviceId",
        "timestampExpression": "$.matchedToken.endDate",
        "values": [
          {
            "required": "true",
            "valueExpression": "$.matchedToken.heartRate",
            "valueName": "hr"
          }
        ]
      }
    }
```

**一致したトークンと元のイベントからデータを Project**

*メッセージ*

```json
{
  "Body": {
    "deviceId": "device123",
    "data": [
      {
        "systolic": "120", // Match
        "diastolic": "80", // Match 
        "date": "2021-07-13T17:29:01.061144Z"
      },
      {
        "systolic": "122", // Match
        "diastolic": "82", // Match
        "date": "2021-07-13T17:28:01.061122Z"
      }
    ]
  },
  "Properties": {},
  "SystemProperties": {}
}
```

*テンプレート*

```json
    {
      "templateType": "CalculatedContent",
      "template": {
        "typeName": "heartrate",
        "typeMatchExpression": "$..[?(@systolic && @diastolic)]", // Expression
        "deviceIdExpression": "$.Body.deviceId", // This accesses the attribute 'deviceId' which belongs to the original event data
        "timestampExpression": "$.matchedToken.date", 
        "values": [
          {
            "required": "true",
            "valueExpression": "$.matchedToken.systolic",
            "valueName": "systolic"
          },
          {
            "required": "true",
            "valueExpression": "$.matchedToken.diastolic",
            "valueName": "diastolic"
          }
        ]
      }
    }
```

**受信データの選択と変換**

次の例では、高さデータがインチまたはメーターで到着します。 すべての正規化された高さデータをメートル単位で計算します。 この結果を得るには、インチ単位の高さデータのみを対象として、メーターに変換するテンプレートを作成します。 別のテンプレートは、メートル単位の高さデータをターゲットにし、そのままそのまま格納します。

*メッセージ*

```json
{
  "Body": [
    {
      "height": "78",
      "unit": "inches", // Match (Template 1)
      "endDate": "2019-02-01T22:46:01.8750000Z",
      "deviceId": "device123"
    },
    {
      "height": "1.9304",
      "unit": "meters", // Match (Template 2)
      "endDate": "2019-02-01T23:46:01.8750000Z",
      "deviceId": "device123"
    }
  ],
  "Properties": {},
  "SystemProperties": {}
}
```

*テンプレート 1*

```json
    {
      "templateType": "CalculatedContent",
      "template": {
        "typeName": "heightInMeters",
        "typeMatchExpression": "$..[?(@unit == 'inches')]",
        "deviceIdExpression": "$.matchedToken.deviceId",
        "timestampExpression": "$.matchedToken.endDate",
        "values": [
          {
            "required": "true",
            "valueExpression": {
              "value": "multiply(to_number(matchedToken.height), `0.0254`)", // Convert inches to meters. Notice we utilize JmesPath as that gives us access to transformation functions
              "language": "JmesPath"
            },
            "valueName": "height"
          }
        ]
      }
    }
```

*テンプレート 2*

```json
    {
      "templateType": "CalculatedContent",
      "template": {
        "typeName": "heightInMeters",
        "typeMatchExpression": "$..[?(@unit == 'meters')]",
        "deviceIdExpression": "$.matchedToken.deviceId",
        "timestampExpression": "$.matchedToken.endDate",
        "values": [
          {
            "required": "true",
            "valueExpression": "$.matchedToken.height", // Simply extract the height as it is already in meters
            "valueName": "height"
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
