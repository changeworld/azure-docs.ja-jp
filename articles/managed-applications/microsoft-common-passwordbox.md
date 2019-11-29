---
title: Azure の PasswordBox UI 要素 | Microsoft Docs
description: Azure Portal の Microsoft.Common.PasswordBox UI 要素について説明します。 管理対象アプリケーションをデプロイするとき、ユーザーがシークレット値を指定できるようにします。
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
ms.openlocfilehash: ab51a4096745c2930199685ac678638a956f21e0
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2019
ms.locfileid: "74151548"
---
# <a name="microsoftcommonpasswordbox-ui-element"></a>Microsoft.Common.PasswordBox UI 要素

パスワードの提供と確認に使用できるコントロールです。

## <a name="ui-sample"></a>UI サンプル

![Microsoft.Common.PasswordBox](./media/managed-application-elements/microsoft.common.passwordbox.png)

## <a name="schema"></a>スキーマ

```json
{
  "name": "element1",
  "type": "Microsoft.Common.PasswordBox",
  "label": {
    "password": "Password",
    "confirmPassword": "Confirm password"
  },
  "toolTip": "",
  "constraints": {
    "required": true,
    "regex": "^[a-zA-Z0-9]{8,}$",
    "validationMessage": "Password must be at least 8 characters long, contain only numbers and letters"
  },
  "options": {
    "hideConfirmation": false
  },
  "visible": true
}
```

## <a name="sample-output"></a>サンプル出力

```json
"p4ssw0rd"
```

## <a name="remarks"></a>解説

- この要素は `defaultValue` プロパティをサポートしていません。
- `constraints` の実装の詳細については、[Microsoft.Common.TextBox](microsoft-common-textbox.md) に関する記事を参照してください。
- `options.hideConfirmation` が **true** に設定されている場合、ユーザーのパスワードを確認するための 2 つ目のテキスト ボックスは表示されません。 既定値は **false** です。

## <a name="next-steps"></a>次の手順

* UI 定義の作成の概要については、「[CreateUiDefinition の基本概念](create-uidefinition-overview.md)」を参照してください。
* UI 要素の共通プロパティの説明については、「[CreateUiDefinition の要素](create-uidefinition-elements.md)」を参照してください。
