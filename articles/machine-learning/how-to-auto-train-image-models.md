---
title: Computer Vision 用に AutoML を設定する
titleSuffix: Azure Machine Learning
description: Azure Machine Learning Python SDK で Computer Vision モデルをトレーニングするために、Azure Machine Learning 自動 ML を設定します (プレビュー)。
services: machine-learning
author: swatig007
ms.author: swatig
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.custom: automl
ms.date: 10/06/2021
ms.openlocfilehash: 312eea28371de6726bca5d1a89e305024e552dad
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130254367"
---
# <a name="set-up-automl-to-train-computer-vision-models-with-python-preview"></a>Python を使用して Computer Vision モデルをトレーニングするために AutoML を設定する (プレビュー)

> [!IMPORTANT]
> 現在、この機能はパブリック プレビュー段階にあります。 このプレビュー バージョンは、サービス レベル アグリーメントなしに提供されます。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

この記事では、[Azure Machine Learning Python SDK](/python/api/overview/azure/ml/) の自動 ML を使用して、画像データに対する Computer Vision モデルをトレーニングする方法を説明します。

自動 ML では、画像分類、物体検出、インスタンス セグメント化などの Computer Vision タスク用のモデル トレーニングがサポートされています。 現在、Computer Vision タスク用の AutoML モデルの作成は、Azure Machine Learning Python SDK を介してサポートされています。 結果として得られる実験の実行、モデル、出力には、Azure Machine Learning スタジオ UI からアクセスできます。 [画像データに対する Computer Vision タスク用の自動 ML の詳細を確認します](concept-automated-ml.md)。

> [!NOTE]
> Computer Vision タスク用の自動 ML は、Azure Machine Learning Python SDK を介してのみ使用できます。 

## <a name="prerequisites"></a>前提条件

* Azure Machine Learning ワークスペース。 ワークスペースを作成するには、[Azure Machine Learning ワークスペース](how-to-manage-workspace.md)の作成に関するページを参照してください。

* Azure Machine Learning Python SDK がインストールされていること。
    SDK をインストールするには、次のいずれかを行います。 
    * コンピューティング インスタンスを作成します。これにより、SDK が自動的にインストールされ、ML ワークフロー用に事前構成されます。 詳細については、「[Azure Machine Learning コンピューティング インスタンスの作成と管理](how-to-create-manage-compute-instance.md)」を参照してください。

    * SDK の[既定のインストール](/python/api/overview/azure/ml/install#default-install)が含まれる、[`automl` パッケージを自分でインストール](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/README.md#setup-using-a-local-conda-environment)します。
    
    > [!NOTE]
    > Python 3.6 と 3.7 のみ、Computer Vision タスクの自動 ML のサポートと互換性があります。 

## <a name="select-your-task-type"></a>タスクの種類の選択
画像の自動 ML では、次のタスクの種類がサポートされています。


タスクの種類 | AutoMLImage 構成構文
---|---
 画像の分類 | `ImageTask.IMAGE_CLASSIFICATION`
画像分類の複数ラベル | `ImageTask.IMAGE_CLASSIFICATION_MULTILABEL`
画像の物体検出 | `ImageTask.IMAGE_OBJECT_DETECTION`
画像インスタンスのセグメント化| `ImageTask.IMAGE_INSTANCE_SEGMENTATION`

このタスクの種類は必須のパラメーターであり、`AutoMLImageConfig` の `task` パラメーターを使用して渡されます。 次に例を示します。

```python
from azureml.train.automl import AutoMLImageConfig
from azureml.automl.core.shared.constants import ImageTask
automl_image_config = AutoMLImageConfig(task=ImageTask.IMAGE_OBJECT_DETECTION)
```

## <a name="training-and-validation-data"></a>データをトレーニングして検証する

Computer Vision モデルを生成するには、ラベル付き画像データを Azure Machine Learning [TabularDataset](/python/api/azureml-core/azureml.data.tabulardataset) の形式でモデル トレーニングの入力として取り込む必要があります。 [データ ラベル付けプロジェクトからエクスポート](./how-to-create-image-labeling-projects.md#export-the-labels)した `TabularDataset` を使用するか、ラベル付けされたトレーニング データで新しい `TabularDataset` を作成できます。 

トレーニング データの形式が異なる (pascal VOC や COCO など) 場合は、サンプル ノートブックに含まれるヘルパー スクリプトを適用して、データを JSONL に変換できます。 [自動 ML で Computer Vision タスク用にデータを準備する](how-to-prepare-datasets-for-automl-images.md)方法の詳細を確認してください。 

> [!Warning]
> TabularDatasets の作成は、SDK を使用して、この機能用に JSONL 形式のデータからデータセットを作成する場合にのみサポートされます。 現時点では、UI によるデータセットの作成はサポートされていません。


### <a name="jsonl-schema-samples"></a>JSONL スキーマのサンプル

TabularDataset の構造は、手元のタスクによって異なります。 Computer Vision タスクの種類の場合、次のフィールドで構成されます。

フィールド| 説明
---|---
`image_url`| StreamInfo オブジェクトとしてファイルパスが含まれます
`image_details`|画像メタデータ情報は、高さ、幅、および形式で構成されます。 このフィールドは省略可能であるため、存在する場合と存在しない場合があります。
`label`| タスクの種類に基づく画像ラベルの JSON 表現。

画像分類のサンプル JSONL ファイルを次に示します。

```python
{
      "image_url": "AmlDatastore://image_data/Image_01.png",
      "image_details":
      {
          "format": "png",
          "width": "2230px",
          "height": "4356px"
      },
      "label": "cat"
  }
  {
      "image_url": "AmlDatastore://image_data/Image_02.jpeg",
      "image_details":
      {
          "format": "jpeg",
          "width": "3456px",
          "height": "3467px"
      },
      "label": "dog"
  }
  ```

  次のコードは、物体検出のサンプル JSONL ファイルです。

  ```python
  {
      "image_url": "AmlDatastore://image_data/Image_01.png",
      "image_details":
      {
          "format": "png",
          "width": "2230px",
          "height": "4356px"
      },
      "label":
      {
          "label": "cat",
          "topX": "1",
          "topY": "0",
          "bottomX": "0",
          "bottomY": "1",
          "isCrowd": "true",
      }
  }
  {
      "image_url": "AmlDatastore://image_data/Image_02.png",
      "image_details":
      {
          "format": "jpeg",
          "width": "1230px",
          "height": "2356px"
      },
      "label":
      {
          "label": "dog",
          "topX": "0",
          "topY": "1",
          "bottomX": "0",
          "bottomY": "1",
          "isCrowd": "false",
      }
  }
  ```


### <a name="consume-data"></a>データの使用

データが JSONL 形式になったら、次のコードを使用して TabularDataset を作成できます。

```python
from azureml.core import Dataset

training_dataset = Dataset.Tabular.from_json_lines_files(
        path=ds.path('odFridgeObjects/odFridgeObjects.jsonl'),
        set_column_types={'image_url': DataType.to_stream(ds.workspace)})
training_dataset = training_dataset.register(workspace=ws, name=training_dataset_name)
```

自動 ML では、Computer Vision タスク用のトレーニングまたは検証データ サイズに制約がありません。 データセットの最大サイズは、データセットの背後にあるストレージ レイヤー (つまり BLOB ストア) によってのみ制限されます。 画像またはラベルの最小数はありません。 ただし、出力モデルが十分にトレーニングされるように、ラベルあたり少なくとも 10 から 15 のサンプルから開始することが推奨されます。 ラベルやクラスの合計数が多いほど、ラベルごとに必要なサンプルが多くなります。



トレーニング データは必須であり、`training_data` パラメーターを使用して渡されます。 必要に応じて、AutoMLImageConfig の `validation_data` パラメーターで、モデルに使用する検証データセットとして別の TabularDataset を指定できます。 検証データセットを指定していない場合、別の値で `split_ratio` 引数を渡さない限り、トレーニング データの 20% が既定で検証に使用されます。

次に例を示します。

```python
from azureml.train.automl import AutoMLImageConfig
automl_image_config = AutoMLImageConfig(training_data=training_dataset)
```

## <a name="compute-to-run-experiment"></a>実験を実行するために計算する

モデル トレーニングを実施する自動 ML の[コンピューティング ターゲット](concept-azure-machine-learning-architecture.md#compute-targets)を指定します。 Computer Vision タスク用の自動 ML モデルでは、GPU SKU が必要であり、NC ファミリと ND ファミリがサポートされています。 トレーニングを高速化するには NCsv3 シリーズ (v100 GPU 搭載) が推奨されます。 マルチ GPU VM SKU を使用するコンピューティング ターゲットで、複数の GPU を利用することでもトレーニングが高速化されます。 さらに、複数のノードでコンピューティング ターゲットを設定する場合に、モデルのハイパーパラメーターを調整するときに、並列処理を使用してモデルのトレーニングを高速化できます。

コンピューティング ターゲットは必須のパラメーターであり、`AutoMLImageConfig` の `compute_target` パラメーターを使用して渡されます。 次に例を示します。

```python
from azureml.train.automl import AutoMLImageConfig
automl_image_config = AutoMLImageConfig(compute_target=compute_target)
```

## <a name="configure-model-algorithms-and-hyperparameters"></a>モデル アルゴリズムとハイパーパラメーターを構成する

Computer Vision タスクのサポートによって、モデル アルゴリズムを制御して、ハイパーパラメーターをスイープできます。 これらのモデル アルゴリズムとハイパーパラメーターは、スイープのためのパラメーター空間として渡されます。

モデル アルゴリズムは必須であり、`model_name` パラメーターを使用して渡されます。 1 つの `model_name` を指定するか、複数から選択できます。 モデル アルゴリズムの制御に加えて、モデルのトレーニングに使用されるハイパーパラメーターを調整できます。 公開されているハイパーパラメーターの多くはモデルに依存しませんが、ハイパーパラメーターがタスク固有またはモデル固有である場合があります。

### <a name="supported-model-algorithms"></a>サポートされているモデル アルゴリズム

次の表は、各 Computer Vision タスクでサポートされているモデルをまとめたものです。 

タスク |  モデル アルゴリズム | 文字列リテラル構文<br> ***`default_model`\**** を \* で示す
---|----------|----------
画像の分類<br> (複数クラスおよび複数ラベル)| **MobileNet**: モバイル アプリケーション用の軽量モデル <br> **ResNet**: 残差ネットワーク<br> **ResNeSt**: スプリット アテンション ネットワーク<br> **SE-ResNeXt50**: スクイーズおよび励起ネットワーク<br> **ViT**: Vision Transformer ネットワーク| `mobilenetv2`   <br>`resnet18` <br>`resnet34` <br> `resnet50`  <br> `resnet101` <br> `resnet152`    <br> `resnest50` <br> `resnest101`  <br> `seresnext`  <br> `vits16r224` (小) <br> **_`vitb16r224`\_*** (基本) <br>`vitl16r224` (大)|
オブジェクトの検出 | **YOLOv5**: 1 ステージ オブジェクト検出モデル   <br>  **Faster RCNN ResNet FPN**: 2 ステージ オブジェクト検出モデル  <br> **RetinaNet ResNet FPN**: Focal Loss によってクラスの不均衡に対処する <br> <br>*注: YOLOv5 モデルのサイズについては、[`model_size`ハイパーパラメーター](#model-specific-hyperparameters)を参照してください。*| ***`yolov5`\**** <br> `fasterrcnn_resnet18_fpn` <br> `fasterrcnn_resnet34_fpn` <br> `fasterrcnn_resnet50_fpn` <br> `fasterrcnn_resnet101_fpn` <br> `fasterrcnn_resnet152_fpn` <br> `retinanet_resnet50_fpn` 
インスタンスのセグメント化 | **MaskRCNN ResNet FPN**| `maskrcnn_resnet18_fpn` <br> `maskrcnn_resnet34_fpn` <br> **_`maskrcnn_resnet50_fpn`\_***  <br> `maskrcnn_resnet101_fpn` <br> `maskrcnn_resnet152_fpn` <br>`maskrcnn_resnet50_fpn`


### <a name="model-agnostic-hyperparameters"></a>モデルに依存しないハイパーパラメーター

次の表に、モデルに依存しないハイパーパラメーターについて説明します。

| パラメーター名 | 説明 | Default|
| ------------ | ------------- | ------------ |
| `number_of_epochs` | トレーニング エポックの数。 <br>正の整数にする必要があります。 |  15 <br> (`yolov5`: 30 を除く) |
| `training_batch_size` | トレーニング バッチ サイズ。<br> 正の整数にする必要があります。  | 複数クラス/複数ラベル: 78 <br>(*vit-variants*: <br> `vits16r224`: 128 <br>`vitb16r224`: 48 <br>`vitl16r224`:10 を除く)<br><br>物体検出: 2 <br>(`yolov5`: 16 を除く) <br><br> インスタンスのセグメント化: 2  <br> <br> *注: 既定値は、12 GIB GPU メモリで使用できる最大バッチ サイズです*。|
| `validation_batch_size` | 検証バッチ サイズ。<br> 正の整数にする必要があります。 | 複数クラス/複数ラベル: 78 <br>(*vit-variants*: <br> `vits16r224`: 128 <br>`vitb16r224`: 48 <br>`vitl16r224`:10 を除く)<br><br>物体検出: 1 <br>(`yolov5`: 16 を除く) <br><br> インスタンスのセグメント化: 1  <br> <br> *注: 既定値は、12 GIB GPU メモリで使用できる最大バッチ サイズです*。|
| `grad_accumulation_step` | Gradient Accumulation とは、構成された数の `grad_accumulation_step` を、モデルの重みを更新せずに実行し、それと同時にそれらのステップの勾配を蓄積して、その蓄積された勾配を使用して重みの更新を計算することを意味します。 <br> 正の整数にする必要があります。 | 1 |
| `early_stopping` | トレーニング中に早期停止ロジックを有効にします。 <br> 0 または 1 にする必要があります。| 1 |
| `early_stopping_patience` | 実行を停止するまでの主要メトリックの改善がない<br>エポックまたは検証評価の最小数。<br> 正の整数にする必要があります。 | 5 |
| `early_stopping_delay` | 早期停止のために主要メトリックの改善が追跡されるまで、<br>待機するエポックまたは検証評価の最小数。<br> 正の整数にする必要があります。 | 5 |
| `learning_rate` | 初期学習率。 <br>範囲 [0, 1] の float にする必要があります。 | 複数クラス: 0.01 <br>(*vit-variants*: <br> `vits16r224`: 0.0125<br>`vitb16r224`: 0.0125<br>`vitl16r224`: 0.001 を除く) <br><br> 複数ラベル: 0.035 <br>(*vit-variants*:<br>`vits16r224`: 0.025<br>`vitb16r224`: 0.025 <br>`vitl16r224`: 0.002 を除く) <br><br> 物体検出: 0.005 <br>(`yolov5`: 0.01 を除く) <br><br> インスタンスのセグメント化: 0.005  |
| `lr_scheduler` | 学習率スケジューラの種類。 <br> `warmup_cosine` または `step` である必要があります。 | `warmup_cosine` |
| `step_lr_gamma` | 学習率スケジューラが `step` の場合のガンマの値。<br> 範囲 [0, 1] の float にする必要があります。 | 0.5 |
| `step_lr_step_size` | 学習率スケジューラが `step` の場合のステップ サイズの値。<br> 正の整数にする必要があります。 | 5 |
| `warmup_cosine_lr_cycles` | 学習率スケジューラが `warmup_cosine` の場合のコサイン周期の値。 <br> 範囲 [0, 1] の float にする必要があります。 | 0.45 |
| `warmup_cosine_lr_warmup_epochs` | 学習率スケジューラが `warmup_cosine` の場合のウォームアップ エポックの値。 <br> 正の整数にする必要があります。 | 2 |
| `optimizer` | オプティマイザーの種類。 <br> `sgd`、`adam`、`adamw` のいずれかにする必要があります。  | `sgd` |
| `momentum` | オプティマイザーが `sgd` の場合のモメンタムの値。 <br> 範囲 [0, 1] の float にする必要があります。 | 0.9 |
| `weight_decay` | オプティマイザーが `sgd`、`adam`、または `adamw` の場合の荷重減衰の値。 <br> 範囲 [0, 1] の float にする必要があります。 | 1e-4 |
|`nesterov`| オプティマイザーが `sgd` の場合に `nesterov` を有効にします。 <br> 0 または 1 にする必要があります。| 1 |
|`beta1` | オプティマイザーが `adam` または `adamw` の場合の `beta1` の値。 <br> 範囲 [0, 1] の float にする必要があります。 | 0.9 |
|`beta2` | オプティマイザーが `adam` または `adamw` の場合の `beta2` の値。<br> 範囲 [0, 1] の float にする必要があります。 | 0.999 |
|`amsgrad` | オプティマイザーが `adam` または `adamw` の場合に `amsgrad` を有効にします。<br> 0 または 1 にする必要があります。 | 0 |
|`evaluation_frequency`| メトリック スコアを取得するために検証データセットを評価する頻度。 <br> 正の整数にする必要があります。 | 1 |
|`split_ratio`| 検証データが定義されていない場合、これはトレーニング データをランダムなトレーニングおよび検証サブセットに分割するための分割率を指定します。 <br> 範囲 [0, 1] の float にする必要があります。| 0.2 |
|`checkpoint_frequency`| モデル チェックポイントを格納する頻度。 <br> 正の整数にする必要があります。 | 検証に最適な主要メトリックによるエポックのチェックポイント。|
|`layers_to_freeze`| モデルで凍結するレイヤーの数。 たとえば、`seresnext` の値として 2 を渡すことは、それ以下のサポートされているモデル レイヤー情報を表している layer0 と layer1 を凍結することを意味します。 <br> 正の整数にする必要があります。 <br><br>`'resnet': [('conv1.', 'bn1.'), 'layer1.', 'layer2.', 'layer3.', 'layer4.'],`<br>`'mobilenetv2': ['features.0.', 'features.1.', 'features.2.', 'features.3.', 'features.4.', 'features.5.', 'features.6.', 'features.7.', 'features.8.', 'features.9.', 'features.10.', 'features.11.', 'features.12.', 'features.13.', 'features.14.', 'features.15.', 'features.16.', 'features.17.', 'features.18.'],`<br>`'seresnext': ['layer0.', 'layer1.', 'layer2.', 'layer3.', 'layer4.'],`<br>`'vit': ['patch_embed', 'blocks.0.', 'blocks.1.', 'blocks.2.', 'blocks.3.', 'blocks.4.', 'blocks.5.', 'blocks.6.','blocks.7.', 'blocks.8.', 'blocks.9.', 'blocks.10.', 'blocks.11.'],`<br>`'yolov5_backbone': ['model.0.', 'model.1.', 'model.2.', 'model.3.', 'model.4.','model.5.', 'model.6.', 'model.7.', 'model.8.', 'model.9.'],`<br>`'resnet_backbone': ['backbone.body.conv1.', 'backbone.body.layer1.', 'backbone.body.layer2.','backbone.body.layer3.', 'backbone.body.layer4.']` | 既定値なし  |


### <a name="task-specific-hyperparameters"></a>タスク固有のハイパーパラメーター

次の表に、画像分類 (複数クラスおよび複数ラベル) タスクのハイパーパラメーターをまとめています。


| パラメーター名       | 説明           | Default  |
| ------------- |-------------|-----|
| `weighted_loss` | 重み付き損失がない場合は 0。<br>sqrt.(class_weights) による重み付き損失の場合は 1 <br> class_weights による重み付き損失の場合は 2。 <br> 0 または 1 または 2 にする必要があります。 | 0 |
| `valid_resize_size` | 検証データセット用にトリミングする前のサイズ変更先の画像のサイズ。 <br> 正の整数にする必要があります。 <br> <br> *注: <li> `seresnext` は任意のサイズを取りません。<li> サイズが大きすぎる場合、トレーニングの実行が CUDA OOM になることがあります*。  | 256  |
| `valid_crop_size` | 検証データセット用にニューラル ネットワークに入力する画像トリミング サイズ。  <br> 正の整数にする必要があります。 <br> <br> *注: <li> `seresnext` は任意のサイズを取りません。<li> *ViT-variants* の `valid_crop_size` と `train_crop_size` は同じである必要があります。<li> サイズが大きすぎる場合、トレーニングの実行が CUDA OOM になることがあります*。 | 224 |
| `train_crop_size` | トレーニング データセット用にニューラル ネットワークに入力する画像トリミング サイズ。  <br> 正の整数にする必要があります。 <br> <br> *注: <li> `seresnext` は任意のサイズを取りません。<li> *ViT-variants* の `valid_crop_size` と `train_crop_size` は同じである必要があります。<li> サイズが大きすぎる場合、トレーニングの実行が CUDA OOM になることがあります*。 | 224 |


次のハイパーパラメーターは、物体検出とインスタンス セグメント化のタスク用です。

> [!Warning]
> これらのパラメーターは、`yolov5` アルゴリズムではサポートされていません。

| パラメーター名       | 説明           | Default  |
| ------------- |-------------|-----|
| `validation_metric_type` | 検証メトリックに使用するメトリック計算方法。  <br> `none`、`coco`、`voc`、または `coco_voc` にする必要があります。 | `voc` |
| `min_size` | バックボーンにフィードする前に再スケーリングする画像の最小サイズ。 <br> 正の整数にする必要があります。 <br> <br> *注: サイズが大きすぎる場合、トレーニングの実行が CUDA OOM になることがあります。*| 600 |
| `max_size` | バックボーンにフィードする前に再スケーリングする画像の最大サイズ。 <br> 正の整数にする必要があります。<br> <br> *注: サイズが大きすぎる場合、トレーニングの実行が CUDA OOM になることがあります。* | 1333 |
| `box_score_thresh` | 推論時に、`box_score_thresh` より大きい分類スコアを持つ提案のみが返されます。 <br> 範囲 [0, 1] の float にする必要があります。| 0.3 |
| `box_nms_thresh` | 予測ヘッドの Non-Maximum Suppression (NMS) しきい値。 推論時に使用されます。  <br>範囲 [0, 1] の float にする必要があります。 | 0.5 |
| `box_detections_per_img` | すべてのクラスで、画像あたりの最大検出数。 <br> 正の整数にする必要があります。| 100 |
| `tile_grid_size` | 各画像のタイルに使用するグリッド サイズ。 <br>*注: [小さな物体の検出](how-to-use-automl-small-object-detect.md)ロジックを有効にするには、tile_grid_size を None にできません*<br> 文字列として渡される 2 つの整数のタプル。 例: --tile_grid_size "(3, 2)" | 既定値なし |
| `tile_overlap_ratio` | 各ディメンションの隣接するタイル間のオーバーラップ率。 <br> [0, 1] の範囲の float にする必要があります。 | 0.25 |
| `tile_predictions_nms_thresh` | タイルおよび画像から予測をマージしながら NMS を実行するために使用する IOU しきい値。 検証や推論で使用されます。 <br> [0, 1] の範囲の float にする必要があります。 | 0.25 |

### <a name="model-specific-hyperparameters"></a>モデル固有のハイパーパラメーター

次の表は、`yolov5` アルゴリズム固有のハイパーパラメーターをまとめたものです。

| パラメーター名       | 説明           | Default  |
| ------------- |-------------|----|
| `validation_metric_type` | 検証メトリックに使用するメトリック計算方法。  <br> `none`、`coco`、`voc`、または `coco_voc` にする必要があります。 | `voc` |
| `img_size` | トレーニングおよび検証用の画像サイズ。 <br> 正の整数にする必要があります。 <br> <br> *注: サイズが大きすぎる場合、トレーニングの実行が CUDA OOM になることがあります。* | 640 |
| `model_size` | モデルのサイズ。 <br> `small`、`medium`、`large`、または `xlarge` にする必要があります。 <br><br> *注: モデルのサイズが大きすぎる場合、トレーニングの実行が CUDA OOM になることがあります。*  | `medium` |
| `multi_scale` | 画像サイズを +/-50% 変化させて、マルチスケール画像を有効にします <br> 0 または 1 にする必要があります。 <br> <br> *注: 十分な GPU メモリがない場合、トレーニングの実行が CUDA OOM になることがあります。* | 0 |
| `box_score_thresh` | 推論時に、`box_score_thresh` より大きいスコアを持つ提案のみが返されます。 スコアは、物体らしさのスコアと分類の確率を乗算したものです。 <br> 範囲 [0, 1] の float にする必要があります。 | 0.1 |
| `box_iou_thresh` | Non-Maximum Suppression の後処理の推論時に使用される IoU しきい値。 <br> 範囲 [0, 1] の float にする必要があります。 | 0.5 |


### <a name="data-augmentation"></a>データの拡張 

一般に、ディープ ラーニング モデルのパフォーマンスは、多くの場合にデータが多いほど向上する可能性があります。 データの拡張は、データのサイズとデータセットの多様性を拡張するための実用的な手法であり、オーバーフィットを防ぎ、目に見えないデータに対するモデルの一般化機能を改善するのに役立ちます。 自動 ML では、入力画像をモデルにフィードする前に、Computer Vision タスクに基づいてさまざまなデータ拡張手法が適用されます。 現在、データ拡張を制御するために公開されたハイパーパラメーターはありません。 

|タスク | 影響を受けるデータセット | 適用されるデータ拡張手法 |
|-------|----------|---------|
|画像分類 (複数クラスおよび複数ラベル) | トレーニング <br><br><br> 検証とテスト| ランダムなサイズ変更とトリミング、左右反転、色ジッター (輝度、コントラスト、彩度、色相)、チャネルごとの ImageNet の平均と標準偏差を使用した正規化 <br><br><br>サイズ変更、中心のトリミング、正規化 |
|物体検出、インスタンスのセグメント化| トレーニング <br><br> 検証とテスト |境界ボックス周囲のランダムなトリミング、展開、左右反転、正規化、サイズ変更 <br><br><br>正規化、サイズ変更
|yolov5 を使用した物体検出| トレーニング <br><br> 検証とテスト  |モザイク、ランダム アフィン (回転、平行移動、スケーリング、傾斜)、左右反転 <br><br><br> レターボックスのサイズ変更|

## <a name="configure-your-experiment-settings"></a>実験の設定を構成する

最適なモデルとハイパーパラメーターを検索するために大きなスイープを実行する前に、既定値を使用して最初のベースラインを取得することが推奨されます。 次に、複数のモデルとそのパラメーターをスイープする前に、同じモデルで複数のハイパーパラメーターを調べることができます。 このように、より反復的なアプローチを採用することができます。複数のモデルとそれぞれに複数のハイパーパラメーターがあることで、検索領域が指数関数的に拡大し、最適な構成を見つけるためにより多くの反復処理が必要になるためです。

特定のアルゴリズム (たとえば yolov5) に既定のハイパーパラメーター値を使用する場合、次のように、AutoML 画像実行の構成を指定できます。

```python
from azureml.train.automl import AutoMLImageConfig
from azureml.train.hyperdrive import GridParameterSampling, choice
from azureml.automl.core.shared.constants import ImageTask

automl_image_config_yolov5 = AutoMLImageConfig(task=ImageTask.IMAGE_OBJECT_DETECTION,
                                               compute_target=compute_target,
                                               training_data=training_dataset,
                                               validation_data=validation_dataset,
                                               hyperparameter_sampling=GridParameterSampling({'model_name': choice('yolov5')}),
                                               iterations=1)
```

ベースライン モデルを構築したら、モデル アルゴリズムとハイパーパラメーター空間をスイープするために、モデルのパフォーマンスを最適化することができます。 次のサンプル構成を使用して、各アルゴリズムのハイパーパラメーターをスイープし、learning_rate、オプティマイザー、lr_scheduler などの値の範囲から選択して、最適な主要メトリックでモデルを生成できます。 ハイパーパラメーター値を指定していない場合は、指定したアルゴリズムに既定値が使われます。

### <a name="primary-metric"></a>主要メトリック

モデルの最適化とハイパーパラメーターの調整に使用される主要メトリックは、タスクの種類によって異なります。 他の主要メトリック値の使用は現在サポートされていません。 

* IMAGE_CLASSIFICATION の `accuracy`
* IMAGE_CLASSIFICATION_MULTILABEL の `iou`
* IMAGE_OBJECT_DETECTION の `mean_average_precision`
* IMAGE_INSTANCE_SEGMENTATION の `mean_average_precision`
    
### <a name="experiment-budget"></a>実験の予算

必要に応じて、`experiment_timeout_hours` を使用して AutoML Vision 実験の最大時間予算 (実験が終了するまでの時間単位での時間) を指定できます。 何も指定しない場合、既定の実験タイムアウトは 7 日 (最大 60 日) です。

## <a name="sweeping-hyperparameters-for-your-model"></a>モデルのハイパーパラメーターのスイープ

Computer Vision モデルをトレーニングする際のモデルのパフォーマンスは、選択したハイパーパラメーターの値に大きく依存します。 最適なパフォーマンスを得るために、ハイパーパラメーターを調整する必要がある場合がよくあります。
自動 ML の Computer Vision タスクのサポートにより、ハイパーパラメーターをスイープして、モデルに最適な設定を見つけることができます。 この機能は、Azure Machine Learning のハイパーパラメーター調整機能に該当します。 [ハイパーパラメーターを調整する方法を確認してください](how-to-tune-hyperparameters.md)。

### <a name="define-the-parameter-search-space"></a>パラメーター検索空間を定義する

パラメーター空間でスイープするモデル アルゴリズムとハイパーパラメーターを定義できます。 各タスクの種類でサポートされているモデル アルゴリズムとハイパーパラメーターの一覧については、「[モデル アルゴリズムとハイパーパラメーターを構成する](#configure-model-algorithms-and-hyperparameters)」を参照してください。 [不連続および連続のハイパーパラメーターでサポートされているディストリビューションの詳細](how-to-tune-hyperparameters.md#define-the-search-space)を参照してください。


### <a name="sampling-methods-for-the-sweep"></a>スイープのサンプリング方法

ハイパーパラメーターをスイープする場合は、定義されたパラメーター空間のスイープに使用するサンプリング方法を指定する必要があります。 現在のところ、`hyperparameter_sampling` パラメーターで次のサンプリング方法がサポートされています。

* [ランダム サンプリング](how-to-tune-hyperparameters.md#random-sampling)
* [グリッド サンプリング](how-to-tune-hyperparameters.md#grid-sampling) 
* [ベイジアン サンプリング](how-to-tune-hyperparameters.md#bayesian-sampling) 
    
現在、ランダム サンプリングでのみ条件付きハイパーパラメーター空間がサポートされていることに注意してください


### <a name="early-termination-policies"></a>早期終了ポリシー

早期終了ポリシーによって、パフォーマンスの低い実行を自動的に終了できます。 早期終了によって、コンピューティング効率が向上し、そうでなければ、あまり見込みのない構成で費やされてしまうコンピューティング リソースを節約できます。 画像の自動 ML では、`early_termination_policy` パラメーターを使用した次の早期終了ポリシーがサポートされています。 終了ポリシーが指定されていない場合は、すべての構成が完了するまで実行されます。

* [バンディット ポリシー](how-to-tune-hyperparameters.md#bandit-policy)
* [中央値の停止ポリシー](how-to-tune-hyperparameters.md#median-stopping-policy)
* [切り捨て選択ポリシー](how-to-tune-hyperparameters.md#truncation-selection-policy)

[ハイパーパラメーター スイープの早期終了ポリシーを構成する方法](how-to-tune-hyperparameters.md#early-termination)の詳細を確認してください。

### <a name="resources-for-the-sweep"></a>スイープのリソース

ハイパーパラメーター スイープに費やされるリソースを制御するには、スイープに `iterations` および `max_concurrent_iterations` を指定します。

パラメーター | 詳細
-----|----
`iterations` |  スイープする構成の最大数に必要なパラメーター。 1 ～ 1000 の整数にする必要があります。 特定のモデル アルゴリズムで既定のハイパーパラメーターだけを探索する場合は、このパラメーターを 1 に設定します。
`max_concurrent_iterations`| 同時に実行できる実行の最大数。 指定しない場合、すべての実行が並列で起動されます。 指定する場合は、1 ～ 100 の整数にする必要があります。  <br><br> **注:** 同時実行の数は、指定したコンピューティング ターゲットで使用可能なリソースに基づいて制御されます。 目的の同時実行可能性のために、使用可能なリソースをコンピューティング先に確保する必要があります。


> [!NOTE]
> スイープ構成の完全なサンプルについては、この[チュートリアル](tutorial-auto-train-image-models.md#hyperparameter-sweeping-for-image-tasks)を参照してください。

### <a name="arguments"></a>引数

パラメーター空間スイープ中に変更されない固定の設定またはパラメーターを引数として渡すことができます。 引数は名前と値のペアで渡され、名前の先頭に長鎖線を付ける必要があります。 

```python
from azureml.train.automl import AutoMLImageConfig
arguments = ["--early_stopping", 1, "--evaluation_frequency", 2]
automl_image_config = AutoMLImageConfig(arguments=arguments)
```

## <a name="submit-the-run"></a>実行の送信

`AutoMLImageConfig` オブジェクトの準備が整ったら、実験を送信できます。

```python
ws = Workspace.from_config()
experiment = Experiment(ws, "Tutorial-automl-image-object-detection")
automl_image_run = experiment.submit(automl_image_config)
```
## <a name="outputs-and-evaluation-metrics"></a>出力と評価のメトリック

AutoML トレーニングの実行によって、出力モデル ファイル、評価メトリック、ログ、およびスコアリング ファイルや環境ファイルなどのデプロイ成果物が生成され、それらは子実行の出力およびログおよびメトリック タブから表示できます。

> [!TIP]
> 「[実行結果の表示](how-to-understand-automated-ml.md#view-run-results)」セクションから実行結果に移動する方法を確認してください。

実行のたびに提供されるパフォーマンス グラフおよびメトリックの定義と例については、「[自動化機械学習実験の結果を評価する](how-to-understand-automated-ml.md#metrics-for-image-models-preview)」を参照してください

## <a name="register-and-deploy-model"></a>モデルを登録して展開する

実行が完了したら、最適な実行 (最適な主要メトリックになった構成) から作成されたモデルを登録できます

```Python
best_child_run = automl_image_run.get_best_child()
model_name = best_child_run.properties['model_name']
model = best_child_run.register_model(model_name = model_name, model_path='outputs/model.pt')
```

使用するモデルを登録したら、それを [Azure Container Instances (ACI)](how-to-deploy-azure-container-instance.md) または [Azure Kubernetes Service (AKS)](how-to-deploy-azure-kubernetes-service.md) に Web サービスとしてデプロイできます。 ACI はデプロイをテストするための最適なオプションであるのに対し、AKS は高スケールの運用環境での使用により適しています。

この例では、AKS に Web サービスとしてモデルをデプロイします。 AKS にデプロイするには、まず AKS コンピューティング クラスターを作成するか、既存の AKS クラスターを使用します。 デプロイ クラスターには、GPU または CPU VM SKU のいずれかを使用できます。 

```python

from azureml.core.compute import ComputeTarget, AksCompute
from azureml.exceptions import ComputeTargetException

# Choose a name for your cluster
aks_name = "cluster-aks-gpu"

# Check to see if the cluster already exists
try:
    aks_target = ComputeTarget(workspace=ws, name=aks_name)
    print('Found existing compute target')
except ComputeTargetException:
    print('Creating a new compute target...')
    # Provision AKS cluster with GPU machine
    prov_config = AksCompute.provisioning_configuration(vm_size="STANDARD_NC6", 
                                                        location="eastus2")
    # Create the cluster
    aks_target = ComputeTarget.create(workspace=ws, 
                                      name=aks_name, 
                                      provisioning_configuration=prov_config)
    aks_target.wait_for_completion(show_output=True)
```

次に、モデルを含む Web サービスを設定する方法を説明する推論構成を定義できます。 推論構成では、スコアリング スクリプトとトレーニング実行の環境を使用できます。

```python
from azureml.core.model import InferenceConfig

best_child_run.download_file('outputs/scoring_file_v_1_0_0.py', output_file_path='score.py')
environment = best_child_run.get_environment()
inference_config = InferenceConfig(entry_script='score.py', environment=environment)
```

その後、AKS Web サービスとしてモデルをデプロイできます。

```python
# Deploy the model from the best run as an AKS web service
from azureml.core.webservice import AksWebservice
from azureml.core.webservice import Webservice
from azureml.core.model import Model
from azureml.core.environment import Environment

aks_config = AksWebservice.deploy_configuration(autoscale_enabled=True,                                                    
                                                cpu_cores=1,
                                                memory_gb=50,
                                                enable_app_insights=True)

aks_service = Model.deploy(ws,
                           models=[model],
                           inference_config=inference_config,
                           deployment_config=aks_config,
                           deployment_target=aks_target,
                           name='automl-image-test',
                           overwrite=True)
aks_service.wait_for_deployment(show_output=True)
print(aks_service.state)
```

または、[Azure Machine Learning スタジオ UI](https://ml.azure.com/) からモデルをデプロイすることもできます。 自動 ML 実行の **[モデル]** タブで、デプロイするモデルに移動し、**[デプロイ]** を選択します。  

![スタジオ UI で自動 ML の実行からモデルを選択する  ](./media/how-to-auto-train-image-models/select-model.png)

**[モデルのデプロイ]** ウィンドウでモデルのデプロイに使用するモデル デプロイ エンドポイント名と推論クラスターを構成できます。

![構成のデプロイ](./media/how-to-auto-train-image-models/deploy-image-model.png)

### <a name="update-inference-configuration"></a>推論構成を更新する

前の手順では、スコアリング ファイル `outputs/scoring_file_v_1_0_0.py` を最適なモデルからローカル `score.py` ファイルにダウンロードし、それを使用して `InferenceConfig` オブジェクトを作成しました。 このスクリプトは、ダウンロードして `InferenceConfig` を作成する前に、必要に応じてモデル固有の推論設定を変更するために変更できます。 たとえば、次は、スコアリング ファイルのモデルを初期化するコード セクションです。
    
```
...
def init():
    ...
    try:
        logger.info("Loading model from path: {}.".format(model_path))
        model_settings = {...}
        model = load_model(TASK_TYPE, model_path, **model_settings)
        logger.info("Loading successful.")
    except Exception as e:
        logging_utilities.log_traceback(e, logger)
        raise
...
```

各タスク (および一部のモデル) では、`model_settings` ディクショナリ内に一連のパラメーターがあります。 既定で、トレーニングおよび検証時に使用されたパラメーターに同じ値を使用します。 推論にモデルを使用するときに必要な動作に応じて、これらのパラメーターを変更できます。 各タスクの種類とモデルのパラメーターの一覧を次に示します。  

| タスク | パラメーター名 | Default  |
|--------- |------------- | --------- |
|画像分類 (複数クラスおよび複数ラベル) | `valid_resize_size`<br>`valid_crop_size` | 256<br>224 |
|物体検出、インスタンスのセグメント化| `min_size`<br>`max_size`<br>`box_score_thresh`<br>`box_nms_thresh`<br>`box_detections_per_img` | 600<br>1333<br>0.3<br>0.5<br>100 |
|`yolov5` を使用した物体検出| `img_size`<br>`model_size`<br>`box_score_thresh`<br>`box_iou_thresh` | 640<br>中<br>0.1<br>0.5 |

これらのパラメーターの詳細については、[タスク固有のハイパーパラメーター](#task-specific-hyperparameters)に関する上記のセクションを参照してください。
    
タイルを使用し、タイルの動作を制御する場合は、パラメーター `tile_grid_size`、`tile_overlap_ratio`、`tile_predictions_nms_thresh` を使用できます。 これらのパラメーターの詳細については、[AutoML を使用した小さな物体検出モデルのトレーニング](how-to-use-automl-small-object-detect.md)に関するページを確認してください。

## <a name="example-notebooks"></a>サンプルの Notebook
[GitHub の自動機械学習サンプルのノートブック リポジトリ](https://github.com/Azure/azureml-examples/tree/main/python-sdk/tutorials/automl-with-azureml)で詳しいコード サンプルやユース ケースを確認してください。 Computer Vision モデルの構築に固有のサンプルについては、"image-" プレフィックスが付いたフォルダーを確認してください。


## <a name="next-steps"></a>次のステップ

* [チュートリアル: AutoML と Python を使用して物体検出モデル (プレビュー) をトレーニングする](tutorial-auto-train-image-models.md)。
* [自動 ML 実験のトラブルシューティング](how-to-troubleshoot-auto-ml.md)。