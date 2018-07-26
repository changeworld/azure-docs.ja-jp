---
title: Azure Batch の コンテナー ワークロード | Microsoft Docs
description: Azure Batch で コンテナー イメージからアプリケーションを実行する方法について説明します。
services: batch
author: dlepow
manager: jeconnoc
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.workload: na
ms.date: 06/04/2018
ms.author: danlep
ms.openlocfilehash: a85db0315a2ee8aa9fd34b8c18893f4cb1068528
ms.sourcegitcommit: e32ea47d9d8158747eaf8fee6ebdd238d3ba01f7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/17/2018
ms.locfileid: "39090964"
---
# <a name="run-container-applications-on-azure-batch"></a>Azure Batch で コンテナー アプリケーションを実行する

Azure Batch を使用すると、Azure で大量のバッチ コンピューティング ジョブを実行し、また、その実行量を調整できます。 Batch タスクは Batch プール内の仮想マシン (ノード) で直接実行できますが、タスクがノード上の Docker と互換性のあるコンテナーで実行されるように Batch プールを設定することもできます。 この記事では、実行中のコンテナー タスクをサポートするコンピューティング ノードのプールを作成して、プールでコンテナー タスクを実行する方法について説明します。 

コンテナーの概念および Batch プールとジョブを作成する方法に精通しておく必要があります。 コード例では、Batch .NET と Python SDK を使用します。 また、Batch SDK、および Azure Portal などのツールを使用して、コンテナー対応の Batch プールを作成したり、コンテナー タスクを実行したりすることもできます。

## <a name="why-use-containers"></a>コンテナーを使用する理由

コンテナーを使用すると、Batch タスクを簡単に実行できます。アプリケーションを実行する目的で環境や依存関係を管理する必要はありません。 コンテナーにより、アプリケーションが、複数の異なる環境で実行できる、軽量で移植可能かつ自律的なユニットとしてデプロイされます。 たとえば、コンテナーをローカルで構築し、テストしてから、コンテナー イメージを Azure のレジストリなどにアップロードできます。 コンテナー デプロイメント モデルにより、アプリケーションのランタイム環境が、そのアプリケーションをホストする場所がどこであっても、常に適切にインストールおよび構成されます。 Batch のコンテナー ベース タスクでは、アプリケーション パッケージ、リソース ファイルの管理、出力ファイルの管理など、コンテナー タスク以外の機能も活用されます。 

## <a name="prerequisites"></a>前提条件

* **SDK バージョン**: 次のバージョンの時点で、Batch SDK ではコンテナー イメージをサポートしています。
    * Batch REST API バージョン 2017-09-01.6.0
    * Batch .NET SDK バージョン 8.0.0
    * Batch Python SDK バージョン 4.0
    * Batch Java SDK バージョン 3.0
    * Batch Node.js SDK バージョン 3.0

* **アカウント**: ご使用の Azure サブスクリプションで、Batch アカウントを作成する必要があります。また、必要に応じて、Azure Storage アカウントを作成します。

* **サポートされている VM イメージ**: コンテナーは、以下の「サポートされている仮想マシン イメージ」セクションで説明するイメージの仮想マシン構成で作成されたプールでのみサポートされます。 カスタム イメージを提供する場合は、次のセクションの注意点と「[マネージ カスタム イメージを使用して仮想マシンのプールを作成する](batch-custom-images.md)」の要件を参照してください。 

### <a name="limitations"></a>制限事項

* Batch では、Linux プールで実行されているコンテナーに対してのみ、RDMA サポートが提供されます

* Windows コンテナー ワークロードの場合は、プールにマルチコア VM サイズを選択することをお勧めします

## <a name="supported-virtual-machine-images"></a>サポートされている仮想マシン イメージ

コンテナー ワークロードのために VM コンピューティング ノードのプールを作成するには、次のいずれかのサポートされている Windows または Linux イメージを使用します。 Batch と互換性がある Marketplace イメージの詳細については、「[仮想マシン イメージの一覧](batch-linux-nodes.md#list-of-virtual-machine-images)」を参照してください。 

### <a name="windows-images"></a>Windows イメージ

Windows コンテナーのワークロードについては、Batch が現在サポートしているのは Azure Marketplace の **Windows Server 2016 Datacenter with Containers** イメージです。 Windows では、Docker コンテナーのイメージのみがサポートされています。

Windows で Docker を実行している VM からカスタム イメージを作成することもできます。

### <a name="linux-images"></a>Linux イメージ

Linux コンテナー ワークロードについては、Batch が現在サポートしているのは Azure Marketplace の Microsoft Azure Batch によって発行された次の Linux イメージです。

* **Azure Batch コンテナー プール用の CentOS**

* **Azure Batch コンテナー プール用の CentOS (RDMA ドライバー付き)**

* **Azure Batch コンテナー プール用の Ubuntu Server**

* **Azure Batch コンテナー プール用の Ubuntu Server (RDMA ドライバー付き)**

これらのイメージがサポートされるのは、Azure Batch プールでの使用のみです。 これらには以下が装備されています。

* 事前インストールされた [Moby](https://github.com/moby/moby) コンテナー ランタイム 

* Azure N シリーズ VM でのデプロイを合理化するための、事前インストールされた NVIDIA GPU ドライバー

* RDMA ドライバーが事前インストールされている、または事前インストールされていないイメージ。これらのドライバーを使用すると、RDMA 対応の VM サイズにデプロイされている場合、プール ノードが Azure RDMA ネットワークにアクセスできます  

Docker を実行している VM から、Batch と互換性のある Linux ディストリビューションのいずれかでカスタム イメージを作成することもできます。 独自のカスタム Linux イメージを提供する場合は、「[マネージド カスタム イメージを使用して仮想マシンのプールを作成する](batch-custom-images.md)」の手順を参照してください。

カスタム イメージ上の Docker サポートの場合、[Docker Community Edition (CE)](https://www.docker.com/community-edition) または [Docker Enterprise Edition (EE)](https://www.docker.com/enterprise-edition) をインストールします。

カスタム Linux イメージを使用するためのその他の注意点:

* カスタム イメージを使用する場合に Azure N シリーズ サイズの GPU パフォーマンスを活用するには、事前に NVIDIA ドライバーをインストールします。 また、NVIDIA GPU の Docker エンジン ユーティリティ、[NVIDIA Docker](https://github.com/NVIDIA/nvidia-docker) をインストールする必要もあります。

* Azure RDMA ネットワークにアクセスするには、RDMA 対応の VM サイズを使用します。 必要な RDMA ドライバーは、Batch でサポートされている CentOS HPC イメージおよび Ubuntu イメージにインストールされます。 MPI ワークロードを実行するには、追加構成が必要になる可能性があります。 「[Batch プールでの RDMA 対応または GPU 対応インスタンスの使用](batch-pool-compute-intensive-sizes.md)」を参照してください。


## <a name="container-configuration-for-batch-pool"></a>Batch プール用のコンテナー構成

Batch プールでコンテナー ワークロードを実行するには、プールの [VirtualMachineConfiguration](/dotnet/api/microsoft.azure.batch.virtualmachineconfiguration) オブジェクトに [ContainerConfiguration](/dotnet/api/microsoft.azure.batch.containerconfiguration) 設定を指定する必要があります。 (この記事では、Batch .NET API 参照へのリンクを提供しています。 対応する設定は、[Batch Python](/python/api/azure.batch) API にあります。)

次の例のように、プリフェッチされたコンテナー イメージを使用して、または使用しないでコンテナー対応プールを作成できます。 プル (プリフェッチ) プロセスにより、インターネット上の別のコンテナー レジストリまたは Docker Hub のいずれかから、コンテナー イメージを事前に読み込むことができます。 最も高いパフォーマンスを得るには、Batch アカウントと同じリージョンにある [Azure コンテナー レジストリ](../container-registry/container-registry-intro.md)を使用します。

コンテナー イメージをプリフェッチするメリットは、初めてタスクを実行するとき、コンテナー イメージがダウンロードされるのを、そのタスクが待たなくてもよい点です。 プールの作成時に、コンテナー構成によって、コンテナー イメージが VM にプルされます。 これにより、プール上で実行されるタスクが、コンテナー イメージとコンテナー実行オプションの一覧を参照できます。


### <a name="pool-without-prefetched-container-images"></a>プリフェッチされたコンテナー イメージを使用しないプール

プリフェッチされたコンテナー イメージを使用せずにコンテナー対応プールを構成するには、次の Python の例に示すように `ContainerConfiguration` オブジェクトと `VirtualMachineConfiguration` オブジェクトを定義します。 この例では、Marketplace から Azure Batch コンテナー プール イメージ用の Ubuntu Server を使用します。


```python
image_ref_to_use = batch.models.ImageReference(
        publisher='microsoft-azure-batch',
        offer='ubuntu-server-container',
        sku='16-04-lts',
        version='latest')

"""
Specify container configuration. This is required even though there are no prefetched images.
"""

container_conf = batch.models.ContainerConfiguration()

new_pool = batch.models.PoolAddParameter(
        id=pool_id,
        virtual_machine_configuration=batch.models.VirtualMachineConfiguration(
            image_reference=image_ref_to_use,
            container_configuration=container_conf,
            node_agent_sku_id='batch.node.ubuntu 16.04'),
        vm_size='STANDARD_D1_V2',
        target_dedicated_nodes=1)
...
```


### <a name="prefetch-images-for-container-configuration"></a>コンテナー構成用にイメージをプリフェッチする

プールでコンテナー イメージをプリフェッチするには、コンテナー イメージの一覧 (Python の `container_image_names`) を `ContainerConfiguration` に追加します。 

次の基本的な Python の例では、[Docker Hub](https://hub.docker.com) から標準 Ubuntu コンテナー イメージをプリフェッチする方法を示します。

```python
image_ref_to_use = batch.models.ImageReference(
    publisher='microsoft-azure-batch',
    offer='ubuntu-server-container',
    sku='16-04-lts',
    version='latest')

"""
Specify container configuration, fetching the official Ubuntu container image from Docker Hub. 
"""

container_conf = batch.models.ContainerConfiguration(container_image_names=['ubuntu'])

new_pool = batch.models.PoolAddParameter(
    id=pool_id,
    virtual_machine_configuration=batch.models.VirtualMachineConfiguration(
        image_reference=image_ref_to_use,
        container_configuration=container_conf,
        node_agent_sku_id='batch.node.ubuntu 16.04'),
    vm_size='STANDARD_D1_V2',
    target_dedicated_nodes=1)
...
```


次の C# の例では、TensorFlow イメージを [Docker Hub](https://hub.docker.com) からプリフェッチすると仮定しています。 この例には、プール ノード上の VM ホストで実行される開始タスクを含めています。 たとえば、コンテナーからアクセスできるファイル サーバーをマウントする目的で、ホストで開始タスクを実行することがあります。

```csharp

ImageReference imageReference = new ImageReference(
    publisher: "microsoft-azure-batch",
    offer: "ubuntu-server-container",
    sku: "16-04-lts",
    version: "latest");

// Specify container configuration, prefetching Docker images
ContainerConfiguration containerConfig = new ContainerConfiguration(
    containerImageNames: new List<string> { "tensorflow/tensorflow:latest-gpu" } );

// VM configuration
VirtualMachineConfiguration virtualMachineConfiguration = new VirtualMachineConfiguration(
    imageReference: imageReference,
    containerConfiguration: containerConfig,
    nodeAgentSkuId: "batch.node.ubuntu 16.04");

// Set a native host command line start task
StartTask startTaskNative = new StartTask( CommandLine: "<native-host-command-line>" );

// Create pool
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    targetDedicatedComputeNodes: 4,
    virtualMachineSize: "Standard_NC6",
    virtualMachineConfiguration: virtualMachineConfiguration, startTaskContainer);
...
```


### <a name="prefetch-images-from-a-private-container-registry"></a>プライベート コンテナー レジストリからイメージをプリフェッチする

プライベート コンテナー レジストリ サーバーの認証により、コンテナー イメージをプリフェッチすることもできます。 次の例では、`ContainerConfiguration` および `VirtualMachineConfiguration` オブジェクトは、プライベート TensorFlow イメージをプライベート Azure コンテナー レジストリからプリフェッチします。 イメージ参照は前の例と同じです。

```csharp
// Specify a container registry
ContainerRegistry containerRegistry = new ContainerRegistry (
    registryServer: "myContainerRegistry.azurecr.io",
    username: "myUserName",
    password: "myPassword");

// Create container configuration, prefetching Docker images from the container registry
ContainerConfiguration containerConfig = new ContainerConfiguration(
    containerImageNames: new List<string> {
        "myContainerRegistry.azurecr.io/tensorflow/tensorflow:latest-gpu" },
    containerRegistries: new List<ContainerRegistry> { containerRegistry } );

// VM configuration
VirtualMachineConfiguration virtualMachineConfiguration = new VirtualMachineConfiguration(
    imageReference: imageReference,
    containerConfiguration: containerConfig,
    nodeAgentSkuId: "batch.node.ubuntu 16.04");

// Create pool
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    targetDedicatedComputeNodes: 4,
    virtualMachineSize: "Standard_NC6",
    virtualMachineConfiguration: virtualMachineConfiguration);
...
```


## <a name="container-settings-for-the-task"></a>タスク用のコンテナー設定

コンピューティング ノードでコンテナー タスクを実行するには、タスク実行オプション、使用するイメージ、レジストリなど、コンテナー固有設定を指定する必要があります。

コンテナー固有の設定を構成するには、タスク クラスの `ContainerSettings` プロパティを使用します。 これらの設定は、[TaskContainerSettings](/dotnet/api/microsoft.azure.batch.taskcontainersettings) クラスによって定義されます。

コンテナー イメージでタスクを実行する場合は、[クラウド タスク](/dotnet/api/microsoft.azure.batch.cloudtask)と[ジョブ マネージャー タスク](/dotnet/api/microsoft.azure.batch.cloudjob.jobmanagertask)にコンテナー設定が必要です。 ただし、[開始タスク](/dotnet/api/microsoft.azure.batch.starttask)、[ジョブの準備タスク](/dotnet/api/microsoft.azure.batch.cloudjob.jobpreparationtask)、および[ジョブの解放タスク](/dotnet/api/microsoft.azure.batch.cloudjob.jobreleasetask)にはコンテナー設定は不要です (つまり、コンテナーのコンテキスト内で、またはノード上で直接実行できます)。

Azure Batch のコンテナー タスク用のコマンド ラインは、コンテナー内の作業ディレクトリで実行されます。これは、通常の (コンテナーでない) タスク用に Batch が設定する環境によく似ています。

* `AZ_BATCH_NODE_ROOT_DIR` (ノード上の Azure Batch ディレクトリのルート) の下のすべてのディレクトリが、コンテナー内に再帰的にマップされます。
* タスクの環境変数がすべて、コンテナー内にマップされます。
* アプリケーションの作業ディレクトリは、通常のタスクの場合と同様に設定されます。そのため、 アプリケーション パッケージやリソース ファイルなどの機能が使用可能です。

Batch は、コンテナー内の既定の作業ディレクトリを変更します。そのため、タスクは、通常のコンテナー エントリ ポイント (例えば、Windows コンテナーの場合は、既定で `c:\`、Linux の場合は `/`) とは別の場所で実行されます。 タスクのコマンド ラインまたはコンテナー エントリ ポイントでは、絶対パスが指定されるようにしてください (まだそのように構成されていない場合)。

次の Python スニペットは、Docker Hub からプルされた Ubuntu コンテナーで実行されている基本的なコマンド ラインを示しています。 コンテナーの実行オプションは、タスクが実行する `docker create` コマンドの追加引数です。 ここでは、タスクの終了後に `--rm` オプションがコンテナーを削除します。

```python
task_id = 'sampletask'
task_container_settings = batch.models.TaskContainerSettings(
    image_name='ubuntu', 
    container_run_options='--rm')
task = batch.models.TaskAddParameter(
    id=task_id,
    command_line='/bin/echo hello',
    container_settings=task_container_settings
)

```

次の C# の例は、クラウド タスクの基本的なコンテナー設定を示しています。

```csharp
// Simple container task command

string cmdLine = "c:\myApp.exe";

TaskContainerSettings cmdContainerSettings = new TaskContainerSettings (
    imageName: "tensorflow/tensorflow:latest-gpu",
    containerRunOptions: "--rm --read-only"
    );

CloudTask containerTask = new CloudTask (
    id: "Task1",
    containerSettings: cmdContainerSettings,
    commandLine: cmdLine); 
```


## <a name="next-steps"></a>次の手順

* [Shipyard レシピ](https://github.com/Azure/batch-shipyard/tree/master/recipes)を使用して Azure Batch でコンテナー ワークロードを簡単にデプロイする方法については、[Batch Shipyard](https://github.com/Azure/batch-shipyard) ツールキットも参照してください。

* Linux での Docker CE のインストールおよび使用の詳細については、[Docker](https://docs.docker.com/engine/installation/) ドキュメントをご覧ください。

* カスタム イメージの使用方法の詳細については、「[マネージド カスタム イメージを使用して仮想マシンのプールを作成する](batch-custom-images.md)」を参照してください。

* コンテナー ベースのシステムを作成するためのフレームワークである、[Moby プロジェクト](https://mobyproject.org/)について詳細をご確認ください。