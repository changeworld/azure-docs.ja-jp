---
title: マッピング データ フローでのデータ変換を文字列化する
description: 複合データ型を文字列化する
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.date: 10/06/2021
ms.openlocfilehash: e2d0a5993dddccc65109eb623d8f04f11715ac47
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/07/2021
ms.locfileid: "129660041"
---
# <a name="stringify-transformation-in-mapping-data-flow"></a>マッピング データ フローで変換を文字列化する

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

文字列化変換を利用し、複合データ型を文字列に変換します。 これは、元は構造体、マップ、または配列の型であった列データを単一の文字列エンティティとし格納または送信する必要がある場合に非常に便利です。

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RWMTs9]

## <a name="configuration"></a>構成

文字列化変換の構成パネルで、最初に、インラインで解析する列に含まれているデータの種類を選択します。 変換解析には、次の構成設定も含まれています。

:::image type="content" source="media/data-flow/stringify.png" alt-text="設定の文字列化":::

### <a name="column"></a>列

派生列や集計と同様に、ここで、ドロップダウン ピッカーから選択して既存の列を変更できます。 または、ここに新しい列の名前を入力することもできます。 ADF により、文字列化されたソース データがこの列に格納されます。 ほとんどの場合、入ってくる複合フィールド型を文字列化する新しい列を定義します。

### <a name="expression"></a>Expression

式ビルダーを使用し、文字列化するソース複合フィールドを設定します。 これは、文字列化する自己完結型データが含まれたソース列を選択するだけで簡単に行うことができます。または、解析するための複雑な式を作成することもできます。

:::image type="content" source="media/data-flow/stringify-2.png" alt-text="式の文字列化":::

#### <a name="example-expression"></a>式の例

この例では、```body.properties.periods``` は REST ソースから返された構造体内の配列です。

```
body.properties.periods
```

## <a name="data-flow-script"></a>データ フローのスクリプト

```
stringify(mydata = body.properties.periods ? string,
    format: 'json') ~> Stringify1
```

## <a name="next-steps"></a>次のステップ

* [フラット化変換](data-flow-flatten.md)を使用して、行を列にピボットします。
* [解析変換](data-flow-parse.md)を使用し、複合埋め込み型を別々の列に変換します。
* [派生列変換](data-flow-derived-column.md)を使用して、列を行にピボットします。
