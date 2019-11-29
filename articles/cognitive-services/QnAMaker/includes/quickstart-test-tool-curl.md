---
title: インクルード ファイル
description: インクルード ファイル
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: luis
ms.topic: include
ms.custom: include file
ms.date: 11/20/2019
ms.author: diberry
ms.openlocfilehash: 0677a361e853f778894b6a62a054636e3276b364
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/23/2019
ms.locfileid: "74424174"
---
この cURL ベースのクイック スタートでは、ナレッジ ベースから回答を取得する手順を紹介しています。

## <a name="prerequisites"></a>前提条件

* 最新の [**cURL**](https://curl.haxx.se/)。
* [QnA Maker サービス](../How-To/set-up-qnamaker-service-azure.md)と、[質問と回答が含まれるナレッジ ベース](../Tutorials/create-publish-query-in-portal.md)が必要です。

## <a name="publish-to-get-endpoint"></a>発行してエンドポイントを取得する

ナレッジ ベースからの質問に対する回答を生成する準備ができたら、ナレッジ ベースを[発行](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base)します。

## <a name="use-production-endpoint-with-curl"></a>cURL を使って運用エンドポイントを使用する

ナレッジ ベースが発行されると、 **[発行]** ページに、回答を生成するための HTTP 要求の設定が表示されます。 **[CURL]** タブに、[CURL](https://www.getpostman.com) コマンドライン ツールからの回答の生成に必要な設定が表示されます。

[![結果を発行する](../media/qnamaker-use-to-generate-answer/curl-command-on-publish-page.png)](../media/qnamaker-use-to-generate-answer/curl-command-on-publish-page.png#lightbox)

CURL を使って回答を生成するには、次の手順を実行します。

1. [CURL] タブのテキストをコピーします。 
1. コマンドラインまたはターミナルを開き、テキストを貼り付けます。
1. ナレッジ ベースに関係するように質問を編集します。 質問を囲んでいる包含 JSON を削除しないように注意してください。
1. コマンドを入力します。 
1. 応答には、回答に関する関連情報が含まれます。 

    ```bash
    > curl -X POST https://qnamaker-f0.azurewebsites.net/qnamaker/knowledgebases/1111f8c-d01b-4698-a2de-85b0dbf3358c/generateAnswer -H "Authorization: EndpointKey 111841fb-c208-4a72-9412-03b6f3e55ca1" -H "Content-type: application/json" -d "{'question':'How do I programmatically update my Knowledge Base?'}"
    {
      "answers": [
        {
          "questions": [
            "How do I programmatically update my Knowledge Base?"
          ],
          "answer": "You can use our REST APIs to manage your Knowledge Base. See here for details: https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update",
          "score": 100.0,
          "id": 18,
          "source": "Custom Editorial",
          "metadata": [
            {
              "name": "category",
              "value": "api"
            }
          ]
        }
      ]
    }
    ```

## <a name="use-staging-endpoint-with-curl"></a>cURL を使ってステージング エンドポイントを使用する

ステージングのエンドポイントから回答を取得するには、本文のプロパティ `isTest` を使用します。

```json
isTest:true
```


