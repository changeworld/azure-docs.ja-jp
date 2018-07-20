---
title: Machine Learning Package for Computer Vision を使用して画像分類モデルを構築して配置する
description: Azure Machine Learning Package for Computer Vision を使用して、コンピューター ビジョンの画像分類モデルの構築、トレーニング、およびテストを行う方法を説明します。
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: netahw
author: nhaiby
ms.date: 04/23/2018
ms.openlocfilehash: 6b7f73573cb1465b89e54e30894b3549153e4acb
ms.sourcegitcommit: 11321f26df5fb047dac5d15e0435fce6c4fde663
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/06/2018
ms.locfileid: "37888434"
---
# <a name="build-and-deploy-image-classification-models-with-azure-machine-learning"></a>Azure Machine Learning で画像分類モデルを構築して配置する

この記事では、**Azure Machine Learning Package for Computer Vision** を使用して、画像分類モデルのトレーニング、テスト、および展開を行う方法について説明します。 

コンピューター ビジョンの分野における多数の問題は、画像分類を使用して解決できます。 これらの問題には、以下のような質問に回答するモデルの構築が含まれます。
+ "_対象物が画像に存在するか。例: "犬"、"猫"、"船"など_"
+ "_この患者の網膜スキャンによって、どの程度の眼病の重症度が立証されるか。_"

このモデルを AMLPCV を使用して構築して配置するときは、次の手順に従います。
1. データセットの作成
2. 画像の視覚化と注釈
3. 画像の拡張
4. ディープ ニューラル ネットワーク (DNN) モデルの定義
5. 分類器のトレーニング
6. 評価と視覚化
7. Web サービスのデプロイ
8. Web サービスのロード テスト

ディープ ラーニング フレームワークとして [CNTK](https://www.microsoft.com/en-us/cognitive-toolkit/) が使用され、[ディープ ラーニング データ サイエンス VM](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.dsvm-deep-learning?tab=Overview) などの GPU を備えたマシンでトレーニングがローカルに実行され、展開では Azure ML Operationalization CLI が使用されます。

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
> [Jupyter Notebook を取得する](https://aka.ms/aml-packages/vision/notebooks/image_classification)

### <a name="load-the-sample-data"></a>サンプル データセットを読み込む

次の例では、63 個のテーブルウェアの画像で構成されるデータセットを使用します。 各画像には、4 つの異なるクラス (ボウル、カップ、カトラリー、プレート) のいずれかに属していることを示すラベルが付いています。 この例では、画像の数を少なくして、このサンプルを迅速に実行できるようにしています。 実際には、クラスごとに少なくとも 100 枚の画像を用意する必要があります。 すべての画像は、*"../sample_data/imgs_recycling/"* のサブディレクトリ ("bowl"、"cup"、"cutlery"、および "plate") に配置されます。

![Azure Machine Learning のデータセット](media/how-to-build-deploy-image-classification-models/recycling_examples.jpg)


```python
import warnings
warnings.filterwarnings("ignore")
import json, numpy as np, os, timeit 
from azureml.logging import get_azureml_logger
from imgaug import augmenters
from IPython.display import display
from sklearn import svm
from cvtk import ClassificationDataset, CNTKTLModel, Context, Splitter, StorageContext
from cvtk.augmentation import augment_dataset
from cvtk.core.classifier import ScikitClassifier
from cvtk.evaluation import ClassificationEvaluation, graph_roc_curve, graph_pr_curve, graph_confusion_matrix
import matplotlib.pyplot as plt

from classification.notebook.ui_utils.ui_annotation import AnnotationUI
from classification.notebook.ui_utils.ui_results_viewer import ResultsUI
from classification.notebook.ui_utils.ui_precision_recall import PrecisionRecallUI

%matplotlib inline

# Disable printing of logging messages
from azuremltkbase.logging import ToolkitLogger
ToolkitLogger.getInstance().setEnabled(False)
```



## <a name="create-a-dataset"></a>データセットを作成する

依存関係をインポートし、ストレージ コンテキストを設定した後、データセット オブジェクトを作成できます。

Azure Machine Learning Package for Computer Vision でそのオブジェクトを作成するには、ローカル ディスク上の画像のルート ディレクトリを指定します。 このディレクトリは、テーブルウェア データセットと同じ一般的な構造に従う必要があります。つまり、実際の画像が格納されたサブディレクトリを含める必要があります。
- root
    - ラベル 1
    - ラベル 2
    - ...
    - ラベル n
  
別のデータセットに対する画像分類モデルのトレーニングは、簡単に実行できます。次のコードのルート パス `dataset_location` を、別の画像を指すように変更するだけです。


```python
# Root image directory
dataset_location = os.path.abspath("classification/sample_data/imgs_recycling")

dataset_name = 'recycling'
dataset = ClassificationDataset.create_from_dir(dataset_name, dataset_location)
print("Dataset consists of {} images with {} labels.".format(len(dataset.images), len(dataset.labels)))
print("Select information for image 2: name={}, label={}, unique id={}.".format(
    dataset.images[2].name, dataset.images[2]._labels[0].name, dataset.images[2]._storage_id))
```

    F1 2018-04-23 17:12:57,593 INFO azureml.vision:machine info {"is_dsvm": true, "os_type": "Windows"} 
    F1 2018-04-23 17:12:57,599 INFO azureml.vision:dataset creating dataset for scenario=classification 
    Dataset consists of 63 images with 4 labels.
    Select information for image 2: name=msft-plastic-bowl20170725152154282.jpg, label=bowl, unique id=3.

データセット オブジェクトは、[Bing Image Search API](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/) を使用して画像をダウンロードする機能を提供します。 

2 種類の検索クエリがサポートされています。 
+ 通常のテキスト クエリ
+ 画像 URL クエリ

これらのクエリとクラス ラベルを、JSON でエンコードされたテキスト ファイル内に指定する必要があります。 例: 

```json
{
    "bowl": [
                    "plastic bowl",
                    "../imgs_recycling/bowl"
    ],
    "cup": [
                    "plastic cup",
                    "../imgs_recycling/cup",
                    "http://cdnimg2.webstaurantstore.com/images/products/main/123662/268841/dart-solo-ultra-clear-conex-tp12-12-oz-pet-plastic-cold-cup-1000-case.jpg"
    ],
    "cutlery": [
                    "plastic cutlery",
                    "../imgs_recycling/cutlery",
                    "http://img4.foodservicewarehouse.com/Prd/1900SQ/Fineline_2514-BO.jpg"
    ],
    "plate": [
                    "plastic plate",
                    "../imgs_recycling/plate"
    ]
}
```

さらに、Bing Image Search API キーを含むコンテキスト オブジェクトを明示的に作成する必要があります。 これには、Bing Image Search API サブスクリプションが必要です。

## <a name="visualize-and-annotate-images"></a>画像を視覚化して注釈を付ける

次のウィジェットを使用して、画像を視覚化し、データセット オブジェクト内のラベルを修正できます。 

"ウィジェット Javascript が検出されません" エラーが発生した場合は、次のコマンドを実行して解決してください。
<br>`jupyter nbextension enable --py --sys-prefix widgetsnbextension`


```python
annotation_ui = AnnotationUI(dataset, Context.get_global_context())
display(annotation_ui.ui)
```

![Azure Machine Learning のデータセット](media/how-to-build-deploy-image-classification-models/image_annotation.png)

## <a name="augment-images"></a>拡張画像

[`augmentation` モジュール](https://docs.microsoft.com/en-us/python/api/cvtk.augmentation)は、[imgaug](https://github.com/aleju/imgaug) ライブラリに記述されているすべての変換を使用してデータセット オブジェクトを拡張する機能を提供します。 画像の変換は、1 つのパイプラインに分類できます。この場合、パイプライン内のすべての変換は、各画像に同時に適用されます。 

異なる拡張手順を個別に適用するか、別の方法を使用する場合は、複数のパイプラインを定義して、*augment_dataset* 関数に渡すことができます。 画像の拡張の詳細と例については、[imgaug のドキュメント](https://github.com/aleju/imgaug)を参照してください。

トレーニング セットへの拡張イメージの追加は、小規模なデータセットで特に有用です。 DNN トレーニング プロセスは、トレーニング画像の数が増えるにつれて遅くなるため、拡張なしで実験を開始することをお勧めします。


```python
# Split the dataset into train and test  
train_set_orig, test_set = dataset.split(train_size = 0.66, stratify = "label")
print("Number of training images = {}, test images = {}.".format(train_set_orig.size(), test_set.size()))
```

    F1 2018-04-23 17:13:01,780 INFO azureml.vision:splitter splitting a dataset 
    F1 2018-04-23 17:13:01,805 INFO azureml.vision:dataset creating dataset for scenario=classification 
    F1 2018-04-23 17:13:01,809 INFO azureml.vision:dataset creating dataset for scenario=classification 
    Number of training images = 41, test images = 22.
    


```python
augment_train_set = False

if augment_train_set:
    aug_sequence = augmenters.Sequential([
            augmenters.Fliplr(0.5),             # horizontally flip 50% of all images
            augmenters.Crop(percent=(0, 0.1)),  # crop images by 0-10% of their height/width
        ])
    train_set = augment_dataset(train_set_orig, [aug_sequence])
    print("Number of original training images = {}, with augmented images included = {}.".format(train_set_orig.size(), train_set.size()))
else:
    train_set = train_set_orig  
```

## <a name="define-dnn-models"></a>DNN モデルを定義する

このパッケージでは、次のトレーニング済みのディープ ニューラル ネットワーク モデルがサポートされています。 
+ Resnet-18
+ Resnet-34
+ Resnet-50
+ Resnet-101
+ Resnet-152

これらの DNN を、分類器または特徴抽出器として使用できます。 

ネットワークの詳細については[こちら](https://github.com/Microsoft/CNTK/blob/master/PretrainedModels/Image.md)を、転移学習の概要については[こちら](https://blog.slavv.com/a-gentle-intro-to-transfer-learning-2c0b674375a0)を参照してください。

このパッケージの既定の画像分類パラメーターは、224 x 224 ピクセルの解像度と Resnet-18 DNN です。 これらのパラメーターは、さまざまなタスクで適切に動作させるために選択されています。 たとえば 画像解像度を 500 x 500 ピクセルに増やしたり、より深いモデル (Resnet-50) を選択したりすることで、多くの場合、精度を向上させることができます。 ただし、パラメーターを変更すると、トレーニング時間が大幅に増加する可能性があります。 [精度を向上させる方法](https://docs.microsoft.com/azure/machine-learning/service/how-to-improve-accuracy-for-computer-vision-models)に関する記事を参照してください。


```python
# Default parameters (224 x 224 pixels resolution, Resnet-18)
lr_per_mb = [0.05]*7 + [0.005]*7 +  [0.0005]
mb_size = 32
input_resoluton = 224
base_model_name = "ResNet18_ImageNet_CNTK"

# Suggested parameters for 500 x 500 pixels resolution, Resnet-50
# (see in the Appendix "How to improve accuracy", last row in table)
# lr_per_mb   = [0.01] * 7 + [0.001] * 7 + [0.0001]
# mb_size    = 8
# input_resoluton = 500
# base_model_name = "ResNet50_ImageNet_CNTK"

# Initialize model
dnn_model = CNTKTLModel(train_set.labels,
                       base_model_name=base_model_name,
                       image_dims = (3, input_resoluton, input_resoluton))
```

    Successfully downloaded ResNet18_ImageNet_CNTK
    

## <a name="train-the-classifier"></a>分類器をトレーニングする

事前トレーニング済みの DNN に対して、次のいずれかの方法を選択できます。

  - **DNN の調整**: DNN をトレーニングして、分類を直接実行します。 DNN のトレーニングは遅くなりますが、トレーニング中にすべてのネットワークの重みを改善して最高の精度を得ることができるため、通常は最良の結果に至ります。

  - **DNN の特徴抽出**: DNN をそのまま実行して、画像の低次元表現を取得します (512、2048、または 4096 の浮動小数点数)。 その表現が、別の分類器をトレーニングするための入力として使用されます。 DNN は変更されないため、この方法は DNN の調整に比べ、非常に高速で実行されますが、精度はそれほど高くありません。 それにもかかわらず、線形 SVM などの外部分類器のトレーニング (次のコードに示されています) は、強力なベースラインを提供でき、問題の実行可能性を理解するのに役立ちます。
  
TensorBoard を使用して、トレーニングの進行状況を視覚化できます。 TensorBoard をアクティブ化するには:
1. 次のコードに示されているように、`tensorboard_logdir=PATH` パラメーターを追加します。
1. 別のコンソールで `tensorboard --logdir=PATH` コマンドを使用して TensorBoard クライアントを起動します。
1. TensorBoard の指示に従って Web ブラウザーを開きます。既定値は localhost:6006 です。 


```python
# Train either the DNN or a SVM as classifier 
classifier_name = "dnn"

if classifier_name.lower() == "dnn":  
    dnn_model.train(train_set, lr_per_mb = lr_per_mb, mb_size = mb_size, eval_dataset=test_set) #, tensorboard_logdir=r"tensorboard"
    classifier = dnn_model
elif classifier_name.lower() == "svm":
    learner = svm.LinearSVC(C=1.0, class_weight='balanced', verbose=0)
    classifier = ScikitClassifier(dnn_model, learner = learner)
    classifier.train(train_set)
else:
    raise Exception("Classifier unknown: " + classifier)   
```

    F1 2018-04-23 17:13:28,238 INFO azureml.vision:Fit starting in experiment  1541466320 
    F1 2018-04-23 17:13:28,239 INFO azureml.vision:model starting training for scenario=classification 
    <class 'int'>
    1 worker
    Training transfer learning model for 15 epochs (epoch_size = 41).
    non-distributed mode
    Training 15741700 parameters in 53 parameter tensors.
    Training 15741700 parameters in 53 parameter tensors.
    Learning rate per minibatch: 0.05
    Momentum per minibatch: 0.9
    PROGRESS: 0.00%
    Finished Epoch[1 of 15]: [Training] loss = 2.820586 * 41, metric = 68.29% * 41 5.738s (  7.1 samples/s);
    Evaluation Set Error :: 29.27%
    Finished Epoch[2 of 15]: [Training] loss = 0.286728 * 41, metric = 9.76% * 41 0.752s ( 54.5 samples/s);
    Evaluation Set Error :: 34.15%
    Finished Epoch[3 of 15]: [Training] loss = 0.206938 * 41, metric = 4.88% * 41 0.688s ( 59.6 samples/s);
    Evaluation Set Error :: 41.46%
    Finished Epoch[4 of 15]: [Training] loss = 0.098931 * 41, metric = 2.44% * 41 0.785s ( 52.2 samples/s);
    Evaluation Set Error :: 48.78%
    Finished Epoch[5 of 15]: [Training] loss = 0.046547 * 41, metric = 0.00% * 41 0.724s ( 56.6 samples/s);
    Evaluation Set Error :: 43.90%
    Finished Epoch[6 of 15]: [Training] loss = 0.059709 * 41, metric = 4.88% * 41 0.636s ( 64.5 samples/s);
    Evaluation Set Error :: 34.15%
    Finished Epoch[7 of 15]: [Training] loss = 0.005817 * 41, metric = 0.00% * 41 0.710s ( 57.7 samples/s);
    Evaluation Set Error :: 14.63%
    Learning rate per minibatch: 0.005
    Finished Epoch[8 of 15]: [Training] loss = 0.014917 * 41, metric = 0.00% * 41 0.649s ( 63.2 samples/s);
    Evaluation Set Error :: 9.76%
    Finished Epoch[9 of 15]: [Training] loss = 0.040539 * 41, metric = 2.44% * 41 0.777s ( 52.8 samples/s);
    Evaluation Set Error :: 9.76%
    Finished Epoch[10 of 15]: [Training] loss = 0.024606 * 41, metric = 0.00% * 41 0.626s ( 65.5 samples/s);
    Evaluation Set Error :: 7.32%
    PROGRESS: 0.00%
    Finished Epoch[11 of 15]: [Training] loss = 0.004225 * 41, metric = 0.00% * 41 0.656s ( 62.5 samples/s);
    Evaluation Set Error :: 4.88%
    Finished Epoch[12 of 15]: [Training] loss = 0.004364 * 41, metric = 0.00% * 41 0.702s ( 58.4 samples/s);
    Evaluation Set Error :: 4.88%
    Finished Epoch[13 of 15]: [Training] loss = 0.007974 * 41, metric = 0.00% * 41 0.721s ( 56.9 samples/s);
    Evaluation Set Error :: 4.88%
    Finished Epoch[14 of 15]: [Training] loss = 0.000655 * 41, metric = 0.00% * 41 0.711s ( 57.7 samples/s);
    Evaluation Set Error :: 4.88%
    Learning rate per minibatch: 0.0005
    Finished Epoch[15 of 15]: [Training] loss = 0.024865 * 41, metric = 0.00% * 41 0.688s ( 59.6 samples/s);
    Evaluation Set Error :: 4.88%
    Stored trained model at ../../../cvtk_output\model_trained\ImageClassification.model
    F1 2018-04-23 17:13:45,097 INFO azureml.vision:Fit finished in experiment  1541466320 
    


```python
# Plot how the training and test accuracy increases during gradient descent. 
if classifier_name == "dnn":
    [train_accs, test_accs, epoch_numbers] = classifier.train_eval_accs
    plt.xlabel("Number of training epochs") 
    plt.ylabel("Classification accuracy") 
    train_plot = plt.plot(epoch_numbers, train_accs, 'r-', label = "Training accuracy")
    test_plot = plt.plot(epoch_numbers, test_accs, 'b-.', label = "Test accuracy")
    plt.legend()
```

![png](media/how-to-build-deploy-image-classification-models/output_17_0.png)


## <a name="evaluate-and-visualize-model-performance"></a>モデルのパフォーマンスを視覚化して評価する

評価モジュールを使用して、独立したテスト データセットに対するトレーニング済みモデルのパフォーマンスを評価できます。 計算される評価メトリックには、以下が含まれます。
 
+ 精度 (既定ではクラス平均)
+ PR 曲線
+ ROC 曲線
+ 曲線下面積
+ 混同行列


```python
# Run the classifier on all test set images
ce = ClassificationEvaluation(classifier, test_set, minibatch_size = mb_size)

# Compute Accuracy and the confusion matrix
acc = ce.compute_accuracy()
print("Accuracy = {:2.2f}%".format(100*acc))
cm  = ce.compute_confusion_matrix()
print("Confusion matrix = \n{}".format(cm))

# Show PR curve, ROC curve, and confusion matrix
fig, ((ax1, ax2, ax3)) = plt.subplots(1,3)
fig.set_size_inches(18, 4)
graph_roc_curve(ce, ax=ax1)
graph_pr_curve(ce, ax=ax2)
graph_confusion_matrix(ce, ax=ax3)
plt.show()
```

    F1 2018-04-23 17:14:37,449 INFO azureml.vision:evaluation doing evaluation for scenario=classification 
    F1 2018-04-23 17:14:37,450 INFO azureml.vision:model scoring dataset for scenario=classification 
    Accuracy = 95.45%
    Confusion matrix = 
    [[ 0  1  0  1]
     [ 0  7  0  0]
     [ 0  0  2  0]
     [ 0  0  0 11]]
    


![png](media/how-to-build-deploy-image-classification-models/output_20_1.png)



```python
# Results viewer UI
labels = [l.name for l in dataset.labels] 
pred_scores = ce.scores #classification scores for all images and all classes
pred_labels = [labels[i] for i in np.argmax(pred_scores, axis=1)]

results_ui = ResultsUI(test_set, Context.get_global_context(), pred_scores, pred_labels)
display(results_ui.ui)
```

![Azure Machine Learning のデータセット](media/how-to-build-deploy-image-classification-models/Image_Classification_Results.png)


```python
# Precision / recall curve UI
precisions, recalls, thresholds = ce.compute_precision_recall_curve() 
thresholds = list(thresholds)
thresholds.append(thresholds[-1])
pr_ui = PrecisionRecallUI(100*precisions[::-1], 100*recalls[::-1], thresholds[::-1])
display(pr_ui.ui) 
```

![Azure Machine Learning のデータセット](media/how-to-build-deploy-image-classification-models/image_precision_curve.png)

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
# ##### OPTIONAL###### 
# Interactive CLI setup helper, including model management account and deployment environment.
# If you haven't setup you CLI before or if you want to change you CLI settings, you can use this block to help you interactively.
#
# UNCOMMENT THE FOLLOWING LINES IF YOU HAVE NOT CREATED OR SET THE MODEL MANAGEMENT ACCOUNT AND DEPLOYMENT ENVIRONMENT
#
# from azuremltkbase.deployment import CliSetup
# CliSetup().run()
```


```python
# # Optional. Persist your model on disk and reuse it later for deployment. 
# from cvtk import TFFasterRCNN, Context
# import os
# save_model_path = os.path.join(Context.get_global_context().storage.persistent_path, "saved_classifier.model")
# # Save model to disk
# dnn_model.serialize(save_model_path)
# # Load model from disk
# dnn_model = CNTKTLModel.deserialize(save_model_path)
```


```python
from cvtk.operationalization import AMLDeployment

# set deployment name
deployment_name = "wsdeployment"

# Optional Azure Machine Learning deployment cluster name (environment name) and resource group name
# If you don't provide here. It will use the current deployment environment (you can check with CLI command "az ml env show").
azureml_rscgroup = "<resource group>"
cluster_name = "<cluster name>"

# If you provide the cluster information, it will use the provided cluster to deploy. 
# Example: deploy_obj = AMLDeployment(deployment_name=deployment_name, associated_DNNModel=dnn_model,
#                            aml_env="cluster", cluster_name=cluster_name, resource_group=azureml_rscgroup, replicas=1)

# Create deployment object
deploy_obj = AMLDeployment(deployment_name=deployment_name, aml_env="cluster", associated_DNNModel=dnn_model, replicas=1)

# Check if the deployment name exists, if yes remove it first.
if deploy_obj.is_existing_service():
    AMLDeployment.delete_if_service_exist(deployment_name)
    
# Create the web service
print("Deploying to Azure cluster...")
deploy_obj.deploy()
print("Deployment DONE")
```

### <a name="consume-the-web-service"></a>Web サービスを使用する 

モデルを Web サービスとして配置したら、次のいずれかの方法を使用して、その Web サービスで画像をスコア付けできます。

- `deploy_obj.score_image(image_path_or_url)` を使用して、デプロイ オブジェクトで Web サービスを直接スコア付けします。

- サービス エンドポイントの URL とサービス キー (ローカル デプロイでは存在しません) を使用します。`AMLDeployment.score_existing_service_with_image(image_path_or_url, service_endpoint_url, service_key=None)`

- Web サービス エンドポイントをスコア付けする HTTP 要求を直接作成します。 このオプションは、高度な知識を持つユーザー向けです。

### <a name="score-with-existing-deployment-object"></a>既存のデプロイ オブジェクトでスコア付けする

```
deploy_obj.score_image(image_path_or_url)
```


```python
# Score with existing deployment object

# Score local image with file path
print("Score local image with file path")
image_path_or_url = test_set.images[0].storage_path
print("Image source:",image_path_or_url)
serialized_result_in_json = deploy_obj.score_image(image_path_or_url, image_resize_dims=[224,224])
print("serialized_result_in_json:", serialized_result_in_json)

# Score image url and remove image resizing
print("Score image url")
image_path_or_url = "https://cvtkdata.blob.core.windows.net/publicimages/microsoft_logo.jpg"
print("Image source:",image_path_or_url)
serialized_result_in_json = deploy_obj.score_image(image_path_or_url)
print("serialized_result_in_json:", serialized_result_in_json)

# Score image url with added paramters. Add softmax to score.
print("Score image url with added paramters. Add softmax to score")
from cvtk.utils.constants import ClassificationRESTApiParamters
image_path_or_url = "https://cvtkdata.blob.core.windows.net/publicimages/microsoft_logo.jpg"
print("Image source:",image_path_or_url)
serialized_result_in_json = deploy_obj.score_image(image_path_or_url, image_resize_dims=[224,224], parameters={ClassificationRESTApiParamters.ADD_SOFTMAX:True})
print("serialized_result_in_json:", serialized_result_in_json)
```


```python
# Time image scoring
import timeit

for img_index, img_obj in enumerate(test_set.images[:10]):
    print("Calling API for image {} of {}: {}...".format(img_index, len(test_set.images), img_obj.name))
    tic = timeit.default_timer()
    return_json = deploy_obj.score_image(img_obj.storage_path, image_resize_dims=[224,224])
    print("   Time for API call: {:.2f} seconds".format(timeit.default_timer() - tic))
    print(return_json)
```

### <a name="score-with-service-endpoint-url-and-service-key"></a>サービス エンドポイントの URL とサービス キーでスコア付けする

`AMLDeployment.score_existing_service_with_image(image_path_or_url, service_endpoint_url, service_key=None)`

```python
# Import related classes and functions
from cvtk.operationalization import AMLDeployment

service_endpoint_url = "" # please replace with your own service url
service_key = "" # please replace with your own service key
# score local image with file path
image_path_or_url = test_set.images[0].storage_path
print("Image source:",image_path_or_url)
serialized_result_in_json = AMLDeployment.score_existing_service_with_image(image_path_or_url,service_endpoint_url, service_key = service_key)
print("serialized_result_in_json:", serialized_result_in_json)

# score image url
image_path_or_url = "https://cvtkdata.blob.core.windows.net/publicimages/microsoft_logo.jpg"
print("Image source:",image_path_or_url)
serialized_result_in_json = AMLDeployment.score_existing_service_with_image(image_path_or_url,service_endpoint_url, service_key = service_key, image_resize_dims=[224,224])
print("serialized_result_in_json:", serialized_result_in_json)
```

### <a name="score-endpoint-with-http-request-directly"></a>エンドポイントを http 要求で直接スコア付けする

次のコード例では、Python で HTTP 要求を直接作成します。 ただし、他のプログラミング言語で行うこともできます。


```python
def score_image_list_with_http(images, service_endpoint_url, service_key=None, parameters={}):
    """Score image list with http request

    Args:
        images(list): list of (input image file path, base64 image string, url or buffer)
        service_endpoint_url(str): endpoint url
        service_key(str): service key, None for local deployment.
        parameters(dict): service additional paramters in dictionary


    Returns:
        result (list): list of serialized result 
    """
    import requests
    from io import BytesIO
    import base64
    routing_id = ""

    if service_key is None:
        headers = {'Content-Type': 'application/json',
                   'X-Marathon-App-Id': routing_id}
    else:
        headers = {'Content-Type': 'application/json',
                   "Authorization": ('Bearer ' + service_key), 'X-Marathon-App-Id': routing_id}
    payload = []
    for image in images:
        encoded = None
        # read image
        with open(image,'rb') as f:
            image_buffer = BytesIO(f.read()) ## Getting an image file represented as a BytesIO object
        # convert your image to base64 string
        encoded = base64.b64encode(image_buffer.getvalue())
        image_request = {"image_in_base64": "{0}".format(encoded), "parameters": parameters}
        payload.append(image_request)
    body = json.dumps(payload)
    r = requests.post(service_endpoint_url, data=body, headers=headers)
    try:
        result = json.loads(r.text)
    except:
        raise ValueError("Incorrect output format. Result cant not be parsed: " + r.text)
    return result

# Test with images
images = [test_set.images[0].storage_path, test_set.images[1].storage_path] # A list of local image files
score_image_list_with_http(images, service_endpoint_url, service_key)
```

### <a name="parse-serialized-result-from-web-service"></a>Web サービスからのシリアル化された結果を解析する

Web サービスからの出力は、JSON 文字列です。 この JSON 文字列を、異なる DNN モデルのクラスで解析できます。


```python
image_path_or_url = test_set.images[0].storage_path
print("Image source:",image_path_or_url)
serialized_result_in_json = deploy_obj.score_image(image_path_or_url, image_resize_dims=[224,224])
print("serialized_result_in_json:", serialized_result_in_json)
```


```python
# Parse result from json string
import numpy as np
parsed_result = CNTKTLModel.parse_serialized_result(serialized_result_in_json)
print("Parsed result:", parsed_result)
# Map result to image class
class_index = np.argmax(np.array(parsed_result))
print("Class index:", class_index)
dnn_model.class_map
print("Class label:", dnn_model.class_map[class_index])
```


## <a name="next-steps"></a>次の手順

次の記事で、Azure Machine Learning Package for Computer Vision の詳細を確認します。

+ [このモデルの精度を向上させる](how-to-improve-accuracy-for-computer-vision-models.md)方法を確認します。

+ [パッケージの概要とインストール方法](https://aka.ms/aml-packages/vision)を確認します。

+ パッケージの[リファレンス ドキュメント](https://docs.microsoft.com/python/api/overview/azure-machine-learning/computer-vision)を確認します。

+ [Azure Machine Learning 用の他の Python パッケージ](reference-python-package-overview.md)を確認します。
