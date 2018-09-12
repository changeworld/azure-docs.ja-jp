---
title: 'Computer Vision API C# クイック スタート: ローカル画像の分析 | Microsoft Docs'
titleSuffix: Microsoft Cognitive Services
description: このクイック スタートでは、Cognitive Services の Computer Vision と C# を使ってローカル画像を分析します。
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: v-deken
ms.openlocfilehash: 8196787df9f7fadedda72d525aee440afe7b7d34
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/31/2018
ms.locfileid: "43771980"
---
# <a name="quickstart-analyze-a-local-image---rest-c35"></a>クイック スタート: ローカル画像の分析 - REST、C&#35;

このクイック スタートでは、Computer Vision を使ってローカル画像を分析し、視覚的特徴を抽出します。

## <a name="prerequisites"></a>前提条件

Computer Vision を使用するにはサブスクリプション キーが必要です。「[サブスクリプション キーを取得する](../Vision-API-How-to-Topics/HowToSubscribe.md)」をご覧ください。

## <a name="analyze-image-request"></a>画像分析要求

[画像分析メソッド](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa)を使うと、画像の内容に基づいて視覚的特徴を抽出できます。 画像をアップロードするか、画像の URL を指定して、取得する特徴を選択することができます。その例を次に示します。

* 画像の内容に関連するタグの詳細なリスト。
* 画像の内容を完全な文章で説明したもの。
* 画像に含まれている顔の座標、性別、年齢。
* ImageType (クリップ アートまたは線画)。
* ドミナント カラー、アクセント カラー、白黒画像かどうか。
* こちらの[分類](../Category-Taxonomy.md)で定義されているカテゴリ。
* 成人向けのコンテンツや性的な含みのあるコンテンツが画像に含まれているか。

このサンプルを実行するには、次の手順を実行します。

1. Visual Studio で、新しい Visual C# コンソール アプリを作成します。
1. Newtonsoft.Json NuGet パッケージをインストールします。
    1. メニューの **[ツール]** で **[NuGet パッケージ マネージャー]** を選択し、**[ソリューションの NuGet パッケージの管理]** を選択します。
    1. **[参照]** タブをクリックし、**[検索]** ボックスに「Newtonsoft.Json」と入力します。
    1. **[Newtonsoft.Json]** が表示されたら選択し、対象のプロジェクト名の横のチェック ボックスをオンにして、**[インストール]** をクリックします。
1. `Program.cs` を以下のコードに置き換えます。
1. `<Subscription Key>` を、有効なサブスクリプション キーに置き換えます。
1. 必要に応じて `uriBase` の値を、サブスクリプション キーを取得した場所に変更します。
1. プログラムを実行します。
1. プロンプトで、ローカル画像のパスを入力します。

```csharp
using Newtonsoft.Json.Linq;
using System;
using System.IO;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Threading.Tasks;

namespace CSHttpClientSample
{
    static class Program
    {
        // Replace <Subscription Key> with your valid subscription key.
        const string subscriptionKey = "<Subscription Key>";

        // You must use the same region in your REST call as you used to
        // get your subscription keys. For example, if you got your
        // subscription keys from westus, replace "westcentralus" in the URL
        // below with "westus".
        //
        // Free trial subscription keys are generated in the westcentralus region.
        // If you use a free trial subscription key, you shouldn't need to change
        // this region.
        const string uriBase =
            "https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/analyze";

        static void Main()
        {
            // Get the path and filename to process from the user.
            Console.WriteLine("Analyze an image:");
            Console.Write("Enter the path to the image you wish to analyze: ");
            string imageFilePath = Console.ReadLine();

            if (File.Exists(imageFilePath))
            {
                // Make the REST API call.
                Console.WriteLine("\nWait a moment for the results to appear.\n");
                MakeAnalysisRequest(imageFilePath).Wait();
            }
            else
            {
                Console.WriteLine("\nInvalid file path");
            }
            Console.WriteLine("\nPress Enter to exit...");
            Console.ReadLine();
        }

        /// <summary>
        /// Gets the analysis of the specified image file by using
        /// the Computer Vision REST API.
        /// </summary>
        /// <param name="imageFilePath">The image file to analyze.</param>
        static async Task MakeAnalysisRequest(string imageFilePath)
        {
            try
            {
                HttpClient client = new HttpClient();

                // Request headers.
                client.DefaultRequestHeaders.Add(
                    "Ocp-Apim-Subscription-Key", subscriptionKey);

                // Request parameters. A third optional parameter is "details".
                string requestParameters =
                    "visualFeatures=Categories,Description,Color";

                // Assemble the URI for the REST API Call.
                string uri = uriBase + "?" + requestParameters;

                HttpResponseMessage response;

                // Request body. Posts a locally stored JPEG image.
                byte[] byteData = GetImageAsByteArray(imageFilePath);

                using (ByteArrayContent content = new ByteArrayContent(byteData))
                {
                    // This example uses content type "application/octet-stream".
                    // The other content types you can use are "application/json"
                    // and "multipart/form-data".
                    content.Headers.ContentType =
                        new MediaTypeHeaderValue("application/octet-stream");

                    // Make the REST API call.
                    response = await client.PostAsync(uri, content);
                }

                // Get the JSON response.
                string contentString = await response.Content.ReadAsStringAsync();

                // Display the JSON response.
                Console.WriteLine("\nResponse:\n\n{0}\n",
                    JToken.Parse(contentString).ToString());
            }
            catch (Exception e)
            {
                Console.WriteLine("\n" + e.Message);
            }
        }

        /// <summary>
        /// Returns the contents of the specified file as a byte array.
        /// </summary>
        /// <param name="imageFilePath">The image file to read.</param>
        /// <returns>The byte array of the image data.</returns>
        static byte[] GetImageAsByteArray(string imageFilePath)
        {
            using (FileStream fileStream =
                new FileStream(imageFilePath, FileMode.Open, FileAccess.Read))
            {
                BinaryReader binaryReader = new BinaryReader(fileStream);
                return binaryReader.ReadBytes((int)fileStream.Length);
            }
        }
    }
}
```

## <a name="analyze-image-response"></a>画像分析応答

成功応答が JSON で返されます。その例を次に示します。

```json
{
   "categories": [
      {
         "name": "abstract_",
         "score": 0.00390625
      },
      {
         "name": "others_",
         "score": 0.0234375
      },
      {
         "name": "outdoor_",
         "score": 0.00390625
      }
   ],
   "description": {
      "tags": [
         "road",
         "building",
         "outdoor",
         "street",
         "night",
         "black",
         "city",
         "white",
         "light",
         "sitting",
         "riding",
         "man",
         "side",
         "empty",
         "rain",
         "corner",
         "traffic",
         "lit",
         "hydrant",
         "stop",
         "board",
         "parked",
         "bus",
         "tall"
      ],
      "captions": [
         {
            "text": "a close up of an empty city street at night",
            "confidence": 0.7965622853462756
         }
      ]
   },
   "requestId": "dddf1ac9-7e66-4c47-bdef-222f3fe5aa23",
   "metadata": {
      "width": 3733,
      "height": 1986,
      "format": "Jpeg"
   },
   "color": {
      "dominantColorForeground": "Black",
      "dominantColorBackground": "Black",
      "dominantColors": [
         "Black",
         "Grey"
      ],
      "accentColor": "666666",
      "isBWImg": true
   }
}
```

## <a name="next-steps"></a>次の手順

Computer Vision を使用して、光学文字認識 (OCR) を実行し、スマートにクロップされたサムネイルを作成するほか、イメージ内の視覚的な特徴 (顔など) を検出、カテゴライズ、タグ付け、および記述する、基本的な Windows アプリケーションについて詳細を確認します。 Computer Vision API を簡単に試す場合は、[Open API テスト コンソール](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console)をお試しください。


> [!div class="nextstepaction"]
> [C# で Computer Vision を使用する](../Tutorials/CSharpTutorial.md)
