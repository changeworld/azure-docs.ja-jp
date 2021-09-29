---
title: エンティティの種類 - LUIS
description: 予測ランタイム時に、エンティティによってユーザーの発話からデータが抽出されます。 "_オプション_" の副次的な目的は、エンティティを特徴量として使用することで、意図またはその他のエンティティの予測を強化することです。
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/26/2021
ms.openlocfilehash: 7eb4affb75816789dca094f9ec6e2ae19a222285
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128582383"
---
# <a name="entities-in-luis"></a>LUIS のエンティティ

エンティティは、ユーザーの意図に関連する項目または要素です。 エンティティは、発話から抽出できるデータを定義するものであり、必要なユーザー操作を完了するために不可欠です。 次に例を示します。

|発話|予測される意図|抽出されたエンティティ|説明|
|--|--|--|--|
|Hello, how are you? (こんにちは、元気ですか。)|Greeting|-|何も抽出されません。|
|I want to order a small pizza (スモール サイズのピザを注文したい)|orderPizza| "small" | "Size" エンティティは "small" として抽出されます。|
|Turn off bedroom light (寝室の照明を消す)|turnOff| "bedroom" | "Room" エンティティは "bedroom" として抽出されます。|
|Check balance in my savings account ending in 4406 (末尾が 4406 の普通預金口座の残高を確認する)|checkBalance| "savings"、"4406" | "accountType" エンティティは "savings" として抽出され、"accountNumber" エンティティは "4406" として抽出されます。|
|ニューヨーク行きのチケットを 3 枚購入する|buyTickets| "3"、"New York" | "ticketsCount" エンティティは "3" として抽出され、"Destination" エンティティは "New York" として抽出されます。|

エンティティは省略可能ですが、強く推奨されます。 アプリ内のすべての概念に対してエンティティを作成する必要はなく、次の場合のものに対してだけです。

* クライアント アプリケーションでそのデータが必要である。または、 
* エンティティが、別のエンティティや意図のヒントまたはシグナルとして機能する。 特徴としてのエンティティの詳細については、「[特徴としてのエンティティ](#entities-as-features)」を参照してください。

## <a name="entity-types"></a>エンティティの種類

エンティティを作成するには、名前と種類を指定する必要があります。 LUIS には、いくつかの種類のエンティティがあります。 

## <a name="list-entity"></a>リスト エンティティ

リスト エンティティは、固定かつ限定された関連単語セットとそのシノニムを表します。 リスト エンティティを使用すると、複数のシノニムやバリエーションを認識し、それらの正規化された出力を抽出できます。 現在のリストに基づいて新しい単語の候補を表示するには、 *[recommend]\(推奨\)* オプションを使用します。 

リスト エンティティは機械学習ではありません。つまり、スト エンティティの追加の値は検出されません。 LUIS では、応答内のエンティティとして、任意のリスト内の項目に対してあらゆる一致がマークされます。

リスト エンティティでの照合では大文字と小文字が区別されます。抽出されるためには、完全に一致している必要があります。 リスト エンティティを照合するときは、正規化された値も使用されます。 次に例を示します。

|正規化された値|シノニム|
|--|--|
|Small|sm、sml、tiny、smallest|
|Medium|md、mdm、regular、average、middle|
|Large|lg、lrg、big|

詳細については、[リスト エンティティのリファレンス記事](reference-entity-list.md)をご覧ください。

## <a name="regex-entity"></a>正規表現エンティティ

正規表現エンティティは、指定された正規表現パターンに基づいてエンティティを抽出します。 大文字小文字とカルチャのバリアントは無視されます。 正規表現は、構造化テキスト、または特定の形式で想定される英数字値の定義済みシーケンスに最適です。 次に例を示します。

|Entity|正規表現|例|
|--|--|--|
|Flight Number (便名)|flight [A-Z]{2} [0-9]{4}| flight AS 1234|
|クレジット カード番号|[0-9]{16}|5478789865437632|

詳細については、[正規表現エンティティのリファレンス記事](reference-entity-regular-expression.md)をご覧ください。

## <a name="prebuilt-entity"></a>事前構築済みのエンティティ

LUIS では、名前、日付、数値、通貨などの一般的な種類のデータを認識するために一連の事前構築済みエンティティが提供されています。  事前構築済みエンティティの動作は固定されています。 事前構築済みエンティティのサポートは、LUIS アプリのカルチャによって異なります。 次に例を示します。

|事前構築済みのエンティティ|値の例|
|--|--|
|PersonName|James、Bill、Tom|
|DatetimeV2|2019-05-02、May 2nd、8am on may 2nd 2019|

詳細については、[事前構築済みエンティティのリファレンス記事](./luis-reference-prebuilt-entities.md)をご覧ください。

## <a name="patternany-entity"></a>Pattern.Any エンティティ

pattern.Any エンティティは、エンティティの開始位置と終了位置を示すためにパターンのテンプレート発話でのみ使用される可変長プレースホルダーです。 特定のルールまたはパターンに従っており、構文構造が固定された文に使用するのが最適です。 次に例を示します。

|発話の例|Pattern|Entity|
|--|--|--|
|Can I have a burger please? (ハンバーガーを 1 つください)|Can I have a {meal} [please][?]| burger
|Can I have a pizza? (ピザを 1 枚ください)|Can I have a {meal} [please][?]| pizza
|Where can I find The Great Gatsby? (グレート ギャツビーはどこにありますか)|Where can I find {bookName}?| The Great Gatsby|

詳細については、[Pattern.Any エンティティのリファレンス記事](./reference-entity-pattern-any.md)をご覧ください。

## <a name="machine-learned-ml-entity"></a>Machine learned (ML) エンティティ

機械学習エンティティでは、コンテキストを使用して、ラベル付けされた例に基づいてエンティティを抽出します。 これは、LUIS アプリケーションを構築するのに推奨されるエンティティです。 機械学習アルゴリズムに依存しており、アプリケーションに合わせて正しく調整するにはラベル付けが必要です。 ML エンティティを使用して、常に適切な形式とは限らないが、同じ意味を持つデータを識別します。 

|発話の例|抽出される *product* (商品) エンティティ|
|--|--|
|I want to buy a book (本を買いたい)|"book"|
|Can I get these shoes please? (この靴をいただけますか)|"shoes"|
|Add those shorts to my basket. (このパンツをかごに追加します)|"shorts"|

機械学習エンティティの詳細については、[こちら](./reference-entity-machine-learned-entity.md)を参照してください。

詳細については、[機械学習エンティティのリファレンス記事](./reference-entity-machine-learned-entity.md)をご覧ください。

#### <a name="ml-entity-with-structure"></a>構造がある ML エンティティ

1 つの ML エンティティは、それぞれが独自のプロパティを持つ、より小さいサブエンティティで構成することができます。 たとえば、*Address* は次の構造を持つ場合があります。

* Address : 4567 Main Street, NY, 98052, USA
    * Building Number: 4567
    * Street Name: Main Street
    * State: NY
    * Zip Code: 98052
    * Country: USA


## <a name="building-effective-ml-entities"></a>有効な ML エンティティの構築

機械学習エンティティを効果的に構築するには、次のベスト プラクティスに従ってください。

* サブエンティティを持つ機械学習エンティティがある場合は、必ず、エンティティとサブエンティティの異なる順序とバリアントがラベル付けされた発話で提示されるようにしてください。 ラベル付けされた発話の例には、有効なすべてのフォームと、発話内に表示され、存在しない、並べ替えられているエンティティが含まれている必要があります。

* エンティティを非常に固定されたセットにオーバーフィットすることは避けてください。 オーバーフィットは、モデルが適切に一般化されていない場合に発生します。機械学習モデルでは、一般的な問題です。 これは、新しい種類の例に対しては、アプリが適切に動作しないであろうことを意味します。 徐々に、提供した限定的な例を超えてアプリが一般化できるように、ラベル付けされた発話例を変化させる必要があります。

* ラベル付けは、意図全体で一貫している必要があります。 これには、このエンティティを含む *None* 意図で提供する発話も含まれます。 そうでないと、モデルでシーケンスを効果的に特定できなくなります。

## <a name="entities-as-features"></a>特徴としてのエンティティ

エンティティは、別の意図やエンティティの特徴または特徴的な特性として使用されて、システムがそれを通じて監視したり学習したりできるようにすることを、そのもう 1 つの重要な機能としています。

## <a name="entities-as-features-for-intents"></a>意図の特徴としてのエンティティ

エンティティは、意図のシグナルとして使用できます。 たとえば、特定のエンティティが発話内にある場合は、それがどの意図に分類されるかが分かります。

|発話の例|Entity|Intent|
|--|--|--|
|Book me a *fight to New York*. (ニューヨーク行きの便を予約してください)|City|Book Flight (飛行機を予約する)|
|Book me the *main conference room*. (大会議室を予約してください)|ルーム|Reserve Room (部屋を予約する)|

## <a name="entities-as-feature-for-entities"></a>エンティティの特徴としてのエンティティ

エンティティを他のエンティティの存在を示すインジケーターとして使用することもできます。 これの一般的な例では、事前構築済みエンティティを別の ML エンティティの特徴として使用しています。
フライト予約システムを構築している場合、"Book me a flight from Cairo to Seattle" (カイロからシアトル行きのフライトを予約してください) のような発話には、ML エンティティとして *Origin City* (出発地) と *Destination City* (目的地) があります。 事前構築済みの `GeographyV2` エンティティを両方のエンティティの特徴として使用することをお勧めします。

詳細については、[GeographyV2 エンティティのリファレンス記事](./luis-reference-prebuilt-geographyv2.md)を参照してください。

エンティティを、他のエンティティでの必須の特徴として使用することもできます。 これは、抽出されたエンティティの解決に役立ちます。 たとえば、ピザの注文アプリケーションを作成していて、`Size` という ML エンティティがある場合は、`SizeList` リスト エンティティを作成し、それを `Size` エンティティの必須の特徴として使用できます。 アプリケーションからは、発話から抽出されたエンティティとして正規化された値が返されます。 

詳細については、[特徴](luis-concept-feature.md)に関する記事を参照してください。また、実際のカルチャで使用できる事前構築済みエンティティの解決については、[事前構築済みエンティティ](./luis-reference-prebuilt-entities.md)に関する記事を参照してください。 


## <a name="entity-prediction-status-and-errors"></a>エンティティ予測の状態とエラー

発話例でラベル付けしたエンティティと異なるエンティティ予測がエンティティに含まれている場合は、LUIS ポータルに次のように表示されます。 この異なるスコアは、現在のトレーニング済みのモデルに基づいています。 

:::image type="content" source="./media/luis-concept-entities/portal-entity-prediction-error.png" alt-text="発話例で選択したエンティティと異なるエンティティ予測がエンティティに含まれている場合は、LUIS ポータルに表示されます":::

エラーの原因となったテキストが、発話例内で強調表示されています。また、発話例の行の右には、赤い三角形で示されたエラー インジケーターがあります。 

エンティティ エラーを解決するには、次のうち 1 つ以上を試してください。

* 強調表示されているテキストではラベルが間違っています。 修正するには、ラベルを確認して訂正し、アプリを再トレーニングします。 
* エンティティの概念の識別に役立つように、エンティティの[特徴](luis-concept-feature.md)を作成します。
* [発話例](luis-concept-utterance.md)を追加し、エンティティでラベル付けします。
* エンティティの概念を識別するのに役立つ、予測エンドポイントで受信したすべての発話に対する[アクティブ ラーニングの提案](luis-concept-review-endpoint-utterances.md)を確認します。


## <a name="next-steps"></a>次のステップ

* 適切な[発話](luis-concept-utterance.md)例について説明します。
* [エンティティの追加](luis-how-to-add-entities.md)に関するページで、LUIS アプリにエンティティを追加する方法の詳細を確認します。
* LUIS [アプリケーションの制限](./luis-limits.md)について説明します。 
* [チュートリアル](tutorial-machine-learned-entity.md)を使用して、機械学習エンティティを使って発話から構造化データを抽出する方法を学習します。
