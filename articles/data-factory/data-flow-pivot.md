---
title: マッピング データ フローでのピボット変換
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory と Synapse Analytics パイプラインでマッピング データ フロー ピボット変換を使用して、行から列にデータをピボットします。
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.custom: synapse
ms.date: 09/09/2021
ms.openlocfilehash: c6aa95864ad12d5776a8e90eb2b1bd8dac1f04a2
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/26/2021
ms.locfileid: "129059931"
---
# <a name="pivot-transformation-in-mapping-data-flow"></a>マッピング データ フローでのピボット変換

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

[!INCLUDE[data-flow-preamble](includes/data-flow-preamble.md)]

ピボット変換を使用して、1 つの列の一意の行値から複数の列を作成します。 ピボットは、グループ化列を選択し、[集計関数](data-flow-expression-functions.md#aggregate-functions)を使用してピボット列を生成する集計変換です。

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4C4YN]

## <a name="configuration"></a>構成

ピボット変換には、グループ化列、ピボット キー、およびピボットされた列の生成方法という 3 つの異なる入力が必要です

### <a name="group-by"></a>グループ化

:::image type="content" source="media/data-flow/pivot2.png" alt-text="オプションでグループ化する":::

ピボットされた列を集計する列を選択します。 出力データは、同じグループ化の値を持つすべての行を 1 つの行にグループ化します。 ピボットされた列で行われる集計は、各グループに対して行われます。

このセクションは省略可能です。 グループ化列が選択されていない場合は、データ ストリーム全体が集計され、1 つの行だけが出力されます。

### <a name="pivot-key"></a>ピボット キー

:::image type="content" source="media/data-flow/pivot3.png" alt-text="ピボット キー":::

ピボット キーは、行の値が新しい列にピボットされる列です。 既定では、ピボット変換によって、一意の行値ごとに新しい列が作成されます。

**[Value]\(値\)** というラベルが付いたセクションには、ピボットする特定の行値を入力できます。 このセクションに入力された行値だけがピボットされます。 **[Null value]\(Null 値\)** を有効にすると、列の null 値に対するピボットされた列が作成されます。

### <a name="pivoted-columns"></a>ピボットされた列

:::image type="content" source="media/data-flow/pivot4.png" alt-text="ピボットされた列":::

列になる一意のピボット キー値ごとに、各グループの集計された行値を生成します。 ピボット キーごとに複数の列を作成できます。 各ピボット列には、少なくとも 1 つの[集計関数](data-flow-expression-functions.md#aggregate-functions)が含まれている必要があります。

**[Column name pattern]\(列名のパターン\):** 各ピボット列の列名の書式を設定する方法を選択します。 出力された列名は、ピボット キー値、列プレフィックス、および省略可能なプレフィックス、サフィックス、中間文字を組み合わせたものになります。 

**[Column arrangement]\(列の配置\):** ピボット キーごとに複数のピボット列を生成する場合は、列の順序付け方法を選択します。 

**[Column prefix]\(列プレフィックス\):** ピボット キーごとに複数のピボット列を生成する場合は、各列の列プレフィックスを入力します。 ピボットされた列が 1 つしかない場合、この設定は省略可能です。

## <a name="help-graphic"></a>ヘルプ グラフィック

次のヘルプ グラフィックは、さまざまなピボット コンポーネントがどのように相互に作用しているかを示しています。

:::image type="content" source="media/data-flow/pivot5.png" alt-text="ピボットのヘルプ グラフィック":::

## <a name="pivot-metadata"></a>ピボットのメタデータ

ピボット キー構成に値が指定されていない場合、ピボットされた列は実行時に動的に生成されます。 ピボットされた列の数は、一意のピボット キー値の数にピボット列の数を乗算した値と等しくなります。 この数は変わる可能性があるため、UX は **[Inspect]\(検査\)** タブに列のメタデータを表示せず、列の反映は行われません。 これらの列を変換するには、マッピング データ フローの[列パターン](concepts-data-flow-column-pattern.md)機能を使用します。 

特定のピボット キー値が設定されている場合、ピボットされた列はメタデータに表示されます。 列名は、検査とシンク マッピングで利用可能になります。

### <a name="generate-metadata-from-drifted-columns"></a>誤差のある列からメタデータを生成する

ピボットでは、行の値に基づいて動的に新しい列名が生成されます。 これらの新しい列は、後でデータ フローで参照できるメタデータに追加できます。 これを行うには、データ プレビューで [[Map Drifted]\(誤差のマップ\)](concepts-data-flow-schema-drift.md#map-drifted-columns-quick-action) クイック アクションを使用します。 

:::image type="content" source="media/data-flow/newpivot1.png" alt-text="列のピボット":::

### <a name="sinking-pivoted-columns"></a>ピボットされた列のシンク

ピボットされた列は動的ですが、宛先のデータ ストアに書き込むことができます。 シンク設定で、 **[Allow Schema Drift]\(スキーマの誤差を許可\)** を有効にします。 これにより、メタデータに含まれていない列を書き込むことができます。 列のメタデータ内の新しい動的な名前は表示されませんが、スキーマの誤差のオプションによってデータを取得することができます。

### <a name="rejoin-original-fields"></a>元のフィールドを再結合する

ピボット変換では、グループ化列とピボットされた列だけが射影されます。 出力データに他の入力列を含めたい場合は、[自己結合](data-flow-join.md#self-join)パターンを使用します。

## <a name="data-flow-script"></a>データ フローのスクリプト

### <a name="syntax"></a>構文

```
<incomingStreamName>
    pivot(groupBy(Tm),
        pivotBy(<pivotKeyColumn, [<specifiedColumnName1>,...,<specifiedColumnNameN>]),
        <pivotColumnPrefix> = <pivotedColumnValue>,
        columnNaming: '< prefix >< $N | $V ><middle >< $N | $V >< suffix >',
        lateral: { 'true' | 'false'}
    ) ~> <pivotTransformationName
```
### <a name="example"></a>例

構成セクションに表示される画面には、次のデータ フロー スクリプトがあります。

```
BasketballPlayerStats pivot(groupBy(Tm),
    pivotBy(Pos),
    {} = count(),
    columnNaming: '$V$N count',
    lateral: true) ~> PivotExample

```

## <a name="next-steps"></a>次のステップ

列の値を行の値に変換する[ピボット解除変換](data-flow-unpivot.md)を試します。 
