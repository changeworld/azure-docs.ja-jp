---
title: テスト ツールキットのテンプレートのテスト ケース
description: Azure Resource Manager テンプレート テスト ツールキットで実行されるテンプレート テストについて説明します。
ms.topic: conceptual
ms.date: 07/30/2021
ms.author: tomfitz
author: tfitzmac
ms.openlocfilehash: 961a62ae45b423e2e4c2abf8a4e7d771c2775591
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121744901"
---
# <a name="test-cases-for-arm-templates"></a>ARM テンプレートのテスト ケース

この記事では、Azure Resource Manager テンプレート (ARM テンプレート) 用の [テンプレート テスト ツールキット](test-toolkit.md)を使用して実行されるテストについて説明します。 これには、テストに **合格** する、または **不合格** になる例と、各テストの名前が示されています。 テストを実行する方法、または特定のテストを実行する方法の詳細については、「[テスト パラメーター](test-toolkit.md#test-parameters)」を参照してください。

## <a name="use-correct-schema"></a>正しいスキーマを使用する

テスト名: **DeploymentTemplate Schema Is Correct** (DeploymentTemplate スキーマが正しい)

テンプレートでは、有効なスキーマの値を指定する必要があります。

次の例は、スキーマが無効であるため、**不合格** になります。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-01-01/deploymentTemplate.json#",
}
```

次の例では、スキーマ バージョン `2015-01-01` が非推奨であり、保守されていないため、**警告** が表示されます。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
}
```

次の例は、有効なスキーマを使用しているため、**合格** します。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
}
```

テンプレートの `schema` プロパティには、次のいずれかのスキーマが設定されている必要があります。

- `https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#`
- `https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#`
- `https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#`
- `https://schema.management.azure.com/schemas/2019-08-01/tenantDeploymentTemplate.json#`
- `https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json`

## <a name="declared-parameters-must-be-used"></a>宣言されたパラメーターが使用されている必要がある

テスト名: **Parameters Must Be Referenced** (パラメーターは参照されている必要がある)

このテストでは、テンプレートで使用されていないパラメーター、または有効な式で使用されていないパラメーターを検出します。

テンプレートの混乱を軽減するには、定義されていても使用されていないパラメーターを削除します。 未使用のパラメーターを削除すると、不要な値を指定する必要がないため、テンプレートのデプロイが簡素化されます。

次の例は、パラメーターを参照する式の先頭に角かっこ (`[`) がないため、**不合格** になります。

```json
"resources": [
  {
    "location": " parameters('location')]"
  }
]
```

次の例は、式が有効であるため、**合格** します。

```json
"resources": [
  {
    "location": "[parameters('location')]"
  }
]
```

## <a name="secure-parameters-cant-have-hard-coded-default"></a>セキュリティで保護されたパラメーターの既定値をハードコーディングしてはならない

テスト名: **Secure String Parameters Cannot Have Default** (セキュリティで保護された文字列パラメーターに既定値を設定してはならない)

テンプレートのセキュリティで保護されたパラメーターに、ハードコーディングされた既定値を指定してはいけません。 セキュリティで保護されたパラメーターには、既定値として空の文字列を指定するか、式で [newGuid](template-functions-string.md#newguid) 関数を使用することができます。

パスワードのような機密性の高い値を含むパラメーターでは、`secureString` または `secureObject` 型を使用します。 パラメーターでセキュリティ保護された型が使用されていると、そのパラメーターの値はログに記録されず、デプロイ履歴に格納されません。 このアクションにより、悪意のあるユーザーが機密値を検出できなくなります。

既定値を指定すると、テンプレートまたはデプロイ履歴にアクセスできるすべてのユーザーが、その値を検出できます。

次の例は **不合格** になります。

```json
"parameters": {
  "adminPassword": {
    "defaultValue": "HardcodedPassword",
    "type": "secureString"
  }
}
```

次の例は **合格** します。

```json
"parameters": {
  "adminPassword": {
    "type": "secureString"
  }
}
```

次の例は、`newGuid` 関数が使用されているため、**合格** します。

```json
"parameters": {
  "secureParameter": {
    "type": "secureString",
    "defaultValue": "[newGuid()]"
  }
}
```

## <a name="environment-urls-cant-be-hard-coded"></a>環境 URL をハードコーディングしてはならない

テスト名: **DeploymentTemplate Must Not Contain Hardcoded Uri** (DeploymentTemplate にはハードコーディングされた Uri を含めることができない)

テンプレート内に環境 URL をハードコーディングしないでください。 代わりに、[環境](template-functions-deployment.md#environment)関数を使用して、デプロイ時にこれらの URL を動的に取得します。 ブロックされている URL ホストの一覧については、[テスト ケース](https://github.com/Azure/arm-ttk/blob/master/arm-ttk/testcases/deploymentTemplate/DeploymentTemplate-Must-Not-Contain-Hardcoded-Uri.test.ps1)を参照してください。

次の例は、URL がハードコーディングされているため、**不合格** になります。

```json
"variables":{
  "AzureURL":"https://management.azure.com"
}
```

[concat](template-functions-string.md#concat) または [uri](template-functions-string.md#uri) と共に使用されているときも、テストは **不合格** になります。

```json
"variables":{
  "AzureSchemaURL1": "[concat('https://','gallery.azure.com')]",
  "AzureSchemaURL2": "[uri('gallery.azure.com','test')]"
}
```

次の例は **合格** します。

```json
"variables": {
  "AzureSchemaURL": "[environment().gallery]"
}
```

## <a name="location-uses-parameter"></a>場所にはパラメーターを使用する

テスト名: **Location Should Not Be Hardcoded** (場所をハードコーディングしてはいけない)

リソースの場所を設定するには、テンプレートに `location` という名前のパラメーターを指定し、その型を `string` に設定する必要があります。 メイン テンプレート (_azuredeploy.json_ または _mainTemplate.json_) では、このパラメーターにリソース グループの場所を既定で設定できます。 リンクまたは入れ子になったテンプレートの場合、location パラメーターに既定の場所を設定しないでください。

テンプレート ユーザーは、リソースを作成できるリージョンへのアクセスが制限される場合があります。 リソースの場所がハードコーディングされていると、ユーザーがリソースを作成できない恐れがあります。 ユーザーがアクセスできないリージョンにリソース グループが作成された場合、`"[resourceGroup().location]"` 式によってユーザーがブロックされることがあります。 ブロックされたユーザーは、テンプレートを使用できません。

既定のリソース グループの場所になる `location` パラメーターを指定することで、ユーザーは、必要に応じて既定値を使用できますが、別の場所を指定することもできます。

次の例は、リソースの `location` が `resourceGroup().location` に設定されているため、**不合格** になります。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2021-02-01",
      "name": "storageaccount1",
      "location": "[resourceGroup().location]",
      "kind": "StorageV2",
      "sku": {
        "name": "Premium_LRS",
        "tier": "Premium"
      }
    }
  ]
}
```

次の例は、`location` パラメーターを使用していますが、このパラメーターの既定値がハードコーディングされた場所であるため、**不合格** になります。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string",
      "defaultValue": "westus"
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2021-02-01",
      "name": "storageaccount1",
      "location": "[parameters('location')]",
      "kind": "StorageV2",
      "sku": {
        "name": "Premium_LRS",
        "tier": "Premium"
      }
    }
  ],
  "outputs": {}
}
```

次の例は、テンプレートがメイン テンプレートとして使用されているので、**合格** します。 既定値はリソース グループの場所であるが、ユーザーが別の値を指定できるパラメーターを作成してください。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "Location for the resources."
      }
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2021-02-01",
      "name": "storageaccount1",
      "location": "[parameters('location')]",
      "kind": "StorageV2",
      "sku": {
        "name": "Premium_LRS",
        "tier": "Premium"
      }
    }
  ],
  "outputs": {}
}
```

> [!NOTE]
> 前の例をリンクされたテンプレートとして使用した場合、テストは **不合格** になります。 リンクされたテンプレートとして使用する場合は、既定値を削除してください。

## <a name="resources-should-have-location"></a>リソースには場所が必要である

テスト名: **Resources Should Have Location** (リソースには場所が必要である)

リソースの場所は、[テンプレート式](template-expressions.md)または `global` に設定する必要があります。 テンプレート式では、通常、「[場所にはパラメーターを使用する](#location-uses-parameter)」で説明した `location` パラメーターが使用されます。

次の例は、`location` が式でも `global` でもないため、**不合格** になります。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "functions": [],
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2021-02-01",
      "name": "storageaccount1",
      "location": "westus",
      "kind": "StorageV2",
      "sku": {
        "name": "Premium_LRS",
        "tier": "Premium"
      }
    }
  ],
  "outputs": {}
}
```

次の例は、リソースの `location` が `global` に設定されているため、**合格** します。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "functions": [],
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2021-02-01",
      "name": "storageaccount1",
      "location": "global",
      "kind": "StorageV2",
      "sku": {
        "name": "Premium_LRS",
        "tier": "Premium"
      }
    }
  ],
  "outputs": {}
}

```

次の例は、`location` パラメーターが式を使用しているため、これも **合格** します。 リソース `location` では、式の値が使用されます。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "Location for the resources."
      }
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2021-02-01",
      "name": "storageaccount1",
      "location": "[parameters('location')]",
      "kind": "StorageV2",
      "sku": {
        "name": "Premium_LRS",
        "tier": "Premium"
      }
    }
  ],
  "outputs": {}
}
```

## <a name="vm-size-uses-parameter"></a>VM サイズでパラメーターを使用する

テスト名: **VM Size Should Be A Parameter** (VM サイズはパラメーターにする必要がある)

`hardwareProfile` オブジェクトの `vmSize` をハードコーディングしないでください。 `hardwareProfile` を省略するか、ハードコーディングされた値がそれに含まれている場合、テストで不合格になります。 テンプレートのユーザーがデプロイされた仮想マシンのサイズを変更できるように、パラメーターを指定します。 詳細については、[「Microsoft.Compute virtualMachines](/azure/templates/microsoft.compute/virtualmachines)」を参照してください。

次の例は、`hardwareProfile` オブジェクトの `vmSize` がハードコーディングされた値のため、**不合格** になります。

```json
"resources": [
  {
    "type": "Microsoft.Compute/virtualMachines",
    "apiVersion": "2020-12-01",
    "name": "demoVM",
    "location": "[parameters('location')]",
    "properties": {
      "hardwareProfile": {
        "vmSize": "Standard_D2_v3"
      }
    }
  }
]
```

この例は、`vmSize` の値がパラメーターで指定されているので、**合格** します。

```json
"parameters": {
  "vmSizeParameter": {
    "type": "string",
    "defaultValue": "Standard_D2_v3",
    "metadata": {
      "description": "Size for the virtual machine."
    }
  }
}
```

次に、パラメーターの値を参照するために、`hardwareProfile` で `vmSize` に対して式を使用します。

```json
"resources": [
  {
    "type": "Microsoft.Compute/virtualMachines",
    "apiVersion": "2020-12-01",
    "name": "demoVM",
    "location": "[parameters('location')]",
    "properties": {
      "hardwareProfile": {
        "vmSize": "[parameters('vmSizeParameter')]"
      }
    }
  }
]
```

## <a name="min-and-max-values-are-numbers"></a>最小値と最大値が数値である

テスト名: **Min And Max Value Are Numbers** (最小値と最大値が数値である)

`minValue` と `maxValue` を使用してパラメーターを定義する場合は、それらを数値として指定します。 `minValue` と `maxValue` をペアとして使用する必要があります。そうしない場合、テストで不合格になります。

次の例は、`minValue` と `maxValue` が文字列であるため、**不合格** になります。

```json
"exampleParameter": {
  "type": "int",
  "minValue": "0",
  "maxValue": "10"
}
```

次の例は、`minValue` のみが使用されているため、**不合格** になります。

```json
"exampleParameter": {
  "type": "int",
  "minValue": 0
}
```

次の例は、`minValue` と `maxValue` が数値であるため、**合格** します。

```json
"exampleParameter": {
  "type": "int",
  "minValue": 0,
  "maxValue": 10
}
```

## <a name="artifacts-parameter-defined-correctly"></a>成果物パラメーターが正しく定義されている

テスト名: **artifacts parameter** (成果物パラメーター)

`_artifactsLocation` と `_artifactsLocationSasToken` のパラメーターを指定する場合は、正しい既定値と型を使用します。 このテストに合格するには、次の条件が満たされている必要があります。

- 一方のパラメーターを指定する場合は、他方も指定する必要があります。
- `_artifactsLocation` は `string` である必要があります。
- `_artifactsLocation` の既定値は、メイン テンプレートで指定されている必要があります。
- `_artifactsLocation` の既定値を、入れ子になったテンプレートで指定することはできません。
- `_artifactsLocation` には、`"[deployment().properties.templateLink.uri]"` またはその既定値に対する生リポジトリ URL のいずれかが必要です。
- `_artifactsLocationSasToken` は `secureString` である必要があります。
- `_artifactsLocationSasToken` の既定値には空の文字列のみを指定できます。
- `_artifactsLocationSasToken` の既定値を、入れ子になったテンプレートで指定することはできません。

## <a name="declared-variables-must-be-used"></a>宣言された変数が使用されている必要がある

テスト名: **Variables Must Be Referenced** (変数は参照されている必要がある)

このテストでは、テンプレートで使用されていないか、有効な式で使用されていない変数を検出します。 テンプレートの混乱を軽減するには、定義されていても使用されていない変数を削除します。

`copy` 要素を使用して値を反復処理する変数は、参照される必要があります。 詳細については、「[ARM テンプレートでの変数の反復処理](copy-variables.md)」を参照してください。

次の例は、`copy` 要素を使用する変数が参照されていないため、**不合格** になります。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "itemCount": {
      "type": "int",
      "defaultValue": 5
    }
  },
  "variables": {
    "copy": [
      {
        "name": "stringArray",
        "count": "[parameters('itemCount')]",
        "input": "[concat('item', copyIndex('stringArray', 1))]"
      }
    ]
  },
  "resources": [],
  "outputs": {}
}
```

次の例は、変数を参照する式の先頭に角かっこ (`[`) がないため、**不合格** になります。

```json
"outputs": {
  "outputVariable": {
    "type": "string",
    "value": " variables('varExample')]"
  }
}
```

次の例は、変数が `outputs` 内で参照されているため、**合格** します。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "itemCount": {
      "type": "int",
      "defaultValue": 5
    }
  },
  "variables": {
    "copy": [
      {
        "name": "stringArray",
        "count": "[parameters('itemCount')]",
        "input": "[concat('item', copyIndex('stringArray', 1))]"
      }
    ]
  },
  "resources": [],
  "outputs": {
    "arrayResult": {
      "type": "array",
      "value": "[variables('stringArray')]"
    }
  }
}
```

次の例は、式が有効であるため、**合格** します。

```json
"outputs": {
  "outputVariable": {
    "type": "string",
    "value": "[variables('varExample')]"
  }
}
```

## <a name="dynamic-variable-should-not-use-concat"></a>動的変数で concat を使用してはならない

テスト名: **Dynamic Variable References Should Not Use Concat** (動的変数の参照で concat を使用してはならない)

場合によっては、別の変数またはパラメーターの値に基づいて変数を動的に構築する必要があります。 値を設定するときは、[concat](template-functions-string.md#concat) 関数を使用しないでください。 代わりに、使用可能なオプションが含まれるオブジェクトを使用し、デプロイの間にオブジェクトからプロパティの 1 つを動的に取得します。

次の例は **合格** します。 `currentImage` 変数は、デプロイ時に動的に設定されます。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "osType": {
      "type": "string",
      "allowedValues": [
        "Windows",
        "Linux"
      ]
    }
  },
  "variables": {
    "imageOS": {
      "Windows": {
        "image": "Windows Image"
      },
      "Linux": {
        "image": "Linux Image"
      }
    },
    "currentImage": "[variables('imageOS')[parameters('osType')].image]"
  },
  "resources": [],
  "outputs": {
    "result": {
      "type": "string",
      "value": "[variables('currentImage')]"
    }
  }
}
```

## <a name="use-recent-api-version"></a>最新のバージョンの API を使用する

テスト名: **apiVersions Should Be Recent** (apiVersions は最新でなければならない)

各リソースの API バージョンでは、文字列としてハードコーディングされた最新バージョンを使用する必要があります。 このテストでは、テンプレートの API バージョンが、ツールキットのキャッシュ内のリソース プロバイダーのバージョンに対して評価されます。 テストが実行されてから 2 年未満の API バージョンは、最新と見なされます。 より新しいバージョンが使用可能な場合には、プレビュー バージョンを使用しないでください。

API バージョンが検出されなかったことを示す警告は、ツールキットのキャッシュにバージョンが含まれていないことだけを示しています。 最新バージョンの API (推奨) を使用すると、警告が生成されることがあります。

[ツールキットのキャッシュ](https://github.com/Azure/arm-ttk/tree/master/arm-ttk/cache)の詳細について確認してください。

次の例は、API バージョンが 2 年以上経っているため、**不合格** になります。

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2019-06-01",
    "name": "storageaccount1",
    "location": "[parameters('location')]"
  }
]
```

次の例は、新しいバージョンが使用できるときにプレビュー バージョンが使用されているため、**不合格** になります。

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2020-08-01-preview",
    "name": "storageaccount1",
    "location": "[parameters('location')]"
  }
]
```

次の例は、プレビュー バージョンではない最新のバージョンであるため、**合格** します。

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2021-02-01",
    "name": "storageaccount1",
    "location": "[parameters('location')]"
  }
]
```

## <a name="use-hard-coded-api-version"></a>ハードコーディングされた API バージョンを使用する

テスト名: **Providers apiVersions Is Not Permitted** (プロバイダーの apiVersions を使用してはならない)

リソースの種類の API バージョンによって、使用できるプロパティが決まります。 テンプレートではハードコーディングされた API バージョンを指定します。 使用できるプロパティがわからなくなるため、デプロイ時に決定される API バージョンを取得しないでください。

次の例は **不合格** になります。

```json
"resources": [
  {
    "type": "Microsoft.Compute/virtualMachines",
    "apiVersion": "[providers('Microsoft.Compute', 'virtualMachines').apiVersions[0]]",
    ...
  }
]
```

次の例は **合格** します。

```json
"resources": [
  {
    "type": "Microsoft.Compute/virtualMachines",
    "apiVersion": "2020-12-01",
    ...
  }
]
```

## <a name="properties-cant-be-empty"></a>プロパティを空にすることはできない

テスト名: **Template Should Not Contain Blanks** (テンプレートに空白が含まれていてはならない)

プロパティを空の値にハードコーディングしないでください。 空の値には、null および空の文字列、オブジェクト、配列が含まれます。 プロパティを空の値に設定した場合は、テンプレートからそのプロパティを削除します。 パラメーターを使用するなどして、デプロイ時にプロパティを空の値に設定できます。

[入れ子になったテンプレート](linked-templates.md#nested-template)の `template` プロパティには、空のプロパティを含めることができます。 入れ子になったテンプレートの詳細については、「[Microsoft.Resources のデプロイ](/azure/templates/microsoft.resources/deployments)」を参照してください。

次の例は、空のプロパティがあるため、**不合格** になります。

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2021-01-01",
    "name": "storageaccount1",
    "location": "[parameters('location')]",
    "sku": {},
    "kind": ""
  }
]
```

次の例は、プロパティに値が含まれているため、**合格** します。

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2021-01-01",
    "name": "storageaccount1",
    "location": "[parameters('location')]",
    "sku": {
      "name": "Standard_LRS",
      "tier": "Standard"
    },
    "kind": "Storage"
  }
]
```

## <a name="use-resource-id-functions"></a>リソース ID 関数を使用する

テスト名: **IDs Should Be Derived From ResourceIDs** (ID は ResourceID から派生する必要がある)

リソース ID を指定するときは、リソース ID 関数のいずれかを使用します。 使用できる関数は次のとおりです。

- [resourceId](template-functions-resource.md#resourceid)
- [subscriptionResourceId](template-functions-resource.md#subscriptionresourceid)
- [tenantResourceId](template-functions-resource.md#tenantresourceid)
- [extensionResourceId](template-functions-resource.md#extensionresourceid)

concat 関数を使用してリソース ID を作成しないでください。 次の例は **不合格** になります。

```json
"networkSecurityGroup": {
    "id": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/networkSecurityGroups/', variables('networkSecurityGroupName'))]"
}
```

次の例は **合格** します。

```json
"networkSecurityGroup": {
    "id": "[resourceId('Microsoft.Network/networkSecurityGroups', variables('networkSecurityGroupName'))]"
}
```

## <a name="resourceid-function-has-correct-parameters"></a>ResourceId 関数のパラメーターが正しい

テスト名: **ResourceIds should not contain** (ResourceId に含まれていてはならない)

リソース ID を生成するときは、省略可能なパラメーターに不要な関数を使用しないでください。 既定の [resourceId](template-functions-resource.md#resourceid) 関数では、現在のサブスクリプションとリソース グループが使用されます。 それらの値を指定する必要はありません。

次の例は、現在のサブスクリプション ID とリソース グループ名を指定する必要がないため、**不合格** になります。

```json
"networkSecurityGroup": {
    "id": "[resourceId(subscription().subscriptionId, resourceGroup().name, 'Microsoft.Network/networkSecurityGroups', variables('networkSecurityGroupName'))]"
}
```

次の例は **合格** します。

```json
"networkSecurityGroup": {
    "id": "[resourceId('Microsoft.Network/networkSecurityGroups', variables('networkSecurityGroupName'))]"
}
```

このテストは以下に適用されます。

- [resourceId](template-functions-resource.md#resourceid)
- [subscriptionResourceId](template-functions-resource.md#subscriptionresourceid)
- [tenantResourceId](template-functions-resource.md#tenantresourceid)
- [extensionResourceId](template-functions-resource.md#extensionresourceid)
- [reference](template-functions-resource.md#reference)
- [list*](template-functions-resource.md#list)

`reference` と `list*` は、`concat` を使用してリソース ID を作成すると、テストで **不合格** になります。

## <a name="dependson-best-practices"></a>dependsOn ベスト プラクティス

テスト名: **DependsOn Best Practices** (DependsOn ベスト プラクティス)

デプロイの依存関係を設定するときに、[if](template-functions-logical.md#if) 関数を使用して条件をテストしないでください。 あるリソースが[条件付きでデプロイされる](conditional-resource-deployment.md)リソースに依存している場合は、任意のリソースと同様に依存関係を設定します。 条件付きリソースがデプロイされていない場合、Azure Resource Manager によって必要な依存関係からそれが自動的に削除されます。

`dependsOn` 要素は、[concat](template-functions-array.md#concat) 関数から始めることはできません。

次の例は、`if` 関数が含まれているため、**不合格** になります。

```json
"dependsOn": [
  "[if(equals(parameters('newOrExisting'),'new'), variables('storageAccountName'), '')]"
]
```

次の例は、`concat` から始まるため、**不合格** になります。

```json
"dependsOn": [
  "[concat(variables('storageAccountName'))]"
]
```

次の例は **合格** します。

```json
"dependsOn": [
  "[variables('storageAccountName')]"
]
```

## <a name="nested-or-linked-deployments-cant-use-debug"></a>入れ子になったデプロイまたはリンクされたデプロイではデバッグを使用できない

テスト名: **Deployment Resources Must Not Be Debug** (デプロイ リソースをデバッグしてはならない)

リソースの種類 `Microsoft.Resources/deployments` を使用して[入れ子になった、またはリンクされたテンプレート](linked-templates.md)を定義する場合、[デバッグ](/azure/templates/microsoft.resources/deployments#debugsetting-object)を有効にすることができます。 デバッグは、テンプレートをテストする必要があり、機密情報を公開できる場合に使用します。 テンプレートを運用環境で使用する前に、デバッグを無効にしてください。 `debugSetting` オブジェクトを削除するか、`detailLevel` プロパティを `none` に変更できます。

次の例は **不合格** になります。

```json
"debugSetting": {
  "detailLevel": "requestContent"
}
```

次の例は **合格** します。

```json
"debugSetting": {
  "detailLevel": "none"
}
```

## <a name="admin-user-names-cant-be-literal-value"></a>管理者ユーザー名をリテラル値にすることはできない

テスト名: **adminUsername Should Not Be A Literal** (adminUsername をリテラルにしてはならない)

`adminUserName` を設定するときは、リテラル値を使用しないでください。 ユーザー名のパラメーターを作成し、式を使用してパラメーターの値を参照します。

次の例は、リテラル値を使用しているため、**不合格** になります。

```json
"osProfile":  {
  "adminUserName": "myAdmin"
}
```

次の例は、式を使用しているため、**合格** します。

```json
"osProfile": {
  "adminUsername": "[parameters('adminUsername')]"
}
```

## <a name="use-latest-vm-image"></a>最新の VM イメージを使用する

テスト名: **VM Images Should Use Latest Version** (VM イメージでは最新バージョンを使用しなければならない)

このテストは無効になっていますが、出力ではそれに合格したことが示されます。 次の基準についてテンプレートを確認することをお勧めします。

テンプレートにイメージを含む仮想マシンが含まれている場合は、イメージの最新バージョンが使用されていることを確認します。

## <a name="use-stable-vm-images"></a>安定した VM イメージを使用する

テスト名: **Virtual Machines Should Not Be Preview** (仮想マシンはプレビューにしない)

仮想マシンではプレビュー イメージを使用しないでください。 テストでは、`storageProfile` を確認して、`imageReference` で _preview_ を含む文字列が使用されていないことを検証します。 さらに、_preview_ が `imageReference` のプロパティ `offer`、`sku`、または `version` で使用されていないことを検証します。

`imageReference` プロパティの詳細については、「[Microsoft.Compute virtualMachines](/azure/templates/microsoft.compute/virtualmachines#imagereference-object)」と「[Microsoft.Compute virtualMachineScaleSets](/azure/templates/microsoft.compute/virtualmachinescalesets#imagereference-object)」を参照してください。

次の例は、`imageReference` が _preview_ を含む文字列であるため、**不合格** になります。

```json
"properties": {
  "storageProfile": {
    "imageReference": "latest-preview"
  }
}
```

次の例は、_preview_ が `offer`、`sku`、または `version` で使用されているため、**不合格** になります。

```json
"properties": {
  "storageProfile": {
    "imageReference": {
      "publisher": "Canonical",
      "offer": "UbuntuServer_preview",
      "sku": "16.04-LTS-preview",
      "version": "preview"
    }
  }
}
```

次の例は **合格** します。

```json
"storageProfile": {
  "imageReference": {
    "publisher": "Canonical",
    "offer": "UbuntuServer",
    "sku": "16.04-LTS",
    "version": "latest"
  }
}
```

## <a name="dont-use-managedidentity-extension"></a>ManagedIdentity 拡張機能を使用してはならない

テスト名: **ManagedIdentityExtension must not be used** (ManagedIdentityExtension を使用してはならない)

`ManagedIdentity` 拡張機能を仮想マシンに適用しないでください。 この拡張機能は 2019 年に非推奨とされたため、今後は使用しないでください。

## <a name="outputs-cant-include-secrets"></a>出力にシークレットを含めることはできない

テスト名: **Outputs Must Not Contain Secrets** (出力にシークレットが含まれていてはならない)

`outputs` セクションには、シークレットが公開される可能性のある値を含めないでください。 たとえば、`secureString` または `secureObject` 型のセキュリティで保護されたパラメーター、または [list*](template-functions-resource.md#list) 関数 (`listKeys` など) です。

テンプレートからの出力はデプロイ履歴に格納されるため、悪意のあるユーザーがその情報を見つける可能性があります。

次の例は、セキュリティで保護されたパラメーターが出力値に含まれるため、**不合格** になります。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "secureParam": {
      "type": "secureString"
    }
  },
  "functions": [],
  "variables": {},
  "resources": [],
  "outputs": {
    "badResult": {
      "type": "string",
      "value": "[concat('this is the value ', parameters('secureParam'))]"
    }
  }
}
```

次の例は、出力で [list*](template-functions-resource.md#list) 関数が使用されているため、**不合格** になります。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageName": {
      "type": "string"
    }
  },
  "functions": [],
  "variables": {},
  "resources": [],
  "outputs": {
    "badResult": {
      "type": "object",
      "value": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('storageName')), '2021-02-01')]"
    }
  }
}
```

## <a name="use-protectedsettings-for-commandtoexecute-secrets"></a>commandToExecute のシークレットに protectedSettings を使用する

テスト名: **CommandToExecute Must Use ProtectedSettings For Secrets** (CommandToExecute ではシークレットに ProtectedSettings を使用する必要がある)

種類が `CustomScript` のリソースの場合、`commandToExecute` にパスワードなどのシークレット データが含まれるときは、暗号化された `protectedSettings` を使用します。 たとえば、シークレット データは、型 `secureString` または `secureObject` のセキュリティで保護されたパラメーター、[list*](template-functions-resource.md#list) 関数 (`listKeys` など)、またはカスタム スクリプトで使用できます。

`settings` オブジェクトではクリア テキストが使用されているため、シークレット データを使用しないでください。 詳細については、[「Microsoft.Compute virtualMachines/extensions](/azure/templates/microsoft.compute/virtualmachines/extensions)」、[Windows](
/azure/virtual-machines/extensions/custom-script-windows) または [Linux](../../virtual-machines/extensions/custom-script-linux.md) に関する記事を参照してください。

次の例は、`settings` で `commandToExecute` をセキュリティで保護されたパラメーターと共に使用しているため、**不合格** になります。

```json
"parameters": {
  "adminPassword": {
    "type": "secureString"
  }
}
...
"properties": {
  "type": "CustomScript",
  "settings": {
    "commandToExecute": "[parameters('adminPassword')]"
  }
}
```

次の例は、`settings` で `commandToExecute` を `listKeys` 関数と共に使用しているため、**不合格** になります。

```json
"properties": {
  "type": "CustomScript",
  "settings": {
    "commandToExecute": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('storageName')), '2021-02-01')]"
  }
}
```

次の例は、`protectedSettings` で `commandToExecute` をセキュリティで保護されたパラメーターと共に使用しているため、**合格** します。

```json
"parameters": {
  "adminPassword": {
    "type": "secureString"
  }
}
...
"properties": {
  "type": "CustomScript",
  "protectedSettings": {
    "commandToExecute": "[parameters('adminPassword')]"
  }
}
```

次の例は、`protectedSettings` で `commandToExecute` を `listKeys` 関数と共に使用しているため、**合格** します。

```json
"properties": {
  "type": "CustomScript",
  "protectedSettings": {
    "commandToExecute": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('storageName')), '2021-02-01')]"
  }
}
```

## <a name="use-recent-api-versions-in-reference-functions"></a>reference 関数で最新の API バージョンを使用する

テスト名: **apiVersions Should Be Recent In Reference Functions** (reference 関数で apiVersions は最新でなければならない)

[reference](template-functions-resource.md#reference) 関数で使用されている API バージョンは、プレビュー バージョンではない最新である必要があります。 このテストでは、テンプレートの API バージョンが、ツールキットのキャッシュ内のリソース プロバイダーのバージョンに対して評価されます。 テストが実行されてから 2 年未満の API バージョンは、最新と見なされます。

API バージョンが検出されなかったことを示す警告は、ツールキットのキャッシュにバージョンが含まれていないことだけを示しています。 最新バージョンの API (推奨) を使用すると、警告が生成されることがあります。

[ツールキットのキャッシュ](https://github.com/Azure/arm-ttk/tree/master/arm-ttk/cache)の詳細について確認してください。

次の例は、API バージョンが 2 年以上経っているため、**不合格** になります。

```json
"outputs": {
  "stgAcct": {
    "type": "string",
    "value": "[reference(resourceId(parameters('storageResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('storageAccountName')), '2019-06-01')]"
  }
}
```

次の例は、API バージョンがプレビュー バージョンであるため、**不合格** になります。

```json
"outputs": {
  "stgAcct": {
    "type": "string",
    "value": "[reference(resourceId(parameters('storageResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('storageAccountName')), '2020-08-01-preview')]"
  }
}
```

次の例では、API バージョンが 2 年未満であり、プレビュー バージョンではないため、**合格** します。

```json
"outputs": {
  "stgAcct": {
    "type": "string",
    "value": "[reference(resourceId(parameters('storageResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('storageAccountName')), '2021-02-01')]"
  }
}
```

## <a name="use-type-and-name-in-resourceid-functions"></a>resourceId 関数で種類と名前を使用する

テスト名: **Resources Should Not Be Ambiguous** (リソースはあいまいにしない)

このテストは無効になっていますが、出力ではそれに合格したことが示されます。 次の基準についてテンプレートを確認することをお勧めします。

[resourceId](template-functions-resource.md#resourceid) には、リソースの種類とリソース名が含まれている必要があります。 このテストでは、テンプレートのすべての `resourceId` 関数を検出し、リソースが正しい構文でテンプレート内で使用されていることを確認します。 そうでない場合、この関数はあいまいであると見なされます。

たとえば、次の場合に、`resourceId` 関数はあいまいであると見なされます。

- テンプレート内にリソースが見つからず、リソース グループが指定されていない場合。
- リソースに条件が含まれていて、リソース グループが指定されていない場合。
- 関連リソースに、名前セグメントのすべてではなく、一部が含まれている場合。 たとえば、子リソースに複数の名前セグメントが含まれている場合などです。 詳細については、[resourceId の解説](template-functions-resource.md#remarks-3)に関する記事を参照してください。

## <a name="use-inner-scope-for-nested-deployment-secure-parameters"></a>入れ子になったデプロイのセキュリティで保護されたパラメーターに inner スコープを使用する

テスト名: **Secure Params In Nested Deployments** (入れ子になったデプロイのセキュリティで保護されたパラメーター)

入れ子になったテンプレートの `expressionEvaluationOptions` オブジェクトを `inner` スコープと共に使用して、型 `secureString` または `secureObject` のセキュリティで保護されたパラメーター、または [list*](template-functions-resource.md#list) 関数 (`listKeys` など) を含む式を評価します。 `outer` スコープが使用されている場合、式は親テンプレートのスコープ内でクリア テキストで評価されます。 そうすると、セキュリティで保護された値は、デプロイ履歴にアクセスできるすべてのユーザーに表示されます。 `expressionEvaluationOptions` の既定値は `outer`です。

入れ子になったテンプレートの詳細については、「[Microsoft.Resources のデプロイ](/azure/templates/microsoft.resources/deployments)」と「[入れ子になったテンプレートでの式の評価のスコープ](linked-templates.md#expression-evaluation-scope-in-nested-templates)」を参照してください。

次の例は、セキュリティで保護されたパラメーターまたは `list*` 関数を評価するために `expressionEvaluationOptions` で `outer` スコープが使用されているため、**不合格** になります。

```json
"resources": [
  {
    "type": "Microsoft.Resources/deployments",
    "apiVersion": "2021-04-01",
    "name": "nestedTemplate",
    "properties": {
      "expressionEvaluationOptions": {
        "scope": "outer"
      }
    }
  }
]
```

次の例は、セキュリティで保護されたパラメーターまたは `list*` 関数を評価するために `expressionEvaluationOptions` で `inner` スコープが使用されているため、**合格** します。

```json
"resources": [
  {
    "type": "Microsoft.Resources/deployments",
    "apiVersion": "2021-04-01",
    "name": "nestedTemplate",
    "properties": {
      "expressionEvaluationOptions": {
        "scope": "inner"
      }
    }
  }
]
```

## <a name="next-steps"></a>次のステップ

- テスト ツールキットの実行については、「[ARM テンプレート テスト ツールキットを使用する](test-toolkit.md)」を参照してください。
- テスト ツールキットの使用に関して説明している Microsoft Learn モジュールについては、「[what-if と ARM テンプレート テスト ツールキットを使用して変更をプレビューし、Azure リソースを検証する](/learn/modules/arm-template-test/)」をご覧ください。
- パラメーター ファイルをテストするには、[パラメーター ファイルのテスト ケース](parameters.md)に関する記事を参照してください。
- createUiDefinition のテストについては、「[createUiDefinition.json のテスト ケース](createUiDefinition-test-cases.md)」を参照してください。
- すべてのファイルのテストについては、「[すべてのファイルのテスト ケース](all-files-test-cases.md)」を参照してください。
