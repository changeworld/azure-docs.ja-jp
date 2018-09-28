---
title: クラウドで自動機械学習を使用してモデルをトレーニングする - Azure Machine Learning
description: この記事では、リモート コンピューティング リソースを作成して、機械学習モデルを自動的にトレーニングする方法について説明します。
services: machine-learning
author: nacharya1
ms.author: nilesha
ms.reviewer: sgilley
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 00d34fd0fe5f62e4da4be7d80afceb29753251bc
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2018
ms.locfileid: "46946971"
---
# <a name="train-models-with-automated-machine-learning-in-the-cloud-with-azure-machine-learning"></a>Azure Machine Learning を使用してクラウドで自動機械学習を使用してモデルをトレーニングする

Azure Machine Learning では、管理しているさまざまな種類のコンピューティング リソースに対してモデルをトレーニングできます。 ローカル コンピューターまたはクラウド内のコンピューターをコンピューティング ターゲットにすることができます。

Ubuntu ベースの Data Science Virtual Machine (DSVM) や Azure Batch AI などのコンピューティング ターゲットを追加することで、機械学習実験を簡単にスケール アップまたはスケール アウトすることができます。 DSVM とは、データ サイエンス専用に構築された Microsoft の Azure クラウド上にあるカスタマイズされた VM イメージです。 多くの一般的なデータ サイエンスや他のツールが事前にインストールされ、事前に構成されています。  

この記事では、DSVM 上で自動機械学習を使用してモデルを構築する方法について説明します。  

## <a name="how-does-remote-differ-from-local"></a>リモートとローカルの違い

[自動機械学習を使用して分類モデルをトレーニングする方法](tutorial-auto-train-models.md)に関するチュートリアルでは、ローカル コンピューターで自動機械学習を使用してモデルをトレーニングする方法について説明しています。  ローカルでトレーニングするときのワークフローは、リモート ターゲットの場合にも適用できます。 リモート コンピューティングの場合、自動機械学習実験の反復処理は非同期的に実行されます。 そのため、特定の反復を取り消し、実行の状態を確認し、Jupiter ノートブックで他のセルに対する作業を続行することができます。 リモートからトレーニングするには、まず Azure DSVM などのリモート コンピューティング ターゲットを作成し、構成して、そこにコードを送信します。

この記事では、リモートの DSVM で自動機械学習実験を実行するために必要な追加の手順について説明します。  チュートリアルのワークスペース オブジェクト `ws` は、このコード全体で使用されています。

```python
ws = Workspace.from_config()
```

## <a name="create-resource"></a>リソースを作成する

DSVM がまだ存在しない場合は、ワークスペースに DSVM を作成します (`ws`)。 以前に作成された DSVM がある場合、このコードは作成プロセスをスキップし、既存のリソースの詳細を `dsvm_compute` オブジェクトに読み込みます。  

**推定所要時間**: VM の作成時間は約 5 分です。

```python
from azureml.core.compute import DsvmCompute

dsvm_name = 'mydsvm' #Name your DSVM
try:
    dsvm_compute = DsvmCompute(ws, dsvm_name)
    print('found existing dsvm.')
except:
    print('creating new dsvm.')
    dsvm_config = DsvmCompute.provisioning_configuration(vm_size = "Standard_D2_v2")
    dsvm_compute = DsvmCompute.create(ws, name = dsvm_name, provisioning_configuration = dsvm_config)
    dsvm_compute.wait_for_completion(show_output = True)
```

リモート コンピューティング ターゲットとして `dsvm_compute` オブジェクトを使用できるようになりました。

DSVM の名前には次の制限があります。
+ 64 文字未満にする必要があります。  
+ 次の文字は使用できません。`\` ~ ! @ # $ % ^ & * ( ) = + _ [ ] { } \\\\ | ; : \' \\" , < > / ?.`

>[!Warning]
>Marketplace の購入資格に関するメッセージで作成が失敗する場合:
>    1. [Azure portal](https://portal.azure.com) に移動します
>    1. DSVM の作成を開始します 
>    1. [Want to create programmatically]\(プログラムで作成する\) を選択してプログラムによる作成を有効にします
>    1. VM を実際に作成せずに終了します
>    1. 作成コードを再実行します


## <a name="create-a-runconfiguration-with-dsvm-name"></a>DSVM 名を指定して RunConfiguration を作成します
ここで、runconfiguration に実際の dsvm 名を指定します

```python

# create the run configuration to use for remote training
from azureml.core.runconfig import RunConfiguration
run_config = RunConfiguration() 
# set the target to dsvm_compute created above
run_config.target = dsvm_compute 
```

これで、自動機械学習のターゲットとして `run_config` オブジェクトを使用できるようになりました。 

## <a name="access-data-using-get-data-file"></a>get_data ファイルを使用してデータにアクセスする

トレーニング データに対するリモート リソースのアクセスを用意します。 リモート コンピューティングで実行される自動機械学習実験の場合、`get_data()` 関数を使用してデータを取得する必要があります。  

アクセスを用意するには、次の手順を行う必要があります。
+ `get_data()` 関数を含む get_data.py ファイルを作成します 
* そのファイルを、スクリプトが含まれるフォルダーのルート ディレクトリに置きます 

BLOB ストレージまたはローカル ディスクからデータを読み取るコードを get_data.py ファイルにカプセル化することができます。 次のコード サンプルでは、sklearn パッケージのデータが使用されています。

>[!Warning]
>リモート コンピューティングを使用している場合は、`get_data()` を使用してデータの変換を実行する必要があります。


```python
%%writefile $project_folder/get_data.py

from sklearn import datasets
from scipy import sparse
import numpy as np

def get_data():
    
    digits = datasets.load_digits()
    X_digits = digits.data[10:,:]
    y_digits = digits.target[10:]

    return { "X" : X_digits, "y" : y_digits }
```

## <a name="configure-automated-machine-learning-experiment"></a>自動機械学習実験を構成する

`AutoMLConfig` の設定を指定します  (全パラメーターの一覧と使用できる値については[こちら]()を参照してください)。

この設定で、`run_configuration` は `run_config` オブジェクトに設定されます。このオブジェクトには、DSVM の設定と構成が含まれています。  

```python
from azureml.train.automl import AutoMLConfig
import time
import logging

automl_settings = {
    "name": "AutoML_Demo_Experiment_{0}".format(time.time()),
    "max_time_sec": 600,
    "iterations": 20,
    "n_cross_validations": 5,
    "primary_metric": 'AUC_weighted',
    "preprocess": False,
    "concurrent_iterations": 10,
    "verbosity": logging.INFO
}

automl_config = AutoMLConfig(task='classification',
                             debug_log='automl_errors.log',
                             path=project_folder,
                             run_configuration=run_config,
                             data_script=project_folder + "./get_data.py",
                             **automl_settings
                            )
```

## <a name="submit-automated-machine-learning-training-experiment"></a>自動機械学習実験を送信する

次に、自動的にハイパー パラメーターのアルゴリズムを選択し、モデルをトレーニングするように構成を送信します (`submit` メソッドの設定の詳細については[こちら]()を参照してください)。

```python
from azureml.core.experiment import Experiment
experiment=Experiment(ws, 'automl_remote')
remote_run = experiment.submit(automl_config, show_output=True)
```
出力は以下のようになります。

    Running on remote compute: mydsvmParent Run ID: AutoML_015ffe76-c331-406d-9bfd-0fd42d8ab7f6
    ***********************************************************************************************
    ITERATION: The iteration being evaluated.
    PIPELINE:  A summary description of the pipeline being evaluated.
    DURATION: Time taken for the current iteration.
    METRIC: The result of computing score on the fitted pipeline.
    BEST: The best observed score thus far.
    ***********************************************************************************************
    
     ITERATION     PIPELINE                               DURATION                METRIC      BEST
             2      Standardize SGD classifier            0.0                      0.954     0.954
             7      Normalizer DT                         0.0                      0.161     0.954
             0      Scale MaxAbs 1 extra trees            0.0                      0.936     0.954
             4      Robust Scaler SGD classifier          0.0                      0.867     0.954
             1      Normalizer kNN                        0.0                      0.984     0.984
             9      Normalizer extra trees                0.0                      0.834     0.984
             5      Robust Scaler DT                      0.0                      0.736     0.984
             8      Standardize kNN                       0.0                      0.981     0.984
             6      Standardize SVM                       2.2                      0.984     0.984
            10      Scale MaxAbs 1 DT                     0.0                      0.077     0.984
            11      Standardize SGD classifier            0.0                      0.863     0.984
             3      Standardize gradient boosting         5.4                      0.971     0.984
            12      Robust Scaler logistic regression     2.0                      0.955     0.984
            14      Scale MaxAbs 1 SVM                    0.0                      0.989     0.989
            13      Scale MaxAbs 1 gradient boosting      3.4                      0.971     0.989
            15      Robust Scaler kNN                     0.0                      0.904     0.989
            17      Standardize kNN                       0.0                      0.974     0.989
            16      Scale 0/1 gradient boosting           2.8                      0.968     0.989
            18      Scale 0/1 extra trees                 0.0                      0.828     0.989
            19      Robust Scaler kNN                     0.0                      0.983     0.989


## <a name="explore-results"></a>結果を探索する

[トレーニングのチュートリアル](tutorial-auto-train-models.md#explore-the-results)と同じ Jupyter ウィジェットを使用して、結果のグラフと表を表示することができます。

```python
from azureml.train.widgets import RunDetails
RunDetails(remote_run).show()
```
ウィジェットの静的画像を次に示します。  ノートブックで表内の任意の行をクリックすると、その実行の実行プロパティと出力ログが表示されます。   また、グラフの上のドロップダウンを使用して、各繰り返しについて使用できる各メトリックのグラフを表示することもできます。

![ウィジェットの表](./media/how-to-auto-train-remote/table.png)
![ウィジェットのプロット](./media/how-to-auto-train-remote/plot.png)

ウィジェットに表示される URL をクリックすると、個々の実行の詳細を表示し、探索することができます。
 
### <a name="view-logs"></a>ログを表示する。

DSVM に関するログは、/tmp/azureml_run/{iterationid}/azureml-logs 以下にあります。

## <a name="example"></a>例

`automl/03.auto-ml-remote-execution.ipynb` ノートブックは、この記事の概念を説明する例です。  このノートブックの入手:

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>次の手順

[自動トレーニングの設定を構成する方法]()について説明します。
