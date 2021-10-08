---
title: マッピング データ フローでのピボット解除変換
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory と Azure Synapse Analytics 内でのマッピング データ フローのピボット解除変換について説明します。
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.custom: synapse
ms.date: 09/09/2021
ms.openlocfilehash: 28c89622702435f178e241125545f8f0ac86eb18
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/26/2021
ms.locfileid: "129059589"
---
# <a name="unpivot-transformation-in-mapping-data-flow"></a>マッピング データ フローでのピボット解除変換

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

[!INCLUDE[data-flow-preamble](includes/data-flow-preamble.md)]

マッピング データ フローで、正規化されていないデータセットをより正規化されたバージョンに変換するための方法としてピボット解除を使用して、単一のレコード内の複数の列の値を、単一の列内に同じ値を持つ複数のレコードに展開します。

:::image type="content" source="media/data-flow/unpivot1.png" alt-text="メニューから選択された [ピボット解除] を示しているスクリーンショット。":::

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4B1RR]

## <a name="ungroup-by"></a>グループ化解除

:::image type="content" source="media/data-flow/unpivot5.png" alt-text="[グループ化解除] タブが選択された [ピボット解除設定] を示しているスクリーンショット。":::

まず、ピボット解除集計用にグループ化解除する列を設定します。 列の一覧の横にある [+] 記号を使用して、グループ化を解除する 1 つまたは複数の列を設定します。

## <a name="unpivot-key"></a>ピボット解除キー

:::image type="content" source="media/data-flow/unpivot6.png" alt-text="[ピボット解除キー] タブが選択されている [ピボット解除設定] を示しているスクリーンショット。":::

ピボット解除キーは、サービスによって列から行にピボットされる列です。 既定では、データセット内のこのフィールドの一意の値が行にピボットされます。 ただし、必要に応じて、データセットから行の値にピボットする値を入力できます。

## <a name="unpivoted-columns"></a>ピボット解除列

:::image type="content" source="media/data-flow//unpivot7.png" alt-text="[データのプレビュー] タブが選択されている [ピボット解除設定] を示しているスクリーンショット。":::

最後に、行に変換されるピボット解除された列の値を格納する列名を選択します。

(省略可能) Null 値を持つ行は削除することができます。

たとえば、SumCost は、上記の例で選択された列名です。

:::image type="content" source="media/data-flow/unpivot3.png" alt-text="ピボット解除変換前と変換後の PO、Vendor、および Fruit 列を示す画像。Fruit 列をピボット解除キーとして使用しています。":::

[Column Arrangement]\(列の配置\) を [Normal]\(標準\) に設定すると、1 つの値からピボット解除された新しいすべての列がグループ化されます。 列の配置を [Lateral]\(横\) に設定すると、既存の列から生成された、ピボット解除された新しい列がグループ化されます。

:::image type="content" source="media/data-flow//unpivot7.png" alt-text="変換の結果を示しているスクリーンショット。":::

ピポット解除された最終的なデータの結果セットには、別個の行の値にピボット解除された列の合計が表示されます。

## <a name="next-steps"></a>次のステップ

[ピボット解除変換](data-flow-pivot.md)を使用して、行を列にピボットします。
