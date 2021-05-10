---
title: デジタル ツインにタグを追加する
titleSuffix: Azure Digital Twins
description: 「デジタル ツインにタグを実装する方法」を参照してください
author: baanders
ms.author: baanders
ms.date: 7/22/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 0d45ef8c32e61b5567798b7c42af28badb222601
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/13/2021
ms.locfileid: "107376717"
---
# <a name="add-tags-to-digital-twins"></a>デジタル ツインにタグを追加する 

タグの概念を使用すると、デジタル ツインをさらに識別して分類することができます。 特に、ユーザーは、Azure Digital Twins インスタンス内の既存のシステム ([Haystack タグ](https://project-haystack.org/doc/TagModel)など) からタグをレプリケートすることができます。 

このドキュメントでは、デジタル ツインにタグを実装するために使用できるパターンについて説明します。

タグは、デジタル ツインを記述する[モデル](concepts-models.md)内のプロパティとして最初に追加されます。 その後、モデルに基づいてツインが作成されるときに、そのプロパティがツインに設定されます。 設定後は、[クエリ](concepts-query-language.md)でタグを使用して、ツインを特定したり、フィルター処理したりできます。

## <a name="marker-tags"></a>マーカー タグ 

**マーカー タグ** は、デジタル ツインをマークまたは分類するために使用される単純な文字列です ("blue"、"red" など)。 この文字列はタグの名前であり、マーカー タグには意味のある値がありません。タグは存在すること (または存在しないこと) によってのみ意味を持ちます。 

### <a name="add-marker-tags-to-model"></a>モデルにマーカー タグを追加する 

マーカー タグは、`string` から `boolean` への [DTDL](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) マップとしてモデル化されます。 タグが存在することのみが重要であるため、ブール `mapValue` は無視されます。 

マーカー タグをプロパティとして実装するツイン モデルの抜粋を次に示します。

:::code language="json" source="~/digital-twins-docs-samples/models/tags.json" range="2-16":::

### <a name="add-marker-tags-to-digital-twins"></a>デジタルツインにマーカー タグを追加する

`tags` プロパティがデジタル ツインのモデルの一部となったら、このプロパティの値を設定することによって、デジタル ツインにマーカー タグを設定できます。 

3 つのツインにマーカー `tags` を設定する例を次に示します。

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_other.cs" id="TagPropertiesMarker":::

[.NET SDK](/dotnet/api/overview/azure/digitaltwins/client) を使用してツインにマーカー `tags` を設定する方法のコード例を次に示します。

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_other.cs" id="TagPropertiesCsharp":::

### <a name="query-with-marker-tags"></a>マーカー タグを使用したクエリ

デジタル ツインにタグを追加したら、タグを使用してクエリでツインをフィルター処理できます。 

"Red" とタグ付けされているすべてのツインを取得するクエリを次に示します。 

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryMarkerTags1":::

さらに複雑なクエリの場合は、タグを組み合わせることもできます。 red ではなく、round であるすべてのツインを取得するクエリを次に示します。 

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryMarkerTags2":::

## <a name="value-tags"></a>値タグ 

**値タグ** は、各タグに値を設定するために使用されるキーと値のペアです (`"color": "blue"`、`"color": "red"` など)。 値タグが作成されたら、タグの値を無視することによって、マーカー タグとして使用することもできます。 

### <a name="add-value-tags-to-model"></a>モデルに値タグを追加する 

値タグは、`string` から `string` への [DTDL](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) マップとしてモデル化されます。 `mapKey` と `mapValue` の両方が意味を持ちます。 

値タグをプロパティとして実装するツイン モデルの抜粋を次に示します。

:::code language="json" source="~/digital-twins-docs-samples/models/tags.json" range="17-31":::

### <a name="add-value-tags-to-digital-twins"></a>デジタル ツインに値タグを追加する

マーカー タグの場合と同様に、デジタル ツインに値タグを設定するには、モデルからこの `tags` プロパティの値を設定します。 値タグをマーカー タグとして使用するには、`tagValue` フィールドを空の文字列値 (`""`) に設定します。 

3 つのツインに値 `tags` を設定する例を次に示します。

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_other.cs" id="TagPropertiesValue":::

この例では、`red` と `purple` がマーカー タグとして使用されていることに注意してください。

### <a name="query-with-value-tags"></a>値タグを使用したクエリ

マーカー タグの場合と同様に、値タグを使用してクエリでツインをフィルター処理できます。 値タグとマーカー タグを一緒に使用することもできます。

上記の例では、`red` がマーカー タグとして使用されています。 これは、"red" とタグ付けされているすべてのツインを取得するクエリであることに注意してください。 

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryMarkerTags1":::

red ではなく、small (値タグ) であるすべてのエンティティを取得するクエリを次に示します。 

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryMarkerValueTags":::

## <a name="next-steps"></a>次のステップ

デジタル ツイン モデルの設計と管理の詳細については、次の記事を参照してください。
* [*方法: DTDL モデルの管理*](how-to-manage-model.md)に関する記事

ツイン グラフに対するクエリ実行の詳細については、次の記事を参照してください。
* [*方法: ツイン グラフに対してクエリを実行する*](how-to-query-graph.md)
