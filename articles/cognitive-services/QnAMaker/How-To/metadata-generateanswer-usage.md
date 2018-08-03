---
title: GenerateAnswer API と共にナレッジ ベースのメタデータを使用する | Microsoft Docs
description: GenerateAnswer API と共にメタデータを使用する
services: cognitive-services
author: pchoudhari
manager: rsrikan
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 05/18/2018
ms.author: pchoudh
ms.openlocfilehash: 82e3ee460309f293c9bd7eadebe139f85e241f71
ms.sourcegitcommit: 7827d434ae8e904af9b573fb7c4f4799137f9d9b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/18/2018
ms.locfileid: "39113353"
---
# <a name="using-metadata-and-the-generateanswer-api"></a>メタデータと GenerateAnswer API の使用

QnA Maker では、キー/値のペアの形式で、メタデータを質問/回答のセットに追加することができます。 この情報は、ユーザー クエリに対する結果のフィルター処理、特定の結果のブースト、フォローアップ会話で使用できる追加情報の格納など、さまざまな方法で利用できます。 詳細については、「[Knowledge base](../Concepts/knowledge-base.md)」 (ナレッジ ベース) を参照してください。

## <a name="qna-entity"></a>QnA エンティティ

まず、QnA Maker での質問/回答データの格納方法を理解することが重要です。 QnA エンティティを次の図に示します。

![QnA エンティティ](../media/qnamaker-how-to-metadata-usage/qna-entity.png)

QnA エンティティにはそれぞれ一意の永続 ID があります。 ID を使用して、特定の QnA エンティティを更新することができます。

## <a name="generateanswer-api"></a>GenerateAnswer API

ボットやアプリケーションで GenerateAnswer API を使用して、ユーザーの質問についてナレッジ ベースのクエリを実行し、質問/回答のセットから最も一致するものを取得します。

### <a name="generateanswer-endpoint"></a>GenerateAnswer エンドポイント

ナレッジ ベースを公開したら、[QnA Maker ポータル](https://www.qnamaker.ai)から、または [API](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff) を使用して、GenerateAnswer エンドポイントの詳細を取得することができます。

エンドポイントの詳細を取得するには、次のようにします。
1. [https://www.qnamaker.ai](https://www.qnamaker.ai) にログインします。
2. **[My knowledge bases]\(マイ ナレッジ ベース\)** で、ご利用のナレッジ ベースの **[コードの表示]** をクリックします。
![マイ ナレッジ ベース](../media/qnamaker-how-to-metadata-usage/my-knowledge-bases.png)
3. GenerateAnswer エンドポイントの詳細を取得します。

![エンドポイントの詳細](../media/qnamaker-how-to-metadata-usage/view-code.png)

エンドポイントの詳細は、ナレッジ ベースの **[設定]** タブから取得することもできます。

### <a name="generateanswer-request"></a>GenerateAnswer 要求

HTTP POST 要求で GenerateAnswer を呼び出します。 GenerateAnswer を呼び出す方法を示すサンプル コードについては、[クイック スタート](../quickstarts/csharp.md)を参照してください。

- **要求 URL**: https://{QnA Maker エンドポイント}/knowledgebases/{ナレッジ ベース ID}/generateAnswer

- **要求パラメーター**: 
    - **ナレッジ ベース ID** (文字列): ナレッジ ベースの GUID。
    - **QnA Maker エンドポイント** (文字列): Azure サブスクリプションにデプロイされているエンドポイントのホスト名。
- **要求ヘッダー**
    - **Content-Type** (文字列): API に送信される本文のメディアの種類。
    - **Authorization** (文字列): エンドポイント キー (EndpointKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx)。
- **要求本文**
    - **question** (文字列): ナレッジ ベースに対してクエリを実行するユーザーの質問。
    - **top** (省略可能。整数): 出力を含めるランク付けされた結果の数。 既定値は 1 です。
    - **userId** (省略可能。文字列): ユーザーを識別する一意の ID。 この ID はチャット ログに記録されます。
    - **strictFilters** (省略可能。文字列): 指定した場合、指定されたメタデータを含む回答のみを返すように QnA Maker に指示します。 詳細については、以下を参照してください。
    ```json
    {
        "question": "qna maker and luis",
        "top": 6,
        "strictFilters": [
        {
            "name": "category",
            "value": "api"
        }],
        "userId": "sd53lsY="
    }
    ```

### <a name="generateanswer-response"></a>GenerateAnswer 応答

- **応答 200** - 成功した呼び出しで質問の結果が返されます。 応答には次のフィールドが含まれます。
    - **answers** ランキング スコアの降順に並べ替えられた、ユーザー クエリの回答のリスト。
        - **score**: 0 から 100 のランキング スコア。
        - **questions**: ユーザーによって提供された質問。
        - **answer**: 質問への答え。
        - **source**: ナレッジ ベースで抽出または保存された回答のソースの名前。
        - **metadata**: 回答に関連付けられたメタデータ。
            - name: メタデータの名前。 (文字列。最大長: 100。必須)
            - value: メタデータの値。 (文字列。最大長: 100。必須)
        - **Id**: 回答に割り当てられた一意の ID。
    ```json
    {
        "answers": [
            {
                "score": 28.54820341616869,
                "Id": 20,
                "answer": "There is no direct integration of LUIS with QnA Maker. But, in your bot code, you can use LUIS and QnA Maker together. [View a sample bot](https://github.com/Microsoft/BotBuilder-CognitiveServices/tree/master/Node/samples/QnAMaker/QnAWithLUIS)",
                "source": "Custom Editorial",
                "questions": [
                    "How can I integrate LUIS with QnA Maker?"
                ],
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

## <a name="metadata-example"></a>メタデータの例

ハイデラバードのレストランに関する以下の FAQ データがあるとします。 歯車アイコンをクリックして、メタデータをナレッジ ベースに追加します。

![メタデータの追加](../media/qnamaker-how-to-metadata-usage/add-metadata.png)

### <a name="filter-results-with-strictfilters"></a>strictFilters で結果をフィルター処理する

ユーザーの質問が "When does this hotel close?" (このホテルはいつ閉まりますか?) であるとします。 これには、"Paradise" というレストランがいつ閉まるかという意味が含まれます。

"Paradise" レストランに関する結果のみが必要であるため、次のように、メタデータ "Restaurant Name" に対する GenerateAnswer 呼び出しでフィルターを設定することができます。

```json
{
    "question": "When does this hotel close?",
    "top": 1,
    "strictFilters": [
      {
        "name": "restaurant",
        "value": "paradise"
      }]
}
```

### <a name="keep-context"></a>コンテキストの保持
GenerateAnswer への応答には、次のように、一致した質問/回答のセットの対応するメタデータ情報が含まれます。

```json
{
    "answers": [
        {
            "questions": [
                "What is the closing time?"
            ],
            "answer": "10.30 PM",
            "score": 100,
            "id": 1,
            "source": "Editorial",
            "metadata": [
                {
                    "name": "restaurant",
                    "value": "paradise"
                },
                {
                    "name": "location",
                    "value": "secunderabad"
                }
            ]
        }
    ]
}
```

この情報を使って、以降の会話で使用するために以前の会話のコンテキストを記録することができます。 

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [ナレッジ ベースの作成](./create-knowledge-base.md)
