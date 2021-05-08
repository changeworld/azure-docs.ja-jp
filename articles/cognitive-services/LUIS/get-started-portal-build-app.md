---
title: クイック スタート:LUIS ポータルで新しいアプリを作成する
description: このクイックスタートでは、アプリの基本パーツ、意図、エンティティを作成すると共に、LUIS ポータルからサンプル発話を使ってテストを行います。
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 03/26/2021
ms.openlocfilehash: c2f2bafe68dd3f3dad704ba12cdd4d01bf4f6588
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/03/2021
ms.locfileid: "106278764"
---
# <a name="quickstart-create-a-new-app-in-the-luis-portal"></a>クイック スタート:LUIS ポータルで新しいアプリを作成する

このクイック スタートでは、LUIS ポータルで新しいアプリを構築します。 まず、アプリ、**意図**、および **エンティティ** の基本パーツを作成します。 次いで、インタラクティブ テスト パネルにユーザーの発話例を入力し、予測される意図を取得して、アプリをテストします。

[!INCLUDE [Sign in to LUIS](./includes/sign-in-process.md)]

## <a name="create-an-app"></a>アプリを作成する

アプリケーションを作成するには、 **[+ 新しいアプリ]** を選択します。 

表示されたウィンドウで、次の情報を入力します。

|名前  |[説明]  |
|---------|---------|
|名前     | アプリの名前。 たとえば、"home automation"。 必須。        |
|カルチャ     | アプリによって認識され、話される言語。 必須。   |
|説明 | アプリの説明。 省略可能。
|予測リソース | クエリを受け取る予測リソース。 省略可能。 |

**[完了]** を選択します。

>[!NOTE]
>カルチャは、アプリケーションを作成した後に変更できません。


## <a name="create-intents"></a>意図の作成

LUIS アプリを作成したら、意図を作成する必要があります。 意図は、ユーザーから発せられたテキストを分類する手段です。 たとえば、人事管理アプリに 2 つの機能を含めるとします。 ユーザーが次のことをできるようにします。

 1. 仕事を探し、応募する
 1. 仕事に応募するためのフォームを探す

アプリの 2 つの _目的_ は次の意図に対応しています。

|Intent|ユーザーから発せられたテキストの例<br>"_発話_" と呼ばれる|
|--|--|
|ApplyForJob|`I want to apply for the new software engineering position in Cairo.`|
|FindForm|`Where is the job transfer form hrf-123456?`|

意図を作成するには、次の手順を実行します。

1. アプリが作成されたら、 **[ビルド]** セクションの **[意図]** ページが表示されていることを確認します。 **［作成］** を選択します

   [![新しい意図を作成するために [作成] が選択されているスクリーンショット](./media/get-started-portal-build-app/create-new-intent-button.png)](./media/get-started-portal-build-app/create-new-intent-button.png#lightbox)

1. 意図の名前として `FindForm` を入力し、 **[完了]** を選択します。

## <a name="add-an-example-utterance"></a>発話の例を追加する

意図を作成したら、発話の例を追加します。 発話の例は、ユーザーがチャット ボットや他のクライアント アプリケーションに入力するテキストです。 ユーザーのテキストの意図を、LUIS 意図にマッピングします。 このアプリケーション例の `FindForm` 意図では、発話の例にフォーム番号を含めます。 ユーザーの要求を実行するためにクライアント アプリケーションはフォーム番号が必要なので、発話にそれを含めることは重要です。

`FindForm` の **[意図]** ページで、 **[発話の例]** の下に次の発話例を追加します。 

|#|発話の例|
|--|--|
|1|`Looking for hrf-123456`|
|2|`Where is the human resources form hrf-234591?`|
|3|`hrf-345623, where is it`|
|4|`Is it possible to send me hrf-345794`|
|5|`Do I need hrf-234695 to apply for an internal job?`|
|6|`Does my manager need to know I'm applying for a job with hrf-234091`|
|7|`Where do I send hrf-234918? Do I get an email response it was received?`|
|8|`hrf-234555`|
|9|`When was hrf-234987 updated?`|
|10|`Do I use form hrf-876345 to apply for engineering positions`|
|11|`Was a new version of hrf-765234 submitted for my open req?`|
|12|`Do I use hrf-234234 for international jobs?`|
|13|`hrf-234598 spelling mistake`|
|14|`will hrf-234567 be edited for new requirements`|
|15|`hrf-123456, hrf-123123, hrf-234567`|

これらの発話の例は、次の点を意図的に変化させています。

* 発話の長さ
* [句読点](luis-reference-application-settings.md#punctuation-normalization)
* 単語の選択
* 動詞の時制 (現在、過去、未来)
* 単語の順序


## <a name="create-a-regular-expression-entity"></a>正規表現エンティティを作成する

実行時の予測応答としてフォーム番号を返すには、フォーム番号をエンティティとして抽出する必要があります。 フォーム番号のテキストは、高度に構造化されているため、正規表現エンティティを使用できます。 正規表現エンティティを作成するには、以下の手順に従います。

1. 左側のメニューから **[エンティティ]** を選択します。

1. **[エンティティ]** ページの **[作成]** を選択します。

1. 「`FormNumber`」という名前を入力し、 **[Regex]\(正規表現\)** エンティティ型を選択します。

1. 正規表現 `hrf-[0-9]{6}` を **[Regex]\(正規表現\)** フィールドに入力します。 このエントリは、リテラル文字 `hrf-` と一致し、厳密に 6 桁に対応します。続けて **[作成]** を選択します。

    > [!div class="mx-imgBorder"]
    > ![正規表現エンティティを作成しているスクリーンショット](./media/get-started-portal-build-app/create-regular-expression-entity.png)


    このエンティティは、任意の意図で、この正規表現と一致するテキストをすべて抽出します。

## <a name="add-example-utterances-to-the-none-intent"></a>発話の例を None 意図に追加する

**None** 意図は、fallback 意図であるため、空のままにしないでください。 この意図には、アプリの他の意図に追加した 10 の発話の例それぞれに対して 1 つの発話を含める必要があります。

**None** 意図の発話の例は、クライアント アプリケーションのドメイン外にある必要があります。

1. 左側のメニューから **[意図]** を選択した後、意図の一覧から **[None]** を選択します。

1. 以下の発話の例を意図に追加します。

   |None 意図の発話の例|
   |--|
   |`Barking dogs are annoying`|
   |`Penguins in the ocean`|

   このアプリの場合、これらの発話の例は、ドメイン外です。 ドメインに動物または海洋が含まれる場合、**None** 意図には別の発話の例を使用する必要があります。

## <a name="train-the-app"></a>アプリをトレーニングする

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="look-at-the-regular-expression-entity-in-the-example-utterances"></a>発話の例内の正規表現エンティティを見る

1. 左側のメニューから **[意図]** を選択して、エンティティが **FindForm** 意図内にあることを確認します。 その後、**FindForm** 意図を選択します。

   エンティティは、発話の例の中の出現箇所でマークされます。

   > [!div class="mx-imgBorder"]
   > [![エンティティによってマークが付けられたすべての発話の例のスクリーンショット](./media/get-started-portal-build-app/all-example-utterances-marked-with-entities.png)](./media/get-started-portal-build-app/all-example-utterances-marked-with-entities.png#lightbox)

## <a name="test-your-new-app-with-the-interactive-test-pane"></a>インタラクティブな [テスト] ウィンドウを使用して新しいアプリをテストする

LUIS ポータル内のインタラクティブな **[テスト]** ウィンドウを使用して、アプリでまだ確認されていない新しい発話からエンティティが抽出されることを確認します。

1. 右上のメニューから **[テスト]** を選択します。

1. 新しい発話を追加して、Enter キーを押します。

   ```Is there a form named hrf-234098```

    **[検査]** を選択してエンティティの予測を表示します。

   > [!div class="mx-imgBorder"]
   > ![テスト ウィンドウで新しい発話をテストしているスクリーンショット](./media/get-started-portal-build-app/test-new-utterance.png)

   予測意図の上位は正しく **FindForm** になっており、信頼度は 90% を超えています (0.977)。 抽出された **FormNumber** エンティティの値は hrf-234098 です。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このクイック スタートが完了し、次のクイック スタートに進まない場合は、上部のナビゲーション メニューから **[マイ アプリ]** を選択します。 次に、一覧からアプリの左側にあるチェック ボックスをオンにし、一覧の上にあるコンテキスト ツール バーから **[削除]** を選択します。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [アプリのデプロイ](get-started-portal-deploy-app.md)
