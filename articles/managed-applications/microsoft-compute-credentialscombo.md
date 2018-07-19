---
title: Azure の CredentialsCombo UI 要素 | Microsoft Docs
description: Azure Portal の Microsoft.Compute.CredentialsCombo UI 要素について説明します。
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
ms.openlocfilehash: 183075f7407b0a0ca6ea53871e239ab8c2d89490
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/29/2018
ms.locfileid: "37098622"
---
# <a name="microsoftcomputecredentialscombo-ui-element"></a>Microsoft.Compute.CredentialsCombo UI 要素
Windows と Linux のパスワードと SSH 公開キーの検証が組み込まれているコントロールのグループです。

## <a name="ui-sample"></a>UI サンプル

Windows ユーザーの場合、ユーザーには次のような画面が表示されます。

![Microsoft.Compute.CredentialsCombo Windows](./media/managed-application-elements/microsoft.compute.credentialscombo-windows.png)

Linux でパスワードが選択されている場合、ユーザーには次のような画面が表示されます。

![Microsoft.Compute.CredentialsCombo Linux パスワード](./media/managed-application-elements/microsoft.compute.credentialscombo-linux-password.png)

Linux で SSH パブリック キーが選択されている場合、ユーザーには次のような画面が表示されます。

![Microsoft.Compute.CredentialsCombo Linux キー](./media/managed-application-elements/microsoft.compute.credentialscombo-linux-key.png)

## <a name="schema"></a>スキーマ
Windows の場合、次のスキーマを使います。

```json
{
  "name": "element1",
  "type": "Microsoft.Compute.CredentialsCombo",
  "label": {
    "password": "Password",
    "confirmPassword": "Confirm password"
  },
  "toolTip": {
    "password": ""
  },
  "constraints": {
    "required": true,
    "customPasswordRegex": "^(?=.*[A-Za-z])(?=.*\\d)[A-Za-z\\d]{8,}$",
    "customValidationMessage": "The password must contain at least 8 characters, with at least 1 letter and 1 number."
  },
  "options": {
    "hideConfirmation": false
  },
  "osPlatform": "Windows",
  "visible": true
}
```

**Linux** の場合、次のスキーマを使います。

```json
{
  "name": "element1",
  "type": "Microsoft.Compute.CredentialsCombo",
  "label": {
    "authenticationType": "Authentication type",
    "password": "Password",
    "confirmPassword": "Confirm password",
    "sshPublicKey": "SSH public key"
  },
  "toolTip": {
    "authenticationType": "",
    "password": "",
    "sshPublicKey": ""
  },
  "constraints": {
    "required": true,
    "customPasswordRegex": "^(?=.*[A-Za-z])(?=.*\\d)[A-Za-z\\d]{8,}$",
    "customValidationMessage": "The password must contain at least 8 characters, with at least 1 letter and 1 number."
  },
  "options": {
    "hideConfirmation": false,
    "hidePassword": false
  },
  "osPlatform": "Linux",
  "visible": true
}
```

## <a name="remarks"></a>解説
- `osPlatform` は指定する必要があり、**Windows** と **Linux** のいずれかを使用できます。
- `constraints.required` が **true** に設定されている場合、パスワードまたは SSH 公開キーのテキスト ボックスには、正常に検証を完了できる値を指定する必要があります。 既定値は **true**です。
- `options.hideConfirmation` が **true** に設定されている場合、ユーザーのパスワードを確認するための 2 つ目のテキスト ボックスは表示されません。 既定値は **false** です。
- `options.hidePassword` が **true** に設定されている場合、パスワード認証を使用するオプションは表示されません。 これは `osPlatform` が **Linux** であるときのみ使用できます。 既定値は **false** です。
- `customPasswordRegex` プロパティを使用して、許可されたパスワードに対する追加の制約を実装できます。 `customValidationMessage` 内の文字列は、パスワードのカスタム検証が失敗したときに表示されます。 これらのプロパティの既定値は両方とも **null** です。

## <a name="sample-output"></a>サンプル出力
`osPlatform` が **Windows**、または `osPlatform` が **Linux** で、ユーザーが SSH 公開キーではなくパスワードを指定した場合、コントロールは次のような出力を返します。

```json
{
  "authenticationType": "password",
  "password": "p4ssw0rd",
}
```

`osPlatform` が **Linux** で、ユーザーが SSH 公開キーを指定した場合、コントロールは次のような出力を返します。

```json
{
  "authenticationType": "sshPublicKey",
  "sshPublicKey": "AAAAB3NzaC1yc2EAAAABIwAAAIEA1on8gxCGJJWSRT4uOrR13mUaUk0hRf4RzxSZ1zRbYYFw8pfGesIFoEuVth4HKyF8k1y4mRUnYHP1XNMNMJl1JcEArC2asV8sHf6zSPVffozZ5TT4SfsUu/iKy9lUcCfXzwre4WWZSXXcPff+EHtWshahu3WzBdnGxm5Xoi89zcE=",
}
```

## <a name="next-steps"></a>次の手順
* UI 定義の作成の概要については、「[CreateUiDefinition の基本概念](create-uidefinition-overview.md)」を参照してください。
* UI 要素の共通プロパティの説明については、「[CreateUiDefinition の要素](create-uidefinition-elements.md)」を参照してください。
