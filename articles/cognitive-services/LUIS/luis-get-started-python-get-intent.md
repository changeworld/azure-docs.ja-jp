---
title: 'クイック スタート: Python を使用して Language Understanding (LUIS) アプリを呼び出す方法 | Microsoft Docs'
description: このクイック スタートでは、Python を使用して LUIS アプリを呼び出す方法について説明します。
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: quickstart
ms.date: 06/27/2018
ms.author: diberry
ms.openlocfilehash: bc7ae912d762a98c34b9a1b2d6a82d5630c4794b
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/24/2018
ms.locfileid: "43771756"
---
# <a name="quickstart-call-a-luis-endpoint-using-python"></a>クイック スタート: Python を使って LUIS エンドポイントを呼び出す
このクイック スタートでは、LUIS エンドポイントに発話を渡して、意図とエンティティを取得します。

<!-- green checkmark -->
<!--
> [!div class="checklist"]
> * Create LUIS subscription and copy key value for later use
> * View LUIS endpoint results from browser to public sample IoT app
> * Create Visual Studio C# console app to make HTTPS call to LUIS endpoint
-->

この記事に従って LUIS アプリケーションを作成するには、無料の [LUIS](luis-reference-regions.md#luis-website) アカウントが必要です。

<a name="create-luis-subscription-key"></a>
## <a name="create-luis-endpoint-key"></a>LUIS エンドポイント キーの作成
このチュートリアルで使用されるサンプル LUIS アプリを呼び出すには、Cognitive Services API キーが必要です。 

API キーを取得するには、次の手順を実行します。 

1. まず Azure portal で [Cognitive Services API アカウント](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)を作成する必要があります。 Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

2. Azure Portal (https://portal.azure.com) にログインします。 

3. [Azure を使用してエンドポイント キーを作成する](./luis-how-to-azure-subscription.md)方法に関するページの手順に従ってキーを取得します。

4. [LUIS](luis-reference-regions.md) Web サイトに戻り、Azure アカウントを使用してログインします。 

    [![](media/luis-get-started-node-get-intent/app-list.png "アプリ リストのスクリーンショット")](media/luis-get-started-node-get-intent/app-list.png)

## <a name="understand-what-luis-returns"></a>LUIS から返される内容

LUIS アプリから返される内容を理解するには、サンプル LUIS アプリの URL をブラウザー ウィンドウに貼り付けます。 サンプル アプリは、ユーザーがライトをオンにしたいかオフにしたいかを検出する IoT アプリです。

1. サンプル アプリのエンドポイントは、次の形式です。`https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2?subscription-key=<YOUR_API_KEY>&verbose=false&q=turn%20on%20the%20bedroom%20light` URL をコピーし、`subscription-key` フィールドの値を実際のエンドポイント キーに置き換えます。
2. この URL をブラウザー ウィンドウに貼り付け、Enter キーを押します。 `HomeAutomation.TurnOn` の意図と、値が `bedroom` の `HomeAutomation.Room` エンティティが LUIS で検出されたことを示す JSON 結果がブラウザーに表示されます。

    ![意図 TurnOn が検出された JSON の結果](./media/luis-get-started-node-get-intent/turn-on-bedroom.png)
3. URL の `q=` パラメーターの値を `turn off the living room light` に変更し、Enter キーを押します。 今度は、`HomeAutomation.TurnOff` の意図と値が `living room` の `HomeAutomation.Room` エンティティが LUIS で検出されたことを示す結果が表示されます。 

    ![意図 TurnOff が検出された JSON の結果](./media/luis-get-started-node-get-intent/turn-off-living-room.png)


## <a name="consume-a-luis-result-using-the-endpoint-api-with-python"></a>Python でエンドポイント API を使用して LUIS の結果を利用する

Python を使用して、前の手順でブラウザー ウィンドウに表示されたものと同じ結果にアクセスできます。

1. 次のコード スニペットのいずれかを、`quickstart-call-endpoint.py` というファイルにコピーします。

   [!code-python[Console app code that calls a LUIS endpoint for Python 2.7](~/samples-luis/documentation-samples/quickstarts/analyze-text/python/2.x/quickstart-call-endpoint-2-7.py)]

   [!code-python[Console app code that calls a LUIS endpoint for Python 3.6](~/samples-luis/documentation-samples/quickstarts/analyze-text/python/3.x/quickstart-call-endpoint-3-6.py)]

2. `Ocp-Apim-Subscription-Key` フィールドの値を実際の LUIS エンドポイント キーに置き換えます。

3. `pip install requests` で依存関係をインストールします。

4. `python ./quickstart-call-endpoint.py` でスクリプトを実行します。 前の手順でブラウザー ウィンドウに表示されたものと同じ JSON が表示されます。
<!-- 
![Console window displays JSON result from LUIS](./media/luis-get-started-python-get-intent/console-turn-on.png)
-->

## <a name="clean-up-resources"></a>リソースのクリーンアップ
このチュートリアルで作成した 2 つのリソースは、LUIS エンドポイント キーと C# プロジェクトです。 Azure portal から LUIS エンドポイント キーを削除します。 Visual Studio プロジェクトを閉じ、ファイル システムからプロジェクトのディレクトリを削除します。 

## <a name="next-steps"></a>次の手順
> [!div class="nextstepaction"]
> [発話の追加](luis-get-started-python-add-utterance.md)