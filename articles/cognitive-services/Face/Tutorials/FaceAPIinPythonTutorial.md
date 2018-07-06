---
title: Face API Python チュートリアル | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: Python SDK で Face API を使用して Cognitive Services 内の画像から人の顔を検出する方法を説明します。
services: cognitive-services
author: SteveMSFT
manager: corncar
ms.service: cognitive-services
ms.component: face-api
ms.topic: article
ms.date: 03/01/2018
ms.author: sbowles
ms.openlocfilehash: 90d74d8df2ed59e6f3313ef7c620284d1022a667
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/27/2018
ms.locfileid: "37049113"
---
# <a name="getting-started-with-face-api-in-python-tutorial"></a>Python での Face API の使用開始チュートリアル

このチュートリアルでは、Python SDK から Face API を呼び出して画像内の人の顔を検出する方法を説明します。

## <a name="prerequisites"></a> 前提条件

このチュートリアルを使用するには、次の作業が必要となります。

- Python 2.7 以降または Python 3.5 以降をインストールします。
- pip をインストールします。
- 次のようにして Face API 用に Python SDK をインストールします。

```bash
pip install cognitive_face
```

- Microsoft Cognitive Services の[サブスクリプション キー](https://azure.microsoft.com/try/cognitive-services/)を入手します。 このチュートリアルでは、プライマリ キーまたはセカンダリ キーのどちらでも使用できます。 (Face API を使用するには、有効なサブスクリプション キーが必要であることに注意してください。)

## <a name="sdk-example"></a> 画像内の顔を検出する

```python
import cognitive_face as CF

KEY = '<Subscription Key>'  # Replace with a valid subscription key (keeping the quotes in place).
CF.Key.set(KEY)

BASE_URL = 'https://westus.api.cognitive.microsoft.com/face/v1.0/'  # Replace with your regional Base URL
CF.BaseUrl.set(BASE_URL)

# You can use this example JPG or replace the URL below with your own URL to a JPEG image.
img_url = 'https://raw.githubusercontent.com/Microsoft/Cognitive-Face-Windows/master/Data/detection1.jpg'
faces = CF.face.detect(img_url)
print(faces)
```

結果の例を次に示します。 これは、検出された顔の `list` です。 リスト内の各項目は `dict` インスタンスであり、`faceId` は検出された顔の一意 ID、`faceRectangle` は検出された顔の位置の記述です。 顔 ID の有効期間は 24 時間です。

```python
[{u'faceId': u'68a0f8cf-9dba-4a25-afb3-f9cdf57cca51', u'faceRectangle': {u'width': 89, u'top': 66, u'height': 89, u'left': 446}}]
```

## <a name="draw-rectangles-around-the-faces"></a>顔の周囲に四角形を描画する

前のコマンドで受け取った json 座標を使うと、画像に四角形を描画して、各顔を視覚的に表すことができます。 `PIL` イメージング モジュールを使うには、`pip install Pillow` を行う必要があります。  ファイルの先頭に、次を追加します。

```python
import requests
from io import BytesIO
from PIL import Image, ImageDraw
```

次に、スクリプトの `print(faces)` の後に次を追加します。

```python
#Convert width height to a point in a rectangle
def getRectangle(faceDictionary):
    rect = faceDictionary['faceRectangle']
    left = rect['left']
    top = rect['top']
    bottom = left + rect['height']
    right = top + rect['width']
    return ((left, top), (bottom, right))

#Download the image from the url
response = requests.get(img_url)
img = Image.open(BytesIO(response.content))

#For each face returned use the face rectangle and draw a red box.
draw = ImageDraw.Draw(img)
for face in faces:
    draw.rectangle(getRectangle(face), outline='red')

#Display the image in the users default image browser.
img.show()
```

## <a name='further'></a> さらに探索する

Face API をさらに調べることができるように、このチュートリアルでは GUI のサンプルを提供します。 これを実行するには、[wxPython](https://wxpython.org/pages/downloads/) をインストールした後、次のコマンドを実行します。

```bash
git clone https://github.com/Microsoft/Cognitive-Face-Python.git
cd Cognitive-Face-Python
python sample
```

## <a name="summary"></a> まとめ

このチュートリアルでは、Python SDK の呼び出しから Face API を使用する基本プロセスについて説明しました。 API についてさらに詳しくは、ハウツー記事と [API リファレンス](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)をご覧ください。

## <a name="related"></a> 関連トピック

- [C# での Face API の使用開始](FaceAPIinCSharpTutorial.md)
- [Java for Android での Face API の使用開始](FaceAPIinJavaForAndroidTutorial.md)
