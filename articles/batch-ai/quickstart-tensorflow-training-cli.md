---
title: Azure クイック スタート - ディープ ラーニングのトレーニング - Azure CLI | Microsoft Docs
description: クイック スタート - Azure CLI を使用し、Batch AI を使って 1 つの GPU 上で TensorFlow ディープ ラーニング ニューラル ネットワークをトレーニングする方法について簡単に説明します。
services: batch-ai
documentationcenter: na
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.custom: ''
ms.service: batch-ai
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: CLI
ms.topic: quickstart
ms.date: 09/03/2018
ms.author: danlep
ROBOTS: NOINDEX
ms.openlocfilehash: 979253021f5503295e0572759b510e074ceb1a6b
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/14/2018
ms.locfileid: "53408070"
---
# <a name="quickstart-train-a-deep-learning-model-with-batch-ai"></a>クイック スタート:Batch AI を使用してディープ ラーニング モデルをトレーニングする

[!INCLUDE [batch-ai-retiring](../../includes/batch-ai-retiring.md)]

このクイック スタートでは、Batch AI によって管理されている GPU 対応仮想マシンでサンプル ディープ ラーニング モデルをトレーニングする方法について説明します。 Batch AI は、データ サイエンティストや AI 研究者が、Azure 仮想マシンのクラスター上で、AI および機械学習モデルを大規模にトレーニングするためのマネージド サービスです。 

この例では、Azure CLI を使用して、手書き数字の [MNIST データベース](http://yann.lecun.com/exdb/mnist/)で [TensorFlow](https://www.tensorflow.org/) サンプル ニューラル ネットワークをトレーニングする Batch AI を設定します。 このクイック スタートを完了すると、Batch AI を使用した AI または機械学習モデルのトレーニングの主要概念を理解し、さまざまなモデルをより大規模にトレーニングする準備が整います。

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

CLI をローカルにインストールして使用する場合、このクイック スタートでは、Azure CLI バージョン 2.0.38 以降を実行している必要があります。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール](/cli/azure/install-azure-cli)に関するページを参照してください。 

このクイック スタートでは、Cloud Shell 内またはローカル コンピューター上の Bash シェルでコマンドを実行することを前提としています。 [Azure CLI を使用して Batch AI クラスターを作成する](quickstart-create-cluster-cli.md)クイック スタートを既に完了している場合は、リソース グループと Batch AI クラスターを作成する最初の 2 つの手順をスキップしてください。

## <a name="create-a-resource-group"></a>リソース グループの作成

`az group create` コマンドでリソース グループを作成します。 Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。 

次の例では、*myResourceGroup* という名前のリソース グループを *eastus2* に作成します。 米国東部 2 を選択するか、Batch AI サービスが利用可能な別の場所を選択してください。 

```azurecli-interactive 
az group create \
    --name myResourceGroup \
    --location eastus2
```

## <a name="create-a-batch-ai-cluster"></a>Batch AI クラスターを作成する

まず、`az batchai workspace create` コマンドを使用して、Batch AI "*ワークスペース*" を作成します。 Batch AI クラスターや他のリソースを整理するためのワークスペースが必要です。

```azurecli-interactive
az batchai workspace create \
    --workspace myworkspace \
    --resource-group myResourceGroup
```

Batch AI クラスターを作成するには、`az batchai cluster create` コマンドを使用します。 次の例では、次のプロパティを持つ 1 ノード クラスターを作成します。

* NVIDIA Tesla K80 GPU を 1 つ備えた、NC6 VM サイズを使用します。 Azure には、さまざまな NVIDIA GPU を備えた複数の VM サイズが用意されています。
* コンテナー ベースのアプリケーションをホストするように設計された既定の Ubuntu Server イメージを実行します。 このディストリビューションでは、ほとんどのトレーニング ワークロードを実行できます。 
* *myusername* という名前のユーザー アカウントを追加し、SSH キーを生成します (ローカル環境の既定のキーの場所 (*~/.ssh*) にまだ存在しない場合)。

```azurecli-interactive
az batchai cluster create \
    --name mycluster \
    --workspace myworkspace \
    --resource-group myResourceGroup \
    --vm-size Standard_NC6 \
    --target 1 \
    --user-name myusername \
    --generate-ssh-keys
```

コマンド出力には、クラスターのプロパティが示されます。 ノードを作成して起動するまで数分かかります。 クラスターの状態を確認するには、`az batchai cluster show` コマンドを実行します。

```azurecli-interactive
az batchai cluster show \
    --name mycluster \
    --workspace myworkspace \
    --resource-group myResourceGroup \
    --output table
```

クラスター作成の初期段階では、出力は次のようになり、クラスターが `resizing` であることが示されます。

```bash
Name       Resource Group    Workspace    VM Size       State      Idle    Running    Preparing    Leaving    Unusable
---------  ----------------  -----------  ------------  -------  ------  ---------  -----------  ---------  ----------
mycluster  myResourceGroup   myworkspace  STANDARD_NC6  resizing      0          0            0          0           0

```

クラスターの状態が変わる間に、次の手順に進んでトレーニング スクリプトをアップロードし、トレーニング ジョブを作成します。 状態が `steady` になり、単一ノードが `Idle` になったら、クラスターでトレーニング ジョブを実行できるようになります。

## <a name="upload-training-script"></a>トレーニング スクリプトのアップロード

`az storage account create` コマンドを使用して、トレーニング スクリプトとトレーニングの出力を保存するストレージ アカウントを作成します。

```azurecli-interactive
az storage account create \
    --resource-group myResourceGroup \
    --name mystorageaccount \
    --location eastus2 \
    --sku Standard_LRS
```

`az storage share create` コマンドを使用して、アカウントに `myshare` という Azure ファイル共有を作成します。

```azurecli-interactive
az storage share create \
    --name myshare \
    --account-name mystorageaccount
```

`az storage directory create` コマンドを使用して、Azure ファイル共有にディレクトリを作成します。 トレーニング スクリプト用の `scripts` ディレクトリと、トレーニングの出力用の `logs` ディレクトリを作成します。

```azurecli-interactive
# Create /scripts directory in file share
az storage directory create \
    --name scripts \
    --share-name myshare \
    --account-name mystorageaccount

# Create /logs directory in file share 
az storage directory create \
    --name logs \
    --share-name myshare \
    --account-name mystorageaccount
```

Bash シェルでローカル作業ディレクトリを作成し、TensorFlow の [convolutional.py](https://raw.githubusercontent.com/tensorflow/models/master/tutorials/image/mnist/convolutional.py) サンプルをダウンロードします。 この Python スクリプトでは、0 から 9 までの 60,000 個の手書き数字の MNIST 画像セットで、畳み込みニューラル ネットワークをトレーニングします。 次に、一連のテスト サンプルでモデルをテストします。

```bash
wget https://raw.githubusercontent.com/tensorflow/models/master/tutorials/image/mnist/convolutional.py
```

`az storage file upload` コマンドを使用して、共有内の `scripts` ディレクトリにスクリプトをアップロードします。

```azurecli-interactive
az storage file upload \
    --share-name myshare \
    --path scripts \
    --source convolutional.py \
    --account-name mystorageaccount
```

## <a name="submit-training-job"></a>トレーニング ジョブの送信

まず、`az batchai experiment create` コマンドを使用して、ワークスペースに Batch AI "*実験*" を作成します。 実験は、相互に関連する Batch AI ジョブの論理コンテナーとなるものです。

```azurecli-interactive
az batchai experiment create \
    --name myexperiment \
    --workspace myworkspace \
    --resource-group myResourceGroup
```

作業ディレクトリに、次の内容のトレーニング ジョブ構成ファイル (`job.json`) を作成します。 トレーニング ジョブを送信するときに、この構成ファイルを渡します。

この `job.json` ファイルには、Python スクリプト ファイルを見つけ、GPU ノード上の TensorFlow コンテナー内で実行するための設定が含まれています。 また、ジョブの出力ファイルを保存する Azure Storage 内の場所も指定されています。 `<AZURE_BATCHAI_STORAGE_ACCOUNT>` は、ジョブの送信時にストレージ アカウント名が指定されることを示しています。

```json
{
    "$schema": "https://raw.githubusercontent.com/Azure/BatchAI/master/schemas/2018-05-01/job.json",
    "properties": {
        "nodeCount": 1,
        "tensorFlowSettings": {
            "pythonScriptFilePath": "$AZ_BATCHAI_JOB_MOUNT_ROOT/myshare/scripts/convolutional.py"
        },
        "stdOutErrPathPrefix": "$AZ_BATCHAI_JOB_MOUNT_ROOT/myshare/logs",
        "mountVolumes": {
            "azureFileShares": [
                {
                    "azureFileUrl": "https://<AZURE_BATCHAI_STORAGE_ACCOUNT>.file.core.windows.net/myshare",
                    "relativeMountPath": "myshare"
                }
            ]
        },
        "containerSettings": {
            "imageSourceRegistry": {
                "image": "tensorflow/tensorflow:1.8.0-gpu"
            }
        }
    }
}
```

`az batchai job create` コマンドを使用してノード上でジョブを送信し、`job.json` 構成ファイルとストレージ アカウントの名前を渡します。

```azurecli-interactive
az batchai job create \
    --name myjob \
    --cluster mycluster \
    --experiment myexperiment \
    --workspace myworkspace \
    --resource-group myResourceGroup \
    --config-file job.json \
    --storage-account-name mystorageaccount
```

このコマンドはジョブのプロパティを返します。コマンドが完了するまで数分かかります。 このジョブの進行状況を監視するには、`az batchai job file stream` コマンドを使用して、ノード上の標準出力ディレクトリから `stdout-wk-0.txt` ファイルをストリーミングします。 このファイルは、ジョブの実行が開始された後に、トレーニング スクリプトによって生成されます。  

```azurecli-interactive
az batchai job file stream \
    --job myjob \
    --experiment myexperiment \
    --workspace myworkspace \
    --resource-group myResourceGroup \
    --file-name stdout-wk-0.txt
```

出力例:

```
File found with URL "https://mystorageaccount.file.core.windows.net/logs/00000000-0000-0000-0000-000000000000/myResourceGroup/workspaces/myworkspace/experiments/myexperiment/jobs/myjob/<JOB_ID>/stdouterr/stdout-wk-0.txt?sv=2016-05-31&sr=f&sig=Kih9baozMao8Ugos%2FVG%2BcsVsSeY1O%2FTocCNvLQhwtx4%3D&se=2018-06-20T22%3A07%3A30Z&sp=rl". Start streaming
Successfully downloaded train-images-idx3-ubyte.gz 9912422 bytes.
Successfully downloaded train-labels-idx1-ubyte.gz 28881 bytes.
Successfully downloaded t10k-images-idx3-ubyte.gz 1648877 bytes.
Successfully downloaded t10k-labels-idx1-ubyte.gz 4542 bytes.
Extracting data/train-images-idx3-ubyte.gz
Extracting data/train-labels-idx1-ubyte.gz
Extracting data/t10k-images-idx3-ubyte.gz
Extracting data/t10k-labels-idx1-ubyte.gz
Initialized!
Step 0 (epoch 0.00), 14.9 ms
Minibatch loss: 8.334, learning rate: 0.010000
Minibatch error: 85.9%
Validation error: 84.6%
Step 100 (epoch 0.12), 9.7 ms
Minibatch loss: 3.240, learning rate: 0.010000
Minibatch error: 6.2%
Validation error: 7.7%
Step 200 (epoch 0.23), 8.3 ms
Minibatch loss: 3.335, learning rate: 0.010000
Minibatch error: 7.8%
Validation error: 4.5%
Step 300 (epoch 0.35), 8.3 ms
Minibatch loss: 3.157, learning rate: 0.010000
Minibatch error: 3.1%
...
Step 8500 (epoch 9.89), 8.3 ms
Minibatch loss: 1.605, learning rate: 0.006302
Minibatch error: 0.0%
Validation error: 0.9%
Test error: 0.8%
```

ジョブが完了すると、ストリーミングが停止します。 サンプル スクリプトでは、10 "*エポック*" のトレーニングを実行します (トレーニング データ セットを学習させます)。 この例では、10 エポック後、トレーニングされたモデルは、わずか 0.8% のテスト誤差で実行されています。

## <a name="get-job-output"></a>ジョブ出力の取得

Batch AI では、ジョブの出力ごとに固有のフォルダー構造をストレージ アカウントに作成します。 このパスで JOB_OUTPUT_PATH 環境変数を設定します。 次に、`az storage file list` コマンドを使用して、ストレージ内の出力ファイルを表示します。

```azurecli-interactive
export JOB_OUTPUT_PATH=$(az batchai job show --name myjob --experiment myexperiment --workspace myworkspace --resource-group myResourceGroup --query jobOutputDirectoryPathSegment --output tsv)

az storage file list \
    --share-name myshare/logs \
    --account-name mystorageaccount \
    --path $JOB_OUTPUT_PATH/stdouterr \
    --output table
```

出力は次のようになります。

```
Name               Content Length  Type    Last Modified
---------------  ----------------  ------  ---------------
execution.log               14866  file
stderr-wk-0.txt              1527  file
stdout-wk-0.txt             11027  file
```

`az storage file download` コマンドを使用して、ローカル作業ディレクトリに 1 つ以上のファイルをダウンロードします。 例: 

```azurecli-interactive
az storage file download \
    --share-name myshare/logs \
    --account-name mystorageaccount \
    --path $JOB_OUTPUT_PATH/stdouterr/stdout-wk-0.txt
```

## <a name="clean-up-resources"></a>リソースのクリーンアップ

Batch AI のチュートリアルとサンプルを続行する場合は、このクイック スタートで作成した Batch AI ワークスペース、クラスター、ストレージ アカウントを使用します。 

ノードの実行中は、Batch AI クラスターに対して課金されます。 実行するジョブがないときにクラスター構成を保持する場合は、クラスターのサイズを 0 ノードに変更します。 

```azurecli-interactive
az batchai cluster resize \
    --name mycluster \
    --workspace myworkspace \
    --resource-group myResourceGroup \
    --target 0
```

後でジョブを実行するときは、サイズを 1 ノード以上に変更します。 クラスターが不要になったら、`az batchai cluster delete` コマンドを使用して削除します。

```azurecli-interactive
az batchai cluster delete \
    --name mycluster \
    --workspace myworkspace \
    --resource-group myResourceGroup
```

Batch AI およびストレージ リソースのリソース グループが不要になったら、`az group delete` コマンドを使用して削除できます。 

```azurecli-interactive 
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>次の手順
このクイック スタートでは、Azure CLI を使用し、Batch AI を使って 1 つの GPU VM 上で TensorFlow サンプル ディープ ラーニング モデルをトレーニングする方法を説明しました。 大規模な GPU クラスター上でモデル トレーニングを分散する方法については、Batch AI のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [分散トレーニングのチュートリアル](./tutorial-horovod-tensorflow.md)