---
title: Bicep のパラメーター ファイルを作成する
description: Bicep ファイルのデプロイ中に値を渡すためのパラメーター ファイルを作成します
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 06/16/2021
ms.openlocfilehash: 4628b7d6a04bdec2a7ec4273536bf895dc23a5d9
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/17/2021
ms.locfileid: "112292233"
---
# <a name="create-bicep-parameter-file"></a>Bicep のパラメーター ファイルを作成する

スクリプト内のインライン値としてパラメーターを渡すのではなく、パラメーター値を含む JSON ファイルを使用できます。 この記事では、Bicep ファイルと共に使用するパラメーター ファイルを作成する方法について説明します。

## <a name="parameter-file"></a>パラメーター ファイル

パラメーター ファイルでは次の形式を使用します。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "<first-parameter-name>": {
      "value": "<first-value>"
    },
    "<second-parameter-name>": {
      "value": "<second-value>"
    }
  }
}
```

パラメーター ファイルではパラメーターの値がプレーンテキストとして格納されることに注意してください。 この方法は、リソース SKU など、機密性の高くない値に適しています。 プレーンテキストは、パスワードなどの機密性の高い値には適していません。 機密性の高い値を含むパラメーターを渡す必要がある場合は、キー コンテナーに値を格納します。 機密性の高い値は、パラメーター ファイルに追加するのではなく、[getSecret 関数](bicep-functions-resource.md#getsecret)を使用して取得します。 詳細については、「[Bicep デプロイ時に Azure Key Vault を使用して、セキュリティで保護されたパラメーター値を渡す](key-vault-parameter.md)」を参照してください。

## <a name="define-parameter-values"></a>パラメーター値を定義する

パラメーターの名前と値を定義する方法を決定するには、Bicep ファイルを開きます。 Bicep ファイルのパラメーター セクションを確認します。 次の例は、Bicep ファイルのパラメーターを示しています。

```bicep
@maxLength(11)
param storagePrefix string

@allowed([
  'Standard_LRS'
  'Standard_GRS'
  'Standard_ZRS'
  'Premium_LRS'
])
param storageAccountType string = 'Standard_LRS'
```

パラメーター ファイルで最初に注目する詳細は、各パラメーターの名前です。 パラメーター ファイル内のパラメーター名は、Bicep ファイル内のパラメーター名と一致する必要があります。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storagePrefix": {
    },
    "storageAccountType": {
    }
  }
}
```

パラメーターの型に注目します。 パラメーター ファイル内のパラメーターの型は、Bicep ファイルと同じ型を使用する必要があります。 この例では、両方のパラメーターの型が文字列です。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storagePrefix": {
      "value": ""
    },
    "storageAccountType": {
      "value": ""
    }
  }
}
```

Bicep ファイルで既定値が設定されているパラメーターを調べます。 パラメーターに既定値がある場合は、パラメーター ファイルで値を指定できますが、必須ではありません。 パラメーター ファイルの値によって、Bicep ファイルの既定値がオーバーライドされます。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storagePrefix": {
      "value": "" // This value must be provided.
    },
    "storageAccountType": {
      "value": "" // This value is optional. Bicep will use default value if not provided.
    }
  }
}
```

Bicep の許可されている値と、最大長などの制限を確認します。 これらの値は、パラメーターに指定できる値の範囲を規定します。 この例では、`storagePrefix` は最大 11 文字を指定でき、`storageAccountType` には許可される値を指定する必要があります。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storagePrefix": {
      "value": "storage"
    },
    "storageAccountType": {
      "value": "Standard_ZRS"
    }
  }
}
```

> [!NOTE]
> パラメーター ファイルには、Bicep で定義されているパラメーターの値のみを含めることができます。 パラメーター ファイルに、Bicep ファイルのパラメーターと一致しない余分なパラメーターが含まれている場合は、エラーが発生します。

## <a name="parameter-type-formats"></a>パラメーターの型の形式

次の例は、さまざまなパラメーターの型 (文字列、整数、ブール値、配列、およびオブジェクト) の形式を示しています。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "exampleString": {
      "value": "test string"
    },
    "exampleInt": {
      "value": 4
    },
    "exampleBool": {
      "value": true
    },
    "exampleArray": {
      "value": [
        "value 1",
        "value 2"
      ]
    },
    "exampleObject": {
      "value": {
        "property1": "value1",
        "property2": "value2"
      }
    }
  }
}
```

## <a name="deploy-bicep-file-with-parameter-file"></a>パラメーター ファイルを使用して Bicep ファイルをデプロイする

Azure CLI からは、`@` およびパラメーター ファイル名を使用してローカル パラメーター ファイルを渡します。 たとえば、「 `@storage.parameters.json` 」のように入力します。

```azurecli
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.bicep \
  --parameters @storage.parameters.json
```

詳細については、「[Bicep と Azure CLI を使用してリソースをデプロイする](./deploy-cli.md#parameters)」を参照してください。 _.bicep_ ファイルをデプロイするには、Azure CLI バージョン 2.20 以降が必要です。

Azure PowerShell からは、`TemplateParameterFile` パラメーターを使用してローカル パラメーター ファイルを渡します。

```azurepowershell
New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile C:\MyTemplates\storage.bicep `
  -TemplateParameterFile C:\MyTemplates\storage.parameters.json
```

詳細については、「[Bicep と Azure PowerShell を使用してリソースをデプロイする](./deploy-powershell.md#parameters)」を参照してください。 _.bicep_ ファイルをデプロイするには、Azure PowerShell バージョン 5.6.0 以降が必要です。

## <a name="file-name"></a>ファイル名

パラメーター ファイルの一般的な名前付け規則は、Bicep のファイル名に _parameters_ を含めることです。 たとえば、Bicep ファイルの名前が _azuredeploy.bicep_ の場合、パラメーター ファイルには _azuredeploy.parameters.json_ という名前を付けます。 この名前付け規則により、bicep ファイルとパラメーターの関連がわかります。

さまざまな環境にデプロイするには、複数のパラメーター ファイルを作成します。 パラメーター ファイルに名前を付けるときは、開発や運用など、その用途を明確にします。 たとえば、_azuredeploy.parameters-dev.json_ と _azuredeploy.parameters-prod.json_ を使用して、リソースをデプロイします。

## <a name="parameter-precedence"></a>パラメーターの優先順位

同じデプロイ操作で、インライン パラメーターとローカル パラメーター ファイルを使用することができます。 たとえば、一部の値をローカル パラメーター ファイルで指定し、その他の値をデプロイ中にインラインで追加します。 ローカル パラメーター ファイルとインラインの両方でパラメーターの値を指定すると、インラインの値が優先されます。

ファイルへの URI を提供することにより、外部パラメーター ファイルを使用することができます。 外部パラメーター ファイルを使用する場合、他の値をインラインまたはローカル ファイルから渡すことはできません。 すべてのインライン パラメーターは無視されます。 すべてのパラメーター値を外部ファイル内で指定します。

## <a name="parameter-name-conflicts"></a>パラメーター名の競合

PowerShell コマンドのパラメーターのいずれかと同じ名前のパラメーターが Bicep ファイルに含まれている場合、PowerShell では Bicep ファイル内のパラメーター名の後ろに `FromTemplate` という文字を付加します。 たとえば、Bicep ファイル内の `ResourceGroupName` という名前のパラメーターは、[New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) コマンドレットの `ResourceGroupName` パラメーターと競合します。 `ResourceGroupNameFromTemplate` の値を指定するように求められます。 この混乱を回避するには、デプロイ コマンドに使用されていないパラメーター名を使用してください。

## <a name="next-steps"></a>次のステップ

- Bicep ファイルでパラメーターを定義する方法の詳細については、「[Bicep のパラメーター](./parameters.md)」を参照してください。
- 機密性の高い値を取得する方法については、「[デプロイ時に Azure Key Vault を使用して、セキュリティで保護されたパラメーター値を渡す](./key-vault-parameter.md)」を参照してください。
