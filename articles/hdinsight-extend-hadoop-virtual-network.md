<properties
   pageTitle="Azure Virtual Network を使用した HDInsight の拡張 | VNet" metaKeywords="virtual network, vnet, azure, hdinsight" description="Azure Virtual Network を使用して HDInsight を他のクラウド リソースまたはデータ センター内のリソースに接続する方法について説明します。"
   services="hdinsight"
   documentationCenter=""
   authors="blackmist"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang=""
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="02/2/2015"
   ms.author="larryfr"/>


#Azure Virtual Network を使用した HDInsight 機能の拡張

Azure Virtual Network では、Hadoop ソリューションを拡張して、SQL Server などの内部設置型リソースに組み込むことや、クラウドのリソース間にセキュリティで保護されたプライベート ネットワークを作成することができます。

> [AZURE.NOTE] 現在、HDInsight ではアフィニティ ベースの Azure Virtual Network はサポートされていません。HDInsight を使用する時は、場所ベースの仮想ネットワークを使用する必要があります。

##<a id="whatis"></a>Azure Virtual Network とは

[Azure Virtual Network](/documentation/services/virtual-network/) によって、ソリューションに必要なリソースを含む、セキュリティで保護された永続的なネットワークを作成できます。仮想ネットワークでは、次のことが可能になります。

* プライベート ネットワーク (クラウドのみ) 内でのクラウド リソース間の接続

	![diagram of cloud-only configuration](.\media\hdinsight-provision-clusters\cloud-only.png)

	Virtual Network を使用して Azure Services と HDInsight を連携させことで、以下のシナリオが実現できるようになります。

	* Azure Websites から、または Azure Virtual Machines で実行しているサービスから、**HDInsight サービスまたはジョブを呼び出す**。

	* HDInsight と SQL Database、または SQL Server や仮想マシンで実行されている他のデータ ストレージ ソリューションとの間で、**データを直接転送する**。

	* **複数の HDInsight サーバーを結合**して単一のソリューションにする。HDInsight Storm サーバーを使用して受信データを処理し、処理したデータを HDInsight HBase サーバーに格納するなど。今後 MapReduce を使用して分析するために、生データを HDInsight Hadoop サーバーに格納することもできます。

* 仮想プライベート ネットワーク (VPN) を使用したローカル データセンター ネットワークへのクラウド リソースの接続 (サイト間またはポイント対サイト)

	サイト間構成では、ハードウェア VPN を使用するか、ルーティングとリモート アクセス サービスを使用して、データセンターから複数のリソースを Azure 仮想ネットワークに接続できます。

	![diagram of site-to-site configuration](.\media\hdinsight-provision-clusters\site-to-site.png)

	ポイント対サイト構成では、ソフトウェア VPN を使用して、特定のリソースを Azure 仮想ネットワークに接続できます。

	![diagram of point-to-site configuration](.\media\hdinsight-provision-clusters\point-to-site.png)

	Virtual Network を使用してクラウドとデータ センターを連携させると、クラウドのみの構成に似たシナリオを実現できるようになります。ただし、クラウドのリソースだけを使用した処理に制限されるのではなく、データ センターのリソースを使用した処理もできます。

	* HDInsight とデータ センター間で**データを直接転送する**。たとえば、Sqoop を使用して SQL Server に、または SQL Server からデータを転送したり、基幹業務 (LOB) アプリケーションで生成されたデータを読み取ったりします。

	* LOB アプリケーションから **HDInsight サービスを呼び出す**。たとえば、HBase Java API を使用して HDInsight HBase クラスターにデータを格納したり、クラスターからデータを取得したりします。

仮想ネットワークの機能、利点の詳細については、[Azure Virtual Network の概要](http://msdn.microsoft.com/library/azure/jj156007.aspx)に関するページを参照してください。

> [WACOM.NOTE] HDInsight クラスターをプロビジョニングする前に、Azure 仮想ネットワークを作成する必要があります。詳細については、「[仮想ネットワークの構成タスク](http://msdn.microsoft.com/library/azure/jj156206.aspx)」を参照してください。
>
> Azure HDInsight は場所ベースの仮想ネットワークのみをサポートし、アフィニティ グループ ベースの仮想ネットワークは現在取り扱っていません。
>
> 各クラスターには単一のサブネットを指定することを強くお勧めします。

Virtual Network での HDInsight クラスターのプロビジョニングの詳細については、[HDInsight での Hadoop クラスターのプロビジョニング](/documentation/articles/hdinsight-provision-clusters/)に関するページを参照してください。

##<a id="tasks"></a>タスクと情報

このセクションでは、Virtual Network で HDInsight を使用する場合の一般的なタスクに関する情報と、必要になる可能性がある情報を記載しています。

###完全修飾ドメイン名の確認

HDInsight クラスターには、Virtual Network インターフェイスを表す特定の FQDN が割り当てられます。これは、Virtual Network 上の他のリソースからクラスターに接続するときに使用する必要があるアドレスです。FQDN を確認するには、次の URL を使用して Ambari 管理サービスを照会します。

	https://<clustername>.azurehdinsight.net/ambari/api/v1/clusters/<clustername>.azurehdinsight.net/services/<servicename>/components/<componentname>

> [AZURE.NOTE] HDInsight での Ambari の使用の詳細については、「[Ambari API を使用した HDInsight での Hadoop クラスターの監視](/documentation/articles/hdinsight-monitor-use-ambari-api/)」を参照してください。

クラスター名およびクラスターで実行するサービスとコンポーネント (YARN リソース マネージャーなど) を指定する必要があります。

> [AZURE.NOTE] 返されるデータは、コンポーネントに関する多くの情報を含む JSON ドキュメントです。FQDN のみを抽出するには、JSON のパーサーを使用して、 `host_components[0].HostRoles.host_name` の値を取得する必要があります。

たとえば、HDInsight Hadoop クラスターから FQDN を取得するには、次のいずれかのメソッドを使用して、YARN リソース マネージャーのデータを取得します。

* [Azure PowerShell](/documentation/articles/install-configure-powershell/)

		$ClusterDnsName = <clustername>
		$Username = <cluster admin username>
		$Password = <cluster admin password>
		$DnsSuffix = ".azurehdinsight.net"
		$ClusterFQDN = $ClusterDnsName + $DnsSuffix
		
		$webclient = new-object System.Net.WebClient
		$webclient.Credentials = new-object System.Net.NetworkCredential($Username, $Password)
		
		$Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/services/yarn/		components/resourcemanager"
		$Response = $webclient.DownloadString($Url)
		$JsonObject = $Response | ConvertFrom-Json
		$FQDN = $JsonObject.host_components[0].HostRoles.host_name
		Write-host $FQDN

* [Curl](http://curl.haxx.se/) および [jq](http://stedolan.github.io/jq/)

		curl -G -u <username>:<password> https://<clustername>.azurehdinsight.net/ambari/api/v1/clusters/<clustername>.azurehdinsight.net/services/yarn/components/resourcemanager | jq .host_components[0].HostRoles.host_name

###HBase への接続

Java API を使用して HBase にリモート接続するには、HBase クラスターの Zookeeper クォーラム アドレスを確認し、これをアプリケーションで指定する必要があります。

zookeeper クォーラム アドレスを取得するには、次のいずれかのメソッドを使用して、Ambari 管理サービスに問い合わせます。

* [Azure PowerShell](/documentation/articles/install-configure-powershell/)

		$ClusterDnsName = <clustername>
		$Username = <cluster admin username>
		$Password = <cluster admin password>
		$DnsSuffix = ".azurehdinsight.net"
		$ClusterFQDN = $ClusterDnsName + $DnsSuffix

		$webclient = new-object System.Net.WebClient
		$webclient.Credentials = new-object System.Net.NetworkCredential($Username, $Password)

		$Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.zookeeper.quorum"
        $Response = $webclient.DownloadString($Url)
        $JsonObject = $Response | ConvertFrom-Json
        Write-host $JsonObject.items[0].properties.'hbase.zookeeper.quorum'

* [Curl](http://curl.haxx.se/) および [jq](http://stedolan.github.io/jq/)

		curl -G -u <username>:<password> "https://<clustername>.azurehdinsight.net/ambari/api/v1/clusters/<clustername>.azurehdinsight.net/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.zookeeper.quorum" | jq .items[0].properties[]

> [AZURE.NOTE] HDInsight での Ambari の使用の詳細については、「[Ambari API を使用した HDInsight での Hadoop クラスターの監視](/documentation/articles/hdinsight-monitor-use-ambari-api/)」を参照してください。

クォーラムの情報を取得したら、それをクライアント アプリケーションで使用します。

たとえば、HBase API を使用する Java アプリケーションでは、**hbase-site.xml** ファイルをプロジェクトに追加し、次のようにそのファイル内にクォーラム情報を指定します。

```
<configuration>
  <property>
    <name>hbase.cluster.distributed</name>
    <value>true</value>
  </property>
  <property>
    <name>hbase.zookeeper.quorum</name>
    <value>zookeeper0.address,zookeeper1.address,zookeeper2.address</value>
  </property>
  <property>
    <name>hbase.zookeeper.property.clientPort</name>
    <value>2181</value>
  </property>
</configuration>
```

###ネットワーク接続の確認

SQL Server などの一部のサービスでは、着信ネットワーク接続数が制限される場合があります。そのようなサービスでは HDInsight が正常に機能しません。

HDInsight からサービスへのアクセスで問題が発生した場合は、サービスのドキュメントを参照して、ネットワーク アクセスが有効になっていることを確認してください。ネットワーク アクセスを確認するもう 1 つの方法では、同じ Virtual Network 上に Azure Virtual Machine を作成し、クライアント ユーティリティを使用して、VM から Virtual Network 経由でサービスに接続できることを確認します。

##<a id="nextsteps"></a>次のステップ

次の例は、Azure Virtual Network で HDInsight を使用する方法を示しています。

* [HDInsight での、Storm および HBase を使用したセンサー データの分析](/documentation/articles/hdinsight-storm-sensor-data-analysis/) - Virtual Network での Storm および HBase の構成方法と、Storm から HBase にリモートでデータを書き込む方法を示しています。

* [Azure Virtual Network での HBase クラスターのプロビジョニング](/documentation/articles/hdinsight-hbase-provision-vnet/)  - Azure Virtual Network での HBase クラスターのプロビジョニングに関する情報を提供します。

* [HDInsight での Hadoop クラスターのプロビジョニング](/documentation/articles/hdinsight-provision-clusters/) - Azure Virtual Network の使用に関する情報を含めて、Hadoop クラスターのプロビジョニングに関する情報を提供します。

* [HDInsight での Hadoop と Sqoop の使用](/documentation/articles/hdinsight-use-sqoop/)  - SQL Server と Sqoop を使用した Virtual Network 経由のデータ転送に関する情報を提供します。

Azure Virtual Network の詳細については、[Azure Virtual Network の概要](http://msdn.microsoft.com/library/azure/jj156007.aspx)に関するページを参照してください。

<!--HONumber=47-->
