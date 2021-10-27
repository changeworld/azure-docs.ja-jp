---
title: リンター ルール - ハードコーディングされた環境 URL がない
description: リンター ルール - ハードコーディングされた環境 URL がない
ms.topic: conceptual
ms.date: 10/14/2021
ms.openlocfilehash: 8bb3bbd4be61259d1a11184d879f737d970399cd
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/19/2021
ms.locfileid: "130165387"
---
# <a name="linter-rule---no-hardcoded-environment-url"></a>リンター ルール - ハードコーディングされた環境 URL がない

このルールでは、クラウド環境によって異なるハードコーディングされた URL を検索します。

## <a name="returned-code"></a>返されるコード

`no-hardcoded-env-urls`

## <a name="solution"></a>解決策

Bicep ファイルで URL をハードコーディングする代わりに、[環境関数](../templates/template-functions-deployment.md#environment)を使用して、デプロイ時にこれらの URL を動的に取得します。 環境関数では、デプロイするクラウド環境に基づいて異なる URL が返されます。

URL がハードコーディングされているため、次の例ではこのテストに不合格となります。

```bicep
var managementURL = 'https://management.azure.com'
```

concat または uri と共に使用されているときも、テストは不合格になります。

```bicep
var galleryURL1 = concat('https://','gallery.azure.com')
var galleryURL2 = uri('gallery.azure.com','test')
```

これは、ハードコーディングされた URL を `environment()` 関数に置き換えることで修正できます。

```bicep
var galleryURL = environment().gallery
```

場合によっては、デプロイしたリソースからプロパティを取得することで修正できます。 たとえば、ストレージ アカウントのエンドポイントを構築する代わりに、`.properties.primaryEndpoints` を使用して取得します。

```bicep
param storageAccountName string

resource sa 'Microsoft.Storage/storageAccounts@2021-04-01' = {
  name: storageAccountName
  location: 'westus'
  sku: {
    name: 'Standard_LRS'
  }
  kind: 'StorageV2'
  properties: {
    accessTier: 'Hot'
  }
}

output endpoint string = sa.properties.primaryEndpoints.web
```

## <a name="configuration"></a>構成

このルールでは、許可されない URL を判別するために次の設定が既定で使用されます。 

```json
"analyzers": {
  "core": {
    "verbose": false,
    "enabled": true,
    "rules": {
      "no-hardcoded-env-urls": {
        "level": "warning",
        "disallowedhosts": [
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
        ],
        "excludedhosts": [
          "schema.management.azure.com"
        ]
      }
    }
  }
}
```

bicepconfig.json ファイルを追加し、新しい設定を適用することでこれをカスタマイズできます。

## <a name="next-steps"></a>次のステップ

リンターの詳細については、「[Bicep リンターの使用方法](./linter.md)」を参照してください。
