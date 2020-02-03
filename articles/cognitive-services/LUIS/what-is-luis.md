---
title: Language Understanding (LUIS) とは
titleSuffix: Azure Cognitive Services
description: Language Understanding (LUIS) は、カスタムの機械学習インテリジェンスをユーザーの自然言語での会話に適用して、全体の意味を予測し、関連性のある詳細な情報を引き出すクラウド ベースの API サービスです。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: overview
ms.date: 11/22/2019
ms.author: diberry
ms.openlocfilehash: 99f312521727658788e96a57b619a7c0e3d4751b
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/24/2019
ms.locfileid: "74456556"
---
# <a name="what-is-language-understanding-luis"></a>Language Understanding (LUIS) とは

Language Understanding (LUIS) は、カスタムの機械学習インテリジェンスを自然言語のテキストに適用して、全体の意味を予測し、関連性のある詳細な情報を引き出すクラウドベースの API サービスです。 

たとえば、クライアント アプリケーションがテキスト `find me a wireless keyboard for $30` を送信すると、LUIS は次の JSON オブジェクトで応答します。 

```JSON
{
    "query": "find me a wireless keyboard for $30",
    "prediction": {
        "topIntent": "Finditem",
        "intents": {
            "Finditem": {
                "score": 0.934672
            }
        },
        "entities": {
            "item": [
                "wireless keyboard"
            ],
            "money": [
        {
            "number": 30,
            "units": "Dollar"
        }
           ]
        }
        
    }
}
```
上記の例では、" _**intent**_ "、つまり語句の全体的な意味は、ユーザーが品目を検索しようとしていることです。 LUIS によって抽出される詳細な情報は、" _**entities**_ " と呼ばれます。 このケースでは、エンティティは、ユーザーが探している品目の名前と、使用したい金額です。

クライアント アプリケーションは、LUIS から返された JSON、"_意図_" (カテゴリ)、および "_エンティティ_" (抽出された詳細情報) を使用して、クライアント アプリケーションでアクションを実行します。 LUIS 向けのクライアント アプリケーションは、多くの場合、ユーザーと自然言語でコミュニケーションを行ってタスクを完了する会話型アプリケーションです。 クライアント アプリケーションの例として、ソーシャル メディア アプリ、チャットボット、音声対応デスクトップ アプリケーションがあります。 

![Cognitive Services Language Understanding (LUIS) と連携する 3 つのクライアント アプリケーションの概念図](./media/luis-overview/luis-entry-point.png "Cognitive Services Language Understanding (LUIS) と連携する 3 つのクライアント アプリケーションの概念図")

## <a name="example-use-luis-in-a-chat-bot"></a>チャット ボットでの LUIS の使用例

<a name="Accessing-LUIS"></a>

クライアント アプリケーションは、発行された LUIS 自然言語処理エンドポイント [API][endpoint-apis] に発話 (テキスト) を送信し、その結果を JSON 応答として受信します。 LUIS の一般的なクライアント アプリケーションはチャット ボットです。


![自然言語の理解 (NLP) でユーザー テキストを予測するためにチャット ボットと連携する LUIS の概念図](./media/luis-overview/LUIS-chat-bot-request-response.svg "自然言語の理解 (NLP) でユーザー テキストを予測するためにチャット ボットと連携する LUIS の概念図")

|手順|Action|
|:--|:--|
|1|クライアント アプリケーションがユーザーの "_発話_" (自分の言葉で表現されたテキスト) を送信します。ここでは、"I want to call my HR rep." を LUIS エンドポイントに HTTP 要求として送信します。|
|2|LUIS によってユーザーの非構造化入力テキストに機械学習言語モデルが適用され、最上位の意図 `HRContact` を含む JSON 形式の応答が返されます。 エンドポイントからの JSON 形式の応答には、少なくとも、クエリの発話と上位スコアの意図が含まれます。 _Contact Type_ エンティティなどのデータを抽出することもできます。|
|3|クライアント アプリケーションが、JSON 応答を使用して、ユーザーから受けた要求への対応方法を判断します。 これらの判断には、ボット内のデシジョン ツリーや、他のサービスの呼び出しが含まれることがあります。 |

LUIS アプリは、クライアント アプリケーションが賢明な選択を行えるようにするためのインテリジェンスを提供します。 そうした選択を LUIS が提供するわけではありません。 

<a name="Key-LUIS-concepts"></a>
<a name="what-is-a-luis-model"></a>

## <a name="natural-language-processing"></a>自然言語処理

LUIS アプリには、特定の領域 (ドメイン) に固有の連携する自然言語モデルが含まれます。 LUIS アプリは、1 つ以上の構築済みのモデルをひな形にできるほか、独自にモデルを構築したり、構築済みのモデルに独自の情報を融合したりすることができます。

* **事前構築済みモデル** LUIS には、一般的な使用シナリオを完成させるために連携する意図モデルおよびエンティティ モデルを含む多数の事前構築済みドメインがあります。 これらのドメインには、検査と編集が可能なラベル付き発話が含まれており、それらをカスタマイズできます。 [構築済みのドメイン モデル](luis-how-to-use-prebuilt-domains.md)には、必要な設計がすべて含まれているため、LUIS の使用をすぐに開始できる優れた方法です。 さらに、事前構築済みドメインとは別に使用できる、通貨や数値などの事前構築済みエンティティもあります。

* **カスタム モデル** LUIS を使用すると、意図を含む独自のカスタム モデルやエンティティを複数の方法で構築できます。 エンティティには、機械学習エンティティ、パターン マッチング エンティティ、および機械学習とパターン マッチングの組み合わせがあります。

## <a name="build-the-luis-app"></a>LUIS アプリの構築
アプリの構築には、[オーサリング](https://go.microsoft.com/fwlink/?linkid=2092087) API または [LUIS ポータル](https://www.luis.ai)を使用します。

LUIS アプリは、 **[意図](luis-concept-intent.md)** と呼ばれる入力テキストのカテゴリから始めます。 意図にはそれぞれ、ユーザー **[発話](luis-concept-utterance.md)** の例が必要です。 各発話では、抽出する必要があるデータが提供される場合があります。 

|ユーザーの発話例|Intent|抽出された日付|
|-----------|-----------|-----------|
|`Book a flight to __Seattle__?`|BookFlight|Seattle|
|`When does your store __open__?`|StoreHoursAndLocation|open|
|`Schedule a meeting at __1pm__ with __Bob__ in Distribution`|ScheduleMeeting|1pm、Bob (午後 1 時、Bob)|

## <a name="query-prediction-endpoint"></a>予測エンドポイントに対するクエリの実行

アプリがトレーニングされてエンドポイントに発行された後は、クライアント アプリケーションによって、予測[エンドポイント](https://go.microsoft.com/fwlink/?linkid=2092356) API に発話が送信されます。 API によって、分析のために発話にアプリが適用され、予測結果が JSON 形式で返されます。  

エンドポイントからの JSON 形式の応答には、少なくとも、クエリの発話と上位スコアの意図が含まれます。 以下の **Contact Type** エンティティや全体的なセンチメントなどのデータを抽出することもできます。 

```JSON
{
    "query": "I want to call my HR rep",
    "prediction": {
        "normalizedQuery": "i want to call my hr rep",
        "topIntent": "HRContact",
        "intents": {
            "HRContact": {
                "score": 0.8582669
            }
        },
        "entities": {
            "Contact Type": [
                "call"
            ]
        },
        "sentiment": {
            "label": "negative",
            "score": 0.103343368
        }
    }
}
```

## <a name="improve-model-prediction"></a>モデルの予測の改善

LUIS アプリが公開され、実際のユーザーの発話を受け取ると、予測精度向上のためにエンドポイント発話の[アクティブな学習](luis-concept-review-endpoint-utterances.md)が LUIS によって提供されます。 

<a name="using-luis"></a>

## <a name="iterative-development-lifecycle"></a>反復開発ライフサイクル
LUIS では、完全な反復[開発ライフ サイクル](luis-concept-app-iteration.md)に統合するためのツール、バージョン管理、および他の LUIS 作成者とのコラボレーションが提供されます。 

## <a name="implementing-luis"></a>LUIS の実装
Language Understanding (LUIS) は、REST API として、HTTP 要求を行う任意の製品、サービス、フレームワークで使用できます。 以下に、LUIS で使用される Microsoft の代表的な製品とサービスを列挙しています。

LUIS のトップ クライアント アプリケーション:
* [Web アプリ ボット](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0): テキスト入力を介してユーザーと会話する LUIS 対応のチャット ボットをすばやく作成します。 完成度の高いボット エクスペリエンスを実現するために、[Bot Framework][bot-framework] バージョン [4.x](https://github.com/Microsoft/botbuilder-dotnet) を使用してください。

LUIS をボットですばやく簡単に使用するためのツール:
* [LUIS CLI](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/LUIS): この NPM パッケージは、スタンドアロンのコマンド ライン ツールまたはインポートとして、作成と予測を提供します。 
* [LUISGen](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/LUISGen): LUISGen は、エクスポートされた LUIS モデルから厳密に型指定された C# および typescript のソース コードを生成するためのツールです。
* [Dispatch](https://aka.ms/dispatch-tool): ディスパッチャー モデルを使うことで、いくつかの LUIS アプリや QnA Maker アプリを親アプリから使用できます。
* [LUDown](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Ludown): LUDown は、ボットの言語モデルを管理するのに役立つコマンド ライン ツールです。
* [Bot Framework - Composer](https://github.com/microsoft/BotFramework-Composer) - Microsoft Bot Framework を使用したボットや会話エクスペリエンスの作成に携わる開発者およびさまざまな分野にわたるチームのための統合開発ツールです。

LUIS で使用されるその他の Cognitive Services:
* [QnA Maker][qnamaker] は、多種多様なテキストを質問と回答のナレッジ ベースに統合できるようにします。
* [Speech サービス](../Speech-Service/overview.md)は、話し言葉による要求をテキストに変換します。 
* [Conversation Learner](https://docs.microsoft.com/azure/cognitive-services/labs/conversation-learner/overview): LUIS を使ったボットの会話を短時間で構築できます。

LUIS を使用したサンプル:
* [会話型 AI](https://github.com/Microsoft/AI) GitHub リポジトリ。
* [Bot Framework - ボット サンプル](https://github.com/microsoft/BotBuilder-Samples)

## <a name="next-steps"></a>次の手順

* [新機能](whats-new.md)
* [構築済み](luis-get-started-create-app.md)または[カスタム](luis-quickstart-intents-only.md) ドメインを使用して、新しい LUIS アプリを作成する
* 公開 IoT アプリの[予測エンドポイントに対してクエリを実行](luis-get-started-get-intent-from-browser.md)します。 
* LUIS の[開発者向けリソース](developer-reference-resource.md)。 

[bot-framework]: https://docs.microsoft.com/bot-framework/
[flow]: https://docs.microsoft.com/connectors/luis/
[authoring-apis]: https://go.microsoft.com/fwlink/?linkid=2092087
[endpoint-apis]: https://go.microsoft.com/fwlink/?linkid=2092356
[qnamaker]: https://qnamaker.ai/
