---
title: 'Computer Vision Python クイック スタート: サムネイルの作成 | Microsoft Docs'
titleSuffix: Microsoft Cognitive Services
description: このクイック スタートでは、Cognitive Services の Computer Vision と Python を使って、画像からサムネイルを生成します。
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: v-deken
ms.openlocfilehash: bc1d01cd4b8f15ba627d917825ee5205c34f5cdf
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/31/2018
ms.locfileid: "43772120"
---
# <a name="quickstart-generate-a-thumbnail---rest-python"></a>クイック スタート: サムネイルの生成 - REST、Python

このクイック スタートでは、Computer Vision を使って、画像からサムネイルを生成します。

このクイック スタートは、[MyBinder](https://mybinder.org) 上で Jupyter Notebook を使い、ステップ バイ ステップで実行することができます。 Binder を起動するには、次のボタンを選択します。

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=VisionAPI.ipynb)

## <a name="prerequisites"></a>前提条件

Computer Vision を使用するにはサブスクリプション キーが必要です。「[サブスクリプション キーを取得する](../Vision-API-How-to-Topics/HowToSubscribe.md)」をご覧ください。

## <a name="intelligently-generate-a-thumbnail"></a>サムネイルをインテリジェントに生成する

[サムネイル取得メソッド](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fb)を使うと、画像のサムネイルを生成することができます。 その際に指定する高さと幅は、入力画像の縦横比と異なっていてもかまいません。 Computer Vision は、スマート トリミングを使ってインテリジェントに関心領域を識別し、その領域に基づいてトリミングの座標を生成します。

このサンプルを実行するには、次の手順を実行します。

1. 次のコードを新しい Python スクリプト ファイルにコピーします。
1. `<Subscription Key>` を、有効なサブスクリプション キーに置き換えます。
1. 必要に応じて `vision_base_url` の値を、サブスクリプション キーを取得した場所に変更します。
1. 必要に応じて `image_url` の値を、別の画像に変更します。
1. スクリプトを実行します。

次のコードでは、Python `requests` ライブラリを使って、Computer Vision Analyze Image API を呼び出します。 API キーは `headers` ディクショナリで渡します。 サムネイルのサイズは、`params` ディクショナリで渡します。 サムネイルは、応答でバイト配列として返されます。

## <a name="get-thumbnail-request"></a>サムネイル取得要求

```python
import requests
# If you are using a Jupyter notebook, uncomment the following line.
#%matplotlib inline
import matplotlib.pyplot as plt
from PIL import Image
from io import BytesIO

# Replace <Subscription Key> with your valid subscription key.
subscription_key = "<Subscription Key>"
assert subscription_key

# You must use the same region in your REST call as you used to get your
# subscription keys. For example, if you got your subscription keys from
# westus, replace "westcentralus" in the URI below with "westus".
#
# Free trial subscription keys are generated in the westcentralus region.
# If you use a free trial subscription key, you shouldn't need to change
# this region.
vision_base_url = "https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/"

thumbnail_url = vision_base_url + "generateThumbnail"

# Set image_url to the URL of an image that you want to analyze.
image_url = "https://upload.wikimedia.org/wikipedia/commons/9/94/Bloodhound_Puppy.jpg"

headers = {'Ocp-Apim-Subscription-Key': subscription_key}
params  = {'width': '50', 'height': '50', 'smartCropping': 'true'}
data    = {'url': image_url}
response = requests.post(thumbnail_url, headers=headers, params=params, json=data)
response.raise_for_status()

thumbnail = Image.open(BytesIO(response.content))

# Display the thumbnail.
plt.imshow(thumbnail)
plt.axis("off")

# Verify the thumbnail size.
print("Thumbnail is {0}-by-{1}".format(*thumbnail.size))
```

## <a name="next-steps"></a>次の手順

Computer Vision を使用して、光学文字認識 (OCR) を実行し、スマートにクロップされたサムネイルを作成するほか、イメージ内の視覚的な特徴 (顔など) を検出、カテゴライズ、タグ付け、および記述する Python アプリケーションについて説明します。 Computer Vision API を簡単に試す場合は、[Open API テスト コンソール](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console)をお試しください。

> [!div class="nextstepaction"]
> [Computer Vision API Python チュートリアル](../Tutorials/PythonTutorial.md)
