---
title: Azure Digital Twins クエリ言語リファレンス - MATCH 句
titleSuffix: Azure Digital Twins
description: Azure Digital Twins クエリ言語 MATCH 句のリファレンス ドキュメント
author: baanders
ms.author: baanders
ms.date: 10/07/2021
ms.topic: article
ms.service: digital-twins
ms.openlocfilehash: 8c553d0df1f194c1de9f77e21341cb1022f98158
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130269933"
---
# <a name="azure-digital-twins-query-language-reference-match-clause"></a>Azure Digital Twins クエリ言語リファレンス: MATCH 句

このドキュメントには、[Azure Digital Twins クエリ言語](concepts-query-language.md)の **MATCH 句** に関するリファレンス情報が含まれています。

`MATCH` 句は、Azure Digital Twins クエリ言語で [FROM 句](reference-query-clause-from.md)の一部として使用されます。 `MATCH` では、Azure Digital Twins グラフで関係を走査している間に従う必要のあるパターンを指定できます (これは、"変数ホップ" クエリ パターンとも呼ばれます)。

この句はクエリの実行時に省略可能です。

## <a name="core-syntax-match"></a>コア構文: MATCH

`MATCH` では、特定のリレーションシップ条件に基づいて、ホップ数が予測できないツイン間のパスを見つけるあらゆるクエリがサポートされます。 

**リレーションシップ条件** には、次の 1 つまたは複数の詳細情報を含めることができます。
* [リレーションシップ方向](#specify-relationship-direction) (左から右、右から左、または非方向)
* [リレーションシップ名](#specify-relationship-name) (単一の名前または可能性の一覧)
* あるツインから別のツインへの ["ホップ" 数](#specify-number-of-hops) (正確な数または範囲)
* クエリ テキスト内のリレーションシップを表す[クエリ変数の割り当て](#assign-query-variable-to-relationship-and-specify-relationship-properties)。 これにより、リレーションシップのプロパティに基づいてフィルター処理を行うこともできます。

また、`MATCH` 句を使用したクエリでは、参照する少なくとも 1 つのツインについて `$dtId` を指定するために、 [WHERE 句](reference-query-clause-where.md)も使用する必要があります。

>[!NOTE]
>`MATCH` は、クエリ ストアで実行できるすべての `JOIN` クエリのスーパーセットです。

### <a name="syntax"></a>構文

次に基本的な `MATCH` 構文を示します。 

実際の値に置き換える必要のある `MATCH` 句に表示されるプレースホルダー値は、`twin_1`、`relationship_condition`、`twin_2` です。 実際の値に置き換える必要のある `WHERE` 句のプレースホルダー値は、`twin_or_twin_collection` および `twin_ID` です。

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchSyntax":::

そのスポットでどのツイン名でも機能できるように、いずれかのツインの名前を省略できます。

また、リレーションシップ条件の数を変更して、複数の[連鎖](#combine-match-operations)リレーションシップ条件を設定することも、リレーションシップ条件をまったく設定しないこともできます。

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchChainSyntax":::

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchNodeSyntax":::

リレーションシップ条件のそれぞれの種類と組み合わせ方法の詳細については、このドキュメントの他のセクションを参照してください。

### <a name="example"></a>例

`MATCH` を使用したクエリの例を次に示します。

このクエリでは、[リレーションシップ方向](#specify-relationship-direction)を指定し、次の場合の building と sensor のツインを検索します。
* sensor は、`$dtId` が Building21 の building からのすべてのリレーションシップの対象になっており、 
* sensor の温度が 50 度を超えています。
building と sensor は両方ともクエリ結果に含まれます。

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchExample":::

## <a name="specify-relationship-direction"></a>リレーションシップ方向を指定する

`MATCH` 句でリレーションシップ条件を使用して、ツイン間のリレーションシップ方向を指定します。 可能な方向には、左から右、右から左、非方向があります。 循環リレーションシップが自動的に検出され、その結果、リレーションシップは 1 回だけ走査されます。

> [!NOTE]
> [連鎖](#combine-match-operations)を使用して双方向リレーションシップを表すことができます。

### <a name="syntax"></a>構文

>[!NOTE]
> このセクションの例では、リレーションシップ方向に焦点を当てます。 リレーションシップ名は指定されず、既定で 1 つのホップに設定され、クエリ変数はリレーションシップに割り当てられていません。 他の条件を使用してその他の操作を行う手順については、「[リレーションシップ名を指定する](#specify-relationship-name)」、「[ホップ数を指定する](#specify-number-of-hops)」、「[クエリ変数をリレーションシップに割り当てる](#assign-query-variable-to-relationship-and-specify-relationship-properties)」を参照してください。 これらのいくつかを同じクエリで同時に使用する方法については、「[MATCH 操作を結合する](#combine-match-operations)」を参照してください。

方向付けされたリレーションシップの説明では、矢印の外形を使用して、リレーションシップ方向を示します。 矢印には、省略可能な[リレーションシップ名](#specify-number-of-hops)用に角かっこ (`[]`) で確保されたスペースが含まれます。 

このセクションでは、リレーションシップのさまざまな方向の構文について説明します。 実際の値に置き換える必要のあるプレースホルダー値は、`source_twin` および `target_twin` です。

**左から右** のリレーションシップの場合は、次の構文を使用します。

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchDirectionLRSyntax":::

**右から左** のリレーションシップの場合は、次の構文を使用します。

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchDirectionRLSyntax":::

**非方向** のリレーションシップの場合は、次の構文を使用します。 この場合、リレーションシップ方向が指定されないため、結果にはあらゆる方向のリレーションシップが含まれます。

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchDirectionNDSyntax":::

>[!TIP]
>非方向のクエリでは、追加の処理が必要になるため、待機時間とコストが増加する可能性があります。

### <a name="examples"></a>例

最初の例では、**左から右** 方向のトラバーサルが示されています。 このクエリでは、次の場合の *room* と *factory* のツインが検索されます。
* *room* の対象が *factory* になっています (リレーションシップ名は任意)
* *room* の温度値が 50 度を超えています
* *factory* の `$dtId` は 'ABC' です

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchDirectionLRExample":::

次の例では、**右から左** 方向のトラバーサルが示されています。 このクエリは上の例と同じように見えますが、*room* と *factory* の間のリレーションシップの方向が逆になっています。 このクエリでは、次の場合の *room* と *factory* のツインが検索されます。
* *factory* の対象が *room* になっています (リレーションシップ名は任意)
* *factory* の `$dtId` は 'ABC' です
* *room* の温度値が 50 度を超えています

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchDirectionRLExample":::

次の例では、**非方向** のトラバーサルが示されています。 このクエリでは、次の場合の *room* と *factory* のツインが検索されます。
* *room* と *factory* は、いずれかの方向である、任意の名前のリレーションシップを共有しています
* *factory* の `$dtId` は 'ABC' です
* *room* の湿度値が 70 を超えています

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchDirectionNDExample":::

## <a name="specify-relationship-name"></a>リレーションシップ名を指定する

必要に応じて、`MATCH` 句でリレーションシップ条件を使用して、ツイン間のリレーションシップの名前を指定できます。 1 つの名前を指定することも、使用可能な名前の一覧を指定することもできます。 オプションのリレーションシップ名は、[リレーションシップ方向を指定する矢印構文](#specify-relationship-direction)の一部に含まれています。

リレーションシップ名を指定しない場合、既定で、すべてのリレーションシップ名がクエリに含まれます。

>[!TIP]
>クエリでリレーションシップ名を指定すると、パフォーマンスが向上し、結果の予測可能性が高まります。

### <a name="syntax"></a>構文

>[!NOTE]
> このセクションの例では、リレーションシップ名に焦点を当てます。 すべて、非方向のリレーションシップが示されており、既定で 1 つのホップに設定され、クエリ変数はリレーションシップに割り当てられていません。 他の条件を使用してその他の操作を行う手順については、「[リレーションシップ方向を指定する](#specify-relationship-direction)」、「[ホップ数を指定する](#specify-number-of-hops)」、「[クエリ変数をリレーションシップに割り当てる](#assign-query-variable-to-relationship-and-specify-relationship-properties)」を参照してください。 これらのいくつかを同じクエリで同時に使用する方法については、「[MATCH 操作を結合する](#combine-match-operations)」を参照してください。

`MATCH` 句で走査するリレーションシップの名前を角かっこ (`[]`) で囲んで指定します。 このセクションでは、名前付きリレーションシップを指定する構文を示します。

**単一の名前** には、次の構文を使用します。 実際の値に置き換える必要のあるプレースホルダー値は、`twin_1`、`relationship_name`、`twin_2` です。

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchNameSingleSyntax":::

**複数の使用可能な名前** には、次の構文を使用します。 実際の値に置き換える必要があるプレースホルダー値は、`twin_1`、`relationship_name_option_1`、`relationship_name_option_2`、`twin_2` であり、入力するリレーションシップ名の数について必要に応じてパターンを継続するためのメモを置き換えます。

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchNameMultiSyntax":::

(既定値) 名前を **未指定** のままにするには、次のように名前情報のかっこを空のままにします。

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchNameAllSyntax":::

### <a name="examples"></a>例

次の例に、**単一のリレーション名** を示します。 このクエリでは、次の場合の *building* と *sensor* のツインが検索されます。
* *building* には *sensor* と 'contains' リレーションシップがあります (どちらかの方向)
* *building* の `$dtId` は 'Seattle21' です

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchNameSingleExample":::

次の例に、**複数の使用可能なリレーション名** を示します。 このクエリは上の例と同じように見えますが、結果には複数の使用可能なリレーションシップ名が含まれています。 このクエリでは、次の場合の *building* と *sensor* のツインが検索されます。
* *building* には *sensor* との 'contains' または 'isAssociatedWith' リレーションシップのどちらかがあります (どちらかの方向)
* *building* の `$dtId` は 'Seattle21' です

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchNameMultiExample":::

次の例には、**指定されたリレーションシップ名** がありません。 その結果、任意の名前のリレーションシップがクエリ結果に含まれます。 このクエリでは、次の場合の *building* と *sensor* のツインが検索されます。
* *building* には *sensor* との任意の名前のリレーションシップがあります (どちらかの方向)
* *building* の `$dtId` は 'Seattle21' です

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchNameAllExample":::

## <a name="specify-number-of-hops"></a>ホップ数を指定する

必要に応じて、`MATCH` 句でリレーションシップ条件を使用して、ツイン間のリレーションシップのホップ数を指定できます。 正確な数値または範囲を指定できます。 省略可能な値は、[リレーションシップ方向を指定する矢印構文](#specify-relationship-direction)の一部として含まれています。

ホップ数を指定しない場合、クエリは既定で 1 ホップになります。

>[!IMPORTANT]
>1 を超えるホップ数を指定した場合、[リレーションシップ にクエリ変数を割り当て](#assign-query-variable-to-relationship-and-specify-relationship-properties)られません。 同じクエリ内では、これらの条件の 1 つしか使用できません。 

### <a name="syntax"></a>構文

>[!NOTE]
>このセクションの例では、ホップ数に焦点を当てます。 すべて、名前を指定せずに非方向リレーションシップを示します。 他の条件を使用してその他の操作を行う手順については、「[リレーションシップ方向を指定する](#specify-relationship-direction)」、「[リレーションシップ名を指定する](#specify-relationship-name)」を参照してください。 これらのいくつかを同じクエリで同時に使用する方法については、「[MATCH 操作を結合する](#combine-match-operations)」を参照してください。

`MATCH` 句で走査するホップ数を角かっこ (`[]`) で囲んで指定します。

**正確なホップ数** を指定するには、次の構文を使用します。 実際の値に置き換える必要のあるプレースホルダー値は、`twin_1`、`number_of_hops`、`twin_2` です。

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchHopsExactSyntax":::

**ホップの範囲** を指定するには、次の構文を使用します。 実際の値に置き換える必要のあるプレースホルダー値は、`twin_1`、`starting_limit`、`ending_limit`、`twin_2` です。 開始限度は範囲に **含まれません** が、終了限度は **含まれます**。

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchHopsRangeSyntax":::

また、開始限度を省略して、終了限度 "まで" (込み) を示すこともできます。 終了限度は常に指定する必要があります。

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchHopsRangeEndingSyntax":::

(既定値) 既定で **1 つのホップ** に設定するには、次のようにホップ情報のかっこを空のままにします。

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchHopsOneSyntax":::

### <a name="examples"></a>例

次の例では、**正確なホップ数** を指定します。 このクエリでは、ホップ数がちょうど 3 である *floor* と *room* のツイン間のリレーションシップだけを返します。

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchHopsExactExample":::

次の例では、**ホップの範囲** を指定します。 このクエリでは、ホップ数が 1 から 3 である (つまりホップ数は 2 か 3) *floor* と *room* のツイン間のリレーションシップを返します。

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchHopsRangeExample1":::

1 つの境界のみを指定して範囲を表示することもできます。 次の例では、クエリにより、ホップ数が 2 以下である (つまりホップ数は 1 か 2) *floor* と *room* のツイン間のリレーションシップが返されます。

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchHopsRangeEndingExample":::

次の例では、ホップ数が指定されていないので、*floor* と *room* のツイン間は既定で **1 ホップ** に設定されます。

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchHopsOneExample":::

## <a name="assign-query-variable-to-relationship-and-specify-relationship-properties"></a>クエリ変数をリレーションシップに割り当てる (およびリレーションシップのプロパティを指定する)

必要に応じて、`MATCH` 句で参照されているリレーションシップにクエリ変数を割り当てることができ、その結果、クエリ テキスト内でそれを名前で参照できます。

このようにすると、`WHERE` 句でリレーションシップのプロパティに基づいてフィルター処理する機能で役立ちます。

>[!IMPORTANT]
> リレーションシップへのクエリ変数の割り当ては、クエリで単一ホップが指定されている場合にのみサポートされます。 クエリ内では、リレーションシップ変数を指定するか、[さらに大きなホップ数を指定するか](#specify-number-of-hops)を選択する必要があります。

### <a name="syntax"></a>構文

>[!NOTE]
>このセクションの例では、リレーションシップのクエリ変数に焦点を当てます。 すべて、名前を指定せずに非方向リレーションシップを示します。 他の条件を使用してその他の操作を行う手順については、「[リレーションシップ方向を指定する](#specify-relationship-direction)」、「[リレーションシップ名を指定する](#specify-relationship-name)」を参照してください。 これらのいくつかを同じクエリで同時に使用する方法については、「[MATCH 操作を結合する](#combine-match-operations)」を参照してください。

リレーションシップにクエリ変数を割り当てるには、変数名を角かっこ (`[]`) で囲みます。 実際の値に置き換える必要のある下記のプレースホルダー値は、`twin_1`、`relationship_variable`、`twin_2` です。

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchVariableSyntax":::

### <a name="examples"></a>例

次の例では、クエリ変数 'r' をリレーションシップに割り当てます。 後から `WHERE` 句で、変数を使用して、リレーションシップ *rel* に、値が 'child' の name プロパティが必要であることを指定します。

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchVariableExample":::

## <a name="combine-match-operations"></a>MATCH 操作を結合する

同じクエリ内で複数のリレーションシップ条件を結合できます。 また、複数のリレーションシップ条件を連鎖して、双方向リレーションシップや他のより大きな組み合わせを表現できます。

### <a name="syntax"></a>構文

単一のクエリで、[リレーションシップ方向](#specify-relationship-direction)、[リレーションシップ名](#specify-number-of-hops)、[ホップ数](#specify-number-of-hops)または [クエリ変数の割り当て](#assign-query-variable-to-relationship-and-specify-relationship-properties)の **いずれか** を組み合わせられます。

次の構文例は、これらの属性を組み合わせる方法を示します。 また、プレースホルダーに表示される省略可能な任意の詳細を除外して、条件のその部分を省略できます。

単一のクエリ内で **リレーションシップ方向、リレーションシップ名、ホップ数** を指定するには、リレーションシップ条件内で次の構文を使用します。 実際の値に置き換える必要のあるプレースホルダー値は、`twin_1` と `twin_2`、`optional_left_angle_bracket` と `optional_right_angle_bracket`、`relationship_name(s)`、`number_of_hops` です。

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchCombinedHopsSyntax":::

単一のクエリ内で **リレーションシップ方向、リレーションシップ名、リレーションシップのクエリ変数** を指定するには、リレーションシップ条件内で次の構文を使用します。 実際の値に置き換える必要のあるプレースホルダー値は、`twin_1` と `twin_2`、`optional_left_angle_bracket` と `optional_right_angle_bracket`、`relationship_variable`、`relationship_name(s)` です。

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchCombinedVariableSyntax":::

>[!NOTE]
>[リレーションシップ方向を指定](#specify-relationship-direction)するためのオプションに従って、左から右のリレーションシップの場合は左山かっこを、右から左のリレーションシップの場合は右山かっこを選択する必要があります。 両方を同じ矢印に含めることはできませんが、連鎖させることにより双方向リレーションシップを表すことができます。

次のように複数のリレーションシップ条件を **連鎖** できます。 実際の値に置き換える必要のあるプレースホルダー値は、`twin_1`、`relationship_condition` のすべてのインスタンス、`twin_2` です。

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchChainSyntax":::

### <a name="examples"></a>例

**リレーションシップ方向、リレーションシップ名、ホップ数** を組み合わせた例を次に示します。 次のクエリは、*floor* と *room* のツインを検索します。ここでは、*floor* と *room* のリレーションシップは次の条件を満たします。
* リレーションシップが左から右であり、*floor* のターゲットが *room* になっています
* リレーションシップの名前は 'contains' と 'isAssociatedWith' のどちらかです
* リレーションシップのホップ数は 4 または 5 です

また、このクエリでは、ツイン *floor* の `$dtId` が 'thermostat-15' であることも指定されています。

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchCombinedHopsExample":::

**リレーションシップ方向、リレーションシップ名、リレーションシップの名前付きクエリ変数** を組み合わせた例を次に示します。 次のクエリは、*floor* と *room* のツインを検索します。ここでは、*floor* と *room* のリレーションシップは、クエリ変数 *r* に割り当てられ、次の条件を満たします。
* リレーションシップが左から右であり、*floor* のターゲットが *room* になっています
* リレーションシップの名前は 'contains' と 'isAssociatedWith' のどちらかです
* リレーションシップには、クエリ変数 *r* が指定され、10 に等しい長さプロパティがあります

また、このクエリでは、ツイン *floor* の `$dtId` が 'thermostat-15' であることも指定されています。

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchCombinedVariableExample":::

次の例は、**連鎖** したリレーションシップ条件を示しています。 このクエリでは、次の場合の *floor*、*cafe*、*room* のツインが検索されます。
* *floor* と *room* の間のリレーションシップが次の条件を満たしています。
    - *floor* をソース、*cafe* をターゲットとした左から右へのリレーションシップです
    - リレーションシップの名前は 'contains' と 'isAssociatedWith' のどちらかです
    - クエリ変数 *r* が指定されたリレーションシップに、10 に等しい長さプロパティがあります
* 次のクエリは、*cafe* と *room* のリレーションシップは次の条件を満たします。
    - *room* をソース、*cafe* をターゲットとした左から右へのリレーションシップです
    - リレーションシップの名前は 'has' と 'includes' のどちらかです
    - リレーションシップのホップ数は最大 3 (つまり、1、2、または 3) です

また、このクエリでは、ツイン *floor* の `$dtId` が 'thermostat-15' であり、ツイン *cafe* の温度が 55 度であることも指定されます。

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchCombinedChainExample":::

また、連鎖リレーションシップ条件を使用して、**双方向リレーションシップ** を表現できます。 次のクエリは、次の場合の *floor*、*room*、*building* のツインを検索します。
* *building* と *floor* の間のリレーションシップが次の条件を満たしています。
    - *building* をソース、*floor* をターゲットとした左から右へのリレーションシップです
    - リレーションシップの名前は 'isAssociatedWith' です
    - リレーションシップにはクエリ変数 *r1* が指定されています
* *floor* と *room* の間のリレーションシップが次の条件を満たしています。
    - *room* をソース、*floor* をターゲットとした左から右へのリレーションシップです
    - リレーションシップの名前は 'isAssociatedWith' です
    - リレーションシップにはクエリ変数 *r2* が指定されています

また、このクエリでは、ツイン *building* の `$dtId` が 'building-3' であり、ツイン *room* の温度が 50 度を超えていることも指定されます。

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchCombinedChainBDExample":::

## <a name="limitations"></a>制限事項

次の制限は、`MATCH` を使用したクエリに適用されます。
* クエリ ステートメントごとにサポートされる `MATCH` 式は 1 つのみです
* `$dtId` は `WHERE` 句で必須です
* リレーションシップへのクエリ変数の割り当ては、クエリで単一ホップが指定されている場合にのみサポートされます
* クエリでサポートされる最大ホップ数は 10 です