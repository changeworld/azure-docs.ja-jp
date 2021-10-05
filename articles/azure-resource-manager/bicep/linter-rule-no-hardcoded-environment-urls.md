---
title: リンター ルール - ハードコーディングされた環境 URL がない
description: リンター ルール - ハードコーディングされた環境 URL がない
ms.topic: conceptual
ms.date: 09/14/2021
ms.openlocfilehash: d35736860dd672ffc00ea1d4cde52d8f4d4ef8c9
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128594444"
---
# <a name="linter-rule---no-hardcoded-environment-url"></a>リンター ルール - ハードコーディングされた環境 URL がない

リンターを使用すると、開発時にガイダンスを提供することで、コーディング標準を簡単に適用できます。 現在のリンター ルールのセットは最小限であり、[arm-ttk test cases](../templates/template-test-cases.md) から取得されます。

- [no-hardcoded-env-urls](./linter-rule-no-hardcoded-environment-urls.md)
- [no-unused-params](./linter-rule-no-unused-parameters.md)
- [no-unused-vars](./linter-rule-no-unused-variables.md)
- [prefer-interpolation](./linter-rule-prefer-interpolation.md)
- [secure-parameter-default](./linter-rule-secure-parameter-default.md)
- [simplify-interpolation](./linter-rule-simplify-interpolation.md)

詳細については、「[Bicep リンターの使用方法](./linter.md)」を参照してください。

## <a name="code"></a>コード

`no-hardcoded-env-urls`

## <a name="description"></a>説明

テンプレート内に環境 URL をハードコーディングしないでください。 代わりに、[環境関数](../templates/template-functions-deployment.md#environment)を使用し、展開中、これらの URL を動的に取得します。 ブロックされている URL ホストの一覧については、[bicepconfig.json](https://github.com/Azure/bicep/blob/main/src/Bicep.Core/Configuration/bicepconfig.json) で `DisallowedHosts` の既定の一覧を参照してください。

## <a name="examples"></a>例

URL がハードコーディングされているため、次の例ではこのテストに不合格となります。

```bicep
var AzureURL = 'https://management.azure.com'
```

concat または uri と共に使用されているときも、テストは不合格になります。

```bicep
var AzureSchemaURL1 = concat('https://','gallery.azure.com')
var AzureSchemaURL2 = uri('gallery.azure.com','test')
```

次の例は、このテストで合格になります。

```bicep
var AzureSchemaURL = environment().gallery
```

## <a name="configuration"></a>構成

許可しない URL ホストのセットは、次のように bicepconfig.json ファイルの disallowedHosts プロパティを使用してカスタマイズできます。

```json
{
  "analyzers": {
    "core": {
      "enabled": true,
      "rules": {
        "no-hardcoded-env-urls": {
          "level": "warning",
          "disallowedHosts": [
            "gallery.azure.com",
            "management.core.windows.net",
            "management.azure.com",
            "database.windows.net",
            "core.windows.net",
            "login.microsoftonline.com",
            "graph.windows.net",
            "trafficmanager.net",
            "datalake.azure.net",
            "azuredatalakestore.net",
            "azuredatalakeanalytics.net",
            "vault.azure.net",
            "api.loganalytics.io",
            "asazure.windows.net",
            "region.asazure.windows.net",
            "batch.core.windows.net"
          ]
        }
      }
    }
  }
}
```

## <a name="next-steps"></a>次のステップ

Visual Studio Code と Bicep 拡張機能の使用方法の詳細については、「[クイック スタート: Visual Studio Code を使用して Bicep ファイルを作成する](./quickstart-create-bicep-use-visual-studio-code.md)」を参照してください。
