---
title: Text Analytics for Health でのアサーションの検出
titleSuffix: Azure Cognitive Services
description: アサーションの検出について学習します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: overview
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-health, ignite-fall-2021
ms.openlocfilehash: 0f9d24e43bd4525a703027cff6a5ee4bec7bd431
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131029758"
---
# <a name="assertion-detection"></a>アサーションの検出

医療コンテンツの意味は、否定、または条件付きアサーションなどの修飾子によって大きな影響を受け、これが誤って伝わると重大な影響を及ぼす可能性があります。 Text Analytics for Health では、テキスト内のエンティティに対するアサーションの検出の 3 つのカテゴリがサポートされます。 

* 確実性
* 条件付き
* 関連付け

## <a name="assertion-output"></a>アサーションの出力

Text Analytics for Health はアサーション修飾子を返します。これは、テキスト内の概念のコンテキストをより深く理解するために、医療コンセプトに割り当てられた有益な属性です。 これらの修飾子は 3 つのカテゴリに分かれており、それぞれが異なる側面に焦点を置いており、相互に排他的な値のセットを含んでいます。 各エンティティには、カテゴリごとに 1 つの値のみが割り当てられます。 各カテゴリの最も一般的な値が既定値です。 サービスの出力応答には、既定値とは異なるアサーション修飾子だけが含まれます。

**CERTAINTY** - 概念の存在に関する情報と (存在または不在)、その存在に関するテキストがどの程度確実か (的確または可能性) に関する情報を提供します。
*   **Positive** [既定値]: 概念が存在するか、発生しています。
* **Negative**: 概念は現在存在していないか、発生していません。
* **Positive_Possible**: 概念は存在する可能性がありますが、不明確要素があります。
* **Negative_Possible**: 概念が存在する可能性はほとんどありませんが、ある程度の不明確要素があります。
* **Neutral_Possible**: 概念は、存在する可能性と存在しない可能性があり、どちらの側にも偏っていません。

**CONDITIONALITY** - 概念の存在が特定の条件に依存するかどうかに関する情報を提供します。 
*   **None** [既定値]: 概念は事実であり、仮説でなく、特定の条件に依存しません。
*   **Hypothetical**: 概念は発達するか、将来発生する可能性があります。
*   **Conditional**: 概念は存在するか、特定の条件下でのみ発生します。

**ASSOCIATION** - 概念はテキストの対象または別の誰かと関連付けられているかどうかを示します。
*   **Subject** [既定値]: 概念は、テキストの対象 (通常は患者) に関連付けられています。
*   **Someone_Else**: 概念は、テキストの対象ではない誰かに関連付けられています。


アサーションの検出は、次の例のように、否定されたエンティティを、確実性カテゴリの負の値として表します。

```json
{
    "offset": 381,
    "length": 3,
    "text": "SOB",
    "category": "SymptomOrSign",
    "confidenceScore": 0.98,
    "assertion": {
        "certainty": "negative"
    },
    "name": "Dyspnea",
    "links": [
        {
            "dataSource": "UMLS",
            "id": "C0013404"
        },
        {
            "dataSource": "AOD",
            "id": "0000005442"
        },
    ...
}
```

## <a name="next-steps"></a>次のステップ

[Text Analytics for Health を呼び出す方法](../how-to/call-api.md)
