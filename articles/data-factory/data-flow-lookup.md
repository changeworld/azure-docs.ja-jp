---
title: マッピング データ フローでの参照変換
description: マッピング データ フローで参照変換を使用して、別のソースからデータを参照します。
author: kromerm
ms.reviewer: daperlov
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/23/2020
ms.openlocfilehash: 78c6c1363af011a90865770d88c0037e50e958c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240401"
---
# <a name="lookup-transformation-in-mapping-data-flow"></a>マッピング データ フローでの参照変換

参照変換を使用して、データ フロー ストリーム内の別のソースからデータを参照します。 参照変換では、一致したデータの列がソース データに追加されます。

参照変換は、左外部結合と似ています。 プライマリ ストリームのすべての行が出力ストリームに存在し、それに参照ストリームからの列が追加されます。 

## <a name="configuration"></a>構成

![参照変換](media/data-flow/lookup1.png "参照")

**[Primary stream]\(プライマリ ストリーム\):** データの受信ストリーム。 このストリームは、結合の左側に相当します。

**[Lookup stream]\(参照ストリーム\):** プライマリ ストリームに追加されるデータ。 どのデータが追加されるかは、参照条件によって決まります。 このストリームは、結合の右側に相当します。

**[Match multiple rows]\(複数の行の一致\):** 有効にすると、プライマリ ストリームに複数の一致がある行で、複数の行が返されます。 それ以外の場合は、[Match on]\(一致対象\) 条件に基づいて 1 行だけが返されます。

**[Match on]\(一致対象\):** [Match multiple rows]\(複数の行の一致\) が有効になっている場合にのみ表示されます。 任意の行、最初の一致、または最後の一致のいずれと一致するかを選択します。 実行が最も速いので、任意の行をお勧めします。 最初の行または最後の行を選択する場合は、並べ替え条件を指定する必要があります。

**[Lookup conditions]\(参照条件\):** 一致対象の列を選択します。 等値条件が満たされた場合、行は一致と見なされます。 [データ フロー式言語](data-flow-expression-functions.md)を使用して値を抽出するには、ポイントして [Computed column]\(計算列\) を選択します。

参照変換では、等値一致のみがサポートされています。 "より大きい" などの他の演算子を含むように参照式をカスタマイズするには、[結合変換のクロス結合](data-flow-join.md#custom-cross-join)を使用することをお勧めします。 クロス結合を使用すると、実行時に発生する可能性のあるデカルト積エラーを回避できます。

両方のストリームのすべての列が、出力データに含まれます。 重複する列または不要な列を削除するには、参照変換の後に[選択変換](data-flow-select.md)を追加します。 シンク変換で列を削除したり、名前を変更したりすることもできます。

## <a name="analyzing-matched-rows"></a>一致した行の分析

参照変換の後で、`isMatch()` 関数を使用して、参照が個々の行と一致したかどうかを確認できます。

![参照パターン](media/data-flow/lookup111.png "参照パターン")

このパターンの例は、条件分割変換を使用して `isMatch()` 関数で分割する場合です。 上記の例では、一致する行が上のストリームを進み、一致しない行は ```NoMatch``` ストリームを進みます。

## <a name="testing-lookup-conditions"></a>参照条件のテスト

デバッグ モードでデータ プレビューを使用して参照変換のテストを行う場合は、小さな既知のデータ セットを使用してください。 大きなデータセットから行をサンプリングすると、テストでどの行とキーが読み取られるのかを予測できなくなります。 結果が確定的なものとならず、結合条件で一致するものが返されなくなる可能性があります。

## <a name="broadcast-optimization"></a>ブロードキャストの最適化

Azure Data Factory では、マッピング データ フローはスケールアウト Spark 環境で実行されます。 データセットがワーカー ノードのメモリ領域に収まる場合、ブロードキャストを有効にすることにより、参照のパフォーマンスを最適化できます。

![ブロードキャスト結合](media/data-flow/broadcast.png "ブロードキャスト結合")

ブロードキャストを有効にすると、データセット全体がメモリにプッシュされます。 数千行しか含まれない小さなデータセットの場合、ブロードキャストを使用すると、参照のパフォーマンスを大幅に向上させることができます。 大きいデータセットでは、このオプションを使用すると、メモリ不足の例外が発生する可能性があります。

## <a name="data-flow-script"></a>データ フローのスクリプト

### <a name="syntax"></a>構文

```
<leftStream>, <rightStream>
    lookup(
        <lookupConditionExpression>,
        multiple: { true | false },
        pickup: { 'first' | 'last' | 'any' },  ## Only required if false is selected for multiple
        { desc | asc }( <sortColumn>, { true | false }), ## Only required if 'first' or 'last' is selected. true/false determines whether to put nulls first
        broadcast: { 'none' | 'left' | 'right' | 'both' }
    ) ~> <lookupTransformationName>
```
### <a name="example"></a>例

![参照変換](media/data-flow/lookup-dsl-example.png "参照")

次のコード スニペットには、上記の参照構成に対するデータ フロー スクリプトが含まれています。

```
SQLProducts, DimProd lookup(ProductID == ProductKey,
    multiple: false,
    pickup: 'first',
    asc(ProductKey, true),
    broadcast: 'none')~> LookupKeys
```
## 
次のステップ

* [結合](data-flow-join.md)変換と[存在](data-flow-exists.md)変換はどちらも、複数のストリーム入力を受け取ります
* ```isMatch()``` と共に[条件分割変換](data-flow-conditional-split.md)を使用して、一致する値と一致しない値に行を分割します
