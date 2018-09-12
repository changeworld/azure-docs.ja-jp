---
title: Language Understanding (LUIS) の自然言語テキスト分析を Ruby を使って行う - Cognitive Services - Azure Cognitive Services | Microsoft Docs
description: このクイック スタートでは、提供されているパブリック LUIS アプリを使って、会話形式のテキストからユーザーの意図を判断します。 Ruby を使用して、パブリック アプリの HTTP 予測エンドポイントにユーザーの意図をテキストとして送信します。 エンドポイントでは、LUIS によってパブリック アプリのモデルが適用されます。これにより自然言語テキストの意味が分析され、全体的な意図が特定されて、アプリのサブジェクト ドメインに関連したデータが抽出されます。
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: quickstart
ms.date: 08/23/2018
ms.author: diberry
ms.openlocfilehash: 7344d0e4d649134b7d928daec99fa79d0644a7e4
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/24/2018
ms.locfileid: "43771701"
---
# <a name="quickstart-analyze-text-using-ruby"></a>クイック スタート: Ruby を使ってテキストを分析する

[!include[Quickstart introduction for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-intro-para.md)]

## <a name="prerequisites"></a>前提条件

* [Ruby](https://www.ruby-lang.org/) プログラミング言語
* [Visual Studio Code](https://code.visualstudio.com/)
* パブリック アプリ ID: df67dcdb-c37d-46af-88e1-8b97951ca1c2


[!include[Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-luis-repo-note.md)]

<a name="create-luis-subscription-key"></a>

## <a name="get-luis-key"></a>LUIS キーを取得する

[!include[Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-get-key-para.md)]

## <a name="analyze-text-with-browser"></a>ブラウザーでテキストを分析する

[!include[Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-browser-para.md)]

## <a name="analyze-text-with-ruby"></a>Ruby でテキストを分析する 

Ruby を使用して、前の手順でブラウザー ウィンドウに表示されたものと同じ結果にアクセスできます。 

1. 以下のコードをコピーして、`endpoint-call.rb` という名前のファイルに保存します。

   [!code-ruby[Ruby code that calls a LUIS endpoint](~/samples-luis/documentation-samples/quickstarts/analyze-text/ruby/endpoint-call.rb)]

2. `"YOUR-KEY"` を実際のエンドポイント キーで置き換えます。

3. コマンド ラインから `ruby endpoint-call.rb` を使用して Ruby アプリケーションを実行します。 前の手順でブラウザー ウィンドウに表示されたものと同じ JSON が表示されます。

    ```
    LUIS query: turn on the left light
    
    Request URI: https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2?q=turn+on+the+left+light&timezoneOffset=0&verbose=false&spellCheck=false&staging=false
    
    JSON Response:
    
    {
      "query": "turn on the left light",
      "topScoringIntent": {
        "intent": "HomeAutomation.TurnOn",
        "score": 0.933549
      },
      "entities": [
        {
          "entity": "left",
          "type": "HomeAutomation.Room",
          "startIndex": 12,
          "endIndex": 15,
          "score": 0.540835142
        }
      ]
    }
```

## <a name="clean-up-resources"></a>リソースのクリーンアップ

Ruby ファイルを削除します。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [発話の追加](luis-get-started-ruby-add-utterance.md)