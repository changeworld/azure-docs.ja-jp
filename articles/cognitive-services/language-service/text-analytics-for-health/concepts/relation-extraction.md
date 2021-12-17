---
title: Text Analytics for healt での関係抽出
titleSuffix: Azure Cognitive Services
description: 関係抽出について説明します
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: overview
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-health, ignite-fall-2021
ms.openlocfilehash: 6fd838471387d7ef1b54beb9ead7b802f6e041e2
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131029750"
---
# <a name="relation-extraction"></a>関係抽出

関係抽出により、テキスト内で言及されている概念間の意味のある関係が識別されます。 たとえば、"条件の時間" の関係は、条件名を時刻に関連付けるか、省略形と完全な説明の間に関連付けることによって検出されます。  


## <a name="relation-extraction-output"></a>関係抽出の出力

Text Analytics for health によって、属性とエンティティの間の関係 (身体構造の傾向、投薬など) や、エンティティ間の関係 (省略の検出など) など、さまざまな概念間の関係が認識されます。

> [!NOTE]
> * CONDITION を参照する関係は、DIAGNOSIS エンティティ型または SYMPTOM_OR_SIGN エンティティ型のいずれかを参照できます。
> * MEDICATION を参照する関係は、MEDICATION_NAME エンティティ型または MEDICATION_CLASS エンティティ型を参照できます。
> * TIME を参照する関係は、TIME エンティティ型または DATE エンティティ型のいずれかを参照できます。

リレーションシップ抽出の出力には、リレーションシップの型のエンティティの URI 参照と割り当てられたロールが含まれます。 たとえば、次の JSON では以下のようになります。

```json
                "relations": [
                    {
                        "relationType": "DosageOfMedication",
                        "entities": [
                            {
                                "ref": "#/results/documents/0/entities/0",
                                "role": "Dosage"
                            },
                            {
                                "ref": "#/results/documents/0/entities/1",
                                "role": "Medication"
                            }
                        ]
                    },
                    {
                        "relationType": "RouteOfMedication",
                        "entities": [
                            {
                                "ref": "#/results/documents/0/entities/1",
                                "role": "Medication"
                            },
                            {
                                "ref": "#/results/documents/0/entities/2",
                                "role": "Route"
                            }
                        ]
...
]
```

## <a name="recognized-relations"></a>認識される関係

次の関係が API から返される可能性があります。 

**ABBREVIATION**

**BODY_SITE_OF_CONDITION**

**BODY_SITE_OF_TREATMENT**

**COURSE_OF_CONDITION**

**COURSE_OF_EXAMINATION**

**COURSE_OF_MEDICATION**

**COURSE_OF_TREATMENT**

**DIRECTION_OF_BODY_STRUCTURE**

**DIRECTION_OF_CONDITION**

**DIRECTION_OF_EXAMINATION**

**DIRECTION_OF_TREATMENT**

**DOSAGE_OF_MEDICATION**

**EXAMINATION_FINDS_CONDITION**

**EXPRESSION_OF_GENE**

**EXPRESSION_OF_VARIANT**

**FORM_OF_MEDICATION**

**FREQUENCY_OF_CONDITION**

**FREQUENCY_OF_MEDICATION**

**FREQUENCY_OF_TREATMENT**

**MUTATION_TYPE_OF_GENE**

**MUTATION_TYPE_OF_VARIANT**

**QUALIFIER_OF_CONDITION**

**RELATION_OF_EXAMINATION**

**ROUTE_OF_MEDICATION** 

**SCALE_OF_CONDITION**

**TIME_OF_CONDITION**

**TIME_OF_EVENT**

**TIME_OF_EXAMINATION**

**TIME_OF_MEDICATION**

**TIME_OF_TREATMENT**

**UNIT_OF_CONDITION**

**UNIT_OF_EXAMINATION**

**VALUE_OF_CONDITION**  

**VALUE_OF_EXAMINATION**

**VARIANT_OF_GENE**
