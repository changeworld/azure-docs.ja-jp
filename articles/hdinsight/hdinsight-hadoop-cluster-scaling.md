<properties
   pageTitle="HDInsight の Hadoop、HBase、または Apache Storm クラスターのスケール設定 | Microsoft Azure"
   description="HDInsight で実行されている Hadoop、Apache Storm、または HBase クラスター内のデータ ノード数は、クラスターを削除して再作成することなく変更できます。"
   services="hdinsight"
   documentationCenter=""
   authors="mumian"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="06/30/2015"
   ms.author="jgao"/>

#Hadoop、HBase、または Apache Storm クラスターのスケール設定を使用して HDInsight のデータ ノード数を変更する 

クラスターのスケール設定機能を使用すると、Azure HDInsight で実行しているクラスターによって使用されるデータ ノードの数を、クラスターを削除して再作成することなく、変更できます。この処理は、Azure PowerShell、HDInsight SDK、Azure ポータルから実行できます。

## 機能の詳細
このセクションでは、HDInsight でサポートされているクラスターの種類ごとに、データ ノード数を変更した場合の影響について説明します。

* Hadoop は、
* Apache Storm
* HBase 

## Hadoop は、 

### データ ノードの追加
保留中または実行中のジョブに影響を与えることなく、実行中の Hadoop クラスター内のデータ ノードの数をシームレスに増加できます。処理の進行中に新しいジョブを送信することもできます。スケール設定処理の失敗は正常に処理され、クラスターは常に機能状態になります。

### データ ノードの削除
データ ノードの数を減らして Hadoop クラスターのスケールを小さくした場合、クラスター内の一部のサービスが再起動されます。これにより、スケール設定処理の完了時に、実行中および保留中のすべてのジョブが失敗します。ただし、処理が完了した後にジョブを再送信できます。

## Storm
実行中の Storm クラスターに対して、データ ノードの追加または削除をシームレスに実行できます。ただし、スケール設定処理が正常に完了した後、トポロジのバランス再調整が必要になります。

バランス再調整は、次の 2 つの方法で実行できます。

* Storm Web UI
* コマンド ライン インターフェイス (CLI) ツール 

詳細については、[Apache Storm に関するドキュメント](http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html)をご覧ください。

Storm Web UI は、HDInsight クラスターで使用できます。

![Image1](./media/hdinsight-hadoop-cluster-scaling/StormUI.png)

CLI コマンドを使用して Storm トポロジのバランスを再調整する方法を次の例で示します。

	## Reconfigure the topology "mytopology" to use 5 worker processes,
	## the spout "blue-spout" to use 3 executors, and
	## the bolt "yellow-bolt" to use 10 executors

	$ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10

##HBase
実行中の HBase クラスターに対して、ノードの追加または削除をシームレスに実行できます。地域サーバーは、スケール設定処理の完了の数分以内に自動的に分散されます。ただし、クラスターのヘッドノードにログインし、コマンド プロンプト ウィンドウから次のコマンドを実行して、地域サーバーを手動で分散することもできます。

	>pushd %HBASE_HOME%\bin
	>hbase shell
	>balancer

## 前提条件

* HDInsight バージョン 3.1.3 以降を使用しているクラスターのみがサポートされます。クラスターのバージョンが不明な場合は、Azure ポータルで HDInsight クラスター名をクリックするか、Azure PowerShell の `Get-AzureHDInsightCluster –name <clustername>` コマンドを実行すると、クラスターのバージョンを確認できます。

* Azure PowerShell から処理を実行するには、Azure PowerShell バージョン 0.8.14 以降が必要です。[Azure のダウンロード ページ](http://azure.microsoft.com/downloads/)にある**コマンド ライン ツール**のセクションから、Azure PowerShell の最新バージョンをダウンロードできます。インストールした Azure PowerShell のバージョンは、Azure PowerShell ウィンドウで `(get-module Azure).Version` コマンドを使って確認できます。

## クラスターのスケール設定の使用方法

### Azure ポータル
実行中のクラスターのサイズは、Azure HDInsight クラスター ダッシュボードの **[スケール]** タブで変更できます。

![](http://i.imgur.com/u5Mewwx.png)

### Azure PowerShell
Azure PowerShell を使用して Hadoop クラスターのサイズを変更するには、クライアント コンピューターから次のコマンドを実行します。

	Set-AzureHDInsightClusterSize -ClusterSizeInNodes <NewSize> -name <clustername>	

> [AZURE.NOTE]このコマンドを使用するには、クライアント コンピューターに Azure PowerShell Version 0.8.14 以降がインストールされている必要があります。

### SDK
HDInsight SDK を使用して Hadoop クラスターのサイズを変更するには、次のメソッドのいずれかを使用します。

	ChangeClusterSize(string dnsName, string location, int newSize) 

または

	ChangeClusterSizeAsync(string dnsName, string location, int newSize) 


このメソッドの同期バージョンと非同期バージョンの両方で、[ClusterDetails](http://msdn.microsoft.com/library/microsoft.windowsazure.management.hdinsight.clusterdetails_properties.aspx) オブジェクトが返されます。

このメソッドの同期バージョンの使用方法を示すサンプル コードは次のとおりです。

	using System;
	using System.Collections.Generic;
	using System.Linq;
	using System.Text;
	using System.Threading.Tasks;
	using System.Security.Cryptography.X509Certificates;
	using Microsoft.WindowsAzure.Management.HDInsight;
	using Microsoft.WindowsAzure.Management.HDInsight.ClusterProvisioning;

	namespace HDInsightClusterScaling
	{
	    class Program
	    {
	        static void Main(string[] args)
	        {
	            // Friendly name for the certificate your created earlier  
	            string certfriendlyname = "<CertificateFriendlyName>";     
	            string subscriptionid = "<SubscriptionID>";
	            string clustername = "<ClusterDNSName>";
	     		string location = "<ClusterLocation>”";
				int newSize = <NewClusterSize>;
	
	            // Get the certificate object from certificate store by using the friendly name to identify it
	            X509Store store = new X509Store();
	            store.Open(OpenFlags.ReadOnly);
	            X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>().First(item => item.FriendlyName == certfriendlyname);
	
	            // Create an HDInsightClient object
	            HDInsightCertificateCredential creds = new HDInsightCertificateCredential(new Guid(subscriptionid), cert);
	            var client = HDInsightClient.Connect(creds);
	
	            Console.WriteLine("Rescaling HDInsight cluster ...");
	
	            // Change the cluster size
	     		ClusterDetails cluster = client.ChangeClusterSize(clustername, location, newSize);
	            
	            Console.WriteLine("Cluster Rescaled: {0} \n New Cluster Size = {1}", cluster.ConnectionUrl, cluster.ClusterSizeInNodes);
	            Console.WriteLine("Press ENTER to continue.");
	            Console.ReadKey();
	        }
	    }
	}


HDInsight .NET SDK の使用の詳細については、「[カスタム オプションを使用した HDInsight での Hadoop クラスターのプロビジョニング](hdinsight-provision-clusters.md)」のトピックをご覧ください。
 

<!---HONumber=July15_HO2-->