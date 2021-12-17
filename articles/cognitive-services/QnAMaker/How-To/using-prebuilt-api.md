---
title: QnA Maker の事前構築済み API
titleSuffix: Azure Cognitive Services
description: 事前構築済み API を使用してテキストで回答を取得する
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 05/05/2021
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: b7e505bfbb27aca479569ff6cddfa2686674ae94
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/10/2021
ms.locfileid: "111954115"
---
# <a name="prebuilt-question-answering"></a>事前構築済みの質問と回答

事前構築済みの質問と回答を使用すると、ナレッジベースを作成したり、質問と回答のペアのメンテナンスを行ったり、使用率が低いインフラストラクチャのコストを負担することなく、テキストによる質問に回答することができます。 この機能は API として提供され、質問と回答に関するニーズを満たすために使用できます。QnA Maker や追加のストレージに関する詳細を学習する必要はありません。

ユーザー クエリとテキストや文章のブロックを指定すると、API によって回答と正確な回答 (使用可能な場合) が返されます。 

<a name="qna-entity"></a>


## <a name="example-usage-of-prebuilt-question-answering"></a>事前構築済みの質問と回答の使用例

特定の質問に対する回答を取得したいテキストのブロックが 1 つ以上あるとします。 従来であれば、テキストのブロック数と同じ数のソースを作成する必要がありました。 しかし、事前構築済みの質問と回答を使用すれば、ナレッジ ベースでコンテンツ ソースを定義することなく、テキスト ブロックに対してクエリを実行できます。 

事前構築済み API を使用できるその他のシナリオをいくつか次に示します。

* テキストを強調表示して、質問を入力すると、強調表示されたテキストに対する回答の検索が行われる電子ブック リーダー アプリの開発 
* ブラウザー ページに現在表示されている内容について質問できるブラウザー拡張機能
* ユーザーからクエリを受け取り、ユーザー クエリとの関連性が最も高いとボットによって識別された医療コンテンツに基づいて回答を提供する健康ボット 

要求のサンプルの例は次のとおりです。

## <a name="sample-request"></a>要求のサンプル
```
POST https://{Endpoint}/qnamaker/v5.0-preview.2/generateanswer
```

### <a name="sample-query-over-a-single-block-of-text"></a>1 つのテキスト ブロックに対するサンプル クエリ

要求本文

```json
{
    "question": "How long it takes to charge surface pro 4?",
    "documents": [
        {
            "text": "### The basics #### Power and charging It takes two to four hours to charge the Surface Pro 4 battery fully from an empty state. It can take longer if you’re using your Surface for power-intensive activities like gaming or video streaming while you’re charging it. You can use the USB port on your Surface Pro 4 power supply to charge other devices, like a phone, while your Surface charges.",
            "id": "doc1"
        }
    ],
    "Language": "en"
}
```
## <a name="sample-response"></a>応答のサンプル

上の要求本文では、1 つのテキスト ブロックに対してクエリを実行します。 上のクエリに対して受信した応答のサンプルは次のとおりです。

```json
{
    "answers": [
        {
            "answer": "### The basics #### Power and charging It takes two to four hours to charge the Surface Pro 4 battery fully from an empty state. It can take longer if you’re using your Surface for power-intensive activities like gaming or video streaming while you’re charging it. You can use the USB port on your Surface Pro 4 power supply to charge other devices, like a phone, while your Surface charges.",
            "answerSpan": {
                "text": "two to four hours",
                "score": 0.0,
                "startIndex": 47,
                "endIndex": 64
            },
            "score": 0.9599020481109619,
            "id": "doc1",
            "answerStartIndex": 0,
            "answerEndIndex": 390
        },
        {
            "answer": "It can take longer if you’re using your Surface for power-intensive activities like gaming or video streaming while you’re charging it. You can use the USB port on your Surface Pro 4 power supply to charge other devices, like a phone, while your Surface charges.",
            "score": 0.06749606877565384,
            "id": "doc1",
            "answerStartIndex": 129,
            "answerEndIndex": 390
        },
        {
            "answer": "You can use the USB port on your Surface Pro 4 power supply to charge other devices, like a phone, while your Surface charges.",
            "score": 0.011389964260160923,
            "id": "doc1",
            "answerStartIndex": 265,
            "answerEndIndex": 390
        }
    ]
}
```
API 応答の一部として複数の回答が返されているのが確認できます。 各回答には、回答の全体的な関連度を理解するのに役立つ具体的な信頼度スコアが付けられています。 ユーザーは、この信頼度スコアを利用して、クエリに対する回答を表示できます。

## <a name="prebuilt-api-limits"></a>事前構築済み API の制限 

[事前構築済み API の制限](../limits.md#prebuilt-question-answering-limits)に関するドキュメントを参照してください 

## <a name="prebuilt-api-reference"></a>事前構築済み API のリファレンス
API の呼び出しに必要な入力と出力のパラメーターについては、[事前構築済み API のリファレンス](/rest/api/cognitiveservices-qnamaker/qnamaker5.0preview2/prebuilt/generateanswer)に関するドキュメントを参照してください。