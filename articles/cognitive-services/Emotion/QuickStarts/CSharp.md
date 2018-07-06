---
title: Emotion API C# クイック スタート | Microsoft Docs
description: Cognitive Services で Emotion API と C# を使用することで、素早く開始するために役立つ情報とコード サンプルを取得します。
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: emotion-api
ms.topic: article
ms.date: 11/02/2017
ms.author: anroth
ms.openlocfilehash: 89735ae54395447e3cb421f45db3d6b99001ecd6
ms.sourcegitcommit: 0fa8b4622322b3d3003e760f364992f7f7e5d6a9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/27/2018
ms.locfileid: "37016567"
---
# <a name="emotion-api-c-quick-start"></a>Emotion API C# クイック スタート

> [!IMPORTANT]
> Video API のプレビューは 2017 年 10 月 30 日に終了しました。 ビデオから分析情報を簡単に抽出するには、新しい [Video Indexer API のプレビュー](https://azure.microsoft.com/services/cognitive-services/video-indexer/)を試してください。 それを使用して、話し言葉、顔、文字、感情の検出による検索結果などのコンテンツ検出を強化することもできます。 詳細については、[Video Indexer のプレビュー](https://docs.microsoft.com/azure/cognitive-services/video-indexer/video-indexer-overview)の概要を参照してください。

この記事では、[Emotion API の Recognize メソッド](https://westus.dev.cognitive.microsoft.com/docs/services/5639d931ca73072154c1ce89/operations/563b31ea778daf121cc3a5fa) と C# を使用することで、素早く開始するために役立つ情報とコード サンプルを取得します。 それを使用して、画像内の 1 人または複数の人物が表している感情を認識できます。 

## <a name="prerequisites"></a>前提条件
* Cognitive Services [Emotion API Windows SDK](https://www.nuget.org/packages/Microsoft.ProjectOxford.Emotion/)の取得。
* 無料[サブスクリプション キー](https://azure.microsoft.com/try/cognitive-services/)の取得。

## <a name="emotion-recognition-c-example-request"></a>感情認識 C# の要求例

Visual Studio で新しいコンソール ソリューションを作成した後、Program.cs を次のコードに置き換えます。 `string uri` を、サブスクリプション キーを取得したリージョンを使用するように変更します。 **Ocp-Apim-Subscription-Key** 値を、有効なサブスクリプション キーに置き換えます。 サブスクリプションを見つけるには、Azure Portal に移動します。 左側のナビゲーション ウィンドウの **[キー]** セクションで、お使いの Emotion API リソースを参照します。 同様に、**[エンドポイント]** の下に表示されるリソースの **[概要]** ウィンドウで、適切な接続 URI を取得できます。

![API リソース キー](../../media/emotion-api/keys.png)

要求の応答を処理するには、`Newtonsoft.Json` などのライブラリを使用します。 この方法で、JSON 文字列を、トークンと呼ばれる一連の管理可能オブジェクトとして処理できます。 このライブラリをパッケージに追加するには、ソリューション エクスプローラーでプロジェクトを右クリックし、**[NuGet パッケージの管理]** を選択します。 次に、**Newtonsoft** を検索します。 最初の結果は、**Newtonsoft.Json** になるはずです。 **[インストール]** を選択します。 これで、アプリケーションでこのライブラリを参照できます。

![Newtonsoft.Json のインストール](../../media/emotion-api/newtonsoft-nuget.png)

```csharp
using System;
using System.IO;
using System.Net.Http.Headers;
using System.Net.Http;
using Newtonsoft.Json.Linq;

namespace CSHttpClientSample
{
    static class Program
    {
        static void Main()
        {
            Console.Write("Enter the path to a JPEG image file:");
            string imageFilePath = Console.ReadLine();

            MakeRequest(imageFilePath);

            Console.WriteLine("\n\n\nWait for the result below, then hit ENTER to exit...\n\n\n");
            Console.ReadLine(); // wait for ENTER to exit program
        }

        static byte[] GetImageAsByteArray(string imageFilePath)
        {
            FileStream fileStream = new FileStream(imageFilePath, FileMode.Open, FileAccess.Read);
            BinaryReader binaryReader = new BinaryReader(fileStream);
            return binaryReader.ReadBytes((int)fileStream.Length);
        }

        static async void MakeRequest(string imageFilePath)
        {
            var client = new HttpClient();

            // Request headers - replace this example key with your valid key.
            client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", "<your-subscription-key>"); // 

            // NOTE: You must use the same region in your REST call as you used to obtain your subscription keys.
            //   For example, if you obtained your subscription keys from westcentralus, replace "westus" in the 
            //   URI below with "westcentralus".
            string uri = "https://westus.api.cognitive.microsoft.com/emotion/v1.0/recognize?";
            HttpResponseMessage response;
            string responseContent;

            // Request body. Try this sample with a locally stored JPEG image.
            byte[] byteData = GetImageAsByteArray(imageFilePath);

            using (var content = new ByteArrayContent(byteData))
            {
                // This example uses content type "application/octet-stream".
                // The other content types you can use are "application/json" and "multipart/form-data".
                content.Headers.ContentType = new MediaTypeHeaderValue("application/octet-stream");
                response = await client.PostAsync(uri, content);
                responseContent = response.Content.ReadAsStringAsync().Result;
            }

            // A peek at the raw JSON response.
            Console.WriteLine(responseContent);

            // Processing the JSON into manageable objects.
            JToken rootToken = JArray.Parse(responseContent).First;

            // First token is always the faceRectangle identified by the API.
            JToken faceRectangleToken = rootToken.First;

            // Second token is all emotion scores.
            JToken scoresToken = rootToken.Last;

            // Show all face rectangle dimensions
            JEnumerable<JToken> faceRectangleSizeList = faceRectangleToken.First.Children();
            foreach (var size in faceRectangleSizeList) {
                Console.WriteLine(size);
            }

            // Show all scores
            JEnumerable<JToken> scoreList = scoresToken.First.Children();
            foreach (var score in scoreList) {
                Console.WriteLine(score);
            }
        }
    }
}
```

## <a name="recognize-emotions-sample-response"></a>感情サンプル応答を認識する
呼び出しが成功すると、顔エントリとそれらに関連付けられた感情スコアの配列が返ります。 それらは、顔の四角形のサイズによって降順で順位付けされます。 空の応答は、顔が認識されなかったことを示します。 顔エントリには、次のフィールドが含まれます。

* faceRectangle: 画像内の顔の四角形の位置
* スコア: 画像内のそれぞれの顔の感情スコア 

```json
application/json 
[
  {
    "faceRectangle": {
      "left": 68,
      "top": 97,
      "width": 64,
      "height": 97
    },
    "scores": {
      "anger": 0.00300731952,
      "contempt": 5.14648448E-08,
      "disgust": 9.180124E-06,
      "fear": 0.0001912825,
      "happiness": 0.9875571,
      "neutral": 0.0009861537,
      "sadness": 1.889955E-05,
      "surprise": 0.008229999
    }
  }
]
