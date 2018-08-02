---
title: Azure Machine Learning Package for Computer Vision を使用してオブジェクト検出モデルを構築してデプロイする
description: Azure Machine Learning Package for Computer Vision を使用して、コンピューター ビジョンのオブジェクト検出モデルの構築、トレーニング、テスト、およびデプロイを行う方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: netahw
author: nhaiby
ms.date: 06/01/2018
ms.openlocfilehash: 44059de5a0ef0667b4268d9cdc2997162bab474a
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/27/2018
ms.locfileid: "39295371"
---
# <a name="build-and-deploy-object-detection-models-with-azure-machine-learning"></a>Azure Machine Learning を使用してオブジェクト検出モデルを構築してデプロイする

この記事では、**Azure Machine Learning Package for Computer Vision** を使用して、[Faster R-CNN](https://arxiv.org/abs/1506.01497) オブジェクト検出モデルのトレーニング、テスト、およびデプロイを行う方法について説明します。 

コンピューター ビジョンの分野における多数の問題は、オブジェクト検出を使用して解決できます。 こうした問題には、画像上の可変数のオブジェクトを見つけるモデルの構築などがあります。 

このモデルをこのパッケージを使用して構築してデプロイする場合は、次の手順に従います。
1.  データセットの作成
2.  ディープ ニューラル ネットワーク (DNN) モデルの定義
3.  モデル トレーニング
4.  評価と視覚化
5.  Web サービスのデプロイ
6.  Web サービスのロード テスト

この例では、ディープ ラーニング フレームワークとして TensorFlow を使用し、[ディープ ラーニング データ サイエンス VM](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.dsvm-deep-learning?tab=Overview) などの GPU を備えたマシンでトレーニングをローカルに実行し、デプロイに Azure ML Operationalization CLI を使用します。

各モジュールとクラスの詳細なリファレンスについては、[パッケージ リファレンス ドキュメント](https://aka.ms/aml-packages/vision)を参照してください、

## <a name="prerequisites"></a>前提条件

1. Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

1. 次のアカウントとアプリケーションを設定してインストールする必要があります。
   - Azure Machine Learning 実験アカウント 
   - Azure Machine Learning モデル管理アカウント
   - Azure Machine Learning Workbench のインストール

   これら 3 つが作成またはインストールされていない場合は、[Azure Machine Learning のクイックスタートとワークベンチのインストール](../service/quickstart-installation.md)に関する記事に従ってください。 

1. Azure Machine Learning Package for Computer Vision をインストールする必要があります。 このパッケージのインストール方法については、[こちら](https://aka.ms/aml-packages/vision)を参照してください。

## <a name="sample-data-and-notebook"></a>サンプル データと Notebook

### <a name="get-the-jupyter-notebook"></a>Jupyter Notebook を取得する

ここで説明するサンプルを実行するための Notebook をダウンロードします。

> [!div class="nextstepaction"]
> [Jupyter Notebook を取得する](https://aka.ms/aml-packages/vision/notebooks/object_detection)

### <a name="load-the-sample-data"></a>サンプル データセットを読み込む

このデモでは、30 個の画像と 8 個のクラス (eggBox、joghurt、ketchup、mushroom、mustard、orange、squash、water) で構成される、冷蔵庫内の食料品のデータセットが用意されています。 各 jpg 画像には、同じような名前が付いた注釈 xml ファイルがあります。 

次の図は、推奨されるフォルダー構造を示しています。 

![フォルダー構造](media/how-to-build-deploy-object-detection-models/data_directory.JPG)

## <a name="image-annotation"></a>画像の注釈

注釈を付けたオブジェクトの位置は、オブジェクト検出器をトレーニングおよび評価するために必要です。 [LabelImg](https://tzutalin.github.io/labelImg) は画像に注釈を付けるために使用できるオープン ソースの注釈ツールです。 LabelImg は、画像ごとに Pascal-VOC 形式の xml ファイルを作成します。作成されたファイルは、このパッケージで読み取ることができます。 


```python
import warnings
warnings.filterwarnings("ignore")
import os, time
from cvtk.core import Context, ObjectDetectionDataset, TFFasterRCNN
from cvtk.evaluation import DetectionEvaluation
from cvtk.evaluation.evaluation_utils import graph_error_counts
from cvtk.utils import detection_utils

# Disable printing of logging messages
from azuremltkbase.logging import ToolkitLogger
ToolkitLogger.getInstance().setEnabled(False)

from matplotlib import pyplot as plt
# Display the images
%matplotlib inline
```

## <a name="create-a-dataset"></a>データセットを作成する

一連の画像とそれぞれの境界ボックスの注釈で構成された CVTK データセットを作成します。 この例では、"../sample_data/foods/training" フォルダーにある冷蔵庫の画像を使用します。 JPEG 画像のみがサポートされています。


```python
image_folder = "detection/sample_data/foods/train"
data_train = ObjectDetectionDataset.create_from_dir(dataset_name='training_dataset', data_dir=image_folder,
                                                    annotations_dir="Annotations", image_subdirectory='JPEGImages')

# Show some statistics of the training image, and also give one example of the ground truth rectangle annotations
data_train.print_info()
_ = data_train.images[2].visualize_bounding_boxes(image_size = (10,10))
```

    F1 2018-05-25 23:12:21,727 INFO azureml.vision:machine info {"is_dsvm": true, "os_type": "Windows"} 
    F1 2018-05-25 23:12:21,733 INFO azureml.vision:dataset creating dataset for scenario=detection 
    Dataset name: training_dataset
    Total classes: 8, total images: 25
    Label-wise object counts:
        Label eggBox: 20 objects
        Label joghurt: 20 objects
        Label ketchup: 20 objects
        Label mushroom: 20 objects
        Label mustard: 20 objects
        Label orange: 20 objects
        Label squash: 40 objects
        Label water: 20 objects
    Bounding box width and height distribution:
        Bounding box widths  0/5/25/50/75/95/100-percentile: 54/61/79/117/133/165/311 pixels
        Bounding box heights 0/5/25/50/75/95/100-percentile: 48/58/75/124/142/170/212 pixels
    


![png](media/how-to-build-deploy-object-detection-models/output_6_1.JPG)


## <a name="define-a-model"></a>モデルを定義する

この例では、Faster R-CNN モデルを使用します。 このモデルを定義する際に、さまざまなパラメーターを指定できます。 これらのパラメーターの意味のほか、トレーニングに使用するパラメーター (次のセクションを参照) については、CVTK の API ドキュメントまたは [Tensorflow のオブジェクト検出 Web サイト](https://github.com/tensorflow/models/tree/master/research/object_detection)を参照してください。 Faster R-CNN モデルの詳細については、[こちらのリンク先](https://docs.microsoft.com/en-us/cognitive-toolkit/Object-Detection-using-Faster-R-CNN#technical-details)を参照してください。 このモデルは Fast R-CNN が基になっています。詳細については、[こちら](https://docs.microsoft.com/en-us/cognitive-toolkit/Object-Detection-using-Fast-R-CNN#algorithm-details)を参照してください。


```python
score_threshold = 0.0       # Threshold on the detection score, use to discard lower-confidence detections.
max_total_detections = 300  # Maximum number of detections. A high value slows down training but might increase accuracy.
my_detector = TFFasterRCNN(labels=data_train.labels, 
                           score_threshold=score_threshold, 
                           max_total_detections=max_total_detections)
```

## <a name="train-the-model"></a>モデルをトレーニングする

トレーニングの出発点として、ResNet50 を使って COCO でトレーニングした Faster R-CNN モデルを使用します。 

検出器をトレーニングするために、コードのトレーニング ステップ数を 350 に設定します。そのため、トレーニングはより短時間で実行されます (GPU を使用した場合に約 5 分)。 実際には、トレーニング セットの画像の数の 10 倍以上に設定します。

この例では、短時間でトレーニングするために、検出器のトレーニング ステップ数を 350 に設定します。 しかし、実際には、ステップ数をトレーニング セットの画像の数の 10 倍以上に設定することをお勧めします。

トレーニングの重要なパラメーターは次の 2 つです。
- モデルをトレーニングするためのステップの数。num_seps 引数で表されます。 各ステップでは、バッチ サイズ 1 のミニバッチを使用してモデルをトレーニングします。
- 学習率。initial_learning_rate で設定できます。

```python
print("tensorboard --logdir={}".format(my_detector.train_dir))

# to get good results, use a larger value for num_steps, e.g., 5000.
num_steps = 350
learning_rate = 0.001 # learning rate

start_train = time.time()
my_detector.train(dataset=data_train, num_steps=num_steps, 
                  initial_learning_rate=learning_rate)
end_train = time.time()
print(end_train-start_train)
```

    tensorboard --logdir=C:\Users\lixun\Desktop\AutoDL\CVTK\Src\API\cvtk_output\temp_faster_rcnn_resnet50\models\train
    F1 2018-05-25 23:12:22,764 INFO azureml.vision:Fit starting in experiment  1125722225 
    F1 2018-05-25 23:12:22,767 INFO azureml.vision:model starting trainging for scenario=detection 
    Using existing checkpoint file that's saved at 'C:\Users\lixun\Desktop\AutoDL\CVTK\Src\API\cvtk_output\models\detection\faster_rcnn_resnet50_coco_2018_01_28\model.ckpt.index'.
    TFRecords creation started.
    F1 2018-05-25 23:12:22,773 INFO On image 0 of 25
    TFRecords creation completed.
    Training started.
    Training progressing: step 0 ...
    Training progressing: step 100 ...
    Training progressing: step 200 ...
    Training progressing: step 300 ...
    F1 2018-05-25 23:18:02,730 INFO Graph Rewriter optimizations enabled
    Converted 275 variables to const ops.
    F1 2018-05-25 23:18:10,722 INFO 2953 ops in the final graph.
    F1 2018-05-25 23:18:24,244 INFO azureml.vision:Fit finished in experiment  1125722225 
    Training completed.
    361.604615688324
    

TensorBoard を使用して、トレーニングの進行状況を視覚化できます。 TensorBoard イベントは、モデル オブジェクトの train_dir 属性で指定されたフォルダーに格納されます。 TensorBoard を表示するには、次の手順を実行します。
1. "tensorboard --logdir" で始まる出力をコマンド ラインにコピーして実行します。 
2. コマンド ラインから返された URL を Web ブラウザーにコピーして、TensorBoard を表示します。 

TensorBoard は次のスクリーンショットのようになります。 トレーニング フォルダーにイベントが格納されるまでにはしばらく時間がかかります。 そのため、初めてで TensorBoard が正しく表示されない場合は、手順 1 と 2 を繰り返してください。  

![TensorBoard](media/how-to-build-deploy-object-detection-models/tensorboard.JPG)

## <a name="evaluate-the-model"></a>モデルを評価する

モデルの評価には、"evaluate" メソッドを使用します。 この関数には、入力として ObjectDetectionDataset オブジェクトが必要です。 トレーニング データセットに使用したのと同じ関数を使用して、評価データセットを作成できます。 サポートされているメトリックは [PASCAL VOC Challenge](http://host.robots.ox.ac.uk/pascal/VOC/pubs/everingham10.pdf) で定義されている Average Precision です。  


```python
image_folder = "detection/sample_data/foods/test"
data_val = ObjectDetectionDataset.create_from_dir(dataset_name='val_dataset', data_dir=image_folder)
eval_result = my_detector.evaluate(dataset=data_val)
```

    F1 2018-05-25 23:18:24,253 INFO azureml.vision:dataset creating dataset for scenario=detection 
    F1 2018-05-25 23:18:24,286 INFO On image 0 of 5
    F1 2018-05-25 23:18:29,300 INFO Starting evaluation at 2018-05-26-03:18:29
    F1 2018-05-25 23:18:32,403 INFO Creating detection visualizations.
    F1 2018-05-25 23:18:33,158 INFO Detection visualizations written to summary with tag image-0.
    F1 2018-05-25 23:18:33,518 INFO Creating detection visualizations.
    F1 2018-05-25 23:18:34,342 INFO Detection visualizations written to summary with tag image-1.
    F1 2018-05-25 23:18:34,714 INFO Creating detection visualizations.
    F1 2018-05-25 23:18:35,470 INFO Detection visualizations written to summary with tag image-2.
    F1 2018-05-25 23:18:35,835 INFO Creating detection visualizations.
    F1 2018-05-25 23:18:36,654 INFO Detection visualizations written to summary with tag image-3.
    F1 2018-05-25 23:18:37,010 INFO Creating detection visualizations.
    F1 2018-05-25 23:18:37,798 INFO Detection visualizations written to summary with tag image-4.
    F1 2018-05-25 23:18:37,804 INFO Running eval batches done.
    F1 2018-05-25 23:18:37,805 INFO # success: 5
    F1 2018-05-25 23:18:37,806 INFO # skipped: 0
    F1 2018-05-25 23:18:38,119 INFO Writing metrics to tf summary.
    F1 2018-05-25 23:18:38,121 INFO PASCAL/PerformanceByCategory/AP@0.5IOU/eggBox: 1.000000
    F1 2018-05-25 23:18:38,205 INFO PASCAL/PerformanceByCategory/AP@0.5IOU/joghurt: 0.942857
    F1 2018-05-25 23:18:38,206 INFO PASCAL/PerformanceByCategory/AP@0.5IOU/ketchup: 1.000000
    F1 2018-05-25 23:18:38,207 INFO PASCAL/PerformanceByCategory/AP@0.5IOU/mushroom: 1.000000
    F1 2018-05-25 23:18:38,208 INFO PASCAL/PerformanceByCategory/AP@0.5IOU/mustard: 1.000000
    F1 2018-05-25 23:18:38,209 INFO PASCAL/PerformanceByCategory/AP@0.5IOU/orange: 1.000000
    F1 2018-05-25 23:18:38,210 INFO PASCAL/PerformanceByCategory/AP@0.5IOU/squash: 1.000000
    F1 2018-05-25 23:18:38,211 INFO PASCAL/PerformanceByCategory/AP@0.5IOU/water: 1.000000
    F1 2018-05-25 23:18:38,211 INFO PASCAL/Precision/mAP@0.5IOU: 0.992857
    F1 2018-05-25 23:18:38,253 INFO Metrics written to tf summary.
    F1 2018-05-25 23:18:38,254 INFO Finished evaluation!
    

評価結果はきれいな書式で出力できます。


```python
# print out the performance metric values
for label_obj in data_train.labels:
    label = label_obj.name
    key = 'PASCAL/PerformanceByCategory/AP@0.5IOU/' + label
    print('{0: <15}: {1: <3}'.format(label, round(eval_result[key], 2)))
print('{0: <15}: {1: <3}'.format("overall:", round(eval_result['PASCAL/Precision/mAP@0.5IOU'], 2))) 
```

    joghurt        : 0.94
    squash         : 1.0
    mushroom       : 1.0
    eggBox         : 1.0
    ketchup        : 1.0
    mustard        : 1.0
    water          : 1.0
    orange         : 1.0
    overall:       : 0.99
    

同様に、トレーニング セットに対するモデルの精度を計算することもできます。 これを行うと、トレーニングが優れたソリューションに収束していることを確認するのに役立ちます。 トレーニング成功後のトレーニング セットに対する精度は、多くの場合、100% に近くなります。

mAP 値や予測された境界ボックス付きの画像を含め、評価結果も TensorBoard から確認できます。 次のコードの出力をコマンド ライン ウィンドウにコピーして、TensorBoard クライアントを起動します。 ここでは、トレーニングの状態を表示するために使用していたポートの既定値である 6006 との競合を避けるために、値 8008 を使用します。


```python
print("tensorboard --logdir={} --port=8008".format(my_detector.eval_dir))
```

    tensorboard --logdir=C:\Users\lixun\Desktop\AutoDL\CVTK\Src\API\cvtk_output\temp_faster_rcnn_resnet50\models\eval --port=8008
    

## <a name="score-an-image"></a>画像にスコアを付ける

トレーニング済みのモデルのパフォーマンスに満足したら、モデル オブジェクトの "score" 関数を使用して、新しい画像にスコアを付けることができます。 返されたスコアは "visualize" 関数を使用して視覚化できます。 


```python
image_path = data_val.images[1].storage_path
detections_dict = my_detector.score(image_path)
path_save = "./scored_images/scored_image_preloaded.jpg"
ax = detection_utils.visualize(image_path, detections_dict, image_size=(8, 12))
path_save_dir = os.path.dirname(os.path.abspath(path_save))
os.makedirs(path_save_dir, exist_ok=True)
ax.get_figure().savefig(path_save)
```

![png](media/how-to-build-deploy-object-detection-models/output_20_0.JPG)

##  <a name="save-the-model"></a>モデルを保存する

次のコード例に示すように、トレーニング済みのモデルをディスクに保存し、メモリに読み込み直すことができます。


```python
save_model_path = "./frozen_model/faster_rcnn.model"
my_detector.save(save_model_path)
```

    F1 2018-05-25 23:18:55,166 INFO Graph Rewriter optimizations enabled
    Converted 275 variables to const ops.
    F1 2018-05-25 23:19:03,867 INFO 2953 ops in the final graph.
    

## <a name="load-the-saved-model-for-scoring"></a>スコア付けのために保存されたモデルを読み込む

保存されたモデルを使用するには、"load" 関数を使用してモデルをメモリに読み込みます。 一度だけ読み込む必要があります。 

```python
my_detector_loaded = TFFasterRCNN.load(save_model_path)
```

モデルが読み込まれたら、そのモデルを使用して、画像または画像の一覧にスコアを付けることができます。 単一の画像の場合、"detect_boxes"、"detect_scores"、"num_detections" などのキーを含むディクショナリが返されます。 入力が画像の一覧の場合、1 つのディクショナリが 1 つの画像に対応したディクショナリの一覧が返されます。 


```python
detections_dict = my_detector_loaded.score(image_path)
```

スコアが 0.5 を超える検出されたオブジェクトと、ラベル、スコア、座標を出力できます。


```python
look_up = dict((v,k) for k,v in my_detector.class_map.items())
n_obj = 0
for i in range(detections_dict['num_detections']):
    if detections_dict['detection_scores'][i] > 0.5:
        n_obj += 1
        print("Object {}: label={:11}, score={:.2f}, location=(top: {:.2f}, left: {:.2f}, bottom: {:.2f}, right: {:.2f})".format(
            i, look_up[detections_dict['detection_classes'][i]], 
            detections_dict['detection_scores'][i], 
            detections_dict['detection_boxes'][i][0],
            detections_dict['detection_boxes'][i][1], 
            detections_dict['detection_boxes'][i][2],
            detections_dict['detection_boxes'][i][3]))    
        
print("\nFound {} objects in image {}.".format(n_obj, image_path))           
```

    Object 0: label=squash     , score=0.99, location=(top: 0.74, left: 0.30, bottom: 0.84, right: 0.42)
    Object 1: label=squash     , score=0.98, location=(top: 0.27, left: 0.21, bottom: 0.37, right: 0.33)
    Object 2: label=orange     , score=0.98, location=(top: 0.31, left: 0.39, bottom: 0.37, right: 0.48)
    Object 3: label=joghurt    , score=0.98, location=(top: 0.57, left: 0.29, bottom: 0.67, right: 0.39)
    Object 4: label=eggBox     , score=0.97, location=(top: 0.41, left: 0.53, bottom: 0.49, right: 0.69)
    Object 5: label=water      , score=0.95, location=(top: 0.23, left: 0.51, bottom: 0.37, right: 0.57)
    Object 6: label=mustard    , score=0.88, location=(top: 0.61, left: 0.47, bottom: 0.66, right: 0.57)
    Object 7: label=ketchup    , score=0.80, location=(top: 0.62, left: 0.62, bottom: 0.68, right: 0.72)
    
    Found 8 objects in image ../sample_data/foods/test\JPEGImages\10.jpg.
    

前と同じようにスコアを視覚化します。


```python
path_save = "./scored_images/scored_image_frozen_graph.jpg"
ax = detection_utils.visualize(image_path, detections_dict, path_save=path_save, image_size=(8, 12))
# ax.get_figure() # use this code extract the returned image
```

![png](media/how-to-build-deploy-object-detection-models/output_30_0.JPG)

## <a name="operationalization-deploy-and-consume"></a>運用化: デプロイして使用する

運用化は、モデルとコードを Web サービスとして公開し、これらのサービスを使用して、ビジネスの結果を生み出すプロセスです。 

モデルのトレーニングが終わったら、そのモデルを [Azure Machine Learning CLI](https://docs.microsoft.com/azure/machine-learning/desktop-workbench/cli-for-azure-machine-learning) を使用して Web サービスとしてデプロイできます。 モデルは、ローカル コンピューターまたは Azure Container Service (ACS) クラスターに展開できます。 ACS では、Web サービスを手動で、または自動スケール機能を使用してスケーリングできます。

**Azure CLI を使用してサインインする**

[Azure](https://azure.microsoft.com/) アカウントの有効なサブスクリプションを使用して、次の CLI コマンドでログインします。
<br>`az login`

+ 別の Azure サブスクリプションに切り替えるには、次のコマンドを使用します。
<br>`az account set --subscription [your subscription name]`

+ 現在のモデル管理アカウントを表示するには、次のコマンドを使用します。
  <br>`az ml account modelmanagement show`

**クラスター デプロイ環境を作成してセットアップする**

配置環境のセットアップは 1 度だけ行う必要があります。 配置環境がまだない場合は、[こちらの手順](https://docs.microsoft.com/azure/machine-learning/desktop-workbench/deployment-setup-configuration#environment-setup)を使用してセットアップします。 

アクティブな配置環境を表示するには、するには、次の CLI コマンドを使用します。
<br>`az ml env show`
   
配置環境を作成してセットアップするサンプル Azure CLI コマンド

```CLI
az provider register -n Microsoft.MachineLearningCompute
az provider register -n Microsoft.ContainerRegistry
az provider register -n Microsoft.ContainerService
az ml env setup --cluster -n [your environment name] -l [Azure region e.g. westcentralus] [-g [resource group]]
az ml env set -n [environment name] -g [resource group]
az ml env cluster
```
    
### <a name="manage-web-services-and-deployments"></a>Web サービスの管理とデプロイ

次の API を使用して、モデルを Web サービスとして配置し、それらの Web サービスの管理とデプロイを管理できます。

|タスク|API|
|----|----|
|デプロイ オブジェクトの作成|`deploy_obj = AMLDeployment(deployment_name=deployment_name, associated_DNNModel=dnn_model, aml_env="cluster")`
|Web サービスのデプロイ|`deploy_obj.deploy()`|
|画像のスコア付け|`deploy_obj.score_image(local_image_path_or_image_url)`|
|Web サービスの削除|`deploy_obj.delete()`|
|Web サービスの Docker イメージなしの構築|`deploy_obj.build_docker_image()`|
|既存のデプロイの一覧表示|`AMLDeployment.list_deployment()`|
|指定した名前のデプロイが存在する場合のサービスの削除|`AMLDeployment.delete_if_service_exist(deployment_name)`|

**API ドキュメント:** 各モジュールとクラスの詳細なリファレンスについては、[パッケージ リファレンス ドキュメント](https://aka.ms/aml-packages/vision)を参照してください。

**CLI リファレンス:** デプロイに関連する高度な操作については、[モデル管理 CLI リファレンス](https://docs.microsoft.com/azure/machine-learning/desktop-workbench/model-management-cli-reference)を参照してください。

**Azure Portal でのデプロイ管理**: [Azure Portal](https://ms.portal.azure.com/)でデプロイを追跡して管理できます。 Azure Portal で、その名前を使用して Machine Learning モデル管理アカウント ページを検索します。 モデル管理アカウント ページ > モデル管理 > サービスの順に移動します。

```python
# ##### OPTIONAL - Interactive CLI setup helper ###### 
# # Interactive CLI setup helper, including model management account and deployment environment.
# # If you haven't setup you CLI before or if you want to change you CLI settings, you can use this block to help you interactively.
# # UNCOMMENT THE FOLLOWING LINES IF YOU HAVE NOT CREATED OR SET THE MODEL MANAGEMENT ACCOUNT AND DEPLOYMENT ENVIRONMENT

# from azuremltkbase.deployment import CliSetup
# CliSetup().run()
```


```python
from cvtk.operationalization import AMLDeployment

# set deployment name
deployment_name = "wsdeployment"

# Create deployment object
# It will use the current deployment environment (you can check it with CLI command "az ml env show").
deploy_obj = AMLDeployment(deployment_name=deployment_name, aml_env="cluster", associated_DNNModel=my_detector, replicas=1)

# Alternatively, you can provide azure machine learning deployment cluster name (environment name) and resource group name
# to deploy your model. It will use the provided cluster to deploy. To do that, please uncomment the following lines to create 
# the deployment object.

# azureml_rscgroup = "<resource group>"
# cluster_name = "<cluster name>"
# deploy_obj = AMLDeployment(deployment_name=deployment_name, associated_DNNModel=my_detector,
#                            aml_env="cluster", cluster_name=cluster_name, resource_group=azureml_rscgroup, replicas=1)

# Check if the deployment name exists, if yes remove it first.
if deploy_obj.is_existing_service():
    AMLDeployment.delete_if_service_exist(deployment_name)
    
# create the webservice
print("Deploying to Azure cluster...")
deploy_obj.deploy()
print("Deployment DONE")
```

### <a name="consume-the-web-service"></a>Web サービスを使用する

Web サービスを作成したら、デプロイされた Web サービスを使用して画像にスコアを付けることができます。 いくつかのオプションがあります。

   - deploy_obj.score_image(image_path_or_url) を利用して、デプロイ オブジェクトを使って Web サービスに直接スコアを付けることができます。 
   - または、AMLDeployment.score_existing_service_with_image(image_path_or_url, service_endpoint_url, service_key=None) を利用して、サービス エンドポイント URL とサービス キー (ローカル デプロイの場合は None) を使うことができます。
   - Web サービス エンドポイントにスコアを付ける http 要求を直接作成します (上級ユーザー向け)。

### <a name="score-with-existing-deployment-object"></a>既存のデプロイ オブジェクトでスコア付けする
```
deploy_obj.score_image(image_path_or_url)
```


```python
# Score with existing deployment object

# Score local image with file path
print("Score local image with file path")
image_path_or_url = data_train.images[0].storage_path
print("Image source:",image_path_or_url)
serialized_result_in_json = deploy_obj.score_image(image_path_or_url, image_resize_dims=[224,224])
print("serialized_result_in_json:", serialized_result_in_json[:50])

# Score image url and remove image resizing
print("Score image url")
image_path_or_url = "https://cvtkdata.blob.core.windows.net/publicimages/microsoft_logo.jpg"
print("Image source:",image_path_or_url)
serialized_result_in_json = deploy_obj.score_image(image_path_or_url)
print("serialized_result_in_json:", serialized_result_in_json[:50])

```


```python
# Time image scoring
import timeit

num_images = 3
for img_index, img_obj in enumerate(data_train.images[:num_images]):
    print("Calling API for image {} of {}: {}...".format(img_index, num_images, img_obj.name))
    tic = timeit.default_timer()
    return_json = deploy_obj.score_image(img_obj.storage_path, image_resize_dims=[224,224])
    print("   Time for API call: {:.2f} seconds".format(timeit.default_timer() - tic))
```

### <a name="score-with-service-endpoint-url-and-service-key"></a>サービス エンドポイントの URL とサービス キーでスコア付けする
```
    AMLDeployment.score_existing_service_with_image(image_path_or_url, service_endpoint_url, service_key=None)
```


```python
# Import related classes and functions
from cvtk.operationalization import AMLDeployment

service_endpoint_url = "http://xxx" # please replace with your own service url
service_key = "xxx" # please replace with your own service key

# score image url
image_path_or_url = "https://cvtkdata.blob.core.windows.net/publicimages/microsoft_logo.jpg"
print("Image source:",image_path_or_url)
serialized_result_in_json = AMLDeployment.score_existing_service_with_image(image_path_or_url,service_endpoint_url, service_key = service_key, image_resize_dims=[224,224])
print("serialized_result_in_json:", serialized_result_in_json[:50])
```

### <a name="score-endpoint-with-http-request-directly"></a>エンドポイントを http 要求で直接スコア付けする
以下は、Python で http 要求を直接作成するコードの例です。 他のプログラミング言語で行うこともできます。


```python
def score_image_with_http(image, service_endpoint_url, service_key=None, parameters={}):
    """Score local image with http request

    Args:
        image (str): Image file path
        service_endpoint_url(str): web service endpoint url
        service_key(str): Service key. None for local deployment.
        parameters (dict): Additional request paramters in dictionary. Default is {}.


    Returns:
        str: serialized result 
    """
    import requests
    from io import BytesIO
    import base64
    import json

    if service_key is None:
        headers = {'Content-Type': 'application/json'}
    else:
        headers = {'Content-Type': 'application/json',
                   "Authorization": ('Bearer ' + service_key)}
    payload = []
    encoded = None
    
    # Read image
    with open(image,'rb') as f:
        image_buffer = BytesIO(f.read()) ## Getting an image file represented as a BytesIO object
        
    # Convert your image to base64 string
    # image_in_base64 : "b'{base64}'"
    encoded = base64.b64encode(image_buffer.getvalue())
    image_request = {"image_in_base64": "{0}".format(encoded), "parameters": parameters}
    payload.append(image_request)
    body = json.dumps(payload)
    r = requests.post(service_endpoint_url, data=body, headers=headers)
    try:
        result = json.loads(r.text)
        json.loads(result[0])
    except:
        raise ValueError("Incorrect output format. Result cant not be parsed: " + r.text)
    return result[0]

```

### <a name="parse-serialized-result-from-webservice"></a>Web サービスからのシリアル化された結果を解析する
Web サービスからの結果は解析可能な JSON 文字列です。


```python
image_path_or_url = image_path
print("Image source:",image_path_or_url)
serialized_result_in_json = deploy_obj.score_image(image_path_or_url)
print("serialized_result_in_json:", serialized_result_in_json[:50])
```


```python
# Parse result from json string
import numpy as np
parsed_result = TFFasterRCNN.parse_serialized_result(serialized_result_in_json)
print("Parsed result:", parsed_result)
```


```python
ax = detection_utils.visualize(image_path, parsed_result)
path_save = "./scored_images/scored_image_web.jpg"
path_save_dir = os.path.dirname(os.path.abspath(path_save))
os.makedirs(path_save_dir, exist_ok=True)
ax.get_figure().savefig(path_save)
```

## <a name="next-steps"></a>次の手順

次の記事で、Azure Machine Learning Package for Computer Vision の詳細を確認します。

+ [パッケージの概要とインストール方法](https://aka.ms/aml-packages/vision)を確認します。

+ パッケージの[リファレンス ドキュメント](https://docs.microsoft.com/python/api/overview/azure-machine-learning/computer-vision)を確認します。

+ [Azure Machine Learning 用の他の Python パッケージ](reference-python-package-overview.md)を確認します。
