---
title: "Azure でのサーバーなしのソーシャル メディアのダッシュボードの構築 | Microsoft Docs"
description: "Azure でのサーバーなしのソーシャル メディアのダッシュボードの構築"
services: functions, logic-apps, cognitive-services
keywords: "ワークフロー, クラウド アプリ, クラウド サービス, ビジネス プロセス, システム統合, エンタープライズ アプリケーション統合, EAI"
documentationcenter: 
author: rick-anderson
manager: wpickett
editor: 
ms.assetid: 60495cc5-1638-4bf0-8174-52786d227734
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2017
ms.author: riande
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 0d3eb2af197e9923d8e4a86bf1a0033f61e3c568
ms.contentlocale: ja-jp
ms.lasthandoff: 05/10/2017

---

# <a name="building-a-serverless-social-media-dashboard-in-azure"></a>Azure でのサーバーなしのソーシャル メディアのダッシュボードの構築

[Azure Functions](functions-overview.md) を [Azure Logic Apps](../logic-apps/logic-apps-what-are-logic-apps.md) と連携すると、他の Azure のサービスやサードパーティー製のサービスとの複雑なオーケストレーションを実現できます。 このトピックでは、ソーシャル メディアのフィードからロジック アプリをトリガーし、[Azure Cognitive Services](../cognitive-services/Welcome.md) を使用してテキストを分析する方法を説明します。

この記事では、Azure Portal で次のようなロジック アプリを作成する方法について説明します。

> [!div class="checklist"]
> * 指定されたキーワードまたはハッシュタグを使用して新しいツイートをチェックする。
> * **センチメントの検出**コネクタを使用して、(役に立たなかったものから良かったものまで) ツイートのセンチメントを評価する。
> * Azure の関数を使用して、ツイートのセンチメントを 3 つのカテゴリ (赤、黄、緑 - それぞれ、不良、中間、良) に分類する。
> * センチメントが赤色 (不良) かどうかをチェックするために条件を使用する。
> * 条件が赤色の場合は、電子メールを送信する。

次の画像は、デザイナー内のロジック アプリの一部を示しています。

![ロジック アプリ デザイナーでのアプリの最初の 2 つの手順の画像](media/functions-twitter-email/designer1.png)

## <a name="prerequisites"></a>前提条件

* Azure アカウント。 Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/) を作成してください。
* Twitter アカウント。

## <a name="create-a-function-app"></a>Function App を作成する
 
[!INCLUDE [functions-create-function-app-portal](../../includes/functions-create-function-app-portal2.md)]

### <a name="create-a-categorize-function"></a>分類関数を作成する

関数アプリのデプロイが完了したら、新しい関数アプリを開きます。 このセクションでは、ツイートのセンチメントを 3 つのカテゴリ (赤、黄、緑 - それぞれ、不良、中間、良) に分類する関数を作成します。

![[Function App] ブレード、[関数 +]](media/functions-twitter-email/add_fun.png)

**[Webhook + API]**、**[CSharp]** を既定のままにして、**[この関数を作成する]** を選択します。

![[Function App] ブレード、[関数 +]](media/functions-twitter-email/add_fun2.png)

構築しているアプリからオンデマンドで呼び出すことができる Webhook/API (HTTP トリガーとも呼ばれます) 関数を作成しました。 スケジュールに従って実行される関数を作成する場合は、タイマー関数を作成します。

この *run.csx* ファイルの内容を次のコードに置き換えます。

```c#
using System.Net;

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
{
    log.Info("C# HTTP trigger function processed a request.");
    string category = "GREEN";

    // Get request body.
    double score = await req.Content.ReadAsAsync<double>();

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

変更を保存します。

### <a name="test-the-function"></a>関数をテストする

(コード ボックスの右側にある) **[テスト]** を選択します。  **[要求本文]** テキスト ボックスに「0.2」を入力して、**[実行]** を選択します。 出力には "RED" が表示され、HTTP の状態は "200 OK" になります。

 ![test ](media/functions-twitter-email/test.png)

## <a name="cognitive-services"></a>Cognitive Services

Cognitive Services アカウントを作成します。 Cognitive Services アカウントは、監視しているツイートのセンチメントを検出するために必要です。

**[新規]、[インテリジェンス + 分析]、[Cognitive Services]** の順に移動します。 各必須フィールドを設定します。

![[Create Cognitive account (Cognitive アカウントの作成)] ブレード](media/functions-twitter-email/cog_svcs_account.png)

| フィールド               | 値の例 | コメント |
| ----------------- | ------------ | ------------- |
| アカウント名 | MyCognitiveServicesAccnt | 一意の名前を入力します。 |
| API の種類 | Text Analytics API | テキスト分析を選択します。 |
| 場所 | 米国西部 | 現在使用できるのは **[米国西部]** のみです。 |
| [価格レベル] | F0 | 呼び出し回数が不足する場合は、高いレベルに設定します。|
| リソース グループ | rg1 | 前に指定したリソース グループを使用します。|

### <a name="copy-the-cognitive-services-key"></a>Cognitive Services キーをコピーする

**[キー]** を選択します。 後の手順でキーが必要です。

 ![構成する](media/functions-twitter-email/keys.png)

## <a name="create-a-logic-app"></a>ロジック アプリを作成します

Azure Portal で、**[新規]、[Enterprise Integration]、[Logic App]** の順にクリックします。

![新しいロジック アプリの手順の前の手順](media/functions-twitter-email/new_logicApp.png)

**[ロジック アプリの作成]** ブレードで、各フィールドに入力し、**[作成]** を選択します。

![ロジック アプリの作成手順の前の手順](media/functions-twitter-email/new_logicApp2.png)

ロジック アプリを作成すると、デザイナーに表示されます。 **[空のロジック アプリ]** テンプレートを選択します。

![空のロジック アプリ](media/functions-twitter-email/blank.png)

## <a name="add-a-trigger-to-twitter"></a>twitter にトリガーを追加する

**[ロジック アプリ デザイナー]** には多くのサービスや接続できるトリガーが表示されます。

**[Twitter]** サービスを選択します。

![twitter コネクタ](media/functions-twitter-email/twitter_connector.png)

トリガー **[新しいツイートが投稿されたら]** を選択します。

![[新しいツイートが投稿されたら] トリガー](media/functions-twitter-email/tw_trig.png)

twitter アカウントにサインインします。

![twitter アカウントにサインインする](media/functions-twitter-email/signin_twit.png)

パスワードを入力し、**[連携アプリを認証]** を選択します。

![上記の新しいウィンドウでの twitter の認証](media/functions-twitter-email/auth_twit.png)

検索テキスト、頻度、および間隔を入力します。 人気のあるハッシュタグ (#football、#soccer、#futbol など) を指定する場合、Cognitive Services アカウントで割り当てられているサービスの呼び出しをすべて簡単に使用できます。 呼び出し回数が不足する場合は、価格レベルを高くすることができます。 

#Azure を 15 分ごとに検索します。

![#Azure 15 分ごと](media/functions-twitter-email/azure_tweet.png)

アプリを保存します。

### <a name="add-a-text-analytics-connector"></a>**テキスト分析**コネクタを追加する

テキスト分析コネクタは、ツイートのセンチメントを検出します。

**[新しいステップ]**、**[アクションの追加]** の順に選択します。

![[新しいステップ]、[アクションの追加]](media/functions-twitter-email/new_step.png)

**テキスト分析**コネクタを追加します。

![アクション ウィンドウを選択する](media/functions-twitter-email/choose_action.png)

**[Detect Sentiment (センチメントの検出)]** アクションを選択します。 センチメントの評価は正確な場合が多いですが、テキストの解釈が誤っていることがあります。

![センチメントを検出する](media/functions-twitter-email/detect_sent.png)

### <a name="create-the-detect-sentiment-action"></a>センチメントの検出アクションを作成する

  * **MyKey** などの接続名を入力します。
  * [Cognitive Services アカウントを作成する](#cognitive-services)手順で作成したキーをコピーして貼り付けます。
  * **[作成]**を選択します。
  * アプリを保存します。

![センチメントを検出する](media/functions-twitter-email/ta_detect_sent.png)

**分析対象のテキスト**として **[ツイート テキスト]** アイコンを選択します。

![センチメントを検出する](media/functions-twitter-email/ds_tta.png)

![センチメントを検出する](media/functions-twitter-email/ds_tta2.png)

アプリを保存します。

## <a name="connect-to-the-azure-function"></a>Azure 関数に接続する

このセクションでは、上記で作成した、ツイートのセンチメントを赤、黄、または緑に分類する関数を追加します。

* Logic Apps デザイナーで、**[新しいステップ]**、**[アクションの追加]** の順に選択します。
* **[Azure Functions]** を選択します。
* **[Azure 関数を選択する]** を選択します。

![[Azure 関数を選択する] を示す Azure 関数ボックス](media/functions-twitter-email/choose_fun.png)

* 以前に作成した Azure 関数を選択します。
* **[スコア]** を選択して **[要求本文]** を設定します。

![Score](media/functions-twitter-email/trigger_score.png)

アプリを保存します。

## <a name="add-email-notification"></a>電子メール通知を追加する

このセクションでは、否定的なセンチメント ツイート (赤色の条件) の条件付きチェックを追加します。

* **[新しいステップ]**を選択します。
* **[条件の追加]**を選択します。
* 最初の **[値の選択]** テキスト ボックスで **[本文]** を選択します。
* 2 番目の **[値の選択]** テキスト ボックスに「RED」を入力します。
* アプリを保存します。

![条件ボックス](media/functions-twitter-email/condition.png)

* **[IF YES, DO NOTHING ("はい" の場合は何もしない)]** ボックスで、**[アクションの追加]** を選択します。
* **[すべてのサービスとアクションを検索する]** ボックスに Outlook や Gmail を入力します。 このチュートリアルでは Outlook を使用します。 Gmail の操作については、[Gmail の追加アクション](../logic-apps/logic-apps-create-a-logic-app.md#add-an-action-that-responds-to-your-trigger) を参照してください。 注: 個人用の [Microsoft アカウント](https://account.microsoft.com/account)を取得している場合は、Outlook.com アカウント用に使用することができます。

![アクション ボックスを選択する](media/functions-twitter-email/outlook.png)

**[Outlook.com Send an email (Outlook.com 電子メールの送信)]** アクションを選択します。

![Outlook.com ボックス](media/functions-twitter-email/sendEmail.png)

Outlook.com にサインインします。

![サインイン ボックス](media/functions-twitter-email/signin_outlook.png)

次の項目を入力します。

   * **[To (宛先)]**: メッセージの送信先となる電子メール。
   * **[件名]**: スコア。
   * **本文**: 場所とツイートのテキスト。

![[Send an email (電子メールの送信)] ボックス](media/functions-twitter-email/sendEmail2.png)

アプリを保存します。
**[実行]** を選択してアプリを開始します。

### <a name="check-the-status"></a>状態をチェックする

[ロジック アプリ] ブレードで、**[概要]** を選択し、**[すべての実行]** 列内の行をクリックします。

![[概要] ブレード](media/functions-twitter-email/over1.png)

次の画像は、条件が true でなかった場合に電子メールが送信されなかった実行の詳細を示しています。

![[概要] ブレード](media/functions-twitter-email/skipped.png)

**電子メールの送信**関数をすぐにテストする場合は、次の手順に従ってください。

* 最初の手順 (**[新しいツイートが投稿されたら]**) の**入力**を変更して、#football、#soccer、#futbol などの一般的な用語を設定します。

あまり一般的でない用語よりも一般的な用語を処理する方がリソースを消費します。 電子メールの動作を確認した後に、検索用語を変更することができます。

次の画像は、条件が true だった場合に電子メールが送信された実行の詳細を示しています。

![[概要] ブレード](media/functions-twitter-email/sent.png)

任意のサービス ボックスを選択して、実行に使用されるデータの詳細情報を表示することができます。 **[新しいツイートが投稿されたら]** を選択すると、出力を使用しない場合でも、検索テキストとすべての出力が表示されます。

## <a name="next-steps"></a>次のステップ

*  [Azure Functions の概要](functions-overview.md)
*  [Azure Logic Apps](../logic-apps/logic-apps-what-are-logic-apps.md)
*  [条件を追加してワークフローを実行する](../logic-apps/logic-apps-use-logic-app-features.md)
*  [ロジック アプリ テンプレート](../logic-apps/logic-apps-use-logic-app-templates.md)
*  [Azure Resource Manager テンプレートを使用したロジック アプリの作成](../logic-apps/logic-apps-arm-provision.md)

## <a name="get-help"></a>問い合わせ

[Azure Logic Apps フォーラム](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)では、質問の投稿や質問への回答を行うことができるほか、他の Azure Logic Apps ユーザーがどのようなことを行っているかがわかります。

[Azure Logic Apps ユーザー フィードバック サイト](http://aka.ms/logicapps-wish)でアイデアへの投票やアイデアの投稿を行って、Azure Logic Apps とコネクタの改善にご協力ください。

