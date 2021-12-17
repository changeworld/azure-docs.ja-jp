---
title: マッピング データ フローの条件分割変換
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory または Synapse Analytics でマッピング データ フローの条件分割変換を使用してデータを異なるストリームに分割する
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.custom: synapse
ms.date: 09/09/2021
ms.openlocfilehash: 15864e8dfb694478f8156d5122608dec88493285
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/26/2021
ms.locfileid: "129060292"
---
# <a name="conditional-split-transformation-in-mapping-data-flow"></a>マッピング データ フローの条件分割変換

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

[!INCLUDE[data-flow-preamble](includes/data-flow-preamble.md)]

条件分割変換では、データ行を、一致条件に応じて異なるストリームにルーティングします。 条件分割変換は、プログラミング言語の CASE 決定構造と似ています。 この変換では式を評価し、その結果に基づいて、データ行を指定されたストリームに送ります。

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4wKCX]

## <a name="configuration"></a>構成

**[分割]** 設定により、データの行の送り先が、最初に一致したストリームと、一致した全ストリームのどちらになるかが決まります。

データ フローの式ビルダーを使用して、分割条件の式を入力します。 新しい条件を追加する場合は、既存の行にあるプラス記号アイコンをクリックします。 いずれの条件にも一致しない行用に、既定のストリームを追加することもできます。

:::image type="content" source="media/data-flow/conditionalsplit1.png" alt-text="条件分割":::

## <a name="data-flow-script"></a>データ フローのスクリプト

### <a name="syntax"></a>構文

```
<incomingStream>
    split(
        <conditionalExpression1>
        <conditionalExpression2>
        ...
        disjoint: {true | false}
    ) ~> <splitTx>@(stream1, stream2, ..., <defaultStream>)
```

### <a name="example"></a>例

以下の例は、受信ストリーム `CleanData` を受け取る `SplitByYear` という名前の条件分割変換です。 この変換では、`year < 1960` と `year > 1980` という 2 つの分割条件が設定されています。 最初に一致した条件にデータを送るので、`disjoint` は false に設定しています。 最初の条件に一致した行はすべて、出力ストリーム `moviesBefore1960` に送られます。 残りの行のうち 2 番目の条件に一致したものはすべて、出力ストリーム `moviesAFter1980` に送られます。 その他のすべての行は、既定のストリーム `AllOtherMovies` に送られます。

サービスの UI では、この変換は下の画像のように表示されます。

:::image type="content" source="media/data-flow/conditionalsplit1.png" alt-text="条件分割":::

この変換のデータ フロー スクリプトは、次のスニペットに含まれています。

```
CleanData
    split(
        year < 1960,
        year > 1980,
        disjoint: false
    ) ~> SplitByYear@(moviesBefore1960, moviesAfter1980, AllOtherMovies)
```

## <a name="next-steps"></a>次のステップ

条件分割と合わせて使用する一般的なデータ フロー変換には、[結合変換](data-flow-join.md)、[参照変換](data-flow-lookup.md)、[選択変換](data-flow-select.md)があります
