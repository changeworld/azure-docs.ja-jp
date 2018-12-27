---
title: Azure UserNameTextBox UI 要素 | Microsoft Docs
description: Azure Portal の Microsoft.Compute.UserNameTextBox UI 要素について説明します。
services: managed-applications
documentationcenter: na
author: tfitzmac
editor: tysonn
ms.service: managed-applications
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: 9f07c5bf9ba1f1880fa142beb52455522425e68d
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/27/2018
ms.locfileid: "37063303"
---
# <a name="microsoftcomputeusernametextbox-ui-element"></a>Microsoft.Compute.UserNameTextBox UI 要素
Windows と Linux のユーザー名の検証が組み込まれているテキスト ボックス コントロールです。

## <a name="ui-sample"></a>UI サンプル
![Microsoft.Compute.UserNameTextBox](./media/managed-application-elements/microsoft.compute.usernametextbox.png)

## <a name="schema"></a>スキーマ
```json
{
  "name": "element1",
  "type": "Microsoft.Compute.UserNameTextBox",
  "label": "User name",
  "defaultValue": "",
  "toolTip": "",
  "constraints": {
    "required": true,
    "regex": "^[a-z0-9A-Z]{1,30}$",
    "validationMessage": "Only alphanumeric characters are allowed, and the value must be 1-30 characters long."
  },
  "osPlatform": "Windows",
  "visible": true
}
```

## <a name="remarks"></a>解説
- `constraints.required` が **true** に設定されている場合、テキスト ボックスには、正常に検証を完了できる値を指定する必要があります。 既定値は **true**です。
- `osPlatform` は指定する必要があり、**Windows** と **Linux** のいずれかを使用できます。
- `constraints.regex` は JavaScript の正規表現パターンです。 指定する場合、テキスト ボックスの値は、正常に検証を完了できるパターンと一致する必要があります。 既定値は **null** です。
- `constraints.validationMessage` はテキスト ボックスの値が `constraints.regex` で指定された検証に失敗したときに表示される文字列です。 指定しない場合、テキスト ボックスの組み込みの検証メッセージが使用されます。 既定値は **null** です。
- この要素には、`osPlatform` で指定された値に基づく検証が組み込まれています。 この組み込みの検証では、カスタム正規表現も使用できます。 `constraints.regex` の値が指定されている場合、組み込み検証とカスタム検証の両方がトリガーされます。

## <a name="sample-output"></a>サンプル出力
```json
"Example name"
```

## <a name="next-steps"></a>次の手順
* UI 定義の作成の概要については、「[CreateUiDefinition の基本概念](create-uidefinition-overview.md)」を参照してください。
* UI 要素の共通プロパティの説明については、「[CreateUiDefinition の要素](create-uidefinition-elements.md)」を参照してください。
