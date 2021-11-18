---
title: デバイス マッピングの CalculatedContentTemplate IoT Connectorのマッピング - Azure Healthcare API
description: この記事では、IoT コネクタのデバイス マッピング テンプレートで CalculatedContentTemplate マッピングを使用する方法について説明します。
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: how-to
ms.date: 11/16/2021
ms.author: jasteppe
ms.openlocfilehash: 3e1ccf989bcd67e5e45d381ae681287730cad1d2
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132733501"
---
# <a name="how-to-use-calculatedcontenttemplate-mappings"></a>CalculatedContentTemplate マッピングを使用する方法

> [!IMPORTANT]
> Azure Healthcare APIs は現在プレビュー段階です。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」に記載されています。

> [!TIP]
> IoT コネクタの Device と FHIR の変換先マッピングの編集、テスト、トラブルシューティングを行う [IoMT](https://github.com/microsoft/iomt-fhir/tree/master/tools/data-mapper) コネクタ データ マッパー ツールを確認してください。 Azure portal で IoT コネクタにアップロードしたり、オープン ソース バージョンの IoT コネクタで使用したりするために [マッピングをエクスポート](https://github.com/microsoft/iomt-fhir) します。

この記事では、IoT コネクタのデバイス マッピング テンプレートで CalculatedContentTemplate マッピングを使用する方法について説明します。

## <a name="calculatedcontenttemplate"></a>CalculatedContentTemplate

IoT コネクタには、必要なテンプレートと抽出値の両方に一致する式ベースのコンテンツ テンプレートが用意されています。 **式** は、JSONPath または JmesPath で使用できます。 テンプレート内の各式は、独自の式言語を選択できます。 

> [!NOTE]
> 式言語が定義されていない場合は、テンプレート用に構成された既定の式言語が使用されます。 既定値は JSONPath ですが、必要に応じて上書きできます。

式は次のように定義されます。

```json
<name of expression> : {
        "value" : <the expression>,
        "language": <the expression language>
    }
```

次の例では *、typeMatchExpression は次* のように定義されています。

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
> デバイス マッピング テンプレートに使用する既定の式言語は JsonPath です。 JsonPath を使用する場合は、式を単独で指定できます。

```json
"templateType": "CalculatedContent",
    "template": {
        "typeName": "heartrate",
        "typeMatchExpression": "$..[?(@heartRate)]",
        ...
    }
```

テンプレートに使用する既定の式言語は、 パラメーターを使用して明示的に設定 `defaultExpressionLanguage` できます。

```json
"templateType": "CalculatedContent",
    "template": {
        "typeName": "heartrate",
        "defaultExpressionLanguage": "JsonPath",
        "typeMatchExpression": "$..[?(@heartRate)]",
        ...
    }
```

CalculatedContentTemplate では、次に定義されている式を使用して、Azure Event  Hub メッセージの値を照合および抽出できます。

|プロパティ|説明|例|
|--------|-----------|-------|
|TypeName|テンプレートに一致する測定値に関連付ける型|`heartrate`|
|TypeMatchExpression|EventData ペイロードに対して評価される式。 一致する JToken が見つかった場合、テンプレートは一致と見なされます。 以降のすべての式は、ここで一致した抽出された JToken に対して評価されます。|`$..[?(@heartRate)]`|
|TimestampExpression|測定の OccurrenceTimeUtc のタイムスタンプ値を抽出する式。|`$.matchedToken.endDate`|
|DeviceIdExpression|デバイス識別子を抽出する式。|`$.matchedToken.deviceId`|
|PatientIdExpression|*IdentityResolution* が作成モードの場合は必須、IdentityResolution が参照モードの場合は **省略** 可能。 患者識別子を抽出する式。|`$.matchedToken.patientId`|
|EncounterIdExpression|*省略* 可能: 検出識別子を抽出する式。|`$.matchedToken.encounterId`|
|CorrelationIdExpression|*省略* 可能: 関連付け識別子を抽出する式。 この出力は、FHIR 変換先マッピングの 1 つの観測値に値をグループ化するために使用できます。|`$.matchedToken.correlationId`|
|Values[].ValueName|次の式によって抽出された値に関連付ける名前。 FHIR 変換先マッピング テンプレートで必要な値/コンポーネントをバインドするために使用されます。|`hr`|
|Values[].ValueExpression|必要な値を抽出する式。|`$.matchedToken.heartRate`|
|Values[].Required|ペイロード内に値が存在する必要があります。 見つからない場合、測定値は生成されません。InvalidOperationException が作成されます。|`true`|

### <a name="expression-languages"></a>式言語

式に使用する言語を指定する場合、次の値が有効です。

| 式言語 | 値        |
|---------------------|--------------|
| JSONPath            | **JsonPath** |
| JmesPath            | **JmesPath** |

>[!TIP]
>JSONPath の詳細については、「JSONPath」 [を参照してください](https://goessner.net/articles/JsonPath/)。 [CalculatedContentTemplate](#calculatedcontenttemplate)では[、JSONPath](https://www.newtonsoft.com/json/help/html/QueryJsonSelectTokenJsonPath.htm)式を解決するために JSON .NET 実装が使用されます。
>
>JmesPath の詳細については、「JmesPath [」を参照してください](https://jmespath.org/specification.html)。 [CalculatedContentTemplate では](#calculatedcontenttemplate)[、JmesPath 式を解決するために JmesPath .NET](https://github.com/jdevillard/JmesPath.Net)実装が使用されます。

### <a name="custom-functions"></a>カスタム関数

IoT コネクタのカスタム関数のセットも使用できます。 これらのカスタム関数は、JmesPath 仕様の一部として提供される関数の外部です。 カスタム関数の詳細については [、「IoT コネクタのカスタム関数」を参照してください](./how-to-use-custom-functions.md)。

### <a name="matched-token"></a>一致したトークン

**TypeMatchExpression は**、受信 EventData ペイロードに対して評価されます。 一致する JToken が見つかった場合、テンプレートは一致と見なされます。 

以降のすべての式は、新しい JToken に対して評価されます。 この新しい JToken には、元の EventData ペイロードと、ここで一致した抽出された JToken の両方が含まれています。 

この方法では、元のペイロードと一致するオブジェクトを、後の式ごとに使用できます。 抽出された JToken は、 プロパティ **matchedToken として使用できます**。

次の例のメッセージを指定します。

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

上記の式を使用して 2 つの一致が抽出され、JTokens の作成に使用されます。 以降の式は、次の JTokens を使用して評価されます。

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

**[Heart Rate]を選択します。**

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

**血圧**

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

**Projectメッセージからの複数の測定値の作成**

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

**Project配列から複数の測定値を取得する**

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

**Projectトークンと元のイベントからのデータの収集**

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

**受信データを選択して変換する**

次の例では、高さのデータはインチまたはメートルで到着します。 すべての正規化された高さのデータをメートルで指定する必要があります。 この結果を実現するために、高さのデータのみをインチ単位でターゲットにし、それをメートルに変換するテンプレートを作成します。 別のテンプレートでは、高さのデータをメートルでターゲットにし、単に同じ方法で格納します。

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

## <a name="next-steps"></a>次の手順

この記事では、デバイス マッピングを使用する方法について学習しました。 FHIR 変換先マッピングを使用する方法については、次を参照してください。

>[!div class="nextstepaction"]
>[FHIR 変換先マッピングを使用する方法](how-to-use-fhir-mappings.md)

(FHIR&#174;) は HL7 の商標であり [、HL7](https://hl7.org/fhir/) の許可を得て使用されます。
