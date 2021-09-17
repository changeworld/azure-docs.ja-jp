---
title: 'チュートリアル: 初めての Python 機械学習モデルをトレーニングする '
titleSuffix: Azure Machine Learning
description: Azure Machine Learning で機械学習モデルをトレーニングする方法。 これは、3 部構成の入門シリーズのパート 2 です。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: aminsaied
ms.author: amsaied
ms.reviewer: sgilley
ms.date: 08/18/2021
ms.custom: devx-track-python, contperf-fy21q3, FY21Q4-aml-seo-hack, contperf-fy21q
ms.openlocfilehash: f8ff5e9d5a7b35bcc4ada9fd600d28b54ead57a9
ms.sourcegitcommit: 9f1a35d4b90d159235015200607917913afe2d1b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/21/2021
ms.locfileid: "122634352"
---
# <a name="tutorial-train-your-first-machine-learning-model-part-2-of-3"></a>チュートリアル: 初めての機械学習モデルをトレーニングする (パート 2/3)

このチュートリアルでは、Azure Machine Learning で機械学習モデルをトレーニングする方法について説明します。  このチュートリアルは、*3 部構成のチュートリアル シリーズのパート 2* です。

 このシリーズの「[パート 1: "Hello World" の実行](tutorial-1st-experiment-hello-world.md)」 では、コントロール スクリプトを使用してクラウドでジョブを実行する方法について学習しました。  

このチュートリアルでは、機械学習モデルをトレーニングするスクリプトを送信して、次のステップに進みます。 この例は、Azure Machine Learning を使用することで、ローカル デバッグとリモート実行との間で一貫した動作がどのように容易になるかを理解するのに役立ちます。

このチュートリアルでは、次の作業を行いました。

> [!div class="checklist"]
> * トレーニング スクリプトを作成します。
> * Conda を使用して Azure Machine Learning 環境を定義する。
> * コントロール スクリプトを作成する。
> * Azure Machine Learning クラス (`Environment`、`Run`、`Metrics`) について理解する。
> * トレーニング スクリプトを送信して実行する。
> * クラウドでのコード出力を表示する。
> * メトリックを Azure Machine Learning にログする。
> * クラウドのメトリックを表示する。

## <a name="prerequisites"></a>前提条件

- このシリーズの[第 1 部](tutorial-1st-experiment-hello-world.md)を完了している。

## <a name="create-training-scripts"></a>トレーニング スクリプトを作成する

まず、*model.py* ファイルでニューラル ネットワーク アーキテクチャを定義します。 すべてのトレーニング コードは、`src` サブディレクトリ (*model.py* を含む) に入ります。

トレーニング コードは、PyTorch の[この入門の例](https://pytorch.org/tutorials/beginner/blitz/cifar10_tutorial.html)からの抜粋です。 Azure Machine Learning の概念は、PyTorch だけでなく、機械学習コードにも適用されることに注意してください。

1. **src** サブフォルダーに *model.py* ファイルを作成します。 そのファイルにこのコードをコピーします。

    ```python
    import torch.nn as nn
    import torch.nn.functional as F
    class Net(nn.Module):
        def __init__(self):
            super(Net, self).__init__()
            self.conv1 = nn.Conv2d(3, 6, 5)
            self.pool = nn.MaxPool2d(2, 2)
            self.conv2 = nn.Conv2d(6, 16, 5)
            self.fc1 = nn.Linear(16 * 5 * 5, 120)
            self.fc2 = nn.Linear(120, 84)
            self.fc3 = nn.Linear(84, 10)
        def forward(self, x):
            x = self.pool(F.relu(self.conv1(x)))
            x = self.pool(F.relu(self.conv2(x)))
            x = x.view(-1, 16 * 5 * 5)
            x = F.relu(self.fc1(x))
            x = F.relu(self.fc2(x))
            x = self.fc3(x)
            return x
    ```
1. ツール バーの **[保存]** を選択してファイルを保存します。  必要に応じてタブを閉じます。

1. 次に、同じ **src** サブフォルダーに、トレーニング スクリプトを定義します。 このスクリプトは、PyTorch `torchvision.dataset` API を使用して CIFAR10 データセットをダウンロードし、*model.py* で定義されているネットワークを設定して、標準の SGD とクロスエントロピ損失を使用して 2 つのエポックに対してトレーニングを行います。

    **src** サブフォルダーに *train.py* スクリプトを作成します。

    ```python
    import torch
    import torch.optim as optim
    import torchvision
    import torchvision.transforms as transforms
    
    from model import Net
    
    # download CIFAR 10 data
    trainset = torchvision.datasets.CIFAR10(
        root="../data",
        train=True,
        download=True,
        transform=torchvision.transforms.ToTensor(),
    )
    trainloader = torch.utils.data.DataLoader(
        trainset, batch_size=4, shuffle=True, num_workers=2
    )
    
    if __name__ == "__main__":
    
        # define convolutional network
        net = Net()
    
        # set up pytorch loss /  optimizer
        criterion = torch.nn.CrossEntropyLoss()
        optimizer = optim.SGD(net.parameters(), lr=0.001, momentum=0.9)
    
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
                    print(f"epoch={epoch + 1}, batch={i + 1:5}: loss {loss:.2f}")
                    running_loss = 0.0
    
        print("Finished Training")
    ```

1. フォルダー構造は次のようになります。

    :::image type="content" source="media/tutorial-1st-experiment-sdk-train/directory-structure.png" alt-text="src サブディレクトリの train.py を示すディレクトリ構造":::


## <a name="test-locally"></a><a name="test-local"></a> ローカルでテストする

**[Save and run script in terminal]\(スクリプトを保存してターミナルで実行する\)** を選択して、コンピューティング インスタンス上で直接 *train.py* スクリプトを実行します。

スクリプトの完了後、ファイル フォルダーの上にある **[Refresh]\(最新の情報に更新\)** を選択します。 **get-started/data** という新しいデータ フォルダーが表示されます。このフォルダーを展開すると、ダウンロードしたデータが表示されます。  

:::image type="content" source="media/tutorial-1st-experiment-hello-world/directory-with-data.png" alt-text="フォルダーのスクリーンショット。ファイルをローカルで実行することによって作成された新しい data フォルダーが表示されている。":::


## <a name="create-the-control-script"></a><a name="create-local"></a> コントロール スクリプトを作成する

下のコントロール スクリプトと "Hello World" の送信に使用したコントロール スクリプトとの違いは、 環境を設定するための行をいくつか追加することです。

**get-started** フォルダーに、`run-pytorch.py` という新しい Python ファイルを作成します。

```python
# run-pytorch.py
from azureml.core import Workspace
from azureml.core import Experiment
from azureml.core import Environment
from azureml.core import ScriptRunConfig

if __name__ == "__main__":
    ws = Workspace.from_config()
    experiment = Experiment(workspace=ws, name='day1-experiment-train')
    config = ScriptRunConfig(source_directory='./src',
                             script='train.py',
                             compute_target='cpu-cluster')

    # use curated pytorch environment 
    env = ws.environments['AzureML-PyTorch-1.6-CPU']
    config.run_config.environment = env

    run = experiment.submit(config)

    aml_url = run.get_portal_url()
    print(aml_url)
```

> [!TIP]
> コンピューティング クラスターの作成時に別の名前を使用した場合は、`compute_target='cpu-cluster'` コード内の名前も調整してください。

### <a name="understand-the-code-changes"></a>コードの変更を理解する

:::row:::
   :::column span="":::
      `env = ...`
   :::column-end:::
   :::column span="2":::
      Azure Machine Learning では、実験を実行するための、再現可能でバージョン管理された Python 環境を表す[環境](/python/api/azureml-core/azureml.core.environment.environment)の概念が提供されます。 ここでは、いずれかの[キュレーションされた環境](how-to-use-environments.md#use-a-curated-environment)を使用します。  ローカルの Conda 環境または pip 環境から環境を簡単に作成することもできます。
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      `config.run_config.environment = env`
   :::column-end:::
   :::column span="2":::
      [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig) に環境を追加します。
   :::column-end:::
:::row-end:::

## <a name="submit-the-run-to-azure-machine-learning"></a><a name="submit"></a> Azure Machine Learning に実行を送信する

1. **[Save and run script in terminal]\(スクリプトを保存してターミナルで実行する\)** を選択して *run-pytorch.py* スクリプトを実行します。

1. 開いたターミナル ウィンドウにリンクが表示されます。 リンクを選択して、実行を表示します。

    [!INCLUDE [amlinclude-info](../../includes/machine-learning-py38-ignore.md)]

### <a name="view-the-output"></a>出力を表示する

1. 開いたページに、実行の状態が表示されます。 このスクリプトを初めて実行すると、Azure Machine Learning によって PyTorch 環境から新しい Docker イメージが構築されます。 実行全体が完了するまでに 3 分から 4 分かかることがあります。  このイメージは、今後の実行で再利用され、実行がさらに高速化されます。
1. Docker ビルド ログは、Azure Machine Learning スタジオで確認できます。 **[出力 + ログ]** タブを選択し、**20_image_build_log.txt** を選択します。
1. 実行の状態が **[完了]** の場合、 **[出力 + ログ]** を選択します。
1. **70_driver_log.txt** を選択して、実行の出力を表示します。

```txt
Downloading https://www.cs.toronto.edu/~kriz/cifar-10-python.tar.gz to ./data/cifar-10-python.tar.gz
...
Files already downloaded and verified
epoch=1, batch= 2000: loss 2.19
epoch=1, batch= 4000: loss 1.82
epoch=1, batch= 6000: loss 1.66
epoch=1, batch= 8000: loss 1.58
epoch=1, batch=10000: loss 1.52
epoch=1, batch=12000: loss 1.47
epoch=2, batch= 2000: loss 1.39
epoch=2, batch= 4000: loss 1.38
epoch=2, batch= 6000: loss 1.37
epoch=2, batch= 8000: loss 1.33
epoch=2, batch=10000: loss 1.31
epoch=2, batch=12000: loss 1.27
Finished Training
```

"`Your total snapshot size exceeds the limit` (合計スナップショット サイズが上限を超えました)" というエラーが表示された場合、`ScriptRunConfig` で使用されている値 `source_directory` に **data** フォルダーが存在します。

フォルダーの末尾にある **[...]** を選択し、 **[移動]** を選択して、**data** を **get-started** フォルダーに移動します。  



## <a name="log-training-metrics"></a><a name="log"></a> トレーニング メトリックをログする

これで Azure Machine Learning でのモデル トレーニングが完了したので、いくつかのパフォーマンス メトリックの追跡を開始します。

現在のトレーニング スクリプトは、メトリックをターミナルに出力します。 Azure Machine Learning には、より多くの機能を備えたメトリックをログするためのメカニズムが用意されています。 数行のコードを追加することで、スタジオでメトリックを視覚化したり、複数の実行間でメトリックを比較したりできるようになります。

### <a name="modify-trainpy-to-include-logging"></a>*train.py* にログ機能を追加する

1. *train.py* スクリプトに 2 行のコードを追加します。
    
    ```python
    import torch
    import torch.optim as optim
    import torchvision
    import torchvision.transforms as transforms
    from model import Net
    from azureml.core import Run
    # ADDITIONAL CODE: get run from the current context
    run = Run.get_context()
    # download CIFAR 10 data
    trainset = torchvision.datasets.CIFAR10(
        root='./data',
        train=True,
        download=True,
        transform=torchvision.transforms.ToTensor()
    )
    trainloader = torch.utils.data.DataLoader(
        trainset,
        batch_size=4,
        shuffle=True,
        num_workers=2
    )
    if __name__ == "__main__":
        # define convolutional network
        net = Net()
        # set up pytorch loss /  optimizer
        criterion = torch.nn.CrossEntropyLoss()
        optimizer = optim.SGD(net.parameters(), lr=0.001, momentum=0.9)
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
                    # ADDITIONAL CODE: log loss metric to AML
                    run.log('loss', loss)
                    print(f'epoch={epoch + 1}, batch={i + 1:5}: loss {loss:.2f}')
                    running_loss = 0.0
        print('Finished Training')
    ```

2. このファイルを **保存** し、必要に応じてタブを閉じます。

#### <a name="understand-the-additional-two-lines-of-code"></a>追加する 2 行のコードを理解する

*train.py* で、`Run.get_context()` メソッドを使用してトレーニング スクリプト自体の "_内部_" から実行オブジェクトにアクセスし、それを使用してメトリックをログします。

```python
# ADDITIONAL CODE: get run from the current context
run = Run.get_context()

...
# ADDITIONAL CODE: log loss metric to AML
run.log('loss', loss)
```

Azure Machine Learning のメトリックは次のとおりです。

- 実験ごとに整理されて実行されるため、メトリックの追跡と比較が簡単です。
- UI を備えているため、スタジオでトレーニングのパフォーマンスを視覚化できます。
- スケーリングするように設計されているため、数百回の実験を実行してもこれらのベネフィットを維持できます。

### <a name="submit-the-run-to-azure-machine-learning"></a><a name="submit-again"></a> Azure Machine Learning に実行を送信する

*run-pytorch.py* スクリプトのタブを選択し、 **[Save and run script in terminal]\(スクリプトを保存してターミナルで実行する\)** を選択して、*run-pytorch.py* スクリプトを再実行します。 

今回は、スタジオにアクセスしたら、 **[メトリック]** タブに移動します。このタブで、モデル トレーニングの損失に関するライブ更新を確認できます。 トレーニングが開始されるまでに 1 分から 2 分かかる場合があります。  

:::image type="content" source="media/tutorial-1st-experiment-sdk-train/logging-metrics.png" alt-text="[メトリック] タブのトレーニング損失グラフ":::

## <a name="next-steps"></a>次の手順

このセッションでは、基本的な "Hello world!" スクリプトから、 特定の Python 環境を実行する必要がある、より現実的なトレーニング スクリプトにアップグレードしました。 キュレーションされた Azure Machine Learning 環境を使用する方法について確認しました。 最後に、数行のコードでメトリックを Azure Machine Learning にログする方法を確認しました。

Azure Machine Learning 環境を作成する方法は他にもあります。たとえば、[pip requirements.txt ファイルから](/python/api/azureml-core/azureml.core.environment.environment#from-pip-requirements-name--file-path-)、または[既存のローカル Conda 環境から](/python/api/azureml-core/azureml.core.environment.environment#from-existing-conda-environment-name--conda-environment-name-)作成することができます。

次のセッションでは、CIFAR10 データセットを Azure にアップロードして Azure Machine Learning のデータを操作する方法について説明します。

> [!div class="nextstepaction"]
> [チュートリアル:データ持ち込み](tutorial-1st-experiment-bring-data.md)

>[!NOTE] 
> チュートリアル シリーズをここで終了し、次の手順に進まない場合は、必ず[リソースをクリーンアップ](tutorial-1st-experiment-bring-data.md#clean-up-resources)してください。
