---
title: 'チュートリアル: AutoML - オブジェクト検出モデルのトレーニング'
titleSuffix: Azure Machine Learning
description: Azure Machine Learning の自動 ML を使用して Azure Machine Learning Python SDK で NYC タクシーの料金を予測するオブジェクト検出モデルをトレーニングします。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: swatig007
ms.author: swatig
ms.reviewer: nibaccam
ms.date: 10/06/2021
ms.custom: devx-track-python, automl
ms.openlocfilehash: e6906988b47ecdb1ba5e1ca7947666e6371ddd8e
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131468577"
---
# <a name="tutorial-train-an-object-detection-model-preview-with-automl-and-python"></a>チュートリアル: AutoML と Python を使用してオブジェクト検出モデル (プレビュー) をトレーニングする

>[!IMPORTANT]
> この記事に記載されている機能はプレビュー段階です。 これらは、いつでも変更される可能性がある[試験段階](/python/api/overview/azure/ml/#stable-vs-experimental)のプレビュー機能と考える必要があります。

このチュートリアルでは、Azure Machine Learning Python SDK で Azure Machine Learning の自動 ML を使用してオブジェクト検出モデルをトレーニングする方法について説明します。 このオブジェクト検出モデルは、画像に缶、箱、牛乳瓶、水のボトルなどのオブジェクトが含まれているかどうかを識別します。

自動 ML は、トレーニング データと構成設定を受け取り、さまざまな特徴量の正規化/標準化の方法、モデル、およびハイパーパラメーター設定の組み合わせを自動的に反復処理し、最適なモデルに到達します。


このチュートリアルでは、Python SDK を使用してコードを記述し、次のタスクを学習します。

> [!div class="checklist"]
> * データをダウンロードして変換する
> * 自動機械学習オブジェクト検出モデルをトレーニングする
> * モデルのハイパーパラメーター値を指定する
> * ハイパーパラメーター スイープを実行する
> * モデルをデプロイする
> * 検出を視覚化する

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプションをお持ちでない場合は、開始する前に無料アカウントを作成してください。 [無料版または有料版](https://azure.microsoft.com/free/)の Azure Machine Learning を今すぐお試しください。

* この機能では、Python 3.6 または 3.7 がサポートされています。

* まだ Azure Machine Learning ワークスペースがない場合は、[Azure Machine Learning の利用開始に関するクイックスタート](quickstart-create-resources.md#create-the-workspace)を完了します。

* [**odFridgeObjects.csv**](https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/bankmarketing_train.csv) データ ファイルをダウンロードします。

独自の[ローカル環境](how-to-configure-environment.md#local)で実行したい場合は、このチュートリアルを [GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/tutorials) で入手することもできます。 必要なパッケージを取得するには、
* `pip install azureml` を実行します。
* [完全な `automl` クライアントをインストール](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/README.md#setup-using-a-local-conda-environment)します。

## <a name="compute-target-setup"></a>コンピューティング先のセットアップ

最初に、自動 ML モデルのトレーニングに使用するコンピューティング先を設定する必要があります。 画像タスクの自動 ML モデルには、GPU SKU が必要です。

このチュートリアルでは、NCsv3 シリーズ (V100 GPU を使用) を使用します。この種類のコンピューティング先では、トレーニングを高速化するために複数の GPU が利用されるためです。 さらに、複数のノードを設定すると、モデルのハイパーパラメーターをチューニングするときに並列処理を利用できます。

次のコードを実行すると、ワークスペース `ws` に接続されている 4 つのノードを持つ Standard _NC24s_v3 の GPU コンピューティング サイズが作成されます。

> [!WARNING]
> 使用するコンピューティング先に対して、サブスクリプションに十分なクォータがあることを確認します。

```python
from azureml.core.compute import AmlCompute, ComputeTarget

cluster_name = "gpu-cluster-nc24s_v3"

try:
    compute_target = ws.compute_targets[cluster_name]
    print('Found existing compute target.')
except KeyError:
    print('Creating a new compute target...')
    compute_config = AmlCompute.provisioning_configuration(vm_size='Standard_NC24s_v3',
                                                           idle_seconds_before_scaledown=1800,
                                                           min_nodes=0,
                                                           max_nodes=4)

    compute_target = ComputeTarget.create(ws, cluster_name, compute_config)

#If no min_node_count is provided, the scale settings are used for the cluster.
compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
```

## <a name="experiment-setup"></a>実験のセットアップ
次に、ワークスペースに `Experiment` を作成して、モデルのトレーニングの実行を追跡します。

```python

from azureml.core import Experiment

experiment_name = 'automl-image-object-detection'
experiment = Experiment(ws, name=experiment_name)
```

## <a name="visualize-input-data"></a>入力データを視覚化する

[JSONL](https://jsonlines.org/) (JSON 行) 形式で入力画像データを準備したら、画像のグラウンド トゥルース境界ボックスを視覚化できます。 これを行うために、`matplotlib` がインストールされていることを確認します。

```
%pip install --upgrade matplotlib
```
```python

%matplotlib inline
import matplotlib.pyplot as plt
import matplotlib.image as mpimg
import matplotlib.patches as patches
from PIL import Image as pil_image
import numpy as np
import json
import os

def plot_ground_truth_boxes(image_file, ground_truth_boxes):
    # Display the image
    plt.figure()
    img_np = mpimg.imread(image_file)
    img = pil_image.fromarray(img_np.astype("uint8"), "RGB")
    img_w, img_h = img.size

    fig,ax = plt.subplots(figsize=(12, 16))
    ax.imshow(img_np)
    ax.axis("off")

    label_to_color_mapping = {}

    for gt in ground_truth_boxes:
        label = gt["label"]

        xmin, ymin, xmax, ymax =  gt["topX"], gt["topY"], gt["bottomX"], gt["bottomY"]
        topleft_x, topleft_y = img_w * xmin, img_h * ymin
        width, height = img_w * (xmax - xmin), img_h * (ymax - ymin)

        if label in label_to_color_mapping:
            color = label_to_color_mapping[label]
        else:
            # Generate a random color. If you want to use a specific color, you can use something like "red".
            color = np.random.rand(3)
            label_to_color_mapping[label] = color

        # Display bounding box
        rect = patches.Rectangle((topleft_x, topleft_y), width, height,
                                 linewidth=2, edgecolor=color, facecolor="none")
        ax.add_patch(rect)

        # Display label
        ax.text(topleft_x, topleft_y - 10, label, color=color, fontsize=20)

    plt.show()

def plot_ground_truth_boxes_jsonl(image_file, jsonl_file):
    image_base_name = os.path.basename(image_file)
    ground_truth_data_found = False
    with open(jsonl_file) as fp:
        for line in fp.readlines():
            line_json = json.loads(line)
            filename = line_json["image_url"]
            if image_base_name in filename:
                ground_truth_data_found = True
                plot_ground_truth_boxes(image_file, line_json["label"])
                break
    if not ground_truth_data_found:
        print("Unable to find ground truth information for image: {}".format(image_file))

def plot_ground_truth_boxes_dataset(image_file, dataset_pd):
    image_base_name = os.path.basename(image_file)
    image_pd = dataset_pd[dataset_pd['portable_path'].str.contains(image_base_name)]
    if not image_pd.empty:
        ground_truth_boxes = image_pd.iloc[0]["label"]
        plot_ground_truth_boxes(image_file, ground_truth_boxes)
    else:
        print("Unable to find ground truth information for image: {}".format(image_file))
```

上記のヘルパー関数を使用すると、任意の画像に対して、次のコードを実行して境界ボックスを表示できます。

```python
image_file = "./odFridgeObjects/images/31.jpg"
jsonl_file = "./odFridgeObjects/train_annotations.jsonl"

plot_ground_truth_boxes_jsonl(image_file, jsonl_file)
```

## <a name="upload-data-and-create-dataset"></a>データをアップロードしてデータセットを作成する

トレーニングにデータを使用するには、データストアを介してそのデータをワークスペースにアップロードします。 データストアは、データをアップロードまたはダウンロードし、リモート コンピューティング先からデータを操作するためのメカニズムを提供します。

```python
ds = ws.get_default_datastore()
ds.upload(src_dir='./odFridgeObjects', target_path='odFridgeObjects')
```

データストアにアップロードしたら、そのデータから Azure Machine Learning データセットを作成できます。 データセットによって、データがトレーニングに使用可能なオブジェクトにパッケージ化されます。

次のコードでは、トレーニング用のデータセットが作成されます。 検証データセットは指定されていないので、トレーニング データの 20% が既定で検証に使用されます。

``` python
from azureml.core import Dataset
from azureml.data import DataType

training_dataset_name = 'odFridgeObjectsTrainingDataset'
if training_dataset_name in ws.datasets:
    training_dataset = ws.datasets.get(training_dataset_name)
    print('Found the training dataset', training_dataset_name)
else:
    # create training dataset
        # create training dataset
    training_dataset = Dataset.Tabular.from_json_lines_files(
        path=ds.path('odFridgeObjects/train_annotations.jsonl'),
        set_column_types={"image_url": DataType.to_stream(ds.workspace)},
    )
    training_dataset = training_dataset.register(workspace=ws, name=training_dataset_name)

print("Training dataset name: " + training_dataset.name)
```

### <a name="visualize-dataset"></a>データセットを視覚化する

このデータセットから画像のグラウンド トゥルース境界ボックスを視覚化することもできます。

pandas データフレームにデータセットを読み込みます。

```python
import azureml.dataprep as dprep

from azureml.dataprep.api.functions import get_portable_path

# Get pandas dataframe from the dataset
dflow = training_dataset._dataflow.add_column(get_portable_path(dprep.col("image_url")),
                                              "portable_path", "image_url")
dataset_pd = dflow.to_pandas_dataframe(extended_types=True)
```

任意の画像に対して、次のコードを実行して境界ボックスを表示できます。

```python
image_file = "./odFridgeObjects/images/31.jpg"
plot_ground_truth_boxes_dataset(image_file, dataset_pd)
```

## <a name="configure-your-object-detection-experiment"></a>オブジェクト検出実験を構成する

画像関連タスクに対して自動 ML の実行を構成するには、`AutoMLImageConfig` オブジェクトを使用します。 `AutoMLImageConfig` で、`model_name` パラメーターを使用してモデル アルゴリズムを指定し、定義されたパラメーター空間でハイパーパラメーター スイープを実行して最適なモデルを見つけるように設定を構成できます。

この例では、`AutoMLImageConfig` を使用して、`yolov5` と `fasterrcnn_resnet50_fpn` でオブジェクト検出モデルをトレーニングします。これらはどちらも COCO、大規模なオブジェクト検出、セグメンテーション、および 80 を超えるラベル カテゴリと数千を超えるラベル付き画像を含むキャプション データセットで事前トレーニングされています。

### <a name="hyperparameter-sweeping-for-image-tasks"></a>画像タスクのハイパーパラメーター スイープ

最適なモデルを見つけるために、定義されたパラメーター空間に対してハイパーパラメーター スイープを実行できます。

次のコードは、定義された各アルゴリズム (`yolov5` および `fasterrcnn_resnet50_fpn`) のハイパーパラメーター スイープに備えてパラメーター空間を定義します。  パラメーター空間で、AutoML が最適な主要メトリックでモデルの生成を試みるときに選択できるようにするため、`learning_rate`、`optimizer`、`lr_scheduler` などの値の範囲を指定します。 ハイパーパラメーター値を指定しない場合は、アルゴリズムごとに既定値が使用されます。

チューニング設定では、`GridParameterSampling, RandomParameterSampling` および `BayesianParameterSampling` のクラスをインポートすることで、ランダム サンプリングを使用してこのパラメーター空間からサンプルを選択します。  そうすることで、自動 ML に、これらの異なるサンプルで合計 20 回のイテレーションを試行し、4 つのノードを使用して設定されたコンピューティング先で一度に 4 回のイテレーションを実行するように指示します。 空間に含まれるパラメーターが多いほど、最適なモデルを見つけるために必要なイテレーションの回数が多くなります。

バンディット早期終了ポリシーも使用されます。 このポリシーは、パフォーマンスが低い構成 (つまり、最高のパフォーマンスを発揮する構成と比較してパフォーマンスが 20％ 超低下する構成) を終了します。これによりコンピューティング リソースが大幅に節約されます。

```python
from azureml.train.hyperdrive import RandomParameterSampling
from azureml.train.hyperdrive import BanditPolicy, HyperDriveConfig
from azureml.train.hyperdrive import choice, uniform

parameter_space = {
    'model': choice(
        {
            'model_name': choice('yolov5'),
            'learning_rate': uniform(0.0001, 0.01),
            #'model_size': choice('small', 'medium'), # model-specific
            'img_size': choice(640, 704, 768), # model-specific
        },
        {
            'model_name': choice('fasterrcnn_resnet50_fpn'),
            'learning_rate': uniform(0.0001, 0.001),
            #'warmup_cosine_lr_warmup_epochs': choice(0, 3),
            'optimizer': choice('sgd', 'adam', 'adamw'),
            'min_size': choice(600, 800), # model-specific
        }
    )
}

tuning_settings = {
    'iterations': 20,
    'max_concurrent_iterations': 4,
    'hyperparameter_sampling': RandomParameterSampling(parameter_space),
    'policy': BanditPolicy(evaluation_interval=2, slack_factor=0.2, delay_evaluation=6)
}
```

パラメーター空間とチューニング設定を定義したら、それらを `AutoMLImageConfig` オブジェクトに渡し、実験を送信して、トレーニング データセットを使用して画像モデルをトレーニングできます。

```python
from azureml.train.automl import AutoMLImageConfig
automl_image_config = AutoMLImageConfig(task='image-object-detection',
                                        compute_target=compute_target,
                                        training_data=training_dataset,
                                        validation_data=validation_dataset,
                                        primary_metric='mean_average_precision',
                                        **tuning_settings)

automl_image_run = experiment.submit(automl_image_config)
automl_image_run.wait_for_completion(wait_post_processing=True)
```

ハイパーパラメーター スイープを実行する場合は、HyperDrive UI を使用して試行されたさまざまな構成を視覚化すると便利な場合があります。 この UI に移動するには、上の HyperDrive の親実行からメイン automl_image_run の UI の [Child runs]\(子実行\) タブに移動します。 その後、この UI の [Child runs]\(子実行\) タブに移動できます。 または、以下を実行すると、HyperDrive の親実行を直接確認し、その [Child runs]\(子実行\) タブに移動することができます。

```python
from azureml.core import Run
hyperdrive_run = Run(experiment=experiment, run_id=automl_image_run.id + '_HD')
hyperdrive_run
```

## <a name="register-the-best-model"></a>最高のモデルを登録する

実行が完了したら、最適な実行から作成されたモデルを登録できます。

```python
best_child_run = automl_image_run.get_best_child()
model_name = best_child_run.properties['model_name']
model = best_child_run.register_model(model_name = model_name, model_path='outputs/model.pt')
```

## <a name="deploy-model-as-a-web-service"></a>モデルを Web サービスとしてデプロイする

トレーニング済みモデルが準備できたら、そのモデルを Azure にデプロイできます。 Azure Container Instances (ACI) または Azure Kubernetes Service (AKS) にトレーニング済みモデルを Web サービスとしてデプロイできます。 ACI はデプロイをテストするための最適なオプションであるのに対し、AKS は高スケールの運用環境での使用により適しています。

このチュートリアルでは、モデルを Web サービスとして AKS にデプロイします。

1. AKS コンピューティング クラスターを作成します。 この例では、デプロイ クラスターに GPU 仮想マシン SKU が使用されます

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

1. モデルを含む Web サービスを設定する方法を示す推論構成を定義します。 推論構成では、スコアリング スクリプトとトレーニング実行の環境を使用できます。

    > [!NOTE]
    > モデルの設定を変更するには、ダウンロードしたスコアリング スクリプトを開き、モデルをデプロイする前に model_settings 変数を変更します。

    ```python
    from azureml.core.model import InferenceConfig

    best_child_run.download_file('outputs/scoring_file_v_1_0_0.py', output_file_path='score.py')
    environment = best_child_run.get_environment()
    inference_config = InferenceConfig(entry_script='score.py', environment=environment)
    ```

1. その後、AKS Web サービスとしてモデルをデプロイできます。

    ```python

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

## <a name="test-the-web-service"></a>Web サービスをテストする

デプロイした Web サービスをテストして、新しい画像を予測できます。 このチュートリアルでは、データセットからランダムな画像を渡し、スコアリング URI に渡します。

```python
import requests

# URL for the web service
scoring_uri = aks_service.scoring_uri

# If the service is authenticated, set the key or token
key, _ = aks_service.get_keys()

sample_image = './test_image.jpg'

# Load image data
data = open(sample_image, 'rb').read()

# Set the content type
headers = {'Content-Type': 'application/octet-stream'}

# If authentication is enabled, set the authorization header
headers['Authorization'] = f'Bearer {key}'

# Make the request and display the response
resp = requests.post(scoring_uri, data, headers=headers)
print(resp.text)
```
## <a name="visualize-detections"></a>検出を視覚化する
テスト画像をスコア付けしたので、この画像の境界ボックスを視覚化できます。 これを行うために、matplotlib がインストールされていることを確認します。

```
%pip install --upgrade matplotlib
```

```python
%matplotlib inline
import matplotlib.pyplot as plt
import matplotlib.image as mpimg
import matplotlib.patches as patches
from PIL import Image
import numpy as np
import json

IMAGE_SIZE = (18,12)
plt.figure(figsize=IMAGE_SIZE)
img_np=mpimg.imread(sample_image)
img = Image.fromarray(img_np.astype('uint8'),'RGB')
x, y = img.size

fig,ax = plt.subplots(1, figsize=(15,15))
# Display the image
ax.imshow(img_np)

# draw box and label for each detection
detections = json.loads(resp.text)
for detect in detections['boxes']:
    label = detect['label']
    box = detect['box']
    conf_score = detect['score']
    if conf_score > 0.6:
        ymin, xmin, ymax, xmax =  box['topY'],box['topX'], box['bottomY'],box['bottomX']
        topleft_x, topleft_y = x * xmin, y * ymin
        width, height = x * (xmax - xmin), y * (ymax - ymin)
        print('{}: [{}, {}, {}, {}], {}'.format(detect['label'], round(topleft_x, 3),
                                                round(topleft_y, 3), round(width, 3),
                                                round(height, 3), round(conf_score, 3)))

        color = np.random.rand(3) #'red'
        rect = patches.Rectangle((topleft_x, topleft_y), width, height,
                                 linewidth=3, edgecolor=color,facecolor='none')

        ax.add_patch(rect)
        plt.text(topleft_x, topleft_y - 10, label, color=color, fontsize=20)

plt.show()
```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

Azure Machine Learning の他のチュートリアルを実行する予定の場合、このセクションを実行しないでください。

作成したリソースを今後使用する予定がない場合は、課金が発生しないように削除します。

1. Azure Portal で、左端にある **[リソース グループ]** を選択します。
1. 作成したリソース グループを一覧から選択します。
1. **[リソース グループの削除]** を選択します。
1. リソース グループ名を入力します。 次に、 **[削除]** を選択します。

リソース グループは保持しつつ、いずれかのワークスペースを削除することもできます。 ワークスペースのプロパティを表示し、 **[削除]** を選択します。

## <a name="next-steps"></a>次のステップ

自動化された機械学習に関するこのチュートリアルでは、以下のタスクを学習しました。

> [!div class="checklist"]
> * 実験用のワークスペースと準備されたデータを構成しました。
> * 自動オブジェクト検出モデルをトレーニングしました
> * モデルのハイパーパラメーター値を指定しました
> * ハイパーパラメーター スイープを実行しました
> * モデルをデプロイしました
> * 検出を視覚化しました

* [自動 ML の Computer Vision (プレビュー) の詳細を確認します](concept-automated-ml.md#computer-vision-preview)。
* [Python を使用して Computer Vision モデルをトレーニングするために AutoML を設定する (プレビュー) 方法の詳細を確認します](how-to-auto-train-image-models.md)。
* [GitHub の自動機械学習サンプルのノートブック リポジトリ](https://github.com/Azure/azureml-examples/tree/main/python-sdk/tutorials/automl-with-azureml)で詳しいコード サンプルやユース ケースを確認してください。 Computer Vision モデルの構築に固有のサンプルについては、"image-" プレフィックスが付いたフォルダーを確認してください。

> [!NOTE]
> fridge オブジェクト データセットの使用は、[MIT ライセンス](https://github.com/microsoft/computervision-recipes/blob/master/LICENSE)のライセンスを通じて利用できます。
