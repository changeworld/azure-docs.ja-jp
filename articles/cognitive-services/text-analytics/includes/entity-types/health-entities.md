---
title: 医療向けの名前付きエンティティ
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 07/28/2020
ms.author: aahi
ms.openlocfilehash: 4ac65e85e05f408b8d2f37a1d6845dc9e28e2bab
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/29/2020
ms.locfileid: "87373074"
---
## <a name="health-entity-categories"></a>医療エンティティ カテゴリ:

次のエンティティ カテゴリは、[Text Analytics for Health](../../how-tos/text-analytics-for-health.md) によって返されます。  このコンテナー プレビューでは英語のテキストのみがサポートされており、各コンテナー イメージには 1 つのモデル バージョンのみが用意されていることに注意してください。

### <a name="named-entity-recognition"></a>名前付きエンティティの認識

|カテゴリ  |説明   |
|----------|--------------|
| AGE | 年齢。 |
| BODY_STRUCTURE | 臓器やその他の構造を含む、人体の一部。 | 
| CONDITION_QUALIFIER | "*軽度*"、"*広範囲*"、"*びまん性*" などの条件レベル。 | 
| DIAGNOSIS | 医療条件。 たとえば "*高血圧*" などです。 | 
| DIRECTION | "*左*" や "*前部*" などの方向。 | 
| DOSAGE | 医薬品の数または用量。  | 
| EXAMINATION_NAME | 検査の方法または手順。 | 
| EXAMINATION_RELATION | 測定単位と検査の間の関連付け。  | 
| EXAMINATION_UNIT | 検査の測定単位。 | 
| EXAMINATION_VALUE | 検査の測定単位の値。 | 
| FAMILY_RELATION | "*姉妹*" などの家族関係。  | 
| 頻度 | 周波数。   | 
| GENDER | 性別。 | 
| GENE | *TP53* などの遺伝子エンティティ。   | 
| MEDICATION_CLASS | 医薬品クラス。 たとえば "*抗生物質*" です。  | 
| MEDICATION_NAME  | ジェネリック医薬品およびブランド名付き医薬品。| 
| ROUTE_OR_MODE  | 医薬品の投与方法。 | 
| SYMPTOM_OR_SIGN  | 病気の症状。 | 
| TIME  | 時間。 たとえば、"8 年間" や "今日の午前 2 時 30 分" などです |
| TREATMENT_NAME  | 治療名。 | 
| VARIANT  | 遺伝子エンティティの遺伝的変異体 | 

### <a name="relation-extraction"></a>関係抽出

関係抽出により、テキスト内で言及されている概念間の意味のある関係が識別されます。 たとえば、"条件の時間" の関係は、条件名と時間を関連付けることによって検出されます。 Text Analytics for Health では、次の関係を識別できます。

* DIRECTION_OF_BODY_STRUCTURE  
* TIME_OF_CONDITION
* QUALIFIER_OF_CONDITION  
* DOSAGE_OF_MEDICATION 
* FORM_OF_MEDICATION  
* ROUTE_OR_MODE_OF_MEDICATION   
* STRENGTH_OF_MEDICATION 
* ADMINISTRATION_RATE_OF_MEDICATION   
* FREQUENCY_OF_MEDICATION 
* TIME_OF_MEDICATION 
* TIME_OF_TREATMENT 
* FREQUENCY_OF_TREATMENT  
* VALUE_OF_EXAMINATION
* UNIT_OF_EXAMINATION 
* RELATION_OF_EXAMINATION 
* TIME_OF_EXAMINATION  
* ABBREVIATION 
