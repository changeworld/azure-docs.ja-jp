---
title: 事前構築済み API - 質問応答
titleSuffix: Azure Cognitive Services
description: 質問応答の事前構築済み API を使用して、質問を発したり、質問への回答を受け取ったりできます。プロジェクトやナレッジ ベースを作成する必要はありません。
ms.service: cognitive-services
ms.subservice: language-service
author: mrbullwinkle
ms.author: mbullwin
ms.topic: conceptual
ms.date: 11/03/2021
ms.openlocfilehash: 9fe4b80220ccb94e2b89bcdeaacf8cacac624ed0
ms.sourcegitcommit: 512e6048e9c5a8c9648be6cffe1f3482d6895f24
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/10/2021
ms.locfileid: "132159374"
---
# <a name="prebuilt-api"></a>事前構築済み API

質問応答の **事前構築済み API** を使用すると、プロジェクトやナレッジ ベースを作成したり、質問と回答のペアをメンテナンスしたり、使用率が低いインフラストラクチャのコストを負担したりすることなく、テキストの一節に基づいた質問に回答することができます。 この機能は API として提供され、質問と回答に関するニーズを満たすために使用できます。質問応答に関する詳細を学習する必要はありません。

ユーザー クエリとテキストや文章のブロックを指定すると、API によって回答と正確な回答 (使用可能な場合) が返されます。

## <a name="example-api-usage"></a>API の使用例

特定の質問に対する回答を取得したいテキストのブロックが 1 つ以上あるとします。 通常は、テキストのブロック数と同じ数のソースを作成する必要がありました。 しかし、事前構築済み API を使用すれば、プロジェクトやナレッジ ベースでコンテンツ ソースを定義しなくても、テキスト ブロックに対してクエリを実行できます。

この API を使用できるその他のシナリオをいくつか次に示します。

* テキストを強調表示して、質問を入力すると、強調表示されたテキストの一節に対する回答の検索が行われる電子ブック リーダー アプリの開発。
* ブラウザー ページに現在表示されている内容について質問できるブラウザー拡張機能。
* ユーザーからクエリを受け取り、ユーザー クエリとの関連性が最も高いとボットによって識別された医療コンテンツに基づいて回答を提供する健康ボット。

要求のサンプルの例は次のとおりです。

## <a name="sample-request"></a>要求のサンプル

```
POST https://{Unique-to-your-endpoint}.api.cognitive.microsoft.com/language/:query-text
```

### <a name="sample-query-over-a-single-block-of-text"></a>1 つのテキスト ブロックに対するサンプル クエリ

要求本文

```json
{
  "parameters": {
    "Endpoint": "{Endpoint}",
    "Ocp-Apim-Subscription-Key": "{API key}",
    "Content-Type": "application/json",
    "api-version": "2021-10-01",
    "stringIndexType": "TextElements_v8",
    "textQueryOptions": {
      "question": "how long it takes to charge surface?",
      "records": [
        {
          "id": "1",
          "text": "Power and charging. It takes two to four hours to charge the Surface Pro 4 battery fully from an empty state. It can take longer if you’re using your Surface for power-intensive activities like gaming or video streaming while you’re charging it."
        },
        {
          "id": "2",
          "text": "You can use the USB port on your Surface Pro 4 power supply to charge other devices, like a phone, while your Surface charges. The USB port on the power supply is only for charging, not for data transfer. If you want to use a USB device, plug it into the USB port on your Surface."
        }
      ],
      "language": "en"
    }
  }
}
```

## <a name="sample-response"></a>応答のサンプル

上の要求本文では、1 つのテキスト ブロックに対してクエリを実行します。 上のクエリに対して受信した応答のサンプルは次のとおりです。

```json
{
"responses": {
    "200": {
      "headers": {},
      "body": {
        "answers": [
          {
            "answer": "Power and charging. It takes two to four hours to charge the Surface Pro 4 battery fully from an empty state. It can take longer if you’re using your Surface for power-intensive activities like gaming or video streaming while you’re charging it.",
            "confidenceScore": 0.93,
            "id": "1",
            "answerSpan": {
              "text": "two to four hours",
              "confidenceScore": 0,
              "offset": 28,
              "length": 45
            },
            "offset": 0,
            "length": 224
          },
          {
            "answer": "It takes two to four hours to charge the Surface Pro 4 battery fully from an empty state. It can take longer if you’re using your Surface for power-intensive activities like gaming or video streaming while you’re charging it.",
            "confidenceScore": 0.92,
            "id": "1",
            "answerSpan": {
              "text": "two to four hours",
              "confidenceScore": 0,
              "offset": 8,
              "length": 25
            },
            "offset": 20,
            "length": 224
          },
          {
            "answer": "It can take longer if you’re using your Surface for power-intensive activities like gaming or video streaming while you’re charging it.",
            "confidenceScore": 0.05,
            "id": "1",
            "answerSpan": null,
            "offset": 110,
            "length": 244
          }
        ]
      }
    }
  }
```

API 応答の一部として複数の回答が返されているのが確認できます。 各回答には、回答の全体的な関連度を理解するのに役立つ具体的な信頼度スコアが付けられています。 回答スパンは、有効な可能性がある短い回答も検出されたかどうかを表します。 ユーザーはこの信頼度スコアを利用して、クエリに応答して提供する回答を決定できます。

## <a name="prebuilt-api-limits"></a>事前構築済み API の制限

制限を超えるドキュメントを使用する必要がある場合は、テキストを小さなチャンクに分割してから API に送信することができます。 このコンテキストでは、ドキュメントはテキスト文字の定義済み単一文字列です。

これらの数値は、**個々の API 呼び出しあたりの制限** を表します。

* ドキュメントの数: 5。
* 1 つのドキュメントの最大サイズ: 5,120 文字。
* ドキュメントあたりの最大応答数は 3 です。

## <a name="prebuilt-api-reference"></a>事前構築済み API のリファレンス

API の呼び出しに必要な入力と出力のパラメーターについては、[完全な事前構築済み API のサンプル](https://github.com/Azure/azure-rest-api-specs/blob/main/specification/cognitiveservices/data-plane/Language/stable/2021-10-01/examples/questionanswering/SuccessfulQueryText.json)に関するドキュメントを参照してください。