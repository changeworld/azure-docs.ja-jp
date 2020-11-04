---
title: マッピング データ フローでのピボット解除変換
description: Azure Data Factory のマッピング データ フローのピボット解除変換
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 07/14/2020
ms.openlocfilehash: ea8881adf39a315df7746dbce14dedcbee18ccf6
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/24/2020
ms.locfileid: "92521052"
---
# <a name="unpivot-transformation-in-mapping-data-flow"></a>マッピング データ フローでのピボット解除変換

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

ADF のマッピング データ フローで、正規化されていないデータセットをより正規化されたバージョンに変換するための方法としてピボット解除を使用して、単一のレコード内の複数の列の値を、単一の列内に同じ値を持つ複数のレコードに展開します。

![ピボット解除変換](media/data-flow/unpivot1.png "ピボット解除オプション 1")

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4B1RR]

## <a name="ungroup-by"></a>グループ化解除

![ピボット解除変換](media/data-flow/unpivot5.png "ピボット解除オプション 2")

まず、ピボット解除集計用にグループ化解除する列を設定します。 列の一覧の横にある [+] 記号を使用して、グループ化を解除する 1 つまたは複数の列を設定します。

## <a name="unpivot-key"></a>ピボット解除キー

![ピボット解除変換](media/data-flow/unpivot6.png "ピボット解除オプション 3")

ピボット解除キーは、ADF によって列から行にピボットされる列です。 既定では、データセット内のこのフィールドの一意の値が行にピボットされます。 ただし、必要に応じて、データセットから行の値にピボットする値を入力できます。

## <a name="unpivoted-columns"></a>ピボット解除列

![ピボット解除変換](media/data-flow//unpivot7.png "ピボット解除オプション 4")

最後に、行に変換されるピボット解除された列の値を格納する列名を選択します。

(省略可能) Null 値を持つ行は削除することができます。

たとえば、SumCost は、上記の例で選択された列名です。

![ピボット解除変換前と変換後の PO、Vendor、および Fruit 列を示す画像。Fruit 列をピボット解除キーとして使用しています。](media/data-flow/unpivot3.png)

[Column Arrangement]\(列の配置\) を [Normal]\(標準\) に設定すると、1 つの値からピボット解除された新しいすべての列がグループ化されます。 列の配置を [Lateral]\(横\) に設定すると、既存の列から生成された、ピボット解除された新しい列がグループ化されます。

![ピボット解除変換](media/data-flow//unpivot7.png "ピボット解除オプション 5")

ピポット解除された最終的なデータの結果セットには、別個の行の値にピボット解除された列の合計が表示されます。

## <a name="next-steps"></a>次のステップ

[ピボット解除変換](data-flow-pivot.md)を使用して、行を列にピボットします。
