---
title: デバイス マッピングの IotCentralJsonPathContentTemplate IoT Connectorマッピング - Azure Healthcare API
description: この記事では、デバイス マッピング テンプレートを使用した IotCentralJsonPathContent マッピングIoT Connectorについて説明します。
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: how-to
ms.date: 11/16/2021
ms.author: jasteppe
ms.openlocfilehash: 50c4d8bad51998f8077e2d5915748d0a19bd0a00
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132733578"
---
# <a name="how-to-use-iotcentraljsonpathcontenttemplate-mappings"></a>IotCentralJsonPathContentTemplate マッピングを使用する方法

> [!IMPORTANT]
> Azure Healthcare APIs は現在プレビュー段階です。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」に記載されています。

> [!TIP]
> IoT コネクタの Device と FHIR の変換先マッピングの編集、テスト、トラブルシューティングを行う [IoMT](https://github.com/microsoft/iomt-fhir/tree/master/tools/data-mapper) コネクタ データ マッパー ツールを確認してください。 Azure portal で IoT コネクタにアップロードしたり、オープン ソース バージョンの IoT コネクタで使用したりするために [マッピングをエクスポート](https://github.com/microsoft/iomt-fhir) します。

この記事では、IoT コネクタのデバイス マッピングで IoTCentralJsonPathContentTemplate マッピングを使用する方法について説明します。

## <a name="iotcentraljsonpathcontenttemplate"></a>IotCentralJsonPathContentTemplate

IotCentralJsonPathContentTemplate でも、DeviceIdExpression と TimestampExpression は必要ありません。 これは、評価対象メッセージが [Azure IoT Central](../../iot-central/core/overview-iot-central.md) の[データのエクスポート](../../iot-central/core/howto-export-data.md)機能を使用して送信されるときに使用されます。 

Azure IoT Central のデータ エクスポート機能と、デバイス ID または測定タイムスタンプのメッセージ本文のカスタム プロパティを使用している場合でも、JsonPathContentTemplate を使用できます。

> [!NOTE]
> を使用 `IotCentralJsonPathContentTemplate` する `TypeMatchExpression` 場合は、メッセージ全体を JToken として解決する必要があります。 詳細については、次の例を参照してください。
 
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

この記事では、デバイス マッピングを使用する方法について学習しました。 FHIR 変換先マッピングを使用する方法については、次を参照してください。

>[!div class="nextstepaction"]
>[FHIR 変換先マッピングを使用する方法](how-to-use-fhir-mappings.md)

(FHIR&#174;) は HL7 の商標であり [、HL7](https://hl7.org/fhir/) の許可を得て使用されます。
