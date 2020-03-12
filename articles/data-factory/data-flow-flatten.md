---
title: マッピング データ フローのフラット化変換
description: Azure Data Factory マッピング データ フローのフラット化変換
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/25/2020
ms.openlocfilehash: 415a093fd8a8fbe27e1d240b061548e18f2ca6b6
ms.sourcegitcommit: 1f738a94b16f61e5dad0b29c98a6d355f724a2c7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/28/2020
ms.locfileid: "78164731"
---
# <a name="azure-data-factory-flatten-transformation"></a>Azure Data Factory のフラット化変換

フラット化変換を使用すると、階層構造体内の配列値を新しい行にピボットして、基本的にデータの非正規化を行うことができます。

![変換ツールボックス](media/data-flow/flatten5.png "変換ツールボックス")

![フラット化変換 1](media/data-flow/flatten7.png "フラット化変換 1")

## <a name="unroll-by"></a>アンロール

まず、ロールを解除してピボットする配列列を選択します。

![フラット化変換設定](media/data-flow/flatten1.png "フラット化変換設定")

## <a name="unroll-root"></a>ルートのアンロール

既定値では、ADF は、前に選択した非ロール配列の構造体をフラット化します。 または、階層の別の部分を選択してアンロールすることもできます。 「ルートのアンロール」は省略可能な設定です。

## <a name="input-columns"></a>入力列

最後に、入力したフィールドと、アンロールした正規化された列に基づいて、新しい構造体のプロジェクションを選択します。

## <a name="data-flow-script"></a>データ フローのスクリプト

### <a name="syntax"></a>構文

```
<incomingStream>
foldDown(unroll(<unroll cols>),
    mapColumn(
        name,
        each(<array>(type == '<arrayDataType>')),
        each(<array>, match(true())),
        location
    )) ~> <transformationName>
```

### <a name="example"></a>例

```
source(output(
        name as string,
        location as string,
        satellites as string[],
        goods as (trade as boolean, customers as string[], orders as (orderId as string, orderTotal as double, shipped as (orderItems as (itemName as string, itemQty as string)[]))[])
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source2
source2 foldDown(unroll(goods.orders.shipped.orderItems),
    mapColumn(
        name,
        each(goods.orders, match(type == 'integer')),
        each(goods.orders.shipped.orderItems, match(true())),
        location
    )) ~> Flatten1
Flatten1 sink(allowSchemaDrift: true,
    validateSchema: false,
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> sink1
```    

## <a name="next-steps"></a>次のステップ

* [ピボット解除変換](data-flow-pivot.md)を使用して、行を列にピボットします。
* [ピボット解除変換](data-flow-unpivot.md)を使用して、列を行にピボットします。
