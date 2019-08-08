---
title: アプリの計画 - LUIS
titleSuffix: Azure Cognitive Services
description: 関連するアプリの意図とエンティティについて概説し、Language Understanding Intelligent Service (LUIS) でアプリケーション プランを作成します。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: dc648b30dc1236080be06044f510557ae0ce9476
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/30/2019
ms.locfileid: "68638317"
---
# <a name="plan-your-luis-app-with-subject-domain-intents-and-entities"></a>主題の領域、意図、エンティティによって LUIS アプリを計画します

アプリを計画するには、主題領域ドメインを特定します。 これには、アプリケーションに関連する可能性のある意図やエンティティが含まれます。  

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

意図を特定したら、意図ごとに 15 個から 30 個の発話の例を作成します。 まず、各意図に対して発話をこの数より少なくしたり、多すぎる数の発話を作成したりしないでください。 各発話は、前の発話とは異なるものにする必要があります。 全体の単語数、単語の選択、動詞の時制、句読点など、発話に多様性を持たせることをお勧めします。 

詳細については、[発話](luis-concept-utterance.md)に関するページを確認してください。

## <a name="identify-your-entities"></a>エンティティの特定

発話の例では、抽出するエンティティを特定します。 フライトを予約するには、目的地、日付、航空会社、チケットの種類、搭乗クラスなどの情報が必要です。 これらのデータの種類のエンティティを作成し、発話の例で[エンティティ](luis-concept-entity-types.md)をマークしてください。これらのエンティティは意図を実現するうえで重要であるためです。 

どのエンティティをアプリで使用するかを判断するときは、オブジェクト間の関係を把握するための多種多様なエンティティがあることに注意してください。 「[LUIS のエンティティ](luis-concept-entity-types.md)」では、各種エンティティについて詳しく説明しています。

## <a name="next-steps"></a>次の手順

アプリをトレーニングして発行し、アプリによってエンドポイント発話が取得されたら、[アクティブ ラーニング](luis-how-to-review-endpoint-utterances.md)、[フレーズ リスト](luis-concept-feature.md)、[パターン](luis-concept-patterns.md)を使用した予測機能強化の実装を計画します。 


* LUIS アプリを作成するための簡単なチュートリアルについては、[最初の Language Understanding Intelligent Service (LUIS) アプリの作成](luis-get-started-create-app.md)に関するページをご覧ください。
