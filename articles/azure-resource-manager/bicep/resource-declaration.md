---
title: リソースを Bicep で宣言する
description: デプロイするリソースを Bicep で宣言する方法について説明します。
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 06/01/2021
ms.openlocfilehash: f62c790f1cb4f0613e17d2bbb3e4fc13e39d8e39
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/10/2021
ms.locfileid: "111963457"
---
# <a name="resource-declaration-in-bicep"></a>リソースの Bicep を使った宣言

Bicep ファイルを介してリソースをデプロイするには、`resource` キーワードを使用してリソース宣言を追加します。

## <a name="set-resource-type-and-version"></a>リソースの種類とバージョンの設定

Bicep ファイルにリソースを追加する際には、まずリソースの種類と API バージョンを設定します。 これらの値によって、リソースで使用できるその他のプロパティが決まります。

次の例は、ストレージ アカウントのリソースの種類と API バージョンを設定する方法を示したものです。 この例は、リソース宣言の内容を完全に示したものではありません。

```bicep
resource myStorageAccount 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  ...
}
```

リソースのシンボリック名を設定します。 前の例では、シンボリック名は `myStorageAccount` です。 シンボリック名には任意の値を使用できますが、Bicep ファイル内の他のリソース、パラメーター、または変数と同じにすることはできません。 シンボリック名はリソース名と同じものではありません。 シンボリック名を使用すると、リソースを Bicep ファイルの他の部分で簡単に参照できます。

Bicep では、[Azure Resource Manager テンプレート (ARM テンプレート) JSON](../templates/syntax.md) で利用できる `apiProfile` はサポートされていません。

## <a name="set-resource-name"></a>リソース名を設定する

各リソースには名前があります。 リソース名を設定する際には、[リソース名の規則と制限事項](../management/resource-name-rules.md)に注意してください。

```bicep
resource stg 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  name: 'examplestorage'
  ...
}
```

通常は、デプロイ時に異なる値を渡すことができるようにパラメーターに名前を設定します。

```bicep
@minLength(3)
@maxLength(24)
param storageAccountName string

resource stg 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  name: storageAccountName
  ...
}
```

## <a name="set-location"></a>場所の設定

多くのリソースには場所が必要です。 リソースに場所が必要かどうかは、Intellisense または[テンプレート参照](/azure/templates/)を使用して判断できます。 次の例では、ストレージ アカウントに使用される location パラメーターを追加しています。

```bicep
resource stg 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  name: 'examplestorage'
  location: 'eastus'
  ...
}
```

通常は、別の場所にデプロイできるようにパラメーターに場所を設定します。

```bicep
param location string = resourceGroup().location

resource stg 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  name: 'examplestorage'
  location: location
  ...
}
```

場所ごとに、異なるリソースの種類がサポートされます。 Azure サービスのサポートされている場所を取得するには、「[リージョン別の利用可能な製品](https://azure.microsoft.com/global-infrastructure/services/)」を参照してください。  リソースの種類にサポートされている場所を取得するには、Azure PowerShell または Azure CLI を使用します。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
((Get-AzResourceProvider -ProviderNamespace Microsoft.Batch).ResourceTypes `
  | Where-Object ResourceTypeName -eq batchAccounts).Locations
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az provider show \
  --namespace Microsoft.Batch \
  --query "resourceTypes[?resourceType=='batchAccounts'].locations | [0]" \
  --out table
```

---

## <a name="set-tags"></a>タグを設定する

デプロイ時には、リソースにタグを適用することができます。 タグは、デプロイされたリソースを論理的に整理するために役立ちます。 タグを指定するさまざまな方法の例については、[ARM テンプレートのタグ](../management/tag-resources.md#arm-templates)に関する記事を参照してください。

## <a name="set-resource-specific-properties"></a>リソース固有のプロパティを設定する

上記のプロパティは、ほとんどの種類のリソースに共通するものです。 これらの値を設定した後、デプロイするリソースの種類に固有のプロパティを設定する必要があります。

使用可能なプロパティと必要なプロパティを特定するには、Intellisense または[テンプレート参照](/azure/templates/)を使用します。 次の例では、ストレージ アカウントの残りのプロパティを設定しています。

```bicep
resource stg 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  name: 'examplestorage'
  location: 'eastus'
  sku: {
    name: 'Standard_LRS'
    tier: 'Standard'
  }
  kind: 'StorageV2'
  properties: {
    accessTier: 'Hot'
  }
}
```

## <a name="set-resource-dependencies"></a>リソースの依存関係を設定する

リソースをデプロイするときに、一部のリソースが他のリソースの前に存在することを確認しなければならない場合があります。 たとえば、データベースをデプロイする前に論理 SQL サーバーが必要です。 このリレーションシップは、あるリソースが他のリソースに依存しているとマークすることで確立します。 リソースのデプロイ順序は、[暗黙的な依存関係](#implicit-dependency)と[明示的な依存関係](#explicit-dependency)の 2 つの方法で影響を受ける可能性があります。

Azure Resource Manager により、リソース間の依存関係が評価され、リソースは依存する順にデプロイされます。 相互依存していないリソースは、平行してデプロイされます。 必要なのは、デプロイするリソースの依存関係を同じ Bicep ファイルに定義することだけです。

### <a name="implicit-dependency"></a>暗黙の依存関係

暗黙的な依存関係は、あるリソース宣言が、式内の別のリソース宣言の識別子を参照する場合に作成されます。 たとえば、次の例の *dnsZone* は、2 番目のリソース定義で参照されています。

```bicep
resource dnsZone 'Microsoft.Network/dnszones@2018-05-01' = {
  name: 'myZone'
  location: 'global'
}

resource otherResource 'Microsoft.Example/examples@2020-06-01' = {
  name: 'exampleResource'
  properties: {
    // get read-only DNS zone property
    nameServers: dnsZone.properties.nameServers
  }
}
```

また、入れ子になったリソースも、内側のリソースに暗黙的な依存関係があります。

```bicep
resource myParent 'My.Rp/parentType@2020-01-01' = {
  name: 'myParent'
  location: 'West US'

  // depends on 'myParent' implicitly
  resource myChild 'childType' = {
    name: 'myChild'
  }
}
```

詳細については、「[Bicep での子リソースの名前と種類の設定](./child-resource-name-type.md)」を参照してください。

### <a name="explicit-dependency"></a>明示的な依存関係

明示的な依存関係は、リソース宣言内で `dependsOn` プロパティを介して宣言されます。 このプロパティは、リソース識別子の配列を受け入れます。 次に、ある DNS ゾーンが別の DNS ゾーンに明示的に依存している例を示します。

```bicep
resource dnsZone 'Microsoft.Network/dnszones@2018-05-01' = {
  name: 'myZone'
  location: 'global'
}

resource otherZone 'Microsoft.Network/dnszones@2018-05-01' = {
  name: 'myZone'
  location: 'global'
  dependsOn: [
    dnsZone
  ]
}
```

`dependsOn` を使用してリソース間のリレーションシップをマップする傾向があるものの、重要なのは、その操作を行う理由を理解することです。 たとえば、リソースが相互にどのように接続されているかをドキュメント化するには、`dependsOn` は適切な方法ではありません。 どのリソースが `dependsOn` 要素で定義されたかを、デプロイ後に照会することはできません。 Resource Manager ではこれらのリソースを並行してデプロイすることはできないため、不要な依存関係を設定するとデプロイ時間が遅くなります。

明示的な依存関係が必要になる場合もありますが、必要になることはめったにありません。 ほとんどの場合は、シンボリック参照を利用して、リソース間の依存関係を示します。 dependsOn を使用していることに気付いたら、取り除く方法があるかどうかを検討します。

### <a name="visualize-dependencies"></a>依存関係を視覚化する

Visual Studio Code には、依存関係を視覚化するためのツールが用意されています。 Visual Studio Code で Bicep ファイルを開き、左上隅にあるビジュアライザー ボタンを選択します。  次のスクリーンショットは、Bicep ファイルで定義されているビジュアル マシン リソースの依存関係を示しています。

:::image type="content" source="./media/resource-declaration/bicep-resource-visualizer.png" alt-text="Visual Studio Code の Bicep リソース ビジュアライザーのスクリーンショット":::

## <a name="reference-existing-resources"></a>既存のリソースを参照する

リソース宣言で `existing` キーワードを使用して、現在のファイルの外部にあるリソースから参照を追加したり、ランタイム プロパティにアクセスしたりすることができます。 これは、ARM テンプレートの [reference () 関数](../templates/template-functions-resource.md#reference)を使用するのと同じです。

`existing` キーワードを使用する場合は、リソースの `name` を指定する必要があります。また、必要に応じて、`scope` プロパティを設定して、別のスコープ内のリソースにアクセスすることもできます。 スコープ プロパティの使用方法の詳細については、「[リソース スコープ](./deploy-to-resource-group.md)」を参照してください。

```bicep
resource stg 'Microsoft.Storage/storageAccounts@2019-06-01' existing = {
  name: 'exampleStorage'
}

output blobEndpoint string = stg.properties.primaryEndpoints.blob
```

上記の例では、ストレージ アカウントはデプロイされていませんが、宣言により、既存のリソースのプロパティにアクセスできます。 'stg' シンボリック名を使用して、ストレージ アカウントのプロパティにアクセスできます。

`scope` プロパティを指定する例を次に示します。

resource stg 'Microsoft.Storage/storageAccounts@2019-06-01' existing = { name: 'exampleStorage' scope: resourceGroup(mySub, myRg) }

## <a name="next-steps"></a>次のステップ

- リソースを条件付きでデプロイするには、「[Bicep の条件付きデプロイ](./conditional-resource-deployment.md)」を参照してください。