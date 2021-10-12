---
title: Azure Logic Apps と統合される関数を作成する
description: Azure Logic Apps および Azure Cognitive Services と統合される関数を作成します。 結果のワークフローは、ツイートのセンチメントを分類し、メールの通知を送信します。
author: craigshoemaker
ms.assetid: 60495cc5-1638-4bf0-8174-52786d227734
ms.topic: tutorial
ms.date: 04/10/2021
ms.author: cshoe
ms.custom: devx-track-csharp, mvc, cc996988-fb4f-47
ms.openlocfilehash: 0ff31245f739085ac2c8c1381df93a52e837d3a1
ms.sourcegitcommit: 1f29603291b885dc2812ef45aed026fbf9dedba0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/29/2021
ms.locfileid: "129232111"
---
# <a name="tutorial-create-a-function-to-integrate-with-azure-logic-apps"></a>チュートリアル: Azure Logic Apps と統合される関数を作成する

Azure Functions は、Logic Apps デザイナーで Azure Logic Apps と統合できます。 この統合により、他の Azure サービスやサードパーティ製のサービスとのオーケストレーションにおいて Functions の処理能力を使用することができます。

このチュートリアルでは、Twitter アクティビティを分析するワークフローを作成する方法について説明します。 ツイートが評価された後、肯定的なセンチメントが検出されると、ワークフローによって通知が送信されます。

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * Cognitive Services API リソースを作成します。
> * ツイートの感情を分類する関数を作成します。
> * Twitter に接続するロジック アプリを作成します。
> * 感情の検出をロジック アプリに追加します。
> * ロジック アプリを関数に接続します。
> * 関数からの応答に基づいて電子メールを送信します。

## <a name="prerequisites"></a>前提条件

* アクティブな [Twitter](https://twitter.com/) アカウント。
* [Outlook.com](https://outlook.com/) のアカウント (通知の送信用)。

> [!NOTE]
> Gmail コネクタの使用を希望する場合、ロジック アプリで制限なしにこのコネクタを使用できるのは、G-Suite ビジネス アカウントだけです。 Gmail コンシューマー アカウントを持っている場合は、Google によって承認された特定のアプリおよびサービスのみで Gmail コネクタを使用できるほか、[認証に使用する Google クライアント アプリを Gmail コネクタで作成する](/connectors/gmail/#authentication-and-bring-your-own-application)ことができます。 <br><br>詳細については、「[Azure Logic Apps での Google コネクタのデータ セキュリティとプライバシー ポリシー](../connectors/connectors-google-data-security-privacy-policy.md)」を参照してください。

## <a name="create-text-analytics-resource"></a>Text Analytics リソースを作成する

Cognitive Services API は、個々のリソースとして Azure で使用できます。 Text Analytics API を使用して、投稿されたツイートのセンチメントを検出します。

1. [Azure portal](https://portal.azure.com/) にサインインします。

1. Azure Portal の左上隅にある **[リソースの作成]** を選択します。

1. _[カテゴリ]_ で **[AI + 機械学習]** を選択します

1. _[Text Analytics]_ で **[作成]** を選択します。

1. _[Create Text Analytics]\(Text Analytics の作成\)_ 画面で、次の値を入力します。

    | 設定 | 値 | 解説 |
    | ------- | ----- | ------- |
    | サブスクリプション | お使いの Azure サブスクリプション名 | |
    | Resource group | **tweet-sentiment-tutorial** という名前の新しいリソース グループを作成します | 後でこのリソース グループを削除すると、このチュートリアルで作成したすべてのリソースが削除されます。 |
    | リージョン | 最も近いリージョンを選択します | |
    | 名前 | **TweetSentimentApp** | |
    | Pricing tier | **Free F0** を選択します | |

1. **[Review + create]\(レビュー + 作成\)** を選択します。

1. **［作成］** を選択します

1. デプロイが完了したら、 **[リソースに移動]** を選択します。

## <a name="get-text-analytics-settings"></a>Text Analytics の設定を取得する

Text Analytics リソースを作成したら、いくつかの設定をコピーして、後で使用できるように保存しておきます。

1. **[Keys and Endpoint]\(キーとエンドポイント\)** を選択します。

1. 入力ボックスの端にあるアイコンをクリックして、 **[キー 1]** をコピーします。

1. その値をテキスト エディターに貼り付けます。

1. 入力ボックスの端にあるアイコンをクリックして、 **[エンドポイント]** をコピーします。

1. その値をテキスト エディターに貼り付けます。

## <a name="create-the-function-app"></a>Function App の作成

1. 上部の検索ボックスで、「**関数アプリ**」を検索して選択します。

1. **［作成］** を選択します

1. 次の値を入力します。

    | 設定 | 推奨値 | 解説 |
    | ------- | ----- | ------- |
    | サブスクリプション | お使いの Azure サブスクリプション名 | |
    | Resource group | **tweet-sentiment-tutorial** | このチュートリアル全体で、同じリソース グループ名を使用します。 |
    | 関数アプリ名 | **TweetSentimentAPI** + 一意のサフィックス | 関数アプリケーション名はグローバルに一意です。 有効な文字は、`a-z` (大文字と小文字の区別をしない)、`0-9`、および `-`です。 |
    | 発行 | **コード** | |
    | ランタイム スタック | **.NET** | 提供される関数コードは、C# で記述されています。 |
    | Version | 最新のバージョン番号を選択します | |
    | リージョン | 最も近いリージョンを選択します | |

1. **[Review + create]\(レビュー + 作成\)** を選択します。

1. **［作成］** を選択します

1. デプロイが完了したら、 **[リソースに移動]** を選択します。

## <a name="create-an-http-triggered-function"></a>HTTP によってトリガーされる関数を作成する  

1. _[関数]_ ウィンドウの左側のメニューで、 **[関数]** を選択します。

1. 上部のメニューで **[追加]** を選択し、次の値を入力します。

    | 設定 | 値 | 解説 |
    | ------- | ----- | ------- |
    | 開発環境 | **ポータルでの開発** | |
    | Template | **HTTP トリガー** | |
    | 新しい関数 | **TweetSentimentFunction** | これが関数の名前です。 |
    | 承認レベル | **Function** | |

1. **[追加]** ボタンを選びます。

1. **[Code + Test]\(コード + テスト\)** ボタンを選択します。

1. コード エディター ウィンドウに、次のコードを貼り付けます。

    ```csharp
    #r "Newtonsoft.Json"
    
    using System;
    using System.Net;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Extensions.Logging;
    using Microsoft.Extensions.Primitives;
    using Newtonsoft.Json;
    
    public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
    {
    
        string requestBody = String.Empty;
        using (StreamReader streamReader =  new  StreamReader(req.Body))
        {
            requestBody = await streamReader.ReadToEndAsync();
        }
    
        dynamic score = JsonConvert.DeserializeObject(requestBody);
        string value = "Positive";
    
        if(score < .3)
        {
            value = "Negative";
        }
        else if (score < .6) 
        {
            value = "Neutral";
        }
    
        return requestBody != null
            ? (ActionResult)new OkObjectResult(value)
           : new BadRequestObjectResult("Pass a sentiment score in the request body.");
    }
    ```

    センチメント スコアが関数に渡され、その値にカテゴリ名が返されます。

1. ツール バーの **[保存]** ボタンを選択して、変更を保存します。

    > [!NOTE]
    > この関数をテストするには、上部のメニューの **[テストと実行]** を選択します。 _[入力]_ タブで、 _[Body]\(本文\)_ 入力ボックスに値「`0.9`」を入力し、 **[実行]** を選択します。 _[出力]_ セクションの _[HTTP 応答のコンテンツ]_ ボックスに、 _[肯定的]_ という値が返されていることを確認します。

次に、Azure Functions、Twitter、および Cognitive Services API と統合されるロジック アプリを作成します。

## <a name="create-a-logic-app"></a>ロジック アプリを作成する

1. 上部の検索ボックスで、「**ロジック アプリ**」を検索して選択します。

1. **[追加]** を選択します。

1. **[従量課金プラン]** を選択し、次の値を入力します。

    | 設定 | 推奨値 |
    | ------- | --------------- |
    | サブスクリプション | お使いの Azure サブスクリプション名 |
    | Resource group | **tweet-sentiment-tutorial** |
    | ロジック アプリ名 | **TweetSentimentApp** |
    | リージョン | 最も近いリージョンを選択します。可能であれば、前の手順で選択したリージョンと同じリージョンを選択します。 |

    その他の設定はすべて、既定値のままにしておきます。

1. **[Review + create]\(レビュー + 作成\)** を選択します。

1. **［作成］** を選択します

1. デプロイが完了したら、 **[リソースに移動]** を選択します。

1. **[空のロジック アプリ]** ボタンを選択します。

    :::image type="content" source="media/functions-twitter-email/blank-logic-app-button.png" alt-text="[空のロジック アプリ] ボタン":::

1. ツール バーの **[保存]** ボタンを選択して、ここまでの作業を保存します。

これで、Logic Apps デザイナーを使用してサービスとトリガーをアプリケーションに追加できるようになりました。

## <a name="connect-to-twitter"></a>Twitter への接続

アプリが新しいツイートをポーリングできるように、Twitter への接続を作成します。

1. 上部の検索ボックスで、「**Twitter**」を検索します。

1. **[Twitter]** アイコンを選択します。

1. **[新しいツイートが投稿されたら]** トリガーを選択します。

1. 接続を設定するには、次の値を入力します。

    | 設定 |  値 |
    | ------- | ---------------- |
    | [接続名] | **MyTwitterConnection** |
    | 認証の種類 | **既定の共有アプリケーションを使用する** |

1. **[サインイン]** を選択します。

1. ポップアップ ウィンドウの指示に従って、Twitter へのサインインを完了します。

1. 次に、 _[新しいツイートが投稿されたら]_ ボックスで、次の値を入力します。

    | 設定 | 値 |
    | ------- | ----- |
    | 検索テキスト | **#my-twitter-tutorial** |
    | [項目を確認する頻度]:  | ボックスに「**1**」と入力し、 <br> ドロップダウンで **[Hour]\(時間\)** を選択します 異なる値を入力することもできますが、Twitter コネクタの現在の[制限事項](/connectors/twitterconnector/#limits)を確認してください。  |

1. ツール バーの **[保存]** ボタンを選択して、ここまでの作業を保存します。

次に、テキスト分析に接続し、収集されたツイートのセンチメントを検出します。

## <a name="add-text-analytics-sentiment-detection"></a>Text Analytics のセンチメント検出を追加する

1. **[新しいステップ]** を選択します。

1. 検索ボックスで、「**Text Analytics**」を検索します。

1. **[Text Analytics]** アイコンを選択します。

1. **[感情の検出]** を選択し、次の値を入力します。

    | 設定 | 値 |
    | ------- | ----- |
    | [接続名] | **TextAnalyticsConnection** |
    | アカウント キー | 前に保存しておいた Text Analytics アカウント キーを貼り付けます。 |
    | サイトの URL | 前に保存しておいた Text Analytics エンドポイントを貼り付けます。 |

1. **［作成］** を選択します

1. _[新しいパラメーターの追加]_ ボックス内をクリックし、ポップアップに表示される **ドキュメント** の横にあるチェック ボックスをオンにします。

1. _[documents Id - 1]\(ドキュメント ID - 1\)_ ボックス内をクリックして、動的なコンテンツのポップアップを開きます。

1. _[動的なコンテンツ]_ の検索ボックスで、「**ID**」を検索し、 **[ツイート ID]** をクリックします。

1. _[documents Text - 1]\(ドキュメント テキスト - 1\)_ ボックス内をクリックして、動的なコンテンツのポップアップを開きます。

1. _[動的なコンテンツ]_ の検索ボックスで、「**テキスト**」を検索し、 **[ツイート テキスト]** をクリックします。

1. **[アクションを選択してください]** で「**テキスト分析**」と入力し、 **[感情の検出]** アクションをクリックします。

1. ツール バーの **[保存]** ボタンを選択して、ここまでの作業を保存します。

_[感情の検出]_ ボックスは、次のスクリーンショットのようになります。

:::image type="content" source="media/functions-twitter-email/detect-sentiment.png" alt-text="[感情の検出] の設定":::

## <a name="connect-sentiment-output-to-function-endpoint"></a>センチメントの出力を関数エンドポイントに接続する

1. **[新しいステップ]** を選択します。

1. 検索ボックスで「**Azure Functions**」を検索します。

1. **[Azure Functions]** アイコンを選択します。

1. 検索ボックスで自分の関数の名前を検索します。 上記のガイダンスに従った場合、関数名は **TweetSentimentAPI** で始まります。

1. 関数アイコンを選択します。

1. **[TweetSentimentFunction]** 項目を選択します。

1. _[要求本文]_ ボックス内をクリックし、ポップアップ ウィンドウで _[感情の検出]_ の **[スコア]** 項目を選択します。

1. ツール バーの **[保存]** ボタンを選択して、ここまでの作業を保存します。

## <a name="add-conditional-step"></a>条件付きステップを追加する

1. **[アクションの追加]** ボタンを選択します。

1. _[制御]_ ボックス内をクリックし、ポップアップ ウィンドウで「**制御**」を検索して選択します。

1. **[条件]** を選択します。

1. _[値の選択]_ ボックス内をクリックし、ポップアップ ウィンドウで _[TweetSentimentFunction]_ の **[本文]** 項目を選択します。

1. _[値の選択]_ ボックスに「**肯定的**」と入力します。

1. ツール バーの **[保存]** ボタンを選択して、ここまでの作業を保存します。

## <a name="add-email-notifications"></a>電子メール通知を追加する

1. _[True]_ ボックスで、 **[アクションの追加]** ボタンを選択します。

1. テキスト ボックスで、「**Office 365 Outlook**」を検索して選択します。

1. テキスト ボックスで、「**送信**」を検索し、 **[電子メールの送信]** を選択します。

1. **[サインイン]** ボタンを選択します。

1. ポップアップ ウィンドウの指示に従って、Office 365 Outlook へのサインインを完了します。

1. _[宛先]_ ボックスにメール アドレスを入力します。

1. _[件名]_ ボックス内をクリックし、 **[TweetSentimentFunction]** の下の _[本文]_ 項目をクリックします。 _[本文]_ 項目が一覧に表示されない場合は、 **[さらに表示]** リンクをクリックして、オプションの一覧を展開してください。

1. _[件名]_ の _[本文]_ 項目の後に、「**ツイート投稿者:** 」というテキストを入力します。

1. 「_ツイート投稿者:_ 」というテキストの後に、ボックスをもう一度クリックし、 _[新しいツイートが投稿されたら]_ というオプションの一覧で **[ユーザー名]** を選択します。

1. _[本文]_ ボックス内をクリックし、 _[新しいツイートが投稿されたら]_ というオプションの一覧で **[ツイート テキスト]** を選択します。 _[ツイート テキスト]_ 項目が一覧に表示されない場合は、 **[もっと見る]** リンクをクリックして、オプションの一覧を展開してください。

1. ツール バーの **[保存]** ボタンを選択して、ここまでの作業を保存します。

これで、メール ボックスは、このスクリーンショットのようになります。

:::image type="content" source="media/functions-twitter-email/email-notification.png" alt-text="電子メール通知":::

## <a name="run-the-workflow"></a>ワークフローを実行する

1. Twitter アカウントで、**I'm enjoying #my-twitter-tutorial** というテキストをツイートします。

1. Logic Apps デザイナーに戻り、 **[実行]** ボタンを選択します。

1. メールで、このワークフローからのメッセージがないかどうかを確認します。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このチュートリアルで作成した Azure サービスとアカウントをすべてクリーンアップするには、リソース グループを削除します。

1. 上部の検索ボックスで「**リソース グループ**」を検索します。

1. **[tweet-sentiment-tutorial]** を選択します。

1. **[リソース グループの削除]** を選択します。

1. テキスト ボックスに「**tweet-sentiment-tutorial**」と入力します。

1. **[削除]** ボタンを選択します。

必要に応じて、Twitter アカウントに戻り、フィードからすべてのテスト ツイートを削除することもできます。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Azure Functions を使用してサーバーレス API を作成する](functions-create-serverless-api.md)
