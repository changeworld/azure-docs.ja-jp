---
title: アクティブ ラーニングを使用してナレッジ ベースをエンリッチする
description: このチュートリアルでは、アクション ラーニングを使用してナレッジ ベースをエンリッチする方法について説明します
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: tutorial
author: mrbullwinkle
ms.author: mbullwin
ms.date: 06/29/2021
ms.openlocfilehash: f2af689bc0052decd91e8e8e5238ab88f828d261
ms.sourcegitcommit: 285d5c48a03fcda7c27828236edb079f39aaaebf
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2021
ms.locfileid: "113235339"
---
# <a name="enrich-your-knowledge-base-with-active-learning"></a>アクティブ ラーニングを使用してナレッジ ベースをエンリッチする

このチュートリアルでは、アクティブ ラーニングを使用してナレッジ ベースを強化する方法を示します。 顧客がナレッジ ベースに含まれない質問をしていることに気付く場合があります。 多くの場合、質問には異なる言い方のバリエーションがあります。 

これらのバリエーションを関連する QnA ペアに代替の質問として追加すると、実際のユーザーの問い合わせに回答するようナレッジ ベースを最適化するのに役立ちます。 エディターを使用して、QnA ペアに代替の質問を手動で追加できます。 同時に、アクティブ ラーニング機能を使用して、ユーザーの問い合わせに基づいてアクティブ ラーニングの提案を生成できます。 ただし、アクティブ ラーニング機能で提案を生成するには、ナレッジ ベースが規則正しくユーザー トラフィックを受信する必要があります。

## <a name="enable-active-learning"></a>アクティブ ラーニングを有効にする
アクティブ ラーニングは、カスタム質問と回答機能に対して既定で有効になっています。 ただし、QnA Maker GA の場合はアクティブ ラーニング設定を手動で更新する必要があります。 詳細については、[アクティブ ラーニングの有効化](../how-to/use-active-learning.md#turn-on-active-learning-for-alternate-questions)に関する記事を参照してください。

アクティブ ラーニングの提案を試すには、次のファイルをナレッジ ベースにインポートできます: [SampleActiveLearning.tsv](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/knowledge-bases/SampleActiveLearning.tsv)。 ナレッジ ベースのインポートの詳細については、[ナレッジ ベースのインポート](migrate-knowledge-base.md)に関する記事を参照してください。

## <a name="view-and-addreject-active-learning-suggestions"></a>アクティブ ラーニングの提案を表示し、追加または拒否する
アクティブ ラーニングの提案を利用できるようになったら、 **[表示オプション]**  >  **[Show active learning suggestions]\(アクティブ ラーニングの提案を表示\)** から表示できます。

> [!div class="mx-imgBorder"]
> [ ![[表示オプション] と [Show active learning suggestions]\(アクティブ ラーニングの提案を表示\) が赤い四角で囲まれているスクリーンショット]( ../media/active-learning/view-options.png) ]( ../media/active-learning/view-options.png#lightbox)

**[Show active learning suggestions]\(アクティブ ラーニングの提案を表示\)** をクリックすると、提案がある QnA ペアをフィルター処理するオプションが有効になります。 アクティブ ラーニングが無効になっている場合、または提案がない場合は、 **[Show active learning suggestions]\(アクティブ ラーニングの提案を表示\)** は無効になります。

> [!div class="mx-imgBorder"]
> [ ![フィルター オプションが赤い四角で強調されているスクリーンショット]( ../media/active-learning/filter-by-suggestions.png) ]( ../media/active-learning/filter-by-suggestions.png#lightbox)

アクティブ ラーニングによって提案された代替の質問がある QnA ペアのみをフィルター処理して、フィルター処理された QnA ペアの一覧を表示することができます。

> [!div class="mx-imgBorder"]
> [ ![Surface ペンに関するヘルプの表示が赤い四角で強調されているスクリーンショット]( ../media/active-learning/help.png) ]( ../media/active-learning/help.png#lightbox)


チェック マークまたはバツ印を使用して、これらの提案を受け入れるか、拒否することができます。 これは、各 QnA ペアで個別に行うか、上部にある **[Accept]\(受け入れる\) / [Reject]\(受け入れない\)** オプションを使用して行うことができます。

> [!div class="mx-imgBorder"]
> [ ![受け入れまたは拒否のオプションが赤で強調されているスクリーンショット]( ../media/active-learning/accept-reject.png) ]( ../media/active-learning/accept-reject.png#lightbox)

アクティブ ラーニングによる提案を追加または編集しない限り、ナレッジ ベースは変更されません。 最後に、[保存してトレーニング] をクリックして変更を保存します。

> [!NOTE] 
> アクティブ ラーニングのバージョンとサービスの設定を確認するには、[アクティブ ラーニングの使用方法](../how-to/use-active-learning.md)に関する記事を参照してください

## <a name="add-alternate-questions-using-editor"></a>エディターを使用して代替の質問を追加する

アクティブ ラーニングでは、ナレッジ ベースにヒットしたユーザーの問い合わせに基づいて代替の質問が自動的に提案されるのに対し、エディターを使用して質問のバリエーションを追加することもできます。
代替の質問を追加する QnA ペアを選択し、 **[Add alternative phrasing]\(代替の言い回しを追加する\)** を選択します

> [!div class="mx-imgBorder"]
> [ ![[Add alternative phrasing]\(代替の言い回しを追加する\) が赤で強調されたスクリーンショット]( ../media/active-learning/add-alternative-phrasing.png) ]( ../media/active-learning/add-alternative-phrasing.png#lightbox)

QnA ペアに追加された代替の質問が次のように表示されます

> [!div class="mx-imgBorder"]
> [ !["draw with your pen" が赤で強調されたスクリーンショット]( ../media/active-learning/draw-with-pen.png) ]( ../media/active-learning/draw-with-pen.png#lightbox)

アクティブ ラーニングと共に代替の質問を追加することで、似たユーザーの問い合わせに対して同じ応答を提供するのに役立つ質問のバリエーションでナレッジ ベースがさらにエンリッチされます。


> [!NOTE] 
> 代替の質問には多くのストップ ワードがあります。そうすると、期待したように応答の精度に影響しない可能性があります。 そのため、代替の質問の違いがストップ ワードだけである場合、これらの代替の質問は必要ありません。

ストップ ワードの一覧は、[ストップ ワードの一覧](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/STOPWORDS.md)に関するページで確認できます。


## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [同意語で応答の品質を向上させる](adding-synonyms.md)