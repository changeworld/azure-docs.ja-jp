---
title: アプリの計画 - LUIS
description: 関連するアプリの意図とエンティティについて概説し、Language Understanding Intelligent Service (LUIS) でアプリケーション プランを作成します。
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: dfed27a05973a2ea2e9a97eaa1c233b847b33d87
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/14/2020
ms.locfileid: "81382306"
---
# <a name="plan-your-luis-app-schema-with-subject-domain-and-data-extraction"></a>サブジェクト ドメインとデータ抽出を使用して LUIS アプリ スキーマを計画する

LUIS アプリ スキーマには、サブジェクト [ドメイン](luis-glossary.md#domain)に関連する[意図](luis-glossary.md#intent)と[エンティティ](luis-glossary.md#entity)が含まれています。 [意図](luis-glossary.md#utterance)はユーザーの発話を分類し、エンティティはユーザー発話からデータを抽出します。

## <a name="identify-your-domain"></a>ドメインの特定

LUIS アプリはサブジェクト ドメインが中心です。 たとえば、チケット、フライト、ホテル、およびレンタカーの予約を処理する旅行アプリがあります。 また、エクササイズ、フィットネスの努力の追跡、目標設定に関連するコンテンツを提供するアプリなどもあります。 ドメインを特定することで、ドメインに関連する単語やフレーズを見つけやすくなります。

> [!TIP]
> LUIS には、多くの一般的なシナリオに対応する[事前構築済みのドメイン](luis-how-to-use-prebuilt-domains.md)が用意されています。 事前構築されたドメインをベースに、ご自身のアプリを作成できるかどうかを検討してください。

## <a name="identify-your-intents"></a>意図の特定

ご自身のアプリケーションのタスクにとって重要な[意図](luis-concept-intent.md)について考えます。

たとえば、フライトを予約し、目的地の天気を確認する機能を備えた旅行アプリの例で見てみましょう。 これらのアクションの `BookFlight` と `GetWeather` 意図を定義できます。

さらに多くの機能を備えたより複雑なアプリでは、意図がさらに多くなるため、意図を定義するときは、意図が細かくなりすぎないように気を付ける必要があります。 たとえば、`BookFlight` と `BookHotel` は別の意図である必要がありますが、`BookInternationalFlight` と `BookDomesticFlight` は似すぎているかもしれません。

> [!NOTE]
> 使用する意図の数は、ご自身のアプリで実行する必要がある機能の数に合わせることをお勧めします。 定義する意図が多すぎると、LUIS で発話を正しく分類するのが難しくなります。 少なすぎると、一般的になりすぎて、重複する可能性があります。

全体的なユーザーの意図を特定する必要がない場合は、すべてのユーザーの発話例を `None` の意図に追加します。 アプリで必要な意図が増えた場合は、後で作成できます。

## <a name="create-example-utterances-for-each-intent"></a>各意図の発話の例の作成

最初は、各意図に多くの発話を作成しないようにします。 意図を特定したら、意図ごとに 15 個から 30 個の発話の例を作成します。 各発話は、以前に提供された発話とは異なる必要があります。 全体の単語数、単語の選択、動詞の時制、[句読点](luis-reference-application-settings.md#punctuation-normalization)など、発話に多様性を持たせることをお勧めします。

詳細については、[LUIS アプリに適した発話の概要](luis-concept-utterance.md)に関する記事を参照してください。

## <a name="identify-your-entities"></a>エンティティの特定

発話の例では、抽出するエンティティを特定します。 フライトを予約するには、目的地、日付、航空会社、チケットの種類、搭乗クラスなどの情報が必要です。 これらのデータ型のエンティティを作成し、発話例の[エンティティ](luis-concept-entity-types.md)をマークします。 意図を達成するにはエンティティが重要です。

どのエンティティをアプリで使用するかを判断するときは、オブジェクトの種類間の関係を把握するための多種多様なエンティティがあることに注意してください。 「[LUIS のエンティティ](luis-concept-entity-types.md)」では、各種エンティティについて詳しく説明しています。

> [!TIP]
> LUIS には、一般的な会話ユーザーのシナリオに合わせて[構築済みのエンティティ](luis-prebuilt-entities.md)が用意されています。 アプリケーション開発の出発点として、構築済みのエンティティを使用することを検討してください。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [LUIS 開発ライフサイクルの学習](luis-concept-app-iteration.md)

