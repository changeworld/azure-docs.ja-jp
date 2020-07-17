---
title: エンティティの種類 - LUIS
description: 予測ランタイム時に、エンティティによってユーザーの発話からデータが抽出されます。 "_オプション_" の副次的な目的は、エンティティを特徴量として使用することで、意図またはその他のエンティティの予測を強化することです。
ms.topic: conceptual
ms.date: 04/30/2020
ms.openlocfilehash: 9d8afd5a660b3af5556256835486e984d7d657bc
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/19/2020
ms.locfileid: "83585642"
---
# <a name="extract-data-with-entities"></a>エンティティを使用してデータを抽出する

予測ランタイム時に、エンティティによってユーザーの発話からデータが抽出されます。 "_オプション_" の副次的な目的は、エンティティを特徴量として使用することで、意図またはその他のエンティティの予測を強化することです。

エンティティにはいくつかの種類があります。

* [機械学習エンティティ](reference-entity-machine-learned-entity.md)
* 必須[特徴量](luis-concept-feature.md)として使用される非機械学習 - 事前構築済みのエンティティによるテキスト完全一致、パターン一致、または検出用
* [Pattern.any](#patternany-entity) - 書籍のタイトルなどの自由形式のテキストを[パターン](reference-entity-pattern-any.md)から抽出します

機械学習エンティティを使用すると、最も広い範囲でデータ抽出を選択できます。 非機械学習エンティティはテキスト照合によって機能し、機械学習エンティティまたは意図用の[必須特徴量](#design-entities-for-decomposition)として使用されます。

## <a name="entities-represent-data"></a>エンティティはデータを表す

エンティティとは、発話から取得するデータのことです。たとえば、名前、日付、製品名、または重要な単語のグループなどです。 発話には、多数のエンティティを含めることも、まったく含めないこともできます。 クライアント アプリケーションでは、そのタスクを実行するためにデータを必要とする "_可能性_" があります。

エンティティには、モデル内の意図ごとに、すべてのトレーニング発話にわたって一貫性を保持してラベル付けする必要があります。

 独自のエンティティを定義することや、[datetimeV2](luis-reference-prebuilt-datetimev2.md)、[ordinal (序数)](luis-reference-prebuilt-ordinal.md)、[email (電子メール)](luis-reference-prebuilt-email.md)、[phone number (電話番号)](luis-reference-prebuilt-phonenumber.md) などの一般的な概念について、事前構築済みのエンティティを使用して時間を節約することができます。

|発話|Entity|Data|
|--|--|--|
|ニューヨーク行きのチケットを 3 枚購入する|事前構築済みの数値<br>宛先|3<br>ニューヨーク|


### <a name="entities-are-optional-but-recommended"></a>エンティティは省略可能だが強く推奨される

[意図](luis-concept-intent.md)は必須ですが、エンティティは省略可能です。 すべての概念用のエンティティをアプリ内に作成する必要はありません。クライアント アプリケーションで別のエンティティまたは意図に対するヒントまたはシグナルとして機能するデータまたはエンティティが必要な場合のみ、それらを作成する必要があります。

アプリケーションの開発に伴いデータの新しいニーズが特定されたら、適切なエンティティを LUIS モデルに後で追加することができます。

## <a name="entity-compared-to-intent"></a>エンティティと意図の比較

エンティティは、_ユーザーの発話内_のデータの概念を表します。 意図は、_発話全体_を分類します。

次の 4 つの発話について検討します。

|発話|予測される意図|抽出されたエンティティ|説明|
|--|--|--|--|
|Help|help|-|何も抽出されません。|
|Send something|sendSomething|-|何も抽出されません。 このモデルには、このコンテキストの `something` を抽出するための必須特徴量がなく、受領者が示されていません。|
|Send Bob a present|sendSomething|`Bob`, `present`|このモデルでは、事前構築済みのエンティティ `personName` の必須特徴量を追加することで、`Bob` が抽出されます。 `present` を抽出するのに機械学習エンティティが使用されています。|
|Send Bob a box of chocolates|sendSomething|`Bob`, `box of chocolates`|機械学習エンティティによって、データの 2 つの重要な部分である `Bob` と `box of chocolates` が抽出されています。|

## <a name="design-entities-for-decomposition"></a>分解のためのエンティティの設計

機械学習エンティティを使用して、分解用のアプリ スキーマを設計して、大きな概念をサブエンティティに分割できます。

分解のための設計により、LUIS では、クライアント アプリケーションに対してより深いレベルのエンティティ解決を返すことができます。 これにより、クライアント アプリケーションはビジネス ルールに集中して、データの解決を LUIS に任せることができます。

機械学習エンティティは、発話の例を通して学習したコンテキストに基づいてトリガーされます。

[**機械学習エンティティ**](tutorial-machine-learned-entity.md)は、最上位レベルのエクストラクターです。 サブエンティティは、機械学習エンティティの子エンティティです。

<a name="composite-entity"></a>
<a name="list-entity"></a>
<a name="patternany-entity"></a>
<a name="prebuilt-entity"></a>
<a name="regular-expression-entity"></a>
<a name="simple-entity"></a>

## <a name="types-of-entities"></a>エンティティの種類

親に対するサブエンティティは、機械学習エンティティである必要があります。 サブエンティティでは、非機械学習エンティティを[特徴量](luis-concept-feature.md)として使用できます。

データの抽出方法と抽出後にそれを表現する方法に基づいてエンティティを選択します。

|エンティティの種類|目的|
|--|--|
|[**機械学習**](tutorial-machine-learned-entity.md)|ラベル付けされた例から学習された、入れ子になった複雑なデータを抽出します。 |
|[**リスト**](reference-entity-list.md)|項目の一覧と、**テキスト完全一致**を使用して抽出されたそれらのシノニム。|
|[**Pattern.any**](#patternany-entity)|エンティティが自由形式であるため、エンティティの終わりが検出されたことを判断するのが難しいエンティティ。 [パターン](luis-concept-patterns.md)でのみ使用できます。|
|[**事前構築済み**](luis-reference-prebuilt-entities.md)|URL や電子メールなど、特定の種類のデータを抽出するために既にトレーニングされています。 これらの事前構築済みエンティティの一部は、オープンソースの [Recognizers-Text](https://github.com/Microsoft/Recognizers-Text) プロジェクトで定義されます。 ご自分の特定のカルチャまたはエンティティが現在サポートされていない場合は、プロジェクトにご協力ください。|
|[**正規表現**](reference-entity-regular-expression.md)|**テキスト完全一致**のために正規表現を使用します。|

## <a name="extracting-contextually-related-data"></a>文脈的に関連するデータの抽出

1 回の発話にエンティティが 2 回以上出現し、データの意味が発話内のコンテキストに基づく場合があります。 例として、出発地と到着地という 2 つの地理的な場所があるフライトを予約するための発話が挙げられます。

`Book a flight from Seattle to Cairo`

チケットの購入を完了するには、クライアント アプリケーションで各場所の種類が認識されるように 2 つの場所を抽出する必要があります。

出発地と到着地を抽出するには、チケット注文機械学習エンティティの一部として、2 つのサブエンティティを作成します。 各サブエンティティに対して、geographyV2 を使用する必須特徴量を作成します。

<a name="using-component-constraints-to-help-define-entity"></a>
<a name="using-subentity-constraints-to-help-define-entity"></a>

### <a name="using-required-features-to-constrain-entities"></a>必須特徴量を使用したエンティティの制限

[必須特徴量](luis-concept-feature.md)の詳細

## <a name="patternany-entity"></a>Pattern.any エンティティ

Pattern.any は、[パターン](luis-concept-patterns.md)内でのみ使用できます。

<a name="if-you-need-more-than-the-maximum-number-of-entities"></a>
## <a name="exceeding-app-limits-for-entities"></a>エンティティに対するアプリの制限の超過

[制限](luis-limits.md#model-limits)を超える必要がある場合は、サポートにお問い合わせください。 それを行うには、システムに関する詳細情報を収集し、[LUIS](luis-reference-regions.md#luis-website) Web サイトにアクセスして **[サポート]** を選択します。 Azure サブスクリプションにサポート サービスが含まれている場合は、[Azure のテクニカル サポート](https://azure.microsoft.com/support/options/)にお問い合わせください。

## <a name="entity-prediction-status"></a>エンティティ予測の状態

発話の例で選択したエンティティと異なるエンティティ予測がエンティティに含まれている場合は、LUIS ポータルが表示されます。 この異なるスコアは、現在のトレーニング済みのモデルに基づいています。 この情報を使用して、以下の 1 つまたは複数の方法で、トレーニング エラーを解決します。
* エンティティの概念の識別に役立つように、エンティティの[特徴量](luis-concept-feature.md)を作成します。
* [発話の例](luis-concept-utterance.md)を追加し、エンティティにラベルを付けます。
* エンティティの概念を識別するのに役立つ、予測エンドポイントで受信したすべての発話に対する[アクティブ ラーニングの提案](luis-concept-review-endpoint-utterances.md)を確認します。

## <a name="next-steps"></a>次のステップ

正しい[発話](luis-concept-utterance.md)の概念について学習します。

[エンティティの追加](luis-how-to-add-entities.md)に関するページで、LUIS アプリにエンティティを追加する方法の詳細を確認します。

「[チュートリアル:Language Understanding (LUIS) で機械学習エンティティを使用して、ユーザーの発話から構造化データを抽出する](tutorial-machine-learned-entity.md)」を参照して、機械学習エンティティを使用して発話から構造化データを抽出する方法について確認します。

