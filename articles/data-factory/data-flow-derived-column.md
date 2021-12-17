---
title: マッピング データ フローの派生列変換
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory および Azure Synapse Analytics で、マッピング データ フローの派生列変換を使用して、大規模にデータを変換する方法について説明します。
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.custom: synapse
ms.date: 09/09/2021
ms.openlocfilehash: 51656330a0d849ed61d7f1cae88f7bbfc0610c49
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/26/2021
ms.locfileid: "129058787"
---
# <a name="derived-column-transformation-in-mapping-data-flow"></a>マッピング データ フローの派生列変換

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

[!INCLUDE[data-flow-preamble](includes/data-flow-preamble.md)]

データ フロー内に新しい列を生成したり、既存のフィールドを変更したりするには、派生列変換を使用します。

## <a name="create-and-update-columns"></a>列を作成および更新する

派生列を作成するときは、新しい列を生成するか、既存の列を更新することができます。 **[列]** ボックスに、作成する列を入力します。 スキーマ内の既存の列を上書きするには、列ドロップダウンを使用できます。 派生列の式を作成するには、 **[式の入力]** ボックスをクリックします。 式の入力を開始するか、式ビルダーを開いてロジックを作成することができます。

:::image type="content" source="media/data-flow/create-derive-column.png" alt-text="派生列変換の設定":::

派生列をさらに追加するには、列リストの上にある **[追加]** をクリックするか、既存の派生列の横にあるプラス記号のアイコンをクリックします。 **[列の追加]** または **[列パターンの追加]** のいずれかを選択します。

:::image type="content" source="media/data-flow/add-derived-column.png" alt-text="新しい派生列の選択":::

### <a name="column-patterns"></a>列パターン

スキーマが明示的に定義されていない場合、または列のセットを一括して更新する場合は、列パターンを作成する必要があります。 列パターンを使用すると、列のメタデータに基づくルールを使用して列を照合し、一致した各列に対して派生列を作成できます。 詳細については、派生列の変換で[列パターンの作成方法](concepts-data-flow-column-pattern.md#column-patterns-in-derived-column-and-aggregate)に関する説明を参照してください。

:::image type="content" source="media/data-flow/column-pattern-derive.png" alt-text="列パターン":::

## <a name="building-schemas-using-the-expression-builder"></a>式ビルダーを使用してスキーマを作成する

マッピング データ フローの [式ビルダー](concepts-data-flow-expression-builder.md)を使用すると、 **[Derived Columns]\(派生列\)** セクションで派生列を作成、編集、管理できます。 変換で作成または変更されたすべての列の一覧が表示されます。 列の名前をクリックして、編集している列またはパターンを対話形式で選択します。 列を追加するには、 **[新規作成]** を選択し、1 つの列またはパターンのどちらを追加するかを選択します。

:::image type="content" source="media/data-flow/derive-add-column.png" alt-text="新しい列を作成する":::

複合列を使用する場合は、サブ列を作成できます。 これを行うには、任意の列の横にあるプラス アイコンをクリックし、 **[Add subcolumn]\(サブ列の追加\)** を選択します。 データ フローでの複合型の処理の詳細については、[マッピング データ フローでの JSON の処理](format-json.md#mapping-data-flow-properties)に関する記事を参照してください。

:::image type="content" source="media/data-flow/derive-add-subcolumn.png" alt-text="サブ列の追加":::

データ フローでの複合型の処理の詳細については、[マッピング データ フローでの JSON の処理](format-json.md#mapping-data-flow-properties)に関する記事を参照してください。

:::image type="content" source="media/data-flow/derive-complex-column.png" alt-text="複合列の追加":::

### <a name="locals"></a>ローカル

複数の列でロジックを共有する場合、またはロジックをコンパートメント化する場合は、派生列変換の内部にローカルを作成できます。 ローカルは、次の変換へとダウンストリームに伝達されないロジックのセットです。 式ビルダーでローカルを作成するには、 **[Expression elements]\(式の要素\)** に移動して、 **[Locals]\(ローカル\)** を選択します。 **[新規作成]** を選択して、新しく作成します。

:::image type="content" source="media/data-flow/create-local.png" alt-text="ローカルを作成する":::

ローカルでは、関数、入力スキーマ、パラメーター、他のローカルなど、派生列の任意の式要素を参照できます。 他のローカルを参照する場合は順序が重要であり、参照されるローカルが現在のローカルより "上" にある必要があります。

:::image type="content" source="media/data-flow/create-local-2.png" alt-text="ローカルを作成する 2":::

派生列内でローカルを参照するには、 **[Expression elements]\(式の要素\)** ビューでローカルをクリックするか、名前の前にコロンを付けて参照します。 たとえば、local1 という名前のローカルは、`:local1` によって参照されます。 ローカル定義を編集するには、式要素ビューでそれをポイントし、鉛筆アイコンをクリックします。

:::image type="content" source="media/data-flow/using-locals.png" alt-text="ローカルの使用":::

## <a name="data-flow-script"></a>データ フローのスクリプト

### <a name="syntax"></a>構文

```
<incomingStream>
    derive(
           <columnName1> = <expression1>,
           <columnName2> = <expression2>,
           each(
                match(matchExpression),
                <metadataColumn1> = <metadataExpression1>,
                <metadataColumn2> = <metadataExpression2>
               )
          ) ~> <deriveTransformationName>
```

### <a name="example"></a>例

次の例は、受信ストリーム `MoviesYear` を受け取り 2 つの派生列を作成する、`CleanData` という名前の派生列です。 最初の派生列は、列 `Rating` を整数型の Rating の値で置き換えます。 2 番目の派生列は、名前が 'movies' で始まる各列に一致するパターンです。 一致した列ごとに、一致する列の値と等しい列 `movie` を、'movie_' というプレフィックスを付けて作成します。 

この UI では、この変換は次の図のようになります。

:::image type="content" source="media/data-flow/derive-script.png" alt-text="派生の例":::

この変換のデータ フロー スクリプトは、次のスニペットに含まれています。

```
MoviesYear derive(
                Rating = toInteger(Rating),
                each(
                    match(startsWith(name,'movies')),
                    'movie' = 'movie_' + toString($$)
                )
            ) ~> CleanData
```

## <a name="next-steps"></a>次のステップ

- 「[Mapping Data Flow の式言語](data-flow-expression-functions.md)」で詳細を確認します。
