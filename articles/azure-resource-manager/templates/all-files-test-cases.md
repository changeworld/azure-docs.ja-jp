---
title: Azure Resource Manager テスト ツールキットのすべてのファイルのテスト ケース
description: Azure Resource Manager テンプレート テスト ツールキットですべてのファイルに対して実行されるテストについて説明します。
ms.topic: conceptual
ms.date: 07/16/2021
ms.author: tomfitz
author: tfitzmac
ms.openlocfilehash: 6af9a7c149aacbd50537086ae70a8d845604d2ef
ms.sourcegitcommit: e2fa73b682a30048907e2acb5c890495ad397bd3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/16/2021
ms.locfileid: "114393578"
---
# <a name="test-cases-for-all-files"></a>すべてのファイルのテスト ケース

この記事では、すべての JavaScript Object Notation (JSON) ファイルに対して[テンプレート テスト ツールキット](test-toolkit.md)で実行されるテストについて説明します。 例には、テストに **合格** する、または **不合格** になるテスト名とコード サンプルが含まれています。 テストを実行する方法、または特定のテストを実行する方法の詳細については、「[テスト パラメーター](test-toolkit.md#test-parameters)」を参照してください。

## <a name="use-valid-json-syntax"></a>有効な JSON 構文を使用する

テスト名: JSONFiles Should Be Valid (JSONFiles は有効である必要がある)

このテストは、すべての JSON ファイルに有効な構文が含まれていることを確認します。 たとえば、_azuredeploy.json_、_azuredeploy.parameters.json_、_createUiDefinition.json_ などのファイルです。 このテストが **不合格** になる場合、他のテストや JSON の解析でエラーまたは警告が表示されます。

### <a name="template-file-example"></a>テンプレート ファイルの例

_azuredeploy.json_ で `parameters`、`comboBox`、`location` に先頭の中かっこ (`{`) がないため、次の例は不合格になります。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters":
    "comboBox":
      "type": "string"
    },
    "location":
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

次の例は **合格** します。

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

### <a name="parameter-file-example"></a>パラメーター ファイルの例

_azuredeploy.parameters.json_ に `value` のないパラメーターが使用されているため、次の例は **不合格** になります。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "value":
    }
  }
}
```

次の例は **合格** します。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "value": "westus"
    }
  }
}
```

### <a name="createuidefintion-example"></a>CreateUiDefintion の例

_createUiDefinition.json_ の `outputs` セクションに先頭の中かっこ (`{`) がないため、次の例は **不合格** になります。

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
    "outputs":
      "comboBox": "[basics('comboBox')]",
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

## <a name="next-steps"></a>次のステップ

- テスト ツールキットについては、「[ARM テンプレート テスト ツールキットを使用する](test-toolkit.md)」を参照してください。
- ARM テンプレートのテストについては、「[ARM テンプレートのテスト ケース](template-test-cases.md)」を参照してください。
- パラメーター ファイルをテストするには、「[パラメーター ファイルのテスト ケース](parameters.md)」を参照してください。
- createUiDefinition のテストについては、「[createUiDefinition.json のテスト ケース](createUiDefinition-test-cases.md)」を参照してください
