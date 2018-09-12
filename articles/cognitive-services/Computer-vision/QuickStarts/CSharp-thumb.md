---
title: 'Computer Vision API C# クイック スタート: サムネイルの作成 | Microsoft Docs'
titleSuffix: Microsoft Cognitive Services
description: このクイック スタートでは、Cognitive Services の Computer Vision と C# を使って、画像からサムネイルを生成します。
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: v-deken
ms.openlocfilehash: 0f5e3be75ce34d10c223e6a157a89fca12b9c3dc
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/31/2018
ms.locfileid: "43772121"
---
# <a name="quickstart-generate-a-thumbnail---rest-c35"></a>クイック スタート: サムネイルの生成 - REST、C&#35;

このクイック スタートでは、Computer Vision を使って、画像からサムネイルを生成します。

## <a name="prerequisites"></a>前提条件

Computer Vision を使用するにはサブスクリプション キーが必要です。「[サブスクリプション キーを取得する](../Vision-API-How-to-Topics/HowToSubscribe.md)」をご覧ください。

## <a name="get-thumbnail-request"></a>サムネイル取得要求

[サムネイル取得メソッド](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fb)を使うと、画像のサムネイルを生成することができます。 その際に指定する高さと幅は、入力画像の縦横比と異なっていてもかまいません。 Computer Vision は、スマート トリミングを使ってインテリジェントに関心領域を識別し、その領域に基づいてトリミングの座標を生成します。

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

サムネイルは、元の名前に "_thumb" というサフィックスで、ローカル画像と同じフォルダーに保存されます。

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
            "https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/generateThumbnail";

        static void Main()
        {
            // Get the path and filename to process from the user.
            Console.WriteLine("Thumbnail:");
            Console.Write(
                "Enter the path to the image you wish to use to create a thumbnail image: ");
            string imageFilePath = Console.ReadLine();

            if (File.Exists(imageFilePath))
            {
                // Make the REST API call.
                Console.WriteLine("\nWait a moment for the results to appear.\n");
                MakeThumbNailRequest(imageFilePath).Wait();
            }
            else
            {
                Console.WriteLine("\nInvalid file path");
            }
            Console.WriteLine("\nPress Enter to exit...");
            Console.ReadLine();
        }

        /// <summary>
        /// Gets a thumbnail image from the specified image file by using
        /// the Computer Vision REST API.
        /// </summary>
        /// <param name="imageFilePath">The image file to use to create the thumbnail image.</param>
        static async Task MakeThumbNailRequest(string imageFilePath)
        {
            try
            {
                HttpClient client = new HttpClient();

                // Request headers.
                client.DefaultRequestHeaders.Add(
                    "Ocp-Apim-Subscription-Key", subscriptionKey);

                // Request parameters.
                string requestParameters = "width=200&height=150&smartCropping=true";

                // Assemble the URI for the REST API Call.
                string uri = uriBase + "?" + requestParameters;

                HttpResponseMessage response;

                // Request body.
                // Posts a locally stored JPEG image.
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

                if (response.IsSuccessStatusCode)
                {
                    // Display the response data.
                    Console.WriteLine("\nResponse:\n{0}", response);

                    // Get the image data.
                    byte[] thumbnailImageData =
                        await response.Content.ReadAsByteArrayAsync();

                    // Save the thumbnail to the same folder as the original image,
                    // using the original name with the suffix "_thumb".
                    // Note: This will overwrite an existing file of the same name.
                    string thumbnailFilePath =
                        imageFilePath.Insert(imageFilePath.Length - 4, "_thumb");
                    File.WriteAllBytes(thumbnailFilePath, thumbnailImageData);
                    Console.WriteLine("\nThumbnail written to: {0}", thumbnailFilePath);
                }
                else
                {
                    // Display the JSON error data.
                    string errorString = await response.Content.ReadAsStringAsync();
                    Console.WriteLine("\n\nResponse:\n{0}\n",
                        JToken.Parse(errorString).ToString());
                }
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

## <a name="get-thumbnail-response"></a>サムネイル取得応答

成功時の応答には、サムネイル画像のバイナリが格納されます。 要求が失敗した場合は、原因の特定につながるエラー コードとメッセージが応答に格納されます。

```text
Response:

StatusCode: 200, ReasonPhrase: 'OK', Version: 1.1, Content: System.Net.Http.StreamContent, Headers:
{
  Pragma: no-cache
  apim-request-id: 131eb5b4-5807-466d-9656-4c1ef0a64c9b
  Strict-Transport-Security: max-age=31536000; includeSubDomains; preload
  x-content-type-options: nosniff
  Cache-Control: no-cache
  Date: Tue, 06 Jun 2017 20:54:07 GMT
  X-AspNet-Version: 4.0.30319
  X-Powered-By: ASP.NET
  Content-Length: 5800
  Content-Type: image/jpeg
  Expires: -1
}
```

## <a name="next-steps"></a>次の手順

Computer Vision を使用して、光学文字認識 (OCR) を実行し、スマートにクロップされたサムネイルを作成するほか、イメージ内の視覚的な特徴 (顔など) を検出、カテゴライズ、タグ付け、および記述する、基本的な Windows アプリケーションについて詳細を確認します。 Computer Vision API を簡単に試す場合は、[Open API テスト コンソール](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console)をお試しください。

> [!div class="nextstepaction"]
> [Computer Vision API C&#35; チュートリアル](../Tutorials/CSharpTutorial.md)
