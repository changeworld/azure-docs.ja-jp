---
title: IoT Connector デバイスマッピングでの CollectionContentTemplate マッピング-Azure の医療 api
description: この記事では、IoT コネクタのデバイスマッピングで CollectionContentTemplate マッピングを使用する方法について説明します。
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: how-to
ms.date: 11/22/2021
ms.author: jasteppe
ms.openlocfilehash: 824f26181389cdd22ad3508c29fbb8d7ec4fcb17
ms.sourcegitcommit: 3d04177023a3136832adb561da831ccc8e9910c7
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/23/2021
ms.locfileid: "132937244"
---
# <a name="how-to-use-collectioncontenttemplate-mappings"></a>CollectionContentTemplate マッピングの使用方法

> [!IMPORTANT]
> Azure Healthcare APIs は現在プレビュー段階です。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」に記載されています。

> [!TIP]
> IoT コネクタデバイスと FHIR の宛先マッピングの編集、テスト、トラブルシューティングについては、 [IoMT コネクタデータマッパー](https://github.com/microsoft/iomt-fhir/tree/master/tools/data-mapper) ツールをご覧ください。 Azure portal で IoT コネクタにアップロードするか、 [オープンソースバージョン](https://github.com/microsoft/iomt-fhir) の iot コネクタで使用するためのマッピングをエクスポートします。

この記事では、IoT コネクタのデバイスマッピングテンプレートで CollectionContentTemplate マッピングを使用する方法について説明します。

## <a name="collectioncontenttemplate"></a>CollectionContentTemplate

CollectionContentTemplate は、正規化中に使用されるテンプレートの一覧を表すために使用できます。
                                                             
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
> [!TIP]
> 一般的なエラーと問題の解決については、「IoT コネクタの [トラブルシューティングガイド](./iot-troubleshoot-guide.md) 」を参照してください。

## <a name="next-steps"></a>次のステップ

この記事では、デバイスマッピングの使用方法について説明しました。 FHIR 変換先マッピングの使用方法については、「」を参照してください。

>[!div class="nextstepaction"]
>[FHIR 変換先マッピングの使用方法](how-to-use-fhir-mappings.md)

(FHIR&#174;) [HL7](https://hl7.org/fhir/) の登録商標であり、HL7 のアクセス許可と共に使用されます。
