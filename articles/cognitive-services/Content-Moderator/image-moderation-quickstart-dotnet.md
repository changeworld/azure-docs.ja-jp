---
title: 'クイック スタート: .NET を使用して画像をモデレートする - Content Moderator'
titlesuffix: Azure Cognitive Services
description: Content Moderator SDK for .NET を使用して画像をモデレートする方法
services: cognitive-services
author: sanjeev3
manager: cgronlun
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: quickstart
ms.date: 09/10/2018
ms.author: sajagtap
ms.openlocfilehash: d89d9b8a2e3b00155e82cc28105007ab39fc549c
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/26/2018
ms.locfileid: "47226166"
---
# <a name="quickstart-moderate-images-using-net"></a>クイック スタート: .NET を使用して画像をモデレートする

この記事では、[Content Moderator SDK for .NET](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) を使用して次の操作をすぐに開始するために役立つ情報とコード サンプルを提供します。 

- 成人向けまたはわいせつなコンテンツの画像を確認する
- 画像のテキストの検出と抽出
- 画像内の顔を検出する

この記事では Visual Studio と C# に精通していることを前提としています。

## <a name="sign-up-for-content-moderator-services"></a>Content Moderator サービスにサインアップする

REST API や SDK を通じて Content Moderator サービスを使用するには、API キーと、お使いの API アカウントのリージョンが必要です。
Content Moderator にサインアップして両方を入手する方法については、[クイック スタート](quick-start.md)を参照してください。

## <a name="create-your-visual-studio-project"></a>Visual Studio プロジェクトを作成する

1. ソリューションに新しい**コンソール アプリ (.NET Framework)** プロジェクトを追加します。

   サンプル コードで、プロジェクトに **ImageModeration** と名前を付けます。

1. このプロジェクトをソリューションのシングル スタートアップ プロジェクトとして選択します。


### <a name="install-required-packages"></a>必要なパッケージをインストールする

次の NuGet パッケージをインストールします。

- Microsoft.Azure.CognitiveServices.ContentModerator
- Microsoft.Rest.ClientRuntime
- Newtonsoft.Json

### <a name="update-the-programs-using-statements"></a>プログラムの using ステートメントを更新する

プログラムの using ステートメントを変更します。

    using Microsoft.Azure.CognitiveServices.ContentModerator;
    using Microsoft.CognitiveServices.ContentModerator;
    using Microsoft.CognitiveServices.ContentModerator.Models;
    using Newtonsoft.Json;
    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Threading;

### <a name="create-the-content-moderator-client"></a>Content Moderator クライアントを作成する

次のコードを追加して、サブスクリプションの Content Moderator クライアントを作成します。

> [!IMPORTANT]
> **AzureRegion** および **CMSubscriptionKey** フィールドをリージョン識別子とサブスクリプション キーの値で更新します。

    /// <summary>
    /// Wraps the creation and configuration of a Content Moderator client.
    /// </summary>
    /// <remarks>This class library contains insecure code. If you adapt this 
    /// code for use in production, use a secure method of storing and using
    /// your Content Moderator subscription key.</remarks>
    public static class Clients
    {
        /// <summary>
        /// The region/location for your Content Moderator account, 
        /// for example, westus.
        /// </summary>
        private static readonly string AzureRegion = "YOUR API REGION";

        /// <summary>
        /// The base URL fragment for Content Moderator calls.
        /// </summary>
        private static readonly string AzureBaseURL =
            $"https://{AzureRegion}.api.cognitive.microsoft.com";

        /// <summary>
        /// Your Content Moderator subscription key.
        /// </summary>
        private static readonly string CMSubscriptionKey = "YOUR API KEY";

        /// <summary>
        /// Returns a new Content Moderator client for your subscription.
        /// </summary>
        /// <returns>The new client.</returns>
        /// <remarks>The <see cref="ContentModeratorClient"/> is disposable.
        /// When you have finished using the client,
        /// you should dispose of it either directly or indirectly. </remarks>
        public static ContentModeratorClient NewClient()
        {
            // Create and initialize an instance of the Content Moderator API wrapper.
            ContentModeratorClient client = new ContentModeratorClient(new ApiKeyServiceClientCredentials(CMSubscriptionKey));

            client.Endpoint = AzureBaseURL;
            return client;
        }
    }

### <a name="initialize-application-specific-settings"></a>アプリケーション固有の設定を初期化する

次の静的フィールドを Program.cs 内の **Program** クラスに追加します。

    ///<summary>
    ///The name of the file that contains the image URLs to evaluate.
    ///</summary>
    ///<remarks>You will need to create an input file and update 
    ///this path accordingly. Paths are relative to the execution directory.
    ///</remarks>
    private static string ImageUrlFile = "ImageFiles.txt";

    ///<summary>
    ///The name of the file to contain the output from the evaluation.
    ///</summary>
    ///<remarks>Paths are relative to the execution directory.
    ///</remarks>
    private static string OutputFile = "ModerationOutput.json";


> [!NOTE]
> このサンプルでは、次の画像を使用してこのクイックスタートの出力を生成しています。
> - https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg
> - https://moderatorsampleimages.blob.core.windows.net/samples/sample5.png

## <a name="store-the-analysis-results"></a>分析結果を格納する

**Program** クラスに次のクラスを追加します。 このクラスのインスタンスを使用して、レビューした画像のモデレーション結果を記録します。

    /// <summary>
    /// Contains the image moderation results for an image, 
    /// including text and face detection results.
    /// </summary>
    public class EvaluationData
    {
        /// <summary>
        /// The URL of the evaluated image.
        /// </summary>
        public string ImageUrl;

        /// <summary>
        /// The image moderation results.
        /// </summary>
        public Evaluate ImageModeration;

        /// <summary>
        /// The text detection results.
        /// </summary>
        public OCR TextDetection;

        /// <summary>
        /// The face detection results;
        /// </summary>
        public FoundFaces FaceDetection;
    }

## <a name="evaluate-an-individual-image"></a>個々の画像を評価する

**Program** クラスに次のメソッドを追加します。 このメソッドは、単一の画像を評価し、評価結果を返します。

> [!NOTE]
> お使いの Content Moderator のサービス キーは 1 秒ごとの要求数 (RPS) が制限されており、その上限を超えると、SDK が 429 エラー コードとともに例外をスローします。 
>
> 階層なしのキーの RPS は 1 に制限されています。


    /// <summary>
    /// Evaluates an image using the Image Moderation APIs.
    /// </summary>
    /// <param name="client">The Content Moderator API wrapper to use.</param>
    /// <param name="imageUrl">The URL of the image to evaluate.</param>
    /// <returns>Aggregated image moderation results for the image.</returns>
    /// <remarks>This method throttles calls to the API.
    /// Your Content Moderator service key will have a requests per second (RPS)
    /// rate limit, and the SDK will throw an exception with a 429 error code 
    /// if you exceed that limit. A free tier key has a 1 RPS rate limit.
    /// </remarks>
    private static EvaluationData EvaluateImage(
    ContentModeratorClient client, string imageUrl)
    {
        var url = new ImageUrl("URL", imageUrl.Trim());

        var imageData = new EvaluationData();

        imageData.ImageUrl = url.Value;

        // Evaluate for adult and racy content.
        imageData.ImageModeration =
            client.ImageModeration.EvaluateUrlInput("application/json", url, true);
        Thread.Sleep(1000);

        // Detect and extract text.
        imageData.TextDetection =
            client.ImageModeration.OCRUrlInput("eng", "application/json", url, true);
        Thread.Sleep(1000);

        // Detect faces.
        imageData.FaceDetection =
            client.ImageModeration.FindFacesUrlInput("application/json", url, true);
        Thread.Sleep(1000);

        return imageData;
    }

**EvaluateUrlInput** メソッドは、Image Moderation REST API のラッパーです。
戻り値には、API 呼び出しから返されたオブジェクトが含まれています。

**OCRUrlInput** メソッドは、Image OCR REST API のラッパーです。
戻り値には、API 呼び出しから返されたオブジェクトが含まれています。

**FindFacesUrlInput** メソッドは、Image Find Faces REST API のラッパーです。
戻り値には、API 呼び出しから返されたオブジェクトが含まれています。

## <a name="process-the-image-urls-in-your-code"></a>コード内の画像の URL を処理する

次のコードを **Main** メソッドに追加する

    // Create an object to store the image moderation results.
    List<EvaluationData> evaluationData = new List<EvaluationData>();

    // Create an instance of the Content Moderator API wrapper.
    using (var client = Clients.NewClient())
    {
        // Read image URLs from the input file and evaluate each one.
        using (StreamReader inputReader = new StreamReader(ImageUrlFile))
        {
            while (!inputReader.EndOfStream)
            {
                string line = inputReader.ReadLine().Trim();
                if (line != String.Empty)
                {
                    EvaluationData imageData = EvaluateImage(client, line);
                    evaluationData.Add(imageData);
                }
            }
        }
    }

    // Save the moderation results to a file.
    using (StreamWriter outputWriter = new StreamWriter(OutputFile, false))
    {
        outputWriter.WriteLine(JsonConvert.SerializeObject(
            evaluationData, Formatting.Indented));

        outputWriter.Flush();
        outputWriter.Close();
    }

## <a name="run-the-program-and-review-the-output"></a>プログラムを実行して出力を確認する

次の JSON オブジェクトには、プログラムの出力が含まれています。

> [!NOTE]
> `isImageAdultClassified` は、特定の状況で露骨な性表現や成人向けの表現と考えられる画像の潜在的な存在を表します。
> `isImageRacyClassified` は、特定の状況で性的な示唆や成人向けの表現と考えられる画像の潜在的な存在を表します。
>

    [
    {
    "ImageUrl": "https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg",
    "ImageModeration": {
      "cacheID": "7733c303-3b95-4710-a41e-7a322ae81a15_636488005858745661",
      "result": false,
      "trackingId": "WE_e1f20803b4ed471fb5de7df551f5bd9f_ContentModerator.Preview_687c356d-0f00-4aeb-ae5f-c7555af80247",
      "adultClassificationScore": 0.019196987152099609,
      "isImageAdultClassified": false,
      "racyClassificationScore": 0.032390203326940536,
      "isImageRacyClassified": false,
      "advancedInfo": [
        {
          "key": "ImageDownloadTimeInMs",
          "value": "116"
        },
        {
          "key": "ImageSizeInBytes",
          "value": "273405"
        }
      ],
      "status": {
        "code": 3000.0,
        "description": "OK",
        "exception": null
      }
    },
    "TextDetection": {
      "status": {
        "code": 3000.0,
        "description": "OK",
        "exception": null
      },
      "metadata": [
        {
          "key": "ImageDownloadTimeInMs",
          "value": "12"
        },
        {
          "key": "ImageSizeInBytes",
          "value": "273405"
        }
      ],
      "trackingId": "WE_e1f20803b4ed471fb5de7df551f5bd9f_ContentModerator.Preview_814fa162-c5d6-4ca6-997b-30ed0686ca83",
      "cacheId": "3fb69496-c64b-4de9-affd-6dd6d23f3e78_636488005876558920",
      "language": "eng",
      "text": "IF WE DID \r\nALL \r\nTHE THINGS \r\nWE ARE \r\nCAPABLE \r\nOF DOING, \r\nWE WOULD \r\nLITERALLY \r\nASTOUND \r\nOURSELVE \r\n",
      "candidates": []
    },
    "FaceDetection": {
      "status": {
        "code": 3000.0,
        "description": "OK",
        "exception": null
      },
      "trackingId": "WE_e1f20803b4ed471fb5de7df551f5bd9f_ContentModerator.Preview_a2c40dbe-609d-4eb8-b01c-9988388804ea",
      "cacheId": "e4c0b500-ea8e-4a31-8fb3-35f98c4fbd65_636488005889528303",
      "result": false,
      "count": 0,
      "advancedInfo": [
        {
          "key": "ImageDownloadTimeInMs",
          "value": "11"
        },
        {
          "key": "ImageSizeInBytes",
          "value": "273405"
        }
      ],
      "faces": []
    }
    },
    {
    "ImageUrl": "https://moderatorsampleimages.blob.core.windows.net/samples/sample5.png",
    "ImageModeration": {
      "cacheID": "b4866aa2-5e69-44ed-806a-f9a5d618c8ae_636488005930693926",
      "result": false,
      "trackingId": "WE_e1f20803b4ed471fb5de7df551f5bd9f_ContentModerator.Preview_fdce5510-f689-4791-b081-c2ad54dcfe78",
      "adultClassificationScore": 0.0035635426174849272,
      "isImageAdultClassified": false,
      "racyClassificationScore": 0.021369094029068947,
      "isImageRacyClassified": false,
      "advancedInfo": [
        {
          "key": "ImageDownloadTimeInMs",
          "value": "109"
        },
        {
          "key": "ImageSizeInBytes",
          "value": "2278902"
        }
      ],
      "status": {
        "code": 3000.0,
        "description": "OK",
        "exception": null
      }
    },
    "TextDetection": {
      "status": {
        "code": 3000.0,
        "description": "OK",
        "exception": null
      },
      "metadata": [
        {
          "key": "ImageDownloadTimeInMs",
          "value": "46"
        },
        {
          "key": "ImageSizeInBytes",
          "value": "2278902"
        }
      ],
      "trackingId": "WE_e1f20803b4ed471fb5de7df551f5bd9f_ContentModerator.Preview_08a4bc19-6010-41bb-a440-a77278e167d8",
      "cacheId": "28b37471-41b3-4f79-bd23-965498bcff51_636488005950851288",
      "language": "eng",
      "text": "",
      "candidates": []
    },
    "FaceDetection": {
      "status": {
        "code": 3000.0,
        "description": "OK",
        "exception": null
      },
      "trackingId": "WE_e1f20803b4ed471fb5de7df551f5bd9f_ContentModerator.Preview_40f2ce07-14ba-47cd-ba09-58b557a89854",
      "cacheId": "ec9c1be3-99b7-4bd9-8bc4-dc958c74459f_636488005964914299",
      "result": true,
      "count": 6,
      "advancedInfo": [
        {
          "key": "ImageDownloadTimeInMs",
          "value": "60"
        },
        {
          "key": "ImageSizeInBytes",
          "value": "2278902"
        }
      ],
      "faces": [
        {
          "bottom": 598,
          "left": 44,
          "right": 268,
          "top": 374
        },
        {
          "bottom": 620,
          "left": 308,
          "right": 532,
          "top": 396
        },
        {
          "bottom": 575,
          "left": 594,
          "right": 773,
          "top": 396
        },
        {
          "bottom": 563,
          "left": 812,
          "right": 955,
          "top": 420
        },
        {
          "bottom": 611,
          "left": 972,
          "right": 1151,
          "top": 432
        },
        {
          "bottom": 510,
          "left": 1232,
          "right": 1456,
          "top": 286
        }
      ]
    }
    }
    ]


## <a name="next-steps---get-the-source-code"></a>次の手順 - ソース コードを入手する

.NET 用のこのクイック スタートや他の Content Moderator のクイック スタートのために、[Content Moderator .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) と [Visual Studio ソリューション](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator)を入手し、統合を開始します。
