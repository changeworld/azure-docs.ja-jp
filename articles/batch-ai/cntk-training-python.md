---
title: Azure Batch AI での CNTK モデルのトレーニング - Python | Microsoft Docs
description: Python SDK を使用して Azure Batch AI で Microsoft Cognitive Toolkit (CNTK) ニューラル ネットワークをトレーニングします
services: batch-ai
documentationcenter: na
author: lliimsft
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.custom: ''
ms.service: batch-ai
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: Python
ms.topic: quickstart
ms.date: 08/15/2018
ms.author: danlep
ROBOTS: NOINDEX
ms.openlocfilehash: 86ce9fd77ccf83ed6f5f1c722b4c8c809ef0eedf
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/14/2018
ms.locfileid: "53407918"
---
# <a name="run-a-cntk-training-job-using-the-azure-python-sdk"></a>Azure Python SDK を使って CNTK トレーニング ジョブを実行する

[!INCLUDE [batch-ai-retiring](../../includes/batch-ai-retiring.md)]

この記事では、Azure Python SDK を使い、Batch AI サービスを使用してサンプルの Microsoft Cognitive Toolkit (CNTK) モデルをトレーニングする方法について説明します。

この例では、手書き画像の MNIST データベースを使い、単一ノードの GPU クラスター上で畳み込みニューラルネットワーク (CNN: Convolutional Neural Network) のトレーニングを実施します。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション - Azure サブスクリプションをお持ちでない場合は、開始する前に[無料のアカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。

* Azure Python SDK - [インストール手順](/python/azure/python-sdk-azure-install)をご覧ください。 この記事では、バージョン 2.0.0 以降の azure-mgmt-batchai パッケージが必要です。

* Azure Storage アカウント - [Azure Storage アカウントの作成方法](../storage/common/storage-create-storage-account.md)に関するページをご覧ください。

* Azure Active Directory サービス プリンシパルの資格情報 - [CLI でサービス プリンシパルを作成する方法](../azure-resource-manager/resource-group-authenticate-service-principal-cli.md)に関するページをご覧ください。

* Azure Cloud Shell または Azure CLI を使って、ご利用のサブスクリプションについて 1 回、Batch AI リソース プロバイダーを登録します。 プロバイダーの登録にかかる時間は最大で 15 分程度です。

```azurecli
az provider register -n Microsoft.BatchAI
```

## <a name="configure-credentials"></a>資格情報の構成

スクリプト ファイルに次のコードを追加します。このとき、`FILL-IN-HERE` を適切な値に置き換えます。

```Python
# credentials used for authentication
aad_client_id = 'FILL-IN-HERE'
aad_secret = 'FILL-IN-HERE'
aad_tenant = 'FILL-IN-HERE'
subscription_id = 'FILL-IN-HERE'

# credentials used for storage
storage_account_name = 'FILL-IN-HERE'
storage_account_key = 'FILL-IN-HERE'

# specify the credentials used to remote login your GPU node
admin_user_name = 'FILL-IN-HERE'
admin_user_password = 'FILL-IN-HERE'

# specify the location in which to create Batch AI resources
mylocation = 'eastus'
```

ソースコードに資格情報を含めるのは良いやり方ではありませんが、ここではクイック スタートを簡単にする目的でこのようにしていることに注意してください。
代わりに、環境変数または別個の構成ファイルを使用することを検討してください。

## <a name="create-batch-ai-client"></a>Batch AI クライアントの作成

次のコードを実行すると、サービス プリンシパルの資格証明オブジェクトと Batch AI クライアントが作成されます。

```Python
from azure.common.credentials import ServicePrincipalCredentials
import azure.mgmt.batchai as batchai
import azure.mgmt.batchai.models as models

creds = ServicePrincipalCredentials(
        client_id=aad_client_id, secret=aad_secret, tenant=aad_tenant)

batchai_client = batchai.BatchAIManagementClient(
    credentials=creds, subscription_id=subscription_id)
```

## <a name="create-a-resource-group"></a>リソース グループの作成

Batch AI のクラスターとジョブは Azure リソースであり、Azure リソース グループに配置する必要があります。 リソース グループは次のスニペットで作成します。

```Python
from azure.mgmt.resource import ResourceManagementClient

resource_group_name = 'myresourcegroup'
resource_management_client = ResourceManagementClient(
        credentials=creds, subscription_id=subscription_id)
resource = resource_management_client.resource_groups.create_or_update(
        resource_group_name, {'location': mylocation})
```


## <a name="prepare-azure-file-share"></a>Azure ファイル共有の準備

このクイック スタートでは、説明上、Azure Files 共有を使用して、トレーニング ジョブのトレーニング データとスクリプトをホストしています。

`batchaiquickstart` という名前のファイル共有を作成します。

```Python
from azure.storage.file import FileService
azure_file_share_name = 'batchaiquickstart'
service = FileService(storage_account_name, storage_account_key)
service.create_share(azure_file_share_name, fail_on_exist=False)
```

共有内に `mnistcntksample` という名前のディレクトリを作成します。

```Python
mnist_dataset_directory = 'mnistcntksample'
service.create_directory(azure_file_share_name, mnist_dataset_directory, fail_on_exist=False)
```
[サンプル パッケージ](https://batchaisamples.blob.core.windows.net/samples/BatchAIQuickStart.zip?st=2017-09-29T18%3A29%3A00Z&se=2099-12-31T08%3A00%3A00Z&sp=rl&sv=2016-05-31&sr=b&sig=hrAZfbZC%2BQ%2FKccFQZ7OC4b%2FXSzCF5Myi4Cj%2BW3sVZDo%3D)をダウンロードし、現在のディレクトリに解凍します。 次のコードを実行すると、必要なファイルが Azure ファイル共有にアップロードされます。

```Python
for f in ['Train-28x28_cntk_text.txt', 'Test-28x28_cntk_text.txt',
          'ConvNet_MNIST.py']:
     service.create_file_from_path(
             azure_file_share_name, mnist_dataset_directory, f, f)
```

## <a name="create-batch-ai-workspace"></a>Batch AI ワークスペースの作成

ワークスペースは、全種類の Batch AI リソースの最上位のコレクションとなります。 Batch AI のクラスターと実験は、どちらもワークスペースに作成します。

```Python
workspace_name='myworkspace'
batchai_client.workspaces.create(resource_group_name, workspace_name, mylocation)
```

## <a name="create-gpu-cluster"></a>GPU クラスターの作成

Batch AI クラスターを作成します。 この例では、クラスターが単一の STANDARD_NC6 VM ノードから成ります。 この VM には NVIDIA K80 GPU が 1 つ含まれています。 `azurefileshare` という名前のフォルダーにファイル共有をマウントします。 このフォルダーの GPU 計算ノード上における完全なパスは `$AZ_BATCHAI_MOUNT_ROOT/azurefileshare` です。

```Python
cluster_name = 'mycluster'

relative_mount_point = 'azurefileshare'

parameters = models.ClusterCreateParameters(
    # VM size. Use N-series for GPU
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
                    azure_file_url='https://{0}/{1}'.format(
                        service.primary_endpoint, azure_file_share_name),
                    relative_mount_path=relative_mount_point)],
        ),
    ),
)
batchai_client.clusters.create(resource_group_name, workspace_name, cluster_name,
                               parameters).result()
```

## <a name="get-cluster-status"></a>クラスターの状態の取得

次のコマンドを使って、クラスターの状態を監視します。

```Python
cluster = batchai_client.clusters.get(resource_group_name, workspace_name, cluster_name)
print('Cluster state: {0} Target: {1}; Allocated: {2}; Idle: {3}; '
      'Unusable: {4}; Running: {5}; Preparing: {6}; Leaving: {7}'.format(
    cluster.allocation_state,
    cluster.scale_settings.manual.target_node_count,
    cluster.current_node_count,
    cluster.node_state_counts.idle_node_count,
    cluster.node_state_counts.unusable_node_count,
    cluster.node_state_counts.running_node_count,
    cluster.node_state_counts.preparing_node_count,
    cluster.node_state_counts.leaving_node_count))
```

前のコードからは、次の例のような基本的なクラスターの割り当て情報が出力されます。

```
Cluster state: AllocationState.steady Target: 1; Allocated: 1; Idle: 0; Unusable: 0; Running: 0; Preparing: 1; Leaving: 0
```

ノードの割り当てが済んで準備が完了したら (`nodeStateCounts` 属性を参照)、クラスターの準備は完了です。 何か問題がある場合、`errors` 属性にエラーの説明が格納されます。

## <a name="create-experiment-and-training-job"></a>実験とトレーニング ジョブの作成

クラスターを作成したら、実験 (相互に関連するジョブのグループを格納する論理コンテナー) を作成します。 その後、実験内で学習ジョブを構成して送信します。

```Python
experiment_name='myexperiment'

batchai_client.experiments.create(resource_group_name, workspace_name, experiment_name)

job_name = 'myjob'

parameters = models.JobCreateParameters(
    # The cluster this job will run on
    cluster=models.ResourceId(id=cluster.id),
    # The number of VMs in the cluster to use
    node_count=1,
    # Write job's standard output and execution log to Azure File Share
    std_out_err_path_prefix='$AZ_BATCHAI_MOUNT_ROOT/{0}'.format(
        relative_mount_point),
    # Configure location of the training script and MNIST dataset
    input_directories=[models.InputDirectory(
        id='SAMPLE',
        path='$AZ_BATCHAI_MOUNT_ROOT/{0}/{1}'.format(
            relative_mount_point, mnist_dataset_directory))],
    # Specify location where generated model will be stored
    output_directories=[models.OutputDirectory(
        id='MODEL',
        path_prefix='$AZ_BATCHAI_MOUNT_ROOT/{0}'.format(relative_mount_point),
        path_suffix="Models")],
    # Container configuration
    container_settings=models.ContainerSettings(
        image_source_registry=models.ImageSourceRegistry(
            image='microsoft/cntk:2.1-gpu-python3.5-cuda8.0-cudnn6.0')),
    # Toolkit specific settings
    cntk_settings=models.CNTKsettings(
        python_script_file_path='$AZ_BATCHAI_INPUT_SAMPLE/ConvNet_MNIST.py',
        command_line_args='$AZ_BATCHAI_INPUT_SAMPLE $AZ_BATCHAI_OUTPUT_MODEL')
)

# Create the job
batchai_client.jobs.create(resource_group_name, workspace_name, experiment_name, job_name, parameters).result()
```

## <a name="monitor-job"></a>ジョブの監視

ジョブの状態は、次のコードを使用して確認できます。

```Python
job = batchai_client.jobs.get(resource_group_name, workspace_name, experiment_name, job_name)

print('Job state: {0} '.format(job.execution_state))
```

`Job state: running` のような出力が返されます。

ジョブの現在の実行状態は `executionState` に含まれています。
* `queued`: ジョブは、クラスター ノードが使用可能な状態になるのを待機しています
* `running`: ジョブは実行中です
* `succeeded` (または `failed`): ジョブが完了しました。`executionInfo` に結果の詳細が格納されています

## <a name="list-stdout-and-stderr-output"></a>stdout と stderr のリスト出力

生成された stdout、stderr、およびログ ファイルを一覧表示するには、次のコードを使用します。

```Python
files = batchai_client.jobs.list_output_files(
    resource_group_name, workspace_name, experiment_name, job_name,
    models.JobsListOutputFilesOptions(outputdirectoryid="stdouterr"))

for file in (f for f in files if f.download_url):
    print('file: {0}, download url: {1}'.format(file.name, file.download_url))
```

## <a name="list-generated-model-files"></a>生成されたモデル ファイルの一覧表示
生成されたモデル ファイルを一覧表示するには、次のコードを使用します。
```Python
files = batchai_client.jobs.list_output_files(
    resource_group_name, workspace_name, experiment_name,job_name,
    models.JobsListOutputFilesOptions(outputdirectoryid="MODEL"))

for file in (f for f in files if f.download_url):
    print('file: {0}, download url: {1}'.format(file.name, file.download_url))
```

## <a name="delete-resources"></a>リソースを削除する

ジョブを削除するには、次のコードを使用します。
```Python
batchai_client.jobs.delete(resource_group_name, workspace_name, experiment_name, job_name)
```

クラスターを削除するには、次のコードを使用します。
```Python
batchai_client.clusters.delete(resource_group_name, workspace_name, cluster_name)
```

割り当てられているすべてのリソースを削除するには、次のコードを使用します。
```Python
resource_management_client.resource_groups.delete('myresourcegroup')
```
## <a name="next-steps"></a>次の手順

さまざまなフレームワークで Batch AI を使う方法の詳細については、[トレーニングのレシピ](https://github.com/Azure/BatchAI)をご覧ください。