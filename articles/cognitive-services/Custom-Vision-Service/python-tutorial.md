---
title: Custom Vision Service Python による作成のチュートリアル - Azure Cognitive Services | Microsoft Docs
description: Microsoft Cognitive Services で Custom Vision API を使用する基本的な Python アプリについて確認します。 既定のエンドポイントを利用し、プロジェクトを作成し、タグを追加し、画像をアップロードし、プロジェクトをトレーニングし、予測を行います。
services: cognitive-services
author: areddish
manager: chbuehle
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: article
ms.date: 05/07/2018
ms.author: areddish
ms.openlocfilehash: 0359935bf266d4f2a5cf845dd0d23183f4f77b72
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2018
ms.locfileid: "35375277"
---
# <a name="custom-vision-api-python-tutorial"></a>Custom Vision API Python チュートリアル

Custom Vision Service および基本の Python スクリプトを使って、画像分類プロジェクトを作成する方法について説明します。 作成後、タグを追加し、画像をアップロードし、プロジェクトをトレーニングし、プロジェクトの既定の予測エンドポイント URL を取得し、このエンドポイントを使用して画像をプログラミングでテストできます。 Custom Vision API を利用し、独自のアプリをビルドするためのテンプレートとしてこのオープン ソース サンプルを利用します。



## <a name="prerequisites"></a>前提条件

- Python 2.7 または Python 3.5+。
- pip ツール。

## <a name="get-the-training-and-prediction-keys"></a>トレーニング キーと予測キーを取得する

このサンプルで使用されるキーを取得するには、[Custom Vision Web ページ](https://customvision.ai)にアクセスし、右上にある__歯車アイコン__を選択します。 __[アカウント]__ セクションで、__[Training Key]\(トレーニング キー\)__ フィールドと __[Prediction Key]\(予測キー\)__ フィールドから値をコピーします。

![キー UI の画像](./media/python-tutorial/training-prediction-keys.png)

## <a name="install-the-custom-vision-service-sdk"></a>Custom Vision Service SDK をインストールする

Custom VIsion Service SDK をインストールするには、以下のコマンドを使用します。

```
pip install azure-cognitiveservices-vision-customvision
```

## <a name="get-example-images"></a>例の画像を取得する

この例では、[https://github.com/Microsoft/Cognitive-CustomVision-Windows](https://github.com/Microsoft/Cognitive-CustomVision-Windows/tree/master/Samples/Images) プロジェクトの `Samples/Images` ディレクトリからの画像を使用しています。 プロジェクトを複製またはダウンロードして、開発環境に抽出します。

## <a name="create-a-custom-vision-service-project"></a>Custom Vision Service プロジェクトを作成する

新しい Custom Vision Service プロジェクトを作成するには、`sample.py` という名前の新しいファイルを作成します。 ファイルのコンテンツとして、以下のコードを使用します。

> [!IMPORTANT]
> 以前に取得したトレーニング キーの値を `training_key` に設定します。
>
> 以前に取得した予測キーの値を `prediction_key` に設定します。

```python
from azure.cognitiveservices.vision.customvision.training import training_api
from azure.cognitiveservices.vision.customvision.training.models import ImageUrlCreateEntry

# Replace with a valid key
training_key = "<your training key>"
prediction_key = "<your prediction key>"

trainer = training_api.TrainingApi(training_key)

# Create a new project
print ("Creating project...")
project = trainer.create_project("My Project")
```

## <a name="add-tags-to-your-project"></a>タグをプロジェクトに追加する

タグをプロジェクトに追加するには、`sample.py` ファイルの末尾に以下のコードを追加します。

```python
# Make two tags in the new project
hemlock_tag = trainer.create_tag(project.id, "Hemlock")
cherry_tag = trainer.create_tag(project.id, "Japanese Cherry")
```

## <a name="upload-images-to-the-project"></a>画像をプロジェクトにアップロードする

サンプルの画像をプロジェクトに追加するには、タグ作成後、次のコードを挿入します。 このコードでは、対応するタグで画像をアップロードします。

> [!IMPORTANT]
>
> 以前に Cognitive-CustomVision-Windows プロジェクトをダウンロードした場所に基づいて画像へのパスを変更します。

```python
base_image_url = "https://raw.githubusercontent.com/Microsoft/Cognitive-CustomVision-Windows/master/Samples/"

print ("Adding images...")
for image_num in range(1,10):
    image_url = base_image_url + "Images/Hemlock/hemlock_{}.jpg".format(image_num)
    trainer.create_images_from_urls(project.id, [ ImageUrlCreateEntry(url=image_url, tag_ids=[ hemlock_tag.id ] ) ])

for image_num in range(1,10):
    image_url = base_image_url + "Images/Japanese Cherry/japanese_cherry_{}.jpg".format(image_num)
    trainer.create_images_from_urls(project.id, [ ImageUrlCreateEntry(url=image_url, tag_ids=[ cherry_tag.id ] ) ])


# Alternatively, if the images were on disk in a folder called Images alongside the sample.py, then
# they can be added by using the following:
#
#import os
#hemlock_dir = "Images\\Hemlock"
#for image in os.listdir(os.fsencode("Images\\Hemlock")):
#    with open(hemlock_dir + "\\" + os.fsdecode(image), mode="rb") as img_data: 
#        trainer.create_images_from_data(project.id, img_data, [ hemlock_tag.id ])
#
#cherry_dir = "Images\\Japanese Cherry"
#for image in os.listdir(os.fsencode("Images\\Japanese Cherry")):
#    with open(cherry_dir + "\\" + os.fsdecode(image), mode="rb") as img_data: 
#        trainer.create_images_from_data(project.id, img_data, [ cherry_tag.id ])
```

## <a name="train-the-project"></a>プロジェクトをトレーニングする

分類子をトレーニングするには、`sample.py` ファイルの末尾に以下のコードを追加します。

```python
import time

print ("Training...")
iteration = trainer.train_project(project.id)
while (iteration.status != "Completed"):
    iteration = trainer.get_iteration(project.id, iteration.id)
    print ("Training status: " + iteration.status)
    time.sleep(1)

# The iteration is now trained. Make it the default project endpoint
trainer.update_iteration(project.id, iteration.id, is_default=True)
print ("Done!")
```

## <a name="get-and-use-the-default-prediction-endpoint"></a>既定の予測エンドポイントを取得して使用する

画像を予測エンドポイントに送信し、予測を取得するには、`sample.py` ファイルの末尾に以下のコードを追加します。

```python
from azure.cognitiveservices.vision.customvision.prediction import prediction_endpoint
from azure.cognitiveservices.vision.customvision.prediction.prediction_endpoint import models

# Now there is a trained endpoint that can be used to make a prediction

predictor = prediction_endpoint.PredictionEndpoint(prediction_key)

test_img_url = base_image_url + "Images/Test/test_image.jpg"
results = predictor.predict_image_url(project.id, iteration.id, url=test_img_url)

# Alternatively, if the images were on disk in a folder called Images alongside the sample.py, then
# they can be added by using the following.
#
# Open the sample image and get back the prediction results.
# with open("Images\\test\\test_image.jpg", mode="rb") as test_data:
#     results = predictor.predict_image(project.id, test_data, iteration.id)

# Display the results.
for prediction in results.predictions:
    print ("\t" + prediction.tag_name + ": {0:.2f}%".format(prediction.probability * 100))
```

## <a name="run-the-example"></a>例を実行する

ソリューションを実行する 予測結果がコンソールに表示されます。

```
python sample.py
```

アプリケーションの出力は次のテキストのようになります。

```
Creating project...
Adding images...
Training...
Training status: Training
Training status: Completed
Done!
        Hemlock: 93.53%
        Japanese Cherry: 0.01%
```