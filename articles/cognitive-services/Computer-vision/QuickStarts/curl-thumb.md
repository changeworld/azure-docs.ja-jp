---
title: クイック スタート:サムネイルの生成 - REST、cURL
titleSuffix: Azure Cognitive Services
description: このクイック スタートでは、Computer Vision API と cURL を使って、画像からサムネイルを生成します。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 05/20/2020
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: d6b55a7f3df7657e2b6b0b23b797d36788658f32
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/21/2020
ms.locfileid: "83745162"
---
# <a name="quickstart-generate-a-thumbnail-using-the-computer-vision-rest-api-and-curl"></a>クイック スタート:Computer Vision の REST API と cURL を使用したサムネイルを生成する

このクイックスタートでは、Computer Vision の REST API を使って、画像からサムネイルを生成します。 その際に指定する目的の高さと幅は、入力画像の縦横比と異なっていてもかまいません。 Computer Vision では、スマート トリミングを使ってインテリジェントに関心領域を識別し、その領域を中心にしたトリミングの座標を生成します。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/ai/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cognitive-services) を作成してください。

## <a name="prerequisites"></a>前提条件

- [cURL](https://curl.haxx.se/windows) が必要です。
- Computer Vision のサブスクリプション キーが必要です。 無料試用版のキーは「[Cognitive Services を試す](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision)」から取得できます。 または、[Cognitive Services アカウントの作成](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)に関するページの手順に従って、Computer Vision をサブスクライブし、キーを取得します。

## <a name="create-and-run-the-sample-command"></a>サンプル コマンドの作成と実行

このサンプルを作成して実行するには、次の手順を実行します。

1. テキスト エディターに次のコマンドをコピーします。
1. 必要に応じて、コマンドに次の変更を加えます。
    1. `<subscriptionKey>` 値を、サブスクリプション キーに置き換えます。
    1. サムネイルを保存するファイルのパスと名前に、`<thumbnailFile>` の値を置き換えます。
    1. 要求 URL (`westcentralus`) の最初の部分を独自のエンドポイント URL 内のテキストに置き換えます。
        [!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]
    1. 必要に応じて、要求本文の画像 URL (`https://upload.wikimedia.org/wikipedia/commons/thumb/5/56/Shorkie_Poo_Puppy.jpg/1280px-Shorkie_Poo_Puppy.jpg\`) を、サムネイルの生成元である別の画像の URL に変更します。
1. コマンド プロンプト ウィンドウを開きます。
1. テキスト エディターからコマンド プロンプト ウィンドウにコマンドを貼り付けます。
1. Enter キーを押して、プログラムを実行します。

    ```bash
    curl -H "Ocp-Apim-Subscription-Key: <subscriptionKey>" -o <thumbnailFile> -H "Content-Type: application/json" "https://westus.api.cognitive.microsoft.com/vision/v3.0/generateThumbnail?width=100&height=100&smartCropping=true" -d "{\"url\":\"https://upload.wikimedia.org/wikipedia/commons/thumb/5/56/Shorkie_Poo_Puppy.jpg/1280px-Shorkie_Poo_Puppy.jpg\"}"
    ```

## <a name="examine-the-response"></a>結果の確認

成功応答では、`<thumbnailFile>` で指定されたファイルにサムネイル画像が書き込まれます。 要求が失敗した場合は、原因の特定につながるエラー コードとメッセージが応答に格納されます。 要求が成功したように見えても、作成されたサムネイルが有効な画像ファイルではない場合は、サブスクリプション キーが有効でない可能性があります。

## <a name="next-steps"></a>次のステップ

画像の分析、著名人やランドマークの検出、サムネイルの作成、印刷されたテキストや手書きテキストの抽出を実行する方法の Computer Vision API の詳細を確認します。 Computer Vision API を簡単に試す場合は、[Open API テスト コンソール](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/56f91f2e778daf14a499f20c)をお試しください。

> [!div class="nextstepaction"]
> [Computer Vision API の詳細を確認する](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/56f91f2e778daf14a499f21b)
