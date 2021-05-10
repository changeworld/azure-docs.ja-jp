---
title: 'クイックスタート: アプリの作成 - LUIS'
description: このクイックスタートでは、照明やアプライアンスの電源をオンにしたりオフにしたりする用途を想定し、事前構築済みのドメイン `HomeAutomation` を使用した LUIS アプリを作成する方法について説明します。 この事前構築済みのドメインによって、意図、エンティティ、発話例が得られます。 完成すると、クラウド内で LUIS エンドポイントが実行されるようになります。
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 03/29/2021
ms.openlocfilehash: e0972998fad647c1c70be814cb7c13f14a6e1141
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/03/2021
ms.locfileid: "106277030"
---
# <a name="quickstart-use-prebuilt-home-automation-app"></a>クイック スタート:事前構築済みの Home Automation アプリを使用する

このクイック スタートでは、照明やアプライアンスの電源をオンにしたりオフにしたりする用途を想定し、事前構築済みのドメイン `HomeAutomation` を使用した LUIS アプリを作成します。 この事前構築済みのドメインによって、意図、エンティティ、発話例が得られます。 完成すると、クラウド内で LUIS エンドポイントが実行されるようになります。

[!INCLUDE [Sign in to LUIS](./includes/sign-in-process.md)]

[!INCLUDE [Select authoring resource](./includes/select-authoring-resource.md)]

## <a name="create-a-new-app"></a>新しいアプリの作成
アプリケーションは、 **[My Apps]** で作成および管理できます。

### <a name="create-an-application"></a>アプリケーションの作成

アプリケーションを作成するには、 **[+ 新しいアプリ]** をクリックします。 

表示されたウィンドウで、次の情報を入力します。

|名前  |説明  |
|---------|---------|
|AName     | アプリの名前。 たとえば、"home automation" など。        |
|カルチャ     | アプリによって認識され、話される言語。   |
|説明 | アプリの説明。
|予測リソース | クエリを受け取る予測リソース。 |

**[完了]** を選択します。

>[!NOTE]
>カルチャは、アプリケーションを作成した後に変更できません。

## <a name="add-prebuilt-domain"></a>事前構築済みのドメインの追加

1. 左側のナビゲーションで **[Prebuilt Domains]\(事前構築済みドメイン\)** を選択します。
1. **HomeAutomation** を検索します。
1. [HomeAutomation] カードで、 **[Add domain]** を選択します。

    > [!div class="mx-imgBorder"]
    > ![[事前構築済みのドメイン] を選択し、"HomeAutomation" を検索します。 HomeAutomation カードで [ドメインの追加] を選択します。](media/luis-quickstart-new-app/home-automation.png)

    ドメインが正常に追加されると、事前構築済みドメインのボックスに、 **[Remove domain]\(ドメインの削除\)** ボタンが表示されます。

## <a name="intents-and-entities"></a>意図とエンティティ

1. 左側のナビゲーション メニューから **[Intents]\(意図\)** を選択し、HomeAutomation ドメインの意図を確認します。 `HomeAutomation.QueryState` や `HomeAutomation.SetDevice` などの発話の例があります。

    > [!NOTE]
    > **[None]\(なし\)** は、すべての LUIS アプリに用意されている意図です。 これは自分のアプリの機能に対応しない発話を処理する目的で使用されます。

1. **[HomeAutomation.TurnOff]** 意図を選択します。 意図には、エンティティでラベル付けされている発話例の一覧が含まれています。

    > [!div class="mx-imgBorder"]
    > [![HomeAutomation.TurnOff 意図のスクリーンショット](media/luis-quickstart-new-app/home-automation-turnoff.png "HomeAutomation.TurnOff 意図のスクリーンショット")](media/luis-quickstart-new-app/home-automation-turnoff.png)

1. アプリのエンティティを表示する場合は、 **[エンティティ]** を選択します。 いずれかのエンティティ (**HomeAutomation.DeviceName** など) をクリックすると、それに関連付けられている値の一覧が表示されます。 
 
    :::image type="content" source="media/luis-quickstart-new-app/entities-page.png" alt-text="画像の代替テキスト" lightbox="media/luis-quickstart-new-app/entities-page.png":::

## <a name="train-the-luis-app"></a>LUIS アプリをトレーニングする

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="test-your-app"></a>アプリをテストする
アプリのトレーニング後、そのテストを行うことができます。

1. 右上のナビゲーションから **[テスト]** を選択します。

1. 対話型のテスト ウィンドウに `Turn off the lights` などのテスト発話を入力し、Enter キーを押します。 たとえば、"*Turn off the lights (照明を消して)* " など。

    この例では、**HomeAutomation.TurnOff** に対する最もスコアの高い意図として `Turn off the lights` が正しく識別されています。

    ![発話が強調表示されたテスト パネルのスクリーンショット](media/luis-quickstart-new-app/review-test-inspection-pane-in-portal.png)

1. **[検査]** を選択して、その予測の詳細を表示します。

    > [!div class="mx-imgBorder"]
    > ![検査情報を含むテスト パネルのスクリーンショット](media/luis-quickstart-new-app/test.png)

1. テスト ウィンドウを閉じます。

<a name="publish-your-app"></a>

## <a name="publish-the-app-to-get-the-endpoint-url"></a>アプリを公開してエンドポイント URL を取得する

チャットボットや他のクライアント アプリケーションで LUIS の予測を受け取るには、アプリを予測エンドポイントに公開する必要があります。

1. ウィンドウの右上で **[公開]** を選択します。

1. **[運用]** スロットを選択し、 **[完了]** を選択します。

    > [!div class="mx-imgBorder"]
    > ![LUIS のエンドポイントへの公開のスクリーンショット](media/howto-publish/publish-app-popup.png)

1. 通知の **[Access your endpoint URLs]\(エンドポイントの URL にアクセス\)** リンクを選択して **[Azure リソース]** ページに移動します。 エンドポイントの URL が **[Example Query]\(サンプル クエリ\)** として一覧表示されます。

<!-- [!INCLUDE [LUIS How to Publish steps](./includes/howto-publish.md)] -->

<a name="query-the-v2-api-prediction-endpoint"></a>

## <a name="query-the-v3-api-prediction-endpoint"></a>V3 API 予測エンドポイントに対してクエリを実行する

[!INCLUDE [LUIS How to get endpoint first step](./includes/v3-prediction-endpoint.md)]

2. ブラウザーのアドレス バーで、クエリ文字列について、URL に次の名前と値のバーが含まれていることを確認します。 それらがクエリ文字列に含まれていない場合は、追加してください。

    |名前と値のペア|
    |--|
    |`verbose=true`|
    |`show-all-intents=true`|

3. ブラウザーのアドレス バーで、URL の最後に移動し、_クエリ_ 値に「`turn off the living room light`」と入力して Enter キーを押します。

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

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [LUIS の反復的アプリ開発](./luis-concept-app-iteration.md)