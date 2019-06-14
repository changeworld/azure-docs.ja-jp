---
title: Azure Data Factory Mapping Data Flow の存在変換
description: データ ファクトリのマッピング データ フローと存在変換を使用して、既存の行を確認する方法
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: b98b7afb21f2f50d44ba93ed793b6efb20f75164
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "65235965"
---
# <a name="mapping-data-flow-exists-transformation"></a>マッピング データ フローの存在変換

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

存在変換は、データ内の行のフローを停止または許可する行のフィルター変換です。 存在変換は ```SQL WHERE EXISTS``` および ```SQL WHERE NOT EXISTS``` と同様です。 存在変換の後、データ ストリームからの結果の行には、ソース 1 の列の値がソース 2 に存在するすべての行か、またはソース 1 の列の値がソース 2 に存在しないすべての行が含まれます。

![存在の設定](media/data-flow/exists.png "存在 1")

Data Flow がストリーム 1 の値をストリーム 2 と比較できるように、存在の 2 番目のソースを選択します。

ソース 1 とソース 2 から、存在の有無を調べる値を持つ列を選択します。

## <a name="multiple-exists-conditions"></a>複数存在条件

存在の列条件の各行の横に、各行の上にカーソルと置くと + 記号が表示されます。 これによって、存在条件に対して複数の行を追加できます。 追加の各条件は、"And" です。

## <a name="custom-expression"></a>カスタム式

![存在のカスタム設定](media/data-flow/exists1.png "存在のカスタム")

[カスタム式] をクリックして、存在の条件または非存在の条件として自由形式の式を作成することもできます。 このチェック ボックスをオンにすると、条件として独自の式を入力できます。

## <a name="next-steps"></a>次の手順

同様の変換として、[参照](data-flow-lookup.md)と[結合](data-flow-join.md)があります。
