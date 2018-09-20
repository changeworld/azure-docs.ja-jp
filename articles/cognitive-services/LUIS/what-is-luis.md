---
title: Language Understanding (LUIS) とは - Azure Cognitive Services | Microsoft Docs
description: Language Understanding (LUIS) は、カスタムの機械学習インテリジェンスをユーザーの自然言語での会話に適用して、全体の意味を予測し、関連性のある詳細な情報を引き出すクラウド ベースの API サービスです。 LUIS のクライアント アプリケーションは、ユーザーと自然言語でコミュニケーションを行ってタスクを完了する会話型アプリケーションであれば、どれでもかまいません。 クライアント アプリケーションの例として、ソーシャル メディア アプリ、チャットボット、音声対応デスクトップ アプリケーションがあります。
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: overview
ms.date: 08/15/2018
ms.author: diberry
ms.openlocfilehash: aadf80df388a25e07051f6dd0a83cd4da7c2ef83
ms.sourcegitcommit: ab9514485569ce511f2a93260ef71c56d7633343
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/15/2018
ms.locfileid: "45629799"
---
# <a name="what-is-language-understanding-luis"></a>Language Understanding (LUIS) とは

Language Understanding (LUIS) は、カスタムの機械学習インテリジェンスをユーザーの自然言語での会話に適用して、全体の意味を予測し、関連性のある詳細な情報を引き出すクラウド ベースの API サービスです。 

LUIS のクライアント アプリケーションは、ユーザーと自然言語でコミュニケーションを行ってタスクを完了する会話型アプリケーションであれば、どれでもかまいません。 クライアント アプリケーションの例として、ソーシャル メディア アプリ、チャットボット、音声対応デスクトップ アプリケーションがあります。  

![Cognitive Services Language Understanding (LUIS) と連携する 3 つのクライアント アプリケーションの概念図](./media/luis-overview/luis-entry-point.png "Cognitive Services Language Understanding (LUIS) と連携する 3 つのクライアント アプリケーションの概念図")

## <a name="use-luis-in-a-chat-bot"></a>チャット ボットでの LUIS の使用

<a name="Accessing-LUIS"></a>

LUIS アプリの発行後、クライアント アプリケーションは、LUIS 自然言語処理エンドポイント [API][endpoint-apis] に発話 (テキスト) を送信し、その結果を JSON 応答として受信します。 LUIS の一般的なクライアント アプリケーションはチャット ボットです。


![チャット ボットと連携し、Natural Language Understanding (NLP) でユーザー テキストを予測する LUIS の概念図](./media/luis-overview/luis-overview-process-2.png "チャット ボットと連携し、Natural Language Understanding (NLP) でユーザー テキストを予測する LUIS の概念図")

|手順|アクションを表示します。|
|:--|:--|
|1|クライアント アプリケーションがユーザーの "_発話_" (自分の言葉で表現されたテキスト) を送信します。ここでは、"I want to call my HR rep." を LUIS エンドポイントに HTTP 要求として送信します。|
|2|LUIS は、自然言語テキストに対して学習済みのモデルを適用し、ユーザー入力についてのインテリジェントな解釈を提供します。 LUIS は、"HRContact" を最上位の意図とする JSON 形式の応答を返します。 エンドポイントからの JSON 形式の応答には、少なくとも、クエリの発話と上位スコアの意図が含まれます。 Contact Type エンティティなどのデータを抽出することもできます。|
|3|クライアント アプリケーションが、JSON 応答を使用して、ユーザーから受けた要求への対応方法を判断します。 これらの判断は、Bot Framework コード内の何らかのデシジョン ツリーを含んでいたり、他のサービスの呼び出しを伴ったりすることがあります。 |

LUIS アプリは、クライアント アプリケーションが賢明な選択を行えるようにするためのインテリジェンスを提供します。 そうした選択を LUIS が提供するわけではありません。 

<!--

### Example of JSON endpoint response

The minimum JSON endpoint response contains the query utterance, and the top scoring intent. It can also extract data such as the following **Contact Type** entity. 

```JSON
{
  "query": "I want to call my HR rep.",
  "topScoringIntent": {
    "intent": "HRContact",
    "score": 0.921233
  },
  "entities": [
    {
      "entity": "call",
      "type": "Contact Type",
      "startIndex": 10,
      "endIndex": 13,
      "score": 0.7615982
    }
  ]
}
```
-->

<a name="Key-LUIS-concepts"></a>
<a name="what-is-a-luis-model"></a>

## <a name="natural-language-processing"></a>自然言語処理

LUIS アプリには、特定の領域 (ドメイン) に固有の自然言語モデルが含まれます。 LUIS アプリは、構築済みのドメイン モデルをひな形にできるほか、独自にモデルを構築したり、構築済みのドメインの一部に独自の情報を融合したりすることができます。

* **構築済みのモデル**: LUIS には、意図、発話、および構築済みのエンティティを含む構築済みのドメイン モデルが多数あります。 構築済みのモデルの意図と発話を使用せずに、構築済みのエンティティを使用できます。 [構築済みのドメイン モデル](luis-how-to-use-prebuilt-domains.md)には、必要な設計がすべて含まれているため、LUIS の使用をすぐに開始できる優れた方法です。

* **カスタム エンティティ**: LUIS では、さまざまな方法で、独自の意図とエンティティ (機械学習によるエンティティ、具体的で逐語的なエンティティ、および機械学習によるエンティティと逐語的なエンティティの組み合わせなど) を識別する方法が用意されています。

## <a name="build-the-luis-model"></a>LUIS モデルの構築
モデルの構築には、[オーサリング](https://aka.ms/luis-authoring-apis) API または LUIS ポータルを使用します。

LUIS モデルの出発点は、ユーザーの目的のカテゴリです。これを "**[意図](luis-concept-intent.md)**" といいます。 意図にはそれぞれ、ユーザー**[発話](luis-concept-utterance.md)** の例が必要です。 それぞれの発話からさまざまなデータが得られますが、それらのデータは、**[エンティティ](luis-concept-entity-types.md)** を使って抽出する必要があります。 

|ユーザーの発話例|意図|エンティティ|
|-----------|-----------|-----------|
|"Book a flight to __Seattle__?" (シアトル行きの便を予約できる?)|BookFlight|シアトル|
|"When does your store __open__?" (お店は何時に開店しますか?)|StoreHoursAndLocation|open (開店)|
|"Schedule a meeting at __1pm__ with __Bob__ in Distribution" (ディストリビューションのボブと午後 1 時にミーティング)|ScheduleMeeting|1pm、Bob (午後 1 時、Bob)|

<!--
## What is a natural language model?

A model begins with a list of general user intentions, called _intents_, such as "Book Flight" or "Contact Help Desk." You provide user's example text, called _example utterances_ for the intents. Then mark significant words or phrases in the utterance, called _entities_.


A model includes:

* **[intents](#intents)**: categories of user intentions (overall intended action or result)
* **[entities](#entities)**: specific types of data in utterances such as number, email, or name contained in text
* **[example utterances](#example-utterances)**: example text a user might enter in the client application

### Intents 

An [intent](luis-how-to-add-intents.md), short for _intention_, is a purpose or goal expressed in a user's utterance, such as booking a flight, paying a bill, or finding a news article. You create an intent for each action. A LUIS travel app may define an intent named "BookFlight." Each prediction query includes the top scored intent. 

The client application can use the top scoring intent to trigger an action. For example, when "BookFlight" intent is returned from LUIS, a client application could trigger an API call to an external service for booking a plane ticket.

### Entities

An [entity](luis-how-to-add-entities.md) represents detailed information found within the utterance that is relevant to the user's request. For example, in the utterance "Book a ticket to Paris",  a single ticket is requested, and "Paris" is a location. Two entities are found "a ticket" indicating a single ticket and "Paris" indicating the destination. 

After LUIS returns the entities found in the user’s utterance, the client application can use the list of entities as parameters to trigger an action. For example, booking a flight requires entities like the travel destination, date, and airline.
-->
<!--
### Example utterances

An example [utterance](luis-how-to-add-example-utterances.md) is text input from the user that the client application needs to understand. It may be a sentence, like "Book a ticket to Paris", or a fragment of a sentence, like "Booking" or "Paris flight." Utterances aren't always well-formed, and there can be many utterance variations for a particular intent. Add 10 to 20 example utterances to each intent and mark entities in every utterance.

|Example user utterance|Intent|Entities|
|-----------|-----------|-----------|
|"Book a flight to __Seattle__?"|BookFlight|Seattle|
|"When does your store __open__?"|StoreHoursAndLocation|open|
|"Schedule a meeting at __1pm__ with __Bob__ in Distribution"|ScheduleMeeting|1pm, Bob|
-->
## <a name="query-prediction-endpoint"></a>予測エンドポイントに対するクエリの実行

モデルが構築されてエンドポイントに発行された後、クライアント アプリケーションは、その発行済みの予測[エンドポイント](https://aka.ms/luis-endpoint-apis)の API に発話を送信します。 解析の対象となるテキストには、この API によってモデルが適用されます。 API は、予測結果を JSON 形式の応答として返します。  

エンドポイントからの JSON 形式の応答には、少なくとも、クエリの発話と上位スコアの意図が含まれます。 **Contact Type** エンティティ (下記) などのデータを抽出することもできます。 

```JSON
{
  "query": "I want to call my HR rep.",
  "topScoringIntent": {
    "intent": "HRContact",
    "score": 0.921233
  },
  "entities": [
    {
      "entity": "call",
      "type": "Contact Type",
      "startIndex": 10,
      "endIndex": 13,
      "score": 0.7615982
    }
  ]
}
```

## <a name="improve-model-prediction"></a>モデルの予測の改善

LUIS モデルが発行され、ユーザーの実際の発話を受信した後、LUIS はさまざまな方法で予測精度を改善できます ([アクティブ ラーニング](#active-learning)ドメインの単語を含む[フレーズ リスト](#phrase-lists)、および必要な発話の数を減少させる[パターン](#patterns))。
<!--
### Active learning

In the [active learning](luis-how-to-review-endoint-utt.md) process, LUIS allows you to adapt the LUIS app to real-world utterances by selecting utterances it received at the endpoint for your review. You can accept or correct the endpoint prediction, retrain, and republish. LUIS learns quickly with this iterative process, taking the minimum amount of your time and effort. 

### Phrase lists 

LUIS provides [phrases lists](luis-concept-feature.md) so you can indicate important words or phrases of the model. LUIS uses these lists to add additional significance to those words and phrases that would otherwise not be found in the model.

### Patterns 

Patterns allow you to simplify an intent's utterance collection into common [templates](luis-concept-patterns.md) of word choice and word order. This allows LUIS to learn quicker by needing fewer example utterances for the intents. Patterns are a hybrid system of regular expressions and machine-learned expressions. 
-->
<a name="using-luis"></a>
<!--
## Authoring and accessing models
Author LUIS from the [authoring](https://aka.ms/luis-authoring-apis) APIs or from the LUIS portal. Query the published prediction endpoint of the model from the [endpoint](https://aka.ms/luis-endpoint-apis) APIs.
-->

## <a name="integrating-with-luis"></a>LUIS を使用した統合
LUIS は、REST API として、HTTP 要求を送信するあらゆる製品、サービス、フレームワークで使用できます。 以下に、LUIS で使用される Microsoft の代表的な製品とサービスを列挙しています。

Microsoft の LUIS 向けクライアント アプリケーションの例:
* [Web アプリ ボット](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-3.0): テキスト入力を介してユーザーと会話する LUIS 対応のチャット ボットをすばやく作成します。 完成度の高いボット エクスペリエンスを実現するために、[Bot Framework][bot-framework] バージョン [3.x](https://github.com/Microsoft/BotBuilder) または [4.x](https://github.com/Microsoft/botbuilder-dotnet) を使用してください。
* [Windows Mixed Reality](https://docs.microsoft.com/windows/mixed-reality/): 詳細については、LUIS を使った [Mixed Reality のコース](https://docs.microsoft.com/windows/mixed-reality/mr-azure-303)を参照してください。 

ボットで LUIS を使用するための Microsoft ツール:
* [Dispatch](https://aka.ms/dispatch-tool): ディスパッチャー モデルを使うことで、いくつかの LUIS アプリや QnA Maker アプリを親アプリから使用できます。
* [Conversation Learner](https://docs.microsoft.com/azure/cognitive-services/labs/conversation-learner/overview): LUIS を使ったボットの会話を短時間で構築できます。
* [Project Personality Chat](https://docs.microsoft.com/azure/cognitive-services/project-personality-chat/overview): ボットの短い会話を扱います。

LUIS で使用されるその他の Cognitive Services:
* [QnA Maker][qnamaker] は、多種多様なテキストを質問と回答のナレッジ ベースに統合できるようにします。
* [Bing Spell Check API](../bing-spell-check/proof-text.md) は、予測を行う前にテキストを修正します。 
* [Speech サービス](../Speech-Service/overview.md)は、話し言葉による要求をテキストに変換します。 



## <a name="next-steps"></a>次の手順

[構築済み](luis-get-started-create-app.md)または[カスタム ](luis-quickstart-intents-only.md) ドメインを使用して、新しい LUIS アプリを作成します。 公開 IoT アプリの[予測エンドポイントに対してクエリを実行](luis-get-started-cs-get-intent.md)します。

<!-- Reference-style links -->
[bot-framework]: https://docs.microsoft.com/bot-framework/
[flow]: https://docs.microsoft.com/connectors/luis/
[authoring-apis]: https://aka.ms/luis-authoring-api
[endpoint-apis]: https://aka.ms/luis-endpoint-apis
[qnamaker]: https://qnamaker.ai/