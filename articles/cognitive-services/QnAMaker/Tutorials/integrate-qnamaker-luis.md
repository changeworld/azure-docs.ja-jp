---
title: QnA Maker と LUIS の統合 - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: QnA Maker と LUIS の統合に関するステップ バイ ステップ チュートリアル
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 04/21/2018
ms.author: saneppal
ms.openlocfilehash: 0a0eeb3815b793ed81f60b2b239bc459e5574788
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2018
ms.locfileid: "35376376"
---
# <a name="integrate-qna-maker-and-luis-to-distribute-your-knowledge-base"></a>QnA Maker と LUIS の統合によるナレッジ ベースの配信
QnA Maker ナレッジ ベースは、大きくなるにつれて、単一のモノリシックなセットとして維持することが難しくなり、より小さな論理的なチャンクにナレッジ ベースを分割する必要があります。

QnA Maker には複数のナレッジ ベースを簡単に作成できますが、入力された質問を適切なナレッジ ベースにルーティングするためには、何らかのロジックが必要となります。 これは LUIS を使用して実行できます。

## <a name="architecture"></a>アーキテクチャ

![QnA Maker LUIS アーキテクチャ](../media/qnamaker-tutorials-qna-luis/qnamaker-luis-architecture.PNG)

上記のシナリオでは、QnA Maker はまず、入力された質問の意図を LUIS モデルから取得し、それを使って正しい QnA Maker ナレッジ ベースにルーティングします。

## <a name="prerequisites"></a>前提条件
- [LUIS](https://www.luis.ai/) ポータルにログインして[アプリを作成](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/create-new-app)します。
- 実際のシナリオに従って[意図を追加](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/add-intents)します。
- LUIS アプリを[トレーニング](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/luis-how-to-train)して[発行](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/publishapp)します。
- [QnA Maker](https://qnamaker.ai) にログインし、実際のシナリオに応じて[ナレッジ ベースを作成]()します。
- ナレッジ ベースを[テスト]()して[発行]()します。

## <a name="qna-maker--luis-bot"></a>QnA Maker + LUIS ボット
1. まず、LUIS テンプレートを使用して Web アプリ ボットを作成し、上で作成した LUIS アプリとリンクさせ、意図を編集します。 [こちら](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/luis-csharp-tutorial-build-bot-framework-sample)で詳細な手順を参照してください。

2. 必要な依存関係をファイルの先頭に追加します。

    ```
    using RestSharp;
    using System.Collections.Generic;
    using Newtonsoft.Json;
    ```
3. QnA Maker サービスを呼び出すためのクラス (下記) を追加します。

    ```
        /// <summary>
        /// QnAMakerService is a wrapper over the QnA Maker REST endpoint
        /// </summary>
        [Serializable]
        public class QnAMakerService
        {
            private string qnaServiceHostName;
            private string knowledgeBaseId;
            private string endpointKey;
    
            /// <summary>
            /// Initialize a particular endpoint with it's details
            /// </summary>
            /// <param name="hostName">Hostname of the endpoint</param>
            /// <param name="kbId">Knowledge base ID</param>
            /// <param name="ek">Endpoint Key</param>
            public QnAMakerService(string hostName, string kbId, string ek)
            {
                qnaServiceHostName = hostName;
                knowledgeBaseId = kbId;
                endpointKey = ek;
            }
    
            /// <summary>
            /// Call the QnA Maker endpoint and get a response
            /// </summary>
            /// <param name="query">User question</param>
            /// <returns></returns>
            public string GetAnswer(string query)
            {
                var client = new RestClient( qnaServiceHostName + "/qnamaker/knowledgebases/" + knowledgeBaseId + "/generateAnswer");
                var request = new RestRequest(Method.POST);
                request.AddHeader("authorization", "EndpointKey " + endpointKey);
                request.AddHeader("content-type", "application/json");
                request.AddParameter("application/json", "{\"question\": \"" + query + "\"}", ParameterType.RequestBody);
                IRestResponse response = client.Execute(request);
    
                // Deserialize the response JSON
                QnAAnswer answer = JsonConvert.DeserializeObject<QnAAnswer>(response.Content);
    
                // Return the answer if present
                if (answer.answers.Count > 0)
                    return answer.answers[0].answer;
                else
                    return "No good match found.";
            }
        }
    
        /* START - QnA Maker Response Class */
        public class Metadata
        {
            public string name { get; set; }
            public string value { get; set; }
        }
    
        public class Answer
        {
            public IList<string> questions { get; set; }
            public string answer { get; set; }
            public double score { get; set; }
            public int id { get; set; }
            public string source { get; set; }
            public IList<object> keywords { get; set; }
            public IList<Metadata> metadata { get; set; }
        }
    
        public class QnAAnswer
        {
            public IList<Answer> answers { get; set; }
        }
        /* END - QnA Maker Response Class */
    ```

3. https://qnamaker.ai にアクセスし、[My knowledge bases]\(マイ ナレッジ ベース\)、[View code]\(コードの表示\) の順に選択して、対応するナレッジ ベースのコードを表示します。 次の情報を確認してください。

    ![QnA Maker の HTTP 要求](../media/qnamaker-tutorials-qna-luis/qnamaker-http-request.png)

4. 各ナレッジ ベースについて、QnAMakerService クラスをインスタンス化します。
    ```
            // Instantiate the knowledge bases
            public QnAMakerService hrQnAService = new QnAMakerService("https://hrkb.azurewebsites.net", "<HR knowledge base id>", "<HR endpoint key>");
            public QnAMakerService payrollQnAService = new QnAMakerService("https://payrollkb.azurewebsites.net", "<Payroll knowledge base id>", "<Payroll endpoint key>");
            public QnAMakerService financeQnAService = new QnAMakerService("https://financekb.azurewebsites.net", "<Finance knowledge base id>", "<Finance endpoint key>");
    ```

5. 意図に対応するナレッジ ベースを呼び出します。
    ```
            // HR Intent
            [LuisIntent("HR")]
            public async Task CancelIntent(IDialogContext context, LuisResult result)
            {
                // Ask the HR knowledge base
                await context.PostAsync(hrQnAService.GetAnswer(result.Query));
            }
    
            // Payroll intent
            [LuisIntent("Payroll")]
            public async Task GreetingIntent(IDialogContext context, LuisResult result)
            {
                // Ask the payroll knowledge base
                await context.PostAsync(payrollQnAService.GetAnswer(result.Query));
            }
    
            // Finance intent
            [LuisIntent("Finance")]
            public async Task HelpIntent(IDialogContext context, LuisResult result)
            {
                // Ask the finance knowledge base
                await context.PostAsync(financeQnAService.GetAnswer(result.Query));
            }
    ```

## <a name="build-the-bot"></a>ボットをビルドする
1. コード エディターで `build.cmd` を右クリックし、**[Run from Console]\(コンソールから実行\)** を選択します。

    ![コンソールから実行](../media/qnamaker-tutorials-qna-luis/run-from-console.png)

2. コード ビューがターミナル ウィンドウに置き換わり、ビルドの進行状況と結果が表示されます。

    ![コンソール ビルド](../media/qnamaker-tutorials-qna-luis/console-build.png)

## <a name="test-the-bot"></a>ボットをテストする
Azure portal で、**[Test in Web Chat]\(Web チャットでのテスト\)** を選択してボットをテストします。 異なる意図のメッセージを入力して、対応するナレッジ ベースからの応答を得ます。

![Web チャット テスト](../media/qnamaker-tutorials-qna-luis/qnamaker-web-chat.png)

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [QnA Maker のビジネス継続性計画の作成](../How-To/business-continuity-plan.md)
