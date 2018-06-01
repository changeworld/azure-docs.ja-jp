---
title: Azure クイック スタート - Batch AI を使用した CNTK トレーニング - Azure CLI | Microsoft Docs
description: Batch AI を使った CNTK トレーニング ジョブを Azure CLI から実行する方法についてわかりやすく説明します。
services: batch-ai
documentationcenter: na
author: AlexanderYukhanov
manager: Vaman.Bedekar
editor: tysonn
ms.assetid: ''
ms.custom: ''
ms.service: batch-ai
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: CLI
ms.topic: quickstart
ms.date: 10/06/2017
ms.author: Alexander.Yukhanov
ms.openlocfilehash: 3601ea412790c991892a0c05210d2551810287b8
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/08/2018
ms.locfileid: "33869021"
---
# <a name="run-a-cntk-training-job-using-the-azure-cli"></a>Azure CLI を使って CNTK トレーニング ジョブを実行する

Batch AI リソースの作成と管理、つまり Batch AI ファイル サーバーとクラスターの作成/削除およびトレーニング ジョブの送信/終了/削除/監視は、Azure CLI 2.0 を使って行うことができます。

このクイック スタートでは、Microsoft Cognitive Toolkit を使用して GPU クラスターを作成し、トレーニング ジョブを実行する方法について説明します。

トレーニング スクリプト [ConvNet_MNIST.py](https://github.com/Azure/BatchAI/blob/master/recipes/CNTK/CNTK-GPU-Python/CNTK-GPU-Python.ipynb) は Batch AI の GitHub ページから入手できます。 このスクリプトでは、手書き数字の MNIST データベースで、畳み込みニューラルネットワークをトレーニングします。

トレーニング データセットの場所と出力ディレクトリの場所をコマンド ライン引数で渡すことができるように、公式の CNTK サンプルに変更を加えています。

## <a name="quickstart-overview"></a>クイック スタートの概要

* 単一ノードの GPU クラスター (`Standard_NC6` VM サイズ) を `nc6` という名前で作成します。
* ジョブの入力と出力を格納する新しいストレージ アカウントを作成します。
* ジョブの出力とトレーニング スクリプトを格納する 2 つのフォルダー `logs` と `scripts` を含む Azure ファイル共有を作成します。
* トレーニング データを格納するための Azure BLOB コンテナー `data` を作成します。
* 作成したファイル共有とコンテナーに、トレーニング スクリプトとトレーニング データをデプロイします。
* Azure ファイル共有と Azure BLOB コンテナーをクラスターのノードでマウントし、`$AZ_BATCHAI_JOB_MOUNT_ROOT/logs`、`$AZ_BATCHAI_JOB_MOUNT_ROOT/scripts`、`$AZ_BATCHAI_JOB_MOUNT_ROOT/data` で通常のファイル システムとして利用できるようにするジョブを構成します。
`AZ_BATCHAI_JOB_MOUNT_ROOT` は、このジョブ用に Batch AI によって設定される環境変数です。
* ジョブの標準出力をストリーム配信することによって、その実行を監視します。
* ジョブの完了後、その出力および生成されたモデルを検査します。
* 最後に、割り当て済みのリソースをすべて削除します。

# <a name="prerequisites"></a>前提条件

* Azure サブスクリプション - Azure サブスクリプションをお持ちでない場合は、開始する前に[無料のアカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。
* バージョン 2.0.31 以降の Azure CLI 2.0 へのアクセス。 Azure CLI 2.0 は、[Cloud Shell](https://docs.microsoft.com/en-us/azure/cloud-shell/overview) で利用するか、または[こちらの手順](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest)に従ってローカルにインストールすることもできます。

# <a name="cloud-shell-only"></a>Cloud Shell のみ

Cloud Shell を使用している場合、作業ディレクトリを `/usr/$USER/clouddrive` に変更してください。ホーム ディレクトリには空の領域が存在しません。

```azurecli
cd /usr/$USER/clouddrive
```

# <a name="create-a-resource-group"></a>リソース グループを作成します

Azure リソース グループとは、Azure リソースのデプロイと管理を目的とした論理コンテナーです。 次のコマンドは、米国東部に新しいリソース グループ ```batchai.quickstart``` を作成します。

```azurecli
az group create -n batchai.quickstart -l eastus
```

# <a name="create-gpu-cluster"></a>GPU クラスターの作成

次のコマンドは、オペレーティング システム イメージに Ubuntu DSVM を使用して、単一ノードの GPU クラスター (VM サイズは Standard_NC6) を作成します。

```azurecli
az batchai cluster create -n nc6 -g batchai.quickstart -s Standard_NC6 -i UbuntuDSVM -t 1 --generate-ssh-keys
```

Ubuntu DSVM を使用すると、あらゆるトレーニング ジョブを Docker コンテナーで実行でき、ディープ ラーニング フレームワークの中でも特に支持されているフレームワークの大半を VM 上で直接実行することができます。

`--generate-ssh-keys` オプションでは、ssh の秘密キーと公開キーがまだ存在しなければそれらのキーを生成するよう Azure CLI に命令しています。 クラスター ノードには、現在のユーザー名と生成された ssh キーを使ってアクセスできます。

Cloud Shell を使用する場合は必ず、何らかの永続記憶域に ~/.ssh フォルダーをバックアップしてください。

出力例:
```json
{
  "allocationState": "steady",
  "allocationStateTransitionTime": "2018-04-11T21:17:26.345000+00:00",
  "creationTime": "2018-04-11T20:12:10.758000+00:00",
  "currentNodeCount": 0,
  "errors": null,
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/batchai.quickstart/providers/Microsoft.BatchAI/clusters/nc6",
  "location": "eastus",
  "name": "nc6",
  "nodeSetup": null,
  "nodeStateCounts": {
    "additionalProperties": {},
    "idleNodeCount": 0,
    "leavingNodeCount": 0,
    "preparingNodeCount": 0,
    "runningNodeCount": 0,
    "unusableNodeCount": 0
  },
  "provisioningState": "succeeded",
  "provisioningStateTransitionTime": "2018-04-11T20:12:11.445000+00:00",
  "resourceGroup": "batchai.quickstart",
  "scaleSettings": {
    "additionalProperties": {},
    "autoScale": null,
    "manual": {
      "nodeDeallocationOption": "requeue",
      "targetNodeCount": 1
    }
  },
  "subnet": null,
  "tags": null,
  "type": "Microsoft.BatchAI/Clusters",
  "userAccountSettings": {
    "additionalProperties": {},
    "adminUserName": "alex",
    "adminUserPassword": null,
    "adminUserSshPublicKey": "<YOUR SSH PUBLIC KEY HERE>"
  },
  "virtualMachineConfiguration": {
    "additionalProperties": {},
    "imageReference": {
      "additionalProperties": {},
      "offer": "linux-data-science-vm-ubuntu",
      "publisher": "microsoft-ads",
      "sku": "linuxdsvmubuntu",
      "version": "latest",
      "virtualMachineImageId": null
    }
  },
  "vmPriority": "dedicated",
  "vmSize": "STANDARD_NC6"
}
```

# <a name="create-a-storage-account"></a>ストレージ アカウントの作成

以下のコマンドは、batchai.repices リソース グループと同じリージョンに新しいストレージ アカウントを作成します。 一意のストレージ アカウント名でコマンドを更新してください。

```azurecli
az storage account create -n <storage account name> --sku Standard_LRS -g batchai.quickstart
```

上記のコマンドで、選択したストレージ アカウント名が利用できない場合、対応するエラーが報告されます。 その場合は、別の名前を選んで再試行してください。

# <a name="data-deployment"></a>データのデプロイ

## <a name="download-the-training-script-and-training-data"></a>トレーニング スクリプトとトレーニング データのダウンロード

* 前処理済みの MNIST データベースを[こちら](https://batchaisamples.blob.core.windows.net/samples/mnist_dataset.zip?st=2017-09-29T18%3A29%3A00Z&se=2099-12-31T08%3A00%3A00Z&sp=rl&sv=2016-05-31&sr=c&sig=PmhL%2BYnYAyNTZr1DM2JySvrI12e%2F4wZNIwCtf7TRI%2BM%3D)から現在のフォルダーにダウンロードして展開してください。

GNU/Linux または Cloud Shell の場合:

```azurecli
wget "https://batchaisamples.blob.core.windows.net/samples/mnist_dataset.zip?st=2017-09-29T18%3A29%3A00Z&se=2099-12-31T08%3A00%3A00Z&sp=rl&sv=2016-05-31&sr=c&sig=PmhL%2BYnYAyNTZr1DM2JySvrI12e%2F4wZNIwCtf7TRI%2BM%3D" -O mnist_dataset.zip
unzip mnist_dataset.zip
```

GNU/Linux ディストリビューションに `unzip` が存在しない場合は、別途インストールする必要があることに注意してください。

* [ConvNet_MNIST.py](https://raw.githubusercontent.com/Azure/BatchAI/master/recipes/CNTK/CNTK-GPU-Python/ConvNet_MNIST.py) サンプル スクリプトを現在のフォルダーにダウンロードします。

GNU/Linux または Cloud Shell の場合:

```azurecli
wget https://raw.githubusercontent.com/Azure/BatchAI/master/recipes/CNTK/CNTK-GPU-Python/ConvNet_MNIST.py
```

## <a name="create-azure-file-share-and-deploy-the-training-script"></a>Azure ファイル共有の作成とトレーニング スクリプトのデプロイ

次のコマンドは、Azure ファイル共有 `scripts` および `logs` を作成し、`scripts` 共有内の `cntk` フォルダーにトレーニング スクリプトをコピーします。

```azurecli
az storage share create -n scripts --account-name <storage account name>
az storage share create -n logs --account-name <storage account name>
az storage directory create -n cntk -s scripts --account-name <storage account name>
az storage file upload -s scripts --source ConvNet_MNIST.py --path cntk --account-name <storage account name> 
```

## <a name="create-a-blob-container-and-deploy-training-data"></a>BLOB コンテナーの作成とトレーニング データのデプロイ

次のコマンドは、Azure BLOB コンテナー `data` を作成し、`mnist_cntk` フォルダーにトレーニング データをコピーします。
```azurecli
az storage container create -n data --account-name <storage account name>
az storage blob upload-batch -s . --pattern '*28x28_cntk*' --destination data --destination-path mnist_cntk --account-name <storage account name>
```

# <a name="submit-training-job"></a>トレーニング ジョブの送信

## <a name="prepare-job-configuration-file"></a>ジョブ構成ファイルの準備

次の内容を含んだトレーニング ジョブ構成ファイル `job.json` を作成します。
```json
{
    "$schema": "https://raw.githubusercontent.com/Azure/BatchAI/master/schemas/2018-03-01/cntk.json",
    "properties": {
        "nodeCount": 1,
        "cntkSettings": {
            "pythonScriptFilePath": "$AZ_BATCHAI_JOB_MOUNT_ROOT/scripts/cntk/ConvNet_MNIST.py",
            "commandLineArgs": "$AZ_BATCHAI_JOB_MOUNT_ROOT/data/mnist_cntk $AZ_BATCHAI_OUTPUT_MODEL"
        },
        "stdOutErrPathPrefix": "$AZ_BATCHAI_JOB_MOUNT_ROOT/logs",
        "outputDirectories": [{
            "id": "MODEL",
            "pathPrefix": "$AZ_BATCHAI_JOB_MOUNT_ROOT/logs"
        }],
        "mountVolumes": {
            "azureFileShares": [
                {
                    "azureFileUrl": "https://<AZURE_BATCHAI_STORAGE_ACCOUNT>.file.core.windows.net/logs",
                    "relativeMountPath": "logs"
                },
                {
                    "azureFileUrl": "https://<AZURE_BATCHAI_STORAGE_ACCOUNT>.file.core.windows.net/scripts",
                    "relativeMountPath": "scripts"
                }
            ],
            "azureBlobFileSystems": [
                {
                    "accountName": "<AZURE_BATCHAI_STORAGE_ACCOUNT>",
                    "containerName": "data",
                    "relativeMountPath": "data"
                }
            ]
        }
    }
}
```

この構成ファイルに指定されている内容は次のとおりです。

* `nodeCount` - ジョブに必要なノード数です (このクイック スタートでは 1)。
* `cntkSettings` - トレーニング スクリプトのパスとコマンド ライン引数を指定します。 コマンド ライン引数には、トレーニング データのパスと、生成されたモデルの保存先パスが含まれます。 `AZ_BATCHAI_OUTPUT_MODEL` は、出力ディレクトリの構成 (以下参照) に基づいて Batch AI が設定する環境変数です。
* `stdOutErrPathPrefix` - ジョブの出力とログを格納するディレクトリが Batch AI によって作成されます。その作成先となるパスです。
* `outputDirectories` - Batch AI によって作成される出力ディレクトリのコレクションです。 ディレクトリごとに、`AZ_BATCHAI_OUTPUT_<id>` という名前の環境変数が Batch AI によって作成されます (`<id>` はディレクトリの識別子)。
* `mountVolumes` - ジョブの実行中にマウントされるファイル システムのリストです。 ファイル システムは `AZ_BATCHAI_JOB_MOUNT_ROOT/<relativeMountPath>` 下にマウントされます。 `AZ_BATCHAI_JOB_MOUNT_ROOT` は、Batch AI によって設定される環境変数です。
* `<AZURE_BATCHAI_STORAGE_ACCOUNT>` は、--storage-account-name パラメーターまたはコンピューター上の `AZURE_BATCHAI_STORAGE_ACCOUNT` 環境変数を介して、ジョブの送信中にストレージ アカウント名が指定されることを示します。

## <a name="submit-the-job"></a>ジョブの送信

クラスターでジョブを送信するには、次のコマンドを使用します。

```azurecli
az batchai job create -n cntk_python_1 -r nc6 -g batchai.quickstart -c job.json --storage-account-name <storage account name>
```

出力例:
```
{
  "additionalProperties": {},
  "caffeSettings": null,
  "chainerSettings": null,
  "cluster": {
    "additionalProperties": {},
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/batchai.quickstart/providers/Microsoft.BatchAI/clusters/nc6",
    "resourceGroup": "batchai.quickstart"
  },
  "cntkSettings": {
    "additionalProperties": {},
    "commandLineArgs": "$AZ_BATCHAI_JOB_MOUNT_ROOT/data/mnist_cntk $AZ_BATCHAI_OUTPUT_MODEL",
    "configFilePath": null,
    "languageType": "Python",
    "processCount": 1,
    "pythonInterpreterPath": null,
    "pythonScriptFilePath": "$AZ_BATCHAI_JOB_MOUNT_ROOT/scripts/cntk/ConvNet_MNIST.py"
  },
  "constraints": {
    "additionalProperties": {},
    "maxWallClockTime": "7 days, 0:00:00"
  },
  "containerSettings": null,
  "creationTime": "2018-04-11T21:48:10.303000+00:00",
  "customToolkitSettings": null,
  "environmentVariables": null,
  "executionInfo": null,
  "executionState": "queued",
  "executionStateTransitionTime": "2018-04-11T21:48:10.303000+00:00",
  "experimentName": null,
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/batchai.quickstart/providers/Microsoft.BatchAI/jobs/cntk_python_1",
  "inputDirectories": null,
  "jobOutputDirectoryPathSegment": "00000000-0000-0000-0000-000000000000/batchai.quickstart/jobs/cntk_python_1/b9576bae-e878-4fb2-9390-2e962356b5b1",
  "jobPreparation": null,
  "location": null,
  "mountVolumes": {
    "additionalProperties": {},
    "azureBlobFileSystems": [
      {
        "accountName": "<YOU STORAGE ACCOUNT NAME>",
        "additionalProperties": {},
        "containerName": "data",
        "credentials": {
          "accountKey": null,
          "accountKeySecretReference": null,
          "additionalProperties": {}
        },
        "mountOptions": null,
        "relativeMountPath": "data"
      }
    ],
    "azureFileShares": [
      {
        "accountName": "<YOU STORAGE ACCOUNT NAME>,
        "additionalProperties": {},
        "azureFileUrl": "https://<YOU STORAGE ACCOUNT NAME>.file.core.windows.net/logs",
        "credentials": {
          "accountKey": null,
          "accountKeySecretReference": null,
          "additionalProperties": {}
        },
        "directoryMode": "0777",
        "fileMode": "0777",
        "relativeMountPath": "logs"
      },
      {
        "accountName": "<YOU STORAGE ACCOUNT NAME>",
        "additionalProperties": {},
        "azureFileUrl": "https://<YOU STORAGE ACCOUNT NAME>.file.core.windows.net/scripts",
        "credentials": {
          "accountKey": null,
          "accountKeySecretReference": null,
          "additionalProperties": {}
        },
        "directoryMode": "0777",
        "fileMode": "0777",
        "relativeMountPath": "scripts"
      }
    ],
    "fileServers": null,
    "unmanagedFileSystems": null
  },
  "name": "cntk_python_1",
  "nodeCount": 1,
  "outputDirectories": [
    {
      "additionalProperties": {},
      "createNew": true,
      "id": "MODEL",
      "pathPrefix": "$AZ_BATCHAI_JOB_MOUNT_ROOT/logs",
      "pathSuffix": null,
      "type": "custom"
    }
  ],
  "priority": 0,
  "provisioningState": "succeeded",
  "provisioningStateTransitionTime": "2018-04-11T21:48:11.577000+00:00",
  "pyTorchSettings": null,
  "resourceGroup": "batchai.quickstart",
  "secrets": null,
  "stdOutErrPathPrefix": "$AZ_BATCHAI_JOB_MOUNT_ROOT/logs",
  "tags": null,
  "tensorFlowSettings": null,
  "toolType": "cntk",
  "type": "Microsoft.BatchAI/Jobs"
}
```

# <a name="monitor-job-execution"></a>ジョブの実行の監視

このトレーニング スクリプトは、標準出力ディレクトリ内の `stderr.txt` ファイルにトレーニングの進行状況をレポートします。 次のコマンドを使用して進行状況を監視することができます。

```azurecli
az batchai job file stream -n cntk_python_1 -g batchai.quickstart -f stderr.txt
```

出力例:
```
File found with URL "https://<YOU STORAGE ACCOUNT>.file.core.windows.net/logs/00000000-0000-0000-0000-000000000000/batchai.quickstart/jobs/cntk_python_1/<JOB's UUID>/stdouterr/stderr.txt?sv=2016-05-31&sr=f&sig=n86JK9YowV%2BPQ%2BkBzmqr0eud%2FlpRB%2FVu%2FFlcKZx192k%3D&se=2018-04-11T23%3A05%3A54Z&sp=rl". Start streaming
Selected GPU[0] Tesla K80 as the process wide default device.
-------------------------------------------------------------------
Build info:

        Built time: Jan 31 2018 15:03:41
        Last modified date: Tue Jan 30 03:26:13 2018
        Build type: release
        Build target: GPU
        With 1bit-SGD: no
        With ASGD: yes
        Math lib: mkl
        CUDA version: 9.0.0
        CUDNN version: 7.0.4
        Build Branch: HEAD
        Build SHA1: a70455c7abe76596853f8e6a77a4d6de1e3ba76e
        MPI distribution: Open MPI
        MPI version: 1.10.7
-------------------------------------------------------------------
Training 98778 parameters in 10 parameter tensors.

Learning rate per 1 samples: 0.001
Momentum per 1 samples: 0.0
Finished Epoch[1 of 40]: [Training] loss = 0.405960 * 60000, metric = 13.01% * 60000 21.741s (2759.8 samples/s);
Finished Epoch[2 of 40]: [Training] loss = 0.106030 * 60000, metric = 3.09% * 60000 3.638s (16492.6 samples/s);
Finished Epoch[3 of 40]: [Training] loss = 0.078542 * 60000, metric = 2.32% * 60000 3.477s (17256.3 samples/s);
...
Final Results: Minibatch[1-11]: errs = 0.54% * 10000
```

ジョブが完了 (成功または失敗) すると、ストリーム配信が停止されます。

# <a name="inspect-generated-model-files"></a>生成されたモデル ファイルの検査

このジョブで生成されたモデル ファイルは、`id` 属性が `MODEL` と等しい出力ディレクトリに格納されます。次のコマンドを使用すると、モデル ファイルをリストしてダウンロード URL を取得できます。

```azurecli
az batchai job file list -n cntk_python_1 -g batchai.quickstart -d MODEL
```

出力例:
```
[
  {
    "additionalProperties": {},
    "contentLength": 409456,
    "downloadUrl": "https://<YOUR STORAGE ACCOUNT>.file.core.windows.net/...",
    "isDirectory": false,
    "lastModified": "2018-04-11T22:05:51+00:00",
    "name": "ConvNet_MNIST_0.dnn"
  },
  {
    "additionalProperties": {},
    "contentLength": 409456,
    "downloadUrl": "https://<YOUR STORAGE ACCOUNT>.file.core.windows.net/...",
    "isDirectory": false,
    "lastModified": "2018-04-11T22:05:55+00:00",
    "name": "ConvNet_MNIST_1.dnn"
  },
...

```

生成されたファイルの検査には、ポータルまたは Azure Storage Explorer を使用してもかまいません。 異なるジョブからの出力を区別するために、それぞれに対応する一意のフォルダー構造が Batch AI によって作成されます。 出力結果が格納されているフォルダーのパスは、送信したジョブの `jobOutputDirectoryPathSegment` 属性を使用して見つけることができます。

```azurecli
az batchai job show -n cntk_python_1 -g batchai.quickstart --query jobOutputDirectoryPathSegment
```

出力例:
```
"00000000-0000-0000-0000-000000000000/batchai.quickstart/jobs/cntk_python_1/<JOB's UUID>"
```

# <a name="delete-resources"></a>リソースの削除

リソース グループとそこに割り当てられているリソースをすべて削除するには、次のコマンドを実行します。

```azurecli
az batchai group delete -n batchai.quickstart -y
```
