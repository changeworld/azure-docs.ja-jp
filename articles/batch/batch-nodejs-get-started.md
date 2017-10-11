---
title: "チュートリアル - for Node.js の Azure Batch のクライアント ライブラリを使用して |Microsoft ドキュメント"
description: "Azure Batch の基本的な概念について学習し、Node.js を使用して、簡単なソリューションをビルドします。"
services: batch
author: shwetams
manager: timlt
ms.assetid: 
ms.service: batch
ms.devlang: nodejs
ms.topic: hero-article
ms.workload: big-compute
ms.date: 05/22/2017
ms.author: shwetams
ms.openlocfilehash: c48171d8634a651718a0775183414f463c6a468c
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2017
---
# <a name="get-started-with-batch-sdk-for-nodejs"></a>Node.js 用のバッチの SDK を概要します。

> [!div class="op_single_selector"]
> * [.NET](batch-dotnet-get-started.md)
> * [Python](batch-python-tutorial.md)
> * [Node.js](batch-nodejs-get-started.md)
>
>

Node.js を使用してバッチ クライアントの構築の基本について[Azure Batch Node.js SDK](http://azure.github.io/azure-sdk-for-node/azure-batch/latest/)です。 バッチ アプリケーションのシナリオを理解して、Node.js クライアントを使用し、設定のステップ バイ ステップ方法で行うこと。  

## <a name="prerequisites"></a>前提条件
この記事では、Node.js と使いやすさと Linux の実用的な知識があることを前提としています。 バッチと記憶域サービスを作成するアクセス権を持つにセットアップされた Azure アカウントがあることも想定しています。

読み取ることをお勧め[Azure Batch の技術概要](batch-technical-overview.md)手順に、この記事の内容が記載されているを通過する前にします。

## <a name="the-tutorial-scenario"></a>チュートリアルのシナリオ
バッチ ワークフローのシナリオを理解しましょう。 Azure Blob ストレージ コンテナーからすべての csv ファイルをダウンロードし、JSON に変換する Python で書かれた単純なスクリプトがあります。 複数のストレージ アカウント コンテナーでの処理に、並列おスクリプトを展開できます Azure バッチ ジョブとして。

## <a name="azure-batch-architecture"></a>Azure Batch のアーキテクチャ
次の図は、Azure Batch および Node.js のクライアントを使用して、Python スクリプトをスケールお方法を示しています。

![Azure Batch のシナリオ](./media/batch-nodejs-get-started/BatchScenario.png)

Node.js クライアントは、(後で詳しく説明) の準備タスクと、一連のタスクは、ストレージ アカウント内のコンテナーの数によっては、バッチ ジョブを展開します。 Github リポジトリからスクリプトをダウンロードすることができます。

* [Node.js クライアント](https://github.com/Azure/azure-batch-samples/blob/master/Node.js/GettingStarted/nodejs_batch_client_sample.js)
* [タスク シェル スクリプトの準備](https://github.com/Azure/azure-batch-samples/blob/master/Node.js/GettingStarted/startup_prereq.sh)
* [JSON のプロセッサに Python csv](https://github.com/Azure/azure-batch-samples/blob/master/Node.js/GettingStarted/processcsv.py)

> [!TIP]
> 指定されたリンクの Node.js クライアントでは、Azure 関数アプリとして展開する特定のコードは含まれません。 1 つを作成する手順については、次のリンクを参照することができます。
> - [関数アプリを作成します。](../azure-functions/functions-create-first-azure-function.md)
> - [タイマー トリガー関数を作成します。](../azure-functions/functions-bindings-timer.md)
>
>

## <a name="build-the-application"></a>アプリケーションをビルドします

ここで、お知らせの手順に従いますステップ バイ ステップ Node.js クライアントの構築に。

### <a name="step-1-install-azure-batch-sdk"></a>手順 1: Azure Batch SDK をインストールします。

Azure Batch SDK for Node.js npm インストール コマンドを使用してインストールできます。

`npm install azure-batch`

このコマンドは、azure batch ノード SDK の最新バージョンをインストールします。

>[!Tip]
> アプリケーションでは Azure 関数、npm インストール コマンドを実行する Azure 関数の設定 タブで「Kudu コンソール」に移動することができます。 Node.js 用の Azure Batch SDK をインストールする場合はこのです。
>
>

### <a name="step-2-create-an-azure-batch-account"></a>手順 2: Azure Batch アカウントを作成します。

作成することができます、 [Azure ポータル](batch-account-create-portal.md)またはコマンドラインから ([Powershell](batch-powershell-cmdlets-get-started.md) /[Azure cli](https://docs.microsoft.com/cli/azure/overview))。

Azure CLI を通じて 1 つを作成するコマンドを次に示します。

リソース グループを作成、既にある Batch アカウントを作成する場合は、この手順をスキップします。

`az group create -n "<resource-group-name>" -l "<location>"`

次に、Azure Batch アカウントを作成します。

`az batch account create -l "<location>"  -g "<resource-group-name>" -n "<batch-account-name>"`

各バッチ アカウントには、その対応するアクセス キーがあります。 Azure batch アカウントのリソースを作成するには、これらのキーが必要です。 実稼働環境に適した方法では、Azure Key Vault を使用して、これらのキーを格納します。 できますし、作成するサービス アプリケーションのプリンシパル。 このサービス プリンシパルを使用して、アプリケーションから作成できる OAuth トークン アクセス キーを key vault です。

`az batch account keys list -g "<resource-group-name>" -n "<batch-account-name>"`

コピーし、以降の手順で使用するキーを格納します。

### <a name="step-3-create-an-azure-batch-service-client"></a>手順 3: Azure Batch サービス クライアントを作成します。
次のコード スニペットは、まず azure batch の Node.js モジュールをインポートし、バッチ サービス クライアントを作成します。 まず、前の手順からコピーされたバッチ アカウント キーを持つ SharedKeyCredentials オブジェクトを作成する必要があります。

```nodejs
// Initializing Azure Batch variables

var batch = require('azure-batch');

var accountName = '<azure-batch-account-name>';

var accountKey = '<account-key-downloaded>';

var accountUrl = '<account-url>'

// Create Batch credentials object using account name and account key

var credentials = new batch.SharedKeyCredentials(accountName,accountKey);

// Create Batch service client

var batch_client = new batch.ServiceClient(credentials,accountUrl);

```

Azure Batch の URI は、Azure ポータルの [概要] タブで確認できます。 形式は。

`https://accountname.location.batch.azure.com`

スクリーン ショットを参照してください。

![Azure batch の uri](./media/batch-nodejs-get-started/azurebatchuri.png)



### <a name="step-4-create-an-azure-batch-pool"></a>手順 4: Azure Batch プールを作成します。
Azure Batch プールは、複数の Vm (バッチ ノードとも呼ばれます) で構成されます。 Azure Batch サービスは、これらのノードでは、作業を展開して、それらを管理します。 プールの次の構成パラメーターを定義できます。

* 仮想マシンのイメージの種類
* 仮想マシン ノードのサイズ
* 仮想マシン ノードの数

> [!Tip]
> 仮想マシン ノードの数とサイズは、タスクを並列で実行して、タスク自体の数によって大きく異なります。 最適な数とサイズを確認するテストをお勧めします。
>
>

次のコード スニペットは、構成にパラメーター オブジェクトを作成します。

```nodejs
// Creating Image reference configuration for Ubuntu Linux VM
var imgRef = {publisher:"Canonical",offer:"UbuntuServer",sku:"14.04.2-LTS",version:"latest"}

// Creating the VM configuration object with the SKUID
var vmconfig = {imageReference:imgRef,nodeAgentSKUId:"batch.node.ubuntu 14.04"}

// Setting the VM size to Standard F4
var vmSize = "STANDARD_F4"

//Setting number of VMs in the pool to 4
var numVMs = 4
```

> [!Tip]
> Azure Batch および SKU Id に使用できる Linux VM イメージの一覧で、次を参照してください。[仮想マシン イメージの一覧](batch-linux-nodes.md#list-of-virtual-machine-images)です。
>
>

プールの構成を定義すると、Azure Batch プールを作成できます。 Batch プールのコマンドは、Azure の仮想マシン ノードを作成し、それらを実行するタスクを受信する準備が完了するを準備します。 各プールによっては、以降の手順で参照の一意の ID が必要です。

次のコード スニペットでは、Azure Batch プールを作成します。

```nodejs
// Create a unique Azure Batch pool ID
var poolid = "pool" + customerDetails.customerid;
var poolConfig = {id:poolid, displayName:poolid,vmSize:vmSize,virtualMachineConfiguration:vmconfig,targetDedicatedComputeNodes:numVms,enableAutoScale:false };
// Creating the Pool for the specific customer
var pool = batch_client.pool.add(poolConfig,function(error,result){
    if(error!=null){console.log(error.response)};
});
```

作成したプールの状態を確認し、そのプールに、ジョブの送信に先に進む前に状態が「アクティブ」であることを確認できます。

```nodejs
var cloudPool = batch_client.pool.get(poolid,function(error,result,request,response){
        if(error == null)
        {

            if(result.state == "active")
            {
                console.log("Pool is active");
            }
        }
        else
        {
            if(error.statusCode==404)
            {
                console.log("Pool not found yet returned 404...");    

            }
            else
            {
                console.log("Error occurred while retrieving pool data");
            }
        }
        });
```

Pool.get 関数によって返されるサンプルの結果オブジェクトを次に示します。

```
{ id: 'processcsv_201721152',
  displayName: 'processcsv_201721152',
  url: 'https://<batch-account-name>.centralus.batch.azure.com/pools/processcsv_201721152',
  eTag: '<eTag>',
  lastModified: 2017-03-27T10:28:02.398Z,
  creationTime: 2017-03-27T10:28:02.398Z,
  state: 'active',
  stateTransitionTime: 2017-03-27T10:28:02.398Z,
  allocationState: 'resizing',
  allocationStateTransitionTime: 2017-03-27T10:28:02.398Z,
  vmSize: 'standard_a1',
  virtualMachineConfiguration:
   { imageReference:
      { publisher: 'Canonical',
        offer: 'UbuntuServer',
        sku: '14.04.2-LTS',
        version: 'latest' },
     nodeAgentSKUId: 'batch.node.ubuntu 14.04' },
  resizeTimeout:
   { [Number: 900000]
     _milliseconds: 900000,
     _days: 0,
     _months: 0,
     _data:
      { milliseconds: 0,
        seconds: 0,
        minutes: 15,
        hours: 0,
        days: 0,
        months: 0,
        years: 0 },
     _locale:
      Locale {
        _calendar: [Object],
        _longDateFormat: [Object],
        _invalidDate: 'Invalid date',
        ordinal: [Function: ordinal],
        _ordinalParse: /\d{1,2}(th|st|nd|rd)/,
        _relativeTime: [Object],
        _months: [Object],
        _monthsShort: [Object],
        _week: [Object],
        _weekdays: [Object],
        _weekdaysMin: [Object],
        _weekdaysShort: [Object],
        _meridiemParse: /[ap]\.?m?\.?/i,
        _abbr: 'en',
        _config: [Object],
        _ordinalParseLenient: /\d{1,2}(th|st|nd|rd)|\d{1,2}/ } },
  currentDedicated: 0,
  targetDedicated: 4,
  enableAutoScale: false,
  enableInterNodeCommunication: false,
  maxTasksPerNode: 1,
  taskSchedulingPolicy: { nodeFillType: 'Spread' } }
```


### <a name="step-4-submit-an-azure-batch-job"></a>手順 4: Azure Batch のジョブを送信します。
Azure Batch のジョブは、同様のタスクの論理グループです。 このシナリオでは"を JSON にプロセス csv です" ここで、各タスクは、各 Azure ストレージ コンテナーに存在する csv ファイルを処理している可能性があります。

これらのタスクは並列で実行があり、Azure Batch service によってコーディネートされます。 複数のノードを展開します。

> [!Tip]
> 使用することができます、 [maxTasksPerNode](http://azure.github.io/azure-sdk-for-node/azure-batch/latest/Pool.html#add)プロパティを 1 つのノードで同時に実行できるタスクの最大数を指定します。
>
>

#### <a name="preparation-task"></a>準備タスク

作成された VM ノードは、空白の Ubuntu ノードです。 多くの場合、前提条件として、一連のプログラムをインストールする必要があります。
通常は、Linux ノードの実際のタスクを実行する前に、前提条件をインストールするシェル スクリプトを持つことができます。 ただし、プログラミング可能な実行可能ファイルがある可能性があります。
[シェル スクリプト](https://github.com/shwetams/azure-batchclient-sample-nodejs/blob/master/startup_prereq.sh)この例では Python 用 Python pip と Azure Storage SDK をインストールします。

スクリプトを Azure ストレージ アカウントにアップロードし、スクリプトにアクセスする SAS URI を生成できます。 Azure ストレージの Node.js SDK を使用してこのプロセスを自動化することもできます。

> [!Tip]
> ジョブの準備タスクは、特定のタスクが実行する必要がある VM ノード上でのみ実行されます。 上で実行を使用できるタスクに関係なくすべてのノードにインストールする前提条件の場合、 [startTask](http://azure.github.io/azure-sdk-for-node/azure-batch/latest/Pool.html#add)プールを追加するときにプロパティです。 次の準備タスクの定義は、参照を使用できます。
>
>

準備タスクは、Azure Batch のジョブの送信中に指定します。 準備タスクの構成パラメーターを次に示します。

* **ID**: 準備タスクの一意の識別子
* **commandLine**: 実行可能なタスクを実行するコマンドライン
* **resourceFiles**: このタスクを実行するダウンロードするために必要なファイルの詳細を提供するオブジェクトの配列。  そのオプションを次に示します
    - blobSource: ファイルの SAS URI
    - filePath: をダウンロードしてファイルを保存するローカル パス
    - fileMode: fileMode 0770 の既定値は、8 進数形式では、Linux ノードのみに適用、
* **waitForSuccess**: 準備タスクが失敗したの true、タスクに設定が実行されない場合
* **runElevated**: タスクを実行する高度な特権が必要な場合は true に設定します。

次のコード スニペットは、準備タスクの構成のサンプル スクリプトを示しています。

```nodejs
var job_prep_task_config = {id:"installprereq",commandLine:"sudo sh startup_prereq.sh > startup.log",resourceFiles:[{'blobSource':'Blob SAS URI','filePath':'startup_prereq.sh'}],waitForSuccess:true,runElevated:true}
```

タスクが実行される、インストールする前提条件がない場合は、準備タスクを省略できます。 次のコード ファイルを作成、ジョブの表示と名"プロセス csv です"

 ```nodejs
 // Setting up Batch pool configuration
 var pool_config = {poolId:poolid}
 // Setting up Job configuration along with preparation task
 var jobId = "processcsvjob"
 var job_config = {id:jobId,displayName:"process csv files",jobPreparationTask:job_prep_task_config,poolInfo:pool_config}
 // Adding Azure batch job to the pool
 var job = batch_client.job.add(job_config,function(error,result){
     if(error != null)
     {
         console.log("Error submitting job : " + error.response);
     }});
```


### <a name="step-5-submit-azure-batch-tasks-for-a-job"></a>手順 5: ジョブの Azure Batch のタスクを送信します。

これで、プロセスの csv ジョブを作成すると、そのジョブのタスクを作成してみましょう。 コンテナーごとに 1 つ、4 つのタスクを作成する必要は 4 個のコンテナーがあると仮定した場合。

見た場合、 [Python スクリプト](https://github.com/shwetams/azure-batchclient-sample-nodejs/blob/master/processcsv.py)、2 つのパラメーターを受け取って。

* コンテナー名: からファイルをダウンロードする、ストレージ コンテナー
* パターン: ファイル名のパターンの省略可能なパラメーター

4 個のコンテナー"con1"、"con2"、"con3"があると仮定した場合、"con4"次のコードは、Azure のバッチ ジョブ"プロセスを csv に"以前に作成したタスクを送信します。

```nodejs
// storing container names in an array
var container_list = ["con1","con2","con3","con4"]
    container_list.forEach(function(val,index){           

           var container_name = val;
           var taskID = container_name + "_process";
           var task_config = {id:taskID,displayName:'process csv in ' + container_name,commandLine:'python processcsv.py --container ' + container_name,resourceFiles:[{'blobSource':'<blob SAS URI>','filePath':'processcsv.py'}]}
           var task = batch_client.task.add(poolid,task_config,function(error,result){
                if(error != null)
                {
                    console.log(error.response);     
                }
                else
                {
                    console.log("Task for container : " + container_name + "submitted successfully");
                }



           });

    });
```

コードは、プールに複数のタスクを追加します。 作成された Vm のプール内のノードの各タスクを実行します。 タスクの数は、プールまたは maxTasksPerNode プロパティ内の Vm の数を超えている場合、タスクは、利用可能なノードを待ちます。 このオーケストレーションは、Azure のバッチによって自動的に処理されます。

ポータルの詳細なタスクおよびジョブの状態ビュー。 リストを使用して Azure ノードの SDK で関数を取得できます。 詳細については、ドキュメントで説明[リンク](http://azure.github.io/azure-sdk-for-node/azure-batch/latest/Job.html)です。

## <a name="next-steps"></a>次のステップ

- 確認、 [Azure Batch の概要機能](batch-api-basics.md)アーティクルで、初めてのかどうか、サービスにお勧めします。
- 参照してください、[バッチ Node.js リファレンス](http://azure.github.io/azure-sdk-for-node/azure-batch/latest/)バッチ API を探索します。

