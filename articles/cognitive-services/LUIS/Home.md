---
title: Azure の Language Understanding (LUIS) について | Microsoft Docs
description: Language Understanding (LUIS) を使用して、機械学習のパワーをアプリケーションに取り入れる方法について説明します。
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/22/2017
ms.author: diberry
ms.openlocfilehash: 072176347adacbabc0a92f1c7e437f8233531003
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2018
ms.locfileid: "39225460"
---
# <a name="what-is-language-understanding-luis"></a>Language Understanding (LUIS) とは
Language Understanding (LUIS) は、カスタム機械学習をユーザーの自然言語での会話に適用して、全体の意味を予測し、関連性のある詳細な情報を引き出すクラウド ベースのサービスです。 

LUIS のクライアント アプリケーションは、ユーザーと自然言語でコミュニケーションを行ってタスクを完了する会話型アプリケーションであれば、どれでもかまいません。 クライアント アプリケーションの例として、ソーシャル メディア アプリ、チャットボット、音声対応デスクトップ アプリケーションがあります。  

![LUIS に情報を供給する 3 つのアプリケーションの概念図](./media/luis-overview/luis-entry-point.png)

## <a name="what-is-a-luis-app"></a>LUIS アプリとは何か
LUIS アプリには、自分で設計した特定の領域 (ドメイン) に固有の自然言語モデルが含まれます。 LUIS アプリは、構築済みのドメイン モデル、独自に構築したモデル、または構築済みのドメインの一部と独自の情報と一体化させたモデルで開始できます。

[構築済みのドメイン モデル](luis-how-to-use-prebuilt-domains.md)には、必要なものがすべて含まれているため、LUIS の使用をすぐに開始できる優れた方法です。

LUIS アプリには、統合設定、[コラボレーター](luis-concept-collaborator.md)、および[バージョン](luis-concept-version.md)も含まれます。

## <a name="using-a-luis-app"></a>LUIS アプリの使用
<a name="Accessing-LUIS"></a>LUIS アプリが発行されたら、クライアント アプリケーションは、LUIS [エンドポイント API][endpoint-apis] に発話を送信し、予測結果を JSON 応答として受信します。

次の図では、最初に、何をしたいかをユーザーが自然言語で表現した発話のテキストがチャットボットによって HTTP 要求の形で LUIS に送信されます (1)。 次に、LUIS が、学習済みのモデルを自然言語に適用して、ユーザーの入力を意味のあるものにし、JSON (JavaScript Object Notation) 形式の応答を返します (2)。 その後、クライアント チャットボットが、JSON 応答を使用して、ユーザーの要求に対応します (3)。 

![チャットボットと連携する LUIS の概念図](./media/luis-overview/luis-overview-process-2.png)

### <a name="example-of-json-endpoint-response"></a>JSON エンドポイント応答の例

JSON エンドポイント応答には、少なくとも、クエリの発話と上位スコアの意図が含まれます。 

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

<a name="Key-LUIS-concepts"></a>
<a name="what-is-a-luis-model"></a>
## <a name="what-is-a-natural-language-model"></a>自然言語モデルとは何か
モデルは、"航空券を予約する" や "ヘルプデスクに問い合わせる" などの一般的なユーザーの目的 ("_意図_" と呼ばれます) の一覧から始まります。 この意図に対応するユーザーのサンプル テキスト ("_発話例_" と呼ばれます) を用意します。 次に、発話内の重要な単語またはフレーズ ("_エンティティ_" と呼ばれます) をマークします。


モデルには、以下が含まれます。

* **[意図](#intents)**: ユーザーの目的のカテゴリ (目的のアクションや結果)
* **[エンティティ](#entities)**: 数字、電子メール、名前などの発話内の特定の種類のデータ
* **[発話例](#example-utterances)**: ユーザーがクライアント アプリケーションに入力するテキストの例

### <a name="intents"></a>意図 
[intention](luis-how-to-add-intents.md) の短縮形である _intent_ (意図) は、航空券の予約、請求書の支払い、ニュース記事の検索などの、ユーザーの発話によって伝達される目的や目標です。 各アクションの意図を作成します。 LUIS 旅行アプリでは、"BookFlight" という名前の意図を定義できます。 クライアント アプリケーションでは、上位スコアの意図を使用して、アクションをトリガーできます。 たとえば、LUIS から "BookFlight" という意図が返された場合、クライアント アプリケーションは、航空券を予約する外部サービスへの API 呼び出しをトリガーできます。

### <a name="entities"></a>エンティティ
[エンティティ](luis-how-to-add-entities.md)は、発話内で検出されたユーザーの要求に関連する詳細な情報を表します。 たとえば、"パリ行きのチケットを 1 枚予約して" という発話では、要求しているのは 1 枚のチケットであり、場所は "パリ" であることを指示しています。 1 枚のチケットを示す "チケットを 1 枚" と目的地を示す "パリ" という 2 つのエンティティが検出されます。 

LUIS によってユーザーの発話内で検出されたエンティティが返された後、クライアント アプリケーションは、エンティティの一覧を、アクションをトリガーするためのパラメーターとして使用できます。 たとえば、航空券の予約には、行先、日付、航空会社などのエンティティが必要です。

LUIS には、エンティティを識別して分類するさまざまな方法が用意されています。

* **構築済みのエンティティ**: LUIS には、意図、発話、および[構築済みのエンティティ](luis-prebuilt-entities.md)を含む構築済みのドメイン モデルが多数あります。 構築済みのモデルの意図と発話を使用せずに、構築済みのエンティティを使用できます。 構築済みのエンティティによって時間を節約できます。

* **カスタム エンティティ**: LUIS では、さまざまな方法で、独自のカスタム [エンティティ](luis-concept-entity-types.md) (機械学習によるエンティティ、具体的で逐語的なエンティティ、および機械学習によるエンティティと逐語的なエンティティの組み合わせなど) を識別する方法が用意されています。

### <a name="example-utterances"></a>発話の例
[発話](luis-how-to-add-example-utterances.md)例は、クライアント アプリケーションが理解する必要があるユーザーからのテキスト入力です。 "パリ行きのチケットを予約して" のような文の場合も、"予約" や "パリ便" のような文の一部の場合もあります。 発話は、常に整っているわけではなく、特定の意図を表す発話のバリエーションは多数あります。 意図ごとに 10 から 20 の発話例を追加し、すべての発話内のエンティティをマークします。

|ユーザーの発話例|意図|エンティティ|
|-----------|-----------|-----------|
|"Book a flight to __Seattle__?" (シアトル行きの便を予約できる?)|BookFlight|シアトル|
|"When does your store __open__?" (お店は何時に開店しますか?)|StoreHoursAndLocation|open (開店)|
|"Schedule a meeting at __1pm__ with __Bob__ in Distribution" (ディストリビューションのボブと午後 1 時にミーティング)|ScheduleMeeting|1pm、Bob (午後 1 時、Bob)|

## <a name="improve-prediction-accuracy"></a>予測精度の改善
LUIS アプリが発行され、ユーザーの実際の発話を受信した後、LUIS はさまざまな方法で予測精度を改善できます ([アクティブ ラーニング](#active-learning)ドメインの単語を含む[フレーズ リスト](#phrase-lists)、および必要な発話の数を減少させる[パターン](#patterns))。

### <a name="active-learning"></a>アクティブ ラーニング
[アクティブ ラーニング](luis-how-to-review-endoint-utt.md) プロセスでは、レビューを行うエンドポイントで LUIS アプリが受け取った発話を選択することで、現実の世界の発話にアプリを順応させることができます。 エンドポイントでの予測の承認、修正、再トレーニング、および再発行を実行できます。 この反復処理によって、LUIS の学習は、最小限の時間と労力ですばやく実行されます。 

### <a name="phrase-lists"></a>フレーズ リスト 
LUIS では、重要な単語またはフレーズをドメイン モデルに指示できるように、[フレーズ リスト](luis-concept-feature.md)が用意されます。 LUIS は、これらのリストを使用して、それ以外の方法ではモデル内で検出されない単語またはフレーズに意味を追加します。

### <a name="patterns"></a>パターン 
パターンを使用して、単語の選択と単語の順序を含む共通[テンプレート](luis-concept-patterns.md)に意図の発話を集めることを単純化できます。 これにより、必要な意図の発話例が少なくなることで、LUIS の高速での学習が可能になります。 パターンは、正規表現と機械学習による表現のハイブリッド システムです。 

<a name="using-luis"></a>

## <a name="authoring-and-accessing-luis"></a>LUIS のオーサリングとアクセス
LUIS アプリのビルドは、LUIS Web サイトから行うか、[オーサリング](https://aka.ms/luis-authoring-apis) API を使用してプログラムで行います。または、オーサリングの必要に応じて、両方を使用します。 発行された LUIS アプリへのアクセスは、クエリ [エンドポイント](https://aka.ms/luis-endpoint-apis)によって行ないます。 

LUIS Web サイトは、オーサリング リージョンに応じて、世界中で 3 つ用意されています。 オーサリング リージョンは、LUIS アプリを発行できる Azure リージョンを決定します。
<!--
|Authoring region|Publishing region(s)|
|--|--|
|[www.luis.ai](https://www.luis.ai)|**U.S.**<br>West US<br>West US 2<br>East US<br>East US 2<br>South Central US<br>West Central US<br><br>**Asia**<br>Southeast Asia<br>East Asia<br><br>**South America**<br>Brazil South |
|[au.luis.ai](https://au.luis.ai)|Australia East|
|[eu.luis.ai](https://eu.luis.ai)|West Europe<br>North Europe|
-->

オーサリング リージョンと発行リージョンの詳細については、[こちら](luis-reference-regions.md)をご覧ください。

## <a name="what-technologies-work-with-luis"></a>LUIS と連携するテクノロジ
Microsoft のさまざまなテクノロジが LUIS と連携しています。

* [Bing Spell Check API](../bing-spell-check/proof-text.md) は、予測を行う前にテキストを修正します。 
* [Bot Framework][bot-framework] は、チャットボットがテキスト入力を介してユーザーと対話できるようにします。 ボット エクスペリエンスを完了するには、[3.x](https://github.com/Microsoft/BotBuilder) または [4.x](https://github.com/Microsoft/botbuilder-dotnet) SDK を選択します。
* [QnA Maker][qnamaker] は、多種多様なテキストを質問と回答のナレッジ ベースに統合できるようにします。
* [Speech](../Speech/home.md) は、話し言葉による要求をテキストに変換します。 要求は、テキストに変換された後、LUIS によって処理されます。 詳細については、[Speech SDK](https://aka.ms/csspeech) を参照してください。
* [Text Analytics](../text-analytics/overview.md) は、感情分析とキー フレーズ データ抽出を行います。

## <a name="next-steps"></a>次の手順
[構築済み](luis-get-started-create-app.md)または[カスタム ](luis-quickstart-intents-only.md) ドメインを使用して、新しい LUIS アプリを作成します。

<!-- Reference-style links -->
[bot-framework]: https://docs.microsoft.com/bot-framework/
[flow]: https://docs.microsoft.com/connectors/luis/
[authoring-apis]: https://aka.ms/luis-authoring-api
[endpoint-apis]: https://aka.ms/luis-endpoint-apis
[qnamaker]: https://qnamaker.ai/