---
title: クイック スタート:LUIS ポータルを使用してアプリをデプロイする
titleSuffix: Azure Cognitive Services
description: このクイックスタートでは、予測エンドポイント リソースを作成し、リソースを割り当て、アプリをトレーニング、公開することによって、アプリをデプロイする方法について説明します。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 01/27/2020
ms.author: diberry
ms.openlocfilehash: 0ee2b33aa3388b3cb99aa42c338ded800c9679a4
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/28/2020
ms.locfileid: "76772496"
---
# <a name="quickstart-deploy-an-app-in-the-luis-portal"></a>クイック スタート:LUIS ポータル内でアプリをデプロイする

LUIS アプリがクライアント アプリケーション (チャット ボットなど) に発話予測を返す準備ができたら、アプリを予測エンドポイントにデプロイする必要があります。

このクイックスタートでは、アプリケーションをデプロイする方法を学習します。 予測エンドポイント リソースを作成し、そのリソースをアプリに割り当て、アプリをトレーニングし、アプリを公開します。

## <a name="prerequisites"></a>前提条件

* [Azure サブスクリプション](https://azure.microsoft.com/free)を取得します。
* [前のポータルのクイック スタート](get-started-portal-build-app.md)を完了するか、[アプリをダウンロードして、インポート](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/quickstarts/in-portal/build-portal-app.json)します。
* Azure リソース認証より前のアプリがある場合は、[Azure リソースに移行します](luis-migration-authoring.md)。 一部のポータル ページは、メール認証が有効になっている場合に表示が異なることがあります。

## <a name="create-the-endpoint-resource"></a>エンドポイント リソースを作成する

予測エンドポイント リソースは、Azure portal 内で作成します。 このリソースは、エンドポイントの予測クエリでのみ使用します。 このアプリに変更を加えるために、このリソースを使用しないでください。

1. [Azure portal](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne) にサインインしてリソースを作成します。

1. 次の設定を使用して、サブスクリプションを構成します。

   |設定|Value|目的|
   |--|--|--|
   |Name|`my-luis-resource`|Azure リソースの名前。 LUIS ポータル内でアプリにリソースを割り当てるときに、この名前が必要です。|
   |サブスクリプション|該当するサブスクリプション|ご自分のアカウントに関連付けられているサブスクリプションの 1 つを選択します。|
   |Resource group|`my-resource-group`|お使いのすべての Cognitive Services リソース向けに新しいリソース グループを作成します。 リソースの作業が完了したら、リソース グループを削除して、サブスクリプションをクリーンアップできます。 |
   |オーサリングの場所|**[米国西部]**|作成の対象となる Azure リージョン。|
   |オーサリングの価格レベル|**F0**|作成の既定の価格レベル。|
   |ランタイムの場所|**[米国西部]**|予測エンドポイント クエリの Azure リージョン。|
   |ランタイム価格レベル|**S0**|これは、トラフィックの多い Web サイトのための価格レベルです。|
   | | | |


   ![Azure API の選択](./media/luis-how-to-azure-subscription/create-resource-in-azure.png)

1. **[作成]** を選択して、Azure リソースを作成します。

   次のセクションでは、LUIS ポータル内で LUIS アプリにこの新しいリソースを接続する方法を学習します。

## <a name="assign-the-resource-key-to-the-luis-app-in-the-luis-portal"></a>LUIS ポータル内で LUIS アプリにリソース キーを割り当てる

LUIS 向けの新しいリソースを作成するたびに、LUIS アプリにそのリソースを割り当てる必要があります。 割り当てた後は、新しいリソースを作成しない限り、この手順をもう一度実行する必要はありません。 ご自分のアプリのリージョンを拡張したり、より多くの予測クエリをサポートしたりする場合に、新しいリソースを作成します。

1. [プレビュー LUIS ポータル](https://preview.luis.ai)にサインインし、アプリの一覧から **myEnglishApp** アプリを選択します。

1. 右上のメニューの **[管理]** を選択し、 **[Azure リソース]** を選択します。

1. LUIS を追加するには、 **[Add prediction resource]\(予測リソースの追加)** を選択します。

    ![LUIS 予測リソースを追加するには、[Add prediction resource]\(予測リソースの追加\) を選択します。](./media/get-started-portal-deploy-app/azure-resources-add-prediction-resource.png)

1. ご自分のテナント、サブスクリプション、リソースの名前を選択します。 **[Assign resource]\(リソースの割り当て)** を選択します。

   ![アプリにリソースを割り当てる](./media/get-started-portal-deploy-app/assign-resource.png)

1. 同じ手順を実行して、オーサリング キーをアプリに追加します。

1. 新しい予測リソースのテーブルから新しい行を見つけて、エンドポイントの URL をコピーします。 予測のために LUIS API エンドポイントに `HTTP GET` 要求を行うことができるように正しく構築されています。

> [!TIP]
> アクティブ ラーニングを使用して LUIS アプリを改善する場合は、 **[Change query parameters]\(クエリ パラメーターの変更\)** を選択し、 **[Save logs]\(ログの保存\)** を選択します。 このアクションでは、`log=true` querystring パラメーターを追加することにより、サンプル URL を変更します。 ランタイム エンドポイントに対する予測クエリを実行するときに、変更したサンプル クエリ URL をコピーして使用します。

## <a name="train-the-app"></a>アプリをトレーニングする

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="publish-the-app-to-the-prediction-endpoint"></a>予測エンドポイントにアプリを公開する

[!INCLUDE [LUIS How to Train steps](includes/howto-publish.md)]

## <a name="prediction-endpoint-request"></a>予測エンドポイントの要求

プレビュー ポータルでは、URL の末尾の `query=` は、GET 要求に対してユーザーの発話を追加する場所です。 `query=` の後に、前のクイック スタートの最後に使用したのと同じユーザーの発話を入力します。

```Is there a form named hrf-234098```

クエリ文字列に次のペアが含まれていることを確認してください。

* `show-all-intents=true`
* `verbose=true`

ブラウザーには、次の応答が表示されます。

```JSON
{
    "query": "Is there a form named hrf-234098",
    "prediction": {
        "topIntent": "FindForm",
        "intents": {
            "FindForm": {
                "score": 0.9768753
            },
            "None": {
                "score": 0.0216071177
            }
        },
        "entities": {
            "Human Resources Form Number": [
                "hrf-234098"
            ],
            "$instance": {
                "Human Resources Form Number": [
                    {
                        "type": "Human Resources Form Number",
                        "text": "hrf-234098",
                        "startIndex": 22,
                        "length": 10,
                        "modelTypeId": 8,
                        "modelType": "Regex Entity Extractor",
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

テスト ウィンドウにこれと同じレベルの情報を表示する場合は、アプリを公開する必要があります。 アプリが公開されたら、テスト ウィンドウで、 **[Compare with published]\(公開済みのものと比較\)** を選択します。 公開済みのテスト ウィンドウの **[Show JSON view]\(JSON ビューの表示\)** を使用して、前の手順と同じ JSON を表示します。 このようにして、現在作業中のアプリに対する変更と、エンドポイントに公開されているアプリとを比較できます。

[![アプリの現在編集中のバージョンと公開済みのバージョンを比較する](./media/get-started-portal-deploy-app/compare-test-pane.png)](./media/get-started-portal-deploy-app/compare-test-pane.png#lightbox)

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このクイックスタートが完了したら、上部のナビゲーション メニューから **[マイ アプリ]** を選択します。 一覧からアプリのチェック ボックスをオンにし、一覧の上にあるコンテキスト ツール バーから **[削除]** を選択します。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [一般的な意図とエンティティを識別する](luis-tutorial-prebuilt-intents-entities.md)
