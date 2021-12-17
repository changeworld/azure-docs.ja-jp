---
title: Azure Digital Twins クエリ言語リファレンス - FROM 句
titleSuffix: Azure Digital Twins
description: Azure Digital Twins クエリ言語の FROM 句のリファレンス ドキュメント
author: baanders
ms.author: baanders
ms.date: 03/31/2021
ms.topic: article
ms.service: digital-twins
ms.openlocfilehash: 3bbd9fb23d970173e486cc721e0e2573603287a8
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130232851"
---
# <a name="azure-digital-twins-query-language-reference-from-clause"></a>Azure Digital Twins クエリ言語リファレンス: FROM 句

このドキュメントには、[Azure Digital Twins クエリ言語](concepts-query-language.md)の **FROM 句** に関するリファレンス情報が含まれています。

FROM 句は、クエリの 2 番目の部分です。 コレクションと、クエリで操作する結合 (ある場合) を指定します。

この句は、すべてのクエリで必要になります。

## <a name="select--from-digitaltwins"></a>SELECT ... FROM DIGITALTWINS

`FROM DIGITALTWINS` (大文字と小文字は区別されません) を使用して、インスタンス内のデジタル ツインのコレクション全体を参照します。

必要に応じて、ステートメントの末尾に名前を追加することで、デジタル ツインのコレクションに名前を追加できます。

### <a name="syntax"></a>構文

基本: 

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="FromDigitalTwinsSyntax":::

コレクションに名前を付けるには:

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="FromDigitalTwinsNamedSyntax":::

### <a name="examples"></a>例

ここで示すのは、基本的なクエリです。 次のクエリでは、インスタンス内のすべてのデジタル ツインが返されます。 

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="FromDigitalTwinsExample":::

ここで示すのは、名前付きコレクションを使用したクエリです。 次のクエリは、名前 `T` をコレクションに割り当てますが、それでもインスタンス内のすべてのデジタル ツインを返します。

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="FromDigitalTwinsNamedExample":::

## <a name="select--from-relationships"></a>SELECT ... FROM RELATIONSHIPS

`FROM RELATIONSHIPS` (大文字と小文字は区別されません) を使用して、インスタンス内のリレーションシップのコレクション全体を参照します。

必要に応じて、ステートメントの末尾に名前を追加することで、リレーションシップのコレクションに名前を追加できます。

>[!NOTE]
> この機能は `JOIN` と組み合わせることはできません。

### <a name="syntax"></a>構文

基本: 

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="FromRelationshipsSyntax":::

コレクションに名前を付けるには:

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="FromRelationshipsNamedSyntax":::

### <a name="examples"></a>例

ここで示すのは、インスタンス内のすべてのリレーションシップを返すクエリです。 

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="FromRelationshipsExample":::

ここで示すのは、ツイン `A`、`B`、`C`、または `D` を起点とするすべてのリレーションシップを返すクエリです。

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="FromRelationshipsFilteredExample":::

## <a name="using-from-and-join-together"></a>FROM と JOIN の併用

`FROM` 句を `JOIN` 句と組み合わせて、Azure Digital Twins グラフでエンティティ間のトラバーサルを表すことができます。

`JOIN` 句、およびグラフ トラバーサル クエリの作成の詳細については、[Azure Digital Twins クエリ言語リファレンス: JOIN 句](reference-query-clause-join.md)に関するページを参照してください。

## <a name="limitations"></a>制限事項

次の制限は、`FROM` を使用したクエリに適用されます。
* [サブクエリなし](#no-subqueries)
* [FROM RELATIONSHIPS または JOIN を選択](#choose-from-relationships-or-join)

詳細については、次のセクションを参照してください。

### <a name="no-subqueries"></a>サブクエリなし

`FROM` ステートメント内ではサブクエリはサポートされていません。

#### <a name="example-negative"></a>例 (否定的)

次のクエリは、この制限のために実行 **できない** ことの例を示しています。

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="FromNegativeExample":::

### <a name="choose-from-relationships-or-join"></a>FROM RELATIONSHIPS または JOIN を選択

`FROM RELATIONSHIPS` 機能は `JOIN` と組み合わせることはできません。 これらのオプションのうち、抽出したい情報に最も適したものを選択する必要があります。


