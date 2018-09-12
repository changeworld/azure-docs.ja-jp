---
title: 'Computer Vision API C# クイック スタート SDK: 画像の分析 | Microsoft Docs'
titleSuffix: Microsoft Cognitive Services
description: このクイック スタートでは、Cognitive Services の Computer Vision Windows C# クライアント ライブラリを使って、画像を分析します。
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: v-deken
ms.openlocfilehash: 3ff3a4702ab0b1fb663ee896f268065caf043809
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/31/2018
ms.locfileid: "43772330"
---
# <a name="quickstart-analyze-an-image---sdk-c35"></a>クイック スタート: 画像の分析 - SDK、C&#35;

このクイック スタートでは、Computer Vision Windows クライアント ライブラリを使ってローカル画像とリモート画像の両方を分析し、視覚的特徴を抽出します。

## <a name="prerequisites"></a>前提条件

* Computer Vision を使用するにはサブスクリプション キーが必要です。「[サブスクリプション キーを取得する](../Vision-API-How-to-Topics/HowToSubscribe.md)」をご覧ください。
* [Visual Studio 2015 または 2017](https://www.visualstudio.com/downloads/) の任意のエディション。
* [Microsoft.Azure.CognitiveServices.Vision.ComputerVision](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.ComputerVision) クライアント ライブラリの NuGet パッケージ。 パッケージをダウンロードする必要はありません。 インストールの手順は、以降で説明しています。

## <a name="analyzeimageasync-method"></a>AnalyzeImageAsync メソッド

`AnalyzeImageAsync` メソッドと `AnalyzeImageInStreamAsync` メソッドは、それぞれリモート画像とローカル画像の[画像分析 API](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) をラップします。 これらのメソッドを使って、画像の内容に基づく視覚的特徴を抽出したり、取得する特徴を選択したりすることができます。その例を次に示します。

* 画像の内容に関連するタグの詳細なリスト。
* 画像の内容を完全な文章で説明したもの。
* 画像に含まれている顔の座標、性別、年齢。
* ImageType (クリップ アートまたは線画)。
* ドミナント カラー、アクセント カラー、白黒画像かどうか。
* こちらの[分類](../Category-Taxonomy.md)で定義されているカテゴリ。
* 成人向けのコンテンツや性的な含みのあるコンテンツが画像に含まれているか。

このサンプルを実行するには、次の手順を実行します。

1. Visual Studio で、新しい Visual C# コンソール アプリを作成します。
1. Computer Vision クライアント ライブラリの NuGet パッケージをインストールします。
    1. メニューの **[ツール]** で **[NuGet パッケージ マネージャー]** を選択し、**[ソリューションの NuGet パッケージの管理]** を選択します。
    1. **[参照]** タブをクリックし、**[検索]** ボックスに「Microsoft.Azure.CognitiveServices.Vision.ComputerVision」と入力します。
    1. **[Microsoft.Azure.CognitiveServices.Vision.ComputerVision]** が表示されたら選択し、対象のプロジェクト名の横のチェック ボックスをオンにして、**[インストール]** をクリックします。
1. `Program.cs` を以下のコードに置き換えます。
1. `<Subscription Key>` を、有効なサブスクリプション キーに置き換えます。
1. 必要に応じて、サブスクリプション キーを取得した場所に `computerVision.AzureRegion = AzureRegions.Westcentralus` を変更します。
1. `<LocalImage>` を、ローカル画像のパスとファイル名に置き換えます。
1. 必要に応じて、`remoteImageUrl` を別の画像に設定します。
1. プログラムを実行します。

```csharp
using Microsoft.Azure.CognitiveServices.Vision.ComputerVision;
using Microsoft.Azure.CognitiveServices.Vision.ComputerVision.Models;

using System;
using System.Collections.Generic;
using System.IO;
using System.Threading.Tasks;

namespace ImageAnalyze
{
    class Program
    {
        // subscriptionKey = "0123456789abcdef0123456789ABCDEF"
        private const string subscriptionKey = "<SubscriptionKey>";

        // localImagePath = @"C:\Documents\LocalImage.jpg"
        private const string localImagePath = @"<LocalImage>";

        private const string remoteImageUrl =
            "http://upload.wikimedia.org/wikipedia/commons/3/3c/Shaki_waterfall.jpg";

        // Specify the features to return
        private static readonly List<VisualFeatureTypes> features =
            new List<VisualFeatureTypes>()
        {
            VisualFeatureTypes.Categories, VisualFeatureTypes.Description,
            VisualFeatureTypes.Faces, VisualFeatureTypes.ImageType,
            VisualFeatureTypes.Tags
        };

        static void Main(string[] args)
        {
            ComputerVisionAPI computerVision = new ComputerVisionAPI(
                new ApiKeyServiceClientCredentials(subscriptionKey), 
                new System.Net.Http.DelegatingHandler[] { });

            // You must use the same region as you used to get your subscription
            // keys. For example, if you got your subscription keys from westus,
            // replace "Westcentralus" with "Westus".
            //
            // Free trial subscription keys are generated in the westcentralus
            // region. If you use a free trial subscription key, you shouldn't
            // need to change the region.

            // Specify the Azure region
            computerVision.AzureRegion = AzureRegions.Westcentralus;

            Console.WriteLine("Images being analyzed ...");
            var t1 = AnalyzeRemoteAsync(computerVision, remoteImageUrl);
            var t2 = AnalyzeLocalAsync(computerVision, localImagePath);

            Task.WhenAll(t1, t2).Wait(5000);
            Console.WriteLine("Press any key to exit");
            Console.ReadLine();
        }

        // Analyze a remote image
        private static async Task AnalyzeRemoteAsync(
            ComputerVisionAPI computerVision, string imageUrl)
        {
            if (!Uri.IsWellFormedUriString(imageUrl, UriKind.Absolute))
            {
                Console.WriteLine(
                    "\nInvalid remoteImageUrl:\n{0} \n", imageUrl);
                return;
            }

            ImageAnalysis analysis =
                await computerVision.AnalyzeImageAsync(imageUrl, features);
            DisplayResults(analysis, imageUrl);
        }

        // Analyze a local image
        private static async Task AnalyzeLocalAsync(
            ComputerVisionAPI computerVision, string imagePath)
        {
            if (!File.Exists(imagePath))
            {
                Console.WriteLine(
                    "\nUnable to open or read localImagePath:\n{0} \n", imagePath);
                return;
            }

            using (Stream imageStream = File.OpenRead(imagePath))
            {
                ImageAnalysis analysis = await computerVision.AnalyzeImageInStreamAsync(
                    imageStream, features);
                DisplayResults(analysis, imagePath);
            }
        }

        // Display the most relevant caption for the image
        private static void DisplayResults(ImageAnalysis analysis, string imageUri)
        {
            Console.WriteLine(imageUri);
            Console.WriteLine(analysis.Description.Captions[0].Text + "\n");
        }
    }
}
```

## <a name="analyzeimageasync-response"></a>AnalyzeImageAsync 応答

成功した場合、各画像について最も関連性の高いキャプションが応答に表示されます。

生の JSON 出力の例については、[C# を使ったローカル画像の分析に関する API クイック スタート](../QuickStarts/CSharp-analyze.md#analyze-image-response)をご覧ください。

```cmd
http://upload.wikimedia.org/wikipedia/commons/3/3c/Shaki_waterfall.jpg
a large waterfall over a rocky cliff
```

## <a name="next-steps"></a>次の手順

画像の分析、著名人やランドマークの検出、サムネイルの作成、印刷されたテキストや手書きテキストの抽出に使用される Computer Vision API の詳細を確認します。

> [!div class="nextstepaction"]
> [Computer Vision API の詳細](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
