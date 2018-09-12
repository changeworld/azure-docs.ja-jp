---
title: 'Computer Vision API cURL クイック スタート: サムネイルの取得 | Microsoft Docs'
titleSuffix: Microsoft Cognitive Services
description: このクイック スタートでは、Cognitive Services の Computer Vision と cURL を使って、画像からサムネイルを生成します。
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: v-deken
ms.openlocfilehash: 4056f05f3f4bf97761f683b5f3a9053666d4ea26
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/31/2018
ms.locfileid: "43771939"
---
# <a name="quickstart-generate-a-thumbnail---rest-curl"></a>クイック スタート: サムネイルの生成 - REST、cURL

このクイック スタートでは、Computer Vision を使って、画像からサムネイルを生成します。

## <a name="prerequisites"></a>前提条件

Computer Vision を使用するにはサブスクリプション キーが必要です。「[サブスクリプション キーを取得する](../Vision-API-How-to-Topics/HowToSubscribe.md)」をご覧ください。

## <a name="get-thumbnail-request"></a>サムネイル取得要求

[サムネイル取得メソッド](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fb)を使うと、画像のサムネイルを生成することができます。 その際に指定する高さと幅は、入力画像の縦横比と異なっていてもかまいません。 Computer Vision は、スマート トリミングを使ってインテリジェントに関心領域を識別し、その領域に基づいてトリミングの座標を生成します。

このサンプルを実行するには、次の手順を実行します。

1. エディターに次のコードをコピーします。
1. `<Subscription Key>` を、有効なサブスクリプション キーに置き換えます。
1. `<File>` を、サムネイルの保存先のパスとファイル名に置き換えます。
1. 必要に応じて、サブスクリプション キーの取得場所を使用するように要求 URL (`https://westcentralus.api.cognitive.microsoft.com/vision/v2.0`) を変更します。
1. 必要に応じて、分析対象の画像 (`{\"url\":\"...`) を変更します。
1. cURL がインストールされているコンピューターのコマンド ウィンドウを開きます。
1. ウィンドウにコードを貼り付けて、コマンドを実行します。

>[!NOTE]
>REST 呼び出しには、サブスクリプション キーの取得に使用したのと同じ場所を使用する必要があります。 たとえば、サブスクリプション キーを westus から取得した場合、下記の URL の "westcentralus" を "westus" に置き換えます。

```json
curl -H "Ocp-Apim-Subscription-Key: <Subscription Key>" -o <File> -H "Content-Type: application/json" "https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/generateThumbnail?width=100&height=100&smartCropping=true" -d "{\"url\":\"https://upload.wikimedia.org/wikipedia/commons/thumb/5/56/Shorkie_Poo_Puppy.jpg/1280px-Shorkie_Poo_Puppy.jpg\"}"
```

## <a name="get-thumbnail-response"></a>サムネイル取得応答

成功時の応答では、サムネイル画像が `<File>` に書き込まれます。 要求が失敗した場合は、原因の特定につながるエラー コードとメッセージが応答に格納されます。

## <a name="next-steps"></a>次の手順

画像の分析、著名人やランドマークの検出、サムネイルの作成、印刷されたテキストや手書きテキストの抽出に使用される Computer Vision API の詳細を確認します。 Computer Vision API を簡単に試す場合は、[Open API テスト コンソール](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console)をお試しください。

> [!div class="nextstepaction"]
> [Computer Vision API の詳細](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
