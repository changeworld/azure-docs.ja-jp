---
title: エンティティにラベルを付ける発話例
titleSuffix: Azure Cognitive Services
description: LUIS ポータルの意図の詳細ページで、発話例内にサブコンポーネントがある機械学習エンティティにラベルを付ける方法について説明します。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 11/15/2019
ms.author: diberry
ms.openlocfilehash: 87b7375b2da0effbc18fff6a7e9d67383c93a403
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2019
ms.locfileid: "74134122"
---
# <a name="label-machine-learned-entity-in-an-example-utterance"></a>発話例内の機械学習エンティティにラベルを付ける

発話例内のエンティティにラベルを付けることで、エンティティの例があること、および発話内でエンティティが出現できる場所が LUIS に示されます。 

## <a name="labeling-machine-learned-entity"></a>機械学習エンティティにラベルを付ける

「`hi, please I want a cheese pizza in 20 minutes`」というフレーズについて考えます。 

1. 目的のエンティティの左端のテキストを選択してから、右端のテキストを選択します。 次の画像では、_完全な注文_にラベルが付けられています。

    > [!div class="mx-imgBorder"]
    > ![完全な機械学習エンティティにラベルを付ける](media/label-utterances/example-1-label-machine-learned-entity-complete-order.png)

1. ポップアップ ウィンドウからエンティティを選択します。 ラベル付けされた完全なピザ注文エンティティには、ラベルが付いたすべての単語が (英語では左から右へ) 含まれています。 

> [!TIP]
> ポップアップ ウィンドウで選択できるエンティティは、テキストが出現するコンテキストに関連しています。 たとえば、5 レベルの機械学習されたエンティティがあり、第 3 レベル (発話例の下のラベルが付いたエンティティ名で示されています) でテキストを選択している場合、ポップアップ ウィンドウで選択できるエンティティは、第 3 レベルのサブコンポーネントのコンテキストに限られます (第 4 レベルのサブコンポーネント)。 

## <a name="review-labeled-text"></a>ラベルが付けられたテキストをレビューする

ラベル付け後、発話例をレビューします。 ラベル付け後、発話例には LUIS によって現在のモデルが適用されます。 実線は、そのテキストにラベルが付けられたことを示しています。 

> [!div class="mx-imgBorder"]
> ![ラベルが付けられた完全な機械学習エンティティ](media/label-utterances/example-1-label-machine-learned-entity-complete-order-labeled.png)

## <a name="when-to-train"></a>トレーニングをするタイミング

ラベル付けされたエンティティを現在のモデルでサポートする必要があるのに、発話例が、予測されたテキストとして表示され続け、ラベル付けされない場合は、アプリをトレーニングします。  

## <a name="confirm-predicted-entity"></a>予測されたエンティティを確定する

視覚的なインジケーターが発話の上にある場合は、そのテキストは予測されていても_まだラベルが付けられていない_ことを示しています。 予測をラベルに変更するには、発話を選択してから、 **[Confirm entity predictions] (エンティティ予測を確定)** を選択します。

> [!div class="mx-imgBorder"]
> ![完全な機械学習エンティティを予測する](media/label-utterances/example-1-label-machine-learned-entity-complete-order-predicted.png)

## <a name="label-subcomponent-entity-by-painting-with-entity-palette-cursor"></a>エンティティ パレットのカーソルを使用してペイントすることでサブコンポーネント エンティティにラベルを付ける

1. 予測 (発話例の上に表示されるエンティティ) を修正するには、エンティティ パレットを開きます。 

    > [!div class="mx-imgBorder"]
    > ![機械学習エンティティのエンティティ パレット](media/label-utterances/pizza-entity-palette-with-pizza-type-selected.png)

1. エンティティのサブコンポーネントを選択します。 この操作は、新しいカーソルによって視覚的に示されます。 このカーソルは、ポータル内でマウスの動きに従います。 

    > [!div class="mx-imgBorder"]
    > ![機械学習エンティティのエンティティ パレット](media/label-utterances/pizza-type-entity-palette-cursor.png)

1. 発話例の中で、カーソルによってエンティティに_ペイント_します。 

    > [!div class="mx-imgBorder"]
    > ![機械学習エンティティのエンティティ パレット](media/label-utterances/paint-subcomponent-with-entity-palette-cursor.png)

## <a name="labeling-matching-text-entities-to-a-machine-learned-entity"></a>機械学習エンティティに対して照合テキスト エンティティをラベル付けする

照合テキスト エンティティには、事前構築済みエンティティ、正規表現エンティティ、およびリスト エンティティが含まれます。 これらは、機械学習エンティティを作成または編集するときに、サブコンポーネントに対する制約として機械学習エンティティに追加します。 

**これらの制約が追加されたら、発話例の中で照合テキストをラベル付けする必要はありません。**

## <a name="entity-prediction-errors"></a>エンティティ予測エラー

エンティティ予測エラーには警告インジケーターが表示されます。 これは、予測されたエンティティが、ラベル付けされたエンティティと一致しないことを示しています。 

> [!div class="mx-imgBorder"]
> ![機械学習エンティティのエンティティ パレット](media/label-utterances/example-utterance-indicates-prediction-error.png)

## <a name="next-steps"></a>次の手順

[ダッシュボード](luis-how-to-use-dashboard.md)を使用し、[エンドポイントの発話をレビュー](luis-how-to-review-endpoint-utterances.md)して、アプリの予測品質を向上させます。
