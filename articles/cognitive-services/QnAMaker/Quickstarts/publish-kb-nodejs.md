---
title: 'クイック スタート: ナレッジ ベースを公開する - REST、Node.js - QnA Maker'
titleSuffix: Azure Cognitive Services
description: このクイック スタートでは、ナレッジ ベース (KB) をプログラムから公開する手順を紹介しています。 公開すると、ナレッジ ベースの最新バージョンが、専用の Azure Search インデックスにプッシュされ、アプリケーションやチャット ボットで呼び出すことができるエンドポイントが作成されます。
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: quickstart
ms.date: 10/02/2018
ms.author: diberry
ms.openlocfilehash: c70b90a6e465c72193f63afd7ab9106579e2c634
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2018
ms.locfileid: "48886612"
---
# <a name="quickstart-publish-a-qna-maker-knowledge-base-in-nodejs"></a>クイック スタート: QnA Maker ナレッジ ベースを Node.js で公開する

このクイック スタートでは、ナレッジ ベース (KB) をプログラムから公開する手順を紹介しています。 公開すると、ナレッジ ベースの最新バージョンが、専用の Azure Search インデックスにプッシュされ、アプリケーションやチャット ボットで呼び出すことができるエンドポイントが作成されます。

このクイック スタートで呼び出す QnA Maker API は次のとおりです。
* [公開](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75fe) - この API は、要求の本文に情報を必要としません。

## <a name="prerequisites"></a>前提条件

* [Node.js 6 以降](https://nodejs.org/en/download/)
* [QnA Maker サービスが必要](../How-To/set-up-qnamaker-service-azure.md)です。 キーを取得するには、ダッシュボードで **[リソース管理]** の **[キー]** を選択します。 
* URL の kbid クエリ文字列パラメーターに含まれている QnA Maker ナレッジ ベース (KB) ID (下図)。

    ![QnA Maker ナレッジ ベース ID](../media/qnamaker-quickstart-kb/qna-maker-id.png)

まだナレッジ ベースがない場合は、[新しいナレッジ ベースの作成](create-new-kb-nodejs.md)に関するページを参照し、このクイック スタート用のサンプルを作成してください。

[!INCLUDE [Code is available in Azure-Samples Github repo](../../../../includes/cognitive-services-qnamaker-nodejs-repo-note.md)]

## <a name="create-a-knowledge-base-nodejs-file"></a>ナレッジ ベース Node.js ファイルを作成する

`publish-knowledge-base.js` という名前でファイルを作成します。

## <a name="add-required-dependencies"></a>必要な依存関係を追加する

`publish-knowledge-base.js` の先頭に次の行を追加して、プロジェクトに必要な依存関係を追加します。

[!code-nodejs[Add the dependencies](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/publish-knowledge-base/publish-knowledge-base.js?range=1-4 "Add the dependencies")]

## <a name="add-required-constants"></a>必要な定数を追加する

上記の必要な依存関係の後に、QnA Maker にアクセスするために必要な定数を追加します。 `subscriptionKey` 変数の値を自分の QnA Maker キーに置き換えます。 

[!code-nodejs[Add required constants](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/publish-knowledge-base/publish-knowledge-base.js?range=10-17 "Add required constants")]

## <a name="add-knowledge-base-id"></a>ナレッジ ベース ID を追加する

前の定数の後に、ナレッジ ベース ID を追加し、これをパスに追加します。

[!code-nodejs[Add knowledge base ID](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/publish-knowledge-base/publish-knowledge-base.js?range=19-23 "Add knowledge base ID")]

## <a name="add-supporting-functions"></a>補助的な関数を追加する

次に、以下の補助的な関数を追加します。

1. JSON を読みやすい形式で出力するために、次の関数を追加します。

   [!code-nodejs[Add supporting functions, step 1](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/publish-knowledge-base/publish-knowledge-base.js?range=25-28 "Add supporting functions, step 1")]

2. 作成操作の状態を取得する HTTP 応答を管理するために、次の関数を追加します。

   [!code-nodejs[Add supporting functions, step 2](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/publish-knowledge-base/publish-knowledge-base.js?range=30-52 "Add supporting functions, step 2")]

## <a name="add-the-publishkb-function-and-main-function"></a>publish_kb 関数と main 関数を追加する

次のコードは、KB を公開するための HTTPS 要求を QnA Maker API に送信し、その応答を受信するものです。

[!code-nodejs[Add POST request to publish KB](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/publish-knowledge-base/publish-knowledge-base.js?range=54-71 "Add POST request to publish KB")]

[!code-nodejs[Add the publish_kb function](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/publish-knowledge-base/publish-knowledge-base.js?range=73-91 "Add the publish_kb function and main function")]

## <a name="add-the-main-function"></a>main 関数を追加する

要求と応答を管理するために、次の関数を追加します。

[!code-nodejs[Add the main function](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/publish-knowledge-base/publish-knowledge-base.js?range=94-97 "Add the main function")]

## <a name="run-the-program"></a>プログラムの実行

プログラムをビルドして実行します。 QnA Maker API に要求が自動的に送信され、KB が公開された後、応答がコンソール ウィンドウに出力されます。

ナレッジ ベースが公開された後は、クライアント アプリケーションまたはチャット ボットを使用して、エンドポイントからナレッジ ベースに対してクエリを実行できます。 

```bash
node publish-knowledge-base.js
```

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [QnA Maker (V4) REST API リファレンス](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff)