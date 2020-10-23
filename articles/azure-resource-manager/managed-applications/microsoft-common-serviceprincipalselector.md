---
title: ServicePrincipalSelector UI 要素
description: Azure portal 用の Microsoft.Common.ServicePrincipalSelector UI 要素について説明します。 アプリケーション識別子を選択するドロップダウンと、パスワードまたは証明書の拇印を入力するテキスト ボックスを提供します。
author: tfitzmac
ms.topic: conceptual
ms.date: 09/29/2020
ms.author: tomfitz
ms.openlocfilehash: 73b242754bfae53b6df5abd9c2c8dee33b973dad
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2020
ms.locfileid: "91575998"
---
# <a name="microsoftcommonserviceprincipalselector-ui-element"></a>Microsoft.Common.ServicePrincipalSelector UI 要素

ユーザーが既存のサービス プリンシパルを選択したり、新しいサービス プリンシパルを登録したりできるようにするコントロールです。 **[新規作成]** を選択したときには、新しいアプリケーションを登録する手順を進めることになります。 既存のアプリケーションを選択すると、コントロールにより、パスワードまたは証明書の拇印を入力するテキスト ボックスが提供されます。

## <a name="ui-sample"></a>UI サンプル

既定のビューは、`defaultValue` プロパティの値によって決まります。 `principalId` プロパティに有効なグローバル一意識別子 (GUID) が含まれている場合、コントロールでは、アプリケーションのオブジェクト ID を検索します。 ユーザーがドロップダウンから選択しない場合は、既定値が適用されます。

:::image type="content" source="./media/managed-application-elements/microsoft-common-serviceprincipal-initial.png" alt-text="Microsoft.Common.ServicePrincipalSelector の初期ビュー":::

**[新規作成]** を選択するか、ドロップダウンから既存のアプリケーション識別子を選択を選択すると、テキスト ボックスにパスワードまたは証明書の拇印を入力するために **[認証の種類]** が表示されます。

:::image type="content" source="./media/managed-application-elements/microsoft-common-serviceprincipal-selection.png" alt-text="Microsoft.Common.ServicePrincipalSelector の初期ビュー":::

## <a name="schema"></a>スキーマ

```json
{
  "name": "ServicePrincipal",
  "type": "Microsoft.Common.ServicePrincipalSelector",
  "label": {
    "principalId": "App Id",
    "password": "Password",
    "certificateThumbprint": "Certificate thumbprint",
    "authenticationType": "Authentication Type",
    "sectionHeader": "Service Principal"
  },
  "toolTip": {
    "principalId": "App Id",
    "password": "Password",
    "certificateThumbprint": "Certificate thumbprint",
    "authenticationType": "Authentication Type"
  },
  "defaultValue": {
    "principalId": "<default guid>",
    "name": "(New) default App Id"
  },
  "constraints": {
    "required": true,
    "regex": "^[a-zA-Z0-9]{8,}$",
    "validationMessage": "Password must be at least 8 characters long, contain only numbers and letters"
  },
  "options": {
    "hideCertificate": false
  },
  "visible": true
}
```

## <a name="remarks"></a>解説

- 必須のプロパティは以下のとおりです。
  - `name`
  - `type`
  - `label`
  - `defaultValue`:既定の `principalId` と `name` を指定します。

- 省略可能なプロパティは以下のとおりです。
  - `toolTip`:各ラベルにヒントの `infoBalloon` をアタッチします。
  - `visible`:コントロールを非表示にするか、表示します。
  - `options`:証明書の拇印オプションを使用可能にする必要があるかどうかを指定します。
  - `constraints`:パスワード検証用の正規表現による制約。

## <a name="example"></a>例

`Microsoft.Common.ServicePrincipalSelector` コントロールの例を次に示します。 `defaultValue` プロパティでは、既定のアプリケーション識別子である GUID のプレースホルダーとして、`principalId` が `<default guid>` に設定されています。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [],
    "steps": [
      {
        "name": "SPNcontrol",
        "label": "SPNcontrol",
        "elements": [
          {
            "name": "ServicePrincipal",
            "type": "Microsoft.Common.ServicePrincipalSelector",
            "label": {
              "principalId": "App Id",
              "password": "Password",
              "certificateThumbprint": "Certificate thumbprint",
              "authenticationType": "Authentication Type",
              "sectionHeader": "Service Principal"
            },
            "toolTip": {
              "principalId": "App Id",
              "password": "Password",
              "certificateThumbprint": "Certificate thumbprint",
              "authenticationType": "Authentication Type"
            },
            "defaultValue": {
              "principalId": "<default guid>",
              "name": "(New) default App Id"
            },
            "constraints": {
              "required": true,
              "regex": "^[a-zA-Z0-9]{8,}$",
              "validationMessage": "Password must be at least 8 characters long, contain only numbers and letters"
            },
            "options": {
              "hideCertificate": false
            },
            "visible": true
          }
        ]
      }
    ],
    "outputs": {
      "appId": "[steps('SPNcontrol').ServicePrincipal.appId]",
      "objectId": "[steps('SPNcontrol').ServicePrincipal.objectId]",
      "password": "[steps('SPNcontrol').ServicePrincipal.password]",
      "certificateThumbprint": "[steps('SPNcontrol').ServicePrincipal.certificateThumbprint]",
      "newOrExisting": "[steps('SPNcontrol').ServicePrincipal.newOrExisting]",
      "authenticationType": "[steps('SPNcontrol').ServicePrincipal.authenticationType]"
    }
  }
}
```

## <a name="example-output"></a>出力例

`appId` は、ユーザーが選択または作成したアプリケーション登録の ID です。 `objectId` は、選択したアプリケーション登録のために構成されたサービス プリンシパルのオブジェクト ID の配列です。

ドロップダウンから何も選択されていないときの `newOrExisting` プロパティの値は、**new** です。

```json
{
  "appId": {
    "value": "<default guid>"
  },
  "objectId": {
    "value": ["<default guid>"]
  },
  "password": {
    "value": "<password>"
  },
  "certificateThumbprint": {
    "value": ""
  },
  "newOrExisting": {
    "value": "new"
  },
  "authenticationType": {
    "value": "password"
  }
}
```

**[新規作成]** を選択するか、ドロップダウンから既存のアプリケーション識別子を選択したときの `newOrExisting` プロパティの値は、**existing** です。

```json
{
  "appId": {
    "value": "<guid>"
  },
  "objectId": {
    "value": ["<guid>"]
  },
  "password": {
    "value": "<password>"
  },
  "certificateThumbprint": {
    "value": ""
  },
  "newOrExisting": {
    "value": "existing"
  },
  "authenticationType": {
    "value": "password"
  }
}
```

## <a name="next-steps"></a>次のステップ

- UI 定義の作成の概要については、「[CreateUiDefinition の基本概念](create-uidefinition-overview.md)」を参照してください。
- UI 要素の共通プロパティの説明については、「[CreateUiDefinition の要素](create-uidefinition-elements.md)」を参照してください。
