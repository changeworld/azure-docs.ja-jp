---
title: LUIS アプリケーションの計画 | Microsoft Docs
description: 関連するアプリの意図とエンティティについて概説し、Language Understanding Intelligent Service (LUIS) でアプリケーション プランを作成します。
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/07/2017
ms.author: diberry
ms.openlocfilehash: cba156d784bfcdd2586073ab5562faf604569cd8
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/03/2018
ms.locfileid: "39505530"
---
# <a name="plan-your-luis-app"></a>LUIS アプリの計画

LUIS でアプリの作成を開始する前に、アプリを計画することが重要です。 ご自身のアプリケーションのドメイン固有のトピックに関連する可能性のある、意図とエンティティのアウトラインまたはスキーマを準備します。  

## <a name="identify-your-domain"></a>ドメインの特定
LUIS アプリは、ドメイン固有のトピックを中心としています。  たとえば、チケット、フライト、ホテル、およびレンタカーを予約する旅行アプリがあります。 また、エクササイズ、フィットネスの努力の追跡、目標設定に関連するコンテンツを提供するアプリなどもあります。 

> [!TIP]
> LUIS には、多くの一般的なシナリオに対応する[事前構築済みのドメイン](luis-how-to-use-prebuilt-domains.md)が用意されています。
> 事前構築されたドメインをベースに、ご自身のアプリを作成できるかどうかを検討してください。

## <a name="identify-your-intents"></a>意図の特定
ご自身のアプリケーションのタスクにとって重要な[意図](luis-concept-intent.md)について考えます。 たとえば、フライトを予約し、目的地の天気を確認する機能を備えた旅行アプリの例で見てみましょう。 これらのアクションに対しては "BookFlight" 意図と "GetWeather" 意図を定義できます。 さらに多くの機能を備えたより複雑なアプリでは、意図がさらに多くなるため、意図を定義するときは、細かくなりすぎないように気を付ける必要があります。 たとえば、"BookFlight" と "BookHotel" はそれぞれ別の意図にする必要がありますが、"BookInternationalFlight" と "BookDomesticFlight" は非常に似ています。

> [!NOTE]
> 使用する意図の数は、ご自身のアプリで実行する必要がある機能の数に合わせることをお勧めします。 定義する意図が多すぎると、LUIS で発話を正しく分類するのが難しくなります。 少なすぎると、一般的になりすぎて、重複が発生する可能性があります。


## <a name="identify-your-entities"></a>エンティティの特定
フライトを予約するには、目的地、日付、航空会社、チケットの種類、搭乗クラスなどの情報が必要です。 これらの情報は、意図を実行するうえで重要であるため、[エンティティ](luis-concept-entity-types.md)として追加できます。 

どのエンティティをアプリで使用するかを判断するときは、オブジェクト間の関係を把握するための多種多様なエンティティがあることに注意してください。 「[LUIS のエンティティ](luis-concept-entity-types.md)」では、各種エンティティについて詳しく説明しています。

### <a name="simple-entity"></a>シンプル エンティティ
シンプル エンティティは 1 つの概念を示しています。

![シンプル エンティティ](./media/luis-plan-your-app/simple-entity.png)

エンドポイント JSON クエリ応答からシンプル エンティティを抽出する方法の詳細については、[データの抽出](luis-concept-data-extraction.md#simple-entity-data)に関するページを参照してください。 シンプル エンティティの使用方法の詳細については、シンプル エンティティの[クイック スタート](luis-quickstart-primary-and-secondary-data.md)をお試しください。

### <a name="hierarchical-entity"></a>階層構造エンティティ
階層構造エンティティは特別な種類の**シンプル** エンティティで、親子関係の形式で、カテゴリとそのメンバーが定義されています。

![階層構造エンティティ](./media/luis-plan-your-app/hierarchical-entity.png)

エンドポイント JSON クエリ応答から階層構造エンティティを抽出する方法の詳細については、[データの抽出](luis-concept-data-extraction.md#hierarchical-entity-data)に関するページを参照してください。 階層構造エンティティの使用方法の詳細については、階層構造エンティティの[クイック スタート](luis-quickstart-intent-and-hier-entity.md)をお試しください。

### <a name="composite-entity"></a>複合エンティティ
複合エンティティは、全体のパーツを形成する他のエンティティで構成されています。 

![複合エンティティ](./media/luis-plan-your-app/composite-entity.png)

エンドポイント JSON クエリ応答から複合エンティティを抽出する方法の詳細については、[データの抽出](luis-concept-data-extraction.md#composite-entity-data)に関するページを参照してください。 複合エンティティの使用方法の詳細については、複合エンティティ [チュートリアル](luis-tutorial-composite-entity.md)をお試しください。

### <a name="prebuilt-entity"></a>事前構築済みのエンティティ
LUIS には、チケット注文におけるチケット数に使用できる `Number` など、一般的な種類については、[事前構築済みのエンティティ](luis-prebuilt-entities.md)が用意されています。

![事前構築済みのエンティティ Number](./media/luis-plan-your-app/number-entity.png)

エンドポイント JSON クエリ応答から事前構築済みのエンティティを抽出する方法について詳しくは、「[データの抽出](luis-concept-data-extraction.md#prebuilt-entity-data)」をご覧ください。 

### <a name="list-entity"></a>リスト エンティティ 
リスト エンティティは、明示的に指定された値の一覧です。 各値は 1 つ以上のシノニムで構成されます。 旅行アプリでは、リスト エンティティを作成して空港の名前を表すことができます。

![リスト エンティティ](./media/luis-plan-your-app/list-entity.png)

エンドポイント JSON クエリ応答からリスト エンティティを抽出する方法の詳細については、[データの抽出](luis-concept-data-extraction.md#list-entity-data)に関するページを参照してください。 リスト エンティティを使用する方法の詳細については、[クイック スタート](luis-quickstart-intent-and-list-entity.md)をお試しください。

### <a name="regular-expression-entity"></a>正規表現エンティティ
正規表現エンティティを使用すると、LUIS が、正規表現式に基づいて発話からデータを抽出できます。

![正規表現エンティティ](./media/luis-plan-your-app/regex-entity.png)

エンドポイント JSON クエリ応答から正規表現エンティティを抽出する方法の詳細については、[データの抽出](luis-concept-data-extraction.md#regular-expression-entity-data)に関するページを参照してください。 正規表現エンティティを使用する方法の詳細については、[クイック スタート](luis-quickstart-intents-regex-entity.md)をお試しください。

## <a name="after-getting-endpoint-utterances"></a>エンドポイントの発話の取得後
お使いのアプリによってエンドポイント発話が取得されたら、[アクティブ ラーニング](luis-how-to-review-endoint-utt.md)、[フレーズ リスト](luis-concept-feature.md)、および[パターン](luis-concept-patterns.md)を使用して、予測機能強化の実装を計画します。 

### <a name="patternany-entity"></a>Pattern.any エンティティ
Patterns.any は、エンティティの開始位置と終了位置を示すために[パターンの](luis-concept-patterns.md)テンプレート発話でのみ使用される、可変長プレース ホルダーです。 テンプレート発話は、エンティティ、および無視できるテキストを特定できるように、[適切な構文](luis-concept-patterns.md#pattern-syntax)に準拠しています。


## <a name="next-steps"></a>次の手順
* LUIS アプリを作成するための簡単なチュートリアルについては、[最初の Language Understanding Intelligent Service (LUIS) アプリの作成](luis-get-started-create-app.md)に関するページをご覧ください。
