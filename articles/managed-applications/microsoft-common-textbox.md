---
title: Azure の TextBox UI 要素 | Microsoft Docs
description: Azure Portal の Microsoft.Common.TextBox UI 要素について説明します。
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
ms.openlocfilehash: fa3e5fff8080acb9e824ffe27f6c149054804830
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/27/2018
ms.locfileid: "37063648"
---
# <a name="microsoftcommontextbox-ui-element"></a>Microsoft.Common.TextBox UI 要素
書式設定されていないテキストの編集に使用できるコントロールです。

## <a name="ui-sample"></a>UI サンプル
![Microsoft.Common.TextBox](./media/managed-application-elements/microsoft.common.textbox.png)

## <a name="schema"></a>スキーマ
```json
{
  "name": "element1",
  "type": "Microsoft.Common.TextBox",
  "label": "Example text box 1",
  "defaultValue": "my text value",
  "toolTip": "Use only allowed characters",
  "constraints": {
    "required": true,
    "regex": "^[a-z0-9A-Z]{1,30}$",
    "validationMessage": "Only alphanumeric characters are allowed, and the value must be 1-30 characters long."
  },
  "visible": true
}
```

## <a name="remarks"></a>解説
- `constraints.required` が **true** に設定されている場合、テキスト ボックスには、正常に検証を完了できる値を指定する必要があります。 既定値は **false** です。
- `constraints.regex` は JavaScript の正規表現パターンです。 指定する場合、テキスト ボックスの値は、正常に検証を完了できるパターンと一致する必要があります。 既定値は **null** です。
- `constraints.validationMessage` はテキスト ボックスの値の検証に失敗したときに表示される文字列です。 指定しない場合、テキスト ボックスの組み込みの検証メッセージが使用されます。 既定値は **null** です。
- `constraints.required` が **false** に設定されている場合、`constraints.regex` の値を指定することができます。 このシナリオでは、テキスト ボックスに正常に検証を完了できる値を指定する必要はありません。 指定する場合、正規表現パターンと一致する必要があります。

## <a name="sample-output"></a>サンプル出力

```json
"my text value"
```

## <a name="next-steps"></a>次の手順
* UI 定義の作成の概要については、「[CreateUiDefinition の基本概念](create-uidefinition-overview.md)」を参照してください。
* UI 要素の共通プロパティの説明については、「[CreateUiDefinition の要素](create-uidefinition-elements.md)」を参照してください。
