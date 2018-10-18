---
title: Azure Resource Graph の初歩的なクエリ
description: いくつかの初歩的なクエリを実行するには、Azure Resource Graph を使用します。
services: resource-graph
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/18/2018
ms.topic: quickstart
ms.service: resource-graph
manager: carmonm
ms.custom: mvc
ms.openlocfilehash: bcc6c6e9d38948cb4be09c9f52049790ccd5ac7e
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/26/2018
ms.locfileid: "47227029"
---
# <a name="starter-resource-graph-queries"></a>Resource Graph の初歩的なクエリ

Azure Resource Graph でクエリを理解する最初の手順は、[クエリ言語](../concepts/query-language.md)の基本を理解することです。 [Azure Data Explorer](../../../data-explorer/data-explorer-overview.md) にまだ精通していない場合、探しているリソースに対する要求を作成する方法を理解するための基礎を確認することをお勧めします。

次のスターター クエリを説明します。

> [!div class="checklist"]
> - [Azure リソースの数](#count-resources)
> - [名前で並べ替えられたリソースの一覧表示](#list-resources)
> - [降順の名前で順序付けられたすべての仮想マシンの表示](#show-vms)
> - [名前とその OS の種類による最初の 5 つの仮想マシンの表示](#show-sorted)
> - [仮想マシンの数 (OS の種類別)](#count-os)
> - [ストレージを含むリソースの表示](#show-storage)
> - [パブリック IP アドレスの一覧表示](#list-publicip)
> - [サブスクリプションで構成されている IP アドレスを持つリソースの数](#count-resources-by-ip)
> - [特定のタグ値が付いたリソースの一覧表示](#list-tag)
> - [特定のタグ値を持つすべてのストレージ アカウントの一覧表示](#list-specific-tag)

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free) を作成してください。

## <a name="language-support"></a>言語のサポート

Azure CLI (拡張経由) および Azure PowerShell (モジュール経由) は、Azure Resource Graph をサポートします。 次のクエリを実行する前に、環境が準備できていることを確認します。 選択するシェル環境をインストールし、検証する手順については、[Azure CLI](../first-query-azurecli.md#add-the-resource-graph-extension) および [Azure PowerShell](../first-query-powershell.md#add-the-resource-graph-module) を参照してください。

## <a name="count-resources"></a>Azure リソースの数

このクエリは、あなたがアクセスしたサブスクリプションに存在する Azure リソースの数を返します。 また、選択するシェルに適切な Azure Resource Graph コンポーネントがインストールされ、正常に動作していることを検証するために使用する場合にも有効なクエリです。

```Query
summarize count()
```

```azurecli-interactive
az graph query -q "summarize count()"
```

```powershell
Search-AzureRmGraph -Query "summarize count()"
```

## <a name="list-resources"></a>名前で並べ替えられたリソースの一覧表示

このクエリは、あらゆる種類のリソースまたは特定の一致するプロパティを制限することなく、Azure リソースの**名前**、**種類**および**場所**のみを返しますが、`order by`**名前**プロパティを昇順 (`asc`) で整理するために使用します。

```Query
project name, type, location
| order by name asc
```

```azurecli-interactive
az graph query -q "project name, type, location | order by name asc"
```

```powershell
Search-AzureRmGraph -Query "project name, type, location | order by name asc"
```

## <a name="show-vms"></a>降順の名前で順序付けられたすべての仮想マシンの表示

すべての Azure リソースを取得する代わりに、仮想マシン(`Microsoft.Compute/virtualMachines`型) の一覧のみが必要な場合は、結果においてプロパティ**の種類**と照合することができます。
前のクエリと同様に、`desc` は `order by` を降順に変更します。 `=~`の種類一致により、Resource Graph では大文字と小文字が区別されないことを示します。

```Query
project name, location, type
| where type =~ 'Microsoft.Compute/virtualMachines'
| order by name desc
```

```azurecli-interactive
az graph query -q "project name, location, type| where type =~ 'Microsoft.Compute/virtualMachines' | order by name desc"
```

```powershell
Search-AzureRmGraph -Query "project name, location, type| where type =~ 'Microsoft.Compute/virtualMachines' | order by name desc"
```

## <a name="show-sorted"></a>名前とその OS の種類による最初の 5 つの仮想マシンの表示

このクエリは、名前別に整理された 5 つの一致するレコードを取得するためのみに `limit` を使用します。 Azure リソースの種類は `Microsoft.Compute/virtualMachines` です。 `project` により、Azure Resource Graph に含めるプロパティを説明します。

```Query
where type =~ 'Microsoft.Compute/virtualMachines'
| project name, properties.storageProfile.osDisk.osType
| top 5 by name desc
```

```azurecli-interactive
az graph query -q "where type =~ 'Microsoft.Compute/virtualMachines' | project name, properties.storageProfile.osDisk.osType | top 5 by name desc"
```

```powershell
Search-AzureRmGraph -Query "where type =~ 'Microsoft.Compute/virtualMachines' | project name, properties.storageProfile.osDisk.osType | top 5 by name desc"
```

## <a name="count-os"></a>仮想マシンの数 (OS の種類別)

前のクエリに基づき、Azure リソースの種類 `Microsoft.Compute/virtualMachines` によりまだ制限していますが、返されるレコード数を制限しなくなっています。
代わりに、プロパティで値をグループ化し、集計する方法を定義するために`summarize` および `count()` を使用しました。この例では、`properties.storageProfile.osDisk.osType`です。 この文字列が完全なオブジェクトにおいてどのように見えるかという例については、[ リソースの確認‐仮想マシンの検出 ](../concepts/explore-resources.md#virtual-machine-discovery)を参照してください。

```Query
where type =~ 'Microsoft.Compute/virtualMachines'
| summarize count() by tostring(properties.storageProfile.osDisk.osType)
```

```azurecli-interactive
az graph query -q "where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by tostring(properties.storageProfile.osDisk.osType)"
```

```powershell
Search-AzureRmGraph -Query "where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by tostring(properties.storageProfile.osDisk.osType)"
```

同じクエリを記述するさまざまな方法は、プロパティを `extend` し、クエリ内で使用するために一時的に名前を付けます。この場合は **os** です。 前の例に示すように、**os** が `summarize` および `count()` により使用されます。

```Query
where type =~ 'Microsoft.Compute/virtualMachines'
| extend os = properties.storageProfile.osDisk.osType
| summarize count() by tostring(os)
```

```azurecli-interactive
az graph query -q "where type =~ 'Microsoft.Compute/virtualMachines' | extend os = properties.storageProfile.osDisk.osType | summarize count() by tostring(os)"
```

```powershell
Search-AzureRmGraph -Query "where type =~ 'Microsoft.Compute/virtualMachines' | extend os = properties.storageProfile.osDisk.osType | summarize count() by tostring(os)"
```

> [!NOTE]
> `=~` によりプロパティの大文字と小文字を区別しないことが可能である一方、クエリにおけるプロパティの使用 (**properties.storageProfile.osDisk.osType** 等) には正しいケースを必要とすることにご注意ください。 プロパティが正しくない場合でも値を返せますが、グループ化または集計が不正確になる可能性があります。

## <a name="show-storage"></a>ストレージを含むリソースの表示

一致する種類を明示的に定義する代わりに、このクエリの例により`contains`単語**ストレージ**する任意の Azure リソースが見つかります。

```Query
where type contains 'storage' | distinct type
```

```azurecli-interactive
az graph query -q "where type contains 'storage' | distinct type"
```

```powershell
Search-AzureRmGraph -Query "where type contains 'storage' | distinct type"
```

## <a name="list-publicip"></a>パブリック IP アドレスの一覧表示

前のクエリと同様に、 **publicIPAddresses** を含む種類を全て見つけます。 このクエリは、**properties.ipAddress** が null の場合、**properties.ipAddress** のみを返す場合、および上位 100 件の結果を`limit`する場合に結果を除外するパターンで拡張します。 選択したシェルによって引用符のエスケープが必要となる場合があります。

```Query
where type contains 'publicIPAddresses' and properties.ipAddress != ''
| project properties.ipAddress
| limit 100
```

```azurecli-interactive
az graph query -q "where type contains 'publicIPAddresses' and properties.ipAddress != '' | project properties.ipAddress | limit 100"
```

```powershell
Search-AzureRmGraph -Query "where type contains 'publicIPAddresses' and properties.ipAddress != '' | project properties.ipAddress | limit 100"
```

## <a name="count-resources-by-ip"></a>サブスクリプションで構成されている IP アドレスを持つリソースの数

前例のクエリを使用し、`summarize` および `count()` を追加して、構成された IP アドレスをもつリソースのサブスクリプションにより一覧を取得できます。

```Query
where type contains 'publicIPAddresses' and properties.ipAddress != ''
| summarize count () by subscriptionId
```

```azurecli-interactive
az graph query -q "where type contains 'publicIPAddresses' and properties.ipAddress != '' | summarize count () by subscriptionId"
```

```powershell
Search-AzureRmGraph -Query "where type contains 'publicIPAddresses' and properties.ipAddress != '' | summarize count () by subscriptionId"
```

## <a name="list-tag"></a>特定のタグ値が付いたリソースの一覧表示

タグなどの Azure リソースの種類以外のプロパティによる結果を制限することができます。 この例では、**内部**の値をもつ**環境**のタグ名を使用して Azure リソースをフィルター処理しています。

```Query
where tags.environment=~'internal'
| project name
```

```azurecli-interactive
az graph query -q "where tags.environment=~'internal' | project name"
```

```powershell
Search-AzureRmGraph -Query "where tags.environment=~'internal' | project name"
```

そのリソースが持っていたタグおよびそれらのタグの値を提供する必要があった場合も、この例ではプロパティ **タグ** を `project` キーワードに追加することにより拡張することができました。

```Query
where tags.environment=~'internal'
| project name, tags
```

```azurecli-interactive
az graph query -q "where tags.environment=~'internal' | project name, tags"
```

```powershell
Search-AzureRmGraph -Query "where tags.environment=~'internal' | project name, tags"
```

## <a name="list-specific-tag"></a>特定のタグ値を持つすべてのストレージ アカウントの一覧表示

前例のフィルター機能と** 種類 **プロパティによる Azure リソースの種類を組み合わせることにより、
特定のタグ名と値をもつ特定の種類の Azure リソースの検索を制限することができます。

```Query
where type =~ 'Microsoft.Storage/storageAccounts'
| where tags['tag with a space']=='Custom value'
```

```azurecli-interactive
az graph query -q "where type =~ 'Microsoft.Storage/storageAccounts' | where tags['tag with a space']=='Custom value'"
```

```powershell
Search-AzureRmGraph -Query "where type =~ 'Microsoft.Storage/storageAccounts' | where tags['tag with a space']=='Custom value'"
```

> [!NOTE]
> この例では`=~`の条件付きの代わりに、`==`をマッチングに使用しています。 `==` は大文字小文字が区別します。

## <a name="next-steps"></a>次の手順

- [クエリ言語](../concepts/query-language.md)の詳細について学習します
- [その他のリソース](../concepts/explore-resources.md)
- [高度なクエリ](advanced.md)のサンプルを参照してください