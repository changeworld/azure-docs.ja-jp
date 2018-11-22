---
title: 'クイック スタート: Custom Vision SDK for Python を使って画像分類プロジェクトを作成する'
titlesuffix: Azure Cognitive Services
description: Python SDK を使用して、プロジェクトの作成、タグの追加、画像のアップロード、プロジェクトのトレーニング、予測を行います。
services: cognitive-services
author: areddish
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: quickstart
ms.date: 11/2/2018
ms.author: areddish
ms.openlocfilehash: c110abd9354134d52d4f82f7c828fc5e13f218a8
ms.sourcegitcommit: fa758779501c8a11d98f8cacb15a3cc76e9d38ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/20/2018
ms.locfileid: "52262997"
---
# <a name="quickstart-create-an-image-classification-project-with-the-custom-vision-python-sdk"></a>クイック スタート: Custom Vision Python SDK を使って画像分類プロジェクトを作成する

この記事では、Custom Vision SDK と Python を使用して画像分類モデルを構築する際の足掛かりとして役立つ情報とサンプル コードを紹介します。 作成後、タグを追加し、イメージをアップロードし、プロジェクトをトレーニングし、プロジェクトの既定の予測エンドポイント URL を取得し、エンドポイントを使用してイメージをプログラミングでテストできます。 この例は、独自の Python アプリケーションを構築するためのテンプレートとしてご利用ください。 分類モデルの構築と使用のプロセスをコード "_なし_" で行う場合は、[ブラウザー ベースのガイダンス](getting-started-build-a-classifier.md)を参照してください。

## <a name="prerequisites"></a>前提条件

- [Python 2.7 以降または 3.5 以降](https://www.python.org/downloads/)
- [pip](https://pip.pypa.io/en/stable/installing/) ツール

## <a name="install-the-custom-vision-sdk"></a>Custom Vision SDK をインストールする

Custom Vision Service SDK for Python をインストールするには、PowerShell で次のコマンドを実行します。

```PowerShell
pip install azure-cognitiveservices-vision-customvision
```

[!INCLUDE [get-keys](includes/get-keys.md)]

[!INCLUDE [python-get-images](includes/python-get-images.md)]


## <a name="add-the-code"></a>コードの追加

目的のプロジェクト ディレクトリに、*sample.py* という新しいファイルを作成します。

### <a name="create-the-custom-vision-service-project"></a>Custom Vision Service プロジェクトを作成する

新しい Custom Vision Service プロジェクトを作成するための次のコードをスクリプトに追加します。 該当する各定義にサブスクリプション キーを挿入します。

```Python
from azure.cognitiveservices.vision.customvision.training import CustomVisionTrainingClient
from azure.cognitiveservices.vision.customvision.training.models import ImageUrlCreateEntry

ENDPOINT = "https://southcentralus.api.cognitive.microsoft.com"

# Replace with a valid key
training_key = "<your training key>"
prediction_key = "<your prediction key>"

trainer = CustomVisionTrainingClient(training_key, endpoint=ENDPOINT)

# Create a new project
print ("Creating project...")
project = trainer.create_project("My New Project")
```

### <a name="create-tags-in-the-project"></a>プロジェクトにタグを作成する

プロジェクトに分類タグを作成するため、*sample.py* の最後に次のコードを追加します。

```Python
# Make two tags in the new project
hemlock_tag = trainer.create_tag(project.id, "Hemlock")
cherry_tag = trainer.create_tag(project.id, "Japanese Cherry")
```

### <a name="upload-and-tag-images"></a>画像をアップロードし、タグ付けする

サンプルの画像をプロジェクトに追加するには、タグ作成後、次のコードを挿入します。 このコードでは、それぞれの画像を対応するタグと共にアップロードします。 Cognitive Services Python SDK Samples プロジェクトをダウンロードした場所に応じて、画像のベース URL パスを入力する必要があります。

> [!NOTE]
> 画像のパスは、事前に Cognitive Services Python SDK Samples プロジェクトをダウンロードした場所に応じて変更する必要があります。

```Python
base_image_url = "<path to project>"

print ("Adding images...")
for image_num in range(1,10):
    image_url = base_image_url + "Images/Hemlock/hemlock_{}.jpg".format(image_num)
    trainer.create_images_from_urls(project.id, [ ImageUrlCreateEntry(url=image_url, tag_ids=[ hemlock_tag.id ] ) ])

for image_num in range(1,10):
    image_url = base_image_url + "Images/Japanese Cherry/japanese_cherry_{}.jpg".format(image_num)
    trainer.create_images_from_urls(project.id, [ ImageUrlCreateEntry(url=image_url, tag_ids=[ cherry_tag.id ] ) ])
```

### <a name="train-the-classifier"></a>分類子をトレーニングする

このコードにより、プロジェクトに最初のイテレーションが作成されて、それが既定のイテレーションに指定されます。 既定のイテレーションは、予測要求に応答するモデルのバージョンを表します。 モデルを再トレーニングするたびに更新する必要があります。

```Python
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

### <a name="get-and-use-the-default-prediction-endpoint"></a>既定の予測エンドポイントを取得して使用する

画像を予測エンドポイントに送信し、予測を取得するには、ファイルの末尾に以下のコードを追加します。

```python
from azure.cognitiveservices.vision.customvision.prediction import CustomVisionPredictionClient
from azure.cognitiveservices.vision.customvision.prediction.prediction_endpoint import models

# Now there is a trained endpoint that can be used to make a prediction

predictor = CustomVisionPredictionClient(prediction_key, endpoint=ENDPOINT)

test_img_url = base_image_url + "Images/Test/test_image.jpg"
results = predictor.predict_image_url(project.id, iteration.id, url=test_img_url)

# Display the results.
for prediction in results.predictions:
    print ("\t" + prediction.tag_name + ": {0:.2f}%".format(prediction.probability * 100))
```

## <a name="run-the-application"></a>アプリケーションの実行

*sample.py* を実行します。

```PowerShell
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

**<base_image_url>/Images/Test/** 内のテスト画像にタグが適切に付けられていることを確認してください。 [Custom Vision Web サイト](https://customvision.ai)に戻って、新しく作成されたプロジェクトの現在の状態を確認してもかまいません。

[!INCLUDE [clean-ic-project](includes/clean-ic-project.md)]

## <a name="next-steps"></a>次の手順

以上、画像の分類処理の各ステップをコードでどのように実装するかを見てきました。 このサンプルで実行したトレーニングのイテレーションは 1 回だけですが、多くの場合、精度を高めるために、モデルのトレーニングとテストは複数回行う必要があります。

> [!div class="nextstepaction"]
> [モデルのテストと再トレーニング](test-your-model.md)