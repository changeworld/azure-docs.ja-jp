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
ms.openlocfilehash: 0b442c8cccf8ee9ed5194a7d3dfbfb4d7aa055a3
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/23/2019
ms.locfileid: "74424182"
---
この Postman ベースのクイック スタートでは、ナレッジ ベースから回答を取得する手順を紹介しています。

## <a name="prerequisites"></a>前提条件

* 最新の [**Postman**](https://www.getpostman.com/)。
* [QnA Maker サービス](../How-To/set-up-qnamaker-service-azure.md)と、[質問と回答が含まれるナレッジ ベース](../Tutorials/create-publish-query-in-portal.md)が必要です。 

## <a name="publish-to-get-endpoint"></a>発行してエンドポイントを取得する

ナレッジ ベースからの質問に対する回答を生成する準備ができたら、ナレッジ ベースを[発行](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base)します。

## <a name="use-production-endpoint-with-postman"></a>Postman を使って運用エンドポイントを使用する

ナレッジ ベースが発行されると、 **[発行]** ページに、回答を生成するための HTTP 要求の設定が表示されます。 既定のビューに、[Postman](https://www.getpostman.com) からの回答の生成に必要な設定が表示されます。

次の図の黄色の数字は、次の手順で使用する名前/値のペアを示しています。

[![結果を発行する](../media/qnamaker-quickstart-get-answer-with-postman/publish-settings.png)](../media/qnamaker-quickstart-get-answer-with-postman/publish-settings.png#lightbox)

Postman を使って回答を生成するには、次の手順を実行します。

1. Postman を開きます。 構成要素の選択を求めるメッセージが表示された場合は、 **[Basic Request]\(基本的な要求\)** 構成要素を選択します。 **要求名**として `Generate QnA Maker answer` を、**コレクション**として `Generate QnA Maker answers` を設定します。 コレクションに保存しない場合は、 **[キャンセル]** を選択します。
1. ワークスペースで、**POST** の HTTP メソッドを選択します。

    [![Postman 内で POST メソッドを設定する](../media/qnamaker-quickstart-get-answer-with-postman/postman-select-post-method.png)](../media/qnamaker-quickstart-get-answer-with-postman/postman-select-post-method.png#lightbox)

1. URL として、HOST (図の #2) と Post (図の #1) の値を連結して、完全な URL を作成します。 完全な URL 例は次のようになります。 

    `https://qnamaker-f0.azurewebsites.net/qnamaker/knowledgebases/e1115f8c-d01b-4698-a2ed-85b0dbf3348c/generateAnswer`

    [![Postman 内で完全な URL を設定する](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-method-and-url.png)](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-method-and-url.png#lightbox)

1. **[ヘッダー]** タブの URL で、 **[一括編集]** を選択します。 

1. ヘッダー (図の #3 と #4) をテキスト領域にコピーします。

    [![Postman で、ヘッダーを設定します。](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-headers.png)](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-headers.png#lightbox)

1. **[本文]** タブを選択します。
1. **未加工**の形式を選択し、質問を表す JSON (図の #5) を入力します。

    `{"question":"How do I programmatically update my Knowledge Base?"}`

    [![Postman で、本文の JSON 値を設定します。](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-body-json-value.png)](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-body-json-value.png#lightbox)

1. **[送信]** ボタンを選択します。
1. 応答には、回答と、クライアント アプリケーションにとって重要である可能性のある他の情報が含まれています。 

    [![Postman で、本文の JSON 値を設定します。](../media/qnamaker-quickstart-get-answer-with-postman/receive-postman-response.png)](../media/qnamaker-quickstart-get-answer-with-postman/receive-postman-response.png#lightbox)

## <a name="use-staging-endpoint"></a>ステージング エンドポイントの使用

ステージングのエンドポイントから回答を取得するには、`isTest` 本文プロパティに URL を追加します。
