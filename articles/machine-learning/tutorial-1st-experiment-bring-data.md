---
title: 'チュートリアル: データをアップロードしてモデルをトレーニングする'
titleSuffix: Azure Machine Learning
description: リモート トレーニングの実行で独自のデータをアップロードして使用する方法。 これは、3 部構成の入門シリーズのパート 3 です。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: aminsaied
ms.author: amsaied
ms.reviewer: sgilley
ms.date: 04/29/2021
ms.custom: tracking-python, contperf-fy21q3, FY21Q4-aml-seo-hack, contperf-fy21q4
ms.openlocfilehash: 43684ffe7287aa8cd227f471b878450de39b72d6
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132704316"
---
# <a name="tutorial-upload-data-and-train-a-model-part-3-of-3"></a>チュートリアル: データをアップロードしてモデルをトレーニングする (3/3)

このチュートリアルでは、独自のデータをアップロードし、それを使用することで、Azure Machine Learning で機械学習モデルをトレーニングする方法について説明します。 このチュートリアルは、"*3 部構成のチュートリアル シリーズのパート 3*" です。  

「[パート 2: モデルをトレーニングする](tutorial-1st-experiment-sdk-train.md)」では、`PyTorch` のサンプル データを使用して、クラウドでモデルをトレーニングしました。  また、PyTorch API の `torchvision.datasets.CIFAR10` メソッドを使用してデータをダウンロードしました。 このチュートリアルでは、ダウンロードしたデータを使用して、Azure Machine Learning で独自のデータを処理するワークフローについて説明します。

このチュートリアルでは、次の作業を行いました。

> [!div class="checklist"]
> * Azure にデータをアップロードする。
> * コントロール スクリプトを作成する。
> * 新しい Azure Machine Learning の概念を理解する (パラメーター、データセット、データストアの受け渡し)。
> * トレーニング スクリプトを送信して実行する。
> * クラウドでのコード出力を表示する。

## <a name="prerequisites"></a>前提条件

前のチュートリアルでダウンロードしたデータが必要です。  これらの手順が完了していることを確認します。

1. [トレーニング スクリプトを作成する](tutorial-1st-experiment-sdk-train.md#create-training-scripts)。  
1. [ローカルでテストする](tutorial-1st-experiment-sdk-train.md#test-local)。

## <a name="adjust-the-training-script"></a>トレーニング スクリプトを調整する

既に、Azure Machine Learning で実行するトレーニング スクリプト (get-started/src/train.py) が用意され、モデルのパフォーマンスを監視できるようになっています。 引数を導入してトレーニング スクリプトをパラメーター化しましょう。 引数を使用すると、さまざまなハイパーパラメーターを簡単に比較できます。

トレーニング スクリプトは現在、実行のたびに CIFAR10 データセットをダウンロードするように設定されています。 次の Python コードは、ディレクトリからデータを読み取るように調整されています。

>[!NOTE] 
> `argparse` を使用すると、スクリプトがパラメーター化されます。

1. *train.py* を開いて、このコードに置き換えます。

    ```python
    import os
    import argparse
    import torch
    import torch.optim as optim
    import torchvision
    import torchvision.transforms as transforms
    from model import Net
    from azureml.core import Run
    run = Run.get_context()
    if __name__ == "__main__":
        parser = argparse.ArgumentParser()
        parser.add_argument(
            '--data_path',
            type=str,
            help='Path to the training data'
        )
        parser.add_argument(
            '--learning_rate',
            type=float,
            default=0.001,
            help='Learning rate for SGD'
        )
        parser.add_argument(
            '--momentum',
            type=float,
            default=0.9,
            help='Momentum for SGD'
        )
        args = parser.parse_args()
        print("===== DATA =====")
        print("DATA PATH: " + args.data_path)
        print("LIST FILES IN DATA PATH...")
        print(os.listdir(args.data_path))
        print("================")
        # prepare DataLoader for CIFAR10 data
        transform = transforms.Compose([
            transforms.ToTensor(),
            transforms.Normalize((0.5, 0.5, 0.5), (0.5, 0.5, 0.5))
        ])
        trainset = torchvision.datasets.CIFAR10(
            root=args.data_path,
            train=True,
            download=False,
            transform=transform,
        )
        trainloader = torch.utils.data.DataLoader(
            trainset,
            batch_size=4,
            shuffle=True,
            num_workers=2
        )
        # define convolutional network
        net = Net()
        # set up pytorch loss /  optimizer
        criterion = torch.nn.CrossEntropyLoss()
        optimizer = optim.SGD(
            net.parameters(),
            lr=args.learning_rate,
            momentum=args.momentum,
        )
        # train the network
        for epoch in range(2):
            running_loss = 0.0
            for i, data in enumerate(trainloader, 0):
                # unpack the data
                inputs, labels = data
                # zero the parameter gradients
                optimizer.zero_grad()
                # forward + backward + optimize
                outputs = net(inputs)
                loss = criterion(outputs, labels)
                loss.backward()
                optimizer.step()
                # print statistics
                running_loss += loss.item()
                if i % 2000 == 1999:
                    loss = running_loss / 2000
                    run.log('loss', loss)  # log loss metric to AML
                    print(f'epoch={epoch + 1}, batch={i + 1:5}: loss {loss:.2f}')
                    running_loss = 0.0
        print('Finished Training')
    ```

1. ファイルを **保存** します。  必要に応じてタブを閉じます。

### <a name="understanding-the-code-changes"></a>コードの変更について

`train.py` のコードでは、`argparse` ライブラリを使用して `data_path`、`learning_rate`、および `momentum` を設定しています。

```python
# .... other code
parser = argparse.ArgumentParser()
parser.add_argument('--data_path', type=str, help='Path to the training data')
parser.add_argument('--learning_rate', type=float, default=0.001, help='Learning rate for SGD')
parser.add_argument('--momentum', type=float, default=0.9, help='Momentum for SGD')
args = parser.parse_args()
# ... other code
```

また、`train.py` スクリプトを調整して、ユーザー定義のパラメーターを使用できるようにオプティマイザーを更新しました。

```python
optimizer = optim.SGD(
    net.parameters(),
    lr=args.learning_rate,     # get learning rate from command-line argument
    momentum=args.momentum,    # get momentum from command-line argument
)
```


## <a name="upload-the-data-to-azure"></a><a name="upload"></a> データを Azure にアップロードする

このスクリプトを Azure Machine Learning で実行するために、トレーニング データを Azure で使用できるようにする必要があります。 Azure Machine Learning ワークスペースには、 _既定_ のデータストアが用意されています。 これは、トレーニング データを格納できる Azure Blob Storage アカウントです。

>[!NOTE] 
> Azure Machine Learning を使用すると、自分のデータが格納される他のクラウドベースのデータストアに接続できます。 詳細については、[データストアに関するドキュメント](./concept-data.md)を参照してください。  

1. **get-started** フォルダー内に新しい Python 制御スクリプトを作成します ( **/src** フォルダー "*ではなく*"、**get-started** フォルダーです。注意してください)。  スクリプトに *upload-data.py* という名前を付け、このコードをファイルにコピーします。
    
    ```python
    # upload-data.py
    from azureml.core import Workspace
    ws = Workspace.from_config()
    datastore = ws.get_default_datastore()
    datastore.upload(src_dir='./data',
                     target_path='datasets/cifar10',
                     overwrite=True)
    
    ```

    `target_path` の値には、CIFAR10 データがアップロードされるデータストア上のパスを指定します。

    >[!TIP] 
    > Azure Machine Learning を使用してデータをアップロードしながら、[Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) を使用してアドホック ファイルをアップロードできます。 ETL ツールが必要な場合は、[Azure Data Factory](../data-factory/introduction.md) を使用して、データを Azure に取り込むことができます。

2. **[Save and run script in terminal]\(スクリプトを保存してターミナルで実行する\)** を選択して *upload-data.py* スクリプトを実行します。

    次の標準的な出力が表示されます。

    ```txt
    Uploading ./data\cifar-10-batches-py\data_batch_2
    Uploaded ./data\cifar-10-batches-py\data_batch_2, 4 files out of an estimated total of 9
    .
    .
    Uploading ./data\cifar-10-batches-py\data_batch_5
    Uploaded ./data\cifar-10-batches-py\data_batch_5, 9 files out of an estimated total of 9
    Uploaded 9 files
    ```

## <a name="create-a-control-script"></a><a name="control-script"></a> コントロール スクリプトを作成する

先ほどと同様、*run-pytorch-data.py* という新しい Python コントロール スクリプトを **get-started** フォルダーに作成します。

```python
# run-pytorch-data.py
from azureml.core import Workspace
from azureml.core import Experiment
from azureml.core import Environment
from azureml.core import ScriptRunConfig
from azureml.core import Dataset

if __name__ == "__main__":
    ws = Workspace.from_config()
    datastore = ws.get_default_datastore()
    dataset = Dataset.File.from_files(path=(datastore, 'datasets/cifar10'))

    experiment = Experiment(workspace=ws, name='day1-experiment-data')

    config = ScriptRunConfig(
        source_directory='./src',
        script='train.py',
        compute_target='cpu-cluster',
        arguments=[
            '--data_path', dataset.as_named_input('input').as_mount(),
            '--learning_rate', 0.003,
            '--momentum', 0.92],
    )

    # use curated pytorch environment 
    env = ws.environments['AzureML-PyTorch-1.3-CPU']
    config.run_config.environment = env

    run = experiment.submit(config)
    aml_url = run.get_portal_url()
    print("Submitted to compute cluster. Click link below")
    print("")
    print(aml_url)
```

> [!TIP]
> コンピューティング クラスターの作成時に別の名前を使用した場合は、`compute_target='cpu-cluster'` コード内の名前も調整してください。

### <a name="understand-the-code-changes"></a>コードの変更を理解する

このコントロール スクリプトは、[このシリーズの第 3 部](tutorial-1st-experiment-sdk-train.md)からのものによく似ていますが、次に示す新しい行が追加されています。

:::row:::
   :::column span="":::
      `dataset = Dataset.File.from_files( ... )`
   :::column-end:::
   :::column span="2":::
      [データセット](/python/api/azureml-core/azureml.core.dataset.dataset)は、Azure Blob Storage にアップロードしたデータを参照するために使用します。 データセットは、ご利用のデータの上にある抽象化レイヤーであり、信頼性および信頼度の向上を目的に設計されています。
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      `config = ScriptRunConfig(...)`
   :::column-end:::
   :::column span="2":::
      [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig) は、`train.py` に渡す引数のリストを含むように変更されています。 `dataset.as_named_input('input').as_mount()` 引数を使用すると、指定したディレクトリがコンピューティング先に _マウント_ されます。
   :::column-end:::
:::row-end:::

## <a name="submit-the-run-to-azure-machine-learning"></a><a name="submit-to-cloud"></a> Azure Machine Learning に実行を送信する

**[Save and run script in terminal]\(スクリプトを保存してターミナルで実行する\)** を選択して *run-pytorch-data.py* スクリプトを実行します。  この実行では、コンピューティング クラスターで、アップロードしたデータを使用してモデルをトレーニングします。

このコードにより Azure Machine Learning スタジオの実験に URL が出力されます。 そのリンクに移動すると、実行中の自分のコードを確認できます。

[!INCLUDE [amlinclude-info](../../includes/machine-learning-py38-ignore.md)]


### <a name="inspect-the-log-file"></a><a name="inspect-log"></a> ログ ファイルの確認

スタジオで、前の URL 出力を選択することによって実験の実行に移動し、 **[出力 + ログ]** を選択します。 `70_driver_log.txt` ファイルを選択します。 次の出力が見えるまでログ ファイルを下へスクロールします。

```txt
Processing 'input'.
Processing dataset FileDataset
{
  "source": [
    "('workspaceblobstore', 'datasets/cifar10')"
  ],
  "definition": [
    "GetDatastoreFiles"
  ],
  "registration": {
    "id": "XXXXX",
    "name": null,
    "version": null,
    "workspace": "Workspace.create(name='XXXX', subscription_id='XXXX', resource_group='X')"
  }
}
Mounting input to /tmp/tmp9kituvp3.
Mounted input to /tmp/tmp9kituvp3 as folder.
Exit __enter__ of DatasetContextManager
Entering Run History Context Manager.
Current directory:  /mnt/batch/tasks/shared/LS_root/jobs/dsvm-aml/azureml/tutorial-session-3_1600171983_763c5381/mounts/workspaceblobstore/azureml/tutorial-session-3_1600171983_763c5381
Preparing to call script [ train.py ] with arguments: ['--data_path', '$input', '--learning_rate', '0.003', '--momentum', '0.92']
After variable expansion, calling script [ train.py ] with arguments: ['--data_path', '/tmp/tmp9kituvp3', '--learning_rate', '0.003', '--momentum', '0.92']

Script type = None
===== DATA =====
DATA PATH: /tmp/tmp9kituvp3
LIST FILES IN DATA PATH...
['cifar-10-batches-py', 'cifar-10-python.tar.gz']
```

注意:

- Azure Machine Learning によって Blob Storage が自動的にコンピューティング クラスターにマウントされました。
- コントロール スクリプトで使用されている ``dataset.as_named_input('input').as_mount()`` は、マウント ポイントに解決されます。


## <a name="clean-up-resources"></a>リソースのクリーンアップ

引き続き別のチュートリアルに取り組む場合や、独自のトレーニングの実行に着手する場合は、「[次のステップ](#next-steps)」に進んでください。

### <a name="stop-compute-instance"></a>コンピューティング インスタンスを停止する

コンピューティング インスタンスをすぐに使用しない場合は、停止してください。

1. スタジオの左側にある **[コンピューティング]** を選択します。
1. 上部のタブで、 **[コンピューティング インスタンス]** を選択します
1. 一覧からコンピューティング インスタンスを選択します。
1. 上部のツールバーで、 **[停止]** を選択します。


### <a name="delete-all-resources"></a>すべてのリソースの削除

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

リソース グループは保持しつつ、いずれかのワークスペースを削除することもできます。 ワークスペースのプロパティを表示し、 **[削除]** を選択します。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、`Datastore` を使用して Azure にデータをアップロードする方法について説明しました。 データストアはワークスペース用のクラウド ストレージとして機能し、データを保持するための永続的で柔軟な場所を提供しました。

コマンドラインを介してデータ パスを受け付けるようにトレーニング スクリプトを変更する方法を説明しました。 `Dataset` を使用することで、ディレクトリをリモート実行にマウントできました。

モデルを用意できましたので、今度は次のことを学習します。

> [!div class="nextstepaction"]
> [Azure Machine Learning を使用してモデルをデプロイする方法](how-to-deploy-and-where.md)。
