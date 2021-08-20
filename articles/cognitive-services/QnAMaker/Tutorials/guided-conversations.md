---
title: ガイド付きの会話にマルチターン プロンプトを追加する
description: このチュートリアルでは、ガイド付きの会話にマルチターン プロンプトを作成する方法について説明します。
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: tutorial
author: mrbullwinkle
ms.author: mbullwin
ms.date: 06/29/2021
ms.openlocfilehash: 5c664dd2d08bb743ebe3e813808c559a30d901fc
ms.sourcegitcommit: 285d5c48a03fcda7c27828236edb079f39aaaebf
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2021
ms.locfileid: "113235294"
---
# <a name="add-guided-conversations-with-multi-turn-prompts"></a>ガイド付きの会話にマルチターン プロンプトを追加する

 このチュートリアルでは、[Surface ペンの FAQ](https://support.microsoft.com/surface/how-to-use-your-surface-pen-8a403519-cd1f-15b2-c9df-faa5aa924e98) を使用してナレッジ ベースを作成します。

この例では、ユーザーが Surface ペン製品に関する追加情報、特に、Surface ペンのトラブルシューティング方法を質問しているが、正しい回答が得られていない場合を想定しましょう。 そこで、追加のシナリオをサポートするためのプロンプトを追加し、ユーザーにマルチターン プロンプトを使用して正しい回答を案内します。

## <a name="view-qnas-with-context"></a>QnA にコンテキストを表示する
[Surface ペンの FAQ](https://support.microsoft.com/surface/how-to-use-your-surface-pen-8a403519-cd1f-15b2-c9df-faa5aa924e98)のナレッジ ベースを作成する際には、ソースドキュメントからのマルチターン抽出を有効にすることを選択します。 詳細については、[ドキュメントからのマルチターン会話の作成](../how-to/multiturn-conversation.md#create-a-multi-turn-conversation-from-a-documents-structure)に関する記事を参照してください。 この一覧には、QnA ペアに関連付けられているマルチターン プロンプトが表示されます。これは、 **[表示オプション]** の **[コンテキストの表示]** を使用して表示できます。

> [!div class="mx-imgBorder"]
> [ ![赤のボックスで囲まれた [表示オプション] と [コンテキストの表示] のスクリーンショット]( ../media/guided-conversations/show-context.png) ]( ../media/guided-conversations/show-context.png#lightbox)

これにより、QnA ペアにリンクされたすべてのフォローアップ プロンプトのコンテキスト ツリーが表示されます。 

> [!div class="mx-imgBorder"]
> [ ![Surface ペンの FAQ ページの一般的な質問に対する回答のスクリーンショット]( ../media/guided-conversations/source.png) ]( ../media/guided-conversations/source.png#lightbox)

## <a name="add-new-qna-pair-with-follow-up-prompts"></a>フォローアップ プロンプトが含まれる新しい QnA ペアを追加する

Surface ペンに関するイシューをユーザーが解決できるように、次の方法でフォローアップ プロンプトを追加します。

1.  2 つのフォローアップ プロンプトが含まれる新しい QnA ペアを追加する
2.  新しく追加されたプロンプトのいずれかにフォローアップ プロンプトを追加する

**ステップ 1**: **[互換性チェック]** と **[ペン設定のチェック]** の 2 つのフォローアップ プロンプトが含まれる新しい QnA ペアを追加します。エディターを使用して、 **[QnA ペアの追加]** をクリックして、フォローアップ プロンプトが含まれる新しい QnA ペアを追加します

> [!div class="mx-imgBorder"]
> [ ![UI の [QnA ペアの追加] が赤いボックスで強調表示されているスクリーンショット]( ../media/guided-conversations/add-pair.png) ]( ../media/guided-conversations/add-pair.png#lightbox)

次に示すように、**編集用** の新しい行が作成され、QnA ペアを入力できます。

> [!div class="mx-imgBorder"]
> [ ![UI の [フォローアップ プロンプトの追加] が強調表示されているスクリーンショット]( ../media/guided-conversations/follow-up.png) ]( ../media/guided-conversations/follow-up.png#lightbox)

次に、 **[フォローアップ プロンプトの追加]** を選択して、新しく作成された QnA ペアにフォローアップ プロンプトを追加します。 これをクリックして、次のようなプロンプトの詳細を入力します。

> [!div class="mx-imgBorder"]
> [ ![UI の [フォローアップ プロンプトの追加] が強調表示されているスクリーンショット]( ../media/guided-conversations/follow-up.png) ]( ../media/guided-conversations/follow-up.png#lightbox)

プロンプトの "表示テキスト" として「**互換性チェック**」と入力して、QnA とリンクさせます。 このプロンプトへのリンクに関連する QnA ペアを使用できないため、"Surface ペンの互換性チェック" という検索を行うときに、 **[新規作成]** をクリックして新しい QnA ペアを作成します。 変更を **保存** すると、次の画面が表示され、次に示すように、フォローアップ プロンプトの新しい QnA ペアを入力できます。

> [!div class="mx-imgBorder"]
> [ ![Surface ペンの互換性チェックに関する質問と回答のスクリーンショット]( ../media/guided-conversations/check-compatibility.png) ]( ../media/guided-conversations/check-compatibility.png#lightbox)

同様に、ユーザーが Surface ペンのトラブルシューティングを行い、QnA ペアを追加できるように、もう 1 つのプロンプトである **[ペン設定のチェック]** を追加します。

> [!div class="mx-imgBorder"]
> [ ![Surface のペン設定のチェックに関する質問と回答のスクリーンショット]( ../media/guided-conversations/check-pen-settings.png) ]( ../media/guided-conversations/check-pen-settings.png#lightbox)

**ステップ 2**: 新しく作成されたプロンプトに別のフォローアップ プロンプトを追加します。 ここでは、前に作成したプロンプト "ペン設定のチェック" のフォローアップ プロンプトとして "ペン先の交換" を追加します。

> [!div class="mx-imgBorder"]
> [ ![ペン先の交換に関する情報が赤いボックスで表示された Surface のペン設定のチェックの質問と回答のスクリーンショット]( ../media/guided-conversations/replace-pen-tips.png) ]( ../media/guided-conversations/replace-pen-tips.png#lightbox)

最後に、変更を保存し、テスト ウィンドウでこれらのプロンプトをテストします。 

> [!div class="mx-imgBorder"]
> [ ![QnAMaker テスト ウィンドウのスクリーンショット]( ../media/guided-conversations/test-pane.png) ]( ../media/guided-conversations/test-pane.png#lightbox)

**Surface ペンのイシュー** に関するユーザー クエリの場合、システムが回答を返し、新しく追加されたプロンプトをユーザーに提示します。 次にユーザーが **[ペン設定のチェック]** プロンプトのいずれかを選択すると、関連する回答が別のプロンプト **[ペン先の交換]** とともにユーザーに返されます。これを選択すると、ユーザーに詳細情報が表示されます。 そのため、マルチターンはユーザーに必要な回答を提供するために使用されます。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [アクティブ ラーニングを使用してナレッジ ベースを強化する](active-learning.md)