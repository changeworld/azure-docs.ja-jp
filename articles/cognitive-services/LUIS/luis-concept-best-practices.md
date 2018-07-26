---
title: LUIS のベスト プラクティスについて - Azure | Microsoft Docs
description: 最良の結果を得るための LUIS のベスト プラクティスについて説明します。
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/08/2018
ms.author: diberry
ms.openlocfilehash: 81a2523f37cac926d9f498953a831c2d877a7528
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2018
ms.locfileid: "39224009"
---
# <a name="best-practices"></a>ベスト プラクティス
アプリの作成プロセスを使用して、ご自身の LUIS アプリをビルドします。 

* 言語モデルを構築する
* 発話のトレーニング例をいくつか追加する (意図あたり 10 ～ 15 個)
* [発行] 
* エンドポイントからテストする 
* 機能を追加する

ご自身のアプリが[公開](luis-how-to-publish-app.md)されたら、機能を追加する、公開する、エンドポイントからテストする、という作成サイクルを使用します。 発話の例を追加することによって、次の作成サイクルを開始しないでください。 そうすると、実際のユーザーの発話を使用して LUIS にモデルを学習させることができなくなります。 

LUIS による学習効率を高めるには、現在の発話の例とエンドポイント発話の両方が、高い予測スコアを確実に返すまでは、発話を増やさないようにします。 スコアを上げるには、アクティブ ラーニング、[パターン](luis-concept-patterns.md)、および[フレーズ リスト](luis-concept-feature.md)を使用します。 

## <a name="do-and-dont"></a>すべきことと、やってはいけないこと
LUIS アプリのベスト プラクティスを次に示します。

|すべきこと|やってはいけないこと|
|--|--|
|[意図を個別に定義する](#do-define-distinct-intents) |[多数の発話の例を意図に追加する](#dont-add-many-example-utterances-to-intents) |
|[意図の汎用性と専用性のスイート スポットを見つける](#do-find-sweet-spot-for-intents)|[LUIS をトレーニング プラットフォームとして使用する](#dont-use-luis-as-a-training-platform)|
|[ご自身のアプリを反復的にビルドする](#do-build-the-app-iteratively)|[同じ形式の発話の例を多数追加して、他の形式を無視する](#dont-add-many-example-utterances-of-the-same-format-ignoring-other-formats)|
|[後のイテレーションでフレーズ リストとパターンを追加する](#do-add-phrase-lists-and-patterns-in-later-iterations)|[意図とエンティティの定義を一緒にする](#dont-mix-the-definition-of-intents-and-entities)|
|[発話の例を None 意図に追加する](#do-add-example-utterances-to-none-intent)|[指定できるすべての値でフレーズ リストを作成する](#dont-create-phrase-lists-with-all-the-possible-values)|
|[アクティブ ラーニングの提案機能を活用する](#do-leverage-the-suggest-feature-for-active-learning)|[多数のパターンを追加する](#dont-add-many-patterns)|
|[ご自身のアプリのパフォーマンスを監視する](#do-monitor-the-performance-of-your-app)|[追加されたすべての発話の例でトレーニングして公開する](#dont-train-and-publish-with-every-single-example-utterance)|

## <a name="do-define-distinct-intents"></a>すべきこと: 意図を個別に定義する
各意図のボキャブラリがその意図のみを対象としていること、また、別の意図と重複していないことを確認します。 たとえば、飛行機のフライトやホテルなど、旅行の手配を処理するアプリが必要な場合は、これらを、それぞれ個別の意図として定義するか、発話内に特定のデータのエンティティを含む 1 つの意図として定義するかを選択できます。

2 つの意図のボキャブラリが同じ場合は、意図を結合し、エンティティを使用します。 

たとえば、次の発話の例について考えます。

```
Book a flight
Book a hotel
```

この "Book a flight" と "Book a hotel" で使用されている共通のボキャブラリが "book a " です。 これは重複しているため、フライトやホテルという抽出されたエンティティが異なる単語を含む 1 つの意図として定義する必要があります。 

## <a name="do-find-sweet-spot-for-intents"></a>すべきこと: 意図のスイート スポットを見つける
LUIS の予測データを使用して、意図が重複していないかどうかを判断します。 意図が重複していると LUIS が混乱します。 その結果、上位スコアの意図と他の意図が非常に近くなります。 LUIS では、トレーニング用のデータから毎回同じパスを使用するわけではないため、重複している意図は、トレーニングで 1 位または 2 位になる可能性があります。 各意図の発話のスコアは、このようなことが発生しないように、それぞれ差をつける必要があります。 意図が適切に区別されていれば、最上位の意図は毎回最上位にくるはずです。 
 
## <a name="do-build-the-app-iteratively"></a>すべきこと: ご自身のアプリを反復的にビルドする
[発話の例](luis-concept-utterance.md)またはエンドポイント発話として使用されていない "*ブラインド*" テスト セットを個別に保持します。 ご自身のテスト セット用のアプリは改善し続けてください。 実際のユーザーの発話が反映されるようにテスト セットを調整します。 このブラインド テスト セットを使用して、各イテレーションを評価します。 

開発者には 3 つのデータ セットが必要です。 1 つ目は、モデルを構築するための発話の例です。 2 つ目は、エンドポイントでモデルをテストするためのデータ セットです。 3 つ目は、[バッチ テスト](luis-how-to-batch-test.md)で使われているブラインド テスト データです。 この最後のセットは、エンドポイントでの送信やアプリケーションのトレーニングには使用されません。  

## <a name="do-add-phrase-lists-and-patterns-in-later-iterations"></a>すべきこと: 後のイテレーションでフレーズ リストとパターンを追加する
[フレーズ リスト](luis-concept-feature.md)を使用すると、お使いのアプリ ドメインに関連する単語のディクショナリを定義できます。 いくつかの単語を使ってフレーズ リストをシード処理し、提案機能を使用することで、LUIS によって、ボキャブラリの単語がさらに詳しく認識されるようになります。 フレーズ リストは完全一致ではないため、ボキャブラリにはすべての単語は追加しないでください。 

エンドポイントからの実際のユーザーの発話で、非常によく似ているものにより、単語の選択と配置のパターンが明らかになる可能性があります。 [パターン](luis-concept-patterns.md)機能では、この単語の選択と配置および正規表現を使って、ご自身の予測精度を向上させます。 パターン内の正規表現を使用すると、単語や句読点を無視して、パターンを一致させることができます。 

句読点を無視するには、パターンの省略可能な句読点の構文を使用します。

これらのプラクティスは、エンドポイントの要求がご自身のアプリに届く前には適用しないでください。適用すると、信頼度にずれが生じます。  

## <a name="do-add-example-utterances-to-none-intent"></a>すべきこと: 発話の例を None 意図に追加する
これは代替用の意図で、お使いのアプリケーション外のものすべてを示していました。 ご自身の残りの LUIS アプリでは、発話の例 10 個それぞれに 1 つの発話の例を None 意図に追加してください。

## <a name="do-leverage-the-suggest-feature-for-active-learning"></a>すべきこと: アクティブ ラーニングの提案機能を活用する
発話の例をさらに多く意図に追加するのではなく、[アクティブ ラーニング](luis-how-to-review-endoint-utt.md)の**エンドポイントの発話の確認**を定期的に使用します。 アプリでは常にエンドポイント発話を受信しているため、このリストは拡大および変化し続けています。

## <a name="do-monitor-the-performance-of-your-app"></a>すべきこと: ご自身のアプリのパフォーマンスを監視する
テスト セットを使用して予測精度を監視します。 

## <a name="dont-add-many-example-utterances-to-intents"></a>やってはいけないこと: 多数の発話の例を意図に追加する
アプリの公開後は、反復的なプロセスでアクティブ ラーニングから発話を追加するだけにします。 発話が似ている場合は、パターンを追加します。 

## <a name="dont-use-luis-as-a-training-platform"></a>やってはいけないこと: LUIS をトレーニング プラットフォームとして使用する
LUIS は、言語モデルのドメインに固有です。 一般的なトレーニング プラットフォームとして動作するようには作られていません。 

## <a name="dont-add-many-example-utterances-of-the-same-format-ignoring-other-formats"></a>やってはいけないこと: 同じ形式の発話の例を多数追加して、他の形式を無視する
LUIS では、意図の発話にバリエーションが必要です。 発話は全体的に意味が同じでも、さまざまな形があります。 バリエーションには、発話の長さ、単語の選択、単語の配置などが含まれます。 

|同じ形式を使用しない|さまざまな形式を使用する|
|--|--|
|シアトルまでのチケットを購入する<br>パリまでのチケットを購入する<br>オーランドまでのチケットを購入する|シアトル行きのチケットを 1 枚購入する<br>パリまでの夜行便を、次の月曜日に二席予約する<br>春休みの間にオーランドへのチケットを 3 枚確保したいのですが|

2 番目の列では、それぞれ異なる動詞 (購入、予約、確保)、数量 (1、二、3)、語順が使用されていますが、旅行のために航空券を購入するという意図はすべて同じです。 

## <a name="dont-mix-the-definition-of-intents-and-entities"></a>やってはいけないこと: 意図とエンティティの定義を一緒にする
お使いのボットで取得されるすべてのアクションに対して意図を作成します。 そのアクションを可能にするためのエンティティをパラメーターとして使用します。 

飛行機のフライトを予約するチャットボットについては、**BookFlight** 意図を作成します。 すべての航空会社、またはすべての目的地に対して、意図を作成しないでください。 これらのデータ部分を[エンティティ](luis-concept-entity-types.md)として使用し、発話の例でマークします。 

## <a name="dont-create-phrase-lists-with-all-the-possible-values"></a>やってはいけないこと: 指定できるすべての値でフレーズ リストを作成する
[フレーズ リスト](luis-concept-feature.md)には数個の例を提供します。すべての単語ではありません。 LUIS で汎用化が行われ、コンテキストが考慮されます。 

## <a name="dont-add-many-patterns"></a>やってはいけないこと: 多数のパターンを追加する
追加する[パターン](luis-concept-patterns.md)は、多くなりすぎないようにします。 LUIS は、少ない例をすばやく学習するように作られています。 不必要にシステムに負荷をかけないでください。

## <a name="dont-train-and-publish-with-every-single-example-utterance"></a>やってはいけないこと: すべての発話の例でトレーニングして公開する
10 ～ 15 個の発話を追加したら、トレーニングして公開します。 これにより、予測精度への影響を確認することができます。 1 つの発話を追加しても、スコアに目に見える影響は出ない可能性があります。 

## <a name="next-steps"></a>次の手順

* お使いの LUIS アプリで[ご自身のアプリを計画](luis-how-plan-your-app.md)する方法を確認します。