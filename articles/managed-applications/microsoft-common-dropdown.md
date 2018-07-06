---
title: Azure の DropDown UI 要素 | Microsoft Docs
description: Azure Portal の Microsoft.Common.DropDown UI 要素について説明します。
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
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: f953e1dc15e12c37c30a86ebd7536b1126bf18f7
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/27/2018
ms.locfileid: "37062002"
---
# <a name="microsoftcommondropdown-ui-element"></a>Microsoft.Common.DropDown UI 要素
ドロップダウン リストを備えた選択コントロールです。

## <a name="ui-sample"></a>UI サンプル
![Microsoft.Common.DropDown](./media/managed-application-elements/microsoft.common.dropdown.png)

## <a name="schema"></a>スキーマ
```json
{
  "name": "element1",
  "type": "Microsoft.Common.DropDown",
  "label": "Example drop down",
  "defaultValue": "Value two",
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
    ],
    "required": true
  },
  "visible": true
}
```

## <a name="remarks"></a>解説

- `constraints.allowedValues` のラベルは項目の表示テキストであり、その値は要素が選択された際の出力値です。
- 指定する場合、既定値は `constraints.allowedValues` 内に存在するラベルである必要があります。 指定しない場合、`constraints.allowedValues` 内の最初の項目が選択されます。 既定値は **null** です。
- `constraints.allowedValues` には少なくとも 1 つの項目が必要です。
- 必須ではない値をエミュレートするには、ラベルと `""` という値 (空の文字列) を持つ項目を `constraints.allowedValues` に追加します。

## <a name="sample-output"></a>サンプル出力
```json
"two"
```

## <a name="next-steps"></a>次の手順
* UI 定義の作成の概要については、「[CreateUiDefinition の基本概念](create-uidefinition-overview.md)」を参照してください。
* UI 要素の共通プロパティの説明については、「[CreateUiDefinition の要素](create-uidefinition-elements.md)」を参照してください。
