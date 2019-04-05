---
title: Azure Data Factory Mapping Data Flow のピボット変換
description: Azure Data Factory Mapping Data Flow のピボット変換
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: 5548a62218aaac2e4da3853e8e5d43a584922bc0
ms.sourcegitcommit: dd1a9f38c69954f15ff5c166e456fda37ae1cdf2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/07/2019
ms.locfileid: "57569894"
---
# <a name="azure-data-factory-mapping-data-flow-pivot-transformation"></a>Azure Data Factory Mapping Data Flow のピボット変換

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

ADF Data Flow のピボットを集計として使用します。この場合、1 つまたは複数のグループ化された列が、個々の列に変換された個別の行の値を持ちます。 基本的には、行の値を新しい列にピボット (データをメタデータに変換) することができます。

![ピボットのオプション](media/data-flow/pivot1.png "ピボット 1")

## <a name="group-by"></a>グループ化

![ピボットのオプション](media/data-flow/pivot2.png "ピボット 2")

まず、ピボット集計用にグループ化する列を設定します。 ここでは、列の一覧の横にある + 記号を使って、複数の列を設定できます。

## <a name="pivot-key"></a>ピボット キー

![ピボットのオプション](media/data-flow/pivot3.png "ピボット 3")

ピボット キーは、ADF によって行から列にピボットされる列です。 既定では、データセット内のこのフィールドの一意の値が列にピボットされます。 ただし、必要に応じて、データセットから列の値にピボットする値を入力できます。

## <a name="pivoted-columns"></a>ピボットされた列

![ピボットのオプション](media/data-flow/pivot4.png "ピボット 4")

最後に、ピボットされた値に対して使用する集計と、変換による新しい出力に列をどのように表示するかを選択します。

(省略可能) 行の値から作成される新しい列の先頭、中間、および末尾に文字列を追加する名前付けパターンを設定できます。

たとえば、"売上" を "地域" によってピボット処理すると、各売上の値が新しい列の値になります (例:"25"、"50"、"1000" など)。ただし、"売上" というプレフィックス値を設定すれば、各列の値の先頭に "売上" が付加されます。

![ピボットのオプション](media/data-flow/pivot5.png "ピボット 5")

[Column Arrangement]\(列の配置\) を [Normal]\(標準\) に設定すると、すべてのピボットされた列がグループ化され、それらの値が集計されます。 [Column Arrangement]\(列の配置\) を [Lateral]\(水平\) に設定すると、列と値が入れ替わります。

### <a name="aggregation"></a>集計

ピボット値に使用する集計を設定するには、[Pivoted Columns]\(ピボットされた列\) ウィンドウの下部にあるフィールドをクリックします。 ADF Data Flow の式ビルダーが開き、そこで集計式を作成し、新しい集計値にわかりやすいエイリアス名を付けることができます。

式ビルダーで、ADF Data Flow 記述言語を使ってピボット列の変換を記述します (https://aka.ms/dataflowexpressions)。

### <a name="how-to-rejoin-original-fields"></a>元のフィールドを再結合する方法
> [!NOTE]
> ピボット変換では、集計、グループ化、およびピボット操作で使用された列のみが出力されます。 フロー内に前の手順の別の列を含めたい場合は、前の手順からの新しい分岐を使用し、自己結合パターンを使ってフローを元のメタデータと接続します。

## <a name="next-steps"></a>次の手順

列の値を行の値に変換する[ピボット解除変換](data-flow-unpivot.md)を試します。 
