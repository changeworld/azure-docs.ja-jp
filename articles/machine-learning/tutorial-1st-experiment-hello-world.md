---
title: 'チュートリアル: Python スクリプトを使ってみる'
titleSuffix: Azure Machine Learning
description: Azure Machine Learning で Python スクリプトを初めて使ってみます。 これは、3 部構成の入門シリーズのパート 1 です。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: aminsaied
ms.author: amsaied
ms.reviewer: sgilley
ms.date: 04/27/2021
ms.custom: devx-track-python, FY21Q4-aml-seo-hack, contperf-fy21q4
ms.openlocfilehash: 472fcf4c7a1cc486db5aded40c87ffec2a9e796a
ms.sourcegitcommit: 9f1a35d4b90d159235015200607917913afe2d1b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/21/2021
ms.locfileid: "122634695"
---
# <a name="tutorial-get-started-with-a-python-script-in-azure-machine-learning-part-1-of-3"></a>チュートリアル: Azure Machine Learning で Python スクリプトを使ってみる (パート 1/3)

このチュートリアルでは、Azure Machine Learning を使用して、初めての Python スクリプトをクラウドで実行します。 このチュートリアルは、"*3 部構成のチュートリアル シリーズのパート 1*" です。

このチュートリアルでは、機械学習モデルのトレーニングの複雑さを回避しています。 クラウドで、"Hello World" Python スクリプトを実行します。 Azure Machine Learning で実行を構成して作成するために、コントロール スクリプトを使用する方法について説明します。

このチュートリアルでは、次のことについて説明します。

> [!div class="checklist"]
> * "Hello world!" Python スクリプトをローカルで作成して実行する。 (訳文なし)
> * Azure Machine Learning に "Hello world" を送信する Python コントロール スクリプトを作成する。 (訳文なし)
> * コントロール スクリプトでの Azure Machine Learning の概念を理解する。
> * "Hello World" スクリプトを送信して実行する  スクリプトを入手してください。
> * クラウドでのコード出力を表示する。

## <a name="prerequisites"></a>前提条件

- [Azure Machine Learning を使い始めるためのワークスペースのセットアップ方法を紹介したクイックスタート](quickstart-create-resources.md)に従って、このチュートリアル シリーズで使用するワークスペース、コンピューティング インスタンス、コンピューティング クラスターを作成します。

## <a name="create-and-run-a-python-script"></a>Python スクリプトを作成して実行する

このチュートリアルでは、コンピューティング インスタンスを開発用コンピューターとして使用します。  まず、いくつかのフォルダーとスクリプトを作成します。

1. [Azure Machine Learning スタジオ](https://ml.azure.com)にサインインし、確認を求められたら、ご使用のワークスペースを選択します。
1. 左側にある **[ノートブック]** を選択します
1. **[ファイル]** ツール バーの **[+]** を選択し、 **[新しいフォルダーの作成]** を選択します。
  :::image type="content" source="media/tutorial-1st-experiment-hello-world/create-folder.png" alt-text="ツール バーの [新しいフォルダーの作成] ツールを示すスクリーンショット。":::
1. このフォルダーに **get-started** という名前を付けます。
1. フォルダー名の右側にある **[...]** を使用して、**get-started** に別のフォルダーを作成します。
  :::image type="content" source="media/tutorial-1st-experiment-hello-world/create-sub-folder.png" alt-text="サブフォルダーの作成メニューを示すスクリーンショット。":::
1. 新しいフォルダーに **src** という名前を付けます。  ファイルの場所が正しくない場合は、 **[場所の編集]** リンクを使用します。
1. **src** フォルダーの右側にある **[...]** を使用して、**src** フォルダーに新しいファイルを作成します。 
1. そのファイルに *hello.py* という名前を付けます。  **[ファイルの種類]** を *Python (* .py)* に切り替えます。

このコードをファイルにコピーします。

```python
# src/hello.py
print("Hello world!")
```

プロジェクトのフォルダー構造は次のようになります。 

:::image type="content" source="media/tutorial-1st-experiment-hello-world/directory-structure.png" alt-text="src サブフォルダー内の hello.py を示すフォルダー構造。":::


### <a name="test-your-script"></a><a name="test"></a>スクリプトをテストする

コードはローカル (このケースではコンピューティング インスタンス) で実行することができます。 コードのローカル実行には、コードの対話型デバッグができるというメリットがあります。  

既にコンピューティング インスタンスを停止してある場合は、[コンピューティング] ドロップダウンの右側にある **[コンピューティングの開始]** ツールを使用して起動してください。 "*実行中*" の状態に変わるまで 1 分ほど待ちます。

:::image type="content" source="media/tutorial-1st-experiment-hello-world/start-compute.png" alt-text="停止しているコンピューティング インスタンスを起動する画面のスクリーンショット":::

**[Save and run script in terminal]\(スクリプトを保存してターミナルで実行する\)** を選択してスクリプトを実行します。

:::image type="content" source="media/tutorial-1st-experiment-hello-world/save-run-in-terminal.png" alt-text="ツール バーの [Save and run script in terminal]\(スクリプトを保存してターミナルで実行する\) ツールを示すスクリーンショット":::

表示されたターミナル ウィンドウにスクリプトの出力が表示されます。 タブを閉じ、 **[終了]** を選択してセッションを終了します。

## <a name="create-a-control-script"></a><a name="control-script"></a> コントロール スクリプトを作成する

"*コントロール スクリプト*" を使用すると、`hello.py` スクリプトを別のコンピューティング リソースで実行することができます。 コントロール スクリプトを使用すると、機械学習コードを実行する方法と場所を制御できます。  

**[get-started]** フォルダーの末尾にある **[...]** を選択して新しいファイルを作成します。  *run-hello.py* という Python ファイルを作成し、次のコードをコピーしてそのファイルに貼り付けます。

```python
# get-started/run-hello.py
from azureml.core import Workspace, Experiment, Environment, ScriptRunConfig

ws = Workspace.from_config()
experiment = Experiment(workspace=ws, name='day1-experiment-hello')

config = ScriptRunConfig(source_directory='./src', script='hello.py', compute_target='cpu-cluster')

run = experiment.submit(config)
aml_url = run.get_portal_url()
print(aml_url)
```

> [!TIP]
> コンピューティング クラスターの作成時に別の名前を使用した場合は、`compute_target='cpu-cluster'` コード内の名前も調整してください。

### <a name="understand-the-code"></a>コードの理解

ここでは、コントロール スクリプトのしくみについて説明します。

:::row:::
   :::column span="":::
      `ws = Workspace.from_config()`
   :::column-end:::
   :::column span="2":::
      [Workspace](/python/api/azureml-core/azureml.core.workspace.workspace) は Azure Machine Learning ワークスペースに接続して、Azure Machine Learning リソースと通信できるようにします。
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      `experiment =  Experiment( ... )`
   :::column-end:::
   :::column span="2":::
      [Experiment](/python/api/azureml-core/azureml.core.experiment.experiment) は、複数の実行を 1 つの名前で整理する簡単な方法を提供します。 後で実験を使用して、多数の実行の間でメトリックを簡単に比較する方法を確認できます。
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      `config = ScriptRunConfig( ... )` 
   :::column-end:::
   :::column span="2":::
      [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig) は、`hello.py` コードをラップしてワークスペースに渡します。 名前に示されているように、このクラスを使用して、Azure Machine Learning で _スクリプト_ を _実行_ する方法を _構成_ できます。 スクリプトを実行するコンピューティング ターゲットも指定します。 このコードでは、ターゲットは[セットアップ チュートリアル](./quickstart-create-resources.md)で作成したコンピューティング クラスターです。
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      `run = experiment.submit(config)`
   :::column-end:::
   :::column span="2":::
       スクリプトを送信します。 この送信は[実行](/python/api/azureml-core/azureml.core.run%28class%29)と呼ばれます。 実行には、コードの 1 回の実行がカプセル化されています。 実行を使用して、スクリプトの進行状況を監視したり、出力をキャプチャしたり、結果を分析したり、メトリックを視覚化したりすることができます。
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      `aml_url = run.get_portal_url()` 
   :::column-end:::
   :::column span="2":::
        `run` オブジェクトは、コードの実行に対するハンドルを提供します。 Python スクリプトから出力された URL を使用して、進行状況を Azure Machine Learning スタジオから監視します。  
   :::column-end:::
:::row-end:::


## <a name="submit-and-run-your-code-in-the-cloud"></a><a name="submit"></a> クラウドでコードを送信して実行する

1. **[Save and run script in terminal]\(スクリプトを保存してターミナルで実行する\)** を選択してコントロール スクリプトを実行すると、[セットアップ チュートリアル](quickstart-create-resources.md)で作成したコンピューティング クラスターで `hello.py` が実行されます。

1. ターミナルで、認証のためにサインインを求められる場合があります。  コードをコピーし、リンクをクリックしてこの手順を完了してください。

1. 認証されると、ターミナルにリンクが表示されます。 リンクを選択して、実行を表示します。

    [!INCLUDE [amlinclude-info](../../includes/machine-learning-py38-ignore.md)]

## <a name="view-the-output"></a>出力を表示する

1. 開いたページに、実行の状態が表示されます。
1. 実行の状態が **[完了]** の場合、ページの上部にある **[出力 + ログ]** を選択します。
1. **70_driver_log.txt** を選択して、実行の出力を表示します。

## <a name="monitor-your-code-in-the-cloud-in-the-studio"></a><a name="monitor"></a>スタジオを使用してクラウドでコードを監視する

スクリプトからの出力には、`https://ml.azure.com/experiments/hello-world/runs/<run-id>?wsid=/subscriptions/<subscription-id>/resourcegroups/<resource-group>/workspaces/<workspace-name>` のようなスタジオへのリンクが含まれています。

リンク先に移動します。  最初は、状態が "**Queued (キュー登録済み)** " または "**Preparing (準備中)** " と表示されます。  最初の実行は、完了までに 5 分から 10 分かかります。 これは、以下のことが行われるためです。

* クラウドで Docker イメージが構築されます
* コンピューティング クラスターのサイズが 0 ノードから 1 ノードに変更されます
* Docker イメージがコンピューティングにダウンロードされます。 

Docker イメージがコンピューティングにキャッシュされるため、それ以降の実行にかかる時間は大幅に短縮されます (約 15 秒)。 これをテストするには、最初の実行が完了した後に、以下のコードを再送信します。

10 分ほどお待ちください。  実行が完了したことを示すメッセージが表示されます。 その後、 **[Refresh]\(最新の情報に更新\)** を使用すると、状態が "*Completed (完了)* " に変わることがわかります。  ジョブが完了したら、 **[出力 + ログ]** タブに移動します。次のような `70_driver_log.txt` ファイルが表示されます。

```txt
 1: [2020-08-04T22:15:44.407305] Entering context manager injector.
 2: [context_manager_injector.py] Command line Options: Namespace(inject=['ProjectPythonPath:context_managers.ProjectPythonPath', 'RunHistory:context_managers.RunHistory', 'TrackUserError:context_managers.TrackUserError', 'UserExceptions:context_managers.UserExceptions'], invocation=['hello.py'])
 3: Starting the daemon thread to refresh tokens in background for process with pid = 31263
 4: Entering Run History Context Manager.
 5: Preparing to call script [ hello.py ] with arguments: []
 6: After variable expansion, calling script [ hello.py ] with arguments: []
 7:
 8: Hello world!
 9: Starting the daemon thread to refresh tokens in background for process with pid = 31263
10:
11:
12: The experiment completed successfully. Finalizing run...
13: Logging experiment finalizing status in history service.
14: [2020-08-04T22:15:46.541334] TimeoutHandler __init__
15: [2020-08-04T22:15:46.541396] TimeoutHandler __enter__
16: Cleaning up all outstanding Run operations, waiting 300.0 seconds
17: 1 items cleaning up...
18: Cleanup took 0.1812913417816162 seconds
19: [2020-08-04T22:15:47.040203] TimeoutHandler __exit__
```

8 行目には、"Hello world!" の出力が表示されます。

`70_driver_log.txt` ファイルには、実行からの標準出力が含まれます。 このファイルは、クラウドでのリモート実行をデバッグするときに役立ちます。


## <a name="next-steps"></a>次の手順

このチュートリアルでは、単純な "Hello world!"  スクリプトを作成し、Azure で実行しました。 Azure Machine Learning ワークスペースに接続し、実験を作成して、`hello.py` コードをクラウドに送信する方法について説明しました。

次のチュートリアルでは、`print("Hello world!")` よりも興味深いものを実行して、これまでの学習をさらに発展させます。

> [!div class="nextstepaction"]
> [チュートリアル: モデルのトレーニング](tutorial-1st-experiment-sdk-train.md)

>[!NOTE] 
> チュートリアル シリーズをここで終了し、次の手順に進まない場合は、必ず[リソースをクリーンアップ](tutorial-1st-experiment-bring-data.md#clean-up-resources)してください。