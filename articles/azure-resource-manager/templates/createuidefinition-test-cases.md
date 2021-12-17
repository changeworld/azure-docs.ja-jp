---
title: Azure Resource Manager テスト ツールキットの createUiDefinition.json テスト ケース
description: Azure Resource Manager テンプレート テスト ツールキットで実行される createUiDefinition.json テストについて説明します。
ms.topic: conceptual
ms.date: 07/16/2021
ms.author: tomfitz
author: tfitzmac
ms.openlocfilehash: 573f7db7c1f6fd3d45531e8be15db3d8affc8eba
ms.sourcegitcommit: e2fa73b682a30048907e2acb5c890495ad397bd3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/16/2021
ms.locfileid: "114392036"
---
# <a name="test-cases-for-createuidefinitionjson"></a>createUiDefinition.json のテスト ケース

この記事では、[createUiDefinition.json](../managed-applications/create-uidefinition-overview.md) ファイルの[テンプレート テスト ツールキット](test-toolkit.md)で実行されるテストについて説明します。 例には、テストに **合格** する、または **不合格** になるテスト名とコード サンプルが含まれています。

このツールキットには、Azure Resource Manager テンプレート (ARM テンプレート) の [テスト ケース](template-test-cases.md)と、_azuredeploy.json_ または _maintemplate.json_ という名前のメイン テンプレート ファイルが含まれています。 ディレクトリに _createUiDefinition.json_ ファイルが含まれている場合、UI コントロールに対して特定のテストが実行されます。 テストを実行する方法、または特定のテストを実行する方法の詳細については、「[テスト パラメーター](test-toolkit.md#test-parameters)」を参照してください。

_createUiDefinition.json_ ファイルにより、[要素](../managed-applications/create-uidefinition-elements.md)と[関数](../managed-applications/create-uidefinition-functions.md) を使用してカスタム ユーザー インターフェイス (UI) コントロールが作成されます。

## <a name="verify-template-parameter-allows-values"></a>テンプレート パラメーターで値が許可されていることを確認する

テスト名: **Allowed Values Should Actually Be Allowed (許可値は実際に許可されている必要がある)**

このテストでは、_createUiDefinition.json_ の各コントロールの値が、メイン テンプレートのパラメーターで許可されていることを確認します。 パラメーターは、メイン テンプレートと _createUiDefinition.json_ ファイルの間で名前によってマップされます。

メイン テンプレートのパラメーターは、コントロールの `allowedValues` からの値を受け付ける必要があります。 このテストでは、_createUiDefinition.json_ の `outputs` セクションでコントロールが参照されていることも確認します。

このテストでは、メイン テンプレートと _createUiDefinition.json_ ファイルを確認します。 メイン テンプレートの例の後に _createUiDefinition.json_ ファイルの例を示します。

次の例は、メイン テンプレートのパラメーター名 `combo` がコントロールのパラメーター名 `comboBox` と一致しないため、**不合格** になります。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "combo": {
      "type": "string",
      "defaultValue": "two"
    }
  },
  "resources": [],
  "outputs": {
    "comboBoxOutput": {
      "type": "string",
      "value": "[parameters('combo')]"
    }
  }
}
```

次の例は、メイン テンプレートのパラメーターの型 `int` がコントロールの `string` 値を受け付けないため、**不合格** になります。 また、メイン テンプレートのパラメーターに `defaultValue` が定義されている場合は、コントロールの `allowedValues` で有効な `value` である必要があります。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "comboBox": {
      "type": "int",
      "defaultValue": 4
    }
  },
  "resources": [],
  "outputs": {
    "comboBoxOutput": {
      "type": "string",
      "value": "[parameters('combo')]"
    }
  }
}
```

次の例は、メイン テンプレートのパラメーター名がコントロールのパラメーター名と一致するため、**合格** します。 また、テンプレートのパラメーターの型は、コントロールの `allowedValues` に指定されている `defaultValue` を持つ `string` です。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "comboBox": {
      "type": "string",
      "defaultValue": "two"
    }
  },
  "resources": [],
  "outputs": {
    "comboBoxOutput": {
      "type": "string",
      "value": "[parameters('comboBox')]"
    }
  }
}
```

この例の _createUiDefinition.json_ ファイルは次のとおりです。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [],
    "steps": [
      {
        "name": "demoComboBox",
        "label": "demoComboBoxLabel",
        "elements": [
          {
            "name": "comboBox",
            "type": "Microsoft.Common.DropDown",
            "label": "Example drop down",
            "defaultValue": "Value two",
            "toolTip": "This is a tool tip",
            "constraints": {
              "allowedValues": [
                {
                  "label": "Value one",
                  "description": "The value to select for option 1.",
                  "value": "one"
                },
                {
                  "label": "Value two",
                  "description": "The value to select for option 2.",
                  "value": "two"
                }
              ],
              "required": true
            },
            "visible": true
          }
        ]
      }
    ],
    "outputs": {
      "comboBox": "[steps('demoComboBox').comboBox]"
    }
  }
}
```

## <a name="output-controls-must-exist"></a>出力コントロールが存在する必要がある

テスト名: **Controls In Outputs Must Exist (出力内のコントロールが存在する必要がある)**

`outputs` セクションで使用されているコントロールは _createUiDefinition.json_ 内の他の場所の要素に存在する必要があります。 `outputs` で参照される名前は、`basics[]` または `steps[]` で使用されている名前と一致する必要があります。

次の例は **不合格** になります。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {
        "name": "nameDoesNotMatchOutput",
        "type": "Microsoft.Common.DropDown",
        "label": "Example drop down",
        "toolTip": "This is a tool tip"
      }
    ],
    "steps": [],
    "outputs": {
      "comboBox": "[basics('comboBox')]"
    }
  }
}
```

次の例は **合格** します。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {
        "name": "comboBox",
        "type": "Microsoft.Common.DropDown",
        "label": "Example drop down",
        "toolTip": "This is a tool tip"
      }
    ],
    "steps": [],
    "outputs": {
      "comboBox": "[basics('comboBox')]"
    }
  }
}
```

## <a name="properties-must-include-values"></a>プロパティには値を含める必要がある

テスト名: **CreateUIDefinition Must Not Have Blanks (CreateUIDefinition に空白を含めることはできない)**

プロパティには値を含める必要があります。 必須のプロパティには有効な値を使用する必要があります。 空白である省略可能なプロパティは削除する必要があります。 このテストでは、空白 `"basics": []`、`"steps": []`、または `defaultValue` は許可されます。

次の例は、`label`、`placeholder`、および `toolTip` が空白であるため、**不合格** になります。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {
        "name": "comboBox",
        "type": "Microsoft.Common.DropDown",
        "label": "",
        "placeholder": "",
        "defaultValue": "",
        "toolTip": ""
      }
    ],
    "steps": [],
    "outputs": {
      "comboBox": "[basics('comboBox')]"
    }
  }
}
```

次の例は、`label` と `toolTip` に値があるため **合格** し、`placeholder` は削除されます。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {
        "name": "comboBox",
        "type": "Microsoft.Common.DropDown",
        "label": "Example drop down",
        "defaultValue": "",
        "toolTip": "This is a tool tip"
      }
    ],
    "steps": [],
    "outputs": {
      "comboBox": "[basics('comboBox')]"
    }
  }
}
```

## <a name="use-valid-schema-and-version"></a>有効なスキーマとバージョンを使用する

テスト名: **CreateUIDefinition Should Have Schema (CreateUIDefinition にはスキーマが必要)**

_createUiDefinition.json_ ファイルには、`$schema` プロパティを含み、有効な `$schema` と `version` を使用する必要があります。 また、`$schema` と `version` のバージョン番号が一致する必要があります。

次の例は **不合格** になります。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.9.9-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.9.9-preview"
}
```

次の例は、最新の `$schema` と `version` が使用されているので **合格** します。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview"
}
```

## <a name="dont-hide-credential-confirmation"></a>資格情報の確認入力を非表示にしない

テスト名: **Credential Confirmation Should Not Be Hidden (資格情報の確認入力を非表示にしない)**

このテストでは、[Microsoft.Common.PasswordBox](../managed-applications/microsoft-common-passwordbox.md) または [Microsoft.Compute.CredentialsCombo](../managed-applications/microsoft-compute-credentialscombo.md) の資格情報が確認入力されていることを確認します。 確認入力が表示されるように `hideConfirmation` プロパティを `false` に設定する必要があります。

次の例は、`hideConfirmation` が `true` であるため、**不合格** になります。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {
        "name": "credentials",
        "type": "Microsoft.Compute.CredentialsCombo",
        "label": {
          "password": "Password",
          "confirmPassword": "Confirm password"
        },
        "toolTip": {
          "password": "Type your credentials"
        },
        "constraints": {
          "required": true,
          "customPasswordRegex": "^(?=.*[A-Za-z])(?=.*\\d)[A-Za-z\\d]{12,}$",
          "customValidationMessage": "The password must be alphanumeric, contain at least 12 characters, and have at least 1 letter and 1 number."
        },
        "options": {
          "hideConfirmation": true
        },
        "osPlatform": "Windows",
        "visible": true
      }
    ],
    "steps": [],
    "outputs": {
      "location": "[location()]",
      "credentials": "[basics('credentials')]"
    }
  }
}
```

次の例は、`hideConfirmation` が `false` であるため、**合格** します。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {
        "name": "credentials",
        "type": "Microsoft.Compute.CredentialsCombo",
        "label": {
          "password": "Password",
          "confirmPassword": "Confirm password"
        },
        "toolTip": {
          "password": "Type your credentials"
        },
        "constraints": {
          "required": true,
          "customPasswordRegex": "^(?=.*[A-Za-z])(?=.*\\d)[A-Za-z\\d]{12,}$",
          "customValidationMessage": "The password must be alphanumeric, contain at least 12 characters, and have at least 1 letter and 1 number."
        },
        "options": {
          "hideConfirmation": false
        },
        "osPlatform": "Windows",
        "visible": true
      }
    ],
    "steps": [],
    "outputs": {
      "location": "[location()]",
      "credentials": "[basics('credentials')]"
    }
  }
}
```

## <a name="use-correct-handler"></a>正しいハンドラーを使用する

テスト名: **Handler Must Be Correct (ハンドラーは正しいものでなければならない)**

_createUiDefinition.json_ ファイルで `Microsoft.Azure.CreateUIDef` または `Microsoft.Compute.MultiVm` を使用します。

次の例は **不合格** になります。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.",
  "version": "0.1.2-preview"
}
```

次の例は **合格** します。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview"
}
```

## <a name="dont-hide-existing-resources"></a>既存のリソースを非表示にしない

テスト名: **HideExisting Must Be Correctly Handled (HideExisting を正しく処理する必要がある)**

`hideExisting` を `false` に設定するか省略する場合、`outputs` には `resourceGroup` と `newOrExisting` を含める必要があります。 `hideExisting` の既定値は `false` です。

`hideExisting` を含むコントロールの種類の例としては、[Microsoft.Storage.StorageAccountSelector](../managed-applications/microsoft-storage-storageaccountselector.md)、[Microsoft.Network.PublicIpAddressCombo](../managed-applications/microsoft-network-publicipaddresscombo.md)、[Microsoft.Network.VirtualNetworkCombo](../managed-applications/microsoft-network-virtualnetworkcombo.md) などがあります。

次の例は **不合格** になります。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {
        "name": "storage",
        "type": "Microsoft.Storage.StorageAccountSelector",
        "label": "Storage account",
        "toolTip": "This is a demo storage account",
        "defaultValue": {
          "name": "storageaccount01",
          "type": "Premium_LRS"
        },
        "options": {
          "hideExisting": false
        },
        "visible": true
      }
    ],
    "steps": [],
    "outputs": {
      "location": "[location()]"
    }
  }
}
```

次の例は **合格** します。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {
        "name": "storage",
        "type": "Microsoft.Storage.StorageAccountSelector",
        "label": "Storage account",
        "toolTip": "This is a demo storage account",
        "defaultValue": {
          "name": "storageaccount01",
          "type": "Premium_LRS"
        },
        "options": {
          "hideExisting": false
        },
        "visible": false
      }
    ],
    "steps": [],
    "outputs": {
      "location": "[location()]",
      "resourceGroup": "[basics('storage').resourceGroup]",
      "newOrExisting": "[basics('storage').newOrExisting]"
    }
  }
}
```

## <a name="use-location-in-outputs"></a>出力に位置情報を使用する

テスト名: **Location Should Be In Outputs (出力に場所を含める必要がある)**

`outputs` セクションには [location](../managed-applications/create-ui-definition-referencing-functions.md#location) 関数を使用して位置情報を含める必要があります。

`outputs` に位置情報が含まれていないため、次の例は **不合格** になります。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {
        "name": "comboBox",
        "type": "Microsoft.Common.DropDown",
        "label": "Example drop down",
        "toolTip": "This is a tool tip"
      }
    ],
    "steps": [],
    "outputs": {
      "comboBox": "[basics('comboBox')]"
    }
  }
}
```

次の例は **合格** します。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {
        "name": "comboBox",
        "type": "Microsoft.Common.DropDown",
        "label": "Example drop down",
        "toolTip": "This is a tool tip"
      }
    ],
    "steps": [],
    "outputs": {
      "comboBox": "[basics('comboBox')]",
      "location": "[location()]"
    }
  }
}
```

## <a name="include-control-outputs-in-template-parameters"></a>テンプレート パラメーターにコントロール出力を含める

テスト名: **Outputs Must Be Present In Template Parameters (テンプレート パラメーターに出力が存在する必要がある)**

このテストでは、_createUiDefinition.json_ に `outputs` セクションが含まれていることを確認します。 このテストでは、その `outputs` がメイン テンプレートの `parameters` セクションに定義されているかどうかも確認します。 _createUiDefinition.json_ とメイン テンプレートの間でパラメーターが名前でマップされるため、名前が一致している必要があります。

このテストでは、メイン テンプレートと _createUiDefinition.json_ ファイルを確認します。 メイン テンプレートの例の後に _createUiDefinition.json_ ファイルの例を示します。

メイン テンプレートに _createUiDefinition.json_ ファイルの `outputs` セクションの `comboBox` パラメーターが含まれていないため、次の例は **不合格** になります。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]"
    }
  },
  "resources": [],
  "outputs": {
    "location": {
      "type": "string",
      "value": "[parameters('location')]"
    }
  }
}
```

メイン テンプレートに `comboBox` パラメーターが含まれているため、次の例は **合格** します。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "comboBox": {
      "type": "string",
      "defaultValue": "two"
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]"
    }
  },
  "resources": [],
  "outputs": {
    "comboBox": {
      "type": "string",
      "value": "[parameters('comboBox')]"
    },
    "location": {
      "type": "string",
      "value": "[parameters('location')]"
    }
  }
}
```

この例の _createUiDefinition.json_ ファイルは次のとおりです。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {
        "name": "comboBox",
        "type": "Microsoft.Common.DropDown",
        "label": "Example drop down",
        "toolTip": "This is a tool tip"
      }
    ],
    "steps": [],
    "outputs": {
      "comboBox": "[basics('comboBox')]",
      "location": "[location()]"
    }
  }
}
```

## <a name="parameters-without-default-must-exist-in-outputs"></a>既定値のないパラメーターは出力に存在する必要がある

テスト名: **Parameters Without Default Must Exist In CreateUIDefinition (既定値のないパラメーターは CreateUIDefinition に存在する必要がある)**

メイン テンプレート内の既定値のないパラメーターは、_createUiDefinition.json_ ファイルの `outputs` セクションに存在する必要があります。

このテストでは、メイン テンプレートと _createUiDefinition.json_ ファイルを確認します。 コントロールの例の後に _azuredeploy.json_ ファイルの例を示します。

_createUiDefinition.json_ ファイルの `outputs` にメイン テンプレートのパラメーター `comboBox` が含まれていないため、次の例は **不合格** になります。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {
        "name": "comboBox",
        "type": "Microsoft.Common.DropDown",
        "label": "Example drop down",
        "toolTip": "This is a tool tip"
      }
    ],
    "steps": [],
    "outputs": {
      "location": "[location()]"
    }
  }
}
```

_createUiDefinition.json_ の `outputs` に `comboBox` が含まれているため、次の例は **合格** します。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {
        "name": "comboBox",
        "type": "Microsoft.Common.DropDown",
        "label": "Example drop down",
        "toolTip": "This is a tool tip"
      }
    ],
    "steps": [],
    "outputs": {
      "comboBox": "[basics('comboBox')]",
      "location": "[location()]"
    }
  }
}
```

この例の _azuredeploy.json_ ファイルです。 `comboBox` パラメーターには既定値がありません。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "comboBox": {
      "type": "string"
    },
    "location": {
      "type": "string"
    }
  },
  "resources": [],
  "outputs": {
    "comboBox": {
      "type": "string",
      "value": "[parameters('comboBox')]"
    },
    "location": {
      "type": "string",
      "value": "[parameters('location')]"
    }
  }
}
```

## <a name="use-secure-parameter-with-password-box"></a>パスワード ボックスにセキュリティで保護されたパラメーターを使用する

テスト名: **Password Textboxes Must Be Used For Password Parameters (パスワード パラメーターにはパスワード テキストボックスを使用する必要がある)**

このテストでは、[Microsoft.Common.PasswordBox](../managed-applications/microsoft-common-passwordbox.md) 要素がメイン テンプレートの `parameters` と _createUiDefinition.json_ `outputs` で定義されていることを確認します。 パスワード ボックスのメイン テンプレートのパラメーターの種類は `secureString` または `secureObject` にする必要があります。

このテストでは、メイン テンプレートと _createUiDefinition.json_ ファイルを確認します。 メイン テンプレートの例の後に _createUiDefinition.json_ ファイルの例を示します。

メイン テンプレートの `passwordBox` パラメーターが `string` であるため、次の例は **不合格** になります。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "passwordBox": {
      "type": "string"
    },
    "location": {
      "type": "string"
    }
  },
  "resources": [],
  "outputs": {
    "location": {
      "type": "string",
      "value": "[parameters('location')]"
    }
  }
}
```

メイン テンプレートの `passwordBox` パラメーターが `secureString` であるため、次の例は **合格** します。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "passwordBox": {
      "type": "secureString"
    },
    "location": {
      "type": "string"
    }
  },
  "resources": [],
  "outputs": {
    "location": {
      "type": "string",
      "value": "[parameters('location')]"
    }
  }
}
```

この例の _createUiDefinition.json_ ファイルは次のとおりです。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {
        "name": "passwordBox",
        "type": "Microsoft.Common.PasswordBox",
        "label": {
          "password": "Password",
          "confirmPassword": "Confirm password"
        },
        "toolTip": "Type a password"
      }
    ],
    "steps": [],
    "outputs": {
      "location": "[location()]",
      "passwordBox": "[basics('passwordBox')]"
    }
  }
}
```

## <a name="password-box-requires-minimum-length"></a>パスワード ボックスには最短の長さが必要

テスト名: **PasswordBoxes Must Have Min Length (PasswordBoxes に最短の長さが必要)**

このテストでは、[Microsoft.Common.PasswordBox](../managed-applications/microsoft-common-passwordbox.md) 要素に、12 文字以上を必要とする `regex` と共に `constraints` を使用していることを確認します。

次の例は、`constraints` がないため **不合格** になります。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {
        "name": "passwordBox",
        "type": "Microsoft.Common.PasswordBox",
        "label": {
          "password": "Password",
          "confirmPassword": "Confirm password"
        },
        "toolTip": "Type a password"
      }
    ],
    "steps": [],
    "outputs": {
      "location": "[location()]",
      "passwordBox": "[basics('passwordBox')]"
    }
  }
}
```

次の例は、`regex` に 12 文字以上が必要であるため、**合格** します。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {
        "name": "passwordBox",
        "type": "Microsoft.Common.PasswordBox",
        "label": {
          "password": "Password",
          "confirmPassword": "Confirm password"
        },
        "toolTip": "Type a password",
        "constraints": {
          "required": true,
          "regex": "^[a-zA-Z0-9]{12,}$",
          "validationMessage": "Password must be at least 12 characters long, contain only numbers and letters"
        }
      }
    ],
    "steps": [],
    "outputs": {
      "location": "[location()]",
      "passwordBox": "[basics('passwordBox')]"
    }
  }
}
```

## <a name="text-box-must-use-validation"></a>テキスト ボックスには検証を使用する必要がある

テスト名: **Textboxes Are Well Formed (テキストボックスは適切な形式である)**

テキスト ボックスに検証を使用して、`regex` と `message` を含む `constraints` を確認します。

次の例は **不合格** になります。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {
        "name": "textBox",
        "type": "Microsoft.Common.TextBox",
        "label": "Text box",
        "toolTip": "Type 1-30 alphanumeric characters",
        "placeholder": "Type your text here",
        "visible": true
      }
    ],
    "steps": [],
    "outputs": {
      "location": "[location()]",
      "textBox": "[basics('textBox')]"
    }
  }
}
```

次の例は **合格** します。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {
        "name": "textBox",
        "type": "Microsoft.Common.TextBox",
        "label": "Text box",
        "toolTip": "Type 1-30 alphanumeric characters",
        "placeholder": "Type your text here",
        "constraints": {
          "required": true,
          "validations": [
            {
              "regex": "^[a-z0-9A-Z]{1,30}$",
              "message": "Only 1-30 characters alphanumeric characters are allowed."
            }
          ]
        },
        "visible": true
      }
    ],
    "steps": [],
    "outputs": {
      "location": "[location()]",
      "textBox": "[basics('textBox')]"
    }
  }
}
```

## <a name="tooltip-must-exist-with-a-value"></a>toolTip には値が存在する必要がある

テスト名: **Tooltips Should Be Present (ヒントを提示する必要がある)**

このテストでは、`toolTip` プロパティが存在し、値が含まれていることを確認します。

次の例は **不合格** になります。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {
        "name": "comboBox",
        "type": "Microsoft.Common.DropDown",
        "label": "Example drop down",
        "toolTip": ""
      }
    ],
    "steps": [],
    "outputs": {
      "location": "[location()]",
      "comboBox": "[basics('comboBox')]"
    }
  }
}
```

次の例は **合格** します。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {
        "name": "comboBox",
        "type": "Microsoft.Common.DropDown",
        "label": "Example drop down",
        "toolTip": "This is a tool tip"
      }
    ],
    "steps": [],
    "outputs": {
      "location": "[location()]",
      "comboBox": "[basics('comboBox')]"
    }
  }
}
```

## <a name="dont-set-a-default-user-name"></a>既定のユーザー名を設定しない

テスト名: **Usernames Should Not Have A Default (ユーザー名に既定値を設定しない)**

このテストでは、[Microsoft.Compute.UserNameTextBox](../managed-applications/microsoft-compute-usernametextbox.md) に `defaultValue` が設定されているかどうかを確認します。

次の例は、`defaultValue` が指定されているため、**不合格** になります。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {
        "name": "userNameBox",
        "type": "Microsoft.Compute.UserNameTextBox",
        "label": "User name",
        "defaultValue": "admin",
        "toolTip": "Enter your user name",
        "osPlatform": "Windows"
      }
    ],
    "steps": [],
    "outputs": {
      "location": "[location()]",
      "userNameBox": "[basics('userNameBox')]"
    }
  }
}
```

次の例は **合格** します。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {
        "name": "userNameBox",
        "type": "Microsoft.Compute.UserNameTextBox",
        "label": "User name",
        "toolTip": "Enter your user name",
        "osPlatform": "Windows"
      }
    ],
    "steps": [],
    "outputs": {
      "location": "[location()]",
      "userNameBox": "[basics('userNameBox')]"
    }
  }
}
```

## <a name="use-message-with-validations"></a>検証にメッセージを使用する

テスト名: **Validations Must Have Message (検証にはメッセージが必要)**

このテストでは、_createUiDefinition.json_ のすべての `validations` に `message` が含まれていることを確認します。

次の例は、`regex` の検証に `message` が含まれていないため、**不合格** になります。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {
        "name": "textBox",
        "type": "Microsoft.Common.TextBox",
        "label": "Text box",
        "toolTip": "Type 1-30 alphanumeric characters",
        "placeholder": "Type your text here",
        "constraints": {
          "required": true,
          "validations": [
            {
              "regex": "^[a-z0-9A-Z]{1,30}$"
            }
          ]
        },
        "visible": true
      }
    ],
    "steps": [],
    "outputs": {
      "location": "[location()]",
      "textBox": "[basics('textBox')]"
    }
  }
}
```

次の例は **合格** します。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {
        "name": "textBox",
        "type": "Microsoft.Common.TextBox",
        "label": "Text box",
        "toolTip": "Type 1-30 alphanumeric characters",
        "placeholder": "Type your text here",
        "constraints": {
          "required": true,
          "validations": [
            {
              "regex": "^[a-z0-9A-Z]{1,30}$",
              "message": "Only 1-30 characters alphanumeric characters are allowed."
            }
          ]
        },
        "visible": true
      }
    ],
    "steps": [],
    "outputs": {
      "location": "[location()]",
      "textBox": "[basics('textBox')]"
    }
  }
}
```

## <a name="virtual-machine-sizes-must-match"></a>仮想マシンのサイズが一致する必要がある

テスト名: **VM Sizes Must Match Template (VM のサイズはテンプレートと一致する必要がある)**

このテストでは、[Microsoft.Compute.SizeSelector](../managed-applications/microsoft-compute-sizeselector.md) が _createUiDefinition.json_ の `outputs` とメイン テンプレートの `parameters` セクションにあることを確認します。 `defaultValue` を指定するメイン テンプレートのパラメーターは、コントロールの `allowedSizes` の値と一致する必要があります。

このテストでは、メイン テンプレートと _createUiDefinition.json_ ファイルを確認します。 メイン テンプレートの例の後に _createUiDefinition.json_ ファイルの例を示します。

次の例は、メイン テンプレートの `defaultValue` が `allowedSizes` の値と一致しないため、**不合格** になります。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string"
    },
    "vmSize": {
      "type": "string",
      "defaultValue": "Standard_D9"
    }
  },
  "resources": [],
  "outputs": {
    "location": {
      "type": "string",
      "value": "[parameters('location')]"
    },
    "vmSize": {
      "type": "string",
      "value": "[parameters('vmSize')]"
    }
  }
}
```

次の例は、メイン テンプレートの `defaultValue` が `allowedSizes` の値と一致するため、**合格** します。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string"
    },
    "vmSize": {
      "type": "string",
      "defaultValue": "Standard_D3"
    }
  },
  "resources": [],
  "outputs": {
    "location": {
      "type": "string",
      "value": "[parameters('location')]"
    },
    "vmSize": {
      "type": "string",
      "value": "[parameters('vmSize')]"
    }
  }
}
```

この例の _createUiDefinition.json_ ファイルは次のとおりです。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {
        "name": "vmSize",
        "type": "Microsoft.Compute.SizeSelector",
        "label": "VM Size",
        "toolTip": "Select a virtual machine size",
        "recommendedSizes": [
          "Standard_D1"
        ],
        "constraints": {
          "allowedSizes": [
            "Standard_D1",
            "Standard_D2",
            "Standard_D3"
          ]
        },
        "osPlatform": "Windows",
        "visible": true
      }
    ],
    "steps": [],
    "outputs": {
      "location": "[location()]",
      "vmSize": "[basics('vmSize')]"
    }
  }
}
```

## <a name="next-steps"></a>次のステップ

- Azure portal のユーザー インターフェイスを作成するには、「[Azure マネージド アプリケーションの作成エクスペリエンスのための CreateUiDefinition.json](../managed-applications/create-uidefinition-overview.md)」を参照してください。
- UI 定義の作成サンドボックスを使用するには、「[Azure Managed Applications 用のポータル インターフェイスをテストする](../managed-applications/test-createuidefinition.md)」を参照してください。
- UI コントロールの詳細については、「[CreateUiDefinition の要素](../managed-applications/create-uidefinition-elements.md)」と「[CreateUiDefinition 関数](../managed-applications/create-uidefinition-functions.md)」を参照してください。
- ARM テンプレート テストの詳細については、「[ARM テンプレートのテスト ケース](template-test-cases.md)」を参照してください。
