---
title: IoT コネクタでの FHIR 変換先マッピング - Azure Healthcare API
description: この記事では、IoT コネクタで FHIR 変換先マッピング テンプレートを使用する方法について説明します。
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: how-to
ms.date: 11/05/2021
ms.author: jasteppe
ms.openlocfilehash: 0839376e1f04acc525cd7b33cd281edd3e66ee56
ms.sourcegitcommit: 5af89a2a7b38b266cc3adc389d3a9606420215a9
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/08/2021
ms.locfileid: "131990359"
---
# <a name="how-to-use-the-fhir-destination-mappings"></a>FHIR 変換先マッピングを使用する方法

> [!IMPORTANT]
> Azure Healthcare APIs は現在プレビュー段階です。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」に記載されています。

この記事では、高速ヘルスケア相互運用性リソース (FHIR&#174;) 変換先マッピングを使用して IoT コネクタを構成する方法について説明します。

> [!TIP]
> IoT コネクタの Device と FHIR の変換先マッピングの編集、テスト、トラブルシューティングを行う [IoMT](https://github.com/microsoft/iomt-fhir/tree/master/tools/data-mapper) コネクタ データ マッパー ツールを確認してください。 Azure portal で IoT コネクタにアップロードしたり、オープン ソース バージョンの IoT コネクタで使用したりするために [マッピングをエクスポート](https://github.com/microsoft/iomt-fhir) します。

次に示すのは、IoT コネクタ内の正規化および変換プロセス中に行う処理の概念例です。

:::image type="content" source="media/iot-data-normalization-high-level.png" alt-text="IoT データ正規化フローの例1" lightbox="media/iot-data-normalization-high-level.png":::

## <a name="fhir-destination-mappings"></a>FHIR 宛先マッピング

デバイス コンテンツが正規化されたモデルに抽出されると、デバイス識別子、測定の種類、期間に従ってデータが収集され、グループ化されます。 このグループの出力は、FHIR リソース (現在は [Observation](https://www.hl7.org/fhir/observation.html)) に変換するために送信されます。 FHIR 変換先マッピング テンプレートは、データを FHIR 観察にマップする方法を制御します。 特定の時点または 1 時間という期間にわたって観察を作成する必要がありますか。 観察にどのようなコードを追加する必要がありますか。 値は [SampledData](https://www.hl7.org/fhir/datatypes.html#SampledData) または Quantity として表す [必要がありますか](https://www.hl7.org/fhir/datatypes.html#Quantity)? これらのデータ型は、FHIR 変換先マッピング構成コントロールのすべてのオプションです。

> [!NOTE]
> マッピングは基になる BLOB ストレージに格納され、コンピューティングの実行ごとに BLOB から読み込まれます。 更新された場合は、すぐに有効になります。 

### <a name="codevaluefhirtemplate"></a>CodeValueFhirTemplate

現在、CodeValueFhirTemplate は、現時点で FHIR 変換先マッピングでサポートされている唯一のテンプレートです。  これを使用すると、コード、有効期間、観察の値を定義できます。 複数の値の種類がサポートされています: [SampledData](https://www.hl7.org/fhir/datatypes.html#SampledData)、[CodeableConcept](https://www.hl7.org/fhir/datatypes.html#CodeableConcept)、[Quantity](https://www.hl7.org/fhir/datatypes.html#Quantity)。 これらの構成可能な値と共に、Observation リソースの識別子と、適切な Device および Patient リソースへのリンクが自動的に処理されます。

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
|**Components**|*省略可能:* 観察に対して作成する 1 つ以上のコンポーネント。
|**Components[].Codes**|コンポーネントに適用する 1 つ以上の [Codings](http://hl7.org/fhir/datatypes-definitions.html#coding)。
|**Components[].Value**|コンポーネントで抽出して表す値。 詳細については、「[値の型テンプレート](#value-type-templates)」を参照してください。

### <a name="value-type-templates"></a>値の型テンプレート 

現在サポートされている値の型テンプレートを以下に示します。

#### <a name="sampleddata"></a>SampledData

[SampledData](http://hl7.org/fhir/datatypes.html#SampledData) FHIR データ型を表します。 観測測定値は、ある時点から始まる値ストリームに書き込み、定義された期間を使用して前方にインクリメントされます。 値が存在しない場合、`E` はデータ ストリームに書き込まれます。 この期間が、さらに 2 つの値がデータ ストリーム内の同じ位置を占めている場合は、最新の値が使用されます。 SampledData を使用した観察が更新されるときは、同じロジックが適用されます。

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

この記事では、FHIR 変換先マッピングを使用する方法について学習しました。 デバイス マッピングを使用する方法については、以下を参照してください。

>[!div class="nextstepaction"]
>[デバイス マッピングを使用する方法](how-to-use-device-mapping-iot.md)

(FHIR&#174;) は HL7 の商標であり [、HL7](https://hl7.org/fhir/) の許可を得て使用されます。
