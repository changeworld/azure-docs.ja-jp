---
title: デバイス マッピングの IotJsonPathContentTemplate IoT Connectorマッピング - Azure Healthcare API
description: この記事では、デバイス マッピング テンプレートで IotJsonPathContentTemplate マッピングを使用IoT Connector方法について説明します。
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: how-to
ms.date: 11/22/2021
ms.author: jasteppe
ms.openlocfilehash: 56304f99e786a06abdfa67495bea061d21ebabb4
ms.sourcegitcommit: 3d04177023a3136832adb561da831ccc8e9910c7
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/23/2021
ms.locfileid: "132936508"
---
# <a name="how-to-use-iotjsonpathcontenttemplate-mappings"></a>IotJsonPathContentTemplate マッピングを使用する方法

> [!IMPORTANT]
> Azure Healthcare APIs は現在プレビュー段階です。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」に記載されています。

> [!TIP]
> IoT コネクタの Device と FHIR の変換先マッピングの編集、テスト、トラブルシューティングを行う [IoMT](https://github.com/microsoft/iomt-fhir/tree/master/tools/data-mapper) コネクタ データ マッパー ツールを確認してください。 Azure portal で IoT コネクタにアップロードしたり、オープン ソース バージョンの IoT コネクタで使用したりするために [マッピングをエクスポート](https://github.com/microsoft/iomt-fhir) します。

この記事では、IoT コネクタのデバイス マッピング テンプレートで IoTJsonPathContentTemplate マッピングを使用する方法について説明します。

## <a name="iotjsonpathcontenttemplate"></a>IotJsonPathContentTemplate

IotJsonPathContentTemplate は、必須ではない を除き、JsonPathContentTemplate に `DeviceIdExpression` `TimestampExpression` 似ています。

このテンプレートを使用する場合、評価されるメッセージは[、Azure IoT Hub Device SDK](../../iot-hub/iot-hub-devguide-sdks.md#azure-iot-hub-device-sdks)または Azure IoT Central のデータのエクスポート[(レガシ)](../../iot-central/core/howto-export-data-legacy.md)機能を使用して送信[されたという前提があります](../../iot-central/core/overview-iot-central.md)。 

これらの SDK を使用している場合、デバイス ID とメッセージのタイムスタンプが既知です。

>[!IMPORTANT]
>宛先 FHIR サービスのデバイス リソースの識別子として登録されている Azure Iot Hub または Azure IoT Central のデバイス識別子を使用している必要があります。

Azure IoT Hub Device SDK を使用している場合でも、デバイス ID または測定タイムスタンプにメッセージ本文のカスタム プロパティを使用している場合は、JsonPathContentTemplate を使用できます

> [!NOTE]
> を使用 `IotJsonPathContentTemplate` する場合 `TypeMatchExpression` 、 はメッセージ全体を JToken として解決する必要があります。 詳細については、次の例を参照してください。

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

> [!TIP]
> 一般的なエラーと [問題の修正については、IoT](./iot-troubleshoot-guide.md) コネクタのトラブルシューティング ガイドを参照してください。

## <a name="next-steps"></a>次のステップ

この記事では、デバイス マッピングを使用する方法について学習しました。 FHIR 変換先マッピングを使用する方法については、次を参照してください。

>[!div class="nextstepaction"]
>[FHIR 変換先マッピングを使用する方法](how-to-use-fhir-mappings.md)

(FHIR&#174;) は HL7 の商標であり [、HL7](https://hl7.org/fhir/) の許可を得て使用されます。
