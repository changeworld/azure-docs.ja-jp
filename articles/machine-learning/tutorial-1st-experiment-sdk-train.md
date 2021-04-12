---
title: チュートリアル:初めての機械学習モデルをトレーニングする - Python
titleSuffix: Azure Machine Learning
description: Azure Machine Learning 入門シリーズの第 3 部では、機械学習モデルをトレーニングする方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: aminsaied
ms.author: amsaied
ms.reviewer: sgilley
ms.date: 02/11/2021
ms.custom: devx-track-python, contperf-fy21q3
ms.openlocfilehash: 7e035406ce220728c316aaa2005e0f4d6089b5e2
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "105935517"
---
# <a name="tutorial-train-your-first-machine-learning-model-part-3-of-4"></a>チュートリアル:初めての機械学習モデルをトレーニングする (パート 3/4)

このチュートリアルでは、Azure Machine Learning で機械学習モデルをトレーニングする方法について説明します。

このチュートリアルは、"*4 部構成のチュートリアル シリーズのパート 3* " であり、Azure Machine Learning の基礎を学習し、Azure でジョブベースの機械学習タスクを実行します。 このチュートリアルは次のパートで完了した作業を基にしています。このシリーズの[第 1 部: 設定](tutorial-1st-experiment-sdk-setup-local.md)と[第 2 部:"Hello world!" の実行](tutorial-1st-experiment-hello-world.md) です。

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

- Python の仮想環境を管理してパッケージをインストールするための [Anaconda](https://www.anaconda.com/download/) または [Miniconda](https://www.anaconda.com/download/)。
- このシリーズの[第 1 部](tutorial-1st-experiment-sdk-setup-local.md)と[第 2 部](tutorial-1st-experiment-hello-world.md)を完了している。

## <a name="create-training-scripts"></a>トレーニング スクリプトを作成する

まず、`model.py` ファイルでニューラル ネットワーク アーキテクチャを定義します。 すべてのトレーニング コードは、`src` サブディレクトリ (`model.py` を含む) に入ります。

次のコードは、PyTorch の[この入門の例](https://pytorch.org/tutorials/beginner/blitz/cifar10_tutorial.html)からの抜粋です。 Azure Machine Learning の概念は、PyTorch だけでなく、機械学習コードにも適用されることに注意してください。

:::code language="python" source="~/MachineLearningNotebooks/tutorials/get-started-day1/IDE-users/src/model.py":::

次に、トレーニング スクリプトを定義します。 このスクリプトは、PyTorch `torchvision.dataset` API を使用して CIFAR10 データセットをダウンロードし、`model.py` で定義されているネットワークを設定して、標準の SGD とクロスエントロピ損失を使用して 2 つのエポックに対してトレーニングを行います。

`src` サブディレクトリに `train.py` スクリプトを作成します。

:::code language="python" source="~/MachineLearningNotebooks/tutorials/get-started-day1/IDE-users/src/train.py":::

これで、次のディレクトリ構造ができました。

:::image type="content" source="media/tutorial-1st-experiment-sdk-train/directory-structure.png" alt-text="src サブディレクトリの train.py を示すディレクトリ構造":::


> [!div class="nextstepaction"]
> [トレーニング スクリプトを作成しました](?success=create-scripts#environment) [問題が発生しました](https://www.research.net/r/7CTJQQN?issue=create-scripts)

## <a name="create-a-new-python-environment"></a><a name="environment"></a> 新しい Python 環境を作成する

隠しディレクトリ `.azureml` に `pytorch-env.yml` という名前のファイルを作成します。

:::code language="yml" source="~/MachineLearningNotebooks/tutorials/get-started-day1/IDE-users/environments/pytorch-env.yml":::

この環境には、モデルとトレーニング スクリプトに必要なすべての依存関係があります。 Azure Machine Learning SDK for Python には依存関係がないことに注意してください。

> [!div class="nextstepaction"]
> [環境ファイルを作成しました](?success=create-env-file#test-local) [問題が発生しました](https://www.research.net/r/7CTJQQN?issue=create-env-file)

## <a name="test-locally"></a><a name="test-local"></a> ローカルでテストする

ターミナルまたは Anaconda プロンプト ウィンドウから次のコードを使用して、新しい環境のローカルでスクリプトをテストします。  

```bash
conda deactivate                                # If you are still using the tutorial environment, exit it
conda env create -f .azureml/pytorch-env.yml    # create the new Conda environment
conda activate pytorch-env                      # activate new Conda environment
python src/train.py                             # train model
```

このスクリプトを実行すると、`tutorial/data` という名前のディレクトリにダウンロードされたデータが表示されます。

> [!div class="nextstepaction"]
> [コードをローカルで実行しました](?success=test-local#create-local) [問題が発生しました](https://www.research.net/r/7CTJQQN?issue=test-local)

## <a name="create-the-control-script"></a><a name="create-local"></a> コントロール スクリプトを作成する

下のコントロール スクリプトと "Hello World" の送信に使用したコントロール スクリプトとの違いは、 環境を設定するための行をいくつか追加することです。

`tutorial` ディレクトリに `04-run-pytorch.py` という名前の新しい Python ファイルを作成します。

```python
# 04-run-pytorch.py
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

    # set up pytorch environment
    env = Environment.from_conda_specification(
        name='pytorch-env',
        file_path='./.azureml/pytorch-env.yml'
    )
    config.run_config.environment = env

    run = experiment.submit(config)

    aml_url = run.get_portal_url()
    print(aml_url)
```    
    
### <a name="understand-the-code-changes"></a>コードの変更を理解する

:::row:::
   :::column span="":::
      `env = ...`
   :::column-end:::
   :::column span="2":::
      Azure Machine Learning では、実験を実行するための、再現可能でバージョン管理された Python 環境を表す[環境](/python/api/azureml-core/azureml.core.environment.environment)の概念が提供されます。 ローカルの Conda 環境または pip 環境から環境を簡単に作成できます。
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

> [!div class="nextstepaction"]
> [コントロール スクリプトを作成しました](?success=control-script#submit) [問題が発生しました](https://www.research.net/r/7CTJQQN?issue=control-script)


## <a name="submit-the-run-to-azure-machine-learning"></a><a name="submit"></a> Azure Machine Learning に実行を送信する

Azure Machine Learning SDK for Python がインストールされている *tutorial* 環境に再び切り替えてください。 トレーニング コードが自分のコンピューターで実行されるわけではないので、PyTorch をインストールする必要はありません。  ただし、*tutorial* 環境に含まれている `azureml-sdk` は必要です。

```bash
conda deactivate
conda activate tutorial
python 04-run-pytorch.py
```

>[!NOTE] 
> このスクリプトを初めて実行すると、Azure Machine Learning によって PyTorch 環境から新しい Docker イメージが構築されます。 実行全体が完了するまでに 5 分から 10 分かかることがあります。 
>
> Docker ビルド ログは、Azure Machine Learning スタジオで確認できます。 リンクをたどってスタジオにアクセスし、 **[出力 + ログ]** タブを選択して、`20_image_build_log.txt` を選択します。
>
> このイメージは、今後の実行で再利用され、実行がさらに高速化されます。

イメージがビルドされたら、`70_driver_log.txt` を選択してトレーニング スクリプトの出力を表示します。

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

> [!WARNING]
> "`Your total snapshot size exceeds the limit`" というエラーが表示された場合、`ScriptRunConfig` で使用されている値 `source_directory` に `data` ディレクトリが存在します。
>
> `data` を `src` の外に移動してください。

環境は `env.register(ws)` を使用してワークスペースに登録できます。 そのため、共有、再利用、バージョン管理が簡単にできます。 環境を使用すると、以前の結果の再現や、チームとの共同作業が容易に行えます。

Azure Machine Learning では、キュレーションされた環境のコレクションも保持されます。 これらの環境は、一般的な機械学習のシナリオを対象とし、キャッシュされた Docker イメージによってサポートされています。 キャッシュされた Docker イメージによって、最初のリモート実行が高速になります。

つまり、登録済み環境を使用すると時間を節約できます。 詳細については、[環境の使用方法](./how-to-use-environments.md)に関するページを参照してください。

> [!div class="nextstepaction"]
> [実行を送信しました](?success=test-w-environment#log) [問題が発生しました](https://www.research.net/r/7CTJQQN?issue=test-w-environment)

## <a name="log-training-metrics"></a><a name="log"></a> トレーニング メトリックをログする

これで Azure Machine Learning でのモデル トレーニングが完了したので、いくつかのパフォーマンス メトリックの追跡を開始します。

現在のトレーニング スクリプトは、メトリックをターミナルに出力します。 Azure Machine Learning には、より多くの機能を備えたメトリックをログするためのメカニズムが用意されています。 数行のコードを追加することで、スタジオでメトリックを視覚化したり、複数の実行間でメトリックを比較したりできるようになります。

### <a name="modify-trainpy-to-include-logging"></a>ログを含めるように `train.py` を変更する

`train.py` スクリプトを変更して、2 行のコードを追加します。

:::code language="python" source="~/MachineLearningNotebooks/tutorials/get-started-day1/code/pytorch-cifar10-train-with-logging/train.py":::


#### <a name="understand-the-additional-two-lines-of-code"></a>追加する 2 行のコードを理解する

`train.py` で、`Run.get_context()` メソッドを使用してトレーニング スクリプト自体の "_内部_" から実行オブジェクトにアクセスし、それを使用してメトリックをログします。

```python
# in train.py
run = Run.get_context()

...

run.log('loss', loss)
```

Azure Machine Learning のメトリックは次のとおりです。

- 実験ごとに整理されて実行されるため、メトリックの追跡と比較が簡単です。
- UI を備えているため、スタジオでトレーニングのパフォーマンスを視覚化できます。
- スケーリングするように設計されているため、数百回の実験を実行してもこれらのベネフィットを維持できます。

> [!div class="nextstepaction"]
> [train.py に変更を加えました](?success=modify-train#log) [問題が発生しました](https://www.research.net/r/7CTJQQN?issue=modify-train)

### <a name="update-the-conda-environment-file"></a>Conda 環境ファイルを更新する

`train.py` スクリプトにより、`azureml.core` に対して新しい依存関係が作成されました。 この変更を反映するように `pytorch-env.yml` を更新します。

:::code language="python" source="~/MachineLearningNotebooks/tutorials/get-started-day1/configuration/pytorch-aml-env.yml":::

> [!div class="nextstepaction"]
> [環境ファイルを更新しました](?success=update-environment#submit-again) [問題が発生しました](https://www.research.net/r/7CTJQQN?issue=update-environment)

### <a name="submit-the-run-to-azure-machine-learning"></a><a name="submit-again"></a> Azure Machine Learning に実行を送信する
このスクリプトをもう一度送信します。

```bash
python 04-run-pytorch.py
```

今回は、スタジオにアクセスしたら、 **[メトリック]** タブに移動します。このタブで、モデル トレーニングの損失に関するライブ更新を確認できます。

:::image type="content" source="media/tutorial-1st-experiment-sdk-train/logging-metrics.png" alt-text="[メトリック] タブのトレーニング損失グラフ":::

> [!div class="nextstepaction"]
> [実行を再送信しました](?success=resubmit-with-logging#next-steps) [問題が発生しました](https://www.research.net/r/7CTJQQN?issue=resubmit-with-logging)

## <a name="next-steps"></a>次の手順

このセッションでは、基本的な "Hello world!" スクリプトから、 特定の Python 環境を実行する必要がある、より現実的なトレーニング スクリプトにアップグレードしました。 Azure Machine Learning 環境を使用してローカルの Conda 環境をクラウドに移動する方法を確認しました。 最後に、数行のコードでメトリックを Azure Machine Learning にログする方法を確認しました。

Azure Machine Learning 環境を作成する方法は他にもあります。たとえば、[pip requirements.txt ファイルから](/python/api/azureml-core/azureml.core.environment.environment#from-pip-requirements-name--file-path-)、または[既存のローカル Conda 環境から](/python/api/azureml-core/azureml.core.environment.environment#from-existing-conda-environment-name--conda-environment-name-)作成することができます。

次のセッションでは、CIFAR10 データセットを Azure にアップロードして Azure Machine Learning のデータを操作する方法について説明します。

> [!div class="nextstepaction"]
> [チュートリアル:データ持ち込み](tutorial-1st-experiment-bring-data.md)

>[!NOTE] 
> チュートリアル シリーズをここで終了し、次の手順に進まない場合は、必ず[リソースをクリーンアップ](tutorial-1st-experiment-bring-data.md#clean-up-resources)してください。
