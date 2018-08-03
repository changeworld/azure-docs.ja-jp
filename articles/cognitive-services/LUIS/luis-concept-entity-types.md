---
title: Azure での LUIS アプリのエンティティの種類について | Microsoft Docs
description: Language Understanding Intelligent Service (LUIS) アプリにエンティティ (アプリケーションのドメイン内のキー データ) を追加します。
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/28/2018
ms.author: diberry
ms.openlocfilehash: b0b5852a223a77e33a288bb8061c1ce374018ec1
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/27/2018
ms.locfileid: "39282281"
---
# <a name="entities-in-luis"></a>LUIS のエンティティ

エンティティとは、発話に含まれる単語またはフレーズのことで、アプリケーションのドメイン内のキー データです。

## <a name="entity-compared-to-intent"></a>エンティティと意図の比較
エンティティは、抽出しようとしている発話内の単語またはフレーズを表します。 発話には、多数のエンティティを含めることも、まったく含めないこともできます。 エンティティは、類似したオブジェクトのコレクション (場所、もの、人、イベント、または概念) を含むクラスを表します。 エンティティは、意図に関連する情報を説明し、アプリがタスクを実行するうえで不可欠なこともあります。 たとえば、ニュース検索アプリには、"トピック"、"ソース"、"キーワード"、"公開日" などのエンティティが含まれることがありますが、これらはニュースを検索する際のキー データです。 旅行予約アプリでは、"場所"、"日付"、"航空会社"、"旅行クラス"、および "チケット" が、航空券の予約のキー情報です ("航空券予約" という意図に関連して)。

一方、意図は、発話全体の予測を表します。 

## <a name="entities-represent-data"></a>エンティティはデータを表す
エンティティは、発話から引き出す必要があるデータです。 名前、日付、製品名、任意の単語のグループを指定できます。 

|発話|エンティティ|データ|
|--|--|--|
|ニューヨーク行きのチケットを 3 枚購入する|事前構築済みの数値<br>場所.目的地|3<br>ニューヨーク|
|3 月 5 日のニューヨーク発ロンドン行きのチケットを購入する|場所.出発地<br>場所.目的地<br>事前構築済みの datetimeV2|ニューヨーク<br>ロンドン<br>2018 年 3 月 5 日|

## <a name="entities-are-optional-but-highly-recommended"></a>エンティティは省略可能だが強く推奨される
意図は必須ですが、エンティティは省略可能です。 アプリ内のすべての概念を表すエンティティを作成する必要はありません。アプリがアクションを実行するために必須のエンティティのみ作成します。 

ボットが継続するために必要な詳細が発話に含まれない場合は、エンティティを追加する必要はありません。 アプリが成熟するに従い、後で追加することもできます。 

情報の使用方法が不明の場合は、datetimeV2、序数、電子メール アドレス、電話番号などの一般的な事前構築済みのエンティティを追加します。

## <a name="label-for-word-meaning"></a>単語の意味のラベル
単語の選択や配置は同じなのに同じものを意味しない場合は、そのエンティティでラベル付けしません。 

次の発話で、単語 `fair` は、同形異義語です。 スペルは同じですが、意味が異なります。

```
What kind of county fairs are happening in the Seattle area this summer?
Is the current rating for the Seattle review fair?
```

イベント エンティティですべてのイベント データを検索する場合は、2 つ目ではなく最初の発話の単語 `fair` にラベルを付けます。

## <a name="entities-are-shared-across-intents"></a>エンティティは意図全体で共有される
エンティティは、意図全体で共有されます。 これらは 1 つの意図に属しません。 意図とエンティティは、意味的に関連付けることができますが、排他的な関係ではありません。

発話 "パリへのチケットを予約する" の "パリ" は、場所の種類のエンティティです。 LUIS では、ユーザーの入力で言及されるエンティティを認識することで、意図を実現するために実行する特定のアクションを選択できるようにします。

## <a name="assign-entities-in-none-intent"></a>None 意図にエンティティを割り当てる
**None** 意図を含むすべての意図で、エンティティにラベルを付ける必要があります。 これによって、LUIS では、エンティティが発話内のどこにあるかと、どの単語がエンティティの周囲にあるかについて学習できます。 

## <a name="types-of-entities"></a>エンティティの種類
LUIS には、事前構築済みのエンティティ、機械学習済みのカスタム エンティティ、リスト エンティティなど、さまざまな種類のエンティティが用意されています。

| Name | ラベル付けできる | 説明 |
| -- |--|--|
| **事前構築済み** <br/>[カスタム](#prebuilt)| |  **定義**<br>一般的な概念を表す組み込みの種類です。 <br><br>**リスト**<br/>キー フレーズの数、序数、温度、寸法、お金、年齢、パーセンテージ、電子メール アドレス、URL、電話番号、およびキー フレーズです。 <br><br>事前構築済みのエンティティ名は予約されています。 <br><br>アプリケーションに追加されるすべての事前構築済みエンティティが、[エンドポイント](luis-glossary.md#endpoint) クエリに返されます。 詳細については、「[事前構築済みのエンティティ](./luis-prebuilt-entities.md)」を参照してください。 <br/><br/>[エンティティの応答例](luis-concept-data-extraction.md#prebuilt-entity-data)|
|<!-- added week of 3/21/08 --> **正規表現**<br/>[正規表現](#regex)||**定義**<br>書式設定された生の発話テキストのカスタム正規表現。 大文字小文字とカルチャのバリアントは無視されます。  <br><br>このエンティティは、バリエーションにも一貫性のある、一貫して書式設定されている単語またはフレーズに適しています。<br><br>正規表現の照合は、スペルチェックによる変更後に適用されます。 <br><br>角かっこが多用されているなど、正規表現が複雑すぎる場合は、このモデルに式を追加できません。 <br><br>**例**<br>`kb[0-9]{6,}` は kb123456 と一致します。<br/><br/>[クイックスタート](luis-quickstart-intents-regex-entity.md)<br>[エンティティの応答例](luis-concept-data-extraction.md)|
| **シンプル** <br/>[機械学習済み](#machine-learned) | ✔ | **定義**<br>シンプルなエンティティとは、1 つの概念について説明し、機械学習コンテキストから学習した汎用的エンティティです。 コンテキストには、単語の選択、単語の配置、および発話の長さが含まれます。<br/><br/>これは、形式に一貫性がないが同じものを示す単語またはフレーズに適したエンティティです。 <br/><br/>[クイックスタート](luis-quickstart-primary-and-secondary-data.md)<br/>[エンティティの応答例](luis-concept-data-extraction.md#simple-entity-data)|  
| **リスト** <br/>[完全一致](#exact-match)|| **定義**<br>リスト エンティティは、システム内の、固定かつ限定された関連単語セットとそのシノニムを表します。 <br><br>各リスト エンティティでは、1 つ以上の形式を持つことができます。 同じ概念を表現する方法のバリエーションに関する既知のセットとして使用されます。<br/><br/>LUIS では、リスト エンティティの追加の値は検出されません。 現在のリストに基づいて新しい単語の候補を表示するには、**[Recommend] (推奨)** 機能を使用します。<br/><br>同じ値を持つリスト エンティティが複数存在する場合は、エンドポイント クエリに各エンティティが返されます。 <br/><br/>[クイックスタート](luis-quickstart-intent-and-list-entity.md)<br>[エンティティの応答例](luis-concept-data-extraction.md#list-entity-data)| 
| **Pattern.any** <br/>[混合](#mixed) | ✔|**定義**<br>patterns.any は、エンティティの開始位置と終了位置を示すためにパターンのテンプレート発話でのみ使用される、可変長プレース ホルダーです。  <br><br>**例**<br>タイトルに基づく書籍の発話検索では、pattern.any によって完全なタイトルが抽出されます。 pattern.any を使用するテンプレート発話は `Who wrote {BookTitle}[?]` です。<br/><br/>[チュートリアル](luis-tutorial-pattern.md)<br>[エンティティの応答例](luis-concept-data-extraction.md#composite-entity-data)|  
| **複合** <br/>[機械学習済み](#machine-learned) | ✔|**定義**<br>複合エンティティは、事前構築済みエンティティ、シンプル エンティティ、RegEx エンティティ、リスト エンティティ、階層構造エンティティなどの他のエンティティで構成されます。 個別のエンティティが、エンティティ全体を形成します。 <br><br>**例**<br>PlaneTicketOrder という名前の複合エンティティは、事前構築済みの `number` と `ToLocation` という子エンティティを持つことができます。 <br/><br/>[チュートリアル](luis-tutorial-composite-entity.md)<br>[エンティティの応答例](luis-concept-data-extraction.md#composite-entity-data)|  
| **階層構造** <br/>[機械学習済み](#machine-learned) |✔ | **定義**<br>階層構造エンティティは、コンテキストから学習されたシンプル エンティティのカテゴリです。<br><br>**例**<br>子 `ToLocation` および `FromLocation` を持つ階層構造エンティティ `Location` では、それぞれの子は、発話内の**コンテキスト**を基に決定できます。 発話 `Book 2 tickets from Seattle to New York` の `ToLocation` および `FromLocation` は、その周囲の単語に基づく前後関係によって異なります。 <br/><br/>**次の場合は使用しない**<br>コンテキストに関係なく、子にテキストが完全一致するエンティティが必要な場合は、リスト エンティティを使用してください。 他のエンティティの種類との親子関係が必要な場合は、複合エンティティを使用する必要があります。<br/><br/>[クイックスタート](luis-quickstart-intent-and-hier-entity.md)<br>[エンティティの応答例](luis-concept-data-extraction.md#hierarchical-entity-data)|

<a name="prebuilt"></a>
**事前構築済み**のエンティティは、LUIS によって提供されるカスタム エンティティです。 これらのエンティティの一部は、オープンソースの [Recognizers-Text](https://github.com/Microsoft/Recognizers-Text) プロジェクトで定義されます。 サポートされるカルチャの /Specs ディレクトリに、多数の[例](https://github.com/Microsoft/Recognizers-Text/tree/master/Specs)があります。 ご自分の特定のカルチャまたはエンティティが現在サポートされていない場合は、プロジェクトにご協力ください。 

<a name="machine-learned"></a>
**機械学習**エンティティは、[エンドポイント クエリ](luis-concept-test.md#endpoint-testing)を使用してテストし、[エンドポイントの発話の確認](luis-how-to-review-endoint-utt.md)を行うと、最適に動作します。 

<a name="regex"></a>
**正規表現エンティティ**は、エンティティ定義の一環としてユーザーが提供する正規表現によって定義されます。 

<a name="exact-match"></a>
**完全一致**エンティティでは、エンティティ内で指定されたテキストを使用して、完全一致のテキスト照合を行います。

<a name="mixed"></a>
**混合**エンティティでは、エンティティの検出方法の組み合わせを使用します。

## <a name="entity-limits"></a>エンティティの制限
[制限](luis-boundaries.md#model-boundaries)を確認して、モデルに追加できるエンティティの種類ごとの数を把握してください。

## <a name="entity-roles"></a>エンティティのロール
エンティティの[ロール](luis-concept-roles.md)は、パターンでのみ使用されます。 

## <a name="composite-vs-hierarchical-entities"></a>複合エンティティと階層構造エンティティの比較
複合エンティティと階層構造エンティティは、どちらも親子関係を持ち、機械学習されます。 機械学習により、LUIS では、異なるコンテキスト (単語の配置) に基づいてエンティティを理解できます。 複合エンティティでは、子としてさまざまな種類のエンティティを使用できるため、より柔軟です。 階層構造エンティティの子は、シンプルなエンティティのみです。 

|Type|目的|例|
|--|--|--|
|階層構造|シンプルなエンティティの親子|Location.Origin=New York<br>Location.Destination=London|
|複合|親子エンティティ: 事前構築済み、リスト、シンプル、階層構造| number=3<br>list=first class<br>prebuilt.datetimeV2=March 5|

## <a name="data-matching-multiple-entities"></a>複数のエンティティに一致するデータ
単語またはフレーズが複数のエンティティに一致すると、エンドポイント クエリからそれぞれのエンティティが返されます。 事前構築済みの数値エンティティと事前構築済みの datetimeV2 の両方を追加した場合、LUIS では発話 `create meeting on 2018/03/12 for lunch with wayne` のすべてのエンティティを認識し、JSON エンドポイントの応答の一部としてエンティティの配列を返します。 

```JSON
{
  "query": "create meeting on 2018/03/12 for lunch with wayne",
  "topScoringIntent": {
    "intent": "Calendar.Add",
    "score": 0.9333419
  },
  "entities": [
    {
      "entity": "2018/03/12",
      "type": "builtin.datetimeV2.date",
      "startIndex": 18,
      "endIndex": 27,
      "resolution": {
        "values": [
          {
            "timex": "2018-03-12",
            "type": "date",
            "value": "2018-03-12"
          }
        ]
      }
    },
    {
      "entity": "2018",
      "type": "builtin.number",
      "startIndex": 18,
      "endIndex": 21,
      "resolution": {
        "value": "2018"
      }
    },
    {
      "entity": "03/12",
      "type": "builtin.number",
      "startIndex": 23,
      "endIndex": 27,
      "resolution": {
        "value": "0.25"
      }
    }
  ]
}
```

## <a name="data-matching-multiple-list-entities"></a>複数のリスト エンティティに一致するデータ
単語またはフレーズが複数のリスト エンティティに一致すると、エンドポイント クエリからそれぞれのリスト エンティティが返されます。

クエリ `when is the best time to go to red rock?` の場合、アプリの複数のリストに単語 `red` があると、LUIS ではすべてのエンティティを認識し、JSON エンドポイントの応答の一部としてエンティティの配列を返します。 

```JSON
{
  "query": "when is the best time to go to red rock?",
  "topScoringIntent": {
    "intent": "Calendar.Find",
    "score": 0.06701678
  },
  "entities": [
    {
      "entity": "red",
      "type": "Colors",
      "startIndex": 31,
      "endIndex": 33,
      "resolution": {
        "values": [
          "Red"
        ]
      }
    },
    {
      "entity": "red rock",
      "type": "Cities",
      "startIndex": 31,
      "endIndex": 38,
      "resolution": {
        "values": [
          "Destinations"
        ]
      }
    }
  ]
}
```

## <a name="if-you-need-more-than-the-maximum-number-of-entities"></a>最大数を超えるエンティティが必要な場合 

階層構造エンティティと複合エンティティを使用することが必要になる場合があります。 階層構造エンティティには、特性を共有しているエンティティ間や、カテゴリのメンバーであるエンティティ間の関係が反映されます。 子エンティティは、その親のカテゴリのすべてのメンバーです。 たとえば、PlaneTicketClass という名前の階層構造エンティティは、子エンティティ EconomyClass および FirstClass を持つことができます。 階層の範囲は、1 つのレベルの深さのみです。  

複合エンティティは、全体の一部を表します。 たとえば、PlaneTicketOrder をという名前の複合エンティティは、子エンティティ Airline、Destination、DepartureCity、DepartureDate、および PlaneTicketClass を持つことができます。 複合エンティティは、既存のシンプルなエンティティ、階層構造エンティティの子、または事前構築済みのエンティティから作成します。  

LUIS には、機械学習は行われないが、LUIS アプリに固定の値リストを指定できるリスト エンティティの種類も用意されています。 「[LUIS Boundaries (LUIS の境界)](luis-boundaries.md)」を参照し、リスト エンティティの種類の制限を確認してください。 

階層構造エンティティ、複合エンティティ、リスト エンティティを検討した結果、制限を超えるエンティティが必要な場合は、サポートにお問い合わせください。 それを行うには、システムに関する詳細情報を収集し、[LUIS](luis-reference-regions.md#luis-website) Web サイトにアクセスして **[サポート]** を選択します。 Azure サブスクリプションにサポート サービスが含まれている場合は、[Azure のテクニカル サポート](https://azure.microsoft.com/support/options/)にお問い合わせください。 

## <a name="best-practices"></a>ベスト プラクティス

呼び出し元のアプリケーションやボットでアクションを実行するために必要なパラメーターや発話からのデータが必要な場合は、[エンティティ](luis-concept-entity-types.md)を作成します。 エンティティは、(おそらく関数のパラメーターとして) 抽出する必要がある発話内の単語またはフレーズです。 

アプリケーションに追加する適切な種類のエンティティを選択するために、ユーザーがそのデータを入力する方法を理解する必要があります。 エンティティの種類はそれぞれ、機械学習、限定されたリスト、正規表現などの、さまざまなメカニズムを使用して見つけることができます。 確信がない場合は、シンプルなエンティティから始めて、None 意図を含むすべての意図について、すべての発話内でそのデータを表す単語またはフレーズにラベルを付けます。  

エンドポイントの発話を定期的に確認し、エンティティを正規表現として識別できる、または完全なテキスト一致で検索できる一般的な使用法を見つけてください。  

確認の一環として、ドメインにとって重要だが完全一致しない単語またはフレーズや、LUIS が確信を持てない単語またはフレーズについて、LUIS にとってのシグナルになるようにフレーズ リストを追加することを検討してください。  

詳細については、[ベスト プラクティス](luis-concept-best-practices.md)に関するページを参照してください。

## <a name="next-steps"></a>次の手順

正しい[発話](luis-concept-utterance.md)の概念について学習します。 

[エンティティの追加](luis-how-to-add-entities.md)に関するページで、LUIS アプリにエンティティを追加する方法の詳細を確認します。