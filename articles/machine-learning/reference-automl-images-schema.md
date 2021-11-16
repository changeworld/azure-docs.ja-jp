---
title: コンピューター ビジョン タスク用の JSONL 形式
titleSuffix: Azure Machine Learning
description: コンピューター ビジョン タスクの自動 ML 実験で、JSONL ファイルをデータ利用のために書式設定する方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: automl
ms.topic: reference
ms.reviewer: nibaccam
ms.author: rvadthyavath
author: vadthyavath
ms.date: 10/13/2021
ms.custom: ''
ms.openlocfilehash: 725e8ae80d29a352bd2146906a782d4c8c2e6e38
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131442564"
---
# <a name="data-schemas-to-train-computer-vision-models-with-automated-machine-learning"></a>自動機械学習を使用してコンピューター ビジョン モデルをトレーニングするためのデータ スキーマ

コンピューター ビジョン タスクの自動 ML 実験で、トレーニングおよび推論時のデータ利用のために JSONL ファイルを書式設定する方法について説明します。


## <a name="data-schema-for-training"></a>トレーニング用のデータ スキーマ 

Azure Machine Learning AutoML for Images では、入力画像データを [JSONL](https://jsonlines.org/) (JSON Lines) 形式で準備する必要があります。 このセクションでは、画像分類複数クラス、画像分類複数ラベル、物体検出、インスタンスのセグメント化のための入力データ形式またはスキーマについて説明します。 また、最終的なトレーニングまたは検証の JSON Lines ファイルのサンプルも示します。

### <a name="image-classification-binarymulti-class"></a>画像分類 (バイナリまたは複数クラス)

**各 JSON 行の入力データ形式またはスキーマ:**
```json
{
   "image_url":"AmlDatastore://data_directory/../Image_name.image_format",
   "image_details":{
      "format":"image_format",
      "width":"image_width",
      "height":"image_height"
   },
   "label":"class_name",
}
```

| Key       | 説明  | 例 |
| -------- |----------|-----|
| `image_url` | AML データソース内での画像の場所<br>`Required, String` | `"AmlDatastore://data_directory/Image_01.jpg"` |
| `image_details` | 画像詳細<br>`Optional, Dictionary` | `"image_details":{"format": "jpg", "width": "400px", "height": "258px"}` |
| `format`  | 画像の種類 ([Pillow](https://pillow.readthedocs.io/en/stable/releasenotes/8.0.1.html) ライブラリで使用可能なすべての画像形式がサポートされています)<br>`Optional, String from {"jpg", "jpeg", "png", "jpe", "jfif","bmp", "tif", "tiff"}`  |  `"jpg" or "jpeg" or "png" or "jpe" or "jfif" or "bmp" or "tif" or "tiff"` |
| `width` | 画像の幅<br>`Optional, String or Positive Integer`  | `"400px" or 400`|
| `height` | 画像の高さ<br>`Optional, String or Positive Integer` | `"200px" or 200` |
| `label` | 画像のクラスまたはラベル<br>`Required, String` | `"cat"` |


複数クラス画像分類用の JSONL ファイルの例:
```json
{"image_url": "AmlDatastore://image_data/Image_01.jpg", "image_details":{"format": "jpg", "width": "400px", "height": "258px"}, "label": "can"}
{"image_url": "AmlDatastore://image_data/Image_02.jpg", "image_details": {"format": "jpg", "width": "397px", "height": "296px"}, "label": "milk_bottle"}
.
.
.
{"image_url": "AmlDatastore://image_data/Image_n.jpg", "image_details": {"format": "jpg", "width": "1024px", "height": "768px"}, "label": "water_bottle"}
  ```

![複数クラス画像分類の画像の例。](media/reference-automl-images-schema/multiclass-predictions.jpg)

### <a name="image-classification-multi-label"></a>画像分類複数ラベル

画像分類の各 JSON 行の入力データ形式またはスキーマの例を次に示します。


```json
{
   "image_url":"AmlDatastore://data_directory/../Image_name.image_format",
   "image_details":{
      "format":"image_format",
      "width":"image_width",
      "height":"image_height"
   },
   "label":[
      "class_name_1",
      "class_name_2",
      "class_name_3",
      "...",
      "class_name_n"
        
   ]
}
```

| Key       | 説明  | 例 |
| -------- |----------|-----|
| `image_url` | AML データソース内での画像の場所<br>`Required, String` | `"AmlDatastore://data_directory/Image_01.jpg"` |
| `image_details` | 画像詳細<br>`Optional, Dictionary` | `"image_details":{"format": "jpg", "width": "400px", "height": "258px"}` |
| `format`  | 画像の種類 ([Pillow](https://pillow.readthedocs.io/en/stable/releasenotes/8.0.1.html) ライブラリで使用可能なすべての画像形式がサポートされています)<br>`Optional, String from {"jpg", "jpeg", "png", "jpe", "jfif", "bmp", "tif", "tiff"}`  |  `"jpg" or "jpeg" or "png" or "jpe" or "jfif" or "bmp" or "tif" or "tiff"` |
| `width` | 画像の幅<br>`Optional, String or Positive Integer`  | `"400px" or 400`|
| `height` | 画像の高さ<br>`Optional, String or Positive Integer` | `"200px" or 200` |
| `label` | 画像のクラスまたはラベルの一覧<br>`Required, List of Strings` | `["cat","dog"]` |


画像分類複数ラベル用の JSONL ファイルの例:

```json
{"image_url": "AmlDatastore://image_data/Image_01.jpg", "image_details":{"format": "jpg", "width": "400px", "height": "258px"}, "label": ["can"]}
{"image_url": "AmlDatastore://image_data/Image_02.jpg", "image_details": {"format": "jpg", "width": "397px", "height": "296px"}, "label": ["can","milk_bottle"]}
.
.
.
{"image_url": "AmlDatastore://image_data/Image_n.jpg", "image_details": {"format": "jpg", "width": "1024px", "height": "768px"}, "label": ["carton","milk_bottle","water_bottle"]}
  ```

![画像分類複数ラベルの画像の例。](media/reference-automl-images-schema/multilabel-predictions.jpg)

### <a name="object-detection"></a>オブジェクトの検出

物体検出用の JSONL ファイルの例を次に示します。

```json
{
   "image_url":"AmlDatastore://data_directory/../Image_name.image_format",
   "image_details":{
      "format":"image_format",
      "width":"image_width",
      "height":"image_height"
   },
   "label":[
      {
         "label":"class_name_1",
         "topX":"xmin/width",
         "topY":"ymin/height",
         "bottomX":"xmax/width",
         "bottomY":"ymax/height",
         "isCrowd":"isCrowd"
      },
      {
         "label":"class_name_2",
         "topX":"xmin/width",
         "topY":"ymin/height",
         "bottomX":"xmax/width",
         "bottomY":"ymax/height",
         "isCrowd":"isCrowd"
      },
      "..."
   ]
}
```

ここでは、 
- `xmin` = 境界ボックスの左上隅の x 座標
- `ymin` = 境界ボックスの左上隅の y 座標
- `xmax` = 境界ボックスの右下隅の x 座標
- `ymax` = 境界ボックスの右下隅の y 座標



| Key       | 説明  | 例 |
| -------- |----------|-----|
| `image_url` | AML データソース内での画像の場所<br>`Required, String` | `"AmlDatastore://data_directory/Image_01.jpg"` |
| `image_details` | 画像詳細<br>`Optional, Dictionary` | `"image_details":{"format": "jpg", "width": "400px", "height": "258px"}` |
| `format`  | 画像の種類 ([Pillow](https://pillow.readthedocs.io/en/stable/releasenotes/8.0.1.html) ライブラリで使用可能なすべての画像形式がサポートされています。 ただし、YOLO の場合は、[opencv](https://pypi.org/project/opencv-python/4.3.0.36/) で許可されている画像形式だけがサポートされます)<br>`Optional, String from {"jpg", "jpeg", "png", "jpe", "jfif", "bmp", "tif", "tiff"}`  |  `"jpg" or "jpeg" or "png" or "jpe" or "jfif" or "bmp" or "tif" or "tiff"` |
| `width` | 画像の幅<br>`Optional, String or Positive Integer`  | `"499px" or 499`|
| `height` | 画像の高さ<br>`Optional, String or Positive Integer` | `"665px" or 665` |
| `label` (外部キー) | 境界ボックスの一覧。各ボックスは左上と右下の座標 `label, topX, topY, bottomX, bottomY, isCrowd` の辞書です<br>`Required, List of dictionaries` | `[{"label": "cat", "topX": 0.260, "topY": 0.406, "bottomX": 0.735, "bottomY": 0.701, "isCrowd": 0}]` |
| `label` (内部キー)| 境界ボックス内の物体のクラスまたはラベル<br>`Required, String` | `"cat"` |
| `topX` | 境界ボックスの左上隅の x 座標と画像の幅の比率<br>`Required, Float in the range [0,1]` | `0.260` |
| `topY` | 境界ボックスの左上隅の y 座標と画像の高さの比率<br>`Required, Float in the range [0,1]` | `0.406` |
| `bottomX` | 境界ボックスの右下隅の x 座標と画像の幅の比率<br>`Required, Float in the range [0,1]` | `0.735` |
| `bottomY` | 境界ボックスの右下隅の y 座標と画像の高さの比率<br>`Required, Float in the range [0,1]` | `0.701` |
| `isCrowd` | 境界ボックスが物体群の周りにあるかどうかを示します。 この特別なフラグが設定されている場合は、メトリックを計算するときにこの特定の境界ボックスをスキップします。<br>`Optional, Bool` | `0` |


物体検出用の JSONL ファイルの例:

```json
{"image_url": "AmlDatastore://image_data/Image_01.jpg", "image_details": {"format": "jpg", "width": "499px", "height": "666px"}, "label": [{"label": "can", "topX": 0.260, "topY": 0.406, "bottomX": 0.735, "bottomY": 0.701, "isCrowd": 0}]}
{"image_url": "AmlDatastore://image_data/Image_02.jpg", "image_details": {"format": "jpg", "width": "499px", "height": "666px"}, "label": [{"label": "carton", "topX": 0.172, "topY": 0.153, "bottomX": 0.432, "bottomY": 0.659, "isCrowd": 0}, {"label": "milk_bottle", "topX": 0.300, "topY": 0.566, "bottomX": 0.891, "bottomY": 0.735, "isCrowd": 0}]}
.
.
.
{"image_url": "AmlDatastore://image_data/Image_n.jpg", "image_details": {"format": "jpg", "width": "499px", "height": "666px"}, "label": [{"label": "carton", "topX": 0.0180, "topY": 0.297, "bottomX": 0.380, "bottomY": 0.836, "isCrowd": 0}, {"label": "milk_bottle", "topX": 0.454, "topY": 0.348, "bottomX": 0.613, "bottomY": 0.683, "isCrowd": 0}, {"label": "water_bottle", "topX": 0.667, "topY": 0.279, "bottomX": 0.841, "bottomY": 0.615, "isCrowd": 0}]}
```

![物体検出の画像の例。](media/reference-automl-images-schema/object-detect-predictions.jpg)

### <a name="instance-segmentation"></a>インスタンスのセグメント化

インスタンスのセグメント化については、自動 ML で入力および出力としてサポートされるのは多角形のみであり、マスクはサポートされません。

インスタンスのセグメント化用の JSONL ファイルの例を次に示します。

```json
{
   "image_url":"AmlDatastore://data_directory/../Image_name.image_format",
   "image_details":{
      "format":"image_format",
      "width":"image_width",
      "height":"image_height"
   },
   "label":[
      {
         "label":"class_name",
         "isCrowd":"isCrowd",
         "polygon":[["x1", "y1", "x2", "y2", "x3", "y3", "...", "xn", "yn"]]
      }
   ]
}
```

| Key       | 説明  | 例 |
| -------- |----------|-----|
| `image_url` | AML データソース内での画像の場所<br>`Required, String` | `"AmlDatastore://data_directory/Image_01.jpg"` |
| `image_details` | 画像詳細<br>`Optional, Dictionary` | `"image_details":{"format": "jpg", "width": "400px", "height": "258px"}` |
| `format`  | イメージの種類<br>`Optional, String from {"jpg", "jpeg", "png", "jpe", "jfif", "bmp", "tif", "tiff" }`  |  `"jpg" or "jpeg" or "png" or "jpe" or "jfif" or "bmp" or "tif" or "tiff"` |
| `width` | 画像の幅<br>`Optional, String or Positive Integer`  | `"499px" or 499`|
| `height` | 画像の高さ<br>`Optional, String or Positive Integer` | `"665px" or 665` |
| `label` (外部キー) | マスクの一覧。各マスクは `label, isCrowd, polygon coordinates` のディクショナリです <br>`Required, List of dictionaries` | ` [{"label": "can", "isCrowd": 0, "polygon": [[0.577, 0.689,`<br> ` 0.562, 0.681,`<br> `0.559, 0.686]]}]` |
| `label` (内部キー)| マスク内の物体のクラスとラベル<br>`Required, String` | `"cat"` |
| `isCrowd` | マスクが物体群の周りにあるかどうかを示します<br>`Optional, Bool` | `0` |
| `polygon` | 物体を表す多角形の座標<br>`Required,  List of list for multiple segments of the same instance. Float values in the range [0,1]` | ` [[0.577, 0.689, 0.567, 0.689, 0.559, 0.686]]` |


インスタンス セグメント化用の JSONL ファイルの例:

```python
{"image_url": "AmlDatastore://image_data/Image_01.jpg", "image_details": {"format": "jpg", "width": "499px", "height": "666px"}, "label": [{"label": "can", "isCrowd": 0, "polygon": [[0.577, 0.689, 0.567, 0.689, 0.559, 0.686, 0.380, 0.593, 0.304, 0.555, 0.294, 0.545, 0.290, 0.534, 0.274, 0.512, 0.2705, 0.496, 0.270, 0.478, 0.284, 0.453, 0.308, 0.432, 0.326, 0.423, 0.356, 0.415, 0.418, 0.417, 0.635, 0.493, 0.683, 0.507, 0.701, 0.518, 0.709, 0.528, 0.713, 0.545, 0.719, 0.554, 0.719, 0.579, 0.713, 0.597, 0.697, 0.621, 0.695, 0.629, 0.631, 0.678, 0.619, 0.683, 0.595, 0.683, 0.577, 0.689]]}]}
{"image_url": "AmlDatastore://image_data/Image_02.jpg", "image_details": {"format": "jpg", "width": "499px", "height": "666px"}, "label": [{"label": "carton", "isCrowd": 0, "polygon": [[0.240, 0.65, 0.234, 0.654, 0.230, 0.647, 0.210, 0.512, 0.202, 0.403, 0.182, 0.267, 0.184, 0.243, 0.180, 0.166, 0.186, 0.159, 0.198, 0.156, 0.396, 0.162, 0.408, 0.169, 0.406, 0.217, 0.414, 0.249, 0.422, 0.262, 0.422, 0.569, 0.342, 0.569, 0.334, 0.572, 0.320, 0.585, 0.308, 0.624, 0.306, 0.648, 0.240, 0.657]]}, {"label": "milk_bottle",  "isCrowd": 0, "polygon": [[0.675, 0.732, 0.635, 0.731, 0.621, 0.725, 0.573, 0.717, 0.516, 0.717, 0.505, 0.720, 0.462, 0.722, 0.438, 0.719, 0.396, 0.719, 0.358, 0.714, 0.334, 0.714, 0.322, 0.711, 0.312, 0.701, 0.306, 0.687, 0.304, 0.663, 0.308, 0.630, 0.320, 0.596, 0.32, 0.588, 0.326, 0.579]]}]}
.
.
.
{"image_url": "AmlDatastore://image_data/Image_n.jpg", "image_details": {"format": "jpg", "width": "499px", "height": "666px"}, "label": [{"label": "water_bottle", "isCrowd": 0, "polygon": [[0.334, 0.626, 0.304, 0.621, 0.254, 0.603, 0.164, 0.605, 0.158, 0.602, 0.146, 0.602, 0.142, 0.608, 0.094, 0.612, 0.084, 0.599, 0.080, 0.585, 0.080, 0.539, 0.082, 0.536, 0.092, 0.533, 0.126, 0.530, 0.132, 0.533, 0.144, 0.533, 0.162, 0.525, 0.172, 0.525, 0.186, 0.521, 0.196, 0.521 ]]}, {"label": "milk_bottle", "isCrowd": 0, "polygon": [[0.392, 0.773, 0.380, 0.732, 0.379, 0.767, 0.367, 0.755, 0.362, 0.735, 0.362, 0.714, 0.352, 0.644, 0.352, 0.611, 0.362, 0.597, 0.40, 0.593, 0.444,  0.494, 0.588, 0.515, 0.585, 0.621, 0.588, 0.671, 0.582, 0.713, 0.572, 0.753 ]]}]}
```

![インスタンス セグメント化の画像の例。](media/reference-automl-images-schema/instance-segmentation-predictions.jpg)

## <a name="data-format-for-inference"></a>推論用のデータ形式

このセクションでは、デプロイされたモデルの使用時に予測を行うために必要とされる入力データ形式について説明します。 前述の画像形式はいずれもコンテンツ タイプ `application/octet-stream` で使用できます。

### <a name="input-format"></a>入力形式

タスク固有のモデル エンドポイントを使用して任意のタスクで予測を生成するために必要な入力形式を次に示します。 [モデルのデプロイ](how-to-auto-train-image-models.md#register-and-deploy-model)後、次のコード スニペットを使用して、すべてのタスクの予測を取得できます。

```python
# input image for inference
sample_image = './test_image.jpg'
# load image data
data = open(sample_image, 'rb').read()
# set the content type
headers = {'Content-Type': 'application/octet-stream'}
# if authentication is enabled, set the authorization header
headers['Authorization'] = f'Bearer {key}'
# make the request and display the response
response = requests.post(scoring_uri, data, headers=headers)
```
### <a name="output-format"></a>出力形式

モデル エンドポイントで行われた予測は、タスクの種類に応じて異なる構造に従います。 このセクションでは、複数クラス、複数ラベルの画像分類、物体検出、インスタンスのセグメント化タスクの出力データ形式について説明します。  

#### <a name="image-classification"></a>画像の分類

画像分類のエンドポイントからは、データセット内のすべてのラベルと、入力画像の確率スコアが次の形式で返されます。

```json
{
   "filename":"/tmp/tmppjr4et28",
   "probs":[
      2.098e-06,
      4.783e-08,
      0.999,
      8.637e-06
   ],
   "labels":[
      "can",
      "carton",
      "milk_bottle",
      "water_bottle"
   ]
}
```

#### <a name="image-classification-multi-label"></a>画像分類複数ラベル

画像分類複数ラベルの場合、モデル エンドポイントからはラベルとその確率が返されます。

```json
{
   "filename":"/tmp/tmpsdzxlmlm",
   "probs":[
      0.997,
      0.960,
      0.982,
      0.025
   ],
   "labels":[
      "can",
      "carton",
      "milk_bottle",
      "water_bottle"
   ]
}
```

#### <a name="object-detection"></a>オブジェクトの検出

物体検出モデルを使用すると、ボックス ラベルおよび信頼度スコアと共に、スケーリングされた左上および右下の座標を持つ複数のボックスが返されます。

```json
{
   "filename":"/tmp/tmpdkg2wkdy",
   "boxes":[
      {
         "box":{
            "topX":0.224,
            "topY":0.285,
            "bottomX":0.399,
            "bottomY":0.620
         },
         "label":"milk_bottle",
         "score":0.937
      },
      {
         "box":{
            "topX":0.664,
            "topY":0.484,
            "bottomX":0.959,
            "bottomY":0.812
         },
         "label":"can",
         "score":0.891
      },
      {
         "box":{
            "topX":0.423,
            "topY":0.253,
            "bottomX":0.632,
            "bottomY":0.725
         },
         "label":"water_bottle",
         "score":0.876
      }
   ]
}
```
#### <a name="instance-segmentation"></a>インスタンスのセグメント化

インスタンスのセグメント化では、出力は、スケーリングされた左上と右下の座標、ラベル、信頼度スコア、多角形 (マスクではない) を持つ複数のボックスで構成されます。 ここでは、多角形の値は、スキーマ セクションで説明したのと同じ形式です。

```json
{
   "filename":"/tmp/tmpi8604s0h",
   "boxes":[
      {
         "box":{
            "topX":0.679,
            "topY":0.491,
            "bottomX":0.926,
            "bottomY":0.810
         },
         "label":"can",
         "score":0.992,
         "polygon":[
            [
               0.82, 0.811, 0.771, 0.810, 0.758, 0.805, 0.741, 0.797, 0.735, 0.791, 0.718, 0.785, 0.715, 0.778, 0.706, 0.775, 0.696, 0.758, 0.695, 0.717, 0.698, 0.567, 0.705, 0.552, 0.706, 0.540, 0.725, 0.520, 0.735, 0.505, 0.745, 0.502, 0.755, 0.493
            ]
         ]
      },
      {
         "box":{
            "topX":0.220,
            "topY":0.298,
            "bottomX":0.397,
            "bottomY":0.601
         },
         "label":"milk_bottle",
         "score":0.989,
         "polygon":[
            [
               0.365, 0.602, 0.273, 0.602, 0.26, 0.595, 0.263, 0.588, 0.251, 0.546, 0.248, 0.501, 0.25, 0.485, 0.246, 0.478, 0.245, 0.463, 0.233, 0.442, 0.231, 0.43, 0.226, 0.423, 0.226, 0.408, 0.234, 0.385, 0.241, 0.371, 0.238, 0.345, 0.234, 0.335, 0.233, 0.325, 0.24, 0.305, 0.586, 0.38, 0.592, 0.375, 0.598, 0.365
            ]
         ]
      },
      {
         "box":{
            "topX":0.433,
            "topY":0.280,
            "bottomX":0.621,
            "bottomY":0.679
         },
         "label":"water_bottle",
         "score":0.988,
         "polygon":[
            [
               0.576, 0.680, 0.501, 0.680, 0.475, 0.675, 0.460, 0.625, 0.445, 0.630, 0.443, 0.572, 0.440, 0.560, 0.435, 0.515, 0.431, 0.501, 0.431, 0.433, 0.433, 0.426, 0.445, 0.417, 0.456, 0.407, 0.465, 0.381, 0.468, 0.327, 0.471, 0.318
            ]
         ]
      }
   ]
}
```

> [!NOTE]
> この記事で使用されている画像は Fridge Objects データセットのもので、著作権は © Microsoft Corporation に帰属します。[MIT ライセンス](https://github.com/microsoft/computervision-recipes/blob/master/LICENSE)の下で [computervision-recipes/01_training_introduction.ipynb](https://github.com/microsoft/computervision-recipes/blob/master/scenarios/detection/01_training_introduction.ipynb) から入手できます。 

## <a name="next-steps"></a>次のステップ

[自動 ML でコンピューター ビジョン モデルのトレーニング用にデータを準備する](how-to-prepare-datasets-for-automl-images.md)方法の詳細をご確認ください。
