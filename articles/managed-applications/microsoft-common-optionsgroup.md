---
title: Azure OptionsGroup UI 要素 | Microsoft Docs
description: Azure Portal の Microsoft.Common.OptionsGroup UI 要素について説明します。
services: managed-applications
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: managed-applications
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/30/2018
ms.author: tomfitz
ms.openlocfilehash: 5387f3911c58b115629c461420737230fce6b85a
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/17/2018
---
# <a name="microsoftcommonoptionsgroup-ui-element"></a>Microsoft.Common.OptionsGroup UI 要素
使用可能なオプションの行を備えた選択コントロールです。

## <a name="ui-sample"></a>UI サンプル
![Microsoft.Common.OptionsGroup](./media/managed-application-elements/microsoft.common.optionsgroup.png)

## <a name="schema"></a>スキーマ
```json
{
  "name": "element1",
  "type": "Microsoft.Common.OptionsGroup",
  "label": "Some options group",
  "defaultValue": "my value",
  "toolTip": "",
  "constraints": {
    "allowedValues": [
      {
        "label": "Value one",
        "value": "one"
      },
      {
        "label": "Value two",
        "value": "two"
      }
    ]
  },
  "visible": true
}
```

## <a name="remarks"></a>解説
- `constraints.allowedValues` のラベルは項目の表示テキストであり、その値は要素が選択された際の出力値です。
- 指定する場合、既定値は `constraints.allowedValues` 内に存在するラベルである必要があります。 指定しない場合、`constraints.allowedValues` 内の最初の項目が既定で選択されます。 既定値は **null** です。
- `constraints.allowedValues` には少なくとも 1 つの項目を含める必要があります。
- この要素は `constraints.required` プロパティをサポートしていないため、正常に検証を完了できる項目を選択する必要があります。

## <a name="sample-output"></a>サンプル出力
```json
"Bar"
```

## <a name="next-steps"></a>次の手順
* UI 定義の作成の概要については、「[CreateUiDefinition の基本概念](create-uidefinition-overview.md)」を参照してください。
* UI 要素の共通プロパティの説明については、「[CreateUiDefinition の要素](create-uidefinition-elements.md)」を参照してください。
