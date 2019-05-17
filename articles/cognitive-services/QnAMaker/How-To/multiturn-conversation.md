---
title: 複数ターン会話
titleSuffix: Azure Cognitive Services
description: プロンプトとコンテキストを使用して、一連の質問についてボットの複数ターンと呼ばれる複数回のターンを管理します。 複数ターンとは、前の質問のコンテキストが次の質問と応答に影響する会話をやりとりする機能です。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: article
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: 24158d5949c0a31e5444c609cbfced970bdeca07
ms.sourcegitcommit: 399db0671f58c879c1a729230254f12bc4ebff59
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/09/2019
ms.locfileid: "65471975"
---
# <a name="use-follow-up-prompts-to-create-multiple-turns-of-a-conversation"></a>フォローアップ プロンプトを使用して、複数のターンを含む会話を作成します。

フォローアップ プロンプトとコンテキストを使用して、一連の質問についてボットの "_複数ターン_" と呼ばれる複数回のターンを管理します。

詳しくは、[デモ ビデオ](https://aka.ms/multiturnexample)をご覧ください。

## <a name="what-is-a-multi-turn-conversation"></a>複数ターン会話とは

会話の種類によっては 1 回のターンでは完了できません。 クライアント アプリケーション (チャット ボット) の会話を設計するにあたり、場合によっては、ユーザーが行う質問をフィルター処理または調整してから正しい応答を決定する必要があります。 質問のこのフローは、ユーザーに**フォローアップ プロンプト**を示すことで実現します。

ユーザーが質問したとき、QnA Maker は応答 "_と_" フォローアップ プロンプトがあれば返します。 このようにフォローアップ質問を選択肢として提示できます。 

## <a name="example-multi-turn-conversation-with-chat-bot"></a>チャット ボットとの複数ターン会話の例

チャット ボットは会話を質問ごとに管理し、ユーザーが最後の応答を決定します。

![会話フロー内で、会話を続けるためのオプションとして応答内に示すプロンプトを提供し、複数ターン ダイアログ システムで会話の状態を管理します。](../media/conversational-context/conversation-in-bot.png)

上の図では、応答を返す前に、ユーザーの質問を調整する必要があります。 ナレッジ ベースでは、質問 (#1) には 4 つのフォローアップ プロンプトがあり、チャット ボットで 4 つの選択肢 (#2) として提示されます。 

ユーザーが選択肢 (#3) を選ぶと、調整のための次の選択肢 (#4) が表示されます。 これが続き (#5)、正しい最終応答 (#6) が決まります。

コンテキスト会話を管理するにはクライアント アプリケーションを変更する必要があります。

## <a name="create-a-multi-turn-conversation-from-documents-structure"></a>ドキュメントの構造から複数ターン会話を作成する
ナレッジ ベースを作成するとき、複数ターン抽出を可能にするオプションのチェックボックスが表示されます。 このオプションを選択すると、ドキュメントをインポートするときに、構造から複数ターン会話が暗黙に示されます。 その構造が存在する場合、QnA Maker によってフォローアップ プロンプトの QnA ペアが作成されます。 複数ターン構造を推論できるのは、URL、PDF、または DOCX ファイルのみです。 

Microsoft Surface [PDF ファイル](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/product-manual.pdf) の次の図は、マニュアルとして使用するためのものです。 

![![ドキュメントをインポートする場合、コンテキスト会話が構造から暗黙に示される場合があります。 その構造が存在する場合、ドキュメントのインポートの際に、QnA Maker によってフォローアップ プロンプトの QnA ペアが作成されます。](../media/conversational-context/import-file-with-conversational-structure.png)](../media/conversational-context/import-file-with-conversational-structure.png#lightbox)

この PDF ドキュメントをインポートすると、QnA Maker は構造からフォローアップ プロンプトを決定して、会話フローを作成します。 

![![この PDF ドキュメントをインポートすると、QnA Maker は構造からフォローアップ プロンプトを決定して、会話フローを作成します。 ](../media/conversational-context/surface-manual-pdf-follow-up-prompt.png)](../media/conversational-context/surface-manual-pdf-follow-up-prompt.png#lightbox)

## <a name="filter-questions-and-answers-by-context"></a>コンテキストで質問と応答をフィルター処理する

1. 表示される質問と応答のペアを減らして、コンテキスト会話の質問と応答のみにします。 **[オプションの表示]** を選択し、**[Show context (PREVIEW)]\(コンテキストを表示 (プレビュー)\)** を選択します。 フォローアップ プロンプトを含む最初の質問と応答のペアが追加されるまで一覧は空です。 

    ![コンテキスト会話による質問と会話のペアのフィルター処理](../media/conversational-context/filter-question-and-answers-by-context.png)

## <a name="add-new-qna-pair-as-follow-up-prompt"></a>フォローアップ プロンプトとして新しい QnA ペアを追加する

1. **[Add QnA pair]\(QnA ペアの追加\)** を選択します。 
1. 新しい質問のテキスト `Give feedback.` と応答 `What kind of feedback do you have?` を入力します。

1. この質問の **[応答]** 列で **[Add follow-up prompt]\(フォローアップ プロンプトの追加\)** を選択します。 
1. **[Follow-up prompt]\(フォローアップ プロンプト\)** ポップアップ ダイアログでは、既存の質問を検索したり、新しい質問を入力したりできます。 この手順では、**[表示テキスト]** にテキスト `Feedback on an QnA Maker service` を入力します。 
1. **[Context-only]\(コンテキストのみ\)** をオンにします。 **[Context-only]\(コンテキストのみ\)** オプションは、このユーザー テキストが認識されるのは前の質問に対する応答として与えられる場合 "_のみ_" であることを示します。 このシナリオでは、プロンプト テキストは単独の質問としては意味をなしません。意味があるのは前の質問とのコンテキストにおいてのみです。
1. **[Link to answer]\(応答へのリンク\)** テキスト ボックスに応答 `How would you rate QnA Maker?` を入力します。
1. **[新規作成]** をクリックし、**[保存]** を選択します。 

    ![新しいプロンプト QnA の作成](../media/conversational-context/create-child-prompt-from-parent.png)

    これによって、新しい質問と応答のペアが作成され、選択した質問がフォローアップ プロンプトとしてリンクされました。 両方の質問の **[コンテキスト]** 列は、フォローアップ プロンプトの関係を示します。 

    ![![両方の質問の [コンテキスト] 列は、フォローアップ プロンプトの関係を示します。](../media/conversational-context/child-prompt-created.png)](../media/conversational-context/child-prompt-created.png#lightbox)

1. `Give feedback`質問に対して **[Add follow-up prompt]\(フォローアップ プロンプトの追加\)** を選択して、もう 1 つのフォローアップ プロンプトを追加します。 
1. `Feedback on an existing feature` を入力し、`Which feature would you like to give feedback on?` という応答の新しい質問を作成します。  

1.  **[Context-only]\(コンテキストのみ\)** をオンにします。 **[Context-only]\(コンテキストのみ\)** オプションは、このユーザー テキストが認識されるのは前の質問に対する応答として与えられる場合 "_のみ_" であることを示します。 このシナリオでは、プロンプト テキストは単独の質問としては意味をなしません。意味があるのは前の質問とのコンテキストにおいてのみです。
1.  **[保存]** を選択します。 

    これによって、新しい質問が作成され、質問がフォローアップ プロンプト質問として `Give feedback`質問にリンクされました。
    
    この時点で、最上位の質問には、直前の質問 `Give feedback`にリンクされた 2 つのフォローアップ プロンプトがあります。

    ![![この時点で、最上位の質問には、直前の質問 `Give feedback`にリンクされた 2 つのフォローアップ プロンプトがあります。](../media/conversational-context/all-child-prompts-created.png)](../media/conversational-context/all-child-prompts-created.png#lightbox)

1. **[Save and Train]\(保存してトレーニング\)** を選択して、ナレッジ ベースを新しい質問でトレーニングします。 

## <a name="add-existing-qna-pair-as-follow-up-prompt"></a>フォローアップ プロンプトとして既存の QnA ペアを追加する

1. 既存の QnA ペアをフォローアップ プロンプトとしてリンクする場合は、質問と応答のペアの行を選択します。
1. その行で **[Add follow-up prompt]\(フォローアップ プロンプトの追加\)** を選択します。
1. ポップアップ ダイアログで、検索ボックスに質問のテキストを入力します。 すべての一致が返されます。 フォローアップとして使用したい質問を選択し、**[Context-only]\(コンテキストのみ\)** をオンにして、**[保存]** を選択します。 

    フォローアップ プロンプトを追加したら、忘れずに **[Save and train]\(保存してトレーニング\)** を選択してください。
  
## <a name="add-metadata-to-follow-up-prompts"></a>フォローアップ プロンプトにメタデータを追加する 

ナレッジ ベースでは、質問と応答のペアをフォローアップ プロンプトにリンクされているとき、最初にメタデータ フィルターが適用されてからフォローアップが返されます。

1. 2 つのフォローアップ QnA ペアについて、1 つずつにメタデータを追加します。

    |質問|メタデータの追加|
    |--|--|
    |`Feedback on an QnA Maker service`|"Feature":"all"|
    |`Feedback on an existing feature`|"Feature":"one"|
    
    ![サービスとの会話応答でフィルター処理できるようにメタデータをフォローアップ プロンプトに追加](../media/conversational-context/add-metadata-feature-to-follow-up-prompt.png) 

1. 保存してトレーニングします。 

    質問 `Give feedback` を送信してメタデータ フィルター `Feature` の値が `all` であれば、そのメタデータを含む QnA ペアのみが返されます。 どちらの QnA ペアもフィルターに一致しないため返されません。 

## <a name="test-the-qna-set-to-get-all-the-follow-up-prompts"></a>QnA セットをテストしてすべてのフォローアップ プロンプトを取得する

**[テスト]** ウィンドウ内でフォローアップ プロンプトを使った質問をテストするときは、**[Enable multi-turn]\(複数ターンの有効化\)** を選択して、質問を入力します。 応答には、フォローアップ プロンプトが含まれます。

![[テスト] ウィンドウで質問をテストすると、応答にフォローアップ プロンプトが含まれます。](../media/conversational-context/test-pane-with-question-having-follow-up-prompts.png)

## <a name="json-request-to-return-initial-answer-and-follow-up-prompts"></a>最初の応答とフォローアップ プロンプトを返す JSON 要求

空の `context` オブジェクトを使用して、ユーザーの質問に対する応答を要求し、フォローアップ プロンプトを含めます。 

```JSON
{
  "question": "accounts and signing in",
  "top": 30,
  "userId": "Default",
  "isTest": false,
  "context": {}
}
```

## <a name="json-response-to-return-initial-answer-and-follow-up-prompts"></a>最初の応答とフォローアップ プロンプトを返す JSON 応答

前のセクションでは、`Accounts and signing in` に対する応答とフォローアップ プロンプト (ある場合) を要求しました。 応答には、。`answers[0].context` にあるプロンプト情報とユーザーに表示するテキストが含まれます。 

```JSON
{
    "answers": [
        {
            "questions": [
                "Accounts and signing in"
            ],
            "answer": "**Accounts and signing in**\n\nWhen you set up your Surface, an account is set up for you. You can create additional accounts later for family and friends, so each person using your Surface can set it up just the way he or she likes. For more info, see All about accounts on Surface.com. \n\nThere are several ways to sign in to your Surface Pro 4: ",
            "score": 86.96,
            "id": 37,
            "source": "surface-pro-4-user-guide-EN .pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": [
                    {
                        "displayOrder": 0,
                        "qnaId": 38,
                        "qna": null,
                        "displayText": "Use the sign-in screen"
                    },
                    {
                        "displayOrder": 1,
                        "qnaId": 39,
                        "qna": null,
                        "displayText": "Use Windows Hello to sign in"
                    },
                    {
                        "displayOrder": 2,
                        "qnaId": 40,
                        "qna": null,
                        "displayText": "Sign out"
                    }
                ]
            }
        },
        {
            "questions": [
                "Use the sign-in screen"
            ],
            "answer": "**Use the sign-in screen**\n\n1.  \n\nTurn on or wake your Surface by pressing the power button. \n\n2.  \n\nSwipe up on the screen or tap a key on the keyboard. \n\n3.  \n\nIf you see your account name and account picture, enter your password and select the right arrow or press Enter on your keyboard. \n\n4.  \n\nIf you see a different account name, select your own account from the list at the left. Then enter your password and select the right arrow or press Enter on your keyboard. ",
            "score": 32.27,
            "id": 38,
            "source": "surface-pro-4-user-guide-EN .pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": []
            }
        },
        {
            "questions": [
                "Sign out"
            ],
            "answer": "**Sign out**\n\nHere's how to sign out: \n\n Go to Start , and right-click your name. Then select Sign out. ",
            "score": 27.0,
            "id": 40,
            "source": "surface-pro-4-user-guide-EN .pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": []
            }
        }
    ],
    "debugInfo": null
}
```

`prompts` 配列によって `displayText` プロパティのテキストと `qnaId` 値が提供されるため、これらの応答を会話フローで次に表示される選択肢として示すことができます。 

## <a name="json-request-to-return-non-initial-answer-and-follow-up-prompts"></a>最初以外の応答とフォローアップ プロンプトを返す JSON 要求

前のコンテキストを `context` オブジェクトに含めます。

次の JSON 要求では、現在の質問は `Use Windows Hello to sign in`、前の質問は `Accounts and signing in` です。 

```JSON
{
  "question": "Use Windows Hello to sign in",
  "top": 30,
  "userId": "Default",
  "isTest": false,
  "qnaId": 39,
  "context": {
    "previousQnAId": 37,
    "previousUserQuery": "accounts and signing in"
  }
}
``` 

##  <a name="json-response-to-return-non-initial-answer-and-follow-up-prompts"></a>最初以外の応答とフォローアップ プロンプトを返す JSON 応答

QnA Maker _GenerateAnswer_ JSON 応答は、`answers`オブジェクトの最初の項目にある `context` プロパティのフォローアップ プロンプトを含みます。

```JSON
{
    "answers": [
        {
            "questions": [
                "Give feedback"
            ],
            "answer": "What kind of feedback do you have?",
            "score": 100.0,
            "id": 288,
            "source": "Editorial",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": [
                    {
                        "displayOrder": 0,
                        "qnaId": 291,
                        "qna": null,
                        "displayText": "Feedback on an QnA Maker service"
                    },
                    {
                        "displayOrder": 0,
                        "qnaId": 292,
                        "qna": null,
                        "displayText": "Feedback on an existing feature"
                    }
                ]
            }
        }
    ]
}
```

## <a name="displaying-prompts-and-sending-context-in-the-client-application"></a>クライアント アプリケーションでのプロンプトの表示とコンテキストの送信 

クライアント アプリケーションでは、推奨されるアクションまたはボタンとしてユーザーがプロンプトを表示できるオプションとともに、すべての質問が表示されます。
その後、クライアント アプリケーションは、現在の QnA ペアとユーザー クエリをコンテキストとして格納し、次のユーザー クエリと一緒に渡せるようにします。 

この[ボット フレームワーク サンプル](https://aka.ms/qnamakermultiturnsample)を使用して、QnA Maker ボットでの複数ターン ダイアログの詳しい作業手順を確認します。


## <a name="prompt-order-supported-in-api"></a>API でサポートされるプロンプトの順序

JSON 応答で返されるプロンプトの順序は、API によってのみ編集できます。 

## <a name="next-steps"></a>次の手順

コンテキスト会話について詳しくは、[ダイアログのサンプル](https://aka.ms/qnamakermultiturnsample)に関するページまたは[複数ターン会話のボット設計の概要](https://docs.microsoft.com/azure/bot-service/bot-builder-conversations?view=azure-bot-service-4.0)に関するページをご覧ください。

> [!div class="nextstepaction"]
> [ナレッジ ベースの移行](../Tutorials/migrate-knowledge-base.md)
