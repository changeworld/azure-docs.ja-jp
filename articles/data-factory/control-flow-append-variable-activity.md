---
title: 変数アクティビティの追加
titleSuffix: Azure Data Factory & Azure Synapse
description: 変数追加アクティビティを設定して、Data Factory または Synapse Analytics パイプラインで定義されている既存の配列変数に値を追加する方法について説明します。
ms.service: data-factory
ms.subservice: orchestration
ms.custom: synapse
ms.topic: conceptual
author: chez-charlie
ms.author: chez
ms.reviewer: jburchel
ms.date: 09/09/2021
ms.openlocfilehash: 9d2a081535f571b139a5cb15cdb85b37e2c3af97
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124811706"
---
# <a name="append-variable-activity-in-azure-data-factory-and-synapse-analytics"></a>Azure Data Factory と Synapse Analytics での変数の追加アクティビティ
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]
変数追加アクティビティを使用して、Data Factory または Synapse Analytics パイプラインで定義されている既存の配列変数に値を追加します。

## <a name="type-properties"></a>型のプロパティ

プロパティ | 説明 | 必須
-------- | ----------- | --------
name | パイプラインのアクティビティの名前 | はい
description | アクティビティの動作を説明するテキスト | no
type | アクティビティの種類は AppendVariable です | はい
value | 指定した変数に追加するために使用する文字列リテラルまたは式のオブジェクト値 | はい
variableName | アクティビティによって変更される変数の名前。変数は "Array" 型でなければなりません | はい

## <a name="next-steps"></a>次の手順
関連する制御フロー アクティビティを確認します。 

- [変数アクティビティの設定](control-flow-set-variable-activity.md)
