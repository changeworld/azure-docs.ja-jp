---
title: "Azure マネージ アプリケーションの DropDown UI 要素 | Microsoft Docs"
description: "Azure マネージ アプリケーションの Microsoft.Common.DropDown UI 要素について説明します。"
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/12/2017
ms.author: tomfitz
ms.openlocfilehash: a3cbb7363c04043eb253e91e058753bc271e4e01
ms.sourcegitcommit: 3ab5ea589751d068d3e52db828742ce8ebed4761
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/27/2017
---
# <a name="microsoftcommondropdown-ui-element"></a>Microsoft.Common.DropDown UI 要素
ドロップダウン リストを備えた選択コントロールです。 この要素は、[Azure マネージ アプリケーションを作成する](publish-service-catalog-app.md)ときに使用します。

## <a name="ui-sample"></a>UI サンプル
![Microsoft.Common.DropDown](./media/managed-application-elements/microsoft.common.dropdown.png)

## <a name="schema"></a>スキーマ
```json
{
  "name": "element1",
  "type": "Microsoft.Common.DropDown",
  "label": "Some drop down",
  "defaultValue": "Foo",
  "toolTip": "",
  "constraints": {
    "allowedValues": [
      {
        "label": "Foo",
        "value": "Bar"
      },
      {
        "label": "Baz",
        "value": "Qux"
      }
    ]
  },
  "visible": true
}
```

## <a name="remarks"></a>解説
- `constraints.allowedValues` のラベルは項目の表示テキストであり、その値は要素が選択された際の出力値です。
- 指定する場合、既定値は `constraints.allowedValues` 内に存在するラベルである必要があります。 指定しない場合、`constraints.allowedValues` 内の最初の項目が選択されます。 既定値は **null** です。
- `constraints.allowedValues` には少なくとも 1 つの項目を含める必要があります。
- この要素は `constraints.required` プロパティをサポートしていません。 この動作をエミュレートするには、ラベルと `""` という値 (空の文字列) を持つ項目を `constraints.allowedValues` に追加します。

## <a name="sample-output"></a>サンプル出力
```json
"Bar"
```

## <a name="next-steps"></a>次のステップ
* マネージ アプリケーションの概要については、「[Azure マネージ アプリケーションの概要](overview.md)」を参照してください。
* UI 定義の作成の概要については、「[CreateUiDefinition の基本概念](create-uidefinition-overview.md)」を参照してください。
* UI 要素の共通プロパティの説明については、「[CreateUiDefinition の要素](create-uidefinition-elements.md)」を参照してください。
