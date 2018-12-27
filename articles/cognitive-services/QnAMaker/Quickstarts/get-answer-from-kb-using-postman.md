---
title: クイック スタート:Postman を使用押してナレッジ ベースから回答を取得する - QnA Maker
titlesuffix: Azure Cognitive Services
description: このクイック スタートでは、Postman を使用して、ナレッジ ベースから回答を取得する手順を紹介しています。
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: quickstart
ms.date: 12/11/2018
ms.author: diberry
ms.openlocfilehash: 476e982bdddd41c1daf06c3a673d964ce2a85f98
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/12/2018
ms.locfileid: "53270894"
---
# <a name="quickstart-get-an-answer-from-knowledge-base-using-postman"></a>クイック スタート:Postman を使用してナレッジ ベースから回答を取得する

この Postman ベースのクイック スタートでは、ナレッジ ベースから回答を取得する手順を紹介しています。

## <a name="prerequisites"></a>前提条件

* 最新の [**Postman**](https://www.getpostman.com/)。
* [QnA Maker サービス](../How-To/set-up-qnamaker-service-azure.md)と、[質問と回答が含まれるナレッジ ベース](../Tutorials/create-publish-query-in-portal.md)が必要です。 

## <a name="publish-to-get-endpoint"></a>発行してエンドポイントを取得する

ナレッジ ベースからの質問に対する回答を生成する準備ができたら、ナレッジ ベースを[発行](../How-to/publish-knowledge-base.md)します。

## <a name="use-production-endpoint-with-postman"></a>Postman を使って運用エンドポイントを使用する

ナレッジ ベースが発行されると、**[発行]** ページに、回答を生成するための HTTP 要求の設定が表示されます。 既定のビューに、[Postman](https://www.getpostman.com) からの回答の生成に必要な設定が表示されます。

[![結果を発行する](../media/qnamaker-quickstart-get-answer-with-postman/publish-settings.png)](../media/qnamaker-quickstart-get-answer-with-postman/publish-settings.png#lightbox)

Postman を使って回答を生成するには、次の手順を実行します。

1. Postman を開きます。 
1. 構成用を選択して、基本的な要求を作成します。
1. **要求名**として `Generate QnA Maker answer` を、**コレクション**として `Generate QnA Maker answers` を設定します。
1. ワークスペースで、**POST** の HTTP メソッドを選択します。
1. URL として、HOST と Post の値を連結して、完全な URL を作成します。 

    [![Postman で、Post に対するメソッドと、完全な URL を設定します](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-method-and-url.png)](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-method-and-url.png#lightbox)

1. **[ヘッダー]** タブの URL で、**[一括編集]** を選択します。 
1. テキスト領域にヘッダーをコピーします。

    [![Postman で、ヘッダーを設定します。](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-headers.png)](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-headers.png#lightbox)

1. **[本文]** タブを選択します。
1. **未加工**の形式を選択し、質問を表す JSON を入力します。

    [![Postman で、本文の JSON 値を設定します。](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-body-json-value.png)](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-body-json-value.png#lightbox)

1. **[送信]** ボタンを選択します。
1. 応答には、回答と、クライアント アプリケーションにとって重要である可能性のある他の情報が含まれています。 

    [![Postman で、本文の JSON 値を設定します。](../media/qnamaker-quickstart-get-answer-with-postman/receive-postman-response.png)](../media/qnamaker-quickstart-get-answer-with-postman/receive-postman-response.png#lightbox)

## <a name="use-staging-endpoint-with-curl"></a>cURL を使ってステージング エンドポイントを使用する

ステージングのエンドポイントから回答を取得するには、値 `true` を持つクエリ文字列ブール型パラメーター `isTest` を使用します。

`isTest=true`

## <a name="next-steps"></a>次の手順

[発行] ページで、cURL を使用して[回答を生成](get-answer-from-kb-using-curl.md)するための情報も指定されます。 

> [!div class="nextstepaction"]
> [回答の生成中にメタデータを使用する](../How-to/metadata-generateanswer-usage.md)