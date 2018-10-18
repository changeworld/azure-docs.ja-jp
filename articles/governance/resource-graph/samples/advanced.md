---
title: Azure Resource Graph の高度なクエリ
description: いくつかの高度なクエリを実行するには、Azure Resource Graph を使用します。
services: resource-graph
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/18/2018
ms.topic: quickstart
ms.service: resource-graph
manager: carmonm
ms.custom: mvc
ms.openlocfilehash: a9281bc333b3edd12501a6813f12b9e7ad1bf59f
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/26/2018
ms.locfileid: "47219269"
---
# <a name="advanced-resource-graph-queries"></a>Resource Graph の高度なクエリ

Azure Resource Graph でクエリを理解する最初の手順は、[クエリ言語](../concepts/query-language.md)の基本を理解することです。 [Azure Data Explorer](../../../data-explorer/data-explorer-overview.md) にまだ精通していない場合、探しているリソースに対する要求を作成する方法を理解するための基礎を確認することをお勧めします。

次の高度なクエリを説明します。

> [!div class="checklist"]
> - [VMSS の容量とサイズを取得します](#vmss-capacity)
> - [すべてのタグ名を一覧表示します](#list-all-tags)
> - [ regexに一致する仮想マシン](#vm-regex)

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free) を作成してください。

## <a name="language-support"></a>言語のサポート

Azure CLI (拡張経由) および Azure PowerShell (モジュール経由) は、Azure Resource Graph をサポートします。 次のクエリを実行する前に、環境が準備できていることを確認します。 選択するシェル環境をインストールし、検証する手順については、[Azure CLI](../first-query-azurecli.md#add-the-resource-graph-extension) および [Azure PowerShell](../first-query-powershell.md#add-the-resource-graph-module) を参照してください。

## <a name="vmss-capacity"></a>VMSS の容量とサイズを取得します

このクエリでは、仮想マシン スケール セット (VMSS) のリソースを検索し、仮想マシンのサイズ、スケール セットの容量などのさまざまな詳細情報を取得します。 この情報は、`toint()`分類できるように、数値に容量をキャストする機能を使用します。 これにより、カスタムの名前付きプロパティに返される値も名前変更します。

```Query
where type=~ 'microsoft.compute/virtualmachinescalesets'
| where name contains 'contoso'
| project subscriptionId, name, location, resourceGroup, Capacity = toint(sku.capacity), Tier = sku.name
| order by Capacity desc
```

```azurecli-interactive
az graph query -q "where type=~ 'microsoft.compute/virtualmachinescalesets' | where name contains 'contoso' | project subscriptionId, name, location, resourceGroup, Capacity = toint(sku.capacity), Tier = sku.name | order by Capacity desc"
```

```powershell
Search-AzureRmGraph -Query "where type=~ 'microsoft.compute/virtualmachinescalesets' | where name contains 'contoso' | project subscriptionId, name, location, resourceGroup, Capacity = toint(sku.capacity), Tier = sku.name | order by Capacity desc"
```

## <a name="list-all-tags"></a>すべてのタグ名を一覧表示します

このクエリは、タグを使用して開始し、全ての独自のタグ名とその対応する種類を一覧表示する JSON オブジェクトを構築します。

```Query
project tags
| summarize buildschema(tags)
```

```azurecli-interactive
az graph query -q "project tags | summarize buildschema(tags)"
```

```powershell
Search-AzureRmGraph -Query "project tags | summarize buildschema(tags)"
```

## <a name="vm-regex"></a> regexに一致する仮想マシン

このクエリは、[正規表現](/dotnet/standard/base-types/regular-expression-language-quick-reference) (_regex_と呼ばれる) に一致する仮想マシンを検索します。
**regex @ との一致**により、一致する regex を **^Contoso(.*) [0-9] + $** と定義することができます。 その regex の定義は以下のように説明されています。

- `^` - 一致は、文字列の先頭から始まる必要があります。
- `Contoso` - 照合する Core 文字列 (大文字小文字を区別)。
- `(.*)` - 部分式一致:
  - `.` - 任意の 1 文字との一致 (新しい行を除く)。
  - `*` - 「直前の要素 0 回以上」との一致。
- `[0-9]` - 文字グループは 0 ~ 9 の数字に一致。
- `+` - 「直前の要素 1 回以上」との一致。
- `$` - 直前の要素との一致は、文字列の最後に発生する必要があります。

名前で一致した後、クエリはプロジェクトの名前を提示し、名前の昇順で順序付けします。

```Query
where type =~ 'microsoft.compute/virtualmachines' and name matches regex @'^Contoso(.*)[0-9]+$'
| project name
| order by name asc
```

```azurecli-interactive
az graph query -q "where type =~ 'microsoft.compute/virtualmachines' and name matches regex @'^Contoso(.*)[0-9]+$' | project name | order by name asc"
```

```powershell
Search-AzureRmGraph -Query "where type =~ 'microsoft.compute/virtualmachines' and name matches regex @'^Contoso(.*)[0-9]+$' | project name | order by name asc"
```

## <a name="next-steps"></a>次の手順

- [Starter クエリ](starter.md)のサンプルを参照してください
- [クエリ言語](../concepts/query-language.md)の詳細について学習します
- [その他のリソース](../concepts/explore-resources.md)