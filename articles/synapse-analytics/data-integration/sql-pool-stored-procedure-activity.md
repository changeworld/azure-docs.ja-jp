---
title: SQL プール ストアド プロシージャ アクティビティを使用してデータを変換する
description: Azure Synapse Analytics でストアド プロシージャを呼び出すために SQL プール ストアド プロシージャ アクティビティを使用する方法を説明します。
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: pipeline
author: linda33wj
ms.author: jingwang
ms.reviewer: jrasnick
ms.date: 05/13/2021
ms.openlocfilehash: 4c1f12d4e61f254f753f3feb59ca46997f88b751
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/31/2021
ms.locfileid: "123257064"
---
# <a name="transform-data-by-using-sql-pool-stored-procedure-activity-in-azure-synapse-analytics"></a>Azure Synapse Analytics で SQL プール ストアド プロシージャ アクティビティを使用してデータを変換する

[!INCLUDE[appliesto-xxx-asa-md](../../data-factory/includes/appliesto-xxx-asa-md.md)]

[パイプライン](../../data-factory/concepts-pipelines-activities.md)のデータ変換アクティビティを使用して、生データを予測や分析情報に変換し、処理します。 この記事は、データ変換とサポートされている変換アクティビティの概要を説明する、[データ変換](../../data-factory/transform-data.md)に関する記事に基づいています。

Azure Synapse Analytics では、SQL プール ストアド プロシージャ アクティビティを使用して、専用 SQL プールのストアド プロシージャを呼び出すことができます。

## <a name="syntax-details"></a>構文の詳細

SQL プール ストアド プロシージャ アクティビティでは、次の設定がサポートされています。

| プロパティ                  | 説明                              | 必須 |
| ------------------------- | ---------------------------------------- | -------- |
| name                      | アクティビティの名前                     | はい      |
| description               | アクティビティの用途を説明するテキストです。 | いいえ       |
| type                      | SQL プール ストアド プロシージャ アクティビティの場合、アクティビティの種類は **SqlPoolStoredProcedure** です | はい      |
| sqlPool         | 現在の Azure Synapse ワークスペース内の[専用 SQL プール](../sql/overview-architecture.md)への参照です。 | はい      |
| storedProcedureName       | 呼び出すストアド プロシージャの名前を指定します。 | はい      |
| storedProcedureParameters | ストアド プロシージャのパラメーター値を指定します。 パラメーター値と、データ ソースでサポートされるパラメーター値の型を渡すには、`"param1": { "value": "param1Value","type":"param1Type" }` を使います。 パラメーターで null を渡す必要がある場合は、`"param1": { "value": null }` (すべて小文字) を使います。 | いいえ       |

例:

```json
{
    "name": "SQLPoolStoredProcedureActivity",
    "description":"Description",
    "type": "SqlPoolStoredProcedure",
    "sqlPool": {
        "referenceName": "DedicatedSQLPool",
        "type": "SqlPoolReference"
    },
    "typeProperties": {
        "storedProcedureName": "usp_sample",
        "storedProcedureParameters": {
            "identifier": { "value": "1", "type": "Int" },
            "stringData": { "value": "str1" }

        }
    }
}
```

## <a name="next-steps"></a>次のステップ
 
- [パイプラインとアクティビティ](../../data-factory/concepts-pipelines-activities.md)
- [専用 SQL プール](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md)
