---
title: AutoMLイメージ用のONNX を使用したローカル推論
titleSuffix: Azure Machine Learning
description: Azure Machine Learningでの自動MLでONNX を使用して、分類、物体検出、およびセグメント化用のコンピュータービジョンモデルで予測を行うことができます。
author: vadthyavath
ms.author: rvadthyavath
ms.service: machine-learning
ms.subservice: automl
ms.topic: how-to
ms.date: 10/18/2021
ms.openlocfilehash: a8eded57142bf4682a0b555136c30462e9cf08ad
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/09/2021
ms.locfileid: "132060512"
---
# <a name="make-predictions-with-onnx-on-computer-vision-models-from-automl"></a>AutoMLのコンピュータービジョンモデルでONNX を使用して予測を行います。 

このアーティクルでは、Open Neural Network Exchange (ONNX) を使用して、Azure Machine Learningでの自動ML (AutoML) から生成されたコンピュータービジョンモデルの予測を行う方法について説明します。 

予測にONNX を使用するには、次のことを行う必要があります。 
 
1. AutoMLトレーニングの実行からONNX モデルファイルをダウンロードします。
1. ONNXモデルの入力と出力を理解します。
1. 入力イメージに必要な形式になるようにデータを前処理します。
1. Python用のONNX Runtimeで推論を行います。
1. 物体検出とセグメント化タスクの予測を可視化します。

[ONNX](https://onnx.ai/about.html)は、機械学習とディープラーニングモデルのオープン標準です。 これにより、一般的なAI フレームワーク全体でモデルのインポートとエクスポート(相互運用性)を有効にします。 詳細については、[ONNX GitHubプロジェクト](https://github.com/onnx/onnx)を参照してください。

[ONNX Runtime](https://onnxruntime.ai/index.html) は、クロスプラットフォームの推論をサポートするオープンソースのプロジェクトです。 ONNX Runtimeでは、プログラミング言語 (Python、C++、C#、C、Java、JavaScript など) 全体で APIs を提供します。 これらのAPIs を使用して、入力イメージに対して推論を行うことができます。 ONNX 形式にエクスポートされたモデルを作成したら、プロジェクトに必要な任意のプログラミング言語でこれらのAPIsを使用できます。 

これらのガイドでは、[ONNX Runtime用のPython APIs](https://onnxruntime.ai/docs/get-started/with-python.html) を使用して、一般的なビジョンタスクのイメージの予測を行う方法について説明します。 これらのONNX のエクスポートされたモデルは、複数の言語で使用できます。

## <a name="prerequisites"></a>前提条件

* サポートされているイメージタスク (分類、オ物体検出、セグメント化) のいずれかについて、AutoMLトレーニング済みコンピュータービジョンのモデルを取得できます。 [コンピュータービジョンのタスクに対するAutoMLサポートの詳細については、こちらを参照して](how-to-auto-train-image-models.md)ください。

* [Onnxruntime](https://onnxruntime.ai/docs/get-started/with-python.html)パッケージをインストールします。 このアーティクルのメソッドは、バージョン1.3.0 の1.8.0以降に対してテストされています。

## <a name="download-onnx-model-files"></a>ONNXモデルファイルのダウンロード

Azure Machine Learning studio UIまたはAzure Machine Learning Python SDKを使用して、AutoMLの実行から ONNX モデルファイルをダウンロードできます。 実験名と親の実行ID を使用してSDK を使用してダウンロードすることをお勧めします。 

### <a name="azure-machine-learning-studio"></a>Azure Machine Learning Studio

Azure Machine Learning studioで、トレーニングノートブックで生成された実験へのハイパーリンクを使用するか、 **[アセット]** の **[実験]** タブで実験名を選択して、実験にアクセスします。 次に、最適な子の実行を選択します。 

最適な子を実行するには、[**出力＋ログ** > **train_artifacts**]にアクセスします。 **[ダウンロード]** ボタンを使用して、次のファイルを手動でダウンロードします。

- *labels. json*: トレーニングデータセット内のすべてのクラスまたはラベルを含むファイル。
- *model.onnx*: ONNX形式のモデル。 

![ONNXモデルファイルをダウンロードするための選択内容を示すスクリーンショット。](./media/how-to-inference-onnx-automl-image-models/onnx-files-manual-download.png)

ダウンロードしたモデルファイルをディレクトリに保存します。 このアーティクルの例では、 *./automl_models* ディレクトリを使用します。 

### <a name="azure-machine-learning-python-sdk"></a>Azure Machine Learning Python SDK

SDKでは、実験名と親の実行 ID を使用して、(プライマリメトリックごとに) 最適な子の実行を選択できます。 次に、*labels.json* と *model.onnx* ファイルをダウンロードできます。

次のコードは、関連するプライマリメトリックに基づいて、最適な子の実行を返します。

```python
# Select the best child run
from azureml.train.automl.run import AutoMLRun

run_id = "" # Specify the run ID
automl_image_run = AutoMLRun(experiment=experiment, run_id=run_id)
best_child_run = automl_image_run.get_best_child()
```

トレーニングデータセット内のすべてのクラスとラベルを含む、*labels.json* ファイルをダウンロードします。

```python
import json

labels_file = "automl_models/labels.json"
best_child_run.download_file(name="train_artifacts/labels.json", output_file_path=labels_file)

```

*model.onnx* ファイルをダウンロードします。

```python
onnx_model_path = "automl_models/model.onnx"
best_child_run.download_file(name="train_artifacts/model.onnx", output_file_path=onnx_model_path)
```

モデルのダウンロード手順の後、ONNX Runtime Pythonパッケージを使用して、 *model.onnx* ファイルを使用して推論を行います。 デモンストレーションを目的として、このアーティクルでは、各ビジョンタスクの[イメージデータセットを準備する方法](how-to-prepare-datasets-for-automl-images.md)のデータセットを使用します。 

ONNXモデルの推定を示すために、各データセットを使用してすべてのビジョンタスクのモデルをトレーニングしました。
 
## <a name="load-the-labels-and-onnx-model-files"></a>ラベルとONNXモデルファイルを読み込む。

次のコードスニペットでは、クラス名が順序付けられている名前の *labels.json* を読み込みます。 つまり、ONNX モデルでラベル ID が2と予測される場合、ラベルID は、*labels.json* ファイルの3番目のインデックスで指定されたラベル名に対応します。

```python
import onnxruntime

labels_file = "automl_models/labels.json"
with open(labels_file) as f:
    classes = json.load(f)
print(classes)
try:
    session = onnxruntime.InferenceSession(onnx_model_path)
    print("ONNX model loaded...")
except Exception as e: 
    print("Error loading ONNX file: ",str(e))
```

## <a name="get-expected-input-and-output-details-for-an-onnx-model"></a>ONNXモデルの予想される入力と出力の詳細を取得する。

モデルがある場合は、モデル固有の詳細とタスク固有の詳細を把握しておくことが重要です。 これらの詳細には、モデル固有またはタスク固有の出力がわかるように、入力の数と出力の数、想定される入力図形またはイメージのプリプロセスの形式、および出力図形が含まれます。

```python
sess_input = session.get_inputs()
sess_output = session.get_outputs()
print(f"No. of inputs : {len(sess_input)}, No. of outputs : {len(sess_output)}")

for idx, input_ in enumerate(range(len(sess_input))):
    input_name = sess_input[input_].name
    input_shape = sess_input[input_].shape
    input_type = sess_input[input_].type
    print(f"{idx} Input name : { input_name }, Input shape : {input_shape}, \
    Input type  : {input_type}")  

for idx, output in enumerate(range(len(sess_output))):
    output_name = sess_output[output].name
    output_shape = sess_output[output].shape
    output_type = sess_output[output].type
    print(f" {idx} Output name : {output_name}, Output shape : {output_shape}, \
    Output type  : {output_type}") 
``` 

### <a name="expected-input-and-output-formats-for-the-onnx-model"></a>ONNXモデルに必要な入力形式と出力形式

すべてのONNXモデルには、入力形式と出力形式の定義済みセットがあります。

# <a name="multi-class-image-classification"></a>[複数クラスの画像分類](#tab/multi-class)

この例では、 [fridgeObjects](https://cvbp-secondary.z19.web.core.windows.net/datasets/image_classification/fridgeObjects.zip) データセットでトレーニングされたモデルに134のイメージと4つのクラス/ラベルを適用して、ONNX モデルの推定について説明します。 画像分類タスクのトレーニングの詳細については、「 [複数クラスの画像分類のノートブック](https://github.com/Azure/azureml-examples/tree/81c7d33ed82f62f419472bc11f7e1bad448ff15b/python-sdk/tutorials/automl-with-azureml/image-classification-multiclass)」を参照してください。

### <a name="input-format"></a>入力形式
    
この入力は、前処理されたイメージです。

| 入力名  | 入力図形  | 入力型 | 説明 |
| -------- |----------:|-----|--------|
| 入力1 | `(batch_size, num_channels, height, width)` | ndarray(float) | 入力は前処理されたイメージで、 `(1, 3, 224, 224)` バッチサイズは1で、高さと幅は224です。 これらの数値は、 `crop_size`トレーニングの例に使用されている値に対応して います。 |
    

### <a name="output-format"></a>出力形式

出力は、すべてのクラス/ラベルのlogitsの配列です。
         
| 出力名   | 出力図形  | 出力の種類 | 説明 |
| -------- |----------|-----|------|
| 出力1 | `(batch_size, num_classes)` | ndarray(float) | モデルは、( `softmax`なし) logitsを返します。 たとえば、バッチサイズ1と4クラスの場合、`(1, 4)`を返します。 |

# <a name="multi-label-image-classification"></a>[複数ラベルの画像分類](#tab/multi-label)

この例では、 [マルチラベルfridgeObjectsデータセット](https://cvbp-secondary.z19.web.core.windows.net/datasets/image_classification/multilabelFridgeObjects.zip)でトレーニングされたモデルを128の画像と4つのクラス/ラベルで使用して、ONNX モデルの推定について説明します。 複数クラスの画像分類のモデルトレーニングの詳細については、「 [複数クラスの画像分類のノートブック](https://github.com/Azure/azureml-examples/tree/81c7d33ed82f62f419472bc11f7e1bad448ff15b/python-sdk/tutorials/automl-with-azureml/image-classification-multilabel)」を参照してください。

### <a name="input-format"></a>入力形式

入力は前処理されたイメージです。

| 入力名       | 入力図形  | 入力型 | 説明 |
| -------- |----------|-----|--------|
| 入力1 | `(batch_size, num_channels, height, width)` | ndarray(float) | 入力は前処理されたイメージで、 `(1, 3, 224, 224)` バッチサイズは1で、高さと幅は224です。 これらの数値は、 `crop_size`トレーニングの例に使用されている値に対応して います。 |
        
### <a name="output-format"></a>出力形式

出力は、すべてのクラス/ラベルのlogitsの配列です。
    
      
| 出力名       | 出力図形  | 出力の種類 | 説明 |
| -------- |----------|-----|------
| 出力1 | `(batch_size, num_classes)` | ndarray(float) | モデルは、( `sigmoid`なし) logitsを返します。 たとえば、バッチサイズ1と4クラスの場合、`(1, 4)`を返します。 |


# <a name="object-detection-with-faster-r-cnn"></a>[より高速なR-CNN を使用した物体検出](#tab/object-detect-cnn)

これらの物体検出の例では、128イメージの[fridgeObjects 検出データセット](https://cvbp-secondary.z19.web.core.windows.net/datasets/object_detection/odFridgeObjects.zip)でトレーニングされたモデルを使用して、ONNX モデルの推定について説明します。 この例では、より高速なR-CNN モデルをトレーニングして、推論の手順を示します。 物体検出モデルのトレーニングの詳細については、「 [物体検出のノートブック](https://github.com/Azure/azureml-examples/tree/81c7d33ed82f62f419472bc11f7e1bad448ff15b/python-sdk/tutorials/automl-with-azureml/image-object-detection)」を参照してください。

### <a name="input-format"></a>入力形式

入力は前処理されたイメージです。

| 入力名       | 入力図形  | 入力型 | 説明 |
| -------- |----------|-----|--------|
| 入力 | `(batch_size, num_channels, height, width)` | ndarray(float) | 入力は前処理されたイメージで、 `(1, 3, 600, 800)` バッチサイズは1、高さは600、高さは800です。|
        
    
### <a name="output-format"></a>出力形式

出力は、ボックス、ラベル、およびスコアの組です。
  
| 出力名       | 出力図形  | 出力の種類 | 説明 |
| -------- |----------|-----|------|
| 矩形 | `(n_boxes, 4)`、各ボックスには `x_min, y_min, x_max, y_max` | ndarray(float) | モデルは、左上と右下の座標を持つ *n 個* のボックスを返します。 |
| ラベル | `(n_boxes)`| ndarray(float) | 各ボックス内のオブジェクトのラベルまたはクラスID。 |  
| スコア | `(n_boxes)` | ndarray(float) | 各ボックス内のオブジェクトの信頼スコア。 |    


# <a name="object-detection-with-yolo"></a>[物体検出とYOLO](#tab/object-detect-yolo)

これらの物体検出の例では、128イメージの[fridgeObjects 検出データセット](https://cvbp-secondary.z19.web.core.windows.net/datasets/object_detection/odFridgeObjects.zip)でトレーニングされたモデルを使用して、ONNX モデルの推定について説明します。 この例では、推論手順を示すためにYOLOモデルをトレーニングします。 物体検出モデルのトレーニングの詳細については、「 [物体検出のノートブック](https://github.com/Azure/azureml-examples/tree/81c7d33ed82f62f419472bc11f7e1bad448ff15b/python-sdk/tutorials/automl-with-azureml/image-object-detection)」を参照してください。 

### <a name="input-format"></a>入力形式

この入力は、 `(1, 3, 640, 640)`バッチサイズが1で、高さと幅が640である前処理されたイメージです。 これらの数値は、トレーニングの例で使用した値に対応しています。        

| 入力名       | 入力図形  | 入力型 | 説明 |
| -------- |----------|-----|--------|
| 入力 | `(batch_size, num_channels, height, width)` | ndarray(float) | 入力は前処理されたイメージで、 `(1, 3, 600, 800)` バッチサイズは1、高さは600、高さは800です。|
        
### <a name="output-format"></a>出力形式
出力は、ボックス、ラベル、およびスコアの一覧です。 YOLO の場合、ボックス、ラベル、スコアを抽出するための最初の出力が必要です。
    
| 出力名       | 出力図形  | 出力の種類 | 説明 |
| -------- |----------|-----|------|
| 出力 | `(n_boxes, 6)`、各ボックスには`x_min, y_min, x_max, y_max, confidence_score, class_id` | ndarray(float) | モデルは、左上および右下の座標と、オブジェクトの信頼スコア、クラスIDs、またはラベルIDs と共に *n 個* のボックスを返します。 |

# <a name="instance-segmentation"></a>[インスタンスのセグメント化](#tab/instance-segmentation)

このインスタンスセグメント化の例では、 [fridgeObjectsデータセット](https://cvbp-secondary.z19.web.core.windows.net/datasets/object_detection/odFridgeObjectsMask.zip)でトレーニングされた Mask R-CNNモデルを128の画像と4つのクラス/ラベルで使用して、ONNX モデルの推定を説明します。 インスタンスセグメント化モデルのトレーニングの詳細については、「 [インスタンスセグメント化のノートブック](https://github.com/Azure/azureml-examples/tree/81c7d33ed82f62f419472bc11f7e1bad448ff15b/python-sdk/tutorials/automl-with-azureml/image-instance-segmentation)」を参照してください。

>[!IMPORTANT]
> インスタンス セグメント化タスクでは、Mask R-CNN のみがサポートされています。 入力形式と出力形式は、Mask R-CNN のみに基づいています。

### <a name="input-format"></a>入力形式

この入力は、前処理されたイメージです。 Mask R-CNNのONNX モデルをエクスポートして、異なる図形のイメージを操作します。 パフォーマンスを向上させるために、トレーニングイメージのサイズと一貫性のある固定サイズにサイズ変更することをお勧めします。
    
| 入力名       | 入力図形  | 入力型 | 説明 |
| -------- |----------|-----|--------|
| 入力 | `(batch_size, num_channels, height, width)` | ndarray(float) | 入力は、 `(1, 3, input_image_height, input_image_width)` バッチサイズが1で、高さと幅が入力イメージに似ている、前処理されたイメージです。 |
        
    
### <a name="output-format"></a>出力形式

出力は、ボックス (インスタンス)、ラベル、およびスコアの組になります。
    
| 出力名       | 出力図形  | 出力の種類 | 説明 |
| -------- |----------|-----|------|
| 矩形 | `(n_boxes, 4)`、各ボックスには `x_min, y_min, x_max, y_max` | ndarray(float) | モデルは、左上と右下の座標を持つ *n 個* のボックスを返します。 |
| ラベル | `(n_boxes)`| ndarray(float) | 各ボックス内のオブジェクトのラベルまたはクラスID。 |  
| スコア | `(n_boxes)` | ndarray(float) | 各ボックス内のオブジェクトの信頼スコア。 |    
| マスク | `(n_boxes, 1, height, width)` | ndarray(float) | 入力イメージの形状の高さと幅を持つ、検出されたオブジェクトのマスク (ポリゴン)。 |    

---

## <a name="preprocessing"></a>前処理

# <a name="multi-class-image-classification"></a>[複数クラスの画像分類](#tab/multi-class)

ONNXモデルの推定について、次の前処理手順を実行します。

1. 画像をRGBに変換します。
2. トレーニング中に検証データセットの変換に使用される値に対応する`valid_resize_size` と `valid_resize_size`値に画像のサイズを変更します。 `valid_resize_size`の既定値は256です。
3. [中央] 画像を`height_onnx_crop_size`と`width_onnx_crop_size`にトリミングします。 これは、既定値である224の`valid_crop_size`に対応しています。
4. `HxWxC` を `CxHxW` に変更します。
5. Float型に変換します。
6. ImageNetの`mean` = `[0.485, 0.456, 0.406]`および`std` = `[0.229, 0.224, 0.225]`で正規化 します。

トレーニング中に[ハイパーパラメーター](how-to-auto-train-image-models.md#configure-model-algorithms-and-hyperparameters)`valid_resize_size`と`valid_crop_size`に異なる値を選択した場合は、それらの値を使用する必要があります。

ONNXモデルに必要な入力図形を取得します。

```python
batch, channel, height_onnx_crop_size, width_onnx_crop_size = session.get_inputs()[0].shape
batch, channel, height_onnx_crop_size, width_onnx_crop_size
```

### <a name="without-pytorch"></a>PyTorchなし

```python
def preprocess(image, resize_size, crop_size_onnx):
    """perform pre-processing on raw input image
  
    :param image: raw input image
    :type image: PIL image
    :param resize_size: value to resize the image
    :type image: Int
    :param crop_size_onnx: expected crop size of an input image in onnx model
    :type crop_size_onnx: Int
    :return: pre-processed image in numpy format
    :rtype: ndarray of shape 1xCxHxW
    """

    image = image.convert('RGB')
    # resize
    image = image.resize((resize_size, resize_size))

    # center crop
    left = (resize_size - crop_size_onnx)/2
    top = (resize_size - crop_size_onnx)/2
    right = (resize_size + crop_size_onnx)/2
    bottom = (resize_size + crop_size_onnx)/2
    image = image.crop((left, top, right, bottom))
    np_image = np.array(image)

    # HWC -> CHW
    np_image = np_image.transpose(2, 0, 1)# CxHxW

    # normalize the image
    mean_vec = np.array([0.485, 0.456, 0.406])
    std_vec = np.array([0.229, 0.224, 0.225])
    norm_img_data = np.zeros(np_image.shape).astype('float32')
    for i in range(np_image.shape[0]):
        norm_img_data[i,:,:] = (np_image[i,:,:]/255 - mean_vec[i]) / std_vec[i]
    np_image = np.expand_dims(norm_img_data, axis=0)# 1xCxHxW
    return np_image

from PIL import Image

# you can modify resize_size based on your trained model
resize_size = 256 

# height and width will be the same for classification
crop_size_onnx = height_onnx_crop_size 
test_image_path = "automl_models/test_image.jpg"
img = Image.open(test_image_path)
print("Input image dimensions: ", img.size)
display(img)
img_data = preprocess(img, resize_size, crop_size_onnx)

```

### <a name="with-pytorch"></a>With PyTorch

```python
import torch
from torchvision import transforms
# tested with torch version: '1.7.1', torchvision version: '0.8.2')

def _make_3d_tensor(x) -> torch.Tensor:
    """This function is for images that have less channels.

    :param x: input tensor
    :type x: torch.Tensor
    :return: return a tensor with the correct number of channels
    :rtype: torch.Tensor
    """
    return x if x.shape[0] == 3 else x.expand((3, x.shape[1], x.shape[2]))

def preprocess(image, resize_size, crop_size_onnx):
    transform = transforms.Compose([
        transforms.Resize(resize_size),
        transforms.CenterCrop(crop_size_onnx),
        transforms.ToTensor(),
        transforms.Lambda(_make_3d_tensor),
        transforms.Normalize([0.485, 0.456, 0.406], [0.229, 0.224, 0.225])])
    
    img_data = transform(image)
    img_data = img_data.numpy()
    img_data = np.expand_dims(img_data, axis=0)
    return img_data

# you can modify resize_size based on your trained model
resize_size = 256

#height and width will be the same for classification
test_image_path = "automl_models/test_image.jpg"
img = Image.open(test_image_path)
print("Input image dimensions: ", img.size)
display(img)
img_data = preprocess(img, resize_size, crop_size_onnx)
```

# <a name="multi-label-image-classification"></a>[複数ラベルの画像分類](#tab/multi-label)

ONNXモデルの推定について、次の前処理手順を実行します。 これらの手順は、複数クラスの画像分類の場合と同じです。

1. 画像をRGBに変換します。
2. トレーニング中に検証データセットの変換に使用される値に対応する`valid_resize_size` と `valid_resize_size`値に画像のサイズを変更します。 `valid_resize_size`の既定値は256です。
3. [中央] 画像を`height_onnx_crop_size`と`width_onnx_crop_size`にトリミングします。 これは、既定値である224の`valid_crop_size`に対応しています。
4. `HxWxC` を `CxHxW` に変更します。
5. Float型に変換します。
6. ImageNetの`mean` = `[0.485, 0.456, 0.406]`および`std` = `[0.229, 0.224, 0.225]`で正規化 します。

トレーニング中に[ハイパーパラメーター](how-to-auto-train-image-models.md#configure-model-algorithms-and-hyperparameters)`valid_resize_size`と`valid_crop_size`に異なる値を選択した場合は、それらの値を使用する必要があります。

ONNXモデルに必要な入力図形を取得します。

```python
batch, channel, height_onnx_crop_size, width_onnx_crop_size = session.get_inputs()[0].shape
batch, channel, height_onnx_crop_size, width_onnx_crop_size
```

### <a name="without-pytorch"></a>PyTorchなし

```python
def preprocess(image, resize_size, crop_size_onnx):
    """perform pre-processing on raw input image
  
    :param image: raw input image
    :type image: PIL image
    :param resize_size: value to resize the image
    :type image: Int
    :param crop_size_onnx: expected crop size of an input image in onnx model
    :type crop_size_onnx: Int
    :return: pre-processed image in numpy format
    :rtype: ndarray of shape 1xCxHxW
    """

    image = image.convert('RGB')
    # resize
    image = image.resize((resize_size, resize_size))

    # center crop
    left = (resize_size - crop_size_onnx)/2
    top = (resize_size - crop_size_onnx)/2
    right = (resize_size + crop_size_onnx)/2
    bottom = (resize_size + crop_size_onnx)/2
    image = image.crop((left, top, right, bottom))
    np_image = np.array(image)

    # HWC -> CHW
    np_image = np_image.transpose(2, 0, 1)# CxHxW

    # normalize the image
    mean_vec = np.array([0.485, 0.456, 0.406])
    std_vec = np.array([0.229, 0.224, 0.225])
    norm_img_data = np.zeros(np_image.shape).astype('float32')
    for i in range(np_image.shape[0]):
        norm_img_data[i,:,:] = (np_image[i,:,:]/255 - mean_vec[i]) / std_vec[i]
    np_image = np.expand_dims(norm_img_data, axis=0)# 1xCxHxW
    return np_image

from PIL import Image

# you can modify resize_size based on your trained model
resize_size = 256 

# height and width will be the same for classification
crop_size_onnx = height_onnx_crop_size 
test_image_path = "automl_models/test_image.jpg"
img = Image.open(test_image_path)
print("Input image dimensions: ", img.size)
display(img)
img_data = preprocess(img, resize_size, crop_size_onnx)

```

### <a name="with-pytorch"></a>With PyTorch

```python
import torch
from torchvision import transforms
# tested with torch version: '1.7.1', torchvision version: '0.8.2')

def _make_3d_tensor(x) -> torch.Tensor:
    """This function is for images that have less channels.

    :param x: input tensor
    :type x: torch.Tensor
    :return: return a tensor with the correct number of channels
    :rtype: torch.Tensor
    """
    return x if x.shape[0] == 3 else x.expand((3, x.shape[1], x.shape[2]))

def preprocess(image, resize_size, crop_size_onnx):
    transform = transforms.Compose([
        transforms.Resize(resize_size),
        transforms.CenterCrop(crop_size_onnx),
        transforms.ToTensor(),
        transforms.Lambda(_make_3d_tensor),
        transforms.Normalize([0.485, 0.456, 0.406], [0.229, 0.224, 0.225])])
    
    img_data = transform(image)
    img_data = img_data.numpy()
    img_data = np.expand_dims(img_data, axis=0)
    return img_data

# you can modify resize_size based on your trained model
resize_size = 256

# height and width will be the same for classification
crop_size_onnx = height_onnx_crop_size 
test_image_path = "automl_models/test_image.jpg"
img = Image.open(test_image_path)
print("Input image dimensions: ", img.size)
display(img)
img_data = preprocess(img, resize_size, crop_size_onnx)
```


# <a name="object-detection-with-faster-r-cnn"></a>[より高速なR-CNN を使用した物体検出](#tab/object-detect-cnn)

より高速なR-CNNアルゴリズムを使用した物体検出では、イメージのトリミングを除き、画像分類と同じ前処理手順に従います。 高さ `600` と幅 `800` を使用してイメージのサイズを変更できます。 次のコードを使用して、予想される入力の高さと幅を取得できます。

```python
batch, channel, height_onnx, width_onnx = session.get_inputs()[0].shape
batch, channel, height_onnx, width_onnx
```

次に、前処理の手順を実行します。

```python
def preprocess(image, height_onnx, width_onnx):
    """perform pre-processing on raw input image
    
    :param image: raw input image
    :type image: PIL image
    :param height_onnx: expected height of an input image in onnx model
    :type height_onnx: Int
    :param width_onnx: expected width of an input image in onnx model
    :type width_onnx: Int
    :return: pre-processed image in numpy format
    :rtype: ndarray of shape 1xCxHxW
    """

    image = image.convert('RGB')
    image = image.resize((width_onnx, height_onnx))
    np_image = np.array(image)
    # HWC -> CHW
    np_image = np_image.transpose(2, 0, 1)# CxHxW

    # Normalize the image
    mean_vec = np.array([0.485, 0.456, 0.406])
    std_vec = np.array([0.229, 0.224, 0.225])
    norm_img_data = np.zeros(np_image.shape).astype('float32')
    for i in range(np_image.shape[0]):
        norm_img_data[i,:,:] = (np_image[i,:,:]/255 - mean_vec[i]) / std_vec[i]
        
        
    np_image = np.expand_dims(norm_img_data, axis=0)# 1xCxHxW
    return np_image

from PIL import Image

test_image_path = "automl_models/test_image.jpg"
img = Image.open(test_image_path)
print("Input image dimensions: ", img.size)
display(img)
img_data = preprocess(img, height_onnx, width_onnx)
```

# <a name="object-detection-with-yolo"></a>[物体検出とYOLO](#tab/object-detect-yolo)

YOLOアルゴリズムを使用した物体検出では、イメージのトリミングを除き、画像分類と同じ前処理手順に従います。 高さ`600`と幅`800`を指定して画像のサイズを変更 し、次のコードを使用して期待される入力の高さと幅を取得できます。

```python
batch, channel, height_onnx, width_onnx = session.get_inputs()[0].shape
batch, channel, height_onnx, width_onnx
```

YOLOの前処理が必要な場合は、 [yolo_onnx_preprocessing_utils.py](https://github.com/Azure/azureml-examples/tree/main/python-sdk/tutorials/automl-with-azureml/image-object-detection) を参照してください。

```python
from yolo_onnx_preprocessing_utils import preprocess

test_image_path = "automl_models_od_yolo/test_image.jpg"
img_data, pad = preprocess(test_image_path)
```

# <a name="instance-segmentation"></a>[インスタンスのセグメント化](#tab/instance-segmentation)
>[!IMPORTANT]
> インスタンス セグメント化タスクでは、Mask R-CNN のみがサポートされています。 前処理の手順は、Mask R-CNN にのみ基づいています。

ONNXモデルの推定について、次の前処理手順を実行します。

1. 画像をRGBに変換します。
2. イメージのサイズを変更します。 
3. `HxWxC` を `CxHxW` に変更します。
4. Float型に変換します。
5. ImageNetの`mean` = `[0.485, 0.456, 0.406]`および`std` = `[0.229, 0.224, 0.225]`で正規化 します。

`resize_height`と`resize_width`については、トレーニング中に使用した値を使用することもできます。これは、R-CNN のマスクの`min_size`と`max_size` [ハイパーパラメーター](how-to-auto-train-image-models.md#configure-model-algorithms-and-hyperparameters)によって制限されています。

```python
def preprocess(image, resize_height, resize_width):
    """perform pre-processing on raw input image
        
    :param image: raw input image
    :type image: PIL image
    :param resize_height: resize height of an input image
    :type resize_height: Int
    :param resize_width: resize width of an input image
    :type resize_width: Int
    :return: pre-processed image in numpy format
    :rtype: ndarray of shape 1xCxHxW
    """

    image = image.convert('RGB')
    image = image.resize((resize_width,resize_height))
    np_image = np.array(image)

    # HWC -> CHW
    np_image = np_image.transpose(2, 0, 1)# CxHxW

    # normalize the image
    mean_vec = np.array([0.485, 0.456, 0.406])
    std_vec = np.array([0.229, 0.224, 0.225])
    norm_img_data = np.zeros(np_image.shape).astype('float32')
    for i in range(np_image.shape[0]):
        norm_img_data[i,:,:] = (np_image[i,:,:]/255 - mean_vec[i]) / std_vec[i]
    np_image = np.expand_dims(norm_img_data, axis=0)# 1xCxHxW
    return np_image

from PIL import Image
# use height and width based on the trained model
resize_height, resize_width = 600, 800 
test_image_path = 'automl_models/test_image.jpg'
img = Image.open(test_image_path)
print("Input image dimensions: ", img.size)
display(img)
img_data = preprocess(img, resize_height, resize_width)

```

---

## <a name="inference-with-onnx-runtime"></a>ONNX Runtimeでの推論

推論とONNX Runtime は、コンピュータービジョンのタスクごとに異なります。

>[!WARNING]
> バッチ スコアリングは、すべてのコンピューター ビジョン タスクで現在サポートされていません。 

# <a name="multi-class-image-classification"></a>[複数クラスの画像分類](#tab/multi-class)

```python
def get_predictions_from_ONNX(onnx_session,img_data):
    """perform predictions with ONNX Runtime
    
    :param onnx_session: onnx inference session
    :type onnx_session: class InferenceSession
    :param img_data: pre-processed numpy image
    :type img_data: ndarray with shape 1xCxHxW
    :return: scores with shapes
            (1, No. of classes in training dataset) 
    :rtype: ndarray
    """
    sess_input = onnx_session.get_inputs()
    sess_output = onnx_session.get_outputs()
    print(f"No. of inputs : {len(sess_input)}, No. of outputs : {len(sess_output)}")    
    # predict with ONNX Runtime
    output_names = [ output.name for output in sess_output]
    scores = onnx_session.run(output_names=output_names,\
                                               input_feed={sess_input[0].name: img_data})
    return scores[0]

scores = get_predictions_from_ONNX(session, img_data)
```

# <a name="multi-label-image-classification"></a>[複数ラベルの画像分類](#tab/multi-label)

```python
def get_predictions_from_ONNX(onnx_session,img_data):
    """perform predictions with ONNX Runtime
    
    :param onnx_session: onnx inference session
    :type onnx_session: class InferenceSession
    :param img_data: pre-processed numpy image
    :type img_data: ndarray with shape 1xCxHxW
    :return: scores with shapes
            (1, No. of classes in training dataset) 
    :rtype: ndarray
    """
    sess_input = onnx_session.get_inputs()
    sess_output = onnx_session.get_outputs()
    print(f"No. of inputs : {len(sess_input)}, No. of outputs : {len(sess_output)}")    
    # predict with ONNX Runtime
    output_names = [ output.name for output in sess_output]
    scores = onnx_session.run(output_names=output_names,\
                                               input_feed={sess_input[0].name: img_data})
    return scores[0]

scores = get_predictions_from_ONNX(session, img_data)
```

# <a name="object-detection-with-faster-r-cnn"></a>[より高速なR-CNN を使用した物体検出](#tab/object-detect-cnn)

```python
def get_predictions_from_ONNX(onnx_session,img_data):
    """perform predictions with ONNX Runtime
    
    :param onnx_session: onnx model session
    :type onnx_session: class InferenceSession
    :param img_data: pre-processed numpy image
    :type img_data: ndarray with shape 1xCxHxW
    :return: boxes, labels , scores 
            (No. of boxes, 4) (No. of boxes,) (No. of boxes,)
    :rtype: tuple
    """
    sess_input = onnx_session.get_inputs()
    sess_output = onnx_session.get_outputs()
    
    # predict with ONNX Runtime
    output_names = [ output.name for output in sess_output]
    boxes, labels, scores = onnx_session.run(output_names=output_names,\
                                               input_feed={sess_input[0].name: img_data})
    return boxes, labels, scores

boxes, labels, scores = get_predictions_from_ONNX(session, img_data)
```

# <a name="object-detection-with-yolo"></a>[物体検出とYOLO](#tab/object-detect-yolo)

```python
def get_predictions_from_ONNX(onnx_session,img_data):
    """perform predictions with ONNX Runtime
    
    :param onnx_session: onnx model session
    :type onnx_session: class InferenceSession
    :param img_data: pre-processed numpy image
    :type img_data: ndarray with shape 1xCxHxW
    :return: boxes, labels , scores 
    :rtype: list
    """
    sess_input = onnx_session.get_inputs()
    sess_output = onnx_session.get_outputs()
    # predict with ONNX Runtime
    output_names = [ output.name for output in sess_output]
    pred = onnx_session.run(output_names=output_names,\
                                               input_feed={sess_input[0].name: img_data})
    return pred[0]

result = get_predictions_from_ONNX(session, img_data)

```

# <a name="instance-segmentation"></a>[インスタンスのセグメント化](#tab/instance-segmentation)

インスタンスのセグメント化モデルでは、ボックス、ラベル、スコア、およびマスクが予測されます。 ONNX は、対応する境界ボックスとクラスの信頼度スコアと共に、インスタンスごとに予測されるマスクを出力します。 必要に応じて、バイナリマスクから多角形への変換が必要になる場合があります。

```python
def get_predictions_from_ONNX(onnx_session,img_data):
    """perform predictions with ONNX Runtime
    
    :param onnx_session: onnx model session
    :type onnx_session: class InferenceSession
    :param img_data: pre-processed numpy image
    :type img_data: ndarray with shape 1xCxHxW
    :return: boxes, labels , scores , masks with shapes
            (No. of instances, 4) (No. of instances,) (No. of instances,)
            (No. of instances, 1, HEIGHT, WIDTH))  
    :rtype: tuple
    """
    
    sess_input = onnx_session.get_inputs()
    sess_output = onnx_session.get_outputs()
    # predict with ONNX Runtime
    output_names = [ output.name for output in sess_output]
    boxes, labels, scores, masks = onnx_session.run(output_names=output_names,\
                                               input_feed={sess_input[0].name: img_data})
    return boxes, labels, scores, masks


boxes, labels, scores, masks = get_predictions_from_ONNX(session, img_data)
```

---

## <a name="postprocessing"></a>後処理

# <a name="multi-class-image-classification"></a>[複数クラスの画像分類](#tab/multi-class)

予測された値`softmax()`を適用して、各クラスの信頼度スコア (確率) を取得します。 次に、確率が最も高いクラスが予測になります。 

### <a name="without-pytorch"></a>PyTorchなし

```python

def softmax(x):
    x = x.reshape(-1)
    e_x = np.exp(x - np.max(x))
    return e_x / e_x.sum(axis=0)
conf_scores = softmax(scores).tolist()
class_idx = np.argmax(conf_scores)
print("predicted class:",(class_idx, classes[class_idx]))

```

### <a name="with-pytorch"></a>With PyTorch

```python

conf_scores = torch.nn.functional.softmax(torch.from_numpy(scores), dim=1).tolist()[0]
class_idx = np.argmax(conf_scores)
print("predicted class:", (class_idx, classes[class_idx]))
```

# <a name="multi-label-image-classification"></a>[複数ラベルの画像分類](#tab/multi-label)

この手順は、複数クラス分類とは異なります。 複数ラベルの画像分類の信頼度スコアを取得するために、logits (ONNX output) に`sigmoid`を適用する必要があります。

### <a name="without-pytorch"></a>PyTorchなし

```python

import math

def sigmoid(x):
    return 1 / (1 + math.exp(-x))

# we apply a threshold of 0.5 on confidence scores
cutoff_threshold = .5
for class_idx, prob in enumerate([sigmoid(logit) for logit in scores[0]]):
    if prob > cutoff_threshold:
        print("predicted class:", (class_idx, classes[class_idx]))
```

### <a name="with-pytorch"></a>With PyTorch

```python

# we apply a threshold of 0.5 on confidence scores
cutoff_threshold = .5
conf_scores = torch.sigmoid(torch.from_numpy(scores[0])).tolist()
for class_idx, prob in enumerate(conf_scores):
    if prob > cutoff_threshold:
        print("predicted class:", (class_idx, classes[class_idx]))
```

複数クラスおよび複数ラベルの分類では、「AutoML」でサポートされているすべてのアルゴリズムについて前述した同じ手順に従うことができます。


# <a name="object-detection-with-faster-r-cnn"></a>[より高速なR-CNN を使用した物体検出](#tab/object-detect-cnn)

```python
def _get_box_dims(image_shape, box):
    box_keys = ['topX', 'topY', 'bottomX', 'bottomY']
    height, width = image_shape[0], image_shape[1]

    box_dims = dict(zip(box_keys, [coordinate.item() for coordinate in box]))

    box_dims['topX'] = box_dims['topX'] * 1.0 / width
    box_dims['bottomX'] = box_dims['bottomX'] * 1.0 / width
    box_dims['topY'] = box_dims['topY'] * 1.0 / height
    box_dims['bottomY'] = box_dims['bottomY'] * 1.0 / height

    return box_dims

def _get_prediction(boxes, labels, scores, image_shape, classes):
    bounding_boxes = []
    for box, label_index, score in zip(boxes, labels, scores):
        box_dims = _get_box_dims(image_shape, box)

        box_record = {'box': box_dims,
                      'label': classes[label_index],
                      'score': score.item()}

        bounding_boxes.append(box_record)

    return bounding_boxes

bounding_boxes = _get_prediction(boxes, labels, scores, (height_onnx,width_onnx), classes)
print(json.dumps(bounding_boxes, indent=1))

# Filter the results with a threshold.
# Replace the threshold for your test scenario.
score_threshold = 0.8
filtered_bounding_boxes = []
for box in bounding_boxes:
    if box['score'] >= score_threshold:
        filtered_bounding_boxes.append(box)
```

### <a name="visualize-boxes"></a>ボックスを視覚化する

```python

import matplotlib.pyplot as plt
import matplotlib.image as mpimg
import matplotlib.patches as patches
from PIL import Image
%matplotlib inline

IMAGE_SIZE = (18,12)
plt.figure(figsize=IMAGE_SIZE)
img_np = mpimg.imread(test_image_path)
img = Image.fromarray(img_np.astype('uint8'),'RGB')
x, y = img.size
print(img.size)

fig,ax = plt.subplots(1)
# Display the image
ax.imshow(img_np)

# Draw a box and label for each detection 
for detect in filtered_bounding_boxes:
    label = detect['label']
    box = detect['box']
    ymin, xmin, ymax, xmax =  box['topY'],box['topX'], box['bottomY'],box['bottomX']
    topleft_x, topleft_y = x * xmin, y * ymin
    width, height = x * (xmax - xmin), y * (ymax - ymin)
    print('{}: {}, {}, {}, {}'.format(detect['label'], topleft_x, topleft_y, width, height))
    rect = patches.Rectangle((topleft_x, topleft_y), width, height, 
                             linewidth=1, edgecolor='green',facecolor='none')

    ax.add_patch(rect)
    color = 'green'
    plt.text(topleft_x, topleft_y, label, color=color)

plt.show()
```

# <a name="object-detection-with-yolo"></a>[物体検出とYOLO](#tab/object-detect-yolo)

次のコードは、ボックス、ラベル、およびスコアを作成します。 これらの境界ボックスの詳細を使用して、より高速なR-CNN モデルに対して行ったのと同じ後処理手順を実行します。 

```python
from yolo_onnx_preprocessing_utils import non_max_suppression, _convert_to_rcnn_output
import torch

result = non_max_suppression(
    torch.from_numpy(result),
    conf_thres=.1,
    iou_thres=.5)
label, image_shape = _convert_to_rcnn_output(result[0], height_onnx, width_onnx, pad)
boxes = np.array(label["boxes"])
labels = np.array(label["labels"])
labels = [label[0] for label in labels]
scores = np.array(label["scores"])
scores = [score[0] for score in scores]
boxes, labels, scores
```

### <a name="visualize-boxes"></a>ボックスを視覚化する

```python

import matplotlib.pyplot as plt
import matplotlib.image as mpimg
import matplotlib.patches as patches
from PIL import Image
%matplotlib inline

IMAGE_SIZE = (18,12)
plt.figure(figsize=IMAGE_SIZE)
img_np = mpimg.imread(test_image_path)
img = Image.fromarray(img_np.astype('uint8'),'RGB')
x, y = img.size
print(img.size)

fig,ax = plt.subplots(1)
# Display the image
ax.imshow(img_np)

# Draw a box and label for each detection 
for detect in filtered_bounding_boxes:
    label = detect['label']
    box = detect['box']
    ymin, xmin, ymax, xmax =  box['topY'],box['topX'], box['bottomY'],box['bottomX']
    topleft_x, topleft_y = x * xmin, y * ymin
    width, height = x * (xmax - xmin), y * (ymax - ymin)
    print('{}: {}, {}, {}, {}'.format(detect['label'], topleft_x, topleft_y, width, height))
    rect = patches.Rectangle((topleft_x, topleft_y), width, height, 
                             linewidth=1, edgecolor='green',facecolor='none')

    ax.add_patch(rect)
    color = 'green'
    plt.text(topleft_x, topleft_y, label, color=color)

plt.show()
```


# <a name="instance-segmentation"></a>[インスタンスのセグメント化](#tab/instance-segmentation)

#### <a name="visualize-the-masks-and-bounding-boxes"></a>マスクと境界ボックスを視覚化する

```python
import numpy as np
import matplotlib.pyplot as plt
import matplotlib.patches as patches
import cv2
%matplotlib inline

def display_detections(image, boxes, labels, scores, masks, resize_height, 
                       resize_width, classes, score_threshold=0.3):
    """visualize boxes and masks
    
    :param image: raw image
    :type image: PIL image
    :param boxes: box with shape (No. of instances, 4) 
    :type boxes: ndarray 
    :param labels: classes with shape (No. of instances,) 
    :type labels: ndarray
    :param scores: scores with shape (No. of instances,)
    :type scores: ndarray
    :param masks: masks with shape (No. of instances, 1, HEIGHT, WIDTH) 
    :type masks:  ndarray
    :param resize_height: resize height of an input image
    :type resize_height: Int
    :param resize_width: resize width of an input image
    :type resize_width: Int
    :param classes: classes with shape (No. of classes) 
    :type classes:  list
    :param score_threshold: threshold on scores in the range of 0-1
    :type score_threshold: float
    :return: None     
   
    """

    _, ax = plt.subplots(1, figsize=(12,9))

    image = np.array(image)
    original_height = image.shape[0]
    original_width = image.shape[1]

    for mask, box, label, score in zip(masks, boxes, labels, scores):        
        if score <= score_threshold:
            continue
        mask = mask[0, :, :, None]        
        # resize boxes to original raw input size
        box = [box[0]*original_width/resize_width, 
               box[1]*original_height/resize_height, 
               box[2]*original_width/resize_width, 
               box[3]*original_height/resize_height]
        
        mask = cv2.resize(mask, (image.shape[1],image.shape[0]), 0, 0, interpolation = cv2.INTER_NEAREST)
        # mask is a matrix with values in the range of [0,1]
        # higher values indicate the presence of an object and vice versa
        # select the threshold or cutoff value to get objects present               
        mask = mask > 0.5 
        image_masked = image.copy()
        image_masked[mask] = (0, 255, 255)
        alpha = .5 # alpha blending with range 0 to 1
        cv2.addWeighted(image_masked, alpha, image, 1 - alpha,0, image)        
        rect = patches.Rectangle((box[0], box[1]), box[2] - box[0], box[3] - box[1],\
                                 linewidth=1, edgecolor='b', facecolor='none')
        ax.annotate(classes[label] + ':' + str(np.round(score, 2)), (box[0], box[1]),\
                    color='w', fontsize=12)
        ax.add_patch(rect)
        

    ax.imshow(image)
    plt.show()

display_detections(img, boxes.copy(), labels, scores, masks.copy(), 
                   resize_height, resize_width, classes, score_threshold=.5)
```

---


## <a name="next-steps"></a>次の手順
* [AutoMLでのコンピュータービジョンのタスクについての詳細情報](how-to-auto-train-image-models.md)
* [AutoMLの実験のトラブルシューティング](how-to-troubleshoot-auto-ml.md)

