---
title: Azure Digital Twins クエリ言語リファレンス - JOIN 句
titleSuffix: Azure Digital Twins
description: Azure Digital Twins クエリ言語 JOIN 句のリファレンス ドキュメント
author: baanders
ms.author: baanders
ms.date: 03/31/2021
ms.topic: article
ms.service: digital-twins
ms.openlocfilehash: ad9447966916098a5ca0834d593242e365b8a088
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130246758"
---
# <a name="azure-digital-twins-query-language-reference-join-clause"></a>Azure Digital Twins クエリ言語リファレンス: JOIN 句

このドキュメントには、[Azure Digital Twins クエリ言語](concepts-query-language.md)の **JOIN 句** に関するリファレンス情報が含まれています。

`JOIN` 句は、クエリで Azure Digital Twins グラフをトラバーサルする場合に、Azure Digital Twins クエリ言語で [FROM 句](reference-query-clause-from.md)の一部として使用されます。

この句はクエリの実行時に省略可能です。

## <a name="core-syntax-join--related"></a>コア構文: JOIN ... RELATED 
Azure Digital Twins のリレーションシップは、独立したエンティティでなく、デジタル ツインの一部であるため、ツイン コレクションから特定のタイプのリレーションシップのセットを参照するために `JOIN` クエリで `RELATED` キーワードが使用されます (タイプは [DTDL 定義](concepts-models.md#basic-relationship-example)のリレーションシップの **name** フィールドを使用して指定されます)。 リレーションシップのセットには、クエリ内でコレクション名を割り当てることができます。

次に、クエリでは `WHERE` 句を使用して、リレーションシップ クエリをサポートするために使用されている特定のツイン (1 つまたは複数) を指定する必要があります。これは、ソースまたはターゲット ツインの `$dtId` 値をフィルター処理することによって行われます。

### <a name="syntax"></a>構文

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="JoinSyntax":::

### <a name="example"></a>例

次のクエリでは、*contains* リレーションシップを通じて ID が *ABC* のツインと関連しているすべてのデジタル ツインが選択されます。

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="JoinExample":::

## <a name="multiple-joins"></a>複数の JOIN

1 つのクエリで最大 5 つの `JOIN` がサポートされているため、複数レベルのリレーションシップを一度にトラバーサルできます。

### <a name="syntax"></a>構文

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MultiJoinSyntax":::

### <a name="example"></a>例

次のクエリが基づく Room には LightPanel が含まれており、各 LightPanel には複数の LightBulb が含まれています。 このクエリでは、room 1 と 2 の LightPanel に含まれるすべての LightBulb を取得します。

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MultiJoinExample":::

## <a name="limitations"></a>制限事項

次の制限は、`JOIN` を使用したクエリに適用されます。
* [5 の深さ制限](#depth-limit-of-five)
* [OUTER JOIN セマンティクスがない](#no-outer-join-semantics)
* [ソース ツインが必要](#twins-required)

詳細については、次のセクションを参照してください。

### <a name="depth-limit-of-five"></a>5 の深さ制限

グラフ トラバーサルの深さがクエリごとに 5 つの `JOIN` レベルに制限されます。

#### <a name="example"></a>例

次のクエリは、Azure Digital Twins クエリで可能な `JOIN` 句の最大数を示しています。 これにより Buliding1 のすべての LightBulb が取得されます。

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MaxJoinExample":::

### <a name="no-outer-join-semantics"></a>OUTER JOIN セマンティクスがない

`OUTER JOIN` セマンティクスはサポートされていません。つまり、リレーションシップのランクがゼロの場合、"行" 全体が出力結果セットから削除されます。

#### <a name="example"></a>例

Building (建物) のトラバーサルを示す次のクエリについて考えてみましょう。

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="NoOuterJoinExample":::

Building1 に Floor (フロア) が含まれていない場合、このクエリでは (建物の値とフロアの `undefined` を持つ 1 つの行を返す代わりに) 空の結果セットが返されます。

### <a name="twins-required"></a>ツインが必要

Azure Digital Twins のリレーションには、独立したエンティティのようにクエリを実行できません。リレーションシップの発生元になるソース ツインに関する情報も提供する必要があります。 この機能は、`RELATED` キーワードによる Azure Digital Twins の既定の `JOIN` 使用の一部として含まれています。 

`JOIN` 句を使用したクエリでは、`WHERE` 句の任意のツインの `$dtId` プロパティによるフィルター処理もする必要があります。これにより、リレーションシップ クエリをサポートするために使用されているツインが明確になります。