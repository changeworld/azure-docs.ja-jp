---
title: Azure クイック スタート - Batch AI を使用した CNTK トレーニング - Python | Microsoft Docs
description: Batch AI を使った CNTK トレーニング ジョブを Python SDK から実行する方法についてわかりやすく説明します。
services: batch-ai
documentationcenter: na
author: lliimsft
manager: Vaman.Bedekar
editor: tysonn
ms.assetid: ''
ms.custom: ''
ms.service: batch-ai
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: Python
ms.topic: quickstart
ms.date: 10/06/2017
ms.author: lili
ms.openlocfilehash: f535c9adf4926f29ae9cade6382debedab73937d
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2018
---
# <a name="run-a-cntk-training-job-using-the-azure-python-sdk"></a>Azure Python SDK を使って CNTK トレーニング ジョブを実行する

このクイック スタートでは、Batch AI サービスを使った Microsoft Cognitive Toolkit (CNTK) トレーニング ジョブを Azure Python SDK から実行する方法について詳しく説明します。 

この例では、手書き画像の MNIST データベースを使い、単一ノードの GPU クラスター上で畳み込みニューラルネットワーク (CNN: Convolutional Neural Network) のトレーニングを実施します。 

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション - Azure サブスクリプションをお持ちでない場合は、開始する前に[無料のアカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。 

* Azure Python SDK -[インストール手順](/python/azure/python-sdk-azure-install)をご覧ください。

* Azure Storage アカウント - [Azure Storage アカウントの作成方法](../storage/common/storage-create-storage-account.md)に関するページをご覧ください。

* Azure Active Directory サービス プリンシパルの資格情報 - [CLI でサービス プリンシパルを作成する方法](../azure-resource-manager/resource-group-authenticate-service-principal-cli.md)に関するページをご覧ください。

* Azure Cloud Shell または Azure CLI を使って、ご利用のサブスクリプションについて 1 回、Batch AI リソース プロバイダーを登録します。 プロバイダーの登録にかかる時間は最大で 15 分程度です。

  ```azurecli
  az provider register -n Microsoft.BatchAI
  az provider register -n Microsoft.Batch
  ```


## <a name="configure-credentials"></a>資格情報の構成
Python スクリプトで次のパラメーターを作成します。パラメーターの値は、実際の値に置き換えてください。

```Python
# credentials used for authentication
client_id = 'my_aad_client_id'
secret = 'my_aad_secret_key'
token_uri = 'my_aad_token_uri'
subscription_id = 'my_subscription_id'

# credentials used for storage
storage_account_name = 'my_storage_account_name'
storage_account_key = 'my_storage_account_key'

# specify the credentials used to remote login your GPU node
admin_user_name = 'my_admin_user_name'
admin_user_password = 'my_admin_user_password'
```

この例では紹介していませんが、ベスト プラクティスとしては、すべての資格情報を別個の構成ファイルに格納することをお勧めします。 構成ファイルの実装については、[レシピ](https://github.com/Azure/BatchAI/tree/master/recipes)をご覧ください。 

## <a name="authenticate-with-batch-ai"></a>Batch AI を使った認証

Azure Batch AI にアクセスするには、Azure Active Directory を使って認証を行う必要があります。 以下に示したのは、サービス プリンシパルの資格情報とサブスクリプション ID を使って、サービスに対して認証を行う (`BatchAIManagementClient` オブジェクトを作成する) コードです。

```Python
from azure.common.credentials import ServicePrincipalCredentials
import azure.mgmt.batchai as batchai
import azure.mgmt.batchai.models as models

creds = ServicePrincipalCredentials(
        client_id=client_id, secret=secret, token_uri=token_uri)

batchai_client = batchai.BatchAIManagementClient(credentials=creds,
                                         subscription_id=subscription_id
)
```

## <a name="create-a-resource-group"></a>リソース グループの作成

Batch AI のクラスターとジョブは Azure リソースであり、Azure リソース グループに配置する必要があります。 リソース グループは次のスニペットで作成します。

```Python
from azure.mgmt.resource import ResourceManagementClient

resource_group_name = 'myresourcegroup'

resource_management_client = ResourceManagementClient(
        credentials=creds, subscription_id=subscription_id)

resource = resource_management_client.resource_groups.create_or_update(
        resource_group_name, {'location': 'eastus'})
```


## <a name="prepare-azure-file-share"></a>Azure ファイル共有の準備
このクイック スタートでは説明上、学習ジョブに使用するスクリプトとトレーニング データのホストとして Azure ファイル共有を使います。 

1. *batchaiquickstart* という名前のファイル共有を作成します。

  ```Python
  from azure.storage.file import FileService 
 
  azure_file_share_name = 'batchaiquickstart' 
 
  service = FileService(storage_account_name, storage_account_key) 
 
  service.create_share(azure_file_share_name, fail_on_exist=False)
  ``` 
 
2. *mnistcntksample* という名前の共有にディレクトリを作成します。 

  ```Python
  mnist_dataset_directory = 'mnistcntksample' 
 
  service.create_directory(azure_file_share_name, mnist_dataset_directory, fail_on_exist=False) 
  ```
3. [サンプル パッケージ](https://batchaisamples.blob.core.windows.net/samples/BatchAIQuickStart.zip?st=2017-09-29T18%3A29%3A00Z&se=2099-12-31T08%3A00%3A00Z&sp=rl&sv=2016-05-31&sr=b&sig=hrAZfbZC%2BQ%2FKccFQZ7OC4b%2FXSzCF5Myi4Cj%2BW3sVZDo%3D)をダウンロードしてファイルを解凍します。 その内容を、Python スクリプトの実行場所となるディレクトリにアップロードします。

  ```Python
  for f in ['Train-28x28_cntk_text.txt', 'Test-28x28_cntk_text.txt', 'ConvNet_MNIST.py']:     
     service.create_file_from_path(
             azure_file_share_name, mnist_dataset_directory, f, f) 
  ```

## <a name="create-gpu-cluster"></a>GPU クラスターの作成

Batch AI クラスターを作成します。 この例では、クラスターが単一の STANDARD_NC6 VM ノードから成ります。 この VM には NVIDIA K80 GPU が 1 つ含まれています。 ファイル共有は、*azurefileshare* という名前のフォルダーにマウントします。 このフォルダーの GPU 計算ノード上における完全なパスは $AZ_BATCHAI_MOUNT_ROOT/azurefileshare です。

```Python
cluster_name = 'mycluster'

relative_mount_point = 'azurefileshare' 
 
parameters = models.ClusterCreateParameters(
    # Location where the cluster will physically be deployed
    location='eastus', 
 
    # VM size. Use NC or NV series for GPU
    vm_size='STANDARD_NC6', 
 
    # Configure the ssh users
    user_account_settings=models.UserAccountSettings(
         admin_user_name=admin_user_name,
         admin_user_password=admin_user_password), 
 
    # Number of VMs in the cluster
    scale_settings=models.ScaleSettings(
         manual=models.ManualScaleSettings(target_node_count=1)
     ), 
 
    # Configure each node in the cluster
    node_setup=models.NodeSetup( 
 
        # Mount shared volumes to the host
         mount_volumes=models.MountVolumes(
             azure_file_shares=[
                 models.AzureFileShareReference(
                     account_name=storage_account_name,
                     credentials=models.AzureStorageCredentialsInfo(
         account_key=storage_account_key),
         azure_file_url='https://{0}.file.core.windows.net/{1}'.format(
               storage_account_name, azure_file_share_name),
                  relative_mount_path = relative_mount_point)],
         ), 
    ), 
) 
batchai_client.clusters.create(resource_group_name, cluster_name, parameters).result() 
```

## <a name="get-cluster-status"></a>クラスターの状態の取得

次のコマンドを使って、クラスターの状態を監視します。 

```Python
cluster = batchai_client.clusters.get(resource_group_name, cluster_name)
print('Cluster state: {0} Target: {1}; Allocated: {2}; Idle: {3}; '
      'Unusable: {4}; Running: {5}; Preparing: {6}; leaving: {7}'.format(
    cluster.allocation_state,
    cluster.scale_settings.manual.target_node_count,
    cluster.current_node_count,
    cluster.node_state_counts.idle_node_count,
    cluster.node_state_counts.unusable_node_count,
    cluster.node_state_counts.running_node_count,
    cluster.node_state_counts.preparing_node_count,
    cluster.node_state_counts.leaving_node_count)) 
```

前のコードからは、基本的なクラスターの割り当て情報が次のように出力されます。

```Shell
Cluster state: AllocationState.steady Target: 1; Allocated: 1; Idle: 0; Unusable: 0; Running: 0; Preparing: 1; Leaving 0
 
```  

ノードの割り当てが済んで準備が完了したら (`nodeStateCounts` 属性を参照)、クラスターの準備は完了です。 何か問題がある場合、`errors` 属性にエラーの説明が格納されます。

## <a name="create-training-job"></a>トレーニング ジョブの作成

クラスターの準備が完了したら、学習ジョブを構成して送信します。 

```Python
job_name = 'myjob' 
 
parameters = models.job_create_parameters.JobCreateParameters( 
 
     # Location where the job will run
     # Ideally this should be co-located with the cluster.
     location='eastus', 
 
     # The cluster this job will run on
     cluster=models.ResourceId(cluster.id), 
 
     # The number of VMs in the cluster to use
     node_count=1, 
 
     # Override the path where the std out and std err files will be written to.
     # In this case we will write these out to an Azure Files share
     std_out_err_path_prefix='$AZ_BATCHAI_MOUNT_ROOT/{0}'.format(relative_mount_point), 
 
     input_directories=[models.InputDirectory(
         id='SAMPLE',
         path='$AZ_BATCHAI_MOUNT_ROOT/{0}/{1}'.format(relative_mount_point, mnist_dataset_directory))], 
 
     # Specify directories where files will get written to 
     output_directories=[models.OutputDirectory(
        id='MODEL',
        path_prefix='$AZ_BATCHAI_MOUNT_ROOT/{0}'.format(relative_mount_point),
        path_suffix="Models")], 
 
     # Container configuration
     container_settings=models.ContainerSettings(
        models.ImageSourceRegistry(image='microsoft/cntk:2.1-gpu-python3.5-cuda8.0-cudnn6.0')), 
 
     # Toolkit specific settings
     cntk_settings = models.CNTKsettings(
        python_script_file_path='$AZ_BATCHAI_INPUT_SAMPLE/ConvNet_MNIST.py',
        command_line_args='$AZ_BATCHAI_INPUT_SAMPLE $AZ_BATCHAI_OUTPUT_MODEL')
 ) 
 
# Create the job 
batchai_client.jobs.create(resource_group_name, job_name, parameters).result() 
```

## <a name="monitor-job"></a>ジョブの監視
ジョブの状態は、次のコマンドで確認できます。 
 
```Python
job = batchai_client.jobs.get(resource_group_name, job_name) 
 
print('Job state: {0} '.format(job.execution_state.name))
```

`Job state: running` のような出力が返されます。

ジョブの現在の実行状態は `executionState` に含まれています。
* `queued`: ジョブは、クラスター ノードが使用可能な状態になるのを待機しています
* `running`: ジョブは実行中です
* `succeeded` (または `failed`) : ジョブは完了しています。その結果についての詳しい情報は `executionInfo` に含まれています
 
## <a name="list-stdout-and-stderr-output"></a>stdout と stderr のリスト出力
stdout および stderr のログ ファイルへのリンクをリストするには、次のコマンドを使います。

```Python
files = batchai_client.jobs.list_output_files(resource_group_name, job_name, models.JobsListOutputFilesOptions("stdouterr")) 
 
for file in list(files):
     print('file: {0}, download url: {1}'.format(file.name, file.download_url)) 
```
## <a name="delete-resources"></a>リソースを削除する

ジョブを削除するには次のコマンドを使います。
```Python
batchai_client.jobs.delete(resource_group_name, job_name) 
```

クラスターを削除するには次のコマンドを使います。
```Python
batchai_client.clusters.delete(resource_group_name, cluster_name)
```
## <a name="next-steps"></a>次の手順

このクイック スタートでは、Azure Python SDK から Batch AI クラスター上で CNTK トレーニング ジョブを実行する方法について説明しました。 さまざまなツールキットで Batch AI を使う方法について詳しくは、[トレーニングのレシピ](https://github.com/Azure/BatchAI)をご覧ください。
