---
title: クイック スタート:テキストの抽出 - SDK、C# - Computer Vision
titleSuffix: Azure Cognitive Services
description: このクイック スタートでは、Computer Vision Windows C# クライアント ライブラリを使って、画像からテキストを抽出します。
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: quickstart
ms.date: 09/27/2018
ms.author: pafarley
ms.openlocfilehash: c0323c215e83850dc53acd5e350d7eeeeb9a05b1
ms.sourcegitcommit: b254db346732b64678419db428fd9eb200f3c3c5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/14/2018
ms.locfileid: "53413219"
---
# <a name="quickstart-extract-text-using-the-computer-vision-sdk-and-c"></a>クイック スタート:Computer Vision SDK と C# によるテキストの抽出

このクイック スタートでは、Computer Vision Windows クライアント ライブラリを使って、画像から手書きテキストまたは印刷されたテキストを抽出します。

## <a name="prerequisites"></a>前提条件

* Computer Vision を使用するにはサブスクリプション キーが必要です。「[サブスクリプション キーを取得する](../Vision-API-How-to-Topics/HowToSubscribe.md)」をご覧ください。
* [Visual Studio 2015 または 2017](https://www.visualstudio.com/downloads/) の任意のエディション。
* [Microsoft.Azure.CognitiveServices.Vision.ComputerVision](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.ComputerVision) クライアント ライブラリの NuGet パッケージ。 パッケージをダウンロードする必要はありません。 インストールの手順は、以降で説明しています。

## <a name="recognizetextasync-method"></a>RecognizeTextAsync メソッド

> [!TIP]
> 最新のコードを Visual Studio ソリューションとして [GitHub](https://github.com/Azure-Samples/cognitive-services-vision-csharp-sdk-quickstarts/tree/master/ComputerVision) から取得してください。

`RecognizeTextAsync` メソッドと `RecognizeTextInStreamAsync` メソッドは、それぞれリモート画像とローカル画像の[テキスト認識 API](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2c6a154055056008f200) をラップします。 `GetTextOperationResultAsync` メソッドは、[テキスト認識操作の結果を取得する API](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2cf1154055056008f201) をラップします。  これらのメソッドを使って画像内のテキストを検出し、認識した文字をマシンに抽出して、使用可能な文字ストリームにします。

このサンプルを実行するには、次の手順を実行します。

1. Visual Studio で、新しい Visual C# コンソール アプリを作成します。
1. Computer Vision クライアント ライブラリの NuGet パッケージをインストールします。
    1. メニューの **[ツール]** で **[NuGet パッケージ マネージャー]** を選択し、**[ソリューションの NuGet パッケージの管理]** を選択します。
    1. **[参照]** タブをクリックし、**[検索]** ボックスに「Microsoft.Azure.CognitiveServices.Vision.ComputerVision」と入力します。
    1. **[Microsoft.Azure.CognitiveServices.Vision.ComputerVision]** が表示されたら選択し、対象のプロジェクト名の横のチェック ボックスをオンにして、**[インストール]** をクリックします。
1. `Program.cs` を以下のコードに置き換えます。
1. `<Subscription Key>` を、有効なサブスクリプション キーに置き換えます。
1. 必要に応じて、`computerVision.Endpoint` をサブスクリプション キーに関連付けられている Azure リージョンに変更します。
1. 必要に応じて、`textRecognitionMode` を `TextRecognitionMode.Printed` に設定します。
1. `<LocalImage>` を、ローカル画像のパスとファイル名に置き換えます。
1. 必要に応じて、`remoteImageUrl` を別の画像に設定します。
1. プログラムを実行します。

```csharp
using Microsoft.Azure.CognitiveServices.Vision.ComputerVision;
using Microsoft.Azure.CognitiveServices.Vision.ComputerVision.Models;

using System;
using System.IO;
using System.Threading.Tasks;

namespace ExtractText
{
    class Program
    {
        // subscriptionKey = "0123456789abcdef0123456789ABCDEF"
        private const string subscriptionKey = "<SubscriptionKey>";

        // For printed text, change to TextRecognitionMode.Printed
        private const TextRecognitionMode textRecognitionMode =
            TextRecognitionMode.Handwritten;

        // localImagePath = @"C:\Documents\LocalImage.jpg"
        private const string localImagePath = @"<LocalImage>";

        private const string remoteImageUrl =
            "https://upload.wikimedia.org/wikipedia/commons/thumb/d/dd/" +
            "Cursive_Writing_on_Notebook_paper.jpg/" +
            "800px-Cursive_Writing_on_Notebook_paper.jpg";

        private const int numberOfCharsInOperationId = 36;

        static void Main(string[] args)
        {
            ComputerVisionClient computerVision = new ComputerVisionClient(
                new ApiKeyServiceClientCredentials(subscriptionKey),
                new System.Net.Http.DelegatingHandler[] { });

            // You must use the same region as you used to get your subscription
            // keys. For example, if you got your subscription keys from westus,
            // replace "westcentralus" with "westus".
            //
            // Free trial subscription keys are generated in the "westus"
            // region. If you use a free trial subscription key, you shouldn't
            // need to change the region.

            // Specify the Azure region
            computerVision.Endpoint = "https://westcentralus.api.cognitive.microsoft.com";

            Console.WriteLine("Images being analyzed ...");
            var t1 = ExtractRemoteTextAsync(computerVision, remoteImageUrl);
            var t2 = ExtractLocalTextAsync(computerVision, localImagePath);

            Task.WhenAll(t1, t2).Wait(5000);
            Console.WriteLine("Press ENTER to exit");
            Console.ReadLine();
        }

        // Recognize text from a remote image
        private static async Task ExtractRemoteTextAsync(
            ComputerVisionClient computerVision, string imageUrl)
        {
            if (!Uri.IsWellFormedUriString(imageUrl, UriKind.Absolute))
            {
                Console.WriteLine(
                    "\nInvalid remoteImageUrl:\n{0} \n", imageUrl);
                return;
            }

            // Start the async process to recognize the text
            RecognizeTextHeaders textHeaders =
                await computerVision.RecognizeTextAsync(
                    imageUrl, textRecognitionMode);

            await GetTextAsync(computerVision, textHeaders.OperationLocation);
        }

        // Recognize text from a local image
        private static async Task ExtractLocalTextAsync(
            ComputerVisionClient computerVision, string imagePath)
        {
            if (!File.Exists(imagePath))
            {
                Console.WriteLine(
                    "\nUnable to open or read localImagePath:\n{0} \n", imagePath);
                return;
            }

            using (Stream imageStream = File.OpenRead(imagePath))
            {
                // Start the async process to recognize the text
                RecognizeTextInStreamHeaders textHeaders =
                    await computerVision.RecognizeTextInStreamAsync(
                        imageStream, textRecognitionMode);

                await GetTextAsync(computerVision, textHeaders.OperationLocation);
            }
        }

        // Retrieve the recognized text
        private static async Task GetTextAsync(
            ComputerVisionClient computerVision, string operationLocation)
        {
            // Retrieve the URI where the recognized text will be
            // stored from the Operation-Location header
            string operationId = operationLocation.Substring(
                operationLocation.Length - numberOfCharsInOperationId);

            Console.WriteLine("\nCalling GetHandwritingRecognitionOperationResultAsync()");
            TextOperationResult result =
                await computerVision.GetTextOperationResultAsync(operationId);

            // Wait for the operation to complete
            int i = 0;
            int maxRetries = 10;
            while ((result.Status == TextOperationStatusCodes.Running ||
                    result.Status == TextOperationStatusCodes.NotStarted) && i++ < maxRetries)
            {
                Console.WriteLine(
                    "Server status: {0}, waiting {1} seconds...", result.Status, i);
                await Task.Delay(1000);

                result = await computerVision.GetTextOperationResultAsync(operationId);
            }

            // Display the results
            Console.WriteLine();
            var lines = result.RecognitionResult.Lines;
            foreach (Line line in lines)
            {
                Console.WriteLine(line.Text);
            }
            Console.WriteLine();
        }
    }
}
```

## <a name="recognizetextasync-response"></a>RecognizeTextAsync 応答

成功した場合、各画像について認識されたテキストの行が応答に表示されます。

「[クイック スタート:手書きテキストの抽出 - REST、C#](../QuickStarts/CSharp-hand-text.md#examine-the-response)」で生の JSON の出力例を参照してください。

```cmd
Calling GetHandwritingRecognitionOperationResultAsync()

Calling GetHandwritingRecognitionOperationResultAsync()
Server status: Running, waiting 1 seconds...
Server status: Running, waiting 1 seconds...

dog
The quick brown fox jumps over the lazy
Pack my box with five dozen liquor jugs
```

## <a name="next-steps"></a>次の手順

画像の分析、著名人やランドマークの検出、サムネイルの作成、印刷されたテキストや手書きテキストの抽出に使用される Computer Vision API の詳細を確認します。

> [!div class="nextstepaction"]
> [Computer Vision API の詳細](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
