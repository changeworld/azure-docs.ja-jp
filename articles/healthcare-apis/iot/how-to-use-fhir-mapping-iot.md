---
title: IoT コネクタの FHIR マッピング テンプレート - Azure Healthcare APIs
description: この記事では、IoT コネクタで FHIR マッピング テンプレートを使用する方法について説明します。
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 07/12/2021
ms.author: rabhaiya
ms.openlocfilehash: be40d72ea7e0da6d1ef48b2b9ca28d73f85f4d29
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121781109"
---
# <a name="how-to-use-the-fhir-mapping-template"></a>FHIR マッピング テンプレートの使用方法

この記事では、FHIR マッピング テンプレートを使用して IoT コネクタを構成する方法について説明します。

## <a name="fhir-mapping"></a>FHIR マッピング

デバイス コンテンツが正規化されたモデルに抽出されると、デバイス識別子、測定の種類、期間に従ってデータが収集され、グループ化されます。 このグループの出力は、FHIR リソース (現在は [Observation](https://www.hl7.org/fhir/observation.html)) に変換するために送信されます。 FHIR マッピング テンプレートでは、データを FHIR Observation にマップする方法を制御します。 特定の時点または 1 時間という期間にわたって観察を作成する必要がありますか。 観察にどのようなコードを追加する必要がありますか。 値を [SampledData](https://www.hl7.org/fhir/datatypes.html#SampledData) として、または [Quantity](https://www.hl7.org/fhir/datatypes.html#Quantity) として表す必要がありますか。 これらのデータ型はすべて、FHIR マッピング構成コントロールのオプションです。

### <a name="codevaluefhirtemplate"></a>CodeValueFhirTemplate

CodeValueFhirTemplate は、FHIR マッピングで現在サポートされている唯一のテンプレートです。  これを使用すると、コード、有効期間、観察の値を定義できます。 複数の値の種類がサポートされています: [SampledData](https://www.hl7.org/fhir/datatypes.html#SampledData)、[CodeableConcept](https://www.hl7.org/fhir/datatypes.html#CodeableConcept)、[Quantity](https://www.hl7.org/fhir/datatypes.html#Quantity)。 これらの構成可能な値と共に、Observation リソースの識別子と、適切な Device および Patient リソースへのリンクが自動的に処理されます。

| プロパティ | 説明 
| --- | ---
|**TypeName**| このテンプレートのバインド先となる測定の種類。 この種類を出力するデバイス マッピング テンプレートが少なくとも 1 つ存在する必要があります。
|**PeriodInterval**|作成された観察が表す期間。 サポートされている値は、0 (1 インスタンス)、60 (1 時間)、1440 (1 日) です。
|**カテゴリ**|作成される観察の種類を分類する任意の数の [CodeableConcepts](http://hl7.org/fhir/datatypes-definitions.html#codeableconcept)。
|**Codes**|作成された観察に適用する 1 つ以上の [Codings](http://hl7.org/fhir/datatypes-definitions.html#coding)。
|**Codes[].Code**|[Coding](http://hl7.org/fhir/datatypes-definitions.html#coding) のコード。
|**Codes[].System**|[Coding](http://hl7.org/fhir/datatypes-definitions.html#coding) のシステム。
|**Codes[].Display**|[Coding](http://hl7.org/fhir/datatypes-definitions.html#coding) の表示。
|**Value**|観察で抽出して表す値。 詳細については、「[値の型テンプレート](#value-type-templates)」を参照してください。
|**コンポーネント**|*省略可能:* 観察に対して作成する 1 つ以上のコンポーネント。
|**Components[].Codes**|コンポーネントに適用する 1 つ以上の [Codings](http://hl7.org/fhir/datatypes-definitions.html#coding)。
|**Components[].Value**|コンポーネントで抽出して表す値。 詳細については、「[値の型テンプレート](#value-type-templates)」を参照してください。

### <a name="value-type-templates"></a>値の型テンプレート 

現在サポートされている値の型テンプレートを以下に示します。

#### <a name="sampleddata"></a>SampledData

[SampledData](http://hl7.org/fhir/datatypes.html#SampledData) FHIR データ型を表します。観察の測定値は、特定の時点から開始し、定義された期間を使用して前方にインクリメントされる値ストリームに書き込まれます。 値が存在しない場合、`E` はデータ ストリームに書き込まれます。 この期間が、さらに 2 つの値がデータ ストリーム内の同じ位置を占めている場合は、最新の値が使用されます。 SampledData を使用した観察が更新されるときは、同じロジックが適用されます。

| プロパティ | 説明 
| --- | ---
|**DefaultPeriod**|使用する既定の時間 (ミリ秒単位)。 
|**単位**|SampledData の原点に設定する単位。 

#### <a name="quantity"></a>Quantity

FHIR データ型の [Quantity](http://hl7.org/fhir/datatypes.html#Quantity) を表します。 グループ内に複数の値が存在する場合は、最初の値のみが使用されます。 同じ観察にマップされる新しい値が到着すると、古い値が上書きされます。

| プロパティ | 説明 
| --- | --- 
|**単位**| 単位の表記。
|**コード**| 単位のコード化された形式。
|**システム**| コード化された単位の形式を定義するシステム。

### <a name="codeableconcept"></a>CodeableConcept

FHIR データ型 [CodeableConcept](http://hl7.org/fhir/datatypes.html#CodeableConcept) を表します。 実際の値は使用されません。

| プロパティ | 説明 
| --- | --- 
|**[テキスト]**|プレーンテキスト表現。 
|**Codes**|作成された観察に適用する 1 つ以上の [Codings](http://hl7.org/fhir/datatypes-definitions.html#coding)。
|**Codes[].Code**|[Coding](http://hl7.org/fhir/datatypes-definitions.html#coding) のコード。
|**Codes[].System**|[Coding](http://hl7.org/fhir/datatypes-definitions.html#coding) のシステム。
|**Codes[].Display**|[Coding](http://hl7.org/fhir/datatypes-definitions.html#coding) の表示。

### <a name="examples"></a>例

**心拍 - SampledData**
```json
{
    "templateType": "CodeValueFhir",
    "template": {
        "codes": [
            {
                "code": "8867-4",
                "system": "http://loinc.org",
                "display": "Heart rate"
            }
        ],
        "periodInterval": 60,
        "typeName": "heartrate",
        "value": {
            "defaultPeriod": 5000,
            "unit": "count/min",
            "valueName": "hr",
            "valueType": "SampledData"
        }
    }
}
```

**歩数 - SampledData**

```json
{
    "templateType": "CodeValueFhir",
    "template": {
        "codes": [
            {
                "code": "55423-8",
                "system": "http://loinc.org",
                "display": "Number of steps"
            }
        ],        
        "periodInterval": 60,
        "typeName": "stepsCount",
        "value": {
            "defaultPeriod": 5000,
            "unit": "",
            "valueName": "steps",
            "valueType": "SampledData"
        }
    }
}
```

**血圧 - SampledData**

```json
{
    "templateType": "CodeValueFhir",
    "template": {
        "codes": [
            {
                "code": "85354-9",
                "display": "Blood pressure panel with all children optional",
                "system": "http://loinc.org"
            }
        ],
        "periodInterval": 60,
        "typeName": "bloodpressure",
        "components": [
            {
                "codes": [
                    {
                        "code": "8867-4",
                        "display": "Diastolic blood pressure",
                        "system": "http://loinc.org"
                    }
                ],
                "value": {
                    "defaultPeriod": 5000,
                    "unit": "mmHg",
                    "valueName": "diastolic",
                    "valueType": "SampledData"
                }
            },
            {
                "codes": [
                    {
                        "code": "8480-6",
                        "display": "Systolic blood pressure",
                        "system": "http://loinc.org"
                    }
                ],
                "value": {
                    "defaultPeriod": 5000,
                    "unit": "mmHg",
                    "valueName": "systolic",
                    "valueType": "SampledData"
                }
            }
        ]
    }
}
```

**血圧 - Quantity**

```json
{
    "templateType": "CodeValueFhir",
    "template": {
        "codes": [
            {
                "code": "85354-9",
                "display": "Blood pressure panel with all children optional",
                "system": "http://loinc.org"
            }
        ],
        "periodInterval": 0,
        "typeName": "bloodpressure",
        "components": [
            {
                "codes": [
                    {
                        "code": "8867-4",
                        "display": "Diastolic blood pressure",
                        "system": "http://loinc.org"
                    }
                ],
                "value": {
                    "unit": "mmHg",
                    "valueName": "diastolic",
                    "valueType": "Quantity"
                }
            },
            {
                "codes": [
                    {
                        "code": "8480-6",
                        "display": "Systolic blood pressure",
                        "system": "http://loinc.org"
                    }
                ],
                "value": {
                    "unit": "mmHg",
                    "valueName": "systolic",
                    "valueType": "Quantity"
                }
            }
        ]
    }
}
```

**削除されたデバイス - CodeableConcept**

```json
{
    "templateType": "CodeValueFhir",
    "template": {
        "codes": [
            {
                "code": "deviceEvent",
                "system": "https://www.mydevice.com/v1",
                "display": "Device Event"
            }
        ],
        "periodInterval": 0,
        "typeName": "deviceRemoved",
        "value": {
            "text": "Device Removed",
            "codes": [
                {
                    "code": "deviceRemoved",
                    "system": "https://www.mydevice.com/v1",
                    "display": "Device Removed"
                }
            ],
            "valueName": "deviceRemoved",
            "valueType": "CodeableConcept"
        }
    }
}
```


## <a name="next-steps"></a>次のステップ


>[!div class="nextstepaction"]
>[デバイス マッピング テンプレートの使用方法](how-to-use-device-mapping-iot.md)
