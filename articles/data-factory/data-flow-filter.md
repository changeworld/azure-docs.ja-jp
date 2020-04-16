---
title: マッピング データ フローのフィルター変換
description: Azure Data Factory マッピング データ フローのフィルター変換を使用して行を絞り込む
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/16/2019
ms.openlocfilehash: 3460c789909c6acd0537ef89a9ec8509bf6e135d
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2020
ms.locfileid: "81413726"
---
# <a name="filter-transformation-in-mapping-data-flow"></a>マッピング データ フローのフィルター変換

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

フィルター変換を使用すると、条件に基づいて行をフィルター処理できます。 出力ストリームには、フィルター条件に一致するすべての行が含まれます。 フィルター変換は、SQL の WHERE 句と似ています。

## <a name="configuration"></a>構成

データ フローの式ビルダーを使用して、フィルター条件の式を入力します。 式ビルダーを開くには、青色のボックスをクリックします。 フィルター条件はブール型にする必要があります。 式の作成方法の詳細については、[式ビルダー](concepts-data-flow-expression-builder.md)のドキュメントを参照してください。

![フィルター変換](media/data-flow/filter1.png "フィルター変換")

## <a name="data-flow-script"></a>データ フローのスクリプト

### <a name="syntax"></a>構文

```
<incomingStream>
    filter(
        <conditionalExpression>
    ) ~> <filterTransformationName>
```

### <a name="example"></a>例

以下の例は、受信ストリーム `FilterBefore1960` を受け取る `CleanData` という名前のフィルター変換です。 フィルター条件は `year <= 1960` という式です。

Data Factory UX では、この変換は次の図のようになります。

![フィルター変換](media/data-flow/filter1.png "フィルター変換")

この変換のデータ フロー スクリプトは、次のスニペットに含まれています。

```
CleanData
    filter(
        year <= 1960
    ) ~> FilterBefore1960

```

## <a name="next-steps"></a>次のステップ

[選択変換](data-flow-select.md)により列を除外する
