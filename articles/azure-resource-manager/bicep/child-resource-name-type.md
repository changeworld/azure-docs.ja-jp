---
title: Bicep の子リソース
description: Bicep で子リソースの名前と種類を設定する方法について説明します。
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 09/13/2021
ms.openlocfilehash: fbaf2eb104f213c6b4b071ce027b7cb6022aad97
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130244618"
---
# <a name="set-name-and-type-for-child-resources-in-bicep"></a>Bicep での子リソースの名前と種類の設定

子リソースとは、別のリソースのコンテキスト内でのみ存在するリソースのことです。 たとえば[仮想マシン拡張機能](/azure/templates/microsoft.compute/virtualmachines/extensions)は、[仮想マシン](/azure/templates/microsoft.compute/virtualmachines)なしでは存在できません。 この拡張機能リソースが仮想マシンの子です。

各親リソースは、子リソースとして特定のリソースの種類のみを受け取ります。 リソースの種類の階層は、[Bicep リソース リファレンス](/azure/templates/)で確認できます。

この記事では、子リソースを宣言するさまざまな方法を示します。

### <a name="microsoft-learn"></a>Microsoft Learn

子リソースの詳細とハンズオン ガイダンスについては、**Microsoft Learn** の「[Bicep を使用して子と拡張機能のリソースをデプロイする](/learn/modules/child-extension-bicep-templates)」を参照してください。

## <a name="name-and-type-pattern"></a>名前と種類のパターン

Bicep では、親リソースの内側または外側に子リソースを指定できます。 リソースの名前とリソースの種類に指定する値は、子リソースを宣言する方法によって異なります。 ただし、完全な名前と種類は、常に同じパターンに解決されます。 

子リソースの **完全な名前** では、次のパターンが使用されます。

```bicep
{parent-resource-name}/{child-resource-name}
```

階層が 3 レベル以上ある場合は、次のように親名を繰り返します。

```bicep
{parent-resource-name}/{child-level1-resource-name}/{child-level2-resource-name}
```

子リソースの **完全な種類** では、次のパターンが使用されます。

```bicep
{resource-provider-namespace}/{parent-resource-type}/{child-resource-type}
```

階層が 3 レベル以上ある場合は、次のように親のリソースの種類を繰り返します。

```bicep
{resource-provider-namespace}/{parent-resource-type}/{child-level1-resource-type}/{child-level2-resource-type}
```

`/` 文字間のセグメントをカウントすると、種類のセグメント数は、常に名前のセグメント数より 1 つ多くなります。 

## <a name="within-parent-resource"></a>親リソースの内側

次の例は、親リソースの resources プロパティ内に追加された子リソースを示しています。

```bicep
resource <parent-resource-symbolic-name> '<resource-type>@<api-version>' = {
  <parent-resource-properties>

  resource <child-resource-symbolic-name> '<child-resource-type>' = {
    <child-resource-properties>
  }
}
```

入れ子になったリソース宣言は、親リソースの構文の最上位レベルに記述する必要があります。 各レベルが親リソースの子の種類である限りは、宣言を任意の深さで入れ子にすることができます。

親リソースの type 内に type と name の値を定義するときは、スラッシュを使わず 1 つのセグメントとして書式設定します。 次の例は、ファイル サービスの子リソースを持つストレージ アカウントを示しています。ファイル サービスには、ファイル共有の子リソースがあります。 ファイル サービスの名前は `default` に設定され、その種類は `fileServices` に設定されます。 ファイル共有の名前は `exampleshare` に設定され、その種類は `shares` に設定されます。

:::code language="bicep" source="~/azure-docs-bicep-samples/syntax-samples/child-resource-name-type/insidedeclaration.bicep" highlight="9,12":::

完全なリソースの種類は、`Microsoft.Storage/storageAccounts/fileServices` と `Microsoft.Storage/storageAccounts/fileServices/shares` です。 `Microsoft.Storage/storageAccounts/` は親リソースの種類とバージョンから想定されるため不要です。 必要に応じて、入れ子になったリソースを使用すれば、構文 `<segment>@<version>` を使用して、API バージョンを宣言できます。 入れ子になったリソースで API バージョンが省略される場合は、親リソースの API バージョンが使用されます。 入れ子になったリソースに API バージョンが指定される場合は、指定されたその API バージョンが使用されます。

子リソース名は `default` と `exampleshare` に設定されますが、完全な名前には親名が含まれます。 `examplestorage` と `default` は、親リソースから想定されるので指定する必要はありません。

入れ子になったリソースは、その親リソースのプロパティにアクセスできます。 同じ親リソースの本体内で宣言されたその他のリソースは、シンボリック名を使用することで互いに参照することができます。 親リソースは、それに含まれるリソースのプロパティにアクセスできない場合があり、この試行により循環依存関係が発生します。

親リソースの外部で入れ子になったリソースを参照するには、それを含むリソース名と `::` 演算子で修飾する必要があります。 たとえば、子リソースからプロパティを出力します。

```bicep
output childAddressPrefix string = VNet1::VNet1_Subnet1.properties.addressPrefix
```

## <a name="outside-parent-resource"></a>親リソースの外側

次の例は、親リソースの外側の子リソースを示しています。 親リソースが同じテンプレート内にデプロイされていない場合、または複数の子リソースを作成するために [loop](loops.md) を使う場合は、このアプローチを使用することがあります。 値を親のシンボリック名に設定して、子の親プロパティを指定します。 この構文では、完全なリソースの種類を宣言する必要がありますが、子リソースの名前は子リソースの名前のみです。

```bicep
resource <parent-resource-symbolic-name> '<resource-type>@<api-version>' = {
  name: 'myParent'
  <parent-resource-properties>
}

resource <child-resource-symbolic-name> '<child-resource-type>@<api-version>' = {
  parent: <parent-resource-symbolic-name>
  name: 'myChild'
  <child-resource-properties>
}
```

親リソースの外側に type と name を定義するときは、スラッシュを使って親の種類と名前を含めるように書式設定します。

次の例は、ルート レベルで定義されているストレージ アカウント、ファイル サービス、およびファイル共有を示しています。

:::code language="bicep" source="~/azure-docs-bicep-samples/syntax-samples/child-resource-name-type/outsidedeclaration.bicep" highlight="10,12,15,17":::

子リソースのシンボリック名の参照は、親を参照する場合と同じように機能します。

## <a name="full-resource-name-outside-parent"></a>親の外側の完全なリソース名

親の外側で子リソースを宣言するときにも、完全なリソース名と種類を使用できます。 子リソースに親プロパティは設定しません。 依存関係は推論できないため、明示的に設定する必要があります。

:::code language="bicep" source="~/azure-docs-bicep-samples/syntax-samples/child-resource-name-type/fullnamedeclaration.bicep" highlight="10,11,17,18":::

> [!IMPORTANT]
> 完全なリソース名と種類を設定する方法は推奨されません。 他の方法の 1 つを使用するほどタイプ セーフではないからです。

## <a name="next-steps"></a>次の手順

* Bicep ファイル作成の詳細については、「[Bicep ファイルの構造と構文の詳細](./file.md)」を参照してください。
* リソースを参照する際のリソース名の形式については、[reference 関数](./bicep-functions-resource.md#reference)の説明を参照してください。
