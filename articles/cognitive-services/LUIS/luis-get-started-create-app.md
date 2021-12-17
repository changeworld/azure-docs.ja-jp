---
title: 'クイックスタート: LUIS ポータルでアプリをビルドする'
description: このクイックスタートでは、照明やアプライアンスの電源をオンにしたりオフにしたりする用途を想定し、事前構築済みのドメイン `HomeAutomation` を使用した LUIS アプリを作成する方法について説明します。 この事前構築済みのドメインによって、意図、エンティティ、発話例が得られます。 完成すると、クラウド内で LUIS エンドポイントが実行されるようになります。
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 10/20/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 70972607788568fbc06f9efe06fe083fd8d8ac01
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131073626"
---
# <a name="quickstart-build-your-app-in-luis-portal"></a>クイックスタート: LUIS ポータルでアプリをビルドする

> [!NOTE]
> 新しいバージョンの Language Understanding 機能が、Azure Cognitive Service for Language の一部として使用できるようになりました。 詳しくは、[Azure Cognitive Service for Language のドキュメント](../language-service/index.yml)参照してください。 Language Service 内の言語理解機能については、「[会話言語の理解](../language-service/conversational-language-understanding/overview.md)」、「[カスタム固有表現認識](../language-service/custom-named-entity-recognition/overview.md)」、「[カスタム分類](../language-service/custom-classification/overview.md)」を参照してください。

このクイックスタートでは、照明や家電製品のオンとオフを切り替えるために、あらかじめ構築されたホーム オートメーション ドメインを使用して LUIS アプリを作成します。 この事前構築済みのドメインによって、意図、エンティティ、発話例が得られます。 次に、さらに意図とエンティティを追加することで、アプリをカスタマイズしてみます。 完成すると、クラウド内で LUIS エンドポイントが実行されるようになります。

[!INCLUDE [Sign in to LUIS](./includes/sign-in-process.md)]

[!INCLUDE [Select authoring resource](./includes/select-authoring-resource.md)]

## <a name="create-a-new-app"></a>新しいアプリの作成

アプリケーションは、 **[My Apps]** で作成および管理できます。

### <a name="create-an-application"></a>アプリケーションの作成

アプリケーションを作成するには、 **[+ 新しいアプリ]** をクリックします。 

表示されたウィンドウで、次の情報を入力します。

|名前  |説明  |
|---------|---------|
|名前     | アプリの名前。 たとえば、"home automation"。        |
|カルチャ     | アプリによって認識され、話される言語。   |
|説明 | アプリの説明。
|予測リソース | クエリを受け取る予測リソース。 |

**[完了]** を選択します。

>[!NOTE]
>カルチャは、アプリケーションを作成した後に変更できません。

## <a name="add-prebuilt-domain"></a>事前構築済みのドメインの追加

LUIS には、アプリケーションの作成を開始するために役立つ、あらかじめ構築されたドメインのセットが用意されています。 構築済みドメイン アプリには、既に[意図](./luis-concept-intent.md)、[エンティティ](./luis-concept-entity-types.md)、および[発話](./luis-concept-utterance.md)が設定されています。

1. 左側のナビゲーションで **[Prebuilt Domains]\(事前構築済みドメイン\)** を選択します。
2. **HomeAutomation** を検索します。
3. [HomeAutomation] カードで、 **[Add domain]** を選択します。

    > [!div class="mx-imgBorder"]
    > ![[事前構築済みのドメイン] を選択し、"HomeAutomation" を検索します。 HomeAutomation カードで [ドメインの追加] を選択します。](media/luis-quickstart-new-app/home-automation.png)

    ドメインが正常に追加されると、事前構築済みドメインのボックスに、 **[Remove domain]\(ドメインの削除\)** ボタンが表示されます。

## <a name="check-out-intents-and-entities"></a>意図とエンティティを確認する

1. 左側のナビゲーション メニューから **[Intents]\(意図\)** を選択し、HomeAutomation ドメインの意図を確認します。 `HomeAutomation.QueryState` や `HomeAutomation.SetDevice` などの発話の例があります。

    > [!NOTE]
    > **[None]\(なし\)** は、すべての LUIS アプリに用意されている意図です。 これは自分のアプリの機能に対応しない発話を処理する目的で使用されます。

2. **[HomeAutomation.TurnOff]** 意図を選択します。 意図には、エンティティでラベル付けされている発話例の一覧が含まれています。

    > [!div class="mx-imgBorder"]
    > [![HomeAutomation.TurnOff 意図のスクリーンショット](media/luis-quickstart-new-app/home-automation-turnoff.png "HomeAutomation.TurnOff 意図のスクリーンショット")](media/luis-quickstart-new-app/home-automation-turnoff.png)

3. アプリのエンティティを表示する場合は、 **[エンティティ]** を選択します。 いずれかのエンティティ (**HomeAutomation.DeviceName** など) をクリックすると、それに関連付けられている値の一覧が表示されます。 
 
    :::image type="content" source="media/luis-quickstart-new-app/entities-page.png" alt-text="画像の代替テキスト" lightbox="media/luis-quickstart-new-app/entities-page.png":::

## <a name="train-the-luis-app"></a>LUIS アプリをトレーニングする
アプリケーションに意図、エンティティ、および発話を設定した後、行った変更が反映されるように、アプリケーションをトレーニングする必要があります。

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="test-your-app"></a>アプリをテストする

アプリのトレーニング後、そのテストを行うことができます。

1. 右上のナビゲーションから **[テスト]** を選択します。

1. 対話型のテスト ペインにテスト発話を入力し、Enter キーを押します。 たとえば、"*Turn off the lights (照明を消して)* " など。

    この例では、**HomeAutomation.TurnOff** に対する最もスコアの高い意図として "*Turn off the lights*" が正しく識別されています。

    :::image type="content" source="media/luis-quickstart-new-app/review-test-inspection-pane-in-portal.png" alt-text="発話が強調表示されたテスト パネルのスクリーンショット" lightbox="media/luis-quickstart-new-app/review-test-inspection-pane-in-portal.png":::

1. **[検査]** を選択して、その予測の詳細を表示します。

    :::image type="content" source="media/luis-quickstart-new-app/test.png" alt-text="検査情報を含むテスト パネルのスクリーンショット" lightbox="media/luis-quickstart-new-app/test.png":::

1. テスト ウィンドウを閉じます。

## <a name="customize-your-application"></a>アプリケーションのカスタマイズ

あらかじめ構築されたドメイン LUIS を使用すると、独自のカスタム アプリケーションを作成したり、あらかじめ構築されたアプリケーションを基にしてカスタマイズしたりすることもできます。

### <a name="create-intents"></a>意図を作成する

アプリにさらに意図を追加するには

1. 左側のナビゲーション メニューで、 **[Intents]\(意図\)** を選択します。
2. **[作成]**
3. 意図の名前として「`HomeAutomation.AddDeviceAlias`」と入力し、[完了] を選択します。

### <a name="create-entities"></a>エンティティを作成する

アプリにさらにエンティティを追加するには

1. 左側のナビゲーション メニューの **[Entities]\(エンティティ\)** を選択します。
2. **[作成]**
3. 意図名の「`HomeAutomation.DeviceAlias`」を入力し、 **[種類]** で機械学習を選択して、 **[作成]** を選択します。

### <a name="add-example-utterances"></a>発話の例を追加する

発話の例は、ユーザーがチャット ボットや他のクライアント アプリケーションに入力するテキストです。 ユーザーのテキストの意図を、LUIS 意図にマッピングします。

`HomeAutomation.AddDeviceAlias` の **[意図]** ページで、 **[発話の例]** の下に次の発話例を追加します。

|#|発話の例|
|--|--|
|1|`Add alias to my fan to be wind machine`|
|2|`Alias lights to illumination`|
|3|`nickname living room speakers to our speakers a new fan`|
|4|`rename living room tv to main tv`|

[!INCLUDE [best-practice-utterances](./includes/best-practice-utterances.md)]


### <a name="label-example-utterances"></a>ラベルの発話の例

ML エンティティを追加したため、発話のラベル付けが必要です。 ラベル付けは、作成した ML エンティティを抽出する方法を学習するために、アプリケーションによって使用されます。

[!INCLUDE [how-to-label](./includes/how-to-label.md)]

## <a name="create-prediction-resource"></a>予測リソースを作成する
この時点で、アプリケーションの作成は完了しています。 予測エンドポイントを介してチャット ボットまたは他のクライアント アプリケーションで予測を受信するためには、アプリケーションを発行するための予測リソースを作成する必要があります

LUIS ポータルで予測リソースを作成するには

[!INCLUDE [add-pred-resource-portal](./includes/add-prediction-resource-portal.md)]


## <a name="publish-the-app-to-get-the-endpoint-url"></a>アプリを公開してエンドポイント URL を取得する

[!INCLUDE [LUIS How to Publish steps](./includes/howto-publish.md)]



## <a name="query-the-v3-api-prediction-endpoint"></a>V3 API 予測エンドポイントに対してクエリを実行する

[!INCLUDE [LUIS How to get endpoint first step](./includes/v3-prediction-endpoint.md)]

2. ブラウザーのアドレス バーで、クエリ文字列について、URL に次の値が含まれていることを確認します。 それらがクエリ文字列に含まれていない場合は、追加してください。

    * `verbose=true`
    * `show-all-intents=true`

3. ブラウザーのアドレス バーで、URL の最後に移動し、クエリ文字列として「*turn off the living room light*」と入力して、Enter キーを押します。

    ```json
    {
        "query": "turn off the living room light",
        "prediction": {
            "topIntent": "HomeAutomation.TurnOff",
            "intents": {
                "HomeAutomation.TurnOff": {
                    "score": 0.969448864
                },
                "HomeAutomation.QueryState": {
                    "score": 0.0122336326
                },
                "HomeAutomation.TurnUp": {
                    "score": 0.006547436
                },
                "HomeAutomation.TurnDown": {
                    "score": 0.0050634006
                },
                "HomeAutomation.SetDevice": {
                    "score": 0.004951761
                },
                "HomeAutomation.TurnOn": {
                    "score": 0.00312553928
                },
                "None": {
                    "score": 0.000552945654
                }
            },
            "entities": {
                "HomeAutomation.Location": [
                    "living room"
                ],
                "HomeAutomation.DeviceName": [
                    [
                        "living room light"
                    ]
                ],
                "HomeAutomation.DeviceType": [
                    [
                        "light"
                    ]
                ],
                "$instance": {
                    "HomeAutomation.Location": [
                        {
                            "type": "HomeAutomation.Location",
                            "text": "living room",
                            "startIndex": 13,
                            "length": 11,
                            "score": 0.902181149,
                            "modelTypeId": 1,
                            "modelType": "Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ],
                    "HomeAutomation.DeviceName": [
                        {
                            "type": "HomeAutomation.DeviceName",
                            "text": "living room light",
                            "startIndex": 13,
                            "length": 17,
                            "modelTypeId": 5,
                            "modelType": "List Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ],
                    "HomeAutomation.DeviceType": [
                        {
                            "type": "HomeAutomation.DeviceType",
                            "text": "light",
                            "startIndex": 25,
                            "length": 5,
                            "modelTypeId": 5,
                            "modelType": "List Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ]
                }
            }
        }
    }
    ```

[V3 予測エンドポイント](luis-migration-api-v3.md)の詳細について学習します。


## <a name="clean-up-resources"></a>リソースをクリーンアップする

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>次の手順

* [反復的アプリ設計](./luis-concept-app-iteration.md)
* [ベスト プラクティス](./luis-concept-best-practices.md)
