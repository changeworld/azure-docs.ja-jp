---
title: Azure Data Factory マッピング データ フローのフィルター変換
description: Azure Data Factory マッピング データ フローのフィルター変換を使用して行を絞り込む
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/16/2019
ms.openlocfilehash: 77587d007d9bfcdc1461201f676c79880bd89d6b
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/06/2019
ms.locfileid: "73676172"
---
# <a name="filter-transformation-in-mapping-data-flow"></a>マッピング データ フローのフィルター変換

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

以下の例は、受信ストリーム `CleanData` を受け取る `FilterBefore1960` という名前の条件分割変換です。 フィルター条件は `year <= 1960` という式です。

Data Factory UX では、この変換は次の図のようになります。

![フィルター変換](media/data-flow/filter1.png "フィルター変換")

この変換のデータ フロー スクリプトは、次のスニペットに含まれています。

```
CleanData
    filter(
        year <= 1960
    ) ~> FilterBefore1960

```

## <a name="next-steps"></a>次の手順

[選択変換](data-flow-select.md)により列を除外する
