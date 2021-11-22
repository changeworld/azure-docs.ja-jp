---
title: リソースの名前および種類の無効なセグメント
description: リソースの名前および種類のセグメント数が同じでない場合に発生するエラーを解決する方法について説明します。
ms.topic: troubleshooting
ms.date: 11/12/2021
ms.openlocfilehash: c4b04c78f84dada1e41289d1523b9ee2c49bd4e5
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/12/2021
ms.locfileid: "132404318"
---
# <a name="resolve-errors-for-resource-name-and-type-mismatch"></a>リソースの名前および種類の不一致が原因のエラーを解決する

この記事では、リソースの名前の形式がリソースの種類の形式と一致しない場合に発生するエラーの解決方法について説明します。

## <a name="symptom"></a>症状

テンプレートをデプロイすると、エラーコード **InvalidTemplate** のエラーが返されます。 このメッセージは、リソースの種類と名前が一致しないことを示します。 その中では、名前に含まれるセグメントの数を修正することが提案されています。

## <a name="cause"></a>原因

リソースの種類には、リソース プロバイダーの名前空間と、種類に関する 1 つまたは複数のセグメントが含まれます。 各セグメントは、リソース階層内のレベルを表し、スラッシュで区切られます。

```
{resource-provider-namespace}/{type-segment-1}/{type-segment-2}
```

リソースの名前には、スラッシュで区切られた 1 つまたは複数のセグメントが含まれます。 このセグメント数は、リソースの種類におけるその数と一致している必要があります。

```
{name-segment-1}/{name-segment-2}
```

リソースの種類と名前に、それぞれ異なる数のセグメントが含まれている場合は、このエラーが返されます。

## <a name="solution"></a>解決策

リソースの種類のレベルについて確実に理解してください。 たとえば、キー コンテナー リソースの完全修飾リソースの種類は、`Microsoft.KeyVault/vaults` です。 リソース プロバイダーの名前空間 (**Microsoft.KeyVault**) を無視し、その種類 (**コンテナー**) に焦点を当てることができます。 これにはセグメントが 1 つ含まれます。

キー コンテナー シークレットは、コンテナーの子リソースです。 完全修飾のリソースの種類は `Microsoft.KeyVault/vaults/secrets` です。 このリソースの種類には、2 つのセグメント (**コンテナーとシークレット**) があります。

キー コンテナーの名前を指定するには、`examplevault123` のようにセグメントを 1 つだけを指定します。 シークレットの名前を指定するには、`examplevault123/examplesecret` のようにセグメントを 2 つ指定します。 最初のセグメントは、このシークレットが格納されているキー コンテナーを示します。

次の例は、リソースの名前の有効な形式を示しています。

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
resource kv 'Microsoft.KeyVault/vaults@2019-09-01' = {
  name: 'examplevault123'
  ...
}
```

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "type": "Microsoft.KeyVault/vaults",
  "apiVersion": "2019-09-01",
  "name": "examplevault123",
  ...
}
```

---

複数のセグメントを持つ名前を指定したならば、**エラー** が表示されます。

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
resource kv 'Microsoft.KeyVault/vaults@2019-09-01' = {
  name: 'contoso/examplevault123'
  ...
}
```

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "type": "Microsoft.KeyVault/vaults",
  "apiVersion": "2019-09-01",
  "name": "contoso/examplevault123",
  ...
}
```

---

親リソース内に子リソースを入れ子にする場合は、追加のセグメントだけを指定します。 完全なリソースの種類と名前にも、親リソースからの値が含まれますが、それらは自動的に作成されます。 次の例では種類が `secrets`、名前が `examplesecret` になっています。

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
resource kv 'Microsoft.KeyVault/vaults@2019-09-01' = {
  name: 'examplevault123'
  ...
  resource kvsecret 'secrets' = {
    name: 'examplesecret'
    properties: {
     value: secretValue
    }
  }
}
```

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "type": "Microsoft.KeyVault/vaults",
  "apiVersion": "2019-09-01",
  "name": "examplevault123",
  ...
  "resources": [
    {
      "type": "secrets",
      "apiVersion": "2019-09-01",
      "name": "examplesecret",
      "properties": {
        "value": "[parameters('secretValue')]"
      },
      "dependsOn": [
        "[resourceId('Microsoft.KeyVault/vaults', 'examplevault123')]"
      ]
    }
  ]
}
```

---

親の外側に子リソースを定義する場合は、完全なリソースの種類を指定します。 JSON の場合は、完全なリソースの名前を指定します。

Bicep の場合は、`parent` プロパティを使用して、親リソースのシンボル名を指定します。 親プロパティを使用する場合は、完全な名前が自動的に作成されるので、子リソース名を 1 つのセグメントとして指定します。

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
resource kvsecret 'Microsoft.KeyVault/vaults/secrets@2021-06-01-preview' = {
  name: 'examplesecret'
  parent: kv
  properties: {
     value: secretValue
  }
}

resource kv 'Microsoft.KeyVault/vaults@2019-09-01' = {
  name: 'examplevault123'
  ...
}
```

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "type": "Microsoft.KeyVault/vaults/secrets",
  "apiVersion": "2019-09-01",
  "name": "examplevault123/examplesecret",
  "properties": {
    "value": "[parameters('secretValue')]"
  },
  "dependsOn": [
    "[resourceId('Microsoft.KeyVault/vaults', 'examplevault123')]"
  ]
},
{
  "type": "Microsoft.KeyVault/vaults",
  "apiVersion": "2019-09-01",
  "name": "examplevault123",
```

---

詳細については、「[Bicep での子リソースの名前と種類の設定](../bicep/child-resource-name-type.md) する」または[ARM テンプレートでの子リソースの名前と種類の設定](../templates/child-resource-name-type.md)に関するページを参照してください。