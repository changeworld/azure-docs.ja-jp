<properties
 pageTitle="HPC クラスターのコンピューティング リソースの自動拡大縮小 | Microsoft Azure"
 description="Azure で HPC Pack クラスターコンピューティング ノードの数を自動的に拡大縮小する "
 services="virtual-machines-windows"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-service-management,hpc-pack"/>
<tags
ms.service="virtual-machines-windows"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-multiple"
 ms.workload="big-compute"
 ms.date="04/14/2016"
 ms.author="danlep"/>

# クラスターのワークロードに合わせて Azure の HPC Pack クラスター リソースを自動的に拡大縮小する




HPC Pack クラスターで Azure の "バースト" ノードをデプロイする場合、あるいは Azure VM で HPC Pack クラスターを作成する場合、クラスターの現在のワークロードに合わせて、コアなどの Azure コンピューティング リソースの数を自動的に増減できれば便利です。そうすれば、Azure リソースをさらに効率的に利用し、そのコストを制御できます。これを行うには、HPC Pack クラスター プロパティ **AutoGrowShrink** を設定します。または、HPC Pack でインストールされる **AzureAutoGrowShrink.ps1** HPC PowerShell スクリプトを実行します。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)].ただし、現在自動的に拡大縮小できるのは、Windows Server オペレーティング システムを実行している HPC Pack コンピューティング ノードのみです。

## AutoGrowShrink クラスター プロパティを設定する

### 前提条件

* **HPC Pack 2012 R2 Update 2 以降のクラスター** - クラスターのヘッド ノードは、オンプレミスでも Azure VM でもデプロイできます。Azure の「バースト」ノードでオンプレミス ヘッド ノードを使用するには、「[HPC Pack でハイブリッド クラスターをセットアップする](../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md)」を参照してください。Azure VM で HPC Pack クラスターをすばやくデプロイするには、[HPC Pack IaaS デプロイ スクリプト](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md)に関するページを参照してください。


* **Azure にヘッド ノードがあるクラスターの場合** - HPC Pack IaaS デプロイ スクリプトを使用してクラスターを作成する場合は、クラスター構成ファイルで AutoGrowShrink オプションを設定して、**AutoGrowShrink** クラスター プロパティを有効にします。詳細については、[スクリプトのダウンロード](https://www.microsoft.com/download/details.aspx?id=44949)に付属のドキュメントを参照してください。

    または、次のセクションに記載されている HPC PowerShell コマンドを使用して、クラスターをデプロイした後に **AutoGrowShrink** クラスター プロパティを設定します。HPC PowerShell を使用してこの設定を行うには、まず次の手順を実行します。
    1. ヘッド ノードおよび Azure サブスクリプションで Azure 管理証明書を構成します。テスト デプロイでは、HPC Pack でヘッド ノードにインストールされる既定の Microsoft HPC Azure 自己署名証明書を使用し、Azure サブスクリプションにその証明書をアップロードするだけで済みます。オプションと手順については、[TechNet ライブラリのガイダンス](https://technet.microsoft.com/library/gg481759.aspx)を参照してください。
    2. ヘッド ノードで **regedit** を実行し、HKLM\\SOFTWARE\\Micorsoft\\HPC\\IaasInfo に移動して、新しい文字列値を追加します。値の名前は "ThumbPrint" に、値のデータは手順 1 の証明書の拇印に設定します。


### AutoGrowShrink プロパティの設定用 HPC PowerShell コマンド

以下に、**AutoGrowShrink** を設定し、追加パラメーターでその動作を調整するためのサンプル HPC PowerShell コマンドを示します。設定の完全な一覧については、この記事の後半の「 [AutoGrowShrink パラメーター](#AutoGrowShrink-parameters)」を参照してください。

これらのコマンドを実行するには、管理者としてクラスターのヘッド ノードで HPC PowerShell を起動します。

**AutoGrowShrink プロパティを有効にするには**

    Set-HpcClusterProperty –EnableGrowShrink 1

**AutoGrowShrink プロパティを無効にするには**

    Set-HpcClusterProperty –EnableGrowShrink 0

**分単位で拡大間隔を変更するには**

    Set-HpcClusterProperty –GrowInterval <interval>

**分単位で縮小間隔を変更するには**

    Set-HpcClusterProperty –ShrinkInterval <interval>

**AutoGrowShrink の現在の構成を表示するには**

    Get-HpcClusterProperty –AutoGrowShrink

### AutoGrowShrink パラメーター

以下に、**Set-HpcClusterProperty** コマンドを使用して変更できる AutoGrowShrink パラメーターを示します。

* **EnableGrowShrink** - **AutoGrowShrink** プロパティの有効/無効を切り替えます。
* **ParamSweepTasksPerCore** - 1 つのコアを拡大するパラメーター スイープ タスクの数。既定では、タスクごとに 1 つのコアを拡大します。
 
    >[AZURE.NOTE] HPC Pack QFE KB3134307 では、**ParamSweepTasksPerCore** が **TasksPerResourceUnit** に変更されました。このコマンドはジョブ リソースの種類に基づいており、ノード、ソケット、またはコアに設定することができます。
    
* **GrowThreshold** - 自動拡大をトリガーする、キューに登録されたタスク数のしきい値。既定値は 1 です。つまり、キューに登録された状態のタスクが 1 つ以上ある場合、ノードを自動拡大します。
* **GrowInterval** - 自動拡大をトリガーする分単位の間隔。既定の間隔は 5 分です。
* **GrowInterval** - 自動縮小をトリガーする分単位の間隔。既定の間隔は 5 分です。
* **ShrinkIdleTimes** - ノードがアイドル状態であることを確認する継続的縮小チェックの回数。既定値は 3 回です。たとえば、 **ShrinkInterval** が 5 分である場合、HPC Pack は 5 分ごとにノードがアイドル状態であるかどうかを確認します。継続的チェックを 3 回 (15 分) してもノードがアイドル状態である場合、HPC Pack はそのノードを縮小します。
* **ExtraNodesGrowRatio** - Message Passing Interface (MPI) ジョブで拡大する追加ノードの割合。既定値は 1 です。つまり、HPC Pack は MPI ジョブでノードを 1% 拡大します。
* **GrowByMin** - 自動拡大ポリシーが、ジョブに必要な最小リソースに基づいているかどうかを示すスイッチ。既定値は false です。つまり、HPC Pack はジョブに必要な最大リソースに基づいてジョブのノードを拡大します。
* **SoaJobGrowThreshold** - 自動拡大プロセスをトリガーする受信 SOA 要求のしきい値。既定値は 50000 です。
    
    >[AZURE.NOTE] このパラメーターは、HPC Pack 2012 R2 Update 3 以降でサポートされます。
    
* **SoaRequestsPerCore** - 1 つのコアを拡大する受信 SOA 要求の数。既定値は 20000 です。

    >[AZURE.NOTE] このパラメーターは、HPC Pack 2012 R2 Update 3 以降でサポートされます。

### MPI の例

既定では、HPC Pack は MPI ジョブの追加ノードを 1% 拡大します (**ExtraNodesGrowRatio** は 1 に設定されています)。その理由は、MPI には複数のノードが必要な場合があり、すべてのノードの準備が完了した場合に限りジョブを実行できるからです。Azure によるノードの起動時に、場合によっては 1 つのノードが他のノードより起動に時間がかかるため、そのノードの準備が完了するまで他のノードはアイドル状態で待機する可能性があります。追加ノードを拡大することにより、HPC Pack は、このリソースの待機時間を縮小し、潜在的にコストを削減します。MPI ジョブの追加ノードの割合を (たとえば 10% に) 増やすには、次のようなコマンドを実行します。

    Set-HpcClusterProperty -ExtraNodesGrowRatio 10

### SOA の例

既定では、**SoaJobGrowThreshold** は 50000 に設定され、**SoaRequestsPerCore** は 200000 に設定されています。要求が 70000 件ある 1 つの SOA ジョブを送信する場合、キューに登録されるタスクは 1 つであり、受信要求数は 70000 件です。この場合、HPC Pack はキューに登録されたタスクで 1 コアを拡大し、受信要求で (70000 - 50000)/20000 = 1 コアを拡大します。そのため、この SOA ジョブでは合計 2 コアを拡大します。

## AzureAutoGrowShrink.ps1 スクリプトを実行する

### 前提条件

* **HPC Pack 2012 R2 更新プログラム 1 以降のクラスター** - **AzureAutoGrowShrink.ps1** スクリプトが %CCP\_HOME%bin フォルダーにインストールされています。クラスターのヘッド ノードはオンプレミスにするか、Azure VM に配置できます。Azure の「バースト」ノードでオンプレミス ヘッド ノードを使用するには、「[HPC Pack でハイブリッド クラスターをセットアップする](../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md)」を参照してください。Azure VM で HPC Pack クラスターをすばやくデプロイするには、[HPC Pack IaaS デプロイ スクリプト](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md)に関するページをご覧ください。または、[Azure クイックスタート テンプレート](https://azure.microsoft.com/documentation/templates/create-hpc-cluster/)を使用してください。

* **Azure PowerShell 0.8.12** - 現時点で、スクリプトはこのバージョンの Azure PowerShell を使用しています。ヘッド ノードでこれより後のバージョンを実行している場合にスクリプトを実行するには、Azure PowerShell を[バージョン 0.8.12](http://az412849.vo.msecnd.net/downloads03/azure-powershell.0.8.12.msi) にダウングレードする必要があります。

* **Azure バースト ノードを含むクラスターの場合** -HPC Pack がインストールされているクライアント コンピューターで、またはヘッド ノードではスクリプトを実行します。クライアント コンピューターで実行する場合、ヘッド ノードをポイントするように $env:CCP\_SCHEDULER 変数を適切に設定します。Azure の「バースト」ノードをクラスターに追加しておく必要がありますが、「未デプロイ」の状態になっている場合があります。


* **Azure VM にデプロイされているクラスター** - ヘッド ノード VM でスクリプトを実行します。そこでインストールされている **Start-HpcIaaSNode.ps1** スクリプトと **Stop-HpcIaaSNode.ps1** スクリプトに依存するためです。これらのスクリプトは追加で Azure 管理証明書を必要とするか、設定ファイルを発行します (「[Azure で HPC Pack クラスターのコンピューティング ノードを管理する](virtual-machines-windows-classic-hpcpack-cluster-node-manage.md)」を参照してください)。必要なコンピューティング ノード VM がすべてクラスターに追加されていることを確認します。これらの VM は "停止" 状態になっている場合があります。

### 構文

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
### パラメーター

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

* **ShrinkCheckIdleTimes** - ノードがアイドル状態であることを確認する継続的縮小チェックの数 (**ShrinkCheckIntervalMins** で分割)。

* **UseLastConfigurations** - 引数ファイルに保存された以前の構成。

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

<!---HONumber=AcomDC_0629_2016-->