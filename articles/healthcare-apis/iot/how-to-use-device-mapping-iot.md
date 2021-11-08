---
title: IoT コネクタでのデバイス マッピング テンプレート - Azure Healthcare APIs
description: この記事では、デバイス マッピング テンプレートを使用する方法について説明IoT Connector。
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: how-to
ms.date: 11/05/2021
ms.author: jasteppe
ms.openlocfilehash: f11770a05d2429c87647b65a828f6477f1b1b8cb
ms.sourcegitcommit: 5af89a2a7b38b266cc3adc389d3a9606420215a9
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/08/2021
ms.locfileid: "131988193"
---
# <a name="how-to-use-device-mappings"></a>デバイス マッピングを使用する方法

> [!IMPORTANT]
> Azure Healthcare APIs は現在プレビュー段階です。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」に記載されています。

IoT コネクタには、2 種類の JSON ベースのマッピングが必要です。 1 種類目の **デバイス マッピング** は、`devicedata` Azure Event Hub エンドポイントに送信されるデバイス ペイロードのマッピングを担います。 型、デバイス識別子、測定日時、測定値を抽出します。 

2 番目の型 **である 高速ヘルスケア相互運用性リソース (FHIR&#174;) 変換** 先マッピングは、FHIR リソースのマッピングを制御します。 これにより、観察期間の長さ、値の格納に使用される FHIR データ型、用語コードを構成できます。 

2 種類のマッピングは、その型に基づいて JSON ドキュメントに構成されます。 次に、これらの JSON ドキュメントは、IoT コネクタに追加されます。このドキュメントは、Azure portal。 [デバイス マッピング] ドキュメントは、[デバイス **マッピング]** ページと [宛先] ページの FHIR 変換先マッピング ドキュメントを通 **じて追加** されます。

> [!NOTE]
> マッピングは基になる BLOB ストレージに格納され、コンピューティングの実行ごとに BLOB から読み込まれます。 更新された場合は、すぐに有効になります。 

> [!TIP]
> IoT コネクタの Device と FHIR の変換先マッピングの編集、テスト、トラブルシューティングを行う [IoMT](https://github.com/microsoft/iomt-fhir/tree/master/tools/data-mapper) コネクタ データ マッパー ツールを確認してください。 Azure portal で IoT コネクタにアップロードしたり、オープン ソース バージョンの IoT コネクタで使用したりするために [マッピングをエクスポート](https://github.com/microsoft/iomt-fhir) します。

## <a name="device-mappings-overview"></a>デバイス マッピングの概要

デバイス マッピングは、デバイス メッセージの内容を一般的な形式に抽出してさらに評価する機能を提供します。 受信した各デバイス メッセージは、すべてのデバイス マッピング テンプレートに対して評価されます。 

1 つの受信デバイス メッセージを複数の送信メッセージに分け、後で FHIR サービス内の異なる観測値にマップすることができます。 

結果は、テンプレートによって解析された 1 つまたは複数の値を表す、正規化されたデータ オブジェクトです。 

正規化されたデータ モデルには、検出して抽出する必要がある、いくつかの必須プロパティがあります。

|プロパティ|説明|
|--------|-----------|
|**Type**|測定を分類するための名前または種類。 この値は、必要な FHIR 変換先マッピングにバインドするために使用されます。 複数のマッピングを同じ種類に出力することで、複数のデバイス間で異なる表現を 1 つの一般的な出力にマップできます。|
|**OccurenceTimeUtc**|測定が発生した時刻。|
|**DeviceId**|デバイスの識別子。 この値は、宛先 FHIR サービスに存在するデバイス リソースの識別子と一致する必要があります。|
|**Properties**|作成された Observation リソースに値を保存できるように、少なくとも 1 つのプロパティを抽出します。 プロパティは、正規化中に抽出されたキーと値のペアのコレクションです。|

> [!IMPORTANT]
> 完全な正規化されたモデルは [、IMeasurement インターフェイスによって定義](https://github.com/microsoft/iomt-fhir/blob/master/src/lib/Microsoft.Health.Fhir.Ingest.Schema/IMeasurement.cs) されます。

IoT コネクタ内の正規化および変換プロセス中に行う処理の概念例を次に示します。

:::image type="content" source="media/iot-data-normalization-high-level.png" alt-text="IoT データ正規化フローの例1" lightbox="media/iot-data-normalization-high-level.png":::

:::image type="content" source="media/concepts-iot-mapping-templates/normalization-example.png" alt-text="IoT データ正規化フローの例2" lightbox="media/concepts-iot-mapping-templates/normalization-example.png":::

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
## <a name="mapping-with-jsonpath"></a>JSONPath を使用したマッピング

現在サポートされている 5 つのデバイス コンテンツ マッピングの種類は、必要なマッピングと抽出された値の両方に一致するために JSONPath に依存しています。 JSONPath の詳細については、こちらを参照 [してください](https://goessner.net/articles/JsonPath/)。 5 つのテンプレート型はすべて [、JSONPath 式を解決するために JSON .NET](https://www.newtonsoft.com/json/help/html/QueryJsonSelectTokenJsonPath.htm) 実装を使用します。

デバイス マッピング テンプレート内で 1 つ以上のテンプレートを定義できます。 受信した各 Event Hub デバイス メッセージは、すべてのデバイス マッピング テンプレートに対して評価されます。 

1 つの受信デバイス メッセージを複数の送信メッセージに分け、後で FHIR サービス内の異なる観測値にマップすることができます。 

さまざまなテンプレートの種類が存在し、デバイス マッピング ファイルを構築するときに使用できます。

|名前                                                                     | 説明                                                                   |  
|-------------------------------------------------------------------------|-------------------------------------------------------------------------------|
|[JsonPathContentTemplate](#jsonpathcontenttemplate)                      |JsonPath を使用した式の記述をサポートするテンプレート                  
|[CollectionContentTemplate](#collectioncontenttemplate)                  |正規化中に使用されるテンプレートの一覧を表すテンプレート。                                                            |                                                           
|[CalculatedContentTemplate](#calculatedcontenttemplate)                  |複数の式言語のいずれかを使用した式の記述をサポートするテンプレート。 JmesPath 関数を使用したデータ変換をサポートします。|
|[IotJsonPathContentTemplate](#iotjsonpathcontenttemplate)                |Azure Iot Hub または Azure Iot Central のレガシ エクスポート データ機能から送信されるメッセージをサポートするテンプレート。                                        |
|[IotCentralJsonPathContentTemplate](#iotcentraljsonpathcontenttemplate)  |Azure Iot Central のデータのエクスポート機能を使用して送信されるメッセージをサポートするテンプレート。|  

## <a name="jsonpathcontenttemplate"></a>JsonPathContentTemplate

JsonPathContentTemplate では、JSONPath を使用して Azure Event Hub メッセージの値を照合および抽出できます。

|プロパティ|説明|例|
|--------|-----------|-------|
|TypeName|テンプレートに一致する測定値に関連付ける型|`heartrate`|
|TypeMatchExpression|EventData ペイロードに対して評価される JSONPath 式。 一致する JToken が見つかった場合、テンプレートは一致と見なされます。 以降のすべての式は、ここで一致した抽出された JToken に対して評価されます。|`$..[?(@heartRate)]`|
|TimestampExpression|測定の OccurrenceTimeUtc のタイムスタンプ値を抽出する JSONPath 式。|`$.matchedToken.endDate`|
|DeviceIdExpression|デバイス識別子を抽出する JSONPath 式。|`$.matchedToken.deviceId`|
|PatientIdExpression|*IdentityResolution* が作成モードの場合は必須、IdentityResolution が参照モードの場合は **省略** 可能。 患者識別子を抽出する式。|`$.matchedToken.patientId`|
|EncounterIdExpression|*省略* 可能: 検出識別子を抽出する式。|`$.matchedToken.encounterId`|
|CorrelationIdExpression|*省略* 可能: 関連付け識別子を抽出する式。 この出力を使用すると、FHIR 変換先マッピングで値を 1 つの観測値にグループ化できます。|`$.matchedToken.correlationId`|
|Values[].ValueName|次の式によって抽出された値に関連付ける名前。 FHIR 変換先マッピング テンプレートで必要な値/コンポーネントをバインドするために使用されます。|`hr`|
|Values[].ValueExpression|必要な値を抽出する JSONPath 式。|`$.matchedToken.heartRate`|
|Values[].Required|ペイロード内に値が存在する必要があります。 見つからない場合、測定値は生成されません。InvalidOperationException が作成されます。|`true`|

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

## <a name="collectioncontenttemplate"></a>CollectionContentTemplate

CollectionContentTemplate は、正規化中に使用されるテンプレートの一覧を表す場合に使用できます。
                                                             
### <a name="example"></a>例

```json
{
  "templateType": "CollectionContent",
  "template": [
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
    },
    {
      "templateType": "CalculatedContent",
      "template": {
        "typeName": "stepcount",
        "typeMatchExpression": "$..[?(@steps)]",
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
  ]
}
```

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
|CorrelationIdExpression|*省略* 可能: 関連付け識別子を抽出する式。 この出力を使用すると、FHIR 変換先マッピングで値を 1 つの観測値にグループ化できます。|`$.matchedToken.correlationId`|
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

一連の IoT コネクタカスタム関数も使用できます。 これらのカスタム関数は、JmesPath 仕様の一部として提供される関数の外部です。 IoT コネクタのカスタム関数の詳細については [、「IoT コネクタの顧客関数」を参照してください](./iot-connector-custom-functions.md)。

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

And

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

**Project1 つのメッセージからの複数の測定値**

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

**Projectメッセージ内の配列からの複数の測定値**

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

**Project一致したトークンと元のイベントからのデータ**

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
## <a name="iotjsonpathcontenttemplate"></a>IotJsonPathContentTemplate

IotJsonPathContentTemplate は、を除き、JsonPathContentTemplate に似ていますが、必須では `DeviceIdExpression` `TimestampExpression` ありません。

このテンプレートを使用する場合、 [Azure IoT Central](../../iot-central/core/overview-iot-central.md)の[Azure IoT Hub デバイス sdk](../../iot-hub/iot-hub-devguide-sdks.md#azure-iot-hub-device-sdks)または[データのエクスポート (レガシ)](../../iot-central/core/howto-export-data-legacy.md)機能を使用して、評価されるメッセージが送信されたという前提があります。 

これらの Sdk を使用している場合、デバイス id とメッセージのタイムスタンプが知られています。

>[!IMPORTANT]
>Azure Iot hub からデバイス id を使用していること、または宛先 fhir サービスのデバイスリソースの識別子として登録されている Central Azure IoT を使用していることを確認します。

Azure IoT Hub デバイス sdk を使用している場合でも、デバイス id または測定タイムスタンプのメッセージ本文でカスタムプロパティを使用していると仮定して、JsonPathContentTemplate を使用することができます。

> [!NOTE]
> を使用する場合、 `IotJsonPathContentTemplate` は `TypeMatchExpression` メッセージ全体を jtoken として解決する必要があります。 詳細については、次の例を参照してください。

### <a name="examples"></a>例

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
## <a name="iotcentraljsonpathcontenttemplate"></a>IotCentralJsonPathContentTemplate

IotCentralJsonPathContentTemplate でも、DeviceIdExpression と TimestampExpression は必要ありません。 これは、評価対象メッセージが [Azure IoT Central](../../iot-central/core/overview-iot-central.md) の[データのエクスポート](../../iot-central/core/howto-export-data.md)機能を使用して送信されるときに使用されます。 

Azure IoT Central のデータエクスポート機能と、デバイス id または測定タイムスタンプのメッセージ本文でカスタムプロパティを使用している場合でも、JsonPathContentTemplate を使用できます。

> [!NOTE]
> を使用する場合 `IotCentralJsonPathContentTemplate` 、は `TypeMatchExpression` メッセージ全体を jtoken として解決する必要があります。 詳細については、次の例を参照してください。
 
### <a name="examples"></a>例

**心拍数**

*メッセージ*

```json
{
    "applicationId": "1dffa667-9bee-4f16-b243-25ad4151475e",
    "messageSource": "telemetry",
    "deviceId": "1vzb5ghlsg1",
    "schema": "default@v1",
    "templateId": "urn:qugj6vbw5:___qbj_27r",
    "enqueuedTime": "2020-08-05T22:26:55.455Z",
    "telemetry": {
        "Activity": "running",
        "BloodPressure": {
            "Diastolic": 7,
            "Systolic": 71
        },
        "BodyTemperature": 98.73447010562934,
        "HeartRate": 88,
        "HeartRateVariability": 17,
        "RespiratoryRate": 13
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
    "enqueuedTime": "2020-08-05T22:26:55.455Z",
    "telemetry": {
        "Activity": "running",
        "BloodPressure": {
            "Diastolic": 7,
            "Systolic": 71
        },
        "BodyTemperature": 98.73447010562934,
        "HeartRate": 88,
        "HeartRateVariability": 17,
        "RespiratoryRate": 13
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

この記事では、デバイスマッピングの使用方法について説明しました。 FHIR 変換先マッピングの使用方法については、「」を参照してください。

>[!div class="nextstepaction"]
>[FHIR 変換先マッピングの使用方法](how-to-use-fhir-mapping-iot.md)

(FHIR&#174;) [HL7](https://hl7.org/fhir/) の登録商標であり、HL7 のアクセス許可と共に使用されます。
