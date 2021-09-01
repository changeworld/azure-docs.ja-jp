---
title: Azure Resource Manager テスト ツールキットのパラメーター ファイルのテスト ケース
description: Azure Resource Manager テンプレート テスト ツールキットによって実行されるパラメーター ファイル テストについて説明します。
ms.topic: conceptual
ms.date: 07/16/2021
ms.author: tomfitz
author: tfitzmac
ms.openlocfilehash: 37fefd84a6385e003a4bb501a789003e8c63d461
ms.sourcegitcommit: e2fa73b682a30048907e2acb5c890495ad397bd3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/16/2021
ms.locfileid: "114393628"
---
# <a name="test-cases-for-parameter-files"></a>パラメーター ファイルのテスト ケース

この記事では、[パラメーター ファイル](parameter-files.md)の[テンプレート テスト ツールキット](test-toolkit.md)で実行されるテストについて説明します。 たとえば、_azuredeploy.parameters.json_ という名前のファイルがあります。 これらの例には、テストに **合格** するか、または **不合格** になるテスト名とコード サンプルが含まれています。 テストを実行する方法、または特定のテストを実行する方法の詳細については、「[テスト パラメーター](test-toolkit.md#test-parameters)」を参照してください。

このツールキットには、Azure Resource Manager テンプレート (ARM テンプレート) の [テスト ケース](template-test-cases.md)と、_azuredeploy.json_ または _maintemplate.json_ という名前のメイン テンプレート ファイルが含まれています。

## <a name="use-valid-contentversion"></a>有効な contentVersion を使用する

テスト名: DeploymentParameters には contentVersion が必要

`contentVersion` には `1.0.0.0` という形式の文字列が含まれ、かつ数字のみが使用されている必要があります。

次の例は、`contentVersion` がないため **不合格** になります。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "parameters": {
    "stgAcctName": {
      "value": "demostorage01"
    }
  }
}
```

次の例は、`contentVersion` が文字列ではないため **不合格** になります。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": {},
  "parameters": {
    "stgAcctName": {
      "value": "demostorage01"
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
    "stgAcctName": {
      "value": "demostorage01"
    }
  }
}
```

## <a name="file-must-include-parameters"></a>ファイルにはパラメーターが含まれている必要がある

テスト名: DeploymentParameters にはパラメーターが必要

パラメーター ファイルには、`parameters` セクションが含まれている必要があります。

次の例は **不合格** になります。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
}
```

次の例は **合格** します。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "stgAcctName": {
      "value": "demostorage01"
    }
  }
}
```

## <a name="use-valid-schema-version"></a>有効なスキーマ バージョンを使用する

テスト名: DeploymentParameters にはスキーマが必要

パラメーター ファイルには、有効なスキーマ バージョンが含まれている必要があります。

パラメーター ファイルの有効なスキーマ バージョンには、次の 2 つがあります。

- `https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#`
- `https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#`

次の例は **不合格** になります。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2021-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "stgAcctName": {
      "value": "demostorage01"
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
    "stgAcctName": {
      "value": "demostorage01"
    }
  }
}
```

## <a name="parameters-must-contain-values"></a>パラメーターには値が含まれている必要がある

テスト名: DeploymentParameters には値が必要

パラメーターには、`value` または `reference` が含まれている必要があります。 パスワードなどのシークレットの場合、キー コンテナーは、パラメーター ファイルで `reference` を使用します。 詳細については、「[デプロイ時に Azure Key Vault を使用して、セキュリティで保護されたパラメーター値を渡す](key-vault-parameter.md)」を参照してください

次の例は、`stgAcctName` に `value` が含まれていないため **不合格** になります。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "stgAcctName": {}
  }
}
```

次の例は **合格** します。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "stgAcctName": {
      "value": "demostorage01"
    }
  }
}
```

## <a name="next-steps"></a>次のステップ

- テスト ツールキットについては、「[ARM テンプレート テスト ツールキットを使用する](test-toolkit.md)」を参照してください。
- ARM テンプレートのテストについては、「[ARM テンプレートのテスト ケース](template-test-cases.md)」を参照してください。
- createUiDefinition のテストについては、「[createUiDefinition.json のテスト ケース](createUiDefinition-test-cases.md)」を参照してください。
- すべてのファイルのテストについては、「[すべてのファイルのテスト ケース](all-files-test-cases.md)」を参照してください。
