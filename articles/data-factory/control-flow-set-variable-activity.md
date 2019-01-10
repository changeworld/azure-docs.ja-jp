---
title: Azure Data Factory の変数アクティビティの設定 | Microsoft Docs
description: 変数アクティビティの設定を使用して、Data Factory パイプラインで定義されている既存の変数の値を設定する方法について説明します
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/10/2018
ms.author: douglasl
ms.openlocfilehash: cc573028779bcd6b77394bbeefbea58f714b835c
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/04/2019
ms.locfileid: "54017346"
---
# <a name="set-variable-activity-in-azure-data-factory"></a>Azure Data Factory の変数アクティビティの設定

変数アクティビティの設定を使用して、Data Factory パイプラインで定義されている文字列型、ブール型、配列型の既存の変数の値を設定します。

## <a name="type-properties"></a>型のプロパティ

プロパティ | 説明 | 必須
-------- | ----------- | --------
name | パイプラインのアクティビティの名前 | [はい]
description | アクティビティの動作を説明するテキスト | ×
type | アクティビティの種類は SetVariable です | はい
value | 指定した変数を設定するために使用する文字列リテラルまたは式のオブジェクト値 | はい
variableName | このアクティビティによって設定される変数の名前 | はい


## <a name="next-steps"></a>次の手順
Data Factory でサポートされている関連制御フロー アクティビティについては、以下を参照してください。 

- [変数アクティビティの追加](control-flow-append-variable-activity.md)
