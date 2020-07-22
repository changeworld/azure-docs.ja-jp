---
title: 既定の回答を取得する - QnA Maker
description: 規定の回答は、質問に対する回答が見つからなかった場合に返されます。 標準の既定の回答から既定の応答を変更することもできます。
ms.topic: how-to
ms.date: 07/02/2020
ms.openlocfilehash: 005442938167c1bf7927603c44d6f870795cbeee
ms.sourcegitcommit: 93462ccb4dd178ec81115f50455fbad2fa1d79ce
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/06/2020
ms.locfileid: "85979974"
---
# <a name="change-default-answer-for-a-qna-maker-resource"></a>QnA Maker リソースの既定の回答を変更する

ナレッジ ベースの既定の回答は、回答が見つからない場合に返されることを意図しています。 [Azure Bot Service](https://docs.microsoft.com/azure/bot-service/bot-builder-howto-qna?view=azure-bot-service-4.0&tabs=cs#calling-qna-maker-from-your-bot) などのクライアント アプリケーションを使用している場合は、スコアしきい値を満たす回答がないことを示す別個の既定の回答が用意されている場合もあります。

## <a name="set-default-answer-when-you-create-knowledge-base"></a>ナレッジ ベースを作成するときに既定の回答を設定する

新しいナレッジ ベースを作成するとき、既定の回答テキストは設定の 1 つです。 作成プロセス中にこの設定をしなかった場合は、後で次の手順で変更できます。

## <a name="change-default-answer-in-qna-maker-portal"></a>QnA Maker ポータルで既定の回答を変更する

QnA Maker サービスから回答が返されない場合、ナレッジ ベースの既定の回答が返されます。

1. [QnA Maker ポータル](https://www.qnamaker.ai/)にサインインし、一覧からご利用のナレッジ ベースを選択します。
1. ナビゲーション バーから **[設定]** を選択します。
1. **[Manage knowledge base]\(ナレッジ ベースの管理\)** セクションで **[Default answer text]\(既定の回答テキスト\)** の値を変更します。

    :::image type="content" source="../media/qnamaker-concepts-confidencescore/change-default-answer.png" alt-text="既定の回答テキスト ボックスが強調表示されている QnA Maker ポータルの [設定] ページのスクリーンショット。":::

1. **[保存してトレーニング]** を選択して変更を保存します。

## <a name="next-steps"></a>次のステップ

* [ナレッジ ベースの作成](../How-to/manage-knowledge-bases.md)