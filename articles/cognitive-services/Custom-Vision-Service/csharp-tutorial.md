---
title: 'チュートリアル: Custom Vision SDK for C# を使って画像分類プロジェクトを作成する'
titlesuffix: Azure Cognitive Services
description: プロジェクトを作成し、タグを追加し、イメージをアップロードし、プロジェクトをトレーニングし、既定のエンドポイントを利用して予測を行います。
services: cognitive-services
author: anrothMSFT
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: tutorial
ms.date: 05/03/2018
ms.author: anroth
ms.openlocfilehash: e046fe452a13384ae7929be805c6252d6ad2fbf9
ms.sourcegitcommit: 5c00e98c0d825f7005cb0f07d62052aff0bc0ca8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/24/2018
ms.locfileid: "49953045"
---
# <a name="tutorial-create-an-image-classification-project-with-the-custom-vision-sdk-for-c"></a>チュートリアル: Custom Vision SDK for C# を使って画像分類プロジェクトを作成する

C# アプリケーションで Custom Vision Service SDK を使用する方法について説明します。 作成後、タグを追加し、イメージをアップロードし、プロジェクトをトレーニングし、プロジェクトの既定の予測エンドポイント URL を取得し、エンドポイントを使用してイメージをプログラミングでテストできます。 Custom Vision Service API を利用し、Windows 用の独自のアプリをビルドするためのテンプレートとしてこのオープン ソース サンプルを利用します。

## <a name="prerequisites"></a>前提条件

* Windows 用 Visual Studio 2017 の任意のエディション。

## <a name="get-the-custom-vision-sdk-and-samples"></a>Custom Vision SDK とサンプルを入手する
このサンプルをビルドするには、次の Custom Vision SDK NuGet パッケージが必要です。

* [Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training/)
* [Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction/)

[C# サンプル](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/CustomVision)と共に画像をダウンロードできます。

## <a name="get-the-training-and-prediction-keys"></a>トレーニング キーと予測キーを取得する

このサンプルで使用されるキーを取得するには、[Custom Vision Web ページ](https://customvision.ai)にアクセスし、右上にある__歯車アイコン__を選択します。 __[アカウント]__ セクションで、__[Training Key]\(トレーニング キー\)__ フィールドと __[Prediction Key]\(予測キー\)__ フィールドから値をコピーします。

![キー UI の画像](./media/csharp-tutorial/training-prediction-keys.png)

## <a name="understand-the-code"></a>コードの理解

Visual Studio で、SDK プロジェクトの `Samples/CustomVision.Sample/` ディレクトリにあるプロジェクトを開きます。

このアプリケーションは、先ほど取得したトレーニング キーを使用して、__My New Project__ という名前の新しいプロジェクトを作成します。 その後、イメージをアップロードして分類器のトレーニングおよびテストを行います。 分類器は、木が __Hemlock__ (ドクニンジン) であるか __Japanese Cherry__ (桜) であるかを識別します。

この例の主な機能は、以下のコード スニペットに実装されています。

* __新しい Custom Vision Service プロジェクトを作成する__

    ```csharp
     // Create a new project
    Console.WriteLine("Creating new project:");
    var project = trainingApi.CreateProject("My New Project");
    ```

* __プロジェクトにタグを作成する__

    ```csharp
    // Make two tags in the new project
    var hemlockTag = trainingApi.CreateTag(project.Id, "Hemlock");
    var japaneseCherryTag = trainingApi.CreateTag(project.Id, "Japanese Cherry");
    ```

* __イメージをアップロードし、タグ付けする__

    ```csharp
    // Add some images to the tags
    Console.WriteLine("\tUploading images");
    LoadImagesFromDisk();

    // Images can be uploaded one at a time
    foreach (var image in hemlockImages)
    {
        using (var stream = new MemoryStream(File.ReadAllBytes(image)))
        {
            trainingApi.CreateImagesFromData(project.Id, stream, new List<string>() { hemlockTag.Id.ToString() });
        }
    }

    // Or uploaded in a single batch 
    var imageFiles = japaneseCherryImages.Select(img => new ImageFileCreateEntry(Path.GetFileName(img), File.ReadAllBytes(img))).ToList();
    trainingApi.CreateImagesFromFiles(project.Id, new ImageFileCreateBatch(imageFiles, new List<Guid>() { japaneseCherryTag.Id }));
    ```

* __分類器をトレーニングする__

    ```csharp
    // Now there are images with tags start training the project
    Console.WriteLine("\tTraining");
    var iteration = trainingApi.TrainProject(project.Id);

    // The returned iteration will be in progress, and can be queried periodically to see when it has completed
    while (iteration.Status == "Completed")
    {
        Thread.Sleep(1000);

        // Re-query the iteration to get it's updated status
        iteration = trainingApi.GetIteration(project.Id, iteration.Id);
    }
    ```

* __予測エンドポイント用の既定のイテレーションを設定する__

    ```csharp
    // The iteration is now trained. Make it the default project endpoint
    iteration.IsDefault = true;
    trainingApi.UpdateIteration(project.Id, iteration.Id, iteration);
    Console.WriteLine("Done!\n");
    ```

* __予測エンドポイントを作成する__
 
    ```csharp
    // Create a prediction endpoint, passing in obtained prediction key
    PredictionEndpoint endpoint = new PredictionEndpoint() { ApiKey = predictionKey };
    ```
 
* __イメージを予測エンドポイントに送信する__

    ```csharp
    // Make a prediction against the new project
    Console.WriteLine("Making a prediction:");
    var result = endpoint.PredictImage(project.Id, testImage);

    // Loop over each prediction and write out the results
    foreach (var c in result.Predictions)
    {
        Console.WriteLine($"\t{c.TagName}: {c.Probability:P1}");
    }
    ```

## <a name="run-the-application"></a>アプリケーションの実行

1. 次の変更を行い、アプリケーションにトレーニング キーと予測キーを追加します。

    * 次の行に__トレーニング キー__を追加します。

        ```csharp
        string trainingKey = "<your key here>";
        ```

    * 次の行に __予測キー__ を追加します。

        ```csharp
        string predictionKey = "<your key here>";
        ```

2. アプリケーションを実行します。 アプリケーションを実行すると、次の出力がコンソールに書き込まれます。

    ```
    Creating new project:
            Uploading images
            Training
    Done!

    Making a prediction:
            Hemlock: 95.0%
            Japanese Cherry: 0.0%
    ```

3. 任意のキーを押してアプリケーションを終了します。
