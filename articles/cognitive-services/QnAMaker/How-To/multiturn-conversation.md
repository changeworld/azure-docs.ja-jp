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
ms.date: 05/20/2019
ms.author: diberry
ms.openlocfilehash: 3ca166b287858b3e42aeda1421d1733fe24c81ab
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/04/2019
ms.locfileid: "66479646"
---
# <a name="use-follow-up-prompts-to-create-multiple-turns-of-a-conversation"></a>フォローアップ プロンプトを使用して、複数のターンを含む会話を作成します。

フォローアップ プロンプトとコンテキストを使用して、一連の質問についてボットの "_複数ターン_" と呼ばれる複数回のターンを管理します。

次のデモンストレーション ビデオで、どのようなプロセスになるか確認してください。

[![](../media/conversational-context/youtube-video.png)](https://aka.ms/multiturnexample)。

## <a name="what-is-a-multi-turn-conversation"></a>複数ターン会話とは

1 回のターンでは複数の質問に応答できません。 クライアント アプリケーション (チャット ボット) の会話を設計するにあたり、場合によっては、ユーザーが行う質問をフィルター処理または調整してから正しい応答を決定する必要があります。 質問のこのフローは、ユーザーに**フォローアップ プロンプト**を示すことで実現します。

ユーザーが質問したとき、QnA Maker は応答 "_と_" フォローアップ プロンプトがあれば返します。 このようにフォローアップ質問を選択肢として提示できます。 

## <a name="example-multi-turn-conversation-with-chat-bot"></a>チャット ボットとの複数ターン会話の例

チャット ボットはユーザーとの会話を質問ごとに管理し、最後の応答を決定します。

![会話フロー内で、会話を続けるためのオプションとして応答内に示すプロンプトを提供し、複数ターン ダイアログ システムで会話の状態を管理します。](../media/conversational-context/conversation-in-bot.png)

上の画像では、ユーザーは `My account` を入力しました。 ナレッジ ベースには、リンクした QnA のペアが 3 組あります。 ユーザーは、3 つの選択肢から 1 つを選択して、応答を絞り込む必要があります。 ナレッジ ベースでは、質問 (#1) には 3 つのフォローアップ プロンプトがあり、チャット ボットで 3 つの選択肢 (#2) として提示されます。 

ユーザーが選択肢 (#3) を選ぶと、調整のための次の選択肢 (#4) が表示されます。 これが続き (#5)、正しい最終応答 (#6) が決まります。

上の画像では、プロンプトを表示するために、 **[Enable multi-turn]** (複数ターンの有効化) が選択されています。 

### <a name="using-multi-turn-in-a-bot"></a>ボットでの複数ターンの使用

コンテキスト会話を管理するにはクライアント アプリケーションを変更する必要があります。 プロンプトを表示するには、[ボットにコード](https://github.com/microsoft/BotBuilder-Samples/tree/master/experimental/qnamaker-prompting)を追加する必要があります。  

## <a name="create-a-multi-turn-conversation-from-a-documents-structure"></a>ドキュメントの構造から複数ターン会話を作成する

ナレッジ ベースを作成するとき、複数ターン抽出を可能にするオプションのチェックボックスが表示されます。 

![ナレッジ ベースを作成するとき、複数ターン抽出を可能にするオプションのチェックボックスが表示されます。](../media/conversational-context/enable-multi-turn.png)

このオプションを選択すると、ドキュメントをインポートするときに、構造から複数ターン会話が暗黙に示されます。 その構造が存在する場合、QnA Maker によってフォローアップ プロンプトの QnA ペアが作成されます。 

複数ターン構造を推論できるのは、URL、PDF、または DOCX ファイルのみです。 

Microsoft Surface [PDF ファイル](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/product-manual.pdf) の次の図は、マニュアルとして使用するためのものです。 この PDF ファイルのサイズのために、Azure QnA Maker リソースには、B (15 インデックス) 以上の Search の価格レベルが必要です。 

![![ドキュメントをインポートする場合、コンテキスト会話が構造から暗黙に示される場合があります。 その構造が存在する場合、ドキュメントのインポートの際に、QnA Maker によってフォローアップ プロンプトの QnA ペアが作成されます。](../media/conversational-context/import-file-with-conversational-structure.png)](../media/conversational-context/import-file-with-conversational-structure.png#lightbox)

この PDF ドキュメントをインポートすると、QnA Maker は構造からフォローアップ プロンプトを決定して、会話フローを作成します。 

1. **手順 1** では、上部のナビゲーションで **[Create a knowledge base]** (ナレッジ ベースの作成) を選択します。
1. **手順 2** では、QnA サービスを作成するか、既存のものを使用します。 必ず、B (15 インデックス) 以上の Search サービスで QnA サービスを使用してください。これより小さなレベルでは、Surface マニュアル PDF ファイルは大きすぎるためです。
1. **手順 3** では、`Surface manual` などのナレッジ ベースの名前を入力します。
1. **手順 4** では、 **[Enable multi-turn extraction from URLs, .pdf or .docx files]** (URL、.pdf、または .docx ファイルからの複数ターンの抽出を有効にする) を選択します。 Surface マニュアルの URL を選択します

    ```text
    https://github.com/Azure-Samples/cognitive-services-sample-data-files/raw/master/qna-maker/data-source-formats/product-manual.pdf
    ```

1. **[Create your KB]** (KB の作成) ボタンを選択します。 

    ナレッジが作成されると、質問と応答のペアのビューが表示されます。

## <a name="show-questions-and-answers-with-context"></a>コンテキストを使用して質問と応答を表示する

表示される質問と応答のペアを減らして、コンテキスト会話の質問と応答のみにします。 

1. **[オプションの表示]** を選択し、 **[Show context (PREVIEW)]\(コンテキストを表示 (プレビュー)\)** を選択します。 一覧には、フォローアップ プロンプトを含む質問と応答のペアが表示されます。 

    ![コンテキスト会話による質問と会話のペアのフィルター処理](../media/conversational-context/filter-question-and-answers-by-context.png)

2. 複数ターンのコンテキストが最初の列に表示されます。

    ![![この PDF ドキュメントをインポートすると、QnA Maker は構造からフォローアップ プロンプトを決定して、会話フローを作成します。 ](../media/conversational-context/surface-manual-pdf-follow-up-prompt.png)](../media/conversational-context/surface-manual-pdf-follow-up-prompt.png#lightbox)

    上の画像では、#1 が列内で太字テキストで表示され、現在の質問を示しています。 親の質問は、行の最上位の項目です。 それより下のすべての質問は、リンクされた質問と応答のペアです。 これらの項目は選択できるので、直ちに他のコンテキスト項目に移動できます。 

## <a name="add-existing-qna-pair-as-follow-up-prompt"></a>フォローアップ プロンプトとして既存の QnA ペアを追加する

`My account` の元の質問には、`Accounts and signing in` などのフォローアップ プロンプトがあります。 

![`My account` の元の質問は、`Accounts and signing in` の応答が正しく返され、フォローアップ プロンプトが既にリンクされています。](../media/conversational-context/detected-and-linked-follow-up-prompts.png)

現在リンクされていない既存の QnA のペアにフォローアップ プロンプトを追加します。 質問がどの QnA ペアにもリンクされていないので、現在のビュー設定を変更する必要があります。

1. 既存の QnA ペアをフォローアップ プロンプトとしてリンクするには、質問と応答のペアの行を選択します。 Surface マニュアルの場合、`Sign out` を検索して一覧を減らします。
1. `Signout` の行で、 **[Add follow-up prompt]** (フォローアップ プロンプトの追加) を **[応答]** 列から選択します。
1. **[Follow-up prompt (PREVIEW)]** (フォローアッププロンプト (プレビュー)) ポップアップ ウィンドウで、次のように入力します。

    |フィールド|値|
    |--|--|
    |表示テキスト|`Turn off the device` これは、フォローアップ プロンプトに表示するように選択するカスタム テキストです。|
    |Context-only (コンテキストのみ)|選択されています。 この応答は、質問がコンテキストを指定する場合にのみ返されます。|
    |Link to answer (応答へのリンク)|`Use the sign-in screen` を入力して、既存の QnA ペアを見つけます。|


1.  1 つの一致が返されます。 この応答をフォローアップとして選択し、 **[保存]** を選択します。 

    ![フォローアップ プロンプトの [Link to answer]\(応答へのリンク\) ダイアログで、応答のテキストを使用して既存の応答を検索します。](../media/conversational-context/search-follow-up-prompt-for-existing-answer.png)

1. フォローアップ プロンプトを追加したら、忘れずに上部のナビゲーションで **[Save and train]** (保存してトレーニング) を選択してください。
  
### <a name="edit-the-display-text"></a>表示テキストを編集する 

フォローアップ プロンプトを作成し、既存の QnA ペアを **[Link to Answer]** (応答へのリンク) として選択すると、新しい **[表示テキスト]** を入力できます。 このテキストは、既存の質問を置き換えず、新しい代替質問を追加しません。 これらの値とは別になります。 

1. 表示テキストを編集するには、 **[コンテキスト]** フィールドで質問を検索し選択します。
1. その質問の行では、応答列でフォローアップ プロンプトを選択します。 
1. 編集する表示テキストを選択し、 **[編集]** を選択します。

    ![編集する表示テキストを選択し、[編集] を選択します。](../media/conversational-context/edit-existing-display-text.png)

1. **[Follow-up prompt]** (フォローアップ プロンプト) ポップアップでは、既存の表示テキスト変更できます。 
1. 表示テキストの編集が完了したら、 **[保存]** を選択します。 
1. 忘れずに上部のナビゲーションで **[Save and train]** (保存してトレーニング) を選択してください。


<!--

## To find best prompt answer, add metadata to follow-up prompts 

If you have several follow-up prompts for a given QnA pair, but you know as the knowledge base manager, that not all prompts should be returned, use metadata to categorize the prompts in the knowledge base, then send the metadata from the client application as part of the GenerateAnswer request.

In the knowledge base, when a question-and-answer pair is linked to follow-up prompts, the metadata filters are applied first, then the follow-ups are returned.

1. For the two follow-up QnA pairs, add metadata to each one:

    |Question|Add metadata|
    |--|--|
    |`Feedback on an QnA Maker service`|"Feature":"all"|
    |`Feedback on an existing feature`|"Feature":"one"|
    
    ![Add metadata to follow-up prompt so it can be filtered in conversation response from service](../media/conversational-context/add-metadata-feature-to-follow-up-prompt.png) 

1. Save and train. 

    When you send the question `Give feedback` with the metadata filter `Feature` with a value of `all`, only the QnA pair with that metadata will be returned. Both QnA pairs are not returned because they both do not match the filter. 

-->

## <a name="add-new-qna-pair-as-follow-up-prompt"></a>フォローアップ プロンプトとして新しい QnA ペアを追加する

ナレッジ ベースに新しい QnA ペアを追加します。 QnA ペアは、フォローアップ プロンプトとして既存の質問にリンクする必要があります。

1. ナレッジ ベースのツールバーから、`Accounts and Signing In` の既存の QnA ペアを検索して選択します。 

1. この質問の **[応答]** 列で **[Add follow-up prompt]\(フォローアップ プロンプトの追加\)** を選択します。 
1. **[Follow-up prompt (PREVIEW)]** (フォローアップ プロンプト (プレビュー)) で、次の値を入力して新しいフォローアップ プロンプトを作成します。 

    |テキスト フィールド|値|
    |--|--|
    |**[表示テキスト]**|`Create a Windows Account` これは、フォローアップ プロンプトに表示するように選択するカスタム テキストです。|
    |**[Context-only]** (コンテキストのみ)|選択されています。 この応答は、質問がコンテキストを指定する場合にのみ返されます。|
    |**[Link to answer]\(応答へのリンク\)**|応答として次のテキストを入力します。<br>`[Create](https://account.microsoft.com/) a Windows account with a new or existing email account.`<br>データベースを保存しトレーニングすると、このテキストは次のように変換されます |
    |||

    ![新しいプロンプト QnA の作成](../media/conversational-context/create-child-prompt-from-parent.png)


1. **[新規作成]** をクリックし、 **[保存]** を選択します。 

    これによって、新しい質問と応答のペアが作成され、選択した質問がフォローアップ プロンプトとしてリンクされました。 両方の質問の **[コンテキスト]** 列は、フォローアップ プロンプトの関係を示します。 

1. 変更、 **[表示のオプション]** を [[show context]](#show-questions-and-answers-with-context) (コンテキストを表示) に変更します。

    新しい質問は、どのようにリンクされているかを示します。

    ![新しいフォローアップ プロンプトを作成する ](../media/conversational-context/new-qna-follow-up-prompt.png)

    親の質問は、その選択肢の 1 つとして新しい質問を表示します。

    ![![両方の質問の [コンテキスト] 列は、フォローアップ プロンプトの関係を示します。](../media/conversational-context/child-prompt-created.png)](../media/conversational-context/child-prompt-created.png#lightbox)

1. フォローアップ プロンプトを追加したら、忘れずに上部のナビゲーションで **[Save and train]** (保存してトレーニング) を選択してください。

## <a name="enable-multi-turn-when-testing-follow-up-prompts"></a>フォローアップ プロンプトのテスト時に複数ターンを有効にする

**[テスト]** ウィンドウ内でフォローアップ プロンプトを使った質問をテストするときは、 **[Enable multi-turn]\(複数ターンの有効化\)** を選択して、質問を入力します。 応答には、フォローアップ プロンプトが含まれます。

![[テスト] ウィンドウで質問をテストすると、応答にフォローアップ プロンプトが含まれます。](../media/conversational-context/test-pane-with-question-having-follow-up-prompts.png)

複数ターンを有効にしない場合、応答は返されますが、フォローアップ プロンプトは返されません。

## <a name="json-request-to-return-initial-answer-and-follow-up-prompts"></a>最初の応答とフォローアップ プロンプトを返す JSON 要求

空の `context` オブジェクトを使用して、ユーザーの質問に対する応答を要求し、フォローアップ プロンプトを含めます。 

```JSON
{
  "question": "accounts and signing in",
  "top": 10,
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
            "score": 100.0,
            "id": 15,
            "source": "product-manual.pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": [
                    {
                        "displayOrder": 0,
                        "qnaId": 16,
                        "qna": null,
                        "displayText": "Use the sign-in screen"
                    },
                    {
                        "displayOrder": 1,
                        "qnaId": 17,
                        "qna": null,
                        "displayText": "Use Windows Hello to sign in"
                    },
                    {
                        "displayOrder": 2,
                        "qnaId": 18,
                        "qna": null,
                        "displayText": "Sign out"
                    },
                    {
                        "displayOrder": 0,
                        "qnaId": 79,
                        "qna": null,
                        "displayText": "Create a Windows Account"
                    }
                ]
            }
        },
        {
            "questions": [
                "Sign out"
            ],
            "answer": "**Sign out**\n\nHere's how to sign out: \n\n Go to Start , and right-click your name. Then select Sign out. ",
            "score": 38.01,
            "id": 18,
            "source": "product-manual.pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": [
                    {
                        "displayOrder": 0,
                        "qnaId": 16,
                        "qna": null,
                        "displayText": "Turn off the device"
                    }
                ]
            }
        },
        {
            "questions": [
                "Use the sign-in screen"
            ],
            "answer": "**Use the sign-in screen**\n\n1.  \n\nTurn on or wake your Surface by pressing the power button. \n\n2.  \n\nSwipe up on the screen or tap a key on the keyboard. \n\n3.  \n\nIf you see your account name and account picture, enter your password and select the right arrow or press Enter on your keyboard. \n\n4.  \n\nIf you see a different account name, select your own account from the list at the left. Then enter your password and select the right arrow or press Enter on your keyboard. ",
            "score": 27.53,
            "id": 16,
            "source": "product-manual.pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": []
            }
        }
    ]
}
```

`prompts` 配列によって `displayText` プロパティのテキストと `qnaId` 値が提供されるため、これらの応答を会話フローで次に表示される選択肢として示し、選択した値を次の要求で QnA Maker に送信できます。 

## <a name="json-request-to-return-non-initial-answer-and-follow-up-prompts"></a>最初以外の応答とフォローアップ プロンプトを返す JSON 要求

前のコンテキストを `context` オブジェクトに含めます。

次の JSON 要求では、現在の質問は `Use Windows Hello to sign in`、前の質問は `Accounts and signing in` です。 

```JSON
{
  "question": "Use Windows Hello to sign in",
  "top": 10,
  "userId": "Default",
  "isTest": false,
  "qnaId": 17,
  "context": {
    "previousQnAId": 15,
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
                "Use Windows Hello to sign in"
            ],
            "answer": "**Use Windows Hello to sign in**\n\nSince Surface Pro 4 has an infrared (IR) camera, you can set up Windows Hello to sign in just by looking at the screen. \n\nIf you have the Surface Pro 4 Type Cover with Fingerprint ID (sold separately), you can set up your Surface sign you in with a touch. \n\nFor more info, see What is Windows Hello? on Windows.com. ",
            "score": 100.0,
            "id": 17,
            "source": "product-manual.pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": []
            }
        },
        {
            "questions": [
                "Meet Surface Pro 4"
            ],
            "answer": "**Meet Surface Pro 4**\n\nGet acquainted with the features built in to your Surface Pro 4. \n\nHere’s a quick overview of Surface Pro 4 features: \n\n\n\n\n\n\n\nPower button \n\n\n\n\n\nPress the power button to turn your Surface Pro 4 on. You can also use the power button to put it to sleep and wake it when you’re ready to start working again. \n\n\n\n\n\n\n\nTouchscreen \n\n\n\n\n\nUse the 12.3” display, with its 3:2 aspect ratio and 2736 x 1824 resolution, to watch HD movies, browse the web, and use your favorite apps. \n\nThe new Surface G5 touch processor provides up to twice the touch accuracy of Surface Pro 3 and lets you use your fingers to select items, zoom in, and move things around. For more info, see Surface touchscreen on Surface.com. \n\n\n\n\n\n\n\nSurface Pen \n\n\n\n\n\nEnjoy a natural writing experience with a pen that feels like an actual pen. Use Surface Pen to launch Cortana in Windows or open OneNote and quickly jot down notes or take screenshots. \n\nSee Using Surface Pen (Surface Pro 4 version) on Surface.com for more info. \n\n\n\n\n\n\n\nKickstand \n\n\n\n\n\nFlip out the kickstand and work or play comfortably at your desk, on the couch, or while giving a hands-free presentation. \n\n\n\n\n\n\n\nWi-Fi and Bluetooth® \n\n\n\n\n\nSurface Pro 4 supports standard Wi-Fi protocols (802.11a/b/g/n/ac) and Bluetooth 4.0. Connect to a wireless network and use Bluetooth devices like mice, printers, and headsets. \n\nFor more info, see Add a Bluetooth device and Connect Surface to a wireless network on Surface.com. \n\n\n\n\n\n\n\nCameras \n\n\n\n\n\nSurface Pro 4 has two cameras for taking photos and recording video: an 8-megapixel rear-facing camera with autofocus and a 5-megapixel, high-resolution, front-facing camera. Both cameras record video in 1080p, with a 16:9 aspect ratio. Privacy lights are located on the right side of both cameras. \n\nSurface Pro 4 also has an infrared (IR) face-detection camera so you can sign in to Windows without typing a password. For more info, see Windows Hello on Surface.com. \n\nFor more camera info, see Take photos and videos with Surface and Using autofocus on Surface 3, Surface Pro 4, and Surface Book on Surface.com. \n\n\n\n\n\n\n\nMicrophones \n\n\n\n\n\nSurface Pro 4 has both a front and a back microphone. Use the front microphone for calls and recordings. Its noise-canceling feature is optimized for use with Skype and Cortana. \n\n\n\n\n\n\n\nStereo speakers \n\n\n\n\n\nStereo front speakers provide an immersive music and movie playback experience. To learn more, see Surface sound, volume, and audio accessories on Surface.com. \n\n\n\n\n",
            "score": 21.92,
            "id": 3,
            "source": "product-manual.pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": [
                    {
                        "displayOrder": 0,
                        "qnaId": 4,
                        "qna": null,
                        "displayText": "Ports and connectors"
                    }
                ]
            }
        },
        {
            "questions": [
                "Use the sign-in screen"
            ],
            "answer": "**Use the sign-in screen**\n\n1.  \n\nTurn on or wake your Surface by pressing the power button. \n\n2.  \n\nSwipe up on the screen or tap a key on the keyboard. \n\n3.  \n\nIf you see your account name and account picture, enter your password and select the right arrow or press Enter on your keyboard. \n\n4.  \n\nIf you see a different account name, select your own account from the list at the left. Then enter your password and select the right arrow or press Enter on your keyboard. ",
            "score": 19.04,
            "id": 16,
            "source": "product-manual.pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": []
            }
        }
    ]
}
```

## <a name="query-the-knowledge-base-with-the-qna-id"></a>QnA ID でナレッジ ベースをクエリする

最初の質問の応答で、フォローアップ プロンプトとそれに関連付けられた `qnaId` が返されます。 ID が付与されたので、フォローアップ プロンプトの要求本文でこれを渡すことができます。 要求本文に `qnaId` とコンテキスト オブジェクト (これには以前の QnA プロパティが含まれます) が含まれる場合、GenerateAnswer は、ランキング アルゴリズムを使用して質問テキストによる応答を見つけるのではなく、ID により正確な質問を返します。 

## <a name="displaying-prompts-and-sending-context-in-the-client-application"></a>クライアント アプリケーションでのプロンプトの表示とコンテキストの送信 

ナレッジ ベースにプロンプトを追加し、テスト ウィンドウでフローをテストしました。 次にクライアント アプリケーションでこれらのプロンプトを使用する必要があります。 Bot framework の場合、プロンプトは自動的には、クライアント アプリケーションで表示し始めません。 この [Bot Framework のサンプル](https://aka.ms/qnamakermultiturnsample)をコードに含めることによって、クライアント アプリケーション内のユーザーのクエリへの応答の一部として、推奨されるアクションまたはボタンとしてプロンプトを表示できます。 クライアント アプリケーションは、現在の QnA ID およびユーザー クエリを格納し、次回のユーザー クエリのために [GenerateAnswer API のコンテキスト オブジェクト](#json-request-to-return-non-initial-answer-and-follow-up-prompts)に渡す必要があります。 

## <a name="display-order-supported-in-api"></a>API でサポートされる表示の順序

JSON 応答で返される[表示テキストと表示の順序](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update#promptdto)は、[更新 API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update) による編集でサポートされています。 

<!--

FIX - Need to go to parent, then answer column, then edit answer. 

-->

## <a name="next-steps"></a>次の手順

コンテキスト会話について詳しくは、[ダイアログのサンプル](https://aka.ms/qnamakermultiturnsample)に関するページまたは[複数ターン会話のボット設計の概要](https://docs.microsoft.com/azure/bot-service/bot-builder-conversations?view=azure-bot-service-4.0)に関するページをご覧ください。

> [!div class="nextstepaction"]
> [ナレッジ ベースの移行](../Tutorials/migrate-knowledge-base.md)
