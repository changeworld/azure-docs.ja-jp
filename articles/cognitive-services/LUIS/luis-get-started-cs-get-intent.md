---
title: 'チュートリアル: C# を使用して Language Understanding (LUIS) アプリを呼び出す方法 | Microsoft Docs'
description: このチュートリアルでは、C# を使用して LUIS アプリを呼び出す方法について説明します。
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 12/13/2017
ms.author: v-geberr
ms.openlocfilehash: 0416d19d27810a2ab8eeb20e16b2f921fc7826ee
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/19/2018
ms.locfileid: "36263491"
---
# <a name="tutorial-call-a-luis-endpoint-using-c"></a>チュートリアル: C# を使用して LUIS エンドポイントを呼び出す

発話を LUIS エンドポイントに渡し、意図とエンティティを戻します。

<!-- green checkmark -->
> [!div class="checklist"]
> * LUIS サブスクリプションを作成し、後で使用するためにキー値をコピーする
> * ブラウザーの LUIS エンドポイントの結果をパブリック サンプル IoT アプリに対して表示する
> * LUIS エンドポイントに対して HTTPS 呼び出しを行う Visual Studio C# コンソール アプリを作成する

<!-- link to free account --> この記事に従って LUIS アプリケーションを作成するには、無料の [LUIS][LUIS] アカウントが必要です。

## <a name="create-luis-subscription-key"></a>LUIS サブスクリプション キーを作成する
1. まず Azure portal で [Cognitive Services API アカウント](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)を作成する必要があります。 Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

2. Azure Portal (https://portal.azure.com) にログインします。 

3. [Azure を使用してサブスクリプション キーを作成する](./luis-how-to-azure-subscription.md)方法に関するページの手順に従ってキーを取得します。

4. [LUIS](luis-reference-regions.md) Web サイトに戻ります。 Azure アカウントを使用してログインします。 

    [![](media/luis-get-started-cs-get-intent/app-list.png "アプリ リストのスクリーンショット")](media/luis-get-started-cs-get-intent/app-list.png)

## <a name="understand-what-luis-returns"></a>LUIS から返される内容

LUIS アプリから返される内容を理解するには、サンプル LUIS アプリの URL をブラウザー ウィンドウに貼り付けます。 サンプル アプリは、ユーザーがライトをオンにしたいかオフにしたいかを検出する IoT アプリです。

1. サンプル アプリのエンドポイントは、次の形式です。`https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2?subscription-key=<YOUR_API_KEY>&verbose=false&q=turn%20on%20the%20bedroom%20light` URL をコピーし、`subscription-key` フィールドの値を実際のサブスクリプション キーに置き換えます。
2. この URL をブラウザー ウィンドウに貼り付け、Enter キーを押します。 `HomeAutomation.TurnOn` の意図と、値が `bedroom` の `HomeAutomation.Room` エンティティが LUIS で検出されたことを示す JSON 結果がブラウザーに表示されます。

    ![意図 TurnOn が検出された JSON の結果](./media/luis-get-started-cs-get-intent/turn-on-bedroom.png)
3. URL の `q=` パラメーターの値を `turn off the living room light` に変更し、Enter キーを押します。 今度は、`HomeAutomation.TurnOff` の意図と値が `living room` の `HomeAutomation.Room` エンティティが LUIS で検出されたことを示す結果が表示されます。 

    ![意図 TurnOff が検出された JSON の結果](./media/luis-get-started-cs-get-intent/turn-off-living-room.png)


## <a name="consume-a-luis-result-using-the-endpoint-api-with-c"></a>C# でエンドポイント API を使用して LUIS の結果を利用する 

C# を使用して、前の手順でブラウザー ウィンドウに表示されたものと同じ結果にアクセスできます。 

1. Visual Studio で、新しいコンソール アプリケーションを作成します。 以下のコードをコピーして *.cs ファイルに保存します。
    
   [!code-csharp[Console app code that calls a LUIS endpoint](~/samples-luis/documentation-samples/endpoint-api-samples/csharp/Program.cs)]
1. `subscriptionKey` 変数の値は、実際の LUIS サブスクリプション キーに置き換えます。

3. Visual Studio プロジェクトで、**System.Web** への参照を追加します。

4. コンソール アプリケーションを実行します。 前の手順でブラウザー ウィンドウに表示されたものと同じ JSON が表示されます。

![コンソール ウィンドウに LUIS の JSON の結果が表示される](./media/luis-get-started-cs-get-intent/console-turn-on.png)

## <a name="clean-up-resources"></a>リソースのクリーンアップ
このチュートリアルで作成した 2 つのリソースは、LUIS サブスクリプション キーと C# プロジェクトです。 Azure portal から LUIS サブスクリプション キーを削除します。 Visual Studio プロジェクトを閉じ、ファイル システムからプロジェクトのディレクトリを削除します。 

## <a name="next-steps"></a>次の手順
> [!div class="nextstepaction"]
> [発話の追加](luis-get-started-cs-add-utterance.md)

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website