<properties
 pageTitle="HPC クラスターのコンピューティング リソースの自動拡大縮小 | Microsoft Azure"
 description="Azure で HPC Pack クラスターのコンピューティング リソースを自動的に拡大縮小する方法について説明します。"
 services="virtual-machines"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-service-management,hpc-pack"/>
<tags
ms.service="virtual-machines"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-multiple"
 ms.workload="big-compute"
 ms.date="09/28/2015"
 ms.author="danlep"/>

# クラスター ワークロードに合わせ、HPC Pack クラスターで Azure コンピューティング リソースを自動的に拡大縮小します。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]リソース マネージャー モデル。


HPC Pack クラスターで Azure の「バースト」ノードをデプロイする場合、あるいは Azure VM で HPC Pack クラスターを作成する場合、クラスターのジョブやタスクの現在のワークロードに合わせ、Azure コンピューティング リソースを自動的に拡大縮小できれば便利です。そうすれば、Azure リソースをさらに効率的に利用し、そのコストを制御できます。これを行うには、HPC Pack でインストールされる **AzureAutoGrowShrink.ps1** HPC PowerShell スクリプトを使用します。

>[AZURE.TIP]HPC Pack 2012 R2 更新プログラム 2 より、HPC Pack には Azure バースト ノードまたは Azure VM コンピューティング ノードを自動的に拡大縮小するためのサービスが内蔵されています。[HPC Pack IaaS デプロイ スクリプト](virtual-machines-hpcpack-cluster-powershell-script.md)の設定でサービスを構成するか、**AutoGrowShrink** クラスター プロパティを手動で設定します。「[Microsoft HPC Pack 2012 R2 更新プログラム 2 の新機能](https://technet.microsoft.com/library/mt269417.aspx)」を参照してください。

## 前提条件

* **HPC Pack 2012 R2 更新プログラム 1 以降のクラスター** - **AzureAutoGrowShrink.ps1** スクリプトが %CCP\_HOME%bin フォルダーにインストールされています。クラスターのヘッド ノードはオンプレミスにするか、Azure VM に配置できます。Azure の「バースト」ノードでオンプレミス ヘッド ノードを使用するには、「[HPC Pack でハイブリッド クラスターをセットアップする](../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md)」を参照してください。Azure VM で HPC Pack クラスターをすばやくデプロイするには、「[HPC Pack IaaS デプロイ スクリプト](virtual-machines-hpcpack-cluster-powershell-script.md)」を参照してください。

* **Azure バースト ノードを含むクラスターの場合** -HPC Pack がインストールされているクライアント コンピューターで、またはヘッド ノードではスクリプトを実行します。クライアント コンピューターで実行する場合、ヘッド ノードをポイントするように $env:CCP\_SCHEDULER 変数を適切に設定します。Azure の「バースト」ノードをクラスターに追加しておく必要がありますが、「未デプロイ」の状態になっている場合があります。


* **Azure VM にデプロイされているクラスター** - ヘッド ノード VM でスクリプトを実行します。そこでインストールされている **Start-HpcIaaSNode.ps1** スクリプトと **Stop-HpcIaaSNode.ps1** スクリプトに依存するためです。これらのスクリプトは追加で Azure 管理証明書を必要とするか、設定ファイルを発行します (「[Azure で HPC Pack クラスターのコンピューティング ノードを管理する](virtual-machines-hpcpack-cluster-node-manage.md)」を参照してください)。必要なコンピューティング ノード VM がすべてクラスターに追加されていることを確認します。ただし、「停止」状態になっている場合があります。

## 構文

```
AzureAutoGrowShrink.ps1
[[-NodeTemplates] <String[]>] [[-JobTemplates] <String[]>] [[-NodeType] <String>]
[[-NumOfQueuedJobsPerNodeToGrow] <Int32>]
[[-NumOfQueuedJobsToGrowThreshold] <Int32>]
[[-NumOfActiveQueuedTasksPerNodeToGrow] <Int32>]
[[-NumOfActiveQueuedTasksToGrowThreshold] <Int32>]
[[-NumOfInitialNodesToGrow] <Int32>] [[-GrowCheckIntervalMins] <Int32>]
[[-ShrinkCheckIntervalMins] <Int32>] [[-ShrinkCheckIdleTimes] <Int32>]
[-UseLastConfigurations] [[-ArgFile] <String>] [[-LogFilePrefix] <String>]
[<CommonParameters>]

```
## パラメーター

 * **NodeTemplates** - ノードを拡大縮小するための範囲を定義するノード テンプレートの名前。指定されていない場合 (既定値は @() です)、**NodeType** の値が AzureNodes のとき、**AzureNodes** ノード グループのすべてのノードが範囲内となり、**NodeType** の値が ComputeNodes のとき、**ComputeNodes** ノード グループのすべてのノードが範囲内になります。

 * **JobTemplates** - ノードを拡大するための範囲を定義するジョブ テンプレートの名前。

 * **NodeType** - 拡大縮小するノードの型。サポートされる値は次のとおりです。

     * **AzureNodes** – オンプレミスまたは Azure IaaS クラスターの Azure PaaS (バースト) ノード用。

     * **ComputeNodes** - Azure IaaS クラスターにのみ存在するコンピューティング ノード VM 用。

* **NumOfQueuedJobsPerNodeToGrow** - 1 つのノードを拡大するために必要で、キューに登録されたジョブの数。

* **NumOfQueuedJobsToGrowThreshold** - 拡大プロセスを始めるためにキューに登録されたジョブのしきい値数。

* **NumOfActiveQueuedTasksPerNodeToGrow** - 1 つのノードを拡大するために必要で、キューに登録されたアクティブなタスクの数。**NumOfQueuedJobsPerNodeToGrow** に 0 より大きい値が指定されている場合、このパラメーターは無視されます。

* **NumOfActiveQueuedTasksToGrowThreshold** - 拡大プロセスを始めるためにキューに登録されたアクティブなタスクのしきい値数。

* **NumOfInitialNodesToGrow** - 範囲内の全ノードが「**未デプロイ**」または「**停止 (割り当て解除)**」の場合、拡大するノードの初回最小数。

* **GrowCheckIntervalMins** - 拡大チェック間の間隔 (分単位)。

* **ShrinkCheckIntervalMins** - 縮小チェック間の間隔 (分単位)。

* **ShrinkCheckIdleTimes**- ノードがアイドル状態であることを示す継続的縮小チェックの数 (**ShrinkCheckIntervalMins** で分割)。

* **UseLastConfigurations*** 引数ファイルに保存された以前の構成。

* **ArgFile** - スクリプトを実行する構成を保存し、更新するための引数ファイルの名前。

* **LogFilePrefix** - ログ ファイルのプレフィックス名。パスを指定できます。既定では、ログは現在の作業ディレクトリに書き込まれます。

### 例 1

次の例では、Azure バースト ノードがデプロイされ、規定の AzureNode テンプレートで自動的に拡大縮小します。最初にすべてのノードが「**未デプロイ**」状態の場合、少なくとも 3 つのノードが開始されます。キューに登録されたジョブの数が 8 を超える場合、その数がキューに登録されたジョブと **NumOfQueuedJobsPerNodeToGrow** の比率を超えるまでスクリプトはノードを起動します。ノードは 3 回連続してアイドル状態になると停止します。

```
.\AzureAutoGrowShrink.ps1 -NodeTemplates @('Default AzureNode
 Template') -NodeType AzureNodes -NumOfQueuedJobsPerNodeToGrow 5
 -NumOfQueuedJobsToGrowThreshold 8 -NumOfInitialNodesToGrow 3
 -GrowCheckIntervalMins 1 -ShrinkCheckIntervalMins 1 -ShrinkCheckIdleTimes 3
```

### 例 2

次の例では、Azure コンピューティング ノード VM がデプロイされ、規定の ComputeNode テンプレートで自動的に拡大縮小します。規定のジョブ テンプレートにより構成されたジョブによりクラスターのワークロード範囲が定義されます。最初にすべてのノードが「停止」状態の場合、少なくとも 5 つのノードが開始されます。キューに登録されたアクティブ タスクの数が 15 を超える場合、その数がキューに登録されたアクティブ タスクと **NumOfActiveQueuedTasksPerNodeToGrow** の比率を超えるまでスクリプトはノードを起動します。ノードは 10 回連続してアイドル状態になると停止します。

```
.\AzureAutoGrowShrink.ps1 -NodeTemplates 'Default ComputeNode Template' -JobTemplates 'Default' -NodeType ComputeNodes -NumOfActiveQueuedTasksPerNodeToGrow 10 -NumOfActiveQueuedTasksToGrowThreshold 15 -NumOfInitialNodesToGrow 5 -GrowCheckIntervalMins 1 -ShrinkCheckIntervalMins 1 -ShrinkCheckIdleTimes 10 -ArgFile 'IaaSVMComputeNodes_Arg.xml' -LogFilePrefix 'IaaSVMComputeNodes_log'
```

<!---HONumber=Nov15_HO3-->