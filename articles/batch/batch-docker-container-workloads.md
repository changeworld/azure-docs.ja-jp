---
title: "Azure Batch の コンテナー ワークロード | Microsoft Docs"
description: "Azure Batch で コンテナー イメージからアプリケーションを実行する方法について説明します。"
services: batch
author: dlepow
manager: jeconnoc
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.workload: na
ms.date: 12/01/2017
ms.author: danlep
ms.openlocfilehash: 2fa5f9335a4d00f489f11c0db23322ab971a224f
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/01/2018
---
# <a name="run-container-applications-on-azure-batch"></a>Azure Batch で コンテナー アプリケーションを実行する

Azure Batch を使用すると、Azure で膨大な数のバッチ コンピューティング ジョブを実行および拡大縮小できます。 これまで Batch タスクは、Batch プール内の仮想マシン (VM) で直接実行されていましたが、タスクが Docker コンテナーで実行されるように Batch プールを設定できるようになりました。

コンテナーを使用すると、Batch タスクを簡単に実行できます。アプリケーション パッケージと依存関係を管理する必要はありません。 コンテナーにより、アプリケーションが、さまざまな環境で実行できる、軽量で移植可能かつ自律的なユニットとしてデプロイされます。 たとえば、コンテナーをローカルで構築し、テストしてから、コンテナー イメージを Azure のレジストリなどにアップロードできます。 コンテナー デプロイメント モデルにより、アプリケーションのランタイム環境が、そのアプリケーションをホストする場所に関係なく、常に適切にインストールおよび構成されます。 このチュートリアルでは、Batch.NET SDK を使用して、実行中のコンテナー タスクをサポートするコンピューティング ノードのプールを作成する方法と、プールでコンテナー タスクを実行する方法について説明します。

この記事は、読者が Docker コンテナーの概念と、.NET SDK を使用して、Batch プールとジョブを作成する方法について熟知していることを前提とします。 コード スニペットは、[DotNetTutorial サンプル](batch-dotnet-get-started.md)のようなクライアント アプリケーションで使用するためのもので、Batch でコンテナー アプリケーションをサポートする必要があるコードの例です。


## <a name="prerequisites"></a>前提条件

* SDK バージョン: 次のバージョンの Batch SDK がコンテナー イメージをサポートします。
    * Batch REST API バージョン 2017-09-01.6.0
    * Batch .NET SDK バージョン 8.0.0
    * Batch Python SDK バージョン 4.0
    * Batch Java SDK バージョン 3.0
    * Batch Node.js SDK バージョン 3.0

* アカウント: ご使用の Azure アカウントで、Batch アカウントを作成する必要があります。また、必要に応じて、汎用の Storage アカウントを作成します。

* サポートされている VM イメージ。 コンテナーは、以下の「サポートされている仮想マシン イメージ」で詳しく説明するイメージの仮想マシン構成で作成されたプールでのみサポートされます。

* カスタム イメージを指定する場合、コンテナー ベースのワークロードを実行するには、アプリケーションで Azure Active Directory (Azure AD) 認証を使用する必要があります。 Azure Marketplace イメージを使用する場合、Azure AD 認証は必要ありません。共有キー認証を使用できます。 Azure AD の Azure Batch のサポートについては、「[Batch サービスの認証に Active Directory を使用する](batch-aad-auth.md)」に記載されています。


## <a name="supported-virtual-machine-images"></a>サポートされている仮想マシン イメージ

VM コンピューティング ノードのプールを作成するには、Windows または Linux イメージを提供する必要があります。

### <a name="windows-images"></a>Windows イメージ

Windows コンテナーのワークロードについては、Batch が現在サポートしているのは、Windows で Docker を実行している VM から作成したカスタム イメージです。また、Azure Marketplace の Windows Server 2016 Datacenter with Containers イメージを使用することもできます。 このイメージは、`batch.node.windows amd64` ノード エージェント SKU ID と互換性があります。 サポートされているコンテナーの種類は、現時点では Docker に制限されています。

### <a name="linux-images"></a>Linux イメージ

Linux コンテナーのワークロードについては、Batch が現在サポートしているのは、Linux ディストリビューション Ubuntu 16.04 LTS または CentOS 7.3 で Docker を実行している VM から作成したカスタム イメージのみです。 独自のカスタム Linux イメージを提供する場合は、「[マネージ カスタム イメージを使用して仮想マシンのプールを作成する](batch-custom-images.md)」の手順を参照してください。

[Docker Community Edition (CE)](https://www.docker.com/community-edition) または [Docker Enterprise Edition (EE)](https://www.docker.com/enterprise-edition) をご利用いただけます。

Azure NC または NV VM サイズの GPU パフォーマンスを利用するには、イメージに NVIDIA ドライバーをインストールする必要があります。 また、NVIDIA GPU の Docker エンジン ユーティリティ、[NVIDIA Docker](https://github.com/NVIDIA/nvidia-docker) をインストールし、実行する必要もあります。

Azure RDMA ネットワークにアクセスするには、サイズが A8、A9、H16r、H16mr、または NC24r の VM を使用します。 必要な RDMA ドライバーは、Azure Marketplace から CentOS 7.3 HPC および Ubuntu 16.04 LTS イメージにインストールされます。 MPI ワークロードを実行するには、追加構成が必要になる可能性があります。 「[Batch プールでの RDMA 対応または GPU 対応インスタンスの使用](batch-pool-compute-intensive-sizes.md)」を参照してください。


## <a name="limitations"></a>制限事項

* Batch では、Linux プールで実行されているコンテナーに対してのみ、RDMA サポートが提供されます。


## <a name="authenticate-using-azure-active-directory"></a>Azure Active Directory を使用して認証する

カスタム VM イメージを使用して Batch プールを作成する場合、クライアント アプリケーションでは、 Azure AD 統合認証を使用して認証を行う必要があります (共有キー認証は機能しません)。 アプリケーションを実行する前に、必ず Azure AD に登録してその ID を確立し、他のアプリケーションへのアクセス許可を指定してください。

また、カスタム VM イメージを使用する場合、その VM イメージにアクセスするには、IAM アクセスの制御をそのアプリケーションに付与する必要があります。 Azure Portal で **[すべてのリソース]** を開いて、コンテナー イメージを選択し、イメージ ブレードの **[アクセス制御 (IAM)]** で **[追加]** をクリックします。 **[アクセス許可の追加]** ブレードで **[ロール]** を指定し、**[アクセスの割り当て先]** で **[Azure AD のユーザー、グループ、またはアプリケーション]** を選択して、**[選択]** にアプリケーション名を入力します。

「[Batch サービスの認証に Active Directory を使用する](batch-aad-auth.md)」の説明に従って、アプリケーションで、[BatchClient.Open](/dotnet/api/microsoft.azure.batch.batchclient.open#Microsoft_Azure_Batch_BatchClient_Open_Microsoft_Azure_Batch_Auth_BatchTokenCredentials_) を使用して Batch クライアントを作成するときに Azure AD 認証トークンを渡します。


## <a name="reference-a-vm-image-for-pool-creation"></a>プール作成用の VM イメージを参照する

アプリケーション コードで、プールのコンピューティング ノードの作成に使用する VM イメージへの参照を指定します。 これを行うには、[ImageReference](/dotnet/api/microsoft.azure.batch.imagereference) オブジェクトを作成します。 使用するイメージを、次のいずれかの方法で指定できます。

* カスタム イメージを使用している場合は、仮想マシン イメージの Azure Resource Manager リソース識別子を指定します。 イメージ識別子のパスの形式を次の例に示します。

  ```csharp
  // Provide a reference to a custom image using an image ID
  ImageReference imageReference = new ImageReference("/subscriptions/<subscription-ID>/resourceGroups/<resource-group>/providers/Microsoft.Compute/images/<imageName>");
  ```

    Azure Portal からこのイメージ ID を取得するには、**[すべてのリソース]** を開いてカスタム イメージを選択し、イメージ ブレードの **[概要]** から **[リソース ID]** のパスをコピーします。

* [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/compute?page=1&subcategories=windows-based) イメージを使用する場合は、「[仮想マシン イメージの一覧](batch-linux-nodes.md#list-of-virtual-machine-images)」に示されているように、プランの種類、パブリッシャー、SKU、イメージのバージョンなど、イメージについて説明するパラメーターのグループを指定します。

  ```csharp
  // Provide a reference to an Azure Marketplace image for
  // "Windows Server 2016 Datacenter with Containers"
  ImageReference imageReference = new ImageReference(
    publisher: "MicrosoftWindowsServer",
    offer: "WindowsServer",
    sku: "2016-Datacenter-with-Containers",
    version: "latest");
  ```


## <a name="container-configuration-for-batch-pool"></a>Batch プール用のコンテナー構成

Batch プールは、Batch がジョブのタスクを実行するコンピューティング ノードのコレクションです。 プールを作成するときに、そのプールに、コンピューティング ノードの VM 構成を指定します。 [VirtualMachineConfiguration](/dotnet/api/microsoft.azure.batch.virtualmachineconfiguration) オブジェクトには、[ContainerConfiguration](/dotnet/api/microsoft.azure.batch.containerconfiguration) オブジェクトへの参照が含まれます。 プールでコンテナー ワークロードを有効にするには、`ContainerConfiguration` 設定を指定します。 次の例に示すように、VM 構成では、イメージ参照とイメージのノード エージェント SKU ID も指定します。

プールの作成にはオプションがいくつかあります。 プリフェッチされたコンテナー イメージを使用して、または使用しないでプールを作成できます。 

プル (プリフェッチ) プロセスにより、インターネット上の別のコンテナー レジストリまたは Docker Hub のいずれかから、コンテナー イメージを事前に読み込むことができます。 コンテナー イメージをプリフェッチするメリットは、初めてタスクを実行するとき、コンテナー イメージがダウンロードされるのを、そのタスクが待たなくてもよい点です。 プールの作成時に、コンテナー構成によって、コンテナー イメージが VM にプルされます。 これにより、プール上で実行されるタスクが、コンテナー イメージとコンテナー実行オプションの一覧を参照できます。



### <a name="pool-without-prefetched-container-images"></a>プリフェッチされたコンテナー イメージを使用しないプール

プリフェッチされたコンテナー イメージを使用せずにプールを構成するには、次の例に示すように `ContainerConfiguration` および `VirtualMachineConfiguration` オブジェクトを定義します。 以降の例では、Docker エンジンがインストールされているカスタム Ubuntu 16.04 LTS イメージを使用していることを前提としています。

```csharp
// Specify container configuration
ContainerConfiguration containerConfig = new ContainerConfiguration();

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

// Commit pool creation
pool.Commit();
```


### <a name="prefetch-images-for-container-configuration"></a>コンテナー構成用にイメージをプリフェッチする

プールでコンテナー イメージをプリフェッチするには、コンテナー イメージの一覧 (`containerImageNames`) を `ContainerConfiguration` に追加し、イメージの一覧に名前を付けます。 次の例では、カスタム Ubuntu 16.04 LTS イメージを使用し、TensorFlow イメージを [Docker Hub](https://hub.docker.com) からプリフェッチして、開始タスクで TensorFlow を開始することを前提としています。

```csharp
// Specify container configuration, prefetching Docker images
ContainerConfiguration containerConfig = new ContainerConfiguration(
    containerImageNames: new List<string> { "tensorflow/tensorflow:latest-gpu" } );

// VM configuration
VirtualMachineConfiguration virtualMachineConfiguration = new VirtualMachineConfiguration(
    imageReference: imageReference,
    containerConfiguration: containerConfig,
    nodeAgentSkuId: "batch.node.ubuntu 16.04");

// Set a native command line start task
StartTask startTaskNative = new StartTask( CommandLine: "<native-host-command-line>" );

// Define container settings
TaskContainerSettings startTaskContainerSettings = new TaskContainerSettings (
    imageName: "tensorflow/tensorflow:latest-gpu");
StartTask startTaskContainer = new StartTask(
    CommandLine: "<docker-image-command-line>",
    TaskContainerSettings: startTaskContainerSettings);

// Create pool
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    targetDedicatedComputeNodes: 4,
    virtualMachineSize: "Standard_NC6",
    virtualMachineConfiguration: virtualMachineConfiguration, startTaskContainer);

// Commit pool creation
pool.Commit();
```


### <a name="prefetch-images-from-a-private-container-registry"></a>プライベート コンテナー レジストリからイメージをプリフェッチする

プライベート コンテナー レジストリ サーバーの認証により、コンテナー イメージをプリフェッチすることもできます。 次の例では、`ContainerConfiguration` および `VirtualMachineConfiguration` オブジェクトはカスタム Ubuntu 16.04 LTS イメージを使用して、プライベート TensorFlow イメージを、プライベート Azure Container Registry からプリフェッチします。

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

// Commit pool creation
pool.Commit();
```


## <a name="container-settings-for-the-task"></a>タスク用のコンテナー設定

コンピューティング ノードで実行されるようにタスクを設定する場合は、タスクの実行オプション、使用するイメージ、レジストリなど、コンテナー固有の設定を指定する必要があります。

コンテナー固有の設定を構成するには、タスク クラスの ContainerSettings プロパティを使用します。 これらの設定は、[TaskContainerSettings](/dotnet/api/microsoft.azure.batch.taskcontainersettings) クラスによって定義されます。

コンテナー イメージでタスクを実行する場合は、[クラウド タスク](/dotnet/api/microsoft.azure.batch.cloudtask)と[ジョブ マネージャー タスク](/dotnet/api/microsoft.azure.batch.cloudjob.jobmanagertask)にコンテナー設定が必要です。 ただし、[開始タスク](/dotnet/api/microsoft.azure.batch.starttask)、[ジョブの準備タスク](/dotnet/api/microsoft.azure.batch.cloudjob.jobpreparationtask)、および[ジョブの解放タスク](/dotnet/api/microsoft.azure.batch.cloudjob.jobreleasetask)にはコンテナー設定は不要です (つまり、コンテナーのコンテキスト内で、またはノード上で直接実行できます)。

コンテナー設定を構成するとき、コンテナーには `AZ_BATCH_NODE_ROOT_DIR` (ノード上の Azure Batch ディレクトリのルート) の下にあるすべてのディレクトリが再帰的にマップされるほか、すべてのタスク環境変数がマップされます。また、タスクのコマンド ラインがコンテナー内で実行されます。

「[コンテナー構成用にイメージをプリフェッチする](#prefetch-images-for-container-configuration)」のコード例では、開始タスクのコンテナー構成を指定する方法を示しました。 次のコード例は、クラウド タスクのコンテナー構成を指定する方法を示しています。

```csharp
// Simple task command

string cmdLine = "<my-command-line>";

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

* Batch の詳細な概要については、「[Batch を使って大規模な並列コンピューティング ソリューションを開発する](batch-api-basics.md)」を参照してください。

* Linux での Docker CE のインストールおよび使用の詳細については、[Docker](https://docs.docker.com/engine/installation/) ドキュメントをご覧ください。

* カスタム イメージの使用方法の詳細については、「[マネージ カスタム イメージを使用して仮想マシンのプールを作成する](batch-custom-images.md)」を参照してください。
