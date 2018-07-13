---
title: C# アプリケーションから Custom Vision Service を使用する - Azure Cognitive Services | Microsoft Docs
description: Microsoft Cognitive Services で Custom Vision API を使用する基本的な C# アプリについて確認します。 プロジェクトを作成し、タグを追加し、イメージをアップロードし、プロジェクトをトレーニングし、既定のエンドポイントを利用して予測を行います。
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: article
ms.date: 05/03/2018
ms.author: anroth
ms.openlocfilehash: 80cb022808748ed2c60dff7c363d6020cb4043a8
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2018
ms.locfileid: "35374720"
---
# <a name="use-the-custom-vision-service-from-a-c35-application"></a>C&#35; アプリケーションから Custom Vision Service を使用する

C# アプリケーションから Custom Vision Service を使用する方法について説明します。 作成後、タグを追加し、イメージをアップロードし、プロジェクトをトレーニングし、プロジェクトの既定の予測エンドポイント URL を取得し、エンドポイントを使用してイメージをプログラミングでテストできます。 Custom Vision Service API を利用し、Windows 用の独自のアプリをビルドするためのテンプレートとしてこのオープン ソース サンプルを利用します。

## <a name="prerequisites"></a>前提条件

* Windows 用 Visual Studio 2015 または 2017 の任意のエディション。

* [Custom Vision Service SDK](http://github.com/Microsoft/Cognitive-CustomVision-Windows/)。 このドキュメントで使用されているサンプルや画像も含まれています。

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

    * 次の行に__予測キー__を追加します。

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
