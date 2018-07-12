---
title: Python での TensorFlow モデルの実行 - Custom Vision Service - Azure Cognitive Services | Microsoft Docs
description: Python での TensorFlow モデルの実行
services: cognitive-services
author: areddish
manager: chbuehle
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: article
ms.date: 05/17/2018
ms.author: areddish
ms.openlocfilehash: d31036404604104ca28328b6c8bc5d3ca74d83ea
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2018
ms.locfileid: "35378144"
---
# <a name="run-tensorflow-model-in-python"></a>Python での TensorFlow モデルの実行

Custom Vision Service から [TensorFlow モデルをエクスポート](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/export-your-model)したら、このクイックスタートが、このモデルをローカルで使用して画像を分類する方法を示します。

## <a name="install-required-components"></a>必須コンポーネントのインストール

### <a name="prerequisites"></a>前提条件

このチュートリアルを使用するには、次の作業が必要となります。

- Python 2.7 以降または Python 3.5 以降をインストールします。
- pip をインストールします。

次のパッケージもインストールする必要があります。

```
pip install tensorflow
pip install pillow
pip install numpy
pip install opencv-python
```

## <a name="load-your-model-and-tags"></a>モデルとタグを読み込む

ダウンロードした zip ファイルには、model.pb と、labels.txt が含まれています。 これらのファイルは、トレーニング済みモデルと分類ラベルを表します。 最初の手順では、プロジェクトに、モデルを読み込みます。

```Python
import tensorflow as tf
import os

graph_def = tf.GraphDef()
labels = []

# Import the TF graph
with tf.gfile.FastGFile(filename, 'rb') as f:
    graph_def.ParseFromString(f.read())
    tf.import_graph_def(graph_def, name='')

# Create a list of labels.
with open(labels_filename, 'rt') as lf:
    for l in lf:
        labels.append(l.strip())
```

## <a name="prepare-an-image-for-prediction"></a>予測の画像を準備する

予測に適した形になるように画像を準備するためのいくつかの手順があります。 これらの手順は、トレーニング中に実行される画像操作に似ています。

### <a name="open-the-file-and-create-an-image-in-the-bgr-color-space"></a>ファイルを開き、BGR 色空間に画像を作成する

```Python
from PIL import Image
import numpy as np
import cv2

# Load from a file
imageFile = "<path to your image file>"
image = Image.open(imageFile)

# Update orientation based on EXIF tags, if the file has orientation info.
image = update_orientation(image)

# Convert to OpenCV format
image = convert_to_opencv(image)
```

### <a name="deal-with-images-with-a-dimension-1600"></a>ディメンションが 1600 より大きい画像を扱う

```Python
# If the image has either w or h greater than 1600 we resize it down respecting
# aspect ratio such that the largest dimension is 1600
image = resize_down_to_1600_max_dim(image)
```

### <a name="crop-the-largest-center-square"></a>中央の最も大きな四角形をトリミングする

```Python
# We next get the largest center square
h, w = image.shape[:2]
min_dim = min(w,h)
max_square_image = crop_center(image, min_dim, min_dim)
```

### <a name="resize-down-to-256x256"></a>256 x 256 までのサイズを縮小する

```Python
# Resize that square down to 256x256
augmented_image = resize_to_256_square(max_square_image)
```


### <a name="crop-the-center-for-the-specific-input-size-for-the-model"></a>モデルの特定の入力サイズの中央をトリミングする

```Python
# The compact models have a network size of 227x227, the model requires this size.
network_input_size = 227

# Crop the center for the specified network_input_Size
augmented_image = crop_center(augmented_image, network_input_size, network_input_size)

```

上記の手順では、次のヘルパー関数を使用します。

```Python
def convert_to_opencv(image):
    # RGB -> BGR conversion is performed as well.
    r,g,b = np.array(image).T
    opencv_image = np.array([b,g,r]).transpose()
    return opencv_image

def crop_center(img,cropx,cropy):
    h, w = img.shape[:2]
    startx = w//2-(cropx//2)
    starty = h//2-(cropy//2)
    return img[starty:starty+cropy, startx:startx+cropx]

def resize_down_to_1600_max_dim(image):
    h, w = image.shape[:2]
    if (h < 1600 and w < 1600):
        return image

    new_size = (1600 * w // h, 1600) if (h > w) else (1600, 1600 * h // w)
    return cv2.resize(image, new_size, interpolation = cv2.INTER_LINEAR)

def resize_to_256_square(image):
    h, w = image.shape[:2]
    return cv2.resize(image, (256, 256), interpolation = cv2.INTER_LINEAR)

def update_orientation(image):
    exif_orientation_tag = 0x0112
    if hasattr(image, '_getexif'):
        exif = image._getexif()
        if (exif != None and exif_orientation_tag in exif):
            orientation = exif.get(exif_orientation_tag, 1)
            # orientation is 1 based, shift to zero based and flip/transpose based on 0-based values
            orientation -= 1
            if orientation >= 4:
                image = image.transpose(Image.TRANSPOSE)
            if orientation == 2 or orientation == 3 or orientation == 6 or orientation == 7:
                image = image.transpose(Image.FLIP_TOP_BOTTOM)
            if orientation == 1 or orientation == 2 or orientation == 5 or orientation == 6:
                image = image.transpose(Image.FLIP_LEFT_RIGHT)
    return image
```

## <a name="predict-an-image"></a>画像を予測する

画像が、テンソルとして準備されたら、予測用モデルを通じて送信できます。

```Python

# These names are part of the model and cannot be changed.
output_layer = 'loss:0'
input_node = 'Placeholder:0'

with tf.Session() as sess:
    prob_tensor = sess.graph.get_tensor_by_name(output_layer)
    predictions, = sess.run(prob_tensor, {input_node: [augmented_image] })
```

## <a name="view-the-results"></a>結果の確認

モデルを通じて画像テンソルを実行した結果をラベルにマップする必要があります。

```Python
    # Print the highest probability label
    highest_probability_index = np.argmax(predictions)
    print('Classified as: ' + labels[highest_probability_index])
    print()

    # Or you can print out all of the results mapping labels to probabilities.
    label_index = 0
    for p in predictions:
        truncated_probablity = np.float64(round(p,8))
        print (labels[label_index], truncated_probablity)
        label_index += 1
```
## <a name="next-steps"></a>次の手順

モバイル アプリケーションに、モデルをラップすることもできます。
* [エクスポートされた Tensorflow モデルを Android アプリケーションで使用する](https://github.com/Azure-Samples/cognitive-services-android-customvision-sample)
* [エクスポートされた CoreML モデルを Swift iOS アプリケーションで使用する](https://go.microsoft.com/fwlink/?linkid=857726)
* [エクスポートされた CoreML モデルを Xamarin を使って iOS アプリケーションで使用する](https://github.com/xamarin/ios-samples/tree/master/ios11/CoreMLAzureModel)

