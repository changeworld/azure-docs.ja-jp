---
title: アプリの計画
titleSuffix: Language Understanding - Azure Cognitive Services
description: 関連するアプリの意図とエンティティについて概説し、Language Understanding Intelligent Service (LUIS) でアプリケーション プランを作成します。
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: conceptual
ms.date: 09/26/2018
ms.author: diberry
ms.openlocfilehash: e14b9f2930ed9c170f31bd654829efe3b5a99446
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2018
ms.locfileid: "53091303"
---
# <a name="plan-your-luis-app"></a>LUIS アプリの計画

アプリを計画することが重要です。 アプリケーションに関連する可能性のある意図やエンティティなど、ドメインを特定します。  

## <a name="identify-your-domain"></a>ドメインの特定
LUIS アプリは、ドメイン固有のトピックを中心としています。  たとえば、チケット、フライト、ホテル、およびレンタカーを予約する旅行アプリがあります。 また、エクササイズ、フィットネスの努力の追跡、目標設定に関連するコンテンツを提供するアプリなどもあります。 ドメインを特定することで、ドメインにとって重要な単語やフレーズを見つけやすくなります。

> [!TIP]
> LUIS には、多くの一般的なシナリオに対応する[事前構築済みのドメイン](luis-how-to-use-prebuilt-domains.md)が用意されています。
> 事前構築されたドメインをベースに、ご自身のアプリを作成できるかどうかを検討してください。

## <a name="identify-your-intents"></a>意図の特定
ご自身のアプリケーションのタスクにとって重要な[意図](luis-concept-intent.md)について考えます。 たとえば、フライトを予約し、目的地の天気を確認する機能を備えた旅行アプリの例で見てみましょう。 これらのアクションに対しては "BookFlight" 意図と "GetWeather" 意図を定義できます。 さらに多くの機能を備えたより複雑なアプリでは、意図がさらに多くなるため、意図を定義するときは、細かくなりすぎないように気を付ける必要があります。 たとえば、"BookFlight" と "BookHotel" はそれぞれ別の意図にする必要がありますが、"BookInternationalFlight" と "BookDomesticFlight" は非常に似ています。

> [!NOTE]
> 使用する意図の数は、ご自身のアプリで実行する必要がある機能の数に合わせることをお勧めします。 定義する意図が多すぎると、LUIS で発話を正しく分類するのが難しくなります。 少なすぎると、一般的になりすぎて、重複が発生する可能性があります。

## <a name="create-example-utterances-for-each-intent"></a>各意図の発話の例の作成
意図を特定したら、意図ごとに 10 から 15 個の発話の例を作成します。 まず、発話をこの数より少なくしたり、各意図に対して多くの発話を作成したりしないでください。 各発話は、前の発話とは異なるものにする必要があります。 全体の単語数、単語の選択、動詞の時制、句読点など、発話に多様性を持たせることをお勧めします。 

## <a name="identify-your-entities"></a>エンティティの特定
発話の例では、抽出するエンティティを特定します。 フライトを予約するには、目的地、日付、航空会社、チケットの種類、搭乗クラスなどの情報が必要です。 これらは意図を実現するうえで重要であるため、これらのデータの種類のエンティティを作成し、発話の例で[エンティティ](luis-concept-entity-types.md)をマークします。 

どのエンティティをアプリで使用するかを判断するときは、オブジェクト間の関係を把握するための多種多様なエンティティがあることに注意してください。 「[LUIS のエンティティ](luis-concept-entity-types.md)」では、各種エンティティについて詳しく説明しています。

### <a name="simple-entity"></a>シンプル エンティティ
シンプル エンティティは 1 つの概念を示しています。

![シンプル エンティティ](./media/luis-plan-your-app/simple-entity.png)

エンドポイント JSON クエリ応答からシンプル エンティティを抽出する方法の詳細については、[データの抽出](luis-concept-data-extraction.md#simple-entity-data)に関するページを参照してください。 シンプル エンティティの使用方法の詳細については、こちらの[クイック スタート](luis-quickstart-primary-and-secondary-data.md)をお試しください。

### <a name="hierarchical-entity"></a>階層構造エンティティ
階層構造エンティティは特別な種類の**シンプル** エンティティで、親子関係の形式で、カテゴリとそのメンバーが定義されています。 この関係は、発話内のコンテキストによって決定されます。 階層構造エンティティの子は、シンプル エンティティでもあります。

![階層構造エンティティ](./media/luis-plan-your-app/hierarchical-entity.png)

エンドポイント JSON クエリ応答から階層構造エンティティを抽出する方法の詳細については、[データの抽出](luis-concept-data-extraction.md#hierarchical-entity-data)に関するページを参照してください。 階層構造エンティティの使用方法の詳細については、こちらの[クイック スタート](luis-quickstart-intent-and-hier-entity.md)をお試しください。

### <a name="composite-entity"></a>複合エンティティ
複合エンティティは、全体のパーツを形成する他のエンティティで構成されています。 複合エンティティには、さまざまなエンティティ型が含まれます。

![複合エンティティ](./media/luis-plan-your-app/composite-entity.png)

エンドポイント JSON クエリ応答から複合エンティティを抽出する方法の詳細については、[データの抽出](luis-concept-data-extraction.md#composite-entity-data)に関するページを参照してください。 複合エンティティの使用方法の詳細については、こちらの[チュートリアル](luis-tutorial-composite-entity.md)をお試しください。

### <a name="prebuilt-entity"></a>事前構築済みのエンティティ
LUIS には、数、日付、電子メール アドレス、URL などの一般的なデータの種類に対応する[事前構築済みのエンティティ](luis-prebuilt-entities.md)が用意されています。 チケットの注文でのチケット数には、事前構築済みのエンティティ Number を使用できます。

![事前構築済みのエンティティ Number](./media/luis-plan-your-app/number-entity.png)

エンドポイント JSON クエリ応答から事前構築済みのエンティティを抽出する方法の詳細については、「[データの抽出](luis-concept-data-extraction.md#prebuilt-entity-data)」をご覧ください。 

### <a name="list-entity"></a>リスト エンティティ 
リスト エンティティは、明示的に指定された値の一覧です。 各値は 1 つ以上のシノニムで構成されます。 旅行アプリでは、リスト エンティティを作成して空港の名前を表すことができます。

![リスト エンティティ](./media/luis-plan-your-app/list-entity.png)

エンドポイント JSON クエリ応答からリスト エンティティを抽出する方法の詳細については、[データの抽出](luis-concept-data-extraction.md#list-entity-data)に関するページを参照してください。 リスト エンティティの使用方法の詳細については、こちらの[クイック スタート](luis-quickstart-intent-and-list-entity.md)をお試しください。

### <a name="regular-expression-entity"></a>正規表現エンティティ
正規表現エンティティを使用すると、LUIS が、正規表現に基づいて発話から正しい形式のデータを抽出できます。

![正規表現エンティティ](./media/luis-plan-your-app/regex-entity.png)

エンドポイント JSON クエリ応答から正規表現エンティティを抽出する方法の詳細については、[データの抽出](luis-concept-data-extraction.md#regular-expression-entity-data)に関するページを参照してください。 正規表現エンティティを使用する方法の詳細については、[クイック スタート](luis-quickstart-intents-regex-entity.md)をお試しください。

## <a name="next-steps"></a>次の手順
アプリをトレーニングして発行し、アプリによってエンドポイント発話が取得されたら、[アクティブ ラーニング](luis-how-to-review-endoint-utt.md)、[フレーズ リスト](luis-concept-feature.md)、[パターン](luis-concept-patterns.md)を使用した予測機能強化の実装を計画します。 


* LUIS アプリを作成するための簡単なチュートリアルについては、[最初の Language Understanding Intelligent Service (LUIS) アプリの作成](luis-get-started-create-app.md)に関するページをご覧ください。
