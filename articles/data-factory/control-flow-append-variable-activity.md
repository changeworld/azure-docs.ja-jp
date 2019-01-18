---
title: Azure Data Factory の変数追加アクティビティ | Microsoft Docs
description: 変数追加アクティビティを設定して、Data Factory パイプラインで定義されている既存の配列変数に値を追加する方法について説明します
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/09/2018
ms.author: douglasl
ms.openlocfilehash: e904075908fe7108c0566856b25fe03be0b7fd86
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/04/2019
ms.locfileid: "54023809"
---
# <a name="append-variable-activity-in-azure-data-factory"></a>Azure Data Factory の変数追加アクティビティ

変数追加アクティビティを使用して、Data Factory パイプラインで定義されている既存の配列変数に値を追加する方法について説明します。

## <a name="type-properties"></a>型のプロパティ

プロパティ | 説明 | 必須
-------- | ----------- | --------
name | パイプラインのアクティビティの名前 | [はい]
description | アクティビティの動作を説明するテキスト | ×
type | アクティビティの種類は AppendVariable です | はい
value | 指定した変数に追加するために使用する文字列リテラルまたは式のオブジェクト値 | はい
variableName | アクティビティによって変更される変数の名前。変数は "Array" 型でなければなりません | はい

## <a name="next-steps"></a>次の手順
Data Factory でサポートされている関連制御フロー アクティビティについては、以下を参照してください。 

- [変数アクティビティの設定](control-flow-set-variable-activity.md)
