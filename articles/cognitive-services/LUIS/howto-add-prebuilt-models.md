---
title: Language Understanding の事前構築済みモデル
titleSuffix: Azure Cognitive Services
description: LUIS には一連の事前構築済みモデルが含まれており、一般的な会話形式のユーザー シナリオを簡単に追加できます。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: 2e90a5f3f7cd2cfde2a2ead26674d2348a24ae6f
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2019
ms.locfileid: "73506786"
---
# <a name="add-prebuilt-models-for-common-usage-scenarios"></a>一般的な使用シナリオ向けに事前構築済みモデルを追加する 

LUIS には事前構築済みドメインの一連の事前構築済み意図が含まれており、一般的な意図と発話を簡単に追加できます。 これは会話型クライアント アプリに機能を追加する迅速かつ簡単な方法であり、これらの機能のモデルを設計する必要はありません。 

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

## <a name="add-a-prebuilt-domain"></a>事前構築済みのドメインの追加

1. **[マイ アプリ]** ページでアプリを選択します。 アプリの **[ビルド]** セクションが開きます。 

1. **[Intents]\(意図\)** ページで、左下のツール バーの **[Add prebuilt domains]\(事前構築済みドメインの追加\)** を選択します。 

1. **[カレンダー]** 意図を選択し、 **[ドメインの追加]** ボタンを選択します。

    ![カレンダー事前構築済みドメインを追加する](./media/luis-prebuilt-domains/add-prebuilt-domain.png)

1. 左側のナビゲーションで **[意図]** を選択し、カレンダー意図を表示します。 このドメインの各意図には、`Calendar.` というプレフィックスが追加されます。 発話と共に、このドメインの 2 つのエンティティ `Calendar.Location` と `Calendar.Subject` がアプリに追加されます。 

### <a name="train-and-publish"></a>トレーニングして公開する

1. ドメインを追加した後、右上のツール バーにある **[トレーニング]** を選択することによってアプリをトレーニングします。 

1. 上部ツール バーで **[発行]** を選択します。 **[運用]** に発行します。 

1. 緑の成功通知が表示されたら、 **[Refer to the list of endpoints]\(エンドポイントの一覧を参照する\)** リンクを選択して、エンドポイントを表示します。

1. エンドポイントを選択します。 新しいブラウザー タブでそのエンドポイントが開きます。 ブラウザー タブを開いたままにして、 **[テスト]** セクションに移動します。

### <a name="test"></a>テスト

**q** パラメーターに値を追加して、エンドポイントで新しい意図をテストします。`Schedule a meeting with John Smith in Seattle next week`

LUIS から、正しい意図と会議の件名が返されます。

```json
{
  "query": "Schedule a meeting with John Smith in Seattle next week",
  "topScoringIntent": {
    "intent": "Calendar.Add",
    "score": 0.824783146
  },
  "entities": [
    {
      "entity": "a meeting with john smith",
      "type": "Calendar.Subject",
      "startIndex": 9,
      "endIndex": 33,
      "score": 0.484055847
    }
  ]
}
```

## <a name="add-a-prebuilt-intent"></a>事前構築済み意図を追加する

1. **[マイ アプリ]** ページでアプリを選択します。 アプリの **[ビルド]** セクションが開きます。 

1. **[Intents]\(意図\)** ページで、意図一覧の上にあるツール バーの **[Add prebuilt intent]\(事前構築済み意図の追加\)** を選択します。 

1. ポップアップ ダイアログから **Utilities.Cancel** 意図を選択します。 

    ![事前構築済み意図を追加する](./media/luis-prebuilt-intents/prebuilt-intents-ddl.png)

1. **[完了]** ボタンを選択します。

### <a name="train-and-test"></a>トレーニングとテスト

1. 意図を追加した後、右上のツール バーにある **[トレーニング]** を選択することによってアプリをトレーニングします。 

1. 右側にあるツール バーの **[テスト]** を選択して、新しい意図をテストします。 

1. テキスト ボックスに、キャンセルの発話を入力します。

    |テスト発話|予測スコア|
    |--|:--|
    |フライトをキャンセルしたい。|0.67|
    |購入をキャンセルする。|0.52|
    |会議を取り消す。|0.56|

    ![事前構築済み意図をテストする](./media/luis-prebuilt-intents/test.png)

## <a name="add-a-prebuilt-entity"></a>作成済みエンティティの追加

1. **[マイ アプリ]** ページで名前をクリックしてアプリを開き、左側で **[エンティティ]** をクリックします。 

1. **[エンティティ]** ページで **[Add prebuilt entity]\(事前構築済みのエンティティを追加する\)** をクリックします。

1. **[Add prebuilt entities]\(事前構築済みエンティティの追加\)** ダイアログ ボックスで、datetimeV2 事前構築済みエンティティを選択します。 

    ![[Add prebuilt entities]\(作成済みエンティティの追加\) ダイアログ ボックス](./media/luis-use-prebuilt-entity/add-prebuilt-entity-dialog.png)

1. **[完了]** を選択します。 エンティティを追加した後、アプリをトレーニングする必要はありません。 

## <a name="publish-to-view-prebuilt-model-from-prediction-endpoint"></a>発行して予測エンドポイントからあらかじめ構築されるモデルを表示する

事前構築済みモデルの値を見る最も簡単な方法は、公開されているエンドポイントに対してクエリを行うことです。 

## <a name="marking-entities-containing-a-prebuilt-entity-token"></a>事前構築済みのエンティティ トークンを含んでいるエンティティをマークする
 カスタム エンティティとしてマークしたいテキスト (`HH-1234` など) があり、"_なおかつ_"[事前構築済みの Number](luis-reference-prebuilt-number.md) をモデルに追加した場合、LUIS ポータルでカスタム エンティティをマークすることはできません。 これをマークするためには API を使用します。 

 このタイプの (一部が事前構築済みエンティティで既にマークされている) トークンをマークするためには、その事前構築済みエンティティを LUIS アプリから削除する必要があります。 アプリをトレーニングする必要はありません。 そのうえで、独自のカスタム エンティティでトークンをマークします。 その後もう一度、事前構築済みエンティティを LUIS アプリに追加します。

 もう 1 つの例として、`I want first year spanish, second year calculus, and fourth year english lit.` という、一連の授業を選択する発話を考えてみましょう。LUIS アプリに事前構築済みの Ordinal が追加されている場合、`first`、`second`、`fourth` はあらかじめ序数でマークされます。 序数と授業をキャプチャしたい場合は、複合エンティティを作成し、事前構築済みの Ordinal と授業名のカスタム エンティティを複合エンティティで囲むようにラップしてください。

## <a name="next-steps"></a>次の手順
> [!div class="nextstepaction"]
> [REST API を使用して .csv からモデルを構築する](./luis-tutorial-node-import-utterances-csv.md)
