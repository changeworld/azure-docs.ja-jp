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
ms.openlocfilehash: 82e3885021a2f2309dfed456d472e7027b8d6cf2
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/09/2018
---
# <a name="run-a-cntk-training-job-using-the-azure-cli"></a>Azure CLI を使って CNTK トレーニング ジョブを実行する

このクイック スタートでは、Batch AI サービスを使った Microsoft Cognitive Toolkit (CNTK) トレーニング ジョブを Azure コマンド ライン インターフェイス (CLI) から実行する方法について詳しく説明します。 Azure CLI は、コマンドラインやスクリプトで Azure リソースを作成および管理するために使用します。

この例では、手書き画像の MNIST データベースを使い、Batch AI によって管理された単一ノードの GPU クラスター上で畳み込みニューラルネットワーク (CNN: Convolutional Neural Network) のトレーニングを実施します。 

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

このクイック スタートでは、最新の Azure CLI バージョンが実行されている必要があります。 インストールまたはアップグレードする必要がある場合は、「[Azure CLI 2.0 のインストール]( /cli/azure/install-azure-cli)」を参照してください。

また、Azure Cloud Shell または Azure CLI を使い、ご利用のサブスクリプションについて 1 回 Batch AI リソース プロバイダーを登録しておく必要があります。 プロバイダーの登録にかかる時間は最大で 15 分程度です。

```azurecli
az provider register -n Microsoft.BatchAI
az provider register -n Microsoft.Batch
```


## <a name="create-a-resource-group"></a>リソース グループの作成

Batch AI のクラスターとジョブは Azure リソースであり、Azure リソース グループに配置する必要があります。

[az group create](/cli/azure/group#az_group_create) コマンドでリソース グループを作成します。

次の例では、*myResourceGroup* という名前のリソース グループを *eastus* に作成します。 その後、[az configure](/cli/azure/reference-index#az_configure) コマンドを使って、このリソース グループと場所を既定値として設定します。

```azurecli
az group create --name myResourceGroup --location eastus

az configure --defaults group=myResourceGroup

az configure --defaults location=eastus
```

>[!NOTE]
>`az` コマンドの既定値を設定する手順は任意です。 必ずしも既定値を設定する必要はありません。 既定値を設定した場合は、このチュートリアルを終えた後で既定の設定を削除する必要があります。 既定の設定を削除するには、次のコマンドを使用します。
>
>```azurecli
>az configure --defaults group=''
>
>az configure --defaults location=''
>```
>

## <a name="create-a-storage-account"></a>ストレージ アカウントの作成

このクイック スタートでは、トレーニング ジョブに使用するスクリプトとデータのホストとして Azure Storage アカウントを使います。 ストレージ アカウントは、[az storage account create](/cli/azure/storage/account#az_storage_account_create) コマンドで作成します。

```azurecli
az storage account create --name mystorageaccount --sku Standard_LRS
```

>[!NOTE]
>ストレージ アカウントには、それぞれ一意の名前が必要です。 直前の `az` コマンドを含め、このチュートリアルに出現する同様のコマンドでは、`mystorageaccount` 設定の値を実際のストレージ アカウント名に置き換えてください。

## <a name="prepare-azure-file-share"></a>Azure ファイル共有の準備

このクイック スタートでは説明上、学習ジョブに使用するスクリプトとトレーニング データのホストとして Azure ファイル共有を使います。

1. [az storage share create](/cli/azure/storage/share#az_storage_share_create) コマンドを使って、*batchaiquickstart* という名前のファイル共有を作成します。

  ```azurecli
  az storage share create --account-name mystorageaccount --name batchaiquickstart
  ```
2. [az storage directory create](/cli/azure/storage/directory#az_storage_directory_create) コマンドを使って、*mnistcntksample* という名前の共有にディレクトリを作成します。

  ```azurecli
  az storage directory create --share-name batchaiquickstart  --name mnistcntksample
  ```

3. [サンプル パッケージ](https://batchaisamples.blob.core.windows.net/samples/BatchAIQuickStart.zip?st=2017-09-29T18%3A29%3A00Z&se=2099-12-31T08%3A00%3A00Z&sp=rl&sv=2016-05-31&sr=b&sig=hrAZfbZC%2BQ%2FKccFQZ7OC4b%2FXSzCF5Myi4Cj%2BW3sVZDo%3D)をダウンロードしてファイルを解凍します。 その内容を、[az storage file upload](/cli/azure/storage/file#az_storage_file_upload) コマンドを使ってディレクトリにアップロードします。

  ```azurecli
  az storage file upload --share-name batchaiquickstart --source Train-28x28_cntk_text.txt --path mnistcntksample

  az storage file upload --share-name batchaiquickstart --source Test-28x28_cntk_text.txt --path mnistcntksample

  az storage file upload --share-name batchaiquickstart --source ConvNet_MNIST.py --path mnistcntksample
  ```


## <a name="create-gpu-cluster"></a>GPU クラスターの作成
[az batchai cluster create](/cli/azure/batchai/cluster#az_batchai_cluster_create) コマンドを使って、単一の GPU VM ノードから成る Batch AI クラスターを作成します。 この例の VM で実行するのは、既定の Ubuntu LTS イメージです。 より豊富なトレーニング フレームワークをサポートする Microsoft Deep Learning Virtual Machine を実行する場合は、`image UbuntuDSVM` を指定してください。 NC6 サイズには NVIDIA K80 GPU が 1 つ含まれています。 ファイル共有は、*azurefileshare* という名前のフォルダーにマウントします。 このフォルダーの GPU 計算ノード上における完全なパスは $AZ_BATCHAI_MOUNT_ROOT/azurefileshare です。


```azurecli
az batchai cluster create --name mycluster --vm-size STANDARD_NC6 --image UbuntuLTS --min 1 --max 1 --storage-account-name mystorageaccount --afs-name batchaiquickstart --afs-mount-path azurefileshare --user-name <admin_username> --password <admin_password>
```


クラスターの作成後、次のような出力が返されます。

```azurecli
{
  "allocationState": "resizing",
  "allocationStateTransitionTime": "2017-10-05T02:09:03.194000+00:00",
  "creationTime": "2017-10-05T02:09:01.998000+00:00",
  "currentNodeCount": 0,
  "errors": null,
  "id": "/subscriptions/10d0b7c6-9243-4713-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.BatchAI/clusters/mycluster",
  "location": "eastus",
  "name": "mycluster",
  "nodeSetup": {
    "mountVolumes": {
      "azureBlobFileSystems": null,
      "azureFileShares": [
        {
          "accountName": "batchaisamples",
          "azureFileUrl": "https://batchaisamples.file.core.windows.net/batchaiquickstart",
          "credentialsInfo": {
            "accountKey": null,
            "accountKeySecretUrl": null
          },
          "directoryMode": "0777",
          "fileMode": "0777",
          "relativeMountPath": "azurefileshare"
        }
      ],
      "fileServers": null,
      "unmanagedFileSystems": null
    },
    "setupTask": null
  },
  "nodeStateCounts": {
    "idleNodeCount": 0,
    "leavingNodeCount": 0,
    "preparingNodeCount": 0,
    "runningNodeCount": 0,
    "unusableNodeCount": 0
  },
  "provisioningState": "succeeded",
  "provisioningStateTransitionTime": "2017-10-05T02:09:02.857000+00:00",
  "resourceGroup": "myresourcegroup",
  "scaleSettings": {
    "autoScale": null,
    "manual": {
      "nodeDeallocationOption": "requeue",
      "targetNodeCount": 1
    }
  },
  "subnet": {
    "id": null
  },
  "tags": null,
  "type": "Microsoft.BatchAI/Clusters",
  "userAccountSettings": {
    "adminUserName": "demoUser",
    "adminUserPassword": null,
    "adminUserSshPublicKey": null
  },
  "virtualMachineConfiguration": {
    "imageReference": {
      "offer": "UbuntuServer",
      "publisher": "Canonical",
      "sku": "16.04-LTS",
      "version": "latest"
    }
  },
  "vmPriority": "dedicated",
  "vmSize": "STANDARD_NC6"
```
## <a name="get-cluster-status"></a>クラスターの状態の取得

クラスターの状態について概要を把握するには、[az batchai cluster list](/cli/azure/batchai/cluster#az_batchai_cluster_list) コマンドを実行します。

```azurecli
az batchai cluster list -o table
```

次のような出力になります。

```azurecli
Name        Resource Group    VM Size        State     Idle    Running    Preparing    Unusable    Leaving
---------   ----------------  -------------  -------   ------  ---------  -----------  ----------  --------
mycluster   myresourcegroup   STANDARD_NC6   steady    1       0          0            0            0
```

詳細については、[az batchai cluster show](/cli/azure/batchai/cluster#az_batchai_cluster_show) コマンドを実行してください。 クラスターの作成後に表示されるクラスターのプロパティがすべて返されます。

ノードの割り当てが済んで準備が完了したら (`nodeStateCounts` 属性を参照)、クラスターの準備は完了です。 何か問題がある場合、`errors` 属性にエラーの説明が格納されます。

## <a name="create-training-job"></a>トレーニング ジョブの作成

クラスターの準備が完了したら、学習ジョブを構成して送信します。

1. ジョブの作成に使用する JSON テンプレート ファイルを job.json という名前で作成します。

  ```JSON
  {
    "properties": {
        "stdOutErrPathPrefix": "$AZ_BATCHAI_MOUNT_ROOT/azurefileshare",
       "inputDirectories": [{
            "id": "SAMPLE",
            "path": "$AZ_BATCHAI_MOUNT_ROOT/azurefileshare/mnistcntksample"
        }],
        "outputDirectories": [{
            "id": "MODEL",
            "pathPrefix": "$AZ_BATCHAI_MOUNT_ROOT/azurefileshare",
            "pathSuffix": "model",
            "type": "custom"
        }],
        "containerSettings": {
            "imageSourceRegistry": {
                "image": "microsoft/cntk:2.1-gpu-python3.5-cuda8.0-cudnn6.0"
            }
        },
        "nodeCount": 1,
        "cntkSettings": {
            "pythonScriptFilePath": "$AZ_BATCHAI_INPUT_SAMPLE/ConvNet_MNIST.py",
            "commandLineArgs": "$AZ_BATCHAI_INPUT_SAMPLE $AZ_BATCHAI_OUTPUT_MODEL"
        }
    }
  }
  ```
2. クラスター上で実行する *myjob* という名前のジョブを [az batchai job create](/cli/azure/batchai/job#az_batchai_job_create) コマンドで作成します。

  ```azurecli
  az batchai job create --name myjob --cluster-name mycluster --config job.json
  ```

次のような出力になります。

```azurecli
{
  "caffeSettings": null,
  "chainerSettings": null,
  "cluster": {
    "id": "/subscriptions/10d0b7c6-9243-4713-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.BatchAI/clusters/mycluster",
    "resourceGroup": "myresourcegroup"
  },
  "cntkSettings": {
    "commandLineArgs": "$AZ_BATCHAI_INPUT_SAMPLE $AZ_BATCHAI_OUTPUT_MODEL",
    "configFilePath": null,
    "languageType": "Python",
    "processCount": 1,
    "pythonInterpreterPath": null,
    "pythonScriptFilePath": "$AZ_BATCHAI_INPUT_SAMPLE/ConvNet_MNIST.py"
  },
  "constraints": {
    "maxTaskRetryCount": null,
    "maxWallClockTime": "7 days, 0:00:00"
  },
  "containerSettings": {
    "imageSourceRegistry": {
      "credentials": null,
      "image": "microsoft/cntk:2.1-gpu-python3.5-cuda8.0-cudnn6.0",
      "serverUrl": null
    }
  },
  "creationTime": "2017-10-05T06:41:42.163000+00:00",
  "customToolkitSettings": null,
  "environmentVariables": null,
  "executionInfo": {
    "endTime": null,
    "errors": null,
    "exitCode": null,
    "lastRetryTime": null,
    "retryCount": null,
    "startTime": "2017-10-05T06:41:44.392000+00:00"
  },
  "executionState": "running",
  "executionStateTransitionTime": "2017-10-05T06:41:44.953000+00:00",
  "experimentName": null,
  "id": "/subscriptions/10d0b7c6-9243-4713-xxxx-xxxxxxxxxxxx/resourceGroups/demo/providers/Microsoft.BatchAI/jobs/myjob",
  "inputDirectories": [
    {
      "id": "SAMPLE",
      "path": "$AZ_BATCHAI_MOUNT_ROOT/azurefileshare/mnistcntksample"
    }
  ],
  "jobPreparation": null,
  "location": null,
  "name": "cntk_job",
  "nodeCount": 1,
  "outputDirectories": [
    {
      "createNew": true,
      "id": "MODEL",
      "pathPrefix": "$AZ_BATCHAI_MOUNT_ROOT/azurefileshare",
      "pathSuffix": "model",
      "type": "Custom"
    }
  ],
  "priority": 0,
  "provisioningState": "succeeded",
  "provisioningStateTransitionTime": "2017-10-05T06:41:44.238000+00:00",
  "resourceGroup": "demo",
  "stdOutErrPathPrefix": "$AZ_BATCHAI_MOUNT_ROOT/azurefileshare",
  "tags": null,
  "tensorFlowSettings": null,
  "toolType": "CNTK",
  "type": "Microsoft.BatchAI/Jobs"
}
```

## <a name="monitor-job"></a>ジョブの監視

[az batchai job list](/cli/azure/batchai/job#az_batchai_job_list) コマンドを使って、ジョブの状態についての概要を把握します。

```azurecli
az batchai job list -o table
```

次のような出力になります。

```azurecli
Name        Resource Group    Cluster    Cluster RG      Nodes  State    Exit code
----------  ----------------  ---------  --------------- -----  -------  -----------
myjob       myresourcegroup   mycluster  myresourcegroup 1      running

```

詳細については、[az batchai job show](/cli/azure/batchai/job#az_batchai_job_show) コマンドを実行してください。

ジョブの現在の実行状態は `executionState` に含まれています。

* `queued`: ジョブは、クラスター ノードが使用可能な状態になるのを待機しています
* `running`: ジョブは実行中です
*   `succeeded` (または `failed`) : ジョブは完了しています。その結果についての詳しい情報は `executionInfo` に含まれています


## <a name="list-stdout-and-stderr-output"></a>stdout と stderr のリスト出力
stdout および stderr のログ ファイルへのリンクをリストするには、[az batchai job list-files](/cli/azure/batchai/job#az_batchai_job_list_files) コマンドを使います。

```azurecli
az batchai job list-files --name myjob --output-directory-id stdouterr
```

次のような出力になります。

```azurecli
[
  {
    "contentLength": 733,
    "downloadUrl": "https://batchaisamples.file.core.windows.net/batchaiquickstart/10d0b7c6-9243-4713-91a9-2730375d3a1b/demo/jobs/cntk_job/stderr.txt?sv=2016-05-31&sr=f&sig=Rh%2BuTg9C1yQxm7NfA9YWiKb%2B5FRKqWmEXiGNRDeFMd8%3D&se=2017-10-05T07%3A44%3A38Z&sp=rl",
    "lastModified": "2017-10-05T06:44:38+00:00",
    "name": "stderr.txt"
  },
  {
    "contentLength": 300,
    "downloadUrl": "https://batchaisamples.file.core.windows.net/batchaiquickstart/10d0b7c6-9243-4713-91a9-2730375d3a1b/demo/jobs/cntk_job/stdout.txt?sv=2016-05-31&sr=f&sig=jMhJfQOGry9jr4Hh3YyUFpW5Uaxnp38bhVWNrTTWMtk%3D&se=2017-10-05T07%3A44%3A38Z&sp=rl",
    "lastModified": "2017-10-05T06:44:29+00:00",
    "name": "stdout.txt"
  }
]
```


## <a name="observe-output"></a>出力の監視

ジョブの実行中に、その出力ファイルをストリーミング (最後の部分を表示) することができます。 次の例では、[az batchai job stream-file](/cli/azure/batchai/job#az_batchai_job_stream_file) コマンドを使って stderr.txt ログをストリーミングしています。

```azurecli
az batchai job stream-file --job-name myjob --output-directory-id stdouterr --name stderr.txt
```

出力は次のようになります。 出力に割り込んで中断するには、Ctrl キーを押しながら C キーを押します。

```azurecli
…
Finished Epoch[2 of 40]: [Training] loss = 0.104846 * 60000, metric = 3.00% * 60000 3.849s (15588.5 samples/s);
Finished Epoch[3 of 40]: [Training] loss = 0.077043 * 60000, metric = 2.23% * 60000 3.902s (15376.7 samples/s);
Finished Epoch[4 of 40]: [Training] loss = 0.063050 * 60000, metric = 1.82% * 60000 3.811s (15743.9 samples/s);
…

```

## <a name="delete-resources"></a>リソースを削除する

ジョブを削除するには、[az batchai job delete](/cli/azure/batchai/job#az_batchai_job_delete) コマンドを使います。

```azurecli
az batchai job delete --name myjob
```
クラスターを削除するには、[az batchai cluster delete](/cli/azure/batchai/cluster#az_batchai_cluster_delete) コマンドを使います。

```azurecli
az batchai cluster delete --name mycluster
```

このクイック スタート用に作成したリソース グループを削除するには、`az group delete` コマンドを使います。

```azurecli
az group delete --name myResourceGroup
```

## <a name="restore-azure-cli-20-default-settings"></a>Azure CLI 2.0 の既定の設定を復元する

これまでに場所とリソース グループに関して構成した既定の設定を削除します。

```azurecli
az configure --defaults group=''

az configure --defaults location=''
```

## <a name="next-steps"></a>次の手順

このクイック スタートでは、Azure CLI から Batch AI クラスター上で CNTK トレーニング ジョブを実行する方法について説明しました。 さまざまなツールキットで Batch AI を使う方法について詳しくは、[トレーニングのレシピ](https://github.com/Azure/BatchAI)をご覧ください。

Azure CLI 2.0 を使った Batch AI の管理について詳しくは、[github のドキュメント](https://github.com/Azure/BatchAI/blob/master/documentation/using-azure-cli-20.md)をご覧ください。
