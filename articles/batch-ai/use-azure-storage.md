---
title: Azure Storage を使用して Batch AI ジョブの入力と出力を格納する | Microsoft Docs
description: 入力ファイルと出力ファイルの高速かつ簡単なクラウド ストレージに Azure Storage と Batch AI を使用する方法
services: batch-ai
documentationcenter: ''
author: kevwang1
manager: jeconnoc
editor: ''
ms.service: batch-ai
ms.topic: article
ms.date: 08/14/2018
ms.author: danlep
ms.custom: mvc
ROBOTS: NOINDEX
ms.openlocfilehash: 1e9a4c6355c60b18bb78aae362c1e2f142e2d864
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/14/2018
ms.locfileid: "53408002"
---
# <a name="store-batch-ai-job-input-and-output-with-azure-storage"></a>Azure Storage を使用して Batch AI ジョブの入力と出力を格納する

[!INCLUDE [batch-ai-retiring](../../includes/batch-ai-retiring.md)]

このガイドでは、Azure Storage を使用して、ジョブの実行時に入力ファイルと出力ファイルを格納する方法について説明します。 Azure Storage は、Batch AI によってサポートされているいくつかのストレージ オプションの 1 つです。 Batch AI は、クラウドに格納されたファイルへのシームレスなアクセスを許可し、Azure Storage システムを Batch AI ジョブまたはクラスター ファイルシステムにマウントすることにより、Azure Storage と統合されます。 

## <a name="introduction-to-azure-storage"></a>Azure Storage の概要

Azure Storage は、Microsoft のクラウド ストレージ ソリューションです。 Batch AI では、ファイルがネイティブのファイルシステム内にあるように、ジョブからアクセスできるようにして、Azure Blob コンテナーと Azure ファイル共有を Batch AI ジョブやクラスターにマウントすることをサポートします。 Batch AI では [blobfuse](https://github.com/Azure/azure-storage-fuse) を使用して Azure Blob コンテナーがマウントされ、SMB プロトコルによって Azure ファイル共有がマウントされます。 Azure Storage の詳細については、「[Azure Storage の概要](../storage/common/storage-introduction.md)」を参照してください。

## <a name="store-datasets-and-input-scripts-in-azure-storage"></a>Azure Storage でデータセットと入力スクリプトを格納する

ご自分の Batch AI 環境に Azure Storage を選ぶ場合、より高いスループットを持つ BLOB コンテナーに入力ファイル (データセットなど) を格納し、ファイル共有にトレーニングの出力を格納することをお勧めします。ファイル共有では、ストリーミングがサポートされます (ジョブが同時に実行されているときに、出力ログの読み取りを許可します)。 

Azure Storage を使用するには、[Azure Storage アカウントを作成](../storage/common/storage-quickstart-create-account.md)しておく必要があります。 Batch AI では、汎用 v1 (GPv1) と汎用 v2 (GPv2) の両方の Azure Storage アカウントからのボリュームのマウントがサポートされます。 Azure Storage アカウントでは、複数の BLOB コンテナーまたはファイル共有インスタンスを保持できます。 作成するストレージ アカウントの種類を選択するときに、コストとパフォーマンスの要件を検討してください。 詳細については、「[Azure ストレージ アカウントの概要](../storage/common/storage-account-overview.md)」を参照してください。 

BLOB コンテナーを作成して、ご自分のデータセットを Azure BLOB コンテナーにアップロードするには、次のいずれかのメソッドを選びます。
- [Azure portal](../storage/blobs/storage-quickstart-blobs-portal.md) で Web ベースの GUI でアップロード。 Azure portal では、少数のファイルをアップロードするために、最も簡単な操作が提供されます。
- [Azure Storage CLI](../storage/blobs/storage-quickstart-blobs-cli.md) でコマンド ラインを介してアップロード (ディレクトリのアップロードをサポート)。 ファイルのディレクトリをアップロードするには、`az storage blob upload-batch` を使用します。
- [その他の手法](../storage/common/storage-moving-data.md)、アプリケーション SDK の使用を含む。

同様に、Azure ファイル共有を作成するには、次のいずれかのメソッドを選びます。
- [Azure Portal](../storage/files/storage-how-to-use-files-portal.md)
- [Azure Storage CLI](../storage/files/storage-how-to-use-files-cli.md)
- [その他の手法](../storage/common/storage-moving-data.md)

### <a name="auto-storage-with-batch-ai"></a>自動ストレージと Batch AI

さらに、`az batchai cluster create` の `--use-auto-storage` パラメーターを使用して、Azure ファイル共有と BLOB コンテナーで Azure Storage アカウントを作成できます (また、自動的にこれらのボリュームを Batch AI クラスターにマウントできます)。 詳細については、[このページ](https://github.com/Azure/BatchAI/blob/master/documentation/using-azure-cli-20.md#auto-storage-account)を参照してください。

## <a name="mount-azure-storage"></a>Azure Storage をマウントする 

### <a name="mount-volumes-to-a-job"></a>ボリュームをジョブにマウントする

Azure Storage ボリュームをマウントすると、ジョブごとに作成されたファイルシステムによってアクセスできるようになります。 そのため、ジョブではローカル ファイルであるかのように、クラウド ストレージに対してシームレスにファイルの読み取りと書き込みを行うことができます。

Azure Storage ボリュームを Azure CLI で作成されたジョブにマウントするには、`az batchai job create` の実行時にご自分の `job.json` ファイルの `mountVolumes` プロパティを使用します。 たとえば、[Tensorflow GPU Distributed のレシピ](https://github.com/Azure/BatchAI/blob/master/recipes/TensorFlow/TensorFlow-GPU-Distributed/job.json)を参照してください。 `mountVolumes` のスキーマは、次のとおりです。
```json
{
    "mountVolumes": {
        "azureFileShares": [{
            "azureFileUrl": "https://<STORAGE_ACCOUNT_NAME>.file.core.windows.net/<FILE_SHARE_NAME>",
            "relativeMountPath": "<RELATIVE_MOUNT_PATH>"
        }],
        "azureBlobFileSystems": [{
            "accountName": "<STORAGE_ACCOUNT_NAME>",
            "containerName": "<BLOB_CONTAINER_NAME>",
            "relativeMountPath": "<RELATIVE_MOUNT_PATH>"
        }]
    }
}
```

`azureFileShares` と `azureBlobFileSystems` はどちらも、マウントするボリュームを表すオブジェクトの配列です。 プレースホルダーの説明は次のとおりです。

- <RELATIVE_MOUNT_PATH> - ボリュームはこのパスにマウントされます。 たとえば、`relativeMountPath` が `jobs` の場合、ボリュームは `$AZ_BATCHAI_JOB_MOUNT_ROOT/jobs` に配置されます
- <STORAGE_ACCOUNT_NAME> - ファイル共有または BLOB コンテナーを保持する Azure Storage アカウントの名前
- <FILE_SHARE_NAME> - ファイル共有の名前
- <BLOB_CONTAINER_NAME> - BLOB コンテナーの名前

Azure Batch AI SDK を使用して Azure Storage ボリュームをマウントするには、`JobCreateParameters` で `mount_volumes` (Python) または `MountVolumes` (C#、Java) プロパティを設定します。 Azure Batch AI SDK を使用してボリュームをマウントするときに、ストレージ アカウントの資格情報を指定する必要があります。 [Python](https://docs.microsoft.com/python/api/azure-mgmt-batchai/azure.mgmt.batchai.models.MountVolumes?view=azure-python)、[C#](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.batchai.models.mountvolumes?view=azure-dotnet)、および [Java](https://docs.microsoft.com/java/api/com.microsoft.azure.management.batchai._mount_volumes?view=azure-java-stable) でボリュームをマウントするためのスキーマを表示します。

### <a name="mount-volumes-to-a-cluster"></a>ボリュームをクラスターにマウントする

Batch AI では、Azure Storage ボリュームの Batch AI クラスターへのマウントもサポートされます。 ボリュームがクラスターにマウントされると、そのクラスター上で実行されているすべてのジョブで、そのクラスターにマウントされたボリュームを使用できます。 ジョブ レベルのマウントでは最高の柔軟性が提供されますが (各ジョブで異なるマウントされたボリュームを持つことを許可)、シンプルなシナリオではクラスター レベルのマウントで十分な場合があります。

Azure Storage ボリュームを Azure CLI で作成されたクラスターにマウントするには、`az batchai cluster create` の実行時にご自分の `cluster.json` ファイルで `mountVolumes` プロパティを使用します。 クラスターのマウント時の `mountVolumes` に対するスキーマは、ジョブにマウントするときと同じです。 

同様に、Azure Batch AI SDK を使用してマウンティングするときに、`ClusterCreateParameters` で `mount_volumes` (Python) または `MountVolumes` (C#、Java) プロパティを使用できます。 

## <a name="access-mounted-filesystem-in-a-job"></a>ジョブでマウントされたファイルシステムにアクセスする

### <a name="azbatchaijobmountroot-environment-variable"></a>$AZ_BATCHAI_JOB_MOUNT_ROOT 環境変数

ジョブの実行環境内で、マウントされたストレージ システムを含むディレクトリには、`$AZ_BATCHAI_JOB_MOUNT_ROOT` 環境変数 (ジョブ レベルのマウントを使用する場合) でアクセスできます。 クラスター レベルのマウントを使用する場合は、この環境変数は `$AZ_BATCHAI_MOUNT_ROOT` になります。 次の例では、ジョブ レベルのマウントを使用するとします。

マウントされたボリュームにデータのパスを指定するには、マウントされたパスと共に環境変数 `$AZ_BATCHAI_JOB_MOUNT_ROOT` を使用します。 たとえば、トレーニング スクリプト `train.py` が相対マウントパス `scripts` にマウントされた Azure ファイル共有にアップロードされた場合、ファイルは `$AZ_BATCHAI_JOB_MOUNT_ROOT/scripts/train.py` で入手できるようになります。

ご利用のトレーニング スクリプトにパスの知識が必要な場合、コマンドラインの引数としてその知識を渡す必要があります。 たとえば、パス `data` でマウントされた Azure Blob コンテナーで `train_data` と呼ばれるフォルダーにご自分のデータを格納した場合、コマンドラインの引数として `--data-dir=$AZ_BATCHAI_JOB_MOUNT_ROOT/data/train_data` をスクリプトに渡すことができます。 したがって、コマンドラインの引数を受け取るように、ご使用のスクリプトを変更する必要があります。

### <a name="abbreviate-input-paths"></a>入力パスを省略形にする

環境変数として入力パスを省略形にするには、`job.json` ファイルの `inputDirectories` プロパティ (または Batch AI SDK を使用している場合は、`models.JobCreateParameters.input_directories`) を使用します。 `inputDirectories` のスキーマは、次のとおりです。

```json
{
    "inputDirectories": [{
        "id": "<ID>",
        "path": "<PATH>" 
    }]
}
```

指定された各パスは、`$AZ_BATCHAI_INPUT_<ID>` と呼ばれる環境変数に配置されます。 このメソッドを使用すると、入力ファイルまたはディレクトリへのパスを簡略化できます。 たとえば、トレーニング スクリプトへのパスを省略形にするには、`"id"` が `"SCRIPT"` で、`"path"` が `"$AZ_BATCHAI_JOB_MOUNT_ROOT/scripts/train.py"` の場合、そのパスはジョブの実行環境の `$AZ_BATCHAI_INPUT_SCRIPT` で入手できます。

詳細については、[このページ](https://github.com/Azure/BatchAI/blob/master/documentation/using-azure-cli-20.md#input-directories)を参照してください。

### <a name="abbreviate-output-paths"></a>出力パスを省略形にする

環境変数として出力パスを省略形にするには、`job.json` ファイルの `outputDirectories` プロパティ (または Batch AI SDK を使用している場合は、`models.JobCreateParameters.output_directories`) を使用します。 このメソッドを使用すると、出力ファイル用のパスを簡略化できます。 `outputDirectories` のスキーマは、次のとおりです。

```json
{
    "outputDirectories": [{
        "id": "<ID>",
        "pathPrefix": "<PATH_PREFIX>",
        "pathSuffix": "<PATH_SUFFIX>"
    }]
}
```

指定された各パスは、`$AZ_BATCHAI_OUTPUT_<ID>` と呼ばれる環境変数に配置されます。 `pathPrefix` では、出力を格納するためにマウントされたボリュームを決定します (例: `"$AZ_BATCHAI_JOB_MOUNT_ROOT/output"`)。 `pathSuffix` では、出力のフォルダー名を決定します (例: `"logs"`、`"checkpoints"`)。 出力の実際のパスは、`pathPrefix`、`jobOutputDirectoryPathSegment` (ジョブごとに自動生成) および `pathSuffix` の連結です。

詳細については、[このページ](https://github.com/Azure/BatchAI/blob/master/documentation/using-azure-cli-20.md#output-directories)を参照してください。

## <a name="retrieve-job-output-from-azure-storage"></a>Azure Storage からジョブ出力を取得する

### <a name="use-azure-portal"></a>Azure Portal の使用

Azure portal は、GUI ファイル エクスプローラーを使用して、ジョブの出力を表示するのに便利な方法です。 しかし、StdOut と StdErr から、または `outputDirectories` 内のパスから出力を表示する場合は、ファイルはご使用の Azure Storage ボリューム内の自動生成されたパスに配置されます。 詳細については、以下を参照してください。

### <a name="access-stdout-and-stderr-output"></a>StdOut と StdErr の出力にアクセスする

`job.json` の `stdOutErrPathPrefix` プロパティを使用して、ジョブの実行ログおよび StdOut と StdErr の出力を配置する場所をジョブに指示します。 たとえば、相互マウント パス `outputs` でファイル共有をマウントして、`stdOutErrPathPrefix` に `"$AZ_BATCHAI_JOB_MOUNT_ROOT/outputs"` を指定する場合は、StdOut と StdErr ジョブの出力は、そのマウントされたボリューム内の `{subscription id}/{resource group}/workspaces/{workspace name}/experiments/{experiment name}/jobs/{job name}/{job uuid}/stdouterr` で入手できます。 この自動生成されたパスは、同じ名前のジョブ間で出力の競合を防ぐために使用されます。

詳細については、[このページ](https://github.com/Azure/BatchAI/blob/master/documentation/using-azure-cli-20.md#standard-and-error-output)を参照してください。

### <a name="list-the-output-files"></a>出力ファイルを一覧する

Azure CLI で `az batchai job file list` コマンドを使用して、ジョブの使用可能な出力ファイルを一覧できます。 たとえば、ジョブの標準出力ディレクトリのファイルを一覧するには、`az batchai job file list -j <JOB_NAME> -g <RESOURCE_GROUP_NAME> -w <WORKSPACE_NAME> -e <EXPERIMENT_NAME>` を使用します。

詳細と例については、[こちら](https://github.com/Azure/BatchAI/blob/master/documentation/using-azure-cli-20.md#stream-files-from-output-directories)をご覧ください。

### <a name="stream-output-files"></a>出力ファイルをストリーミングする

Azure CLI を使用して、`az batchai job file stream` コマンドでファイルをストリーミングすることができます。 たとえば、次のコマンドで表示します。
- StdOut: `az batchai job file stream -j <JOB_NAME> -g <RESOURCE_GROUP_NAME> -w <WORKSPACE_NAME> -e <EXPERIMENT_NAME> -f stdout.txt`
- StdErr: `az batchai job file stream -j <JOB_NAME> -g <RESOURCE_GROUP_NAME> -w <WORKSPACE_NAME> -e <EXPERIMENT_NAME> -f stderr.txt`

詳細と例については、[こちら](https://github.com/Azure/BatchAI/blob/master/documentation/using-azure-cli-20.md#stream-files-from-output-directories)をご覧ください。

## <a name="next-steps"></a>次の手順
- Azure Storage でのインターフェイスへの CLI コマンドに関する詳細については、[Azure Batch AI CLI のドキュメント](https://github.com/Azure/BatchAI/blob/master/documentation/using-azure-cli-20.md)を参照してください。
- ストレージのマウントや出力ファイルの読み取りなど、Batch AI の使用例をさらに確認するには、[Batch AI に対する Jupyter Notebook レシピ](https://github.com/Azure/BatchAI)を参照してください。
- [NFS サーバーのマウント](https://github.com/Azure/BatchAI/blob/master/documentation/using-azure-cli-20.md#mounting-nfs)および[独自の NFS、CIFS、または GlusterFS クラスターのマウント](https://github.com/Azure/BatchAI/blob/master/documentation/using-azure-cli-20.md#mounting-nfs)など、ストレージのマウント向けのその他のオプションを参照してください。