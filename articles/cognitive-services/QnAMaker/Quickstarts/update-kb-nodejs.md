---
title: 'クイック スタート: ナレッジ ベースを更新する - REST、Node.js - QnA Maker'
titleSuffix: Azure Cognitive Services
description: このクイック スタートでは、既存の QnA Maker ナレッジ ベース (KB) をプログラムから更新する手順を紹介しています。  この JSON を使用して新しいデータ ソースを追加したり、データ ソースを変更したり、データ ソースを削除したりすることによって、KB を更新することができます。
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: quickstart
ms.date: 10/19/2018
ms.author: diberry
ms.openlocfilehash: 1b92589fe01f171b732c04057dc290f0f32cb31a
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/23/2018
ms.locfileid: "49647818"
---
# <a name="quickstart-update-a-knowledge-base-in-qna-maker-using-nodejs"></a>クイック スタート: Node.js を使用して QnA Maker のナレッジ ベースを更新する

このクイック スタートでは、既存の QnA Maker ナレッジ ベース (KB) をプログラムから更新する手順を紹介しています。  この JSON を使用して新しいデータ ソースを追加したり、データ ソースを変更したり、データ ソースを削除したりすることによって、KB を更新することができます。

この API には、QnA Maker ポータルでの編集後、**[Save and train]\(保存してトレーニング\)** ボタンを使用することと同等の働きがあります。

このクイック スタートで呼び出す QnA Maker API は次のとおりです。
* [更新](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da7600) - ナレッジ ベースのモデルは、API 要求の本文で送信される JSON で定義されます。 
* [取得操作の詳細](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/operations_getoperationdetails)

## <a name="prerequisites"></a>前提条件

* [Node.js 6 以降](https://nodejs.org/en/download/)
* [QnA Maker サービス](../How-To/set-up-qnamaker-service-azure.md)が必要です。 キーを取得するには、ダッシュボードで **[リソース管理]** の **[キー]** を選択します。 
* URL の kbid クエリ文字列パラメーターに含まれている QnA Maker ナレッジ ベース (KB) ID (下図)。

    ![QnA Maker ナレッジ ベース ID](../media/qnamaker-quickstart-kb/qna-maker-id.png)

まだナレッジ ベースがない場合は、[新しいナレッジ ベースの作成](create-new-kb-nodejs.md)に関するページを参照し、このクイック スタート用のサンプルを作成してください。

[!INCLUDE [Code is available in Azure-Samples Github repo](../../../../includes/cognitive-services-qnamaker-nodejs-repo-note.md)]

## <a name="create-a-knowledge-base-nodejs-file"></a>ナレッジ ベース Node.js ファイルを作成する

`update-knowledge-base.js` という名前でファイルを作成します。

## <a name="add-the-required-dependencies"></a>必要な依存関係を追加する

`update-knowledge-base.js` の先頭に次の行を追加して、プロジェクトに必要な依存関係を追加します。

[!code-nodejs[Add the dependencies](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/update-knowledge-base/update-knowledge-base.js?range=1-4 "Add the dependencies")]

## <a name="add-required-constants"></a>必要な定数を追加する
上記の必要な依存関係の後に、QnA Maker にアクセスするために必要な定数を追加します。 `subscriptionKey` 変数の値を自分の QnA Maker キーに置き換えます。 

[!code-nodejs[Add required constants](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/update-knowledge-base/update-knowledge-base.js?range=10-17 "Add required constants")]

## <a name="add-knowledge-base-id"></a>ナレッジ ベース ID を追加する

前の定数の後に、ナレッジ ベース ID を追加し、これをパスに追加します。

[!code-nodejs[Add knowledge base ID](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/update-knowledge-base/update-knowledge-base.js?range=19-23 "Add knowledge base ID")]

## <a name="add-the-kb-update-model-definition"></a>KB 更新モデル定義を追加する

定数に続けて、次の KB 更新定義を追加します。 更新定義には、次の 3 つのセクションがあります。

* add
* update
* 削除

各セクションは、API に対する 1 回の同じ要求の中で使用することができます。 

[!code-nodejs[Add the KB update definition](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/update-knowledge-base/update-knowledge-base.js?range=25-53 "Add the KB update definition")]


## <a name="add-supporting-functions"></a>補助的な関数を追加する

次に、以下の補助的な関数を追加します。

1. JSON を読みやすい形式で出力するために、次の関数を追加します。

   [!code-nodejs[Add supporting functions, step 1](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/update-knowledge-base/update-knowledge-base.js?range=55-58 "Add supporting functions, step 1")]

2. 作成操作の状態を取得する HTTP 応答を管理するために、次の関数を追加します。

   [!code-nodejs[Add supporting functions, step 2](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/update-knowledge-base/update-knowledge-base.js?range=60-82 "Add supporting functions, step 2")]

## <a name="add-patch-request-to-update-kb"></a>KB を更新するための PATCH 要求を追加する

次の関数を追加して、ナレッジ ベースを更新するための HTTP PATCH 要求を作成します。 `Ocp-Apim-Subscription-Key` は、認証に使用する QnA Maker サービス キーです。

[!code-nodejs[Add PATCH request to update KB](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/update-knowledge-base/update-knowledge-base.js?range=84-111 "Add PATCH request to update KB")]

この API 呼び出しは、操作 ID を含んだ JSON 応答を返します。 操作 ID は、操作が完了していない場合に状態を要求するために必要です。

```JSON
{
  "operationState": "NotStarted",
  "createdTimestamp": "2018-10-02T01:23:00Z",
  "lastActionTimestamp": "2018-10-02T01:23:00Z",
  "userId": "335c3841df0b42cdb00f53a49d51a89c",
  "operationId": "e7be3897-88ff-44e5-a06c-01df0e05b78c"
}
```

## <a name="add-get-request-to-determine-operation-status"></a>操作状態を確認するための GET 要求を追加する

操作の状態を確認します。
    
[!code-nodejs[Add GET request to determine operation status](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/update-knowledge-base/update-knowledge-base.js?range=113-137 "Add GET request to determine operation status")]

この API 呼び出しは、操作の状態を含んだ JSON 応答を返します。 

```JSON
{
  "operationState": "NotStarted",
  "createdTimestamp": "2018-09-26T05:22:53Z",
  "lastActionTimestamp": "2018-09-26T05:22:53Z",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "177e12ff-5d04-4b73-b594-8575f9787963"
}
```

成功または失敗が返されるまで、この呼び出しを繰り返します。 

```JSON
{
  "operationState": "Succeeded",
  "createdTimestamp": "2018-09-26T05:22:53Z",
  "lastActionTimestamp": "2018-09-26T05:23:08Z",
  "resourceLocation": "/knowledgebases/XXX7892b-10cf-47e2-a3ae-e40683adb714",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "177e12ff-5d04-4b73-b594-8575f9787963"
}
```

## <a name="add-updatekb-method"></a>update_kb メソッドを追加する

次のメソッドは、KB を更新して、状態のチェックを繰り返し実行するものです。 KB の作成には多少時間がかかる場合があるため、成功と失敗のどちらかの状態になるまで、呼び出しを繰り返して状態をチェックする必要があります。

[!code-nodejs[Add update_kb method](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/update-knowledge-base/update-knowledge-base.js?range=139-169 "Add update_kb method")]

## <a name="run-the-program"></a>プログラムの実行

コマンド ラインで次のコマンドを入力して、プログラムを実行します。 QnA Maker API に要求が送信され、KB が更新された後、30 秒おきに結果がポーリングされます。 それぞれの応答は、コンソール ウィンドウに出力されます。

```bash
node update-knowledge-base.js
```

更新されたナレッジ ベースは、QnA Maker ポータルの [[My knowledge bases]\(マイ ナレッジ ベース\)](https://www.qnamaker.ai/Home/MyServices) ページで確認できます。 

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [QnA Maker (V4) REST API リファレンス](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff)