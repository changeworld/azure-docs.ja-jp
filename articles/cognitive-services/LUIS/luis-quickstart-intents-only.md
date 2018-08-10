---
title: 2 つの意図で簡単なアプリを作成する - Azure | Microsoft Docs
description: このクイックスタートでは、2 つの意図を使用してユーザーの発話を識別する簡単な LUIS アプリを作成する方法について説明します。エンティティは使用しません。
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 08/02/2018
ms.author: diberry
ms.openlocfilehash: de295a93d395cee4c4dfbea4f2e7f7338036feb8
ms.sourcegitcommit: eaad191ede3510f07505b11e2d1bbfbaa7585dbd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/03/2018
ms.locfileid: "39494375"
---
# <a name="tutorial-1-build-app-with-custom-domain"></a>チュートリアル: 1. カスタム ドメインを使用してアプリをビルドする
このチュートリアルでは、ユーザーがアプリに送信した発話 (テキスト) に基づいて、**意図**を使用してそのユーザーの_意図_を判断する方法を実践するアプリを作成します。 完成すると、クラウド内で LUIS エンドポイントが実行されるようになります。

このアプリは最も簡単な種類の LUIS アプリで、発話からデータを抽出しません。 発話からユーザーの意図のみを判断します。

<!-- green checkmark -->
> [!div class="checklist"]
> * 人事 (HR) ドメインの新しいアプリを作成する 
> * GetJobInformation 意図を追加する
> * GetJobInformation 意図に発話の例を追加する 
> * アプリをトレーニングして公開する
> * アプリのエンドポイントをクエリして LUIS JSON の応答を表示する
> * ApplyForJob 意図を追加する
> * ApplyForJob 意図に発話の例を追加する 
> * もう一度トレーニング、公開し、エンドポイントをクエリする 

[!include[LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="purpose-of-the-app"></a>アプリの目的
このアプリにはいくつかの意図があります。 最初の意図 **`GetJobInformation`** は、ユーザーが社内で有効な仕事に関する情報を必要としているタイミングを識別します。 2 番目の意図 **`None`** は、その他すべての種類の発話を識別します。 このクイックスタートの後半に、3 番目の意図 `ApplyForJob` が追加されます。 

## <a name="create-a-new-app"></a>新しいアプリの作成
1. [LUIS](luis-reference-regions.md#luis-website) Web サイトにログインします。 LUIS エンドポイントを公開する必要がある[リージョン](luis-reference-regions.md#publishing-regions)にログインします。

2. [LUIS](luis-reference-regions.md#luis-website) Web サイトで **[Create new app]\(アプリの新規作成\)** を選択します。  

    [![](media/luis-quickstart-intents-only/app-list.png "[My Apps]\(マイ アプリ\) ページのスクリーン ショット")](media/luis-quickstart-intents-only/app-list.png#lightbox)

3. ポップアップ ダイアログで、名前に「`HumanResources`」と入力します。 このアプリでは、会社の人事部門に関する質問に対応します。 人事部門では、会社のポストの補充など、雇用に関連する問題に対処します。

    ![LUIS の新規アプリ](./media/luis-quickstart-intents-only/create-app.png)

4. 終了が処理すると、アプリの **[Intents]\(意図\)** ページに **None** 意図が表示されます。 

## <a name="create-getjobinformation-intention"></a>GetJobInformation 意図を作成する
1. **[Create new intent]\(意図の新規作成\)** を選択します。 新しい意図の名前として「`GetJobInformation`」と入力します。 この意図は、ユーザーが社内で空きのある仕事に関する情報を必要とするすべてのタイミングで予測されます。

    ![](media/luis-quickstart-intents-only/create-intent.png "[Create new intent]\(意図の新規作成\) ダイアログのスクリーンショット")

    意図を作成すると、識別する情報のカテゴリが作成されます。 カテゴリに名前を付けると、LUIS クエリ結果を使用する他のアプリケーションが、そのカテゴリ名を使用して、適切な回答を見つけることができます。 LUIS はこれらの質問に回答しません。どのような種類の情報が自然言語で質問されたかを明らかにするだけです。 

2. ユーザーの要求として予想される 7 つの発話をこの意図に追加します。次はその例です。

    | 発話の例|
    |--|
    |今日新しく登録された仕事はありますか。|
    |シニア エンジニアではどのようなポストがありますか。|
    |データベースの仕事はありますか。|
    |経理関連の新しい職を探しています|
    |仕事の一覧はどこにありますか。|
    |新しい仕事はありますか。|
    |シアトル オフィスに新しいポストはありますか。|

    [![](media/luis-quickstart-intents-only/utterance-getstoreinfo.png "MyStore 意図用の新しい発話を入力しているスクリーンショット")](media/luis-quickstart-intents-only/utterance-getstoreinfo.png#lightbox)

3. 現在、この LUIS アプリの **None** 意図には発話はありません。 アプリが回答しない発話が必要です。 空白のままにしないでください。 左側のパネルから **[Intents]\(意図\)** を選びます。 

4. **None** 意図を選択します。 ユーザーが入力する可能性があるがアプリには関係のない 3 つの発話を追加します。 求人に関するアプリの場合、**None** 意図の発話の例として、次のような発話が挙げられます。

    | 発話の例|
    |--|
    |吠える犬はうっとうしい|
    |ピザを注文して|
    |海の中のペンギン|

    LUIS 呼び出し元アプリケーション (チャットボットなど) では、LUIS が発話に対して **None** 意図を返した場合、ボットがユーザーが会話の終了を望んでいるかどうかを確認することがあります。 また、ユーザーが終了を望んでいない場合、チャットボットは会話を続けるよう指示することもあります。 

## <a name="train-and-publish-the-app"></a>アプリをトレーニングして公開する

[!include[LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-app-to-endpoint"></a>エンドポイントにアプリを公開する

[!include[LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)] 

## <a name="query-endpoint-for-getjobinformation-intent"></a>GetJobInformation 意図のエンドポイントをクエリする

1. [!include[LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. アドレスの URL の末尾に移動し、「`I'm looking for a job with Natual Language Processing`」と入力します。 最後のクエリ文字列パラメーターは `q` です。これは発話の**クエリ**です。 この発話は手順 4 のどの発話例とも同じではないので、よいテストであり、最もスコアの高い意図として `GetJobInformation` 意図が返される必要があります。 

    ```
    {
      "query": "I'm looking for a job with Natual Language Processing",
      "topScoringIntent": {
        "intent": "GetJobInformation",
        "score": 0.8965092
      },
      "intents": [
        {
          "intent": "GetJobInformation",
          "score": 0.8965092
        },
        {
          "intent": "None",
          "score": 0.147104025
        }
      ],
      "entities": []
    }
    ```

## <a name="create-applyforjob-intention"></a>ApplyForJob 意図を作成する
LUIS Web サイトのブラウザー タブに戻り、仕事に応募するための新しい意図を作成します。

1. 右上のメニューから **[Build]\(ビルド\)** を選択し、アプリのビルドに戻ります。

2. 左側のメニューから **[Intents]\(意図\)** を選びます。

3. **[Create new intent]\(意図の新規作成\)** を選択して `ApplyForJob` という名前を入力します。 

    ![新しい意図を作成するための LUIS ダイアログ](./media/luis-quickstart-intents-only/create-applyforjob-intent.png)

4. ユーザーの質問として予想される複数の発話をこの意図に追加します。次はその例です。

    | 発話の例|
    |--|
    |新しい経理の仕事に応募したい|
    |ジョブ 123456 の申込書に記入して|
    |エンジニアのポストにレジュメを送信して|
    |ポスト 654234 向けの職務経歴書|
    |ジョブ 567890 の書類|

    [![](media/luis-quickstart-intents-only/utterance-applyforjob.png "ApplyForJob 意図用の新しい発話を入力しているスクリーンショット")](media/luis-quickstart-intents-only/utterance-applyforjob.png#lightbox)

    現時点ではその意図が正しいことを LUIS が判断できないため、ラベル付きの意図は赤で囲まれています。 アプリをトレーニングすることで、LUIS にその発話が正しい意図であることを指示します。 

    再度[トレーニングして公開](#train-and-publish-the-app)します。 

## <a name="query-endpoint-for-applyforjob-intent"></a>ApplyForJob 意図のエンドポイントをクエリする

1. [!include[LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. ブラウザーの新しいウィンドウで、URL の最後に `Can I submit my resume for job 235986` と入力します。 

    ```
    {
      "query": "Can I submit my resume for job 235986",
      "topScoringIntent": {
        "intent": "ApplyForJob",
        "score": 0.9166808
      },
      "intents": [
        {
          "intent": "ApplyForJob",
          "score": 0.9166808
        },
        {
          "intent": "GetJobInformation",
          "score": 0.07162977
        },
        {
          "intent": "None",
          "score": 0.0262826588
        }
      ],
      "entities": []
    }
    ```

## <a name="what-has-this-luis-app-accomplished"></a>この LUIS アプリの処理内容
このアプリは、いくつかの意図を使用することで、同じ意図で言い方が異なる自然言語クエリを識別しました。 

JSON の結果は最も高いスコアの意図を識別します。 すべてのスコアは 0 から 1 の範囲であり、1 に近いほどよいスコアです。 `GetJobInformation` 意図と `None` 意図のスコアはよりゼロに近くなっています。 

## <a name="where-is-this-luis-data-used"></a>この LUIS データの使用場所 
LUIS はこの要求の処理を完了しています。 チャットボットなどの呼び出し元アプリケーションは、topScoringIntent の結果を受け、(LUIS に格納されていない) 情報を探して質問に回答するか、会話を終了します。 これらはボットつまり呼び出し元アプリケーションのプログラムのオプションです。 LUIS ではその作業を行いません。 LUIS はユーザーの意図を判断するだけです。 

## <a name="clean-up-resources"></a>リソースのクリーンアップ

[!include[LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [このアプリに事前構築済みの意図とエンティティを追加する](luis-tutorial-prebuilt-intents-entities.md)
