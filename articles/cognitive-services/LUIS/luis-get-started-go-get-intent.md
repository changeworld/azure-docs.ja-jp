---
title: Go 言語クイック スタート - 意図の予測 - LUIS
titleSuffix: Azure Cognitive Services
description: このクイック スタートでは、提供されているパブリック LUIS アプリを使って、会話形式のテキストからユーザーの意図を判断します。 GO を使用して、パブリック アプリの HTTP 予測エンドポイントにユーザーの意図をテキストとして送信します。 エンドポイントでは、LUIS によってパブリック アプリのモデルが適用されます。これにより自然言語テキストの意味が分析され、全体的な意図が特定されて、アプリのサブジェクト ドメインに関連したデータが抽出されます。
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: quickstart
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: f19c46fcc7dcff2e02786bb3a03082f831e880a2
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2018
ms.locfileid: "47032735"
---
# <a name="quickstart-get-intent-using-go"></a>クイック スタート: Go による意図の取得

[!INCLUDE [Quickstart introduction for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-intro-para.md)]

## <a name="prerequisites"></a>前提条件

* [Go](https://golang.org/) プログラミング言語  
* [Visual Studio Code](https://code.visualstudio.com/)
* パブリック アプリ ID: df67dcdb-c37d-46af-88e1-8b97951ca1c2

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-luis-repo-note.md)]

## <a name="get-luis-key"></a>LUIS キーを取得する

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-get-key-para.md)]

## <a name="get-intent-with-browser"></a>ブラウザーで意図を取得する

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-browser-para.md)]

## <a name="get-intent-programmatically"></a>プログラムで意図を取得する

Go を使用して、前の手順でブラウザー ウィンドウに表示されたものと同じ結果にアクセスできます。 

1. `endpoint.go` という名前で新しいファイルを作成します。 次のコードを追加します。
    
   [!code-go[Go code that calls a LUIS endpoint](~/samples-luis/documentation-samples/quickstarts/analyze-text/go/endpoint.go?range=36-98)]

2. このファイルを作成したディレクトリからコマンド プロンプトで「`go build endpoint.go`」と入力し、Go ファイルをコンパイルします。 このコマンド プロンプトから、ビルドの成功に関する情報は一切返されません。

3. コマンド プロンプトに次のテキストを入力して、コマンド ラインから Go アプリケーションを実行します。 

    ```CMD
    go run endpoint.go -appID df67dcdb-c37d-46af-88e1-8b97951ca1c2 -endpointKey <add-your-key> -region westus
    ```
    
    `<add-your-key>` は、実際のキーの値に置き換えてください。  
    
    コマンド プロンプトから次のような応答が返されます。 
    
    ```CMD
    appID has value df67dcdb-c37d-46af-88e1-8b97951ca1c2
    endpointKey has value xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
    region has value westus
    utterance has value turn on the bedroom light
    response
    {
        "query": "turn on the bedroom light",
        "topScoringIntent": {
            "intent": "HomeAutomation.TurnOn",
            "score": 0.809439957
        },
        "entities": [
            {
            "entity": "bedroom",
            "type": "HomeAutomation.Room",
            "startIndex": 12,
            "endIndex": 18,
            "score": 0.8065475
            }
        ]
    }
    ```
    
## <a name="luis-keys"></a>LUIS キー

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-key-usage-para.md)]

## <a name="clean-up-resources"></a>リソースのクリーンアップ
Go ファイルを閉じてファイル システムから削除します。 

## <a name="next-steps"></a>次の手順
> [!div class="nextstepaction"]
> [発話の追加](luis-get-started-go-add-utterance.md)