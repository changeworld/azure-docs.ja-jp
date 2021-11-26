---
title: リソースを Bicep で宣言する
description: デプロイするリソースを Bicep で宣言する方法について説明します。
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 11/12/2021
ms.openlocfilehash: 1398215116307cf810d259ac52c30f6588e612ab
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2021
ms.locfileid: "132494005"
---
# <a name="resource-declaration-in-bicep"></a>リソースの Bicep を使った宣言

この記事では、Bicep ファイルにリソースを追加するために使用する構文について説明します。

## <a name="declaration"></a>宣言

キーワードを使用してリソース宣言を追加 `resource` します。 リソースのシンボリック名を設定します。 シンボリック名はリソース名と同じものではありません。 シンボリック名は、Bicep ファイルの他の部分にあるリソースを参照するために使用します。

```bicep
resource <symbolic-name> '<full-type-name>@<api-version>' = {
  <resource-properties>
}
```

そのため、ストレージ アカウントの宣言は次の方法で開始できます。

```bicep
resource stg 'Microsoft.Storage/storageAccounts@2021-04-01' = {
  ...
}
```

シンボリック名は大文字と小文字が区別されます。 それらには、英字、数字、アンダー スコア (`_`) を含めることができます。 数字で始めることはできません。 リソースの名前を、パラメーター、変数またはモジュールと同じにすることはできません。

使用可能なリソースの種類とバージョンについては [、Bicep リソース リファレンスに関するページを参照してください](/azure/templates/)。 Bicep では、[Azure Resource Manager テンプレート (ARM テンプレート) JSON](../templates/syntax.md) で利用できる `apiProfile` はサポートされていません。

リソースを条件付きでデプロイするには、 構文を使用 `if` します。 詳細については [、「Bicep での条件付きデプロイ」を参照してください](conditional-resource-deployment.md)。

```bicep
resource <symbolic-name> '<full-type-name>@<api-version>' = if (condition) {
  <resource-properties>
}
```

リソースの複数のインスタンスをデプロイするには、 構文を使用 `for` します。 `batchSize` デコレーターを使用して、インスタンスを順番にデプロイするか、または並列でデプロイするかを指定できます。 詳細については [、「Bicep の反復ループ」を参照してください](loops.md)。

```bicep
@batchSize(int) // optional decorator for serial deployment
resource <symbolic-name> '<full-type-name>@<api-version>' = [for <item> in <collection>: {
  <properties-to-repeat>
}]
```

リソース プロパティの `for` 構文を使用して、配列を作成することもできます。

```bicep
resource <symbolic-name> '<full-type-name>@<api-version>' = {
  properties: {
    <array-property>: [for <item> in <collection>: <value-to-repeat>]
  }
}
```

## <a name="resource-name"></a>リソース名

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

## <a name="location"></a>場所

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

## <a name="tags"></a>タグ

デプロイ時には、リソースにタグを適用することができます。 タグは、デプロイされたリソースを論理的に整理するために役立ちます。 タグを指定するさまざまな方法の例については、[ARM テンプレートのタグ](../management/tag-resources.md#arm-templates)に関する記事を参照してください。

## <a name="managed-identities-for-azure-resources"></a>Azure リソースのマネージド ID

一部のリソースでは、[Azure リソース用マネージド ID](../../active-directory/managed-identities-azure-resources/overview.md) がサポートされます。 これらのリソースには、リソース宣言のルート レベルに ID オブジェクトがあります。

システム割り当てまたはユーザー割り当ての ID を使用できます。

次の例は、Azure Kubernetes Service クラスターのシステム割り当て ID を構成する方法を示しています。

```bicep
resource aks 'Microsoft.ContainerService/managedClusters@2020-09-01' = {
  name: clusterName
  location: location
  tags: tags
  identity: {
    type: 'SystemAssigned'
  }
```

次の例は、仮想マシンのユーザー割り当て ID を構成する方法を示しています。

```bicep
param userAssignedIdentity string

resource vm 'Microsoft.Compute/virtualMachines@2020-06-01' = {
  name: vmName
  location: location
  identity: {
    type: 'UserAssigned'
    userAssignedIdentities: {
      '${userAssignedIdentity}': {}
    }
  }
```

## <a name="resource-specific-properties"></a>リソース固有のプロパティ

上記のプロパティは、ほとんどの種類のリソースに共通するものです。 これらの値を設定した後、デプロイするリソースの種類に固有のプロパティを設定する必要があります。

Intellisense または [Bicep リソース](/azure/templates/) リファレンスを使用して、使用可能なプロパティと必要なプロパティを決定します。 次の例では、ストレージ アカウントの残りのプロパティを設定しています。

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

## <a name="dependencies"></a>依存関係

リソースをデプロイするときに、一部のリソースが他のリソースの前に存在することを確認しなければならない場合があります。 たとえば、データベースをデプロイする前に論理 SQL サーバーが必要です。 このリレーションシップは、あるリソースが他のリソースに依存しているとマークすることで確立します。 リソースのデプロイ順序は、[暗黙的な依存関係](#implicit-dependency)と[明示的な依存関係](#explicit-dependency)の 2 つの方法で影響を受ける可能性があります。

Azure Resource Manager により、リソース間の依存関係が評価され、リソースは依存する順にデプロイされます。 相互依存していないリソースは、平行してデプロイされます。 必要なのは、デプロイするリソースの依存関係を同じ Bicep ファイルに定義することだけです。

### <a name="implicit-dependency"></a>暗黙の依存関係

あるリソース宣言が同じデプロイ内の別のリソースを参照している場合は、暗黙的な依存関係が作成されます。 たとえば、次の例の *dnsZone* は、2 番目のリソース定義で参照されています。

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

暗黙的な依存関係が存在する場合は、**明示的な依存関係を追加しないでください**。

入れ子になったリソースの詳細については、「[Bicep での子リソースの名前と種類の設定](./child-resource-name-type.md)」を参照してください。

### <a name="explicit-dependency"></a>明示的な依存関係

明示的な依存関係は、`dependsOn` プロパティで宣言されます。 このプロパティによって、リソース識別子の配列が受け入れられるため、複数の依存関係を指定できます。

次の例は、`dnsZone` という名前の DNS ゾーンに依存する `otherZone` という名前の DNS ゾーンを示しています。

```bicep
resource dnsZone 'Microsoft.Network/dnszones@2018-05-01' = {
  name: 'demoeZone1'
  location: 'global'
}

resource otherZone 'Microsoft.Network/dnszones@2018-05-01' = {
  name: 'demoZone2'
  location: 'global'
  dependsOn: [
    dnsZone
  ]
}
```

`dependsOn` を使用してリソース間のリレーションシップをマップする傾向があるものの、重要なのは、その操作を行う理由を理解することです。 たとえば、リソースが相互にどのように接続されているかをドキュメント化するには、`dependsOn` は適切な方法ではありません。 どのリソースが `dependsOn` 要素で定義されたかを、デプロイ後に照会することはできません。 Resource Manager ではこれらのリソースを並行してデプロイすることはできないため、不要な依存関係を設定するとデプロイ時間が遅くなります。

明示的な依存関係が必要になる場合もありますが、必要になることはめったにありません。 ほとんどの場合は、シンボリック名を利用して、リソース間の依存関係を暗黙的に示すことができます。 明示的な依存関係が設定されていることに気づいた場合、削除する方法があるかどうかを検討する必要があります。

### <a name="visualize-dependencies"></a>依存関係を視覚化する

Visual Studio Code には、依存関係を視覚化するためのツールが用意されています。 Visual Studio Code で Bicep ファイルを開き、左上隅にあるビジュアライザー ボタンを選択します。  次のスクリーンショットには、仮想マシンの依存関係が示されています。

:::image type="content" source="./media/resource-declaration/bicep-resource-visualizer.png" alt-text="Visual Studio Code の Bicep リソース ビジュアライザーのスクリーンショット":::

## <a name="existing-resources"></a>既存のリソース

現在の Bicep ファイルの外部にあるリソースを参照するには、リソース宣言で `existing` キーワードを使用します。

`existing` キーワードを使用する場合、リソースの `name` を指定します。 次の例では、現在のデプロイと同じリソース グループにある既存のストレージ アカウントを取得します。

```bicep
resource stg 'Microsoft.Storage/storageAccounts@2019-06-01' existing = {
  name: 'examplestorage'
}

output blobEndpoint string = stg.properties.primaryEndpoints.blob
```

必要に応じて、別のスコープ内のリソースにアクセスするように `scope` プロパティを設定できます。 次の例では、別のリソース グループにある既存のストレージ アカウントが参照されています。

```bicep
resource stg 'Microsoft.Storage/storageAccounts@2019-06-01' existing = {
  name: 'examplestorage'
  scope: resourceGroup(exampleRG)
}

output blobEndpoint string = stg.properties.primaryEndpoints.blob
```

存在しないリソースを参照しようとすると、エラーが発生し、 `NotFound` デプロイは失敗します。

スコープの設定の詳細については、「[Bicep のスコープ関数](bicep-functions-scope.md)」を参照してください。

前の例では、ストレージ アカウントはデプロイされません。 代わりに、シンボリック名を使用して、既存のリソースのプロパティにアクセスできます。

## <a name="next-steps"></a>次のステップ

- リソースを条件付きでデプロイするには、「[Bicep の条件付きデプロイ](./conditional-resource-deployment.md)」を参照してください。
