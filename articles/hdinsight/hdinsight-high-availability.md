<properties 
	pageTitle="HDInsight での Hadoop クラスターの可用性 | Microsoft Azure" 
	description="HDInsight は追加のヘッド ノードを使用して可用性と信頼性の高いクラスターをデプロイします。" 
	services="hdinsight" 
	editor="cgronlun" 
	manager="paulettm" 
	authors="mumian" 
	documentationCenter=""/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="07/10/2015" 
	ms.author="jgao"/>


#HDInsight における Hadoop クラスターの可用性と信頼性


HDInsight を使用すると、さまざまなクラスターの種類を、異なるデータ分析ワークロードにデプロイできるようになります。現在提供されているクラスターの種類は、クエリとワークロードの分析のための Hadoop クラスター、NoSQL ワークロードのための HBase クラスター、リアルタイムのイベントの処理ワークロードのための Storm クラスターです。特定のクラスターの種類には、さまざまなノードに合わせたロールがあります。次に例を示します。



- HDInsight の Hadoop クラスターは 2 つのロールでデプロイされます。
	- ヘッド ノード (2 ノード)
	- データ ノード (1 つ以上のノード)

- HDInsight の HBase クラスターは 3 つのロールでデプロイされます。
	- ヘッド サーバー (2 つのノード)
	- リージョン サーバー (1 つ以上のノード)
	- マスター/Zookeeper ノード (3 つのノード)

- HDInsight の Storm クラスターは 3 つのロールでデプロイされます。
	- Nimbus ノード (2 つのノード)
	- Supervisor サーバー (1 つ以上のノード)
	- Zookeeper ノード (3 つのノード)
 
通常 Hadoop クラスターの標準的な実装には、ヘッド ノードは 1 つしかありません。HDInsight は、セカンダリのヘッド ノード/ヘッド サーバー/Nimbus ノードを追加してこの単一障害点を削除して、ワークロードの管理に必要なサービスの可用性と信頼性を高めます。これらのヘッド ノード/ヘッド サーバー/Nimbus ノードは、ワーカー ノードの障害を円滑に管理するよう設計されていますが、ヘッド ノードで実行中のマスター サービスが停止すると、クラスターが動作を停止する原因になることがあります。


[ZooKeeper](http://zookeeper.apache.org/) ノード (ZK) を追加して、ヘッド ノードのリーダー選定に使用すると、ワーカー ノードとゲートウェイ (GW) は、アクティブ ヘッド ノード (ヘッド ノード 0) が非アクティブになったときに 2 つ目のヘッド ノード (ヘッド ノード 1) にフェールオーバーするタイミングを認識できます。

![HDInsight の Hadoop 実装における信頼性の高いヘッド ノード図。](http://i.imgur.com/jrUmrH4.png)




## アクティブなヘッド ノードのサービスの状態を確認する
アクティブ ヘッド ノードを特定し、そのヘッド ノードで実行中のサービスの状態をチェックするには、リモート デスクトップ プロトコル (RDP) を使用して Hadoop クラスターに接続する必要があります。RDP の手順については、[Azure ポータルを使用した HDInsight での Hadoop クラスターの管理](hdinsight-administer-use-management-portal.md/#connect-to-hdinsight-clusters-by-using-rdp)に関するページを参照してください。クラスターにリモート接続したら、デスクトップにある **[Hadoop サービスの可用性の状態]** アイコンをダブルクリックして、Namenode、Jobtracker、Templeton、Oozieservice、Metastore、Hiveserver2 の各サービスが実行されているヘッド ノードについて、状態を取得します。HDI 3.0 の場合、対象のサービスは、Namenode、Resource Manager、History Server、Templeton、Oozieservice、Metastore、Hiveserver2 になります。

![](./media/hdinsight-high-availability/Hadoop.Service.Availability.Status.png)

このスクリーン ショットでは、アクティブなヘッド ノードは *headnode0* です。

## セカンダリ ヘッド ノードのログ ファイルにアクセスする

セカンダリ ヘッド ノードがアクティブ ヘッド ノードになった場合、セカンダリ ヘッド ノードのログ ファイルにアクセスできるよう、引き続きプライマリ アクティブ ノードと同様に JobTracker UI を参照できます。Job Tracker にアクセスするには、前のセクションで説明したように RDP を使用して Hadoop クラスターに接続する必要があります。クラスターにリモート接続したら、デスクトップにある **[Hadoop 名前ノードの状態]** アイコンをダブルクリックし、**[NameNode ログ]** をクリックしてセカンダリ ヘッド ノード上のログのディレクトリを参照します。

![](./media/hdinsight-high-availability/Hadoop.Head.Node.Log.Files.png)


## ヘッド ノードのサイズを構成する
既定では、ヘッド ノードは L サイズの仮想マシン (VM) として割り当てられます。このサイズは、クラスター上で実行されるほとんどの Hadoop ジョブを管理するには十分です。ただし、XL サイズの VM がヘッド ノードに必要となるシナリオもあります。その例の 1 つに、クラスターで小さな Oozie ジョブを多数管理しなければならない場合があります。

XL サイズの VM は、Azure PowerShell コマンドレットまたは HDInsight SDK を使用して構成できます。

Azure PowerShell を使用したクラスターの作成とプロビジョニングについては、「[PowerShell を使用した HDInsight の管理](hdinsight-administer-use-powershell.md)」で説明しています。XL サイズのヘッド ノードの構成には、このコードで使用される `New-AzureHDInsightcluster` コマンドレットに `-HeadNodeVMSize ExtraLarge` パラメーターを追加する必要があります。

    # Create a new HDInsight cluster in Azure PowerShell
	# Configured with an ExtraLarge head-node VM
    New-AzureHDInsightCluster -Name $clusterName -Location $location -HeadNodeVMSize ExtraLarge -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainerName $containerName  -ClusterSizeInNodes $clusterNodes

SDK でも、同じような方法を使用します。SDK を使用したクラスターの作成とプロビジョニングについては、「[HDInsight .NET SDK の使用](hdinsight-provision-clusters.md#sdk)」で説明しています。XL サイズのヘッド ノードの構成には、このコードで使用される `ClusterCreateParameters()` メソッドに `HeadNodeSize = NodeVMSize.ExtraLarge` パラメーターを追加する必要があります。

    # Create a new HDInsight cluster with the HDInsight SDK
	# Configured with an ExtraLarge head-node VM
    ClusterCreateParameters clusterInfo = new ClusterCreateParameters()
    {
    Name = clustername,
    Location = location,
    HeadNodeSize = NodeVMSize.ExtraLarge,
    DefaultStorageAccountName = storageaccountname,
    DefaultStorageAccountKey = storageaccountkey,
    DefaultStorageContainer = containername,
    UserName = username,
    Password = password,
    ClusterSizeInNodes = clustersize
    };


**参照**

- [ZooKeeper](http://zookeeper.apache.org/)
- [RDP を使用した HDInsight クラスターへの接続](hdinsight-administer-use-management-portal.md#rdp)
- [HDInsight .NET SDK の使用](hdinsight-provision-clusters.md#sdk) 







 

<!---HONumber=August15_HO6-->