---
title: 'クイック スタート: 画像の中にある顔を検出する - SDK、C#'
titleSuffix: Azure Cognitive Services
description: このクイック スタートでは、Cognitive Services の Face Windows C# クライアント ライブラリを使って画像から顔を検出します。
services: cognitive-services
author: noellelacharite
manager: cgronlun
ms.service: cognitive-services
ms.component: face-api
ms.topic: quickstart
ms.date: 09/14/2018
ms.author: nolachar
ms.openlocfilehash: a9a7efd89f8e7462812064615d07acf12acbc3a3
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/19/2018
ms.locfileid: "46364115"
---
# <a name="quickstart-detect-faces-in-an-image-using-c35---face"></a>クイック スタート: C&#35; を使って画像の中にある顔を検出する - Face

このクイック スタートでは、Face Windows クライアント ライブラリを使って画像から人の顔を検出します。

このサンプルのソース コードは、[GitHub](https://github.com/Azure-Samples/cognitive-services-vision-csharp-sdk-quickstarts/tree/master/Face) で入手できます。

## <a name="prerequisites"></a>前提条件

* サンプルを実行するにはサブスクリプション キーが必要です。 無料試用版のサブスクリプション キーは「[Cognitive Services を試す](https://azure.microsoft.com/try/cognitive-services/?api=face-api)」から取得できます。
* [Visual Studio 2017](https://www.visualstudio.com/downloads/) の任意のエディション。
* [Microsoft.Azure.CognitiveServices.Vision.Face 2.2.0-preview](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/2.2.0-preview) クライアント ライブラリの NuGet パッケージ。 パッケージをダウンロードする必要はありません。 インストールの手順は、以降で説明しています。

## <a name="detectwithurlasync-method"></a>DetectWithUrlAsync メソッド

`DetectWithUrlAsync` メソッドと `DetectWithStreamAsync` メソッドは、それぞれリモート画像とローカル画像の [Face - Detect API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) をラップします。 これらのメソッドを使用して、画像の中にある顔を検出し、次のような顔の属性を返すことができます。

* Face ID: Face API の各種シナリオで使用される一意の ID。
* 顔四角形: 画像内での顔の位置を示す値 (左、上、幅、高さ)。
* ランドマーク: 顔の構成要素の重要な位置を示す 27 地点のランドマークの配列。
* 顔の属性 (年齢、性別、笑顔の強さ、頭部姿勢、顔ひげなど)。

このサンプルを実行するには、次の手順を実行します。

1. Visual Studio で、新しい Visual C# コンソール アプリを作成します。
1. Face クライアント ライブラリの NuGet パッケージをインストールします。
    1. 上部のメニューの **[ツール]** で **[NuGet パッケージ マネージャー]** を選択し、**[ソリューションの NuGet パッケージの管理]** を選択します。
    1. **[参照]** タブをクリックし、**[プレリリースを含める]** を選択します。
    1. **[検索]** ボックスに「Microsoft.Azure.CognitiveServices.Vision.Face」と入力します。
    1. **[Microsoft.Azure.CognitiveServices.Vision.Face]** が表示されたら選択し、対象のプロジェクト名の横のチェック ボックスをオンにして、**[インストール]** をクリックします。
1. *Program.cs* を次のコードに置き換えます。
1. `<Subscription Key>` を、有効なサブスクリプション キーに置き換えます。
1. 必要に応じて、`faceEndpoint` をサブスクリプション キーに関連付けられている Azure リージョンに変更します。
1. 必要に応じて、<`LocalImage>` をローカル画像のパスとファイル名に置き換えます (設定しなかった場合は無視されます)。
1. 必要に応じて、`remoteImageUrl` を別の画像に設定します。
1. プログラムを実行します。

```csharp
using Microsoft.Azure.CognitiveServices.Vision.Face;
using Microsoft.Azure.CognitiveServices.Vision.Face.Models;

using System;
using System.Collections.Generic;
using System.IO;
using System.Threading.Tasks;

namespace DetectFace
{
    class Program
    {
        // subscriptionKey = "0123456789abcdef0123456789ABCDEF"
        private const string subscriptionKey = "<SubscriptionKey>";

        // You must use the same region as you used to get your subscription
        // keys. For example, if you got your subscription keys from westus,
        // replace "westcentralus" with "westus".
        //
        // Free trial subscription keys are generated in the westcentralus
        // region. If you use a free trial subscription key, you shouldn't
        // need to change the region.
        // Specify the Azure region
        private const string faceEndpoint =
            "https://westcentralus.api.cognitive.microsoft.com";

        // localImagePath = @"C:\Documents\LocalImage.jpg"
        private const string localImagePath = @"<LocalImage>";

        private const string remoteImageUrl =
            "https://upload.wikimedia.org/wikipedia/commons/3/37/Dagestani_man_and_woman.jpg";

        private static readonly FaceAttributeType[] faceAttributes =
            { FaceAttributeType.Age, FaceAttributeType.Gender };

        static void Main(string[] args)
        {
            FaceClient faceClient = new FaceClient(
                new ApiKeyServiceClientCredentials(subscriptionKey),
                new System.Net.Http.DelegatingHandler[] { });
            faceClient.Endpoint = faceEndpoint;

            Console.WriteLine("Faces being detected ...");
            var t1 = DetectRemoteAsync(faceClient, remoteImageUrl);
            var t2 = DetectLocalAsync(faceClient, localImagePath);

            Task.WhenAll(t1, t2).Wait(5000);
            Console.WriteLine("Press any key to exit");
            Console.ReadLine();
        }

        // Detect faces in a remote image
        private static async Task DetectRemoteAsync(
            FaceClient faceClient, string imageUrl)
        {
            if (!Uri.IsWellFormedUriString(imageUrl, UriKind.Absolute))
            {
                Console.WriteLine("\nInvalid remoteImageUrl:\n{0} \n", imageUrl);
                return;
            }

            try
            {
                IList<DetectedFace> faceList =
                    await faceClient.Face.DetectWithUrlAsync(
                        imageUrl, true, false, faceAttributes);

                DisplayAttributes(GetFaceAttributes(faceList, imageUrl), imageUrl);
            }
            catch (APIErrorException e)
            {
                Console.WriteLine(imageUrl + ": " + e.Message);
            }
        }

        // Detect faces in a local image
        private static async Task DetectLocalAsync(FaceClient faceClient, string imagePath)
        {
            if (!File.Exists(imagePath))
            {
                Console.WriteLine(
                    "\nUnable to open or read localImagePath:\n{0} \n", imagePath);
                return;
            }

            try
            {
                using (Stream imageStream = File.OpenRead(imagePath))
                {
                    IList<DetectedFace> faceList =
                            await faceClient.Face.DetectWithStreamAsync(
                                imageStream, true, false, faceAttributes);
                    DisplayAttributes(
                        GetFaceAttributes(faceList, imagePath), imagePath);
                }
            }
            catch (APIErrorException e)
            {
                Console.WriteLine(imagePath + ": " + e.Message);
            }
        }

        private static string GetFaceAttributes(
            IList<DetectedFace> faceList, string imagePath)
        {
            string attributes = string.Empty;

            foreach (DetectedFace face in faceList)
            {
                double? age = face.FaceAttributes.Age;
                string gender = face.FaceAttributes.Gender.ToString();
                attributes += gender + " " + age + "   ";
            }

            return attributes;
        }

        // Display the face attributes
        private static void DisplayAttributes(string attributes, string imageUri)
        {
            Console.WriteLine(imageUri);
            Console.WriteLine(attributes + "\n");
        }
    }
}
```

### <a name="detectwithurlasync-response"></a>DetectWithUrlAsync 応答

成功応答には、画像内の個々の顔の性別と年齢が表示されます。

生の JSON 出力例については、[C# を使用して画像の中の顔を検出する API のクイック スタート](CSharp.md)を参照してください。

```
https://upload.wikimedia.org/wikipedia/commons/3/37/Dagestani_man_and_woman.jpg
Male 37   Female 56
```

## <a name="next-steps"></a>次の手順

Face サービスを使用して画像内の顔を検出する WPF Windows アプリケーションを作成する方法について説明します。 このアプリケーションは、各顔のまわりにフレームを描画し、ステータス バーに顔の説明を表示します。

> [!div class="nextstepaction"]
> [チュートリアル: 画像の中にある顔を検出してフレームに収める WPF アプリの作成](../Tutorials/FaceAPIinCSharpTutorial.md)
