---
title: Conversational Language Understanding のエンティティ コンポーネント
titleSuffix: Azure Cognitive Services
description: Conversational Language Understanding によってテキストからエンティティを抽出する方法について説明します
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-clu, ignite-fall-2021
ms.openlocfilehash: c42028b10a71a039a208f82cfacb58e5ba8e07cc
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132347729"
---
# <a name="entity-components"></a>エンティティ コンポーネント

Conversational Language Understanding では、エンティティは、発話から抽出される関連情報です。 エンティティは、さまざまな方法によって抽出できます。 コンテキストから学習したり、一覧から一致させたり、事前構築済みの認識済みエンティティによって検出したりすることができます。 プロジェクト内のすべてのエンティティは、これらの方法の 1 つ以上で構成されており、これらはエンティティのコンポーネントとして定義されています。 エンティティが複数のコンポーネントによって定義されている場合、それらの予測は重複する可能性があります。 コンポーネントが重複したときのエンティティ予測の動作は、**Overlap メソッド** の固定オプション セットを使用して決定できます。

## <a name="component-types"></a>コンポーネントの種類

エンティティ コンポーネントは、エンティティを抽出する方法を決定します。 エンティティは、エンティティを抽出するための唯一の方法を決定する 1 つのコンポーネントだけを含む場合と、エンティティの定義と抽出の方法を拡張する複数のコンポーネントを含む場合があります。

### <a name="learned-component"></a>Learned コンポーネント

learned コンポーネントは、発話へのラベル付けに使用されたエンティティ タグを使用して、機械学習モデルをトレーニングします。 このモデルは、発話内のコンテキストに基づいて、エンティティがどこにあるかを予測するように学習します。 ラベルは、周囲にある単語の意味に基づき、とラベル付けされた単語として、エンティティが発話内に存在すると予想される場所の例を提供します。 このコンポーネントは、エンティティに対して発話にタグを付けてラベルを追加した場合にのみ定義されます。 エンティティに発話をタグ付けしない場合、そのエンティティには Learned コンポーネントが与えられません。

:::image type="content" source="../media/learned-component.png" alt-text="エンティティの learned コンポーネントの例を示すスクリーンショット。" lightbox="../media/learned-component.png":::

### <a name="list-component"></a>List コンポーネント

list コンポーネントは、固定かつ限定された関連単語セットとその同義語を表します。 このコンポーネントは、同義語として指定された値のリストに対して完全なテキスト一致を実行します。 各同意語は "リスト キー" に属しており、list コンポーネントが一致した場合に出力で返される同意語の正規化された標準値として使用できます。 リスト キーは一致には使用 **されません**。


:::image type="content" source="../media/list-component.png" alt-text="エンティティの list コンポーネントの例を示すスクリーンショット。" lightbox="../media/list-component.png":::

### <a name="prebuilt-component"></a>事前構築済みコンポーネント

事前構築済みコンポーネントを使用すると、数値、日時、名前などの一般的な型をライブラリから選択できます。 追加すると、事前構築済みのコンポーネントが自動的に検出されます。 1 つのエンティティにつき、最大 5 つの事前構築済みコンポーネントを使用できます。 詳細については、[サポートされている構築済みコンポーネントの一覧](../prebuilt-component-reference.md)を参照してください。


:::image type="content" source="../media/prebuilt-component.png" alt-text="エンティティの事前構築済みコンポーネントの例を示すスクリーンショット。" lightbox="../media/prebuilt-component.png":::


## <a name="overlap-methods"></a>Overlap メソッド

1 つのエンティティに対して複数のコンポーネントが定義されている場合、それらの予測が重複する可能性があります。 重複が発生すると、各エンティティの最終的な予測は、次のいずれかのオプションによって決定されます。

### <a name="longest-overlap"></a>最も長い重複

テキストに複数のコンポーネントが見つかり、overlap メソッドが使用されている場合は、**文字のセットが最も長い** コンポーネントが返されます。

このオプションは、さまざまなコンポーネントによる最長の予測を抽出する場合に最適です。 このメソッドにより、混同 (重複) があった場合、返されるコンポーネントが最も長くなることが保証されます。

#### <a name="examples"></a>例

List コンポーネントで "Palm Beach" が一致し、"Palm Beach Extension" が Learned コンポーネントによって予測された場合、"**Palm Beach Extension**" が返されます。これは、これがこの重複の中で最も長い文字セットであるためです。

:::image type="content" source="../media/return-longest-overlap-example-1.svg" alt-text="コンポーネントの最も長い重複結果の例を示すスクリーンショット。" lightbox="../media/return-longest-overlap-example-1.svg":::

List コンポーネントで "Palm Beach" が一致し、"Beach Extension" が Learned コンポーネントによって予測された場合、"**Beach Extension**" が返されます。これは、これがこの重複の中で最も長い文字セットを持つコンポーネントであるためです。

:::image type="content" source="../media/return-longest-overlap-example-2.svg" alt-text="コンポーネントの最も長い重複結果の 2 つ目の例を示すスクリーンショット。" lightbox="../media/return-longest-overlap-example-2.svg":::

List コンポーネントで "Palm Beach" が一致し、Learnedコンポーネントによって "Extension" が予測された場合、**Palm Beach**" と "**Extension**" のエンティティの 2 つの別々のインスタンスが返されます。これは、このインスタンスでは重複が発生していないためです。

:::image type="content" source="../media/return-longest-overlap-example-3.svg" alt-text="コンポーネントの最も長い重複結果の 3 つ目の例を示すスクリーンショット。" lightbox="../media/return-longest-overlap-example-3.svg":::

### <a name="exact-overlap"></a>正確な重複

エンティティが返されるには、すべてのコンポーネントがテキストの **正確に同じ文字** で重複している必要があります。 定義されたコンポーネントの 1 つが一致しない、または予測されない場合、そのエンティティは返されません。

このオプションは、複数のコンポーネントを同時に検出して抽出する必要がある厳密なエンティティがある場合に最適です。

#### <a name="examples"></a>例

list コンポーネントで "Palm Beach" が一致し、learned コンポーネントによって "Palm Beach "が予測され、それらがエンティティに定義された唯一の 2 つのコンポーネントであった場合、"**Palm Beach**" が返されます。これは、すべてのコンポーネントがまったく同じ文字で重複しているためです。

:::image type="content" source="../media/require-exact-overlap-example-1.svg" alt-text="コンポーネントの正確な重複結果の例を示すスクリーンショット。" lightbox="../media/require-exact-overlap-example-1.svg":::

list コンポーネントで "Palm Beach" が一致し、learned コンポーネントによって "Beach Extension" が予測された場合、エンティティは返され **ません**。これは、すべてのコンポーネントがまったく同じ文字で重複していないためです。

:::image type="content" source="../media/require-exact-overlap-example-2.svg" alt-text="コンポーネントの正確な重複結果の 2 つ目の例を示すスクリーンショット。" lightbox="../media/require-exact-overlap-example-2.svg":::

list コンポーネントで "Palm Beach" が一致し、learned コンポーネントによって "Extension" が予測された場合、エンティティは返され **ません**。これは、このインスタンスで重複が発生していないためです。

:::image type="content" source="../media/require-exact-overlap-example-3.svg" alt-text="コンポーネントの正確な重複結果の 3 つ目の例を示すスクリーンショット。" lightbox="../media/require-exact-overlap-example-3.svg":::

### <a name="union-overlap"></a>和集合の重複

テキスト内に複数のコンポーネントが見つかり、重複している場合、そのコンポーネントのスパンの **和集合** が返されます。

このオプションは、リコールを最適化し、組み合わせ可能な最も長い一致を取得しようとする場合に最適です。

#### <a name="examples"></a>例

list コンポーネントで "Palm Beach" が一致し、learned コンポーネントによって "Palm Beach Extension" が予測された場合、"**Palm Beach Extension**" が返されます。これは、重複の先頭の最初の文字が "Palm" の "P" であり、重複するコンポーネントの末尾の最後の文字が "Extension" の "n" であるためです。

:::image type="content" source="../media/return-union-example-1.svg" alt-text="コンポーネントの和集合重複結果の例を示すスクリーンショット。" lightbox="../media/return-union-example-1.svg":::

list コンポーネントで "Palm Beach" が一致し、learned コンポーネントによって "Beach Extension" が予測された場合、"**Palm Beach Extension**" が返されます。これは、重複の先頭の最初の文字が "Palm" の "P" であり、重複するコンポーネントの末尾の最後の文字が "Extension" の "n" であるためです。

:::image type="content" source="../media/return-union-example-2.svg" alt-text="コンポーネントの和集合重複結果の 2 つ目の例を示すスクリーンショット。" lightbox="../media/return-union-example-2.svg":::

事前構築済みコンポーネントによって "New York" が予測され、list コンポーネントで "York Beach" が一致し、learned コンポーネントによって "Beach Extension" が予測された場合、 "__**New York Beach Extension**__" が返されます。これは、重複の先頭の最初の文字が "New" の "N" であり、重複するコンポーネントの末尾の最後の文字が "Extension" の "n" であるためです。

:::image type="content" source="../media/return-union-example-3.svg" alt-text="コンポーネントの和集合重複結果の 3 つ目の例を示すスクリーンショット。" lightbox="../media/return-union-example-3.svg":::

### <a name="return-all-separately"></a>すべて個別に返す

すべてのコンポーネントの一致または予測は、エンティティの **個別のインスタンス** として返されます。

このオプションは、予測後にエンティティに対して独自の重複ロジックを適用する場合に最適です。

#### <a name="examples"></a>例

list コンポーネントで "Palm Beach" が 一致し、learned コンポーネントによって "Palm Beach Extension" が予測された場合、エンティティでは "**Palm Beach**" と "**Palm Beach Extension**" の 2 つのインスタンスが返されます。

:::image type="content" source="../media/return-all-overlaps-example-1.svg" alt-text="コンポーネントの重複結果をすべて返す例を示すスクリーンショット。" lightbox="../media/return-all-overlaps-example-1.svg":::

事前構築済みコンポーネントによって "New York" が予測され、list コンポーネントで "York Beach" が一致し、learned コンポーネントによって "Beach Extension" が予測された場合、エンティティでは "**New York**"、"**York Beach**"、"**Beach Extension**" の 3 つのインスタンスが返されます。

:::image type="content" source="../media/return-all-overlaps-example-2.svg" alt-text="コンポーネントの重複結果をすべて返す 2 つ目の例を示すスクリーンショット。" lightbox="../media/return-all-overlaps-example-2.svg":::

## <a name="next-steps"></a>次のステップ

[サポートされている事前構築済みコンポーネント](../prebuilt-component-reference.md)
