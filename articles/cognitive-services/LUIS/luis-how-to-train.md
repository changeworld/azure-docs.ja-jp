---
title: LUIS アプリのトレーニング - Azure | Microsoft Docs
description: Language Understanding (LUIS) を使用したモデルのトレーニング
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/14/2018
ms.author: diberry
ms.openlocfilehash: e947df20141b0b9870f318f410488aea23bafcf5
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2018
ms.locfileid: "39223186"
---
# <a name="train-your-luis-app"></a>LUIS アプリのトレーニング

トレーニングは、自然言語の理解を向上させるために、Language Understanding (LUIS) アプリを教えるプロセスです。 エンティティ、意図、発話の追加、編集、ラベル付け、削除など、モデルを更新した後に、LUIS アプリをトレーニングします。 

<!--
When you train a LUIS app by example, LUIS generalizes from the examples you have labeled, and it learns to recognize the relevant intents and entities. This teaches LUIS to improve classification accuracy in the future. -->

アプリのトレーニングと[テスト](luis-concept-test.md)は反復処理です。 LUIS アプリをトレーニングした後で、サンプルの発話を使用して、意図とエンティティが正しく認識されるかどうかをテストします。 正しく認識されない場合、もう一度 LUIS アプリの更新、トレーニング、およびテストを行います。 

## <a name="train-your-app"></a>アプリをトレーニングする
反復的なプロセスを開始するには、まず、LUIS アプリを少なくとも 1 回トレーニングする必要があります。 すべての意図に少なくとも 1 つの発話があることをトレーニングの前に確認してください。

1. **My Apps** ページでアプリの名前を選択してアプリにアクセスします。 

2. アプリで、上部パネルの **[トレーニング]** を選択します。 

    ![トレーニング ボタン](./media/luis-how-to-train/train-button.png)

3. トレーニングが完了したら、ブラウザーの上部に緑のバーが表示されます。

    ![アプリのトレーニングとテストのページ](./media/luis-how-to-train/train-success.png)

<!-- The following note refers to what might cause the error message "Training failed: FewLabels for model: <ModelName>" -->

>[!NOTE]
>アプリの 1 つまたは複数の意図に、発話の例が含まれていない場合は、アプリのトレーニングを行うことはできません。 すべての意図に発話を追加します。 詳細については、「[Add example utterances](luis-how-to-add-example-utterances.md)」(発話の追加) を参照してください。

## <a name="next-steps"></a>次の手順

* [LUIS で推奨される発話にラベルを付ける](luis-how-to-review-endoint-utt.md) 
* [LUIS アプリのパフォーマンスを向上させる機能を使用する](luis-how-to-add-features.md) 