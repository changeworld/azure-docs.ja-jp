---
title: 変数の設定アクティビティ
titleSuffix: Azure Data Factory & Azure Synapse
description: 変数の設定アクティビティを使用して、Azure Data Factory または Azure Synapse Analytics のパイプラインに定義された既存の変数の値を設定する方法について説明します。
ms.service: data-factory
ms.subservice: orchestration
ms.custom: synapse
ms.topic: conceptual
ms.date: 09/09/2021
author: chez-charlie
ms.author: chez
ms.reviewer: jburchel
ms.openlocfilehash: 63c4a4b53889f2c762b7c1dadffe432cd7383b4c
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124758050"
---
# <a name="set-variable-activity-in-azure-data-factory-and-azure-synapse-analytics"></a>Azure Data Factory と Azure Synapse Analytics での変数の設定アクティビティ
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

変数の設定アクティビティを使用して、Data Factory または Synapse パイプラインに定義された文字列型、ブール型、または配列型の既存の変数の値を設定します。

## <a name="type-properties"></a>型のプロパティ

プロパティ | 説明 | 必須
-------- | ----------- | --------
name | パイプラインのアクティビティの名前 | はい
description | アクティビティの動作を説明するテキスト | no
type | **SetVariable** に設定する必要があります | はい
value | 変数が割り当てられる文字列リテラルまたは式オブジェクトの値 | はい
variableName | このアクティビティによって設定される変数の名前 | はい

## <a name="incrementing-a-variable"></a>変数のインクリメント

変数に関するよくあるシナリオの 1 つに、until または foreach アクティビティ内で変数を反復子として使用するというものがあります。 変数の設定アクティビティでは、`value` フィールドに設定されている変数を参照することはできません。 この制限を回避するには、一時変数を設定してから、2 番目の変数の設定アクティビティを作成します。 2 番目の変数の設定アクティビティでは、反復子の値が一時変数に設定されます。 

以下に、このパターンの例を示します。

:::image type="content" source="media/control-flow-set-variable-activity/increment-variable.png" alt-text="変数のインクリメント":::

``` json
{
    "name": "pipeline3",
    "properties": {
        "activities": [
            {
                "name": "Set I",
                "type": "SetVariable",
                "dependsOn": [
                    {
                        "activity": "Increment J",
                        "dependencyConditions": [
                            "Succeeded"
                        ]
                    }
                ],
                "userProperties": [],
                "typeProperties": {
                    "variableName": "i",
                    "value": {
                        "value": "@variables('j')",
                        "type": "Expression"
                    }
                }
            },
            {
                "name": "Increment J",
                "type": "SetVariable",
                "dependsOn": [],
                "userProperties": [],
                "typeProperties": {
                    "variableName": "j",
                    "value": {
                        "value": "@string(add(int(variables('i')), 1))",
                        "type": "Expression"
                    }
                }
            }
        ],
        "variables": {
            "i": {
                "type": "String",
                "defaultValue": "0"
            },
            "j": {
                "type": "String",
                "defaultValue": "0"
            }
        },
        "annotations": []
    }
}
```

現在、変数のスコープはパイプライン レベルです。 つまり、これらはスレッド セーフではなく、foreach ループなどの並列反復アクティビティ内からアクセスされた場合 (特に、その foreach アクティビティ内で値も変更される場合)、予想外の望ましくない動作を引き起こす可能性があります。

## <a name="next-steps"></a>次の手順
制御フローに関連するその他のアクティビティを確認します。 

- [変数アクティビティの追加](control-flow-append-variable-activity.md)
