---
title: ドメイン固有のコンテンツの検出 - Computer Vision
titleSuffix: Azure Cognitive Services
description: Computer Vision API を使用した画像の記述に関連する概念。
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: pafarley
ms.openlocfilehash: a9c71fa7e5d86cfeb4fe6fab44bbce241546ccb8
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/16/2018
ms.locfileid: "49342560"
---
# <a name="detecting-domain-specific-content"></a>ドメイン固有のコンテンツの検出

タグ付けやトップレベルの分類に加え、Computer Vision では特殊な (つまり、ドメイン固有の) 情報もサポートされます。 特殊な情報は、スタンドアロン方式として、または高度な分類を使用して実装することができます。 これは、ドメイン固有のモデルを追加することにより、86 のカテゴリ分類をさらに絞り込む手段として機能します。

ドメイン固有のモデルを使用する場合、次の 2 つのオプションがあります。

* スコープされた分析  
  HTTP POST 呼び出しを行うことで、選択したモデルのみを分析します。 使用するモデルがわかっている場合は、モデルの名前を指定します。 そのモデルに関連する情報のみを取得します。 たとえば、このオプションを使用して、有名人識別のみを検索することができます。 応答には、信頼度スコアと共に、潜在的に一致する有名人のリストが含まれます。
* 高度な分析  
  86 のカテゴリ分類からのカテゴリに関連する詳細情報を取得するための分析です。 このオプションは、1 つ以上のドメイン固有のモデルからの詳細に加え、ユーザーが汎用的なイメージ分析を行うアプリケーションで使用できます。 このメソッドが呼び出された場合、最初に 86 のカテゴリ分類の分類子が呼び出されます。 いずれかのカテゴリが既知のモデルや一致するモデルのカテゴリと一致した場合、分類子呼び出しの 2 番目のパスが後に続きます。 たとえば、HTTP POST 呼び出しの `details` パラメーターに "all" が設定されているか、または "celebrities" が含まれている場合、86 のカテゴリ分類子が呼び出された後、メソッドで有名人の分類子が呼び出されます。 画像が `people_` として、またはそのカテゴリのサブカテゴリとして分類された場合、celebrity 分類子が呼び出されます。

## <a name="listing-domain-specific-models"></a>ドメイン固有のモデルの一覧取得

Computer Vision でサポートされているドメイン固有モデルの一覧を取得できます。 現在、Computer Vision では、ドメイン固有コンテンツの検出に対して次のドメイン固有モデルがサポートされています。

| Name | 説明 |
|------|-------------|
| celebrities | 著名人の認識、`people_` カテゴリに分類された画像についてサポート |
| landmarks | ランドマークの認識、`outdoor_` または `building_` カテゴリに分類された画像についてサポート |

### <a name="domain-model-list-example"></a>ドメイン モデル一覧取得の例

次の JSON 応答では、Computer Vision でサポートされているドメイン固有モデルの一覧が取得されます。

```json
{
    "models": [
        {
            "name": "celebrities",
            "categories": ["people_", "人_", "pessoas_", "gente_"]
        },
        {
            "name": "landmarks",
            "categories": ["outdoor_", "户外_", "屋外_", "aoarlivre_", "alairelibre_",
                "building_", "建筑_", "建物_", "edifício_"]
        }
    ]
}
```

## <a name="next-steps"></a>次の手順

[画像の分類](concept-categorizing-images.md)に関する概念を学習する。