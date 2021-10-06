---
title: マッピング データ フローのフィルター変換
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory または Synapse Analytics のマッピング データ フローのフィルター変換を使用して行を絞り込みます。
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.custom: synapse
ms.date: 09/09/2021
ms.openlocfilehash: c3cf00a5ea80278973a548a19e9ef0dc82ef877f
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/26/2021
ms.locfileid: "129060197"
---
# <a name="filter-transformation-in-mapping-data-flow"></a>マッピング データ フローのフィルター変換

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

[!INCLUDE[data-flow-preamble](includes/data-flow-preamble.md)]

フィルター変換を使用すると、条件に基づいて行をフィルター処理できます。 出力ストリームには、フィルター条件に一致するすべての行が含まれます。 フィルター変換は、SQL の WHERE 句と似ています。

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4xnxN]

## <a name="configuration"></a>構成

データ フローの式ビルダーを使用して、フィルター条件の式を入力します。 式ビルダーを開くには、青色のボックスをクリックします。 フィルター条件はブール型にする必要があります。 式の作成方法の詳細については、[式ビルダー](concepts-data-flow-expression-builder.md)のドキュメントを参照してください。

:::image type="content" source="media/data-flow/filter1.png" alt-text="フィルター変換":::

## <a name="data-flow-script"></a>データ フローのスクリプト

### <a name="syntax"></a>構文

```
<incomingStream>
    filter(
        <conditionalExpression>
    ) ~> <filterTransformationName>
```

### <a name="example"></a>例

以下の例は、受信ストリーム `CleanData` を受け取る `FilterBefore1960` という名前のフィルター変換です。 フィルター条件は `year <= 1960` という式です。

UI では、この変換は次の図のようになります。

:::image type="content" source="media/data-flow/filter1.png" alt-text="フィルター変換":::

この変換のデータ フロー スクリプトは、次のスニペットに含まれています。

```
CleanData
    filter(
        year <= 1960
    ) ~> FilterBefore1960

```

## <a name="next-steps"></a>次のステップ

[選択変換](data-flow-select.md)により列を除外する
