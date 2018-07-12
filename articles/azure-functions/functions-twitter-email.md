---
title: Azure Logic Apps と統合される関数を作成する | Microsoft Docs
description: Azure Logic Apps および Azure Cognitive Services と統合し、ツイートのセンチメントを分類して、センチメントが良くない場合に通知を送信する関数を作成します。
services: functions, logic-apps, cognitive-services
keywords: ワークフロー, クラウド アプリ, クラウド サービス, ビジネス プロセス, システム統合, エンタープライズ アプリケーション統合, EAI
documentationcenter: ''
author: ggailey777
manager: cfowler
editor: ''
ms.assetid: 60495cc5-1638-4bf0-8174-52786d227734
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/12/2017
ms.author: glenga
ms.custom: mvc, cc996988-fb4f-47
ms.openlocfilehash: 16a46b4c49687186e25c399dcc2c5c168e7c5004
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2018
ms.locfileid: "38586876"
---
# <a name="create-a-function-that-integrates-with-azure-logic-apps"></a>Azure Logic Apps と統合される関数を作成する

Azure Functions は、Logic Apps デザイナーで Azure Logic Apps と統合できます。 この統合により、他の Azure サービスやサードパーティ製のサービスとのオーケストレーションにおいて、Functions のコンピューティング機能を使用することができます。 

このチュートリアルでは、Azure 上で Logic Apps および Microsoft Cognitive Services と併せて Functions を使用することで、Twitter の投稿のセンチメントを分析する方法を説明します。 HTTP によってトリガーされる関数は、センチメント スコアに基づいて、ツイートを緑、黄、赤に分類します。 ネガティブなセンチメントが検出されると、電子メールが送信されます。 

![Logic Apps デザイナーでのアプリの最初の 2 つの手順の画像](media/functions-twitter-email/designer1.png)

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * Cognitive Services API リソースを作成します。
> * ツイートのセンチメントを分類する関数を作成します。
> * Twitter に接続するロジック アプリを作成します。
> * センチメントの検出をロジック アプリに追加します。 
> * ロジック アプリを関数に接続します。
> * 関数からの応答に基づいて電子メールを送信します。

## <a name="prerequisites"></a>前提条件

+ アクティブな [Twitter](https://twitter.com/) アカウント。 
+ [Outlook.com](https://outlook.com/) のアカウント (通知の送信用)。
+ このトピックでは、「[Azure Portal で初めての関数を作成する](functions-create-first-azure-function.md)」で作成したリソースを使用して作業を開始します。  
リソースの作成が済んでいない場合は、すぐにこれらの手順に従って Function App を作成してください。

## <a name="create-a-cognitive-services-resource"></a>Cognitive Services リソースの作成

Cognitive Services APIs は、個々のリソースとして Azure で使用できます。 Text Analytics API を使用して、監視されているツイートのセンチメントを検出します。

1. [Azure Portal](https://portal.azure.com/) にサインインします。

2. Azure Portal の左上隅にある **[リソースの作成]** をクリックします。

3. **[AI + Cognitive Services]** > **[Text Analytics API]** をクリックします。 表で指定されている設定を使用したうえで、条項に同意し、**[ダッシュボードにピン留めする]** チェック ボックスをオンにします。

    ![Cognitive リソースを作成するページ](media/functions-twitter-email/cog_svcs_resource.png)

    | Setting      |  推奨値   | Description                                        |
    | --- | --- | --- |
    | **名前** | MyCognitiveServicesAccnt | 一意のアカウント名を選択します。 |
    | **場所** | 米国西部 | お近くの場所を使用します。 |
    | **[価格レベル]** | F0 | まずは低いレベルを選んでください。 呼び出し回数が不足する場合は、高いレベルにスケーリングします。|
    | **[リソース グループ]** | myResourceGroup | このチュートリアルでは、すべてのサービスで同じリソース グループを使用します。|

4. **[作成]** をクリックして、リソースを作成します。 作成されたら、ダッシュボードにピン留めされた新しい Cognitive Services リソースを選択します。 

5. 左側のナビゲーション列で **[キー]** をクリックし、**[キー 1]** の値をコピーして保存します。 このキーは、ロジック アプリを Cognitive Services API に接続するために使用します。 
 
    ![構成する](media/functions-twitter-email/keys.png)

## <a name="create-the-function-app"></a>Function App の作成

関数は、Logic Apps ワークフローの処理タスクをオフロードするのに役立ちます。 このチュートリアルでは、HTTP によってトリガーされる関数を使用して、Cognitive Services からのツイート センチメント スコアを処理し、カテゴリ値を返します。  

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

## <a name="create-an-http-triggered-function"></a>HTTP によってトリガーされる関数の作成  

1. Function App を展開し、**[関数]** の横にある **[+]** ボタンをクリックします。 これが Function App で初めての関数の場合、**[カスタム関数]** を選びます。 関数テンプレートの完全なセットが表示されます。

    ![Azure Portal での関数のクイック スタート ページ](media/functions-twitter-email/add-first-function.png)

2. 検索フィールドに「`http`」と入力し、HTTP トリガー テンプレートとして **C#** を選択します。 

    ![HTTP トリガーの選択](./media/functions-twitter-email/select-http-trigger-portal.png)

3. 使用する関数の **[名前]** を入力し、**[[認証レベル]](functions-bindings-http-webhook.md#http-auth)** として `Function` を選択し、**[作成]** を選択します。 

    ![HTTP によってトリガーされる関数の作成](./media/functions-twitter-email/select-http-trigger-portal-2.png)

    これは、HTTP トリガー テンプレートを使用して C# スクリプトの関数を作成します。 コードは新しいウィンドウに `run.csx` として表示されます。

4. この `run.csx` ファイルの内容を次のコードに置き換えて、**[保存]** をクリックします。

    ```csharp
    using System.Net;
    
    public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
    {
        // The sentiment category defaults to 'GREEN'. 
        string category = "GREEN";
    
        // Get the sentiment score from the request body.
        double score = await req.Content.ReadAsAsync<double>();
        log.Info(string.Format("The sentiment score received is '{0}'.",
                    score.ToString()));
    
        // Set the category based on the sentiment score.
        if (score < .3)
        {
            category = "RED";
        }
        else if (score < .6)
        {
            category = "YELLOW";
        }
        return req.CreateResponse(HttpStatusCode.OK, category);
    }
    ```
    この関数コードは、要求で受信したセンチメント スコアに基づいて、色のカテゴリを返します。 

4. 関数をテストするには、一番右の **[テスト]** をクリックして [テスト] タブを展開します。**[要求本文]** に「`0.2`」という値を入力し、**[実行]** をクリックします。 応答本文で **RED** という値が返されます。 

    ![Azure Portal で関数をテストする](./media/functions-twitter-email/test.png)

これで、センチメント スコアを分類する関数が作成できました。 次に、Twitter および Cognitive Services API に関数を統合するロジック アプリを作成します。 

## <a name="create-a-logic-app"></a>ロジック アプリを作成します   

1. Azure Portal で、左上隅にある **[新規]** ボタンをクリックします。

2. **[エンタープライズ統合]** > **[ロジック アプリ]** の順にクリックします。 表で指定されている設定を使用して、**[ダッシュボードにピン留めする]** チェック ボックスをオンにし、**[作成]** をクリックします。
 
4. 次に、`TweetSentiment` などの**名前**を入力し、表で指定されている設定を使用したうえで、条項に同意します。さらに、**[ダッシュボードにピン留めする]** チェック ボックスをオンにします。

    ![Azure Portal でロジック アプリを作成する](./media/functions-twitter-email/new_logic_app.png)

    | Setting      |  推奨値   | Description                                        |
    | ----------------- | ------------ | ------------- |
    | **名前** | TweetSentiment | アプリの適切な名前を選択します。 |
    | **[リソース グループ]** | myResourceGroup | 以前と同じ既存のリソース グループを選択します。 |
    | **場所** | 米国東部 | 近くの場所を選択します。 |    

4. **[ダッシュボードにピン留めする]** を選択し、**[作成]** をクリックしてロジック アプリを作成します。 

5. アプリが作成されたら、ダッシュボードにピン留めされた新しいロジック アプリをクリックします。 Logic Apps デザイナーで、下へスクロールして **[空のロジック アプリ]** テンプレートをクリックします。 

    ![空の Logic Apps テンプレート](media/functions-twitter-email/blank.png)

これで、Logic Apps デザイナーを使用してサービスとトリガーをアプリに追加できるようになりました。

## <a name="connect-to-twitter"></a>Twitter への接続

最初に、Twitter アカウントへの接続を作成します。 ロジック アプリはツイートをポーリングし、これによりアプリの実行がトリガーされます。

1. デザイナーで、**Twitter** サービスをクリックし、**[新しいツイートが投稿されたら]** トリガーをクリックします。 Twitter アカウントにサインインして、Logic Apps によるアカウントの使用を承認します。

2. 次の表で指定されている Twitter トリガーの設定を使用します。 

    ![Twitter コネクタの設定](media/functions-twitter-email/azure_tweet.png)

    | Setting      |  推奨値   | 説明                                        |
    | ----------------- | ------------ | ------------- |
    | **[検索テキスト]** | #Azure | 選択した間隔で新しいツイートが十分に投稿される程度に一般的なハッシュタグを使用します。 Free レベルを使用している状態で、使用頻度の高すぎるハッシュタグを使用すると、Cognitive Services API でのトランザクションのクォータがすぐに上限に達してしまう場合があります。 |
    | **頻度** | [分] | Twitter のポーリングに使用する頻度の単位です。  |
    | **間隔** | 15 | 頻度の単位での、Twitter に対する要求間の間隔です。 |

3.  **[保存]** をクリックして、Twitter アカウントに接続します。 

これで、アプリが Twitter に接続されました。 次はテキスト分析に接続し、収集されたツイートのセンチメントを検出します。

## <a name="add-sentiment-detection"></a>センチメントの検出を追加する

1. **[新しいステップ]**、**[アクションの追加]** の順にクリックします。

    ![[新しいステップ]、[アクションの追加]](media/functions-twitter-email/new_step.png)

2. **[アクションを選択してください]** で **[テキスト分析]** をクリックし、**[Detect sentiment]\(センチメントの検出\)** アクションをクリックします。

    ![センチメントを検出する](media/functions-twitter-email/detect_sent.png)

3. `MyCognitiveServicesConnection` などの接続名を入力し、保存した Cognitive Services API 用のキーを貼り付けてから、**[作成]** をクリックします。  

4. **[Text to analyze]\(分析するテキスト\)** > **[ツイート テキスト]** の順にクリックし、**[保存]** をクリックします。  

    ![センチメントを検出する](media/functions-twitter-email/ds_tta.png)

これでセンチメントの検出が構成されたので、センチメント スコアの出力を使用する関数への接続を追加できます。

## <a name="connect-sentiment-output-to-your-function"></a>センチメントの出力を関数に接続する

1. Logic Apps デザイナーで、**[新しいステップ]** > **[アクションの追加]** の順にクリックし、**[Azure Functions]** をクリックします。 

2. **[Azure 関数を選択する]** をクリックして、先ほど作成した **CategorizeSentiment** 関数を選択します。  

    ![[Azure 関数を選択する] を示す Azure 関数ボックス](media/functions-twitter-email/choose_fun.png)

3. **[要求本文]** で **[Score]\(スコア\)** をクリックし、**[保存]** をクリックします。

    ![Score](media/functions-twitter-email/trigger_score.png)

これで、ロジック アプリからセンチメント スコアが送信されたときに、関数がトリガーされます。 関数によって、色分けされたカテゴリがロジック アプリに返されます。 次に、関数からセンチメントの値 **RED** が返されたときに送信される電子メール通知を追加します。 

## <a name="add-email-notifications"></a>電子メール通知を追加する

ワークフローの最後の部分では、センチメント スコアが _RED_ だったときに電子メールがトリガーされます。 このトピックでは Outlook.com コネクタを使用します。 Gmail や Office 365 の Outlook コネクタを使用して同様の手順を実行することもできます。   

1. Logic Apps デザイナーで、**[新しいステップ]** > **[条件の追加]** をクリックします。 

2. **[値の選択]** をクリックして、**[本文]** をクリックします。 **[次の値に等しい]** を選択し、**[値の選択]** をクリックして「`RED`」と入力し、**[保存]** をクリックします。 

    ![ロジック アプリに条件を追加する](media/functions-twitter-email/condition.png)

3. **[true の場合]** で **[アクションの追加]** をクリックします。`outlook.com` を検索して、**[電子メールの送信]** をクリックし、Outlook.com アカウントにサインインします。
    
    ![条件に対するアクションを選択する](media/functions-twitter-email/outlook.png)

    > [!NOTE]
    > Outlook.com アカウントを取得していない場合は、Gmail や Office 365 Outlook など、別のコネクタを選択できます。

4. **[電子メールの送信]** アクションでは、表で指定されている電子メール設定を使用します。 

    ![[電子メールの送信] アクション用に電子メールを構成する](media/functions-twitter-email/send_email.png)

    | Setting      |  推奨値   | 説明  |
    | ----------------- | ------------ | ------------- |
    | **To** | メール アドレスを入力します。 | 通知を受け取る電子メール アドレス。 |
    | **[件名]** | "ネガティブなツイートのセンチメントを検出しました"  | 電子メール通知の件名。  |
    | **本文** | [ツイート テキスト]、[場所] | **[ツイート テキスト]** パラメーターと **[場所]** パラメーターをクリックします。 |

5.  **[Save]** をクリックします。

これでワークフローが完成したので、ロジック アプリを有効にして、関数の動作を確認できます。

## <a name="test-the-workflow"></a>ワークフローをテストする

1. Logic Apps デザイナーで **[実行]** をクリックしてアプリを起動します。

2. 左の列で **[概要]** をクリックしてロジック アプリの状態を確認します。 
 
    ![ロジック アプリの実行の状態](media/functions-twitter-email/over1.png)

3. (省略可能) 実行中のいずれかをクリックすると、その実行に関する詳細が表示されます。

4. 関数に移動し、ログを表示して、センチメント値の受信と処理が行われたことを確認します。
 
    ![関数のログを表示する](media/functions-twitter-email/sent.png)

5. ネガティブである可能性のあるセンチメントが検出されると、電子メールが届きます。 電子メールが届かない場合は、関数コードを変更して、毎回 RED が返されるようにすることができます。

        return req.CreateResponse(HttpStatusCode.OK, "RED");

    電子メール通知を確認した後、元のコードに戻してください。

        return req.CreateResponse(HttpStatusCode.OK, category);

    > [!IMPORTANT]
    > このチュートリアルを完了した後は、ロジック アプリを無効にする必要があります。 アプリを無効にすることで、実行に対して課金されたり、Cognitive Services API でのトランザクションが上限に達したりするのを回避できます。

ここまでで、Functions を Logic Apps ワークフローと簡単に統合できることが確認できました。

## <a name="disable-the-logic-app"></a>ロジック アプリを無効にする

ロジック アプリを無効にするには、**[概要]** をクリックし、画面上部の **[Disable]\(無効化\)** をクリックします。 これでロジック アプリが停止され、アプリを削除しなくても課金が停止されます。 

![関数のログ](media/functions-twitter-email/disable-logic-app.png)

## <a name="next-steps"></a>次の手順

このチュートリアルで学習した内容は次のとおりです。

> [!div class="checklist"]
> * Cognitive Services API リソースを作成します。
> * ツイートのセンチメントを分類する関数を作成します。
> * Twitter に接続するロジック アプリを作成します。
> * センチメントの検出をロジック アプリに追加します。 
> * ロジック アプリを関数に接続します。
> * 関数からの応答に基づいて電子メールを送信します。

次のチュートリアルに進み、関数用のサーバーレス API を作成する方法を学習してください。

> [!div class="nextstepaction"] 
> [Azure Functions を使用してサーバーレス API を作成する](functions-create-serverless-api.md)

Logic Apps の詳細については、[Azure Logic Apps](../logic-apps/logic-apps-overview.md) に関するページを参照してください。

