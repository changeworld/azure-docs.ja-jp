---
title: LUIS で推奨される発話にラベルを付ける | Microsoft Docs
description: Language Understanding (LUIS) を使用して、推奨される発話にラベルを付け、アクティブな機械学習の促進を支援します。
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/08/2017
ms.author: diberry
ms.openlocfilehash: 5e195b8ef5aeb35b73c22438980fe2b2e3856977
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2018
ms.locfileid: "39224553"
---
# <a name="review-endpoint-utterances"></a>エンドポイントの発話の確認

LUIS の画期的な特長として、アクティブ ラーニングの[概念](luis-concept-review-endpoint-utterances.md)が挙げられます。 LUIS でエンドポイントにクエリを実行すると、LUIS はアクティブ ラーニングを使用して結果の品質を向上させます。 アクティブ ラーニング プロセスでは、LUIS によってエンドポイントのすべての発話が検査され、不確かな発話が選択されます。 これらの発話にラベルを付け、トレーニングして公開すると、LUIS による発話の識別の精度が向上します。 

## <a name="filter-utterances"></a>発話をフィルター処理する
1. **[My Apps]\(マイ アプリ\)** ページでアプリ名を選択してアプリ (例: TravelAgent) を開き、上部のバーの **[Build]\(ビルド\)** を選択します。

2. **[Improve app performance]\(アプリのパフォーマンス向上\)** で **[Review endpoint utterances]\(エンドポイントの発話の確認\)** を選択します。

    ![発話を確認する](./media/label-suggested-utterances/review.png)

3. **[Review endpoint utterances]\(エンドポイントの発話の確認\)** ページで、**[Filter list by intent or entity]\(意図またはエンティティでリストをフィルター処理する\)** テキスト ボックス内を選択します。 このドロップダウン リストでは、**[INTENTS]\(意図\)** にすべての意図が含まれ、**[ENTITIES]\(エンティティ\)** にすべてのエンティティが含まれています。

    ![発話フィルター](./media/label-suggested-utterances/filter.png)

4. ドロップダウン リストでカテゴリ (意図またはエンティティ) を選択し、発話を確認します。

    ![意図の発話](./media/label-suggested-utterances/intent-utterances.png)

## <a name="label-entities"></a>エンティティにラベルを付ける
LUIS では、エンティティ トークン (単語) が、青色で強調表示されたエンティティ名に置き換えられます。 発話にラベル付けされていないエンティティが含まれている場合は、発話内のそれらのエンティティにラベルを付けます。 

1. 発話内の単語を選択します。 

2. 一覧からエンティティを選択します。

    ![エンティティにラベルを付ける](./media/label-suggested-utterances/label-entity.png)

## <a name="align-single-utterance"></a>1 つの発話を配置する

各発話では、**[Aligned intent]\(配置される意図\)** 列に意図の候補が表示されます。 

1. その候補に同意する場合は、チェック マークをオンにします。

    ![配置される意図を保持する](./media/label-suggested-utterances/align-intent-check.png)

2. 候補に同意しない場合は、配置される意図のドロップダウン リストから適切な意図を選択し、その配置される意図の右側にあるチェック マークをオンにします。 

    ![意図を配置する](./media/label-suggested-utterances/align-intent.png)

3. チェック マークをオンにすると、その発話はリストから削除されます。 

## <a name="align-several-utterances"></a>複数の発話を配置する

複数の発話を配置するには、発話の左側のチェック ボックスをオンにし、**[Add selected]\(選択項目の追加\)** を選択します。 

![複数の発話を配置する](./media/label-suggested-utterances/add-selected.png)

## <a name="verify-aligned-intent"></a>配置された意図を確認する
発話に適切な意図が配置されていることを確認するには、**[Intents]\(意図\)** ページに移動し、意図名を選択して、発話を確認します。 **[Review endpoint utterances]\(エンドポイントの発話の確認\)** の発話がリストに含まれています。

## <a name="delete-utterance"></a>発話を削除する
各発話は、レビュー リストから削除できます。 一度削除すると、リストに再び表示されることはなくなります。 ユーザーがエンドポイントから同じ発話を入力したとしても同様です。 

発話を削除する必要があるかどうかわからない場合は、その発話を意図 [None]\(なし\) に移動するか、"その他" などの新しい意図を作成し、その意図に発話を移動します。 

## <a name="delete-several-utterances"></a>複数の発話を削除する
複数の発話を削除するには、各項目を選択し、**[Add selected]\(選択項目の追加\)** の右側にあるごみ箱を選択します。

![複数の発話を削除する](./media/label-suggested-utterances/delete-several.png)

## <a name="next-steps"></a>次の手順

推奨される発話にラベル付けした後にパフォーマンスがどのように向上するかをテストするには、上部のパネルの **[Test]\(テスト\)** を選択してテスト コンソールにアクセスします。 テスト コンソールを使用してアプリをテストする手順については、[アプリのトレーニングとテスト](luis-interactive-test.md)に関する記事をご覧ください。
