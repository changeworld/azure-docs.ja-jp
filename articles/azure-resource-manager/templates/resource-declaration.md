---
title: テンプレート内でリソースを宣言する
description: Azure Resource Manager テンプレート (ARM テンプレート) 内で、デプロイするリソースを宣言する方法について説明します。
ms.topic: conceptual
ms.date: 05/11/2021
ms.openlocfilehash: 7d7bae8adec81aa3344c5c571f0e40556928a1a9
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/10/2021
ms.locfileid: "111960125"
---
# <a name="resource-declaration-in-arm-templates"></a>ARM テンプレートでのリソースの宣言

Azure Resource Manager テンプレート (ARM テンプレート) を使用してリソースをデプロイするには、リソース宣言を追加します。 JSON テンプレート内で `resources` 配列を使用します。

## <a name="set-resource-type-and-version"></a>リソースの種類とバージョンの設定

テンプレートにリソースを追加する際には、まずリソースの種類と API バージョンを設定します。 これらの値によって、リソースで使用できるその他のプロパティが決まります。

次の例は、ストレージ アカウントのリソースの種類と API バージョンを設定する方法を示したものです。 この例は、リソース宣言の内容を完全に示したものではありません。

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2019-06-01",
    ...
  }
]
```

## <a name="set-resource-name"></a>リソース名を設定する

各リソースには名前があります。 リソース名を設定する際には、[リソース名の規則と制限事項](../management/resource-name-rules.md)に注意してください。

```json
"parameters": {
  "storageAccountName": {
    "type": "string",
    "minLength": 3,
    "maxLength": 24
  }
},
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2019-06-01",
    "name": "[parameters('storageAccountName')]",
    ...
  }
]
```

## <a name="set-location"></a>場所の設定

多くのリソースには場所が必要です。 リソースに場所が必要かどうかは、Intellisense または[テンプレート参照](/azure/templates/)を使用して判断できます。 次の例では、ストレージ アカウントに使用される location パラメーターを追加しています。

```json
"parameters": {
  "storageAccountName": {
    "type": "string",
    "minLength": 3,
    "maxLength": 24
  },
  "location": {
    "type": "string",
    "defaultValue": "[resourceGroup().location]"
  }
},
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2019-06-01",
    "name": "[parameters('storageAccountName')]",
    "location": "[parameters('location')]",
    ...
  }
]
```

詳細については、「[ARM テンプレートでリソースの場所を設定する](resource-location.md)」を参照してください。

## <a name="set-tags"></a>タグを設定する

デプロイ時には、リソースにタグを適用することができます。 タグは、デプロイされたリソースを論理的に整理するために役立ちます。 タグを指定するさまざまな方法の例については、[ARM テンプレートのタグ](../management/tag-resources.md#arm-templates)に関する記事を参照してください。

## <a name="set-resource-specific-properties"></a>リソース固有のプロパティを設定する

上記のプロパティは、ほとんどの種類のリソースに共通するものです。 これらの値を設定した後、デプロイするリソースの種類に固有のプロパティを設定する必要があります。

使用可能なプロパティと必要なプロパティを特定するには、Intellisense または[テンプレート参照](/azure/templates/)を使用します。 次の例では、ストレージ アカウントの残りのプロパティを設定しています。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountName": {
      "type": "string",
      "minLength": 3,
      "maxLength": 24
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]"
    }
  },
  "functions": [],
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-06-01",
      "name": "[parameters('storageAccountName')]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "Standard_LRS",
        "tier": "Standard"
      },
      "kind": "StorageV2",
      "properties": {
        "accessTier": "Hot"
      }
    }
  ]
}
```

## <a name="next-steps"></a>次のステップ

* リソースを条件付きでデプロイするには、「[ARM テンプレートでの条件付きデプロイ](conditional-resource-deployment.md)」を参照してください。
* リソースの依存関係を設定するには、「[ARM テンプレートでのリソース デプロイ順序の定義](./resource-dependency.md)」を参照してください。