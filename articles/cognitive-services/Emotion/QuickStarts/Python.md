---
title: Emotion API Python クイック スタート | Microsoft Docs
description: Cognitive Services の Emotion API と Python の使用をすぐに開始するために役立つ情報とコード サンプルを提供します。
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: emotion-api
ms.topic: article
ms.date: 02/05/2018
ms.author: anroth
ms.openlocfilehash: ff1f6b2ddc872d0ee63d9885b04b1f007bc86e33
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2018
ms.locfileid: "35373069"
---
# <a name="emotion-api-python-quickstart"></a>Emotion API Python クイック スタート

> [!IMPORTANT]
> Video API のプレビューは 2017 年 10 月 30 日に終了しました。 新しい [Video Indexer API のプレビュー](https://azure.microsoft.com/services/cognitive-services/video-indexer/)をお試しください。ビデオから分析情報を手軽に抽出でき、ビデオ内で話される言葉や表情、性格、感情を検知することで、検索結果などのコンテンツの検索エクスペリエンスを強化できます。 [詳細情報](https://docs.microsoft.com/azure/cognitive-services/video-indexer/video-indexer-overview)。

このチュートリアルでは、[Emotion API Recognize メソッド](https://westus.dev.cognitive.microsoft.com/docs/services/5639d931ca73072154c1ce89/operations/563b31ea778daf121cc3a5fa) と Python を使って、画像内の 1 人以上の個人が表す感情を認識するための、役立つ情報とコード サンプルを提供します。 

バインダーの起動バッジ [![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=EmotionAPI.ipynb) をクリックして、次の例を [MyBinder](https://mybinder.org) 上で Jupyter Notebook として実行できます。


## <a name="prerequisite"></a>前提条件
無料のサブスクリプション キーを[ここから](https://azure.microsoft.com/try/cognitive-services/)入手します。

## <a name="running-the-walkthrough"></a>チュートリアルの実行
このチュートリアルを続行するには、前に入手した API キーで `subscription_key` を置き換えます。


```python
subscription_key = None
assert subscription_key
```

次に、サービス URL が、API キーの設定時に使用したリージョンに対応することを確認します。 試用版のキーを使用している場合、変更する必要はありません。


```python
emotion_recognition_url = "https://westus.api.cognitive.microsoft.com/emotion/v1.0/recognize"
```

このチュートリアルは、ディスクに保存されているイメージを使用します。 また、アクセスが公開されている URL にある画像を使用することもできます。 詳細については、[REST API のドキュメント](https://westus.dev.cognitive.microsoft.com/docs/services/5639d931ca73072154c1ce89/operations/563b31ea778daf121cc3a5fa)をご覧ください。

画像データが要求本文に含めて渡されるため、`Content-Type` ヘッダーを `application/octet-stream` に設定する必要があることに注意してください。 画像を URL として渡している場合は、次のようにヘッダーを設定してください。
```python
header = {'Ocp-Apim-Subscription-Key': subscription_key }
```
URL を含むディクショナリを作成します。
```python
data = {'url': image_url}
```
次を使用して、それを `requests` ライブラリに渡します。
```python
requests.post(emotion_recognition_url, headers=headers, json=image_data)
```

まず、[Emotion API](https://azure.microsoft.com/services/cognitive-services/emotion/) サイトからいくつかのサンプル画像をダウンロードします。


```bash
%%bash
mkdir -p images
curl -Ls https://aka.ms/csnb-emotion-1 -o images/emotion_1.jpg
curl -Ls https://aka.ms/csnb-emotion-2 -o images/emotion_2.jpg
```


```python
image_path = "images/emotion_1.jpg"
image_data = open(image_path, "rb").read()
```


```python
import requests
headers  = {'Ocp-Apim-Subscription-Key': subscription_key, "Content-Type": "application/octet-stream" }
response = requests.post(emotion_recognition_url, headers=headers, data=image_data)
response.raise_for_status()
analysis = response.json()
analysis
```




    [{'faceRectangle': {'height': 162, 'left': 130, 'top': 141, 'width': 162},
      'scores': {'anger': 9.29041e-06,
       'contempt': 0.000118981574,
       'disgust': 3.15619363e-05,
       'fear': 0.000589638,
       'happiness': 0.06630674,
       'neutral': 0.00555004273,
       'sadness': 7.44669524e-06,
       'surprise': 0.9273863}}]



返される JSON オブジェクトでは、認識された顔に対して、境界ボックスと検出された感情が表示されます。 それぞれの感情には 0 から 1 までのスコアが付いており、高いスコアは低いスコアよりも感情がはっきりしています。 

次に示すコード行では、`matplotlib` ライブラリを使用して画像の顔の感情が検出されます。 見やすくするため、上位 3 つの感情のみが表示されます。


```python
%matplotlib inline
import matplotlib.pyplot as plt
from matplotlib.patches import Rectangle
from io import BytesIO
from PIL import Image

plt.figure(figsize=(5,5))

image  = Image.open(BytesIO(image_data))
ax     = plt.imshow(image, alpha=0.6)

for face in analysis:
    fr = face["faceRectangle"]
    em = face["scores"]
    origin = (fr["left"], fr["top"])
    p = Rectangle(origin, fr["width"], fr["height"], fill=False, linewidth=2, color='b')
    ax.axes.add_patch(p)
    ct = "\n".join(["{0:<10s}{1:>.4f}".format(k,v) for k, v in sorted(list(em.items()),key=lambda r: r[1], reverse=True)][:3])
    plt.text(origin[0], origin[1], ct, fontsize=20)    
_ = plt.axis("off")
```

次に示す `annotate_image` 関数を使用すると、ファイル システムのパスが指定された画像ファイルの上に感情をオーバーレイできます。 これは、前に示した Emotion API を呼び出すコードに基づいています。


```python
def annotate_image(image_path):    
    image_data = open(image_path, "rb").read()
    headers  = {'Ocp-Apim-Subscription-Key': subscription_key, "Content-Type": "application/octet-stream" }
    response = requests.post(emotion_recognition_url, headers=headers, data=image_data)
    response.raise_for_status()
    analysis = response.json()

    plt.figure()

    image  = Image.open(image_path)
    ax     = plt.imshow(image, alpha=0.6)

    for face in analysis:
        fr = face["faceRectangle"]
        em = face["scores"]
        origin = (fr["left"], fr["top"])
        p = Rectangle(origin, fr["width"], fr["height"], fill=False, linewidth=2, color='b')
        ax.axes.add_patch(p)
        ct = "\n".join(["{0:<10s}{1:>.4f}".format(k,v) for k, v in sorted(list(em.items()),key=lambda r: r[1], reverse=True)][:3])
        plt.text(origin[0], origin[1], ct, fontsize=20, va="bottom")    
    _ = plt.axis("off")
```

最後に、次の行に示すように、画像ファイルに対して `annotate_image` 関数を呼び出すことができます。


```python
annotate_image("images/emotion_2.jpg")
```
