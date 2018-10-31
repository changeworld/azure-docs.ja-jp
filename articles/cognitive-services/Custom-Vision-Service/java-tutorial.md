---
title: 'チュートリアル: Custom Vision SDK for Java を使って画像分類プロジェクトを作成する'
titlesuffix: Azure Cognitive Services
description: 既定のエンドポイントを利用し、プロジェクトを作成し、タグを追加し、画像をアップロードし、プロジェクトをトレーニングし、予測を行います。
services: cognitive-services
author: areddish
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: tutorial
ms.date: 08/28/2018
ms.author: areddish
ms.openlocfilehash: e302fc580d9c83d269f0deedd051a3ea23bd274e
ms.sourcegitcommit: 5c00e98c0d825f7005cb0f07d62052aff0bc0ca8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/24/2018
ms.locfileid: "49957215"
---
# <a name="tutorial-create-an-image-classification-project-with-the-custom-vision-sdk-for-java"></a>チュートリアル: Custom Vision SDK for Java を使って画像分類プロジェクトを作成する

Custom Vision Service と Java を使って、画像分類プロジェクトを作成する方法について説明します。 作成後、タグを追加し、画像をアップロードし、プロジェクトをトレーニングし、プロジェクトの既定の予測エンドポイント URL を取得し、このエンドポイントを使用して画像をプログラミングでテストできます。 Custom Vision API を利用し、独自のアプリをビルドするためのテンプレートとしてこのオープン ソース サンプルを利用します。

## <a name="prerequisites"></a>前提条件

- JDK 7 または 8 がインストールされていること。
- Maven がインストールされていること。

## <a name="get-the-training-and-prediction-keys"></a>トレーニング キーと予測キーを取得する

このサンプルで使用されるキーを取得するには、[Custom Vision Web ページ](https://customvision.ai)にアクセスし、右上にある__歯車アイコン__を選択します。 __[アカウント]__ セクションで、__[Training Key]\(トレーニング キー\)__ フィールドと __[Prediction Key]\(予測キー\)__ フィールドから値をコピーします。

![キー UI の画像](./media/python-tutorial/training-prediction-keys.png)

## <a name="install-the-custom-vision-service-sdk"></a>Custom Vision Service SDK をインストールする

Maven Central Repository から Custom Vision SDK をインストールできます。
* [Training SDK](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-customvision-training)
* [Prediction SDK](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-customvision-prediction)

## <a name="understand-the-code"></a>コードの理解

画像を含む完全なプロジェクトは、[Java 用 Custom Vision Azure サンプル リポジトリ](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master)から手に入ります。 

好みの Java IDE を使用して、`Vision/CustomVision` プロジェクトを開きます。 

このアプリケーションは、先ほど取得したトレーニング キーを使用して、__Sample Java Project__ という名前の新しいプロジェクトを作成します。 その後、イメージをアップロードして分類器のトレーニングおよびテストを行います。 分類器は、木が __Hemlock__ (ドクニンジン) であるか __Japanese Cherry__ (桜) であるかを識別します。

この例の主な機能は、以下のコード スニペットに実装されています。

## <a name="create-a-custom-vision-service-project"></a>Custom Vision Service プロジェクトを作成する

> [!IMPORTANT]
> 以前に取得したトレーニング キーの値を `trainingApiKey` に設定します。

```java
final String trainingApiKey = "insert your training key here";
TrainingApi trainClient = CustomVisionTrainingManager.authenticate(trainingApiKey);

Trainings trainer = trainClient.trainings();

System.out.println("Creating project...");
Project project = trainer.createProject()
            .withName("Sample Java Project")
            .execute();
```

## <a name="add-tags-to-your-project"></a>タグをプロジェクトに追加する

```java
// create hemlock tag
Tag hemlockTag = trainer.createTag()
    .withProjectId(project.id())
    .withName("Hemlock")
    .execute();

// create cherry tag
Tag cherryTag = trainer.createTag()
    .withProjectId(project.id())
    .withName("Japanese Cherry")
    .execute();
```

## <a name="upload-images-to-the-project"></a>画像をプロジェクトにアップロードする

サンプルは、最終的なパッケージに画像を含むようにセットアップされます。 画像は、jar の resources セクションから読み取られて、サービスにアップロードされます。

```java
System.out.println("Adding images...");
for (int i = 1; i <= 10; i++) {
    String fileName = "hemlock_" + i + ".jpg";
    byte[] contents = GetImage("/Hemlock", fileName);
    AddImageToProject(trainer, project, fileName, contents, hemlockTag.id(), null);
}

for (int i = 1; i <= 10; i++) {
    String fileName = "japanese_cherry_" + i + ".jpg";
    byte[] contents = GetImage("/Japanese Cherry", fileName);
    AddImageToProject(trainer, project, fileName, contents, cherryTag.id(), null);
}
```

前のスニペット コードでは、リソース ストリームとして画像を取得してサービスにアップロードする 2 つのヘルパー関数を使用しています。

```java
private static void AddImageToProject(Trainings trainer, Project project, String fileName, byte[] contents, UUID tag)
{
    System.out.println("Adding image: " + fileName);

    ImageFileCreateEntry file = new ImageFileCreateEntry()
        .withName(fileName)
        .withContents(contents);

    ImageFileCreateBatch batch = new ImageFileCreateBatch()
        .withImages(Collections.singletonList(file));

    batch = batch.withTagIds(Collections.singletonList(tag));

    trainer.createImagesFromFiles(project.id(), batch);
}

private static byte[] GetImage(String folder, String fileName)
{
    try {
        return ByteStreams.toByteArray(CustomVisionSamples.class.getResourceAsStream(folder + "/" + fileName));
    } catch (Exception e) {
        System.out.println(e.getMessage());
        e.printStackTrace();
    }

    return null;
}
```

## <a name="train-the-project"></a>プロジェクトをトレーニングする

これにより、プロジェクトに最初のイテレーションが作成されて、それが既定のイテレーションに指定されます。 

```java
System.out.println("Training...");
Iteration iteration = trainer.trainProject(project.id());

while (iteration.status().equals("Training"))
{
    System.out.println("Training Status: "+ iteration.status());
    Thread.sleep(1000);
    iteration = trainer.getIteration(project.id(), iteration.id());
}

System.out.println("Training Status: "+ iteration.status());
trainer.updateIteration(project.id(), iteration.id(), iteration.withIsDefault(true));
```

## <a name="get-and-use-the-default-prediction-endpoint"></a>既定の予測エンドポイントを取得して使用する

> [!IMPORTANT]
> 以前に取得した予測キーの値を `predictionApiKey` に設定します。

```java
final String predictionApiKey = "insert your prediction key here";
PredictionEndpoint predictClient = CustomVisionPredictionManager.authenticate(predictionApiKey);

// Use below for predictions from a url
// String url = "some url";
// ImagePrediction results = predictor.predictions().predictImage()
//                         .withProjectId(project.id())
//                         .withUrl(url)
//                         .execute();

// load test image
byte[] testImage = GetImage("/Test", "test_image.jpg");

// predict
ImagePrediction results = predictor.predictions().predictImage()
    .withProjectId(project.id())
    .withImageData(testImage)
    .execute();

for (Prediction prediction: results.predictions())
{
    System.out.println(String.format("\t%s: %.2f%%", prediction.tagName(), prediction.probability() * 100.0f));
}
```

## <a name="run-the-example"></a>例を実行する

Maven を使用してソリューションをコンパイルして実行するには:

```
mvn compile exec:java
```

アプリケーションの出力は次のテキストのようになります。

```
Creating project...
Adding images...
Adding image: hemlock_1.jpg
Adding image: hemlock_2.jpg
Adding image: hemlock_3.jpg
Adding image: hemlock_4.jpg
Adding image: hemlock_5.jpg
Adding image: hemlock_6.jpg
Adding image: hemlock_7.jpg
Adding image: hemlock_8.jpg
Adding image: hemlock_9.jpg
Adding image: hemlock_10.jpg
Adding image: japanese_cherry_1.jpg
Adding image: japanese_cherry_2.jpg
Adding image: japanese_cherry_3.jpg
Adding image: japanese_cherry_4.jpg
Adding image: japanese_cherry_5.jpg
Adding image: japanese_cherry_6.jpg
Adding image: japanese_cherry_7.jpg
Adding image: japanese_cherry_8.jpg
Adding image: japanese_cherry_9.jpg
Adding image: japanese_cherry_10.jpg
Training...
Training status: Training
Training status: Training
Training status: Completed
Done!
        Hemlock: 93.53%
        Japanese Cherry: 0.01%
```