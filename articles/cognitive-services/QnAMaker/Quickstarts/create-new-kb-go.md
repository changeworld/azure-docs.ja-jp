---
title: 'クイック スタート: ナレッジ ベースを作成する - REST (Go) - QnA Maker'
description: この Go REST ベースのクイック スタートでは、Cognitive Services API アカウントの Azure ダッシュボードに表示される QnA Maker ナレッジ ベースのサンプルをプログラムから作成する手順を紹介しています。
ms.service: cognitive-services
ms.subservice: qna-maker
ms.date: 12/16/2019
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27
ms.topic: how-to
ms.openlocfilehash: be314ad74fbb58709f5707e84b193f8ca69b47d9
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/06/2020
ms.locfileid: "91777693"
---
# <a name="quickstart-create-a-knowledge-base-in-qna-maker-using-go"></a>クイック スタート: Go を使用して QnA Maker でナレッジ ベースを作成する

このクイック スタートでは、QnA Maker ナレッジ ベースのサンプルをプログラムから作成する手順を紹介しています。 QnA Maker は、[データ ソース](../Concepts/knowledge-base.md)の FAQ などの半構造化コンテンツから質問とその回答を自動的に抽出します。 ナレッジ ベースのモデルは、API 要求の本文で送信される JSON で定義されます。

このクイック スタートで呼び出す QnA Maker API は次のとおりです。
* [KB の作成](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/create)
* [取得操作の詳細](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/operations/getdetails)

[リファレンス ドキュメント](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase) | [GO サンプル](https://github.com/Azure-Samples/cognitive-services-qnamaker-go/blob/master/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.go)

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>前提条件

* [Go 1.10.1](https://golang.org/dl/)
* [QnA Maker サービス](../How-To/set-up-qnamaker-service-azure.md)が必要です。 キーと (リソース名を含む) エンドポイントを取得するには、Azure portal で対象のリソースの **[クイックスタート]** を選択します。

## <a name="create-a-knowledge-base-go-file"></a>ナレッジ ベースの Go ファイルを作成する

`create-new-knowledge-base.go` という名前でファイルを作成します。

## <a name="add-the-required-dependencies"></a>必要な依存関係を追加する

`create-new-knowledge-base.go` の先頭に次の行を追加して、プロジェクトに必要な依存関係を追加します。

:::code language="go" source="~/cognitive-services-quickstart-code/go/QnAMaker/rest/create-kb.go" id="dependencies":::

## <a name="add-the-kb-model-definition"></a>KB モデル定義を追加する
定数の後に、次の KB モデル定義を追加します。 モデルは、定義の後に文字列に変換されます。

:::code language="go" source="~/cognitive-services-quickstart-code/go/QnAMaker/rest/create-kb.go" id="model":::

## <a name="add-supporting-structures-and-functions"></a>補助的な構造体と関数を追加する

次に、以下の補助的な関数を追加します。

1. HTTP 応答の構造体を追加します。

    :::code language="go" source="~/cognitive-services-quickstart-code/go/QnAMaker/rest/create-kb.go" id="response":::

1. QnA Maker API への POST を処理する次のメソッドを追加します。 このクイック スタートでは、POST を使用して KB の定義を QnA Maker に送信します。

    :::code language="go" source="~/cognitive-services-quickstart-code/go/QnAMaker/rest/create-kb.go" id="post":::

1. QnA Maker API への GET を処理する次のメソッドを追加します。 このクイック スタートでは、GET を使用して作成操作の状態を確認します。

    :::code language="go" source="~/cognitive-services-quickstart-code/go/QnAMaker/rest/create-kb.go" id="get":::

## <a name="add-function-to-create-kb"></a>KB を作成するための関数を追加する

次の関数を追加して、ナレッジ ベースを作成するための HTTP POST 要求を作成します。 _create という_ **操作 ID** は、POST 応答のヘッダー フィールド **Location** で返された後、GET 要求でルートの一部として使用されます。 `Ocp-Apim-Subscription-Key` は、認証に使用される QnA Maker サービス キーです。

:::code language="go" source="~/cognitive-services-quickstart-code/go/QnAMaker/rest/create-kb.go" id="create_kb":::

この API 呼び出しは、操作 ID を含んだ JSON 応答を返します。 この操作 ID を使用して、KB が正常に作成されたかどうかを判断します。

```JSON
{
  "operationState": "NotStarted",
  "createdTimestamp": "2018-09-26T05:19:01Z",
  "lastActionTimestamp": "2018-09-26T05:19:01Z",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "8dfb6a82-ae58-4bcb-95b7-d1239ae25681"
}
```

## <a name="add-function-to-get-status"></a>状態を取得するための関数を追加する

次の関数を追加して、操作の状態をチェックするための HTTP GET 要求を作成します。 `Ocp-Apim-Subscription-Key` は、認証に使用される QnA Maker サービス キーです。

:::code language="go" source="~/cognitive-services-quickstart-code/go/QnAMaker/rest/create-kb.go" id="get_status":::

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
## <a name="add-main-function"></a>main 関数を追加する

次の関数は、KB を作成して、状態のチェックを繰り返し実行する main 関数です。 KB の作成には多少時間がかかる場合があるため、成功と失敗のどちらかの状態になるまで、呼び出しを繰り返して状態をチェックする必要があります。

:::code language="go" source="~/cognitive-services-quickstart-code/go/QnAMaker/rest/create-kb.go" id="main":::


## <a name="compile-the-program"></a>プログラムをコンパイルする
以下のコマンドを入力してファイルをコンパイルします。 このコマンド プロンプトから、ビルドの成功に関する情報は一切返されません。

```bash
go build create-new-knowledge-base.go
```

## <a name="run-the-program"></a>プログラムを実行する

コマンド ラインで次のコマンドを入力して、プログラムを実行します。 QnA Maker API に要求が送信され、KB が作成された後、30 秒おきに結果がポーリングされます。 それぞれの応答は、コンソール ウィンドウに出力されます。

```bash
go run create-new-knowledge-base
```

作成されたナレッジ ベースは、QnA Maker ポータルの [[My knowledge bases]\(マイ ナレッジ ベース\)](https://www.qnamaker.ai/Home/MyServices) ページで確認できます。

[!INCLUDE [Clean up files and KB](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)]

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [QnA Maker (V4) REST API リファレンス](https://go.microsoft.com/fwlink/?linkid=2092179)
