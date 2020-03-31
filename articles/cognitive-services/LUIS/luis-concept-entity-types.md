---
title: エンティティの種類 - LUIS
titleSuffix: Azure Cognitive Services
description: エンティティは、発話からデータを抽出します。 エンティティの種類では、データの予測可能な抽出が提供されます。 エンティティには、機械学習と非機械学習の 2 種類があります。 発話で使用しているエンティティの種類を把握していることが重要です。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: diberry
ms.openlocfilehash: 6ee156efb5512c92d86ba05513b6a2b91df4eae8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219181"
---
# <a name="entities-and-their-purpose-in-luis"></a>エンティティと LUIS におけるその目的

エンティティの主な目的は、クライアント アプリケーションに予測可能な抽出データを提供することです。 "_オプション_" の 2 つ目の目的は、記述子により意図またはその他のエンティティの予測を向上させることです。

エンティティには、次の 2 つの種類があります。

* 機械学習 - コンテキストより
* 非機械学習 - テキスト完全一致、パターン一致、または事前構築済みのエンティティによる検出の場合

機械学習エンティティを使用すると、最も広い範囲でデータ抽出を選択できます。 非機械学習エンティティはテキスト照合によって機能し、単独で使用したり、機械学習エンティティの[制約](#design-entities-for-decomposition)として使用することもできます。

## <a name="entities-represent-data"></a>エンティティはデータを表す

エンティティとは、発話から取得するデータのことです。たとえば、名前、日付、製品名、または重要な単語のグループなどです。 発話には、多数のエンティティを含めることも、まったく含めないこともできます。 クライアント アプリケーションでは、そのタスクを実行するためにデータを必要とする "_可能性_" があります。

エンティティには、モデル内の意図ごとに、すべてのトレーニング発話にわたって一貫性を保持してラベル付けする必要があります。

 独自のエンティティを定義することや、[datetimeV2](luis-reference-prebuilt-datetimev2.md)、[ordinal (序数)](luis-reference-prebuilt-ordinal.md)、[email (電子メール)](luis-reference-prebuilt-email.md)、[phone number (電話番号)](luis-reference-prebuilt-phonenumber.md) などの一般的な概念について、事前構築済みのエンティティを使用して時間を節約することができます。

|発話|Entity|Data|
|--|--|--|
|ニューヨーク行きのチケットを 3 枚購入する|事前構築済みの数値<br>場所.目的地|3<br>ニューヨーク|
|3 月 5 日のニューヨーク発ロンドン行きのチケットを購入する|場所.出発地<br>場所.目的地<br>事前構築済みの datetimeV2|ニューヨーク<br>London<br>2018 年 3 月 5 日|

### <a name="entities-are-optional"></a>エンティティは省略可能

意図は必須ですが、エンティティは省略可能です。 アプリ内のすべての概念を表すエンティティを作成する必要はありません。クライアント アプリケーションがアクションを実行するために必須のエンティティのみ作成します。

クライアント アプリケーションで必要なデータが発話にない場合は、エンティティを追加する必要はありません。 アプリケーションの開発に伴いデータの新しいニーズが特定されたら、適切なエンティティを LUIS モデルに後で追加することができます。

## <a name="entity-compared-to-intent"></a>エンティティと意図の比較

エンティティは、抽出しようとしている発話内のデータの概念を表します。

発話には、必要に応じてエンティティを含めることができます。 一方、発話に対する意図の予測は "_必須_" で、発話全体を表します。 LUIS を使用するには、発話のサンプルが意図に含まれている必要があります。

次の 4 つの発話について考えます。

|発話|予測される意図|抽出されたエンティティ|説明|
|--|--|--|--|
|Help|help|-|何も抽出されません。|
|Send something|sendSomething|-|何も抽出されません。 このコンテキストで `something` を抽出するようにモデルがトレーニングされておらず、受信者も存在しません。|
|Send Bob a present|sendSomething|`Bob`, `present`|モデルは [personName](luis-reference-prebuilt-person.md) という事前構築済みのエンティティでトレーニングされており、これによって名前 `Bob` が抽出されました。 `present` を抽出するのに機械学習エンティティが使用されています。|
|Send Bob a box of chocolates|sendSomething|`Bob`, `box of chocolates`|データの 2 つの重要な部分である `Bob` と `box of chocolates` がエンティティによって抽出されています。|

## <a name="design-entities-for-decomposition"></a>分解のためのエンティティの設計

良いエンティティ設計とは、トップレベルのエンティティを機械学習エンティティにすることです。 これにより、時間の経過と共にエンティティ設計を変更したり、**サブコンポーネント** (子エンティティ) を必要に応じて**制約**および**記述子**とともに使用して、最上位レベルのエンティティをクライアント アプリケーションによって必要な部分に分解できます。

分解のための設計により、LUIS では、クライアント アプリケーションに対してより深いレベルのエンティティ解決を返すことができます。 これにより、クライアント アプリケーションはビジネス ルールに集中して、データの解決を LUIS に任せることができます。

### <a name="machine-learned-entities-are-primary-data-collections"></a>機械学習エンティティは、プライマリ データ コレクションである

[**機械学習エンティティ**](tutorial-machine-learned-entity.md)は、最上位レベルのデータ単位です。 サブコンポーネントは、機械学習エンティティの子エンティティです。

機械学習エンティティは、トレーニング発話を通じて学習したコンテキストに基づいてトリガーされます。 **制約**は、機械学習エンティティに適用されるオプションの規則で、[リスト](reference-entity-list.md) や [正規表現](reference-entity-regular-expression.md)など、非機械学習エンティティのテキスト完全一致の定義に基づきトリガーをさらに制限します。 たとえば、`size` 機械学習エンティティは、`sizeList` エンティティ内に含まれる値が検出された場合にのみ `size` エンティティがトリガーされるように制約する `sizeList` リスト エンティティの制約を持つことができます。

[**記述子**](luis-concept-feature.md)は、予測のために単語や語句の関連性を高める目的で適用される機能です。 これらは意図またはエンティティを*記述する*目的で使用されるため、*記述子*と呼ばれます。 記述子は、LUIS によって観察され、学習される重要な単語や語句など、データの特徴的な特性または属性を識別します。

LUIS アプリで語句一覧機能を作成すると、既定でグローバルに有効になり、すべての意図とエンティティに対して均等に適用されます。 ただし、機械学習エンティティ (または *モデル*) の記述子 (機能) として語句一覧を適用した場合、そのスコープは、そのモデルにのみ適用されるように縮小され、他のすべてのモデルでは使用されなくなります。 モデルに対して語句一覧を記述子として使用すると、それが適用されるモデルの精度が向上し、分解に役立ちます。

<a name="composite-entity"></a>
<a name="list-entity"></a>
<a name="patternany-entity"></a>
<a name="prebuilt-entity"></a>
<a name="regular-expression-entity"></a>
<a name="simple-entity"></a>

## <a name="types-of-entities"></a>エンティティの種類

データの抽出方法と抽出後にそれを表現する方法に基づいてエンティティを選択します。

|エンティティの種類|目的|
|--|--|
|[**機械学習**](tutorial-machine-learned-entity.md)|機械学習エンティティは、発話内のコンテキストから学習します。 エンティティ型に関係なく、エンティティの親グループ。 これにより、発話例での配置の変動が顕著になります。 |
|[**リスト**](reference-entity-list.md)|項目の一覧と、**テキスト完全一致**を使用して抽出されたそれらのシノニム。|
|[**Pattern.any**](reference-entity-pattern-any.md)|エンティティの末尾の特定が困難なエンティティ。 |
|[**事前構築済み**](luis-reference-prebuilt-entities.md)|URL や電子メールなど、特定の種類のデータを抽出するために既にトレーニングされています。 これらの事前構築済みエンティティの一部は、オープンソースの [Recognizers-Text](https://github.com/Microsoft/Recognizers-Text) プロジェクトで定義されます。 ご自分の特定のカルチャまたはエンティティが現在サポートされていない場合は、プロジェクトにご協力ください。|
|[**正規表現**](reference-entity-regular-expression.md)|**テキスト完全一致**のために正規表現を使用します。|

## <a name="extracting-contextually-related-data"></a>文脈的に関連するデータの抽出

1 回の発話にエンティティが 2 回以上出現し、データの意味が発話内のコンテキストに基づく場合があります。 例として、出発地と到着地の 2 つの場所があるフライトを予約するための発話が挙げられます。

`Book a flight from Seattle to Cairo`

`location` エンティティの 2 つの例を抽出する必要があります。 チケットの購入を完了するには、クライアント アプリケーションがそれぞれの場所の種類を把握している必要があります。

文脈的に関連するデータを抽出するには、次の 2 つの方法があります。

 * `location` エンティティは機械学習エンティティであり、2 つのサブコンポーネント エンティティを使用して、`origin` と `destination` をキャプチャします (推奨)。
 * `location` エンティティは `origin` と `destination` の 2 つの**ロール**を使用します

発話には複数のエンティティが存在する可能性があり、使用されるコンテキストが重要でない場合は、分解またはロールを使用せずに抽出することができます。 たとえば、`I want to travel to Seattle, Cairo, and London.` のように、発話に場所の一覧が含まれる場合、この一覧では各項目に付加的な意味はありません。

### <a name="using-subcomponent-entities-of-a-machine-learned-entity-to-define-context"></a>機械学習エンティティのサブコンポーネント エンティティを使用してコンテキストを定義する

[**機械学習エンティティ**](tutorial-machine-learned-entity.md)を使用して、フライトの予約のアクションを記述したデータを抽出し、その後、最上位のエンティティをクライアント アプリケーションで必要な個別の部分に分解することができます。

この `Book a flight from Seattle to Cairo` という例では、最上位レベルのエンティティを `travelAction` にして、`flight from Seattle to Cairo` を抽出するようにラベルを付けることができます。 次に、`origin` と `destination` と呼ばれる 2 つのサブコンポーネント エンティティが作成されます。両方とも、事前に構築された `geographyV2` エンティティの制約が適用されます。 トレーニング発話では、`origin` と `destination` に適切なラベルが付けられます。

### <a name="using-entity-role-to-define-context"></a>エンティティ ロールを使用してコンテキストを定義する

ロールは、発話内のコンテキストに基づくエンティティの名前付きエイリアスです。 ロールは、任意の事前構築済みまたはカスタムのエンティティ型で使用でき、発話とパターンの両方の例で使用できます。 この例で、`location` エンティティには `origin` と `destination` の 2 つのロールが必要であり、その両方が発話の例でマークされている必要があります。

LUIS によって検出された `location` のロールが判断できない場合でも、場所のエンティティは返されます。 クライアント アプリケーションでは、ユーザーが意図した場所の種類を判断するために、フォローアップの質問をする必要があります。


## <a name="if-you-need-more-than-the-maximum-number-of-entities"></a>最大数を超えるエンティティが必要な場合

上限を超えて必要な場合は、サポートにお問い合わせください。 それを行うには、システムに関する詳細情報を収集し、[LUIS](luis-reference-regions.md#luis-website) Web サイトにアクセスして **[サポート]** を選択します。 Azure サブスクリプションにサポート サービスが含まれている場合は、[Azure のテクニカル サポート](https://azure.microsoft.com/support/options/)にお問い合わせください。

## <a name="entity-prediction-status"></a>エンティティ予測の状態

LUIS ポータルは、発話の例のエンティティに、選択したエンティティと異なるエンティティ予測があるときを示します。 この異なるスコアは、現在のトレーニング済みのモデルに基づいています。

## <a name="next-steps"></a>次のステップ

正しい[発話](luis-concept-utterance.md)の概念について学習します。

[エンティティの追加](luis-how-to-add-entities.md)に関するページで、LUIS アプリにエンティティを追加する方法の詳細を確認します。

「[チュートリアル:Language Understanding (LUIS) で機械学習エンティティを使用して、ユーザーの発話から構造化データを抽出する](tutorial-machine-learned-entity.md)」を参照して、機械学習エンティティを使用して発話から構造化データを抽出する方法について確認します。
 
