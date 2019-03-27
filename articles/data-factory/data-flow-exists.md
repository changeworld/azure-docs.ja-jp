---
title: Azure Data Factory Mapping Data Flow の存在変換
description: Azure Data Factory Mapping Data Flow の存在変換
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: 18d8a0e231e8b4dbe33911dd6267966674366904
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/23/2019
ms.locfileid: "56734491"
---
# <a name="azure-data-factory-mapping-data-flow-exists-transformation"></a>Azure Data Factory Mapping Data Flow の存在変換

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

存在変換は、データ内の行のフローを停止または許可する行のフィルター変換です。 存在変換は ```SQL WHERE EXISTS``` および ```SQL WHERE NOT EXISTS``` と同様です。 フィルター変換の後、データ ストリームからの結果の行には、ソース 1 の列の値がソース 2 に存在するすべての行か、またはソース 1 の列の値がソース 2 に存在しないすべての行が含まれます。

![存在の設定](media/data-flow/exsits.png "存在 1")

Data Flow がストリーム 1 の値をストリーム 2 と比較できるように、存在の 2 番目のソースを選択します。

ソース 1 とソース 2 から、存在の有無を調べる値を持つ列を選択します。
