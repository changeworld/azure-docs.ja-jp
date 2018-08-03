---
title: フレーズ リストを使用して LUIS の予測を向上させる方法のチュートリアル - Azure | Microsoft Docs
description: このチュートリアルでは、LUIS アプリにフレーズ リストを追加し、スコアの改善を確認します。
services: cognitive-services
author: v-geberr
manager: kamran.iqbal
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/07/2017
ms.author: v-geberr
ms.openlocfilehash: 2fd53225a455a34d03772487a10f62a94ac86735
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/06/2018
ms.locfileid: "37868975"
---
# <a name="tutorial-add-phrase-list-to-improve-predictions"></a>チュートリアル: フレーズ リストを追加して予測を向上させる
このチュートリアルでは、代替可能な[フレーズ リスト機能](./luis-concept-feature.md)を追加することによって、意図スコアの精度を高め、同じ意味を持つ単語 (同意語) のエンティティを識別します。

> [!div class="checklist"]
* 新しいアプリをインポートする  
* 既知の発話を使用してエンドポイントにクエリを実行する 
* "_不明な_" 発話を使用してエンドポイントにクエリを実行する
* フレーズ リストを追加して不明な発話のスコアを改善する
* フレーズ リストの使用時にエンティティが検出されていることを確認する

この記事に従って LUIS アプリケーションを作成するには、無料の [LUIS][LUIS] アカウントが必要です。

## <a name="import-a-new-app"></a>新しいアプリをインポートする
1. このチュートリアル用に設計された[サンプル LUIS アプリ][LuisSampleApp]をダウンロードします。 この値は次の手順で使用します。 

2. [アプリの作成](Create-new-app.md#import-new-app)に関する記事の説明に従って、[LUIS][LUIS] Web サイトにダウンロードしたファイルを新しいアプリとしてインポートします。 アプリ名は "My Phrase List tutorial" です。 このアプリには、意図、エンティティ、発話が含まれています。 

3. アプリを[トレーニング](luis-how-to-train.md)します。 トレーニングするまで、[LUIS][LUIS] Web サイトで[対話形式でテスト](interactive-test.md#interactive-testing)することはできません。 

4. [[Publish]\(公開\)](luis-how-to-publish-app.md) ページで、**[Include all predicted intent scores]\(予測されたすべての意図スコアを含める\)** チェック ボックスをオンにします。 このチェック ボックスをオンにすると、すべての意図が返されます。 チェック ボックスをオフにすると、最上位の意図だけが返されます。 

5. アプリを[公開](luis-how-to-publish-app.md)します。 アプリを公開すると、HTTPS エンドポイントを使用してアプリをテストできるようになります。 

## <a name="test-a-trained-utterance"></a>トレーニング済みの発話をテストする
公開済みのエンドポイントを使用して、アプリが学習済みの発話のクエリを実行します。 LUIS はこの発話を既に学習しているので、スコアが高く、エンティティが検出されます。

1. [Language Understanding (LUIS)][LUIS] Web サイトで、新しいアプリの **[Publish]\(公開\)** ページの **[Resources and Keys]\(リソースとキー\)** セクションでエンドポイント URL を選択します。 

    ![エンドポイント URL を公開する](./media/luis-tutorial-interchangeable-phrase-list/luis-publish-url.png)

2. ブラウザーで、URL の末尾の `q=` の後に次のクエリを追加します。

    `I want a computer replacement`

    エンドポイントは次の JSON で応答します。
    
    ```JSON
    {
      "query": "I want a computer replacement",
      "topScoringIntent": {
        "intent": "GetHardware",
        "score": 0.9735458
      },
      "intents": [
        {
          "intent": "GetHardware",
          "score": 0.9735458
        },
        {
          "intent": "None",
          "score": 0.07053033
        },
        {
          "intent": "Whois",
          "score": 0.03760778
        },
        {
          "intent": "CheckCalendar",
          "score": 0.02285902
        },
        {
          "intent": "CheckInventory",
          "score": 0.0110072717
        }
      ],
      "entities": [
        {
          "entity": "computer",
          "type": "Hardware",
          "startIndex": 9,
          "endIndex": 16,
          "score": 0.8465915
        }
      ]
    }
    ```

    アプリはこの発話でトレーニングされているため、意図スコアが 0.973、エンティティ検出スコアが 0.846 という高いスコアになっています。 この発話は、LUIS アプリの **GetHardware** の意図ページにあります。 発話の `computer` というテキストは、**Hardware** エンティティとしてラベル付けされています。 
    
    |状態|Word| 意図スコア | エンティティ スコア |
    |--|--|--|--|
    |トレーニング済み| want | 0.973 | 0.846 |
    
    
## <a name="test-an-untrained-utterance"></a>トレーニングされていない発話をテストする
ブラウザーで同じ公開済みエンドポイントを使用して、アプリがまだ学習していない発話でクエリを実行します。

`I require a computer replacement`

この発話では、前の発話の同意語が使用されています。

| トレーニング済みの単語 | トレーニングされていない同意語 |
|--|--|
| want | require |

エンドポイントの応答は次のとおりです。

```JSON
{
  "query": "I require a computer replacement",
  "topScoringIntent": {
    "intent": "GetHardware",
    "score": 0.840912163
  },
  "intents": [
    {
      "intent": "GetHardware",
      "score": 0.840912163
    },
    {
      "intent": "None",
      "score": 0.0972757638
    },
    {
      "intent": "Whois",
      "score": 0.0448251367
    },
    {
      "intent": "CheckCalendar",
      "score": 0.0291390456
    },
    {
      "intent": "CheckInventory",
      "score": 0.0137837809
    }
  ],
  "entities": []
}
```

| 状態 | Word | 意図スコア | エンティティ スコア |
|--|--|--|--|
| トレーニング済み| want | 0.973 | 0.846 |
| トレーニングなし| require | 0.840 | - |

トレーニングされていない発話の意図スコアは、ラベル付けされた発話のスコアよりも低くなっています。これは、文が文法的に同じであることを LUIS が認識しているためです。 しかし、LUIS はこれらの発話が同じ意味であることを認識していません。 また、フレーズ リストがないと、**Hardware** エンティティは検出されません。

単語は複数の意味を持つことがあるため、このアプリ ドメインでは *want* と *require* が同じことを意味するということを LUIS に学習させる必要があります。 

## <a name="improve-the-score-of-untrained-utterance-with-phrase-list"></a>フレーズ リストを使用してトレーニングされていない発話のスコアを改善する 
1. 値 `want` が含まれた **want** という名前の[フレーズ リスト](luis-how-to-add-features.md)機能を追加し、**Enter** キーを押します。

    > [!TIP]
    > 各単語またはフレーズの後で **Enter** キーを押します。 カーソルが **[Value]\(値\)** ボックス内にある間、**[Phrase list values]\(フレーズ リストの値\)** ボックスに単語やフレーズが追加されます。 この機能により、多数の値をすばやく入力できます。

2. LUIS が推奨する単語を表示するには、**[Recommend]\(推奨\)** を選択します。 

    ![推奨値](./media/luis-tutorial-interchangeable-phrase-list/recommend.png)

3. すべての単語を追加します。 `require` が推奨リストにない場合は、必要な値として追加します。 

4. これらの単語は同意語であるため、"*代替可能*" の設定を保持し、**[保存]** をクリックします。

    ![フレーズ リストの値](./media/luis-tutorial-interchangeable-phrase-list/phrase-list-values.png)

5. 上部のナビゲーション バーで、**[Train]\(トレーニング\)** を選択してアプリをトレーニングします。ただし、公開しないでください。 これで 2 つのモデルが作成されました。 この 2 つのモデルの値を比較できます。

## <a name="compare-the-phrase-list-model-to-the-published-model"></a>フレーズ リスト モデルと公開済みのモデルを比較する
このアプリでは、公開済みのモデルは同意語でトレーニングされていません。 現在編集しているモデルにのみ、同意語のフレーズ リストが含まれています。 モデルを比較するには、[対話型テスト](interactive-test.md#interactive-testing)を使用します。 

1. **[Test]\(テスト\)** ウィンドウを開き、次の発話を入力します。

    `I require a computer replacement`

2. 検査パネルを開くには、**[検査]** を選択します。 

    ![検査を選択する](./media/luis-tutorial-interchangeable-phrase-list/inspect-button.png)

3. 公開済みのモデルと新しいフレーズ リスト モデルを比較するには、**[Compare with published]\(公開済みのものと比較\)** を選択します。

    ![公開済みのモデルと現在のモデルを検査する](./media/luis-tutorial-interchangeable-phrase-list/inspect.png)

フレーズ リストの追加後、発話の正確さが向上し、**Hardware** エンティティが検出されています。 

|状態 | フレーズ リスト| 意図スコア | エンティティ スコア |
|--|--|--|--|
| 公開先 | - | 0.84 | - |
| 現在編集中 |✔| 0.92 | Hardware エンティティを識別 |

> [!TIP]
> * [対話型テスト](interactive-test.md#interactive-testing)を使用すると、公開後に行われたトレーニング済みの変更と公開済みのモデルを比較できます。 
> * [エンドポイント テスト](luis-how-to-publish-app.md#test-your-published-endpoint-in-a-browser)を使用すると、LUIS の正確な応答 JSON を表示できます。 

## <a name="get-the-entity-score-with-the-endpoint-test"></a>エンドポイント テストでエンティティ スコアを取得する
エンティティ スコアを表示するには、[モデルを公開](luis-how-to-publish-app.md)し、エンドポイントにクエリを実行します。 

`I require a computer replacement`

```JSON
{
  "query": "I require a computer replacement",
  "topScoringIntent": {
    "intent": "GetHardware",
    "score": 0.916503668
  },
  "intents": [
    {
      "intent": "GetHardware",
      "score": 0.916503668
    },
    {
      "intent": "None",
      "score": 0.136505231
    },
    {
      "intent": "Whois",
      "score": 0.02778677
    },
    {
      "intent": "CheckInventory",
      "score": 0.0144592477
    },
    {
      "intent": "CheckCalendar",
      "score": 0.01401332
    }
  ],
  "entities": [
    {
      "entity": "computer",
      "type": "Hardware",
      "startIndex": 12,
      "endIndex": 19,
      "score": 0.5959917
    }
  ]
}
```

フレーズ リストを使用することで、**Hardware** エンティティのスコアが 0.595 になっています。 フレーズ リストが存在する前は、このエンティティは検出されませんでした。 

|状態 | フレーズ リスト| 意図スコア | エンティティ スコア |
|--|--|--|--|
| 公開先 | - | 0.84 | - |
| 現在編集中 |✔| 0.92 | 0.595 |


## <a name="clean-up-resources"></a>リソースのクリーンアップ
不要になったら、LUIS アプリを削除します。 それを行うには、アプリの一覧内のアプリ名の右にある省略記号 (***...***) を選択し、**[削除]** を選択します。 **[Delete app?]\(アプリを削除しますか?\)** ポップアップ ダイアログで、**[OK]** をクリックします。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [エンドポイント クエリによって発話予測を取得する](luis-get-started-cs-get-intent.md)

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions
[LuisFeatures]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-feature
[LuisSampleApp]: https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/phrase_list/interchangeable/luis-app-before-phrase-list.json
