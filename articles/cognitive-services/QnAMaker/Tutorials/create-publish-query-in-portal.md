---
title: QnA Maker での作成、発行、回答
titleSuffix: Azure Cognitive Services
description: このポータルベースのチュートリアルでは、プログラムでナレッジ ベースを作成して発行した後、そのナレッジ ベースから質問に回答する手順について説明します。
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.technology: qna-maker
ms.topic: tutorial
ms.date: 10/29/2018
ms.author: diberry
ms.openlocfilehash: 9853e84ffd2de8a1dfd6d9c4f32f58bb36b16403
ms.sourcegitcommit: efcd039e5e3de3149c9de7296c57566e0f88b106
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2018
ms.locfileid: "53164434"
---
# <a name="tutorial-create-a-knowledge-base-then-answer-question-via-the-qna-maker-portal"></a>チュートリアル: QnA Maker ポータルでナレッジ ベースを作成して質問に回答する

このチュートリアルでは、ナレッジ ベースを作成して発行した後、そのナレッジ ベースから質問に回答する手順について説明します。

このチュートリアルでは、以下の内容を学習します。 

> [!div class="checklist"]
* QnA Maker ポータルでナレッジ ベースを作成する
* ナレッジ ベースを確認、保存、トレーニングする
* ナレッジ ベースの公開
* Curl を使用してナレッジ ベースを照会する

> [!NOTE] 
> このチュートリアルのプログラムによるバージョンは、[**Azure-Samples/cognitive-services-qnamaker-csharp** GitHub リポジトリ](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/tree/master/documentation-samples/tutorials/create-publish-answer-knowledge-base)にある完全なソリューションで提供されています。

## <a name="prerequisites"></a>前提条件

このチュートリアルでは、既存の [QnA Maker サービス](../How-To/set-up-qnamaker-service-azure.md)が必要です。 

## <a name="create-a-knowledge-base"></a>ナレッジ ベースの作成 

1. [QnA Maker](https://www.qnamaker.ai) ポータルにサインインします。 

1. 上部のメニューで **[Create a knowledge base]\(ナレッジ ベースの作成\)** を選択します。

    ![ナレッジ ベースの作成プロセスの手順 1](../media/qnamaker-tutorial-create-publish-query-in-portal/create-kb-step-1.png)

1. 既存の QnA Maker サービスを使用するため、最初の手順をスキップします。 

1. 次の手順では、既存の設定を選択します。  

    |Setting|目的|
    |--|--|
    |Microsoft Azure Directory ID|実際の _Microsoft Azure Directory ID_ は、Azure portal と QnA Maker ポータルへのサインインに使用するアカウントに関連付けられています。 |
    |Azure Subscription name (Azure サブスクリプション名)|QnA Maker のリソースを作成した請求先アカウント。|
    |Azure QnA Service (Azure QnA サービス)|既存の QnA Maker リソース。|

    ![ナレッジ ベースの作成プロセスの手順 2](../media/qnamaker-tutorial-create-publish-query-in-portal/create-kb-step-2.png)

1. 次の手順で、ナレッジ ベース名「`My Tutorial kb`」を入力します。

    ![ナレッジ ベースの作成プロセスの手順 3](../media/qnamaker-tutorial-create-publish-query-in-portal/create-kb-step-3.png)

1. 次の手順で、ナレッジ ベースに次の設定を指定します。  

    |設定名|設定値|目的|
    |--|--|--|
    |URL|`https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs` |その URL にある FAQ のコンテンツは、質問の後に回答が続く形式になっています。 QnA Maker は、この形式を解釈して、質問とそれに関連付けられた回答を抽出することができます。|
    |ファイル |"_このチュートリアルでは使用しません_"|これにより、質問と回答に関するファイルがアップロードされます。 |
    |[Chit-chat]\(おしゃべり\) の性格|The friend (フレンド)|これにより、一般的な質問と回答には親しみやすくカジュアルな性格が指定されます。 これらの質問と回答は後で編集することができます。 |

    ![ナレッジ ベースの作成プロセスの手順 4](../media/qnamaker-tutorial-create-publish-query-in-portal/create-kb-step-4.png)

1. **[Create your KB]\(\KB の作成)** を選択して作成プロセスを終了します。

    ![ナレッジ ベースの作成プロセスの手順 5](../media/qnamaker-tutorial-create-publish-query-in-portal/create-kb-step-5.png)

## <a name="review-kb-save-and-train"></a>KB を確認し、保存、トレーニングする

1. 質問と回答を確認します。 最初のページは、この URL にある質問と回答です。 

    ![保存してトレーニング](../media/qnamaker-tutorial-create-publish-query-in-portal/save-and-train-kb.png)

1. 表の下部で、質問と回答の最後のページを選択します。 このページには、おしゃべりの性格からの質問と回答が表示されます。 

1. 質問と回答の一覧の上にあるツール バーで、歯車を選択します。 これにより、質問と回答ごとにフィルターが表示されます。 おしゃべりの質問には、**editorial: chit-chat** フィルターが既に設定されています。 このフィルターは、選択した回答と共にクライアント アプリケーションに返されます。 チャット ボットなどのクライアント アプリケーションでは、このフィルターを使用して、追加の処理やユーザーとの対話を判断することができます。

    ![フィルターの表示](../media/qnamaker-tutorial-create-publish-query-in-portal/save-and-train-kb-chit-chat.png)

1. 上部のメニュー バーにある **[Save and train]\(保存してトレーニング\)** を選択します。

## <a name="publish-to-get-kb-endpoints"></a>発行して KB のエンドポイントを取得する

上部のメニューで **[Publish]\(発行\)** ボタンを選択します。 発行ページが表示されたら、**[キャンセル]** ボタンの横にある **[Publish]\(発行\)** を選択します。

![[発行]](../media/qnamaker-tutorial-create-publish-query-in-portal/publish-1.png)

KB が発行されると、エンドポイントが表示されます。

![[発行] ページのエンドポイントの設定](../media/qnamaker-tutorial-create-publish-query-in-portal/publish-2.png)

## <a name="use-curl-to-query-for-an-faq-answer"></a>curl を使用して FAQ の回答を照会する

1. **[Curl]** タブを選択します。 

    ![Curl コマンド](../media/qnamaker-tutorial-create-publish-query-in-portal/publish-3-curl.png)

1. **[Curl]** タブのテキストをコピーし、Curl 対応のターミナルまたはコマンド ラインで実行します。 Authorization ヘッダーの値には、末尾にスペースが追加されたテキスト「`Endpoint `」の後にキーが含まれています。

1. `<Your question>` を `How large can my KB be?` で置き換え これは、`How large a knowledge base can I create?` という質問に近いですが、まったく同じではありません。 QnA Maker によって自然言語処理が適用され、2 つの質問は同じであると判断されます。     

1. CURL コマンドを実行し、スコアと回答を含む JSON 応答を受け取ります。 

    ```TXT
      % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                     Dload  Upload   Total   Spent    Left  Speed
    100   581  100   543  100    38    418     29  0:00:01  0:00:01 --:--:--   447{
      "answers": [
        {
          "questions": [
            "How large a knowledge base can I create?"
          ],
          "answer": "The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment)for more details.",
          "score": 42.81,
          "id": 2,
          "source": "https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs",
          "metadata": []
        }
      ]
    }
    
    ```

    QnA Maker は、42.81% というスコアによりある程度信頼できます。  

## <a name="use-curl-to-query-for-a-chit-chat-answer"></a>curl を使用しておしゃべりの回答を照会する

1. Curl 対応のターミナルで、`How large can my KB be?` を、ユーザーからのボットの会話を終了する文言 (`Thank you` など) に置き換えます。   

1. CURL コマンドを実行し、スコアと回答を含む JSON 応答を受け取ります。 

    ```TXT
      % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                     Dload  Upload   Total   Spent    Left  Speed
    100   525  100   501  100    24    525     25 --:--:-- --:--:-- --:--:--   550{
      "answers": [
        {
          "questions": [
            "Thank you",
            "Thanks",
            "Thnx",
            "Kthx",
            "I appreciate it",
            "Thank you so much",
            "I thank you",
            "My sincere thank"
          ],
          "answer": "You're very welcome.",
          "score": 100.0,
          "id": 109,
          "source": "qna_chitchat_the_friend.tsv",
          "metadata": [
            {
              "name": "editorial",
              "value": "chitchat"
            }
          ]
        }
      ]
    }
   
    ```

    `Thank you` という質問はおしゃべりの質問に完全に一致したため、QnA Maker は 100 というスコアにより完全に信頼できます。 また、QnA Maker からは、関連するすべての質問のほか、おしゃべりのフィルター情報を含むメタデータも返されました。  

## <a name="use-curl-to-query-for-the-default-answer"></a>curl を使用して既定の回答を照会する

QnA Maker が回答に確証がない質問では、既定の回答を受け取ります。 この回答は、Azure portal で構成されています。 

1. Curl 対応のターミナルで、`Thank you` を `x` に置き換えます。 

1. CURL コマンドを実行し、スコアと回答を含む JSON 応答を受け取ります。 

    ```TXT
      % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                     Dload  Upload   Total   Spent    Left  Speed
    100   186  100   170  100    16    272     25 --:--:-- --:--:-- --:--:--   297{
      "answers": [
        {
          "questions": [],
          "answer": "No good match found in KB.",
          "score": 0.0,
          "id": -1,
          "metadata": []
        }
      ]
    }
    ```
    
    QnA Maker からは、信頼できないことを意味するスコア 0 が返されましたが、既定の回答も返されました。 

## <a name="next-steps"></a>次の手順

サポート ファイルの形式の詳細については、[サポートされるデータ ソース](../Concepts/data-sources-supported.md)に関するページを参照してください。 

おしゃべりの[性格](../Concepts/best-practices.md#chit-chat)の詳細を確認してください。

既定の回答の詳細については、「[一致が見つからない](../Concepts/confidence-score.md#no-match-found)」を参照してください。 

> [!div class="nextstepaction"]
> [ナレッジ ベースの概念](../Concepts/knowledge-base.md)