<properties 
	pageTitle="Azure Virtual Network での HBase クラスターのプロビジョニング | Azure" 
	description="Azure Virtual Network での HDInsight クラスターの作成方法について説明します。" 
	services="hdinsight" 
	documentationCenter="" 
	authors="mumian" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/19/2014" 
	ms.author="jgao"/>

# Azure Virtual Network での HBase クラスターのプロビジョニング

[Azure Virtual Network][1] での HDInsight HBase クラスターの作成方法について説明します。 

アプリケーションが HBase と直接通信できるように、仮想ネットワーク統合を使用して、HBase クラスターをアプリケーションと同じ仮想ネットワークにデプロイできます。次の利点があります。

- Web アプリケーションと HBase クラスターのノード間の直接接続により、HBase Java RPC API を使用した通信が可能になります。
- トラフィックが複数のゲートウェイやロード バランサーを経由しないためパフォーマンスが向上します。 
- 機密情報は、パブリック エンドポイントに公開されることなく、より安全な方法で処理されます。


##この記事の内容

- [前提条件](#prerequisites)
- [HBase クラスターを仮想ネットワークへプロビジョニングする](#hbaseprovision)
- [HBase Java RPC API を使用して、仮想ネットワーム内でプロビジョニングされた HBase クラスターへ接続する](#connect)
- [Azure PowerShell を使用して HBase クラスターをプロビジョニングする](#powershell)
- [次のステップ](#nextsteps)

##<a id="prerequisites"></a>前提条件
このチュートリアルを読み始める前に、次の項目を用意する必要があります。

- **Azure サブスクリプション**。Azure はサブスクリプション方式のプラットフォームです。サブスクリプションの入手方法の詳細については、[購入オプション][azure-purchase-options]、[メンバー プラン][azure-member-offers]、または[無料評価版][azure-free-trial]に関するページを参照してください。

- **Azure PowerShell がインストールされ構成されたワークステーション**。手順については、[Azure PowerShell のインストールおよび構成][powershell-install]に関するページを参照してください。PowerShell スクリプトを実行するには、Azure PowerShell を管理者として実行し、実行ポリシーを  *RemoteSigned* に設定する必要があります。「[Set-ExecutionPolicy コマンドレットの使用][2]」を参照してください。

	PowerShell スクリプトを実行する前に、次のコマンドレットを使用して Azure サブスクリプションに接続されていることを確認します。

		Add-AzureAccount

	Azure サブスクリプションが複数ある場合は、次のコマンドレットを使用して、現在のサブスクリプションを設定します。

		Select-AzureSubscription <AzureSubscriptionName>


##<a id="hbaseprovision"></a>HBase クラスターを仮想ネットワークへプロビジョニングする 

**管理ポータルを使用して仮想ネットワークを作成するには、次の手順を実行します。**

1. [Azure の管理ポータル][azure-portal]にサインインします。
2. 左下隅にある **[新規]** をクリックし、**[ネットワーク サービス]**、**[仮想ネットワーク]**、**[簡易作成]** の順にクリックします。
3. 次の値を入力または選択します。

	- **名前**: 仮想ネットワークの名前
	- **アドレス空間**: クラスター内のすべてのノードにアドレスを提供するために十分な大きさの、仮想ネットワークのアドレス空間を選択します。そうでないと、プロビジョニングは失敗します。このチュートリアルを読み進めるには、3 つの中から任意のアドレス空間を選択できます。 
	- **最大 VM 数**: 最大 VM 数のいずれかを選択します。この値は、アドレス空間内に作成できる潜在的なホスト (VM) の数を決定します。このチュートリアルを読み進めるには、**4096 [CIDR:/20]** で十分です。 
	- **場所**: 場所は作成する HBase クラスターと同じである必要があります。
	- **DNS サーバー**: この記事では、Azure が提供する内部 DNS サーバーを使用するため、**[なし]** を選択します。カスタム DNS サーバーを使用した、より高度なネットワーク構成もサポートされています。詳細なガイダンスについては、「[名前解決 (DNS)](http://msdn.microsoft.com/library/azure/jj156088.aspx)」を参照してください。
4. **[仮想ネットワークの作成]** をクリックします。新しい仮想ネットワーク名が一覧に表示されます。[ステータス] 列に **[作成済み]** が表示されるまで待機します。
5. メイン ウィンドウで、作成した仮想ネットワークをクリックします。
6. ページの上部にある **[ダッシュボード]** をクリックします。
7. **[概要]** の下の **[仮想ネットワーク ID]** を書き留めます。この ID は、HBase クラスターのプロビジョニング時に必要です。
8. ページの上部にある **[構成]** をクリックします。
9. ページの下部に示される既定のサブネット名は、**Subnet-1** です。必要に応じて、HBase クラスターのサブネット名を変更したり、新しいサブネットを追加したりできます。サブネット名を書き留めます。これは、クラスターのプロビジョニング時に必要となります。
10. クラスター用に使用するサブネットの **[CIDR (アドレス数)]** を確認します。アドレス数は、ワーカー ノード数に 7 (ゲートウェイ:2、ヘッドノード:2、Zookeeper:3)。たとえば、10 ノードの HBase クラスターが必要な場合、サブネットのアドレス数は、17 (10+7) を超えている必要があります。17 以下の場合、デプロイメントは失敗します。

	> [AZURE.NOTE] 1 つのクラスターには単一のサブネットを指定することを強くお勧めします。 

11. サブネット値を更新した場合は、ページ下部の **[保存]** をクリックします。



> [AZURE.NOTE] HDInsight クラスターは、データ保存するために Azure BLOB ストレージを使用します。詳細については、[HDInsight での Hadoop と Azure BLOB ストレージの使用][hdinsight-storage]に関するページを参照してください。ストレージ アカウントと BLOB ストレージ コンテナーが必要です。ストレージ アカウントの場所は、仮想ネットワークの場所およびクラスターの場所と一致している必要があります。

**Azure Storage アカウントと BLOB ストレージ コンテナーを作成するには、次の手順を実行します。**

1. [Azure の管理ポータル][azure-portal]にサインインします。
2. 左下隅にある **[新規]** をクリックし、**[データ サービス]**、**[ストレージ]**、**[簡易作成]** の順にクリックします。

3. 次の値を入力または選択します。

	- **URL**:ストレージ アカウントの名前
	- **場所**:ストレージ アカウントの場所。仮想ネットワークの場所と一致していることを確認します。アフィニティ グループはサポートされていません。
	- **レプリケーション**:テストの目的には、**[ローカル冗長]** を使用してコストを削減します。

4. **[ストレージ アカウントの作成]** をクリックします。新しいストレージ アカウントがストレージ一覧に表示されます。 
5. 新しいストレージ アカウントの **[状態]** が **[オンライン]** になるまで待ちます。
6. 一覧の新しいストレージ アカウントをクリックして選択します。
7. ページの下部にある **[アクセス キーの管理]** をクリックします。
8. **[ストレージ アカウント名]** と **[プライマリ アクセス キー]** (または **[セカンダリ アクセス キー]**) の値をメモします。どちらのキーでもかまいません。この情報は後で必要になります。
9. ページの上部にある **[コンテナー]** をクリックします。
10. ページの下部にある **[追加]** をクリックします。
11. コンテナーの名前を入力します。このコンテナーは、HBase クラスターの既定のコンテナーとして使用されます。既定では、既定のコンテナー名はクラスター名に一致します。**[アクセス]** フィールドは **[プライベート]** のままにします。  
12. チェック アイコンをクリックしてコンテナーを作成します。

**Azure ポータルを使用して HBase クラスターをプロビジョニングするには、次の手順を実行します。**

> [AZURE.NOTE] PowerShell を使用した新しい HBase クラスターのプロビジョニングの詳細については、[PowerShell を使用した HBase クラスターのプロビジョニング]に関するページを参照してください(#powershell)。

1. [Azure の管理ポータル][azure-portal]にサインインします。

2. 左下隅にある **[新規]** をクリックし、**[データ サービス]**、**[HDInsight]**、**[カスタム作成]** の順にクリックします。

3. クラスター名を入力し、[クラスターの種類] として HBase を選択し、HDInsight のバージョンを選択します。

	![Provide details for the HBase cluster][img-provision-cluster-page1]

	右側のボタンをクリックします。

4. 次の値を入力または選択します。

	- **データ ノード**: HBase クラスターのデータ ノードの数を入力します。 
	- **リージョン/仮想ネットワーク**: 作成した Azure 仮想ネットワークを選択します。
	- **仮想ネットワーク サブネット**: サブネットを選択します。既定の名前は **Subnet-1** です。

	右側のボタンをクリックします。

5. このクラスターで使用する Hadoop ユーザーの **[ユーザー名]** と **[パスワード]** を入力してから、右側のボタンをクリックします。
6. クラスターの既定のストレージ アカウントとして、新しいストレージ アカウントと既存のストレージ アカウントのどちらを使用するか選択します。選択したら、右側のボタンをクリックします。

7. **[スクリプト アクション]** ページで、**[スクリプト アクションの追加]** をクリックして、クラスターを作成するときにクラスターのカスタマイズに使用するカスタム スクリプトの詳細情報を入力します。たとえば、[スクリプト アクション] を使用して、 <a href="http://spark.apache.org/docs/latest/index.html" target="_blank">Apache Spark</a>をインストールするクラスターをカスタマイズできます。詳細については、[スクリプト アクションを使用した HDInsight クラスターのカスタマイズ][hdinsight-customize-cluster]に関するページを参照してください。 
	
	![Configure Script Action to customize an HDInsight HBase cluster][img-provision-cluster-page5]

	<table border='1'>
		<tr><th>プロパティ</th><th>値</th></tr>
		<tr><td>名前</td>
			<td>Script Action の名前を指定します。</td></tr>
		<tr><td>スクリプト URI</td>
			<td>クラスターのカスタマイズのために呼び出されるスクリプトへの URI を指定します。</td></tr>
		<tr><td>ノードの種類</td>
			<td>カスタマイズ スクリプトが実行されるノードを指定します。<b>[すべてのノード]</b>、<b>[ヘッド ノードのみ]</b>、<b>[ワーカー ノードのみ]</b> から選択できます。
		<tr><td>パラメーター</td>
			<td>スクリプトで必要な場合は、パラメーターを指定します。</td></tr>
	</table>

	複数の Script Action を追加して、複数のコンポーネントをクラスターにインストールできます。スクリプトの追加後にチェック マークをクリックすると、クラスターのプロビジョニングが開始されます。


新しい HBase クラスターの使用を開始する場合は、[HDInsight の Hadoop 環境での HBase の使用][hbase-get-started]に関するページに記載されている手順を使用できます。

##<a id="connect"></a>HBase Java RPC API を使用して、仮想ネットワーム内でプロビジョニングされた HBase クラスターへ接続する

1.	IaaS 仮想マシンを同じ Azure 仮想ネットワークと同じサブネットに対してプロビジョニングします。それにより、仮想マシンと HBase クラスターはどちらも同じ内部 DNS サーバーを使用してホスト名を解決します。そのためには、[ギャラリーから] を選択し、データ センターの代わりに仮想ネットワークを選択する必要があります。手順については、[Windows Server を実行する仮想マシンの作成][vm-create]に関するページを参照してください。標準の Windows Server 2012 イメージとサイズの小さい VM で十分です。
	
2.	Java アプリケーションを使用して HBase にリモートで接続する場合は、完全修飾ドメイン名 (FQDN) を使用する必要があります。これを確認するには、HBase クラスターの接続固有の DNS サフィックスを取得する必要があります。それには、Curl を使用して Ambari を照会するか、リモート デスクトップを使用してクラスターに接続します。

	* **Curl** - 次のコマンドを実行します。

			curl -u <username>:<password> -k https://<clustername>.azurehdinsight.net/ambari/api/v1/clusters/<clustername>.azurehdinsight.net/services/hbase/components/hbrest

		返された JSON データで、"host_name" エントリを見つけます。これには、クラスターのノードの完全修飾ドメイン名 (FQDN) が含まれています。次に例を示します。

			...
			"host_name": "wordkernode0.<clustername>.b1.cloudapp.net
			...

		クラスター名で始まるドメイン名の部分は、DNS サフィックスです。たとえば、mycluster.b1.cloudapp.net です。

	* **PowerShell** - 次の PowerShell スクリプトを使用して、DNS サフィックスを返すことができる **Get-ClusterDetail** 関数を登録します。

			function Get-ClusterDetail(
			    [String]
			    [Parameter( Position=0, Mandatory=$true )]
			    $ClusterDnsName,
				[String]
			    [Parameter( Position=1, Mandatory=$true )]
			    $Username,
				[String]
			    [Parameter( Position=2, Mandatory=$true )]
			    $Password,
			    [String]
			    [Parameter( Position=3, Mandatory=$true )]
			    $PropertyName
				)
			{
			<#
			    .SYNOPSIS 
			     Displays information to facilitate HDInsight cluster to cluster scinario within same virtual network.
				.Description
				 This command shows following 4 properties of an HDInsight cluster.
				 1. ZookeeperQuorum (only support HBase type cluster)
					Shows the value of hbase property "hbase.zookeeper.quorum".
				 2. ZookeeperClientPort (only support HBase type cluster)
					Shows the value of hbase property "hbase.zookeeper.property.clientPort".
				 3. HBaseRestServers (only support HBase type cluster)
					Shows a list of host FQDNs which run HBase rest server.
				 4. FQDNSuffix (support all type cluster)
					Shows FQDN suffix of hosts in the cluster.
			    .EXAMPLE
			     Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName ZookeeperQuorum
			     This command shows the value of hbase property "hbase.zookeeper.quorum".
			    .EXAMPLE
			     Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName ZookeeperClientPort
			     This command shows the value of hbase property "hbase.zookeeper.property.clientPort".
			    .EXAMPLE
			     Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName HBaseRestServers
			     This command shows a list of host FQDNs which run HBase rest server.
			    .EXAMPLE
			     Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName FQDNSuffix
			     This command shows FQDN suffix of hosts in the cluster.
			#>
			
				$DnsSuffix = ".azurehdinsight.net"
				
				$ClusterFQDN = $ClusterDnsName + $DnsSuffix
				$webclient = new-object System.Net.WebClient
				$webclient.Credentials = new-object System.Net.NetworkCredential($Username, $Password)
			
				if($PropertyName -eq "ZookeeperQuorum")
				{
					$Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.zookeeper.quorum"
					$Response = $webclient.DownloadString($Url)
					$JsonObject = $Response | ConvertFrom-Json
					Write-host $JsonObject.items[0].properties.'hbase.zookeeper.quorum'
				}
				if($PropertyName -eq "ZookeeperClientPort")
				{
					$Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.zookeeper.property.clientPort"
					$Response = $webclient.DownloadString($Url)
					$JsonObject = $Response | ConvertFrom-Json
					Write-host $JsonObject.items[0].properties.'hbase.zookeeper.property.clientPort'
				}
				if($PropertyName -eq "HBaseRestServers")
				{
					$Url1 = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.rest.port"
					$Response1 = $webclient.DownloadString($Url1)
					$JsonObject1 = $Response1 | ConvertFrom-Json
					$PortNumber = $JsonObject1.items[0].properties.'hbase.rest.port'
					
					$Url2 = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/services/hbase/components/hbrest"
					$Response2 = $webclient.DownloadString($Url2)
					$JsonObject2 = $Response2 | ConvertFrom-Json
					foreach ($host_component in $JsonObject2.host_components)
					{
						$ConnectionString = $host_component.HostRoles.host_name + ":" + $PortNumber
						Write-host $ConnectionString
					}
				}
				if($PropertyName -eq "FQDNSuffix")
				{
					$Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/services/yarn/components/resourcemanager"
					$Response = $webclient.DownloadString($Url)
					$JsonObject = $Response | ConvertFrom-Json
					$FQDN = $JsonObject.host_components[0].HostRoles.host_name
					$pos = $FQDN.IndexOf(".")
					$Suffix = $FQDN.Substring($pos + 1)
					Write-host $Suffix
				}
			}

		PowerShell スクリプトを実行した後、次のコマンドで Get-ClusterDetail 関数を使用して DNS サフィックスを返します。このコマンドを使用する場合は、HDInsight HBase のクラスター名、管理者名、および管理者のパスワードを指定します。

			Get-ClusterDetail -ClusterDnsName <yourclustername> -PropertyName FQDNSuffix -Username <clusteradmin> -Password <clusteradminpassword>

		これは、DNS サフィックスを返します。たとえば、**yourclustername.b4.internal.cloudapp.net** です。

	> [AZURE.NOTE] リモート デスクトップを使用して HBase クラスターに接続し (ヘッドノードに接続されます)、コマンド プロンプトから **ipconfig** を実行して DNS サフィックスを取得することもできます。RDP を有効にし、RDP を使用してクラスターに接続する手順については、「[Azure の管理ポータルを使用した HDInsight での Hadoop クラスターの管理][hdinsight-admin-portal]」を参照してください。
	>
	> ![hdinsight.hbase.dns.surffix][img-dns-surffix]


<!-- 
3.	仮想マシンのプライマリ DNS サフィックス構成を変更します。これによって、仮想マシンは、明示的なサフィックスの指定なしで、HBase クラスターのホスト名を自動的に解決します。たとえば、ホスト名 *workernode0* は、HBase クラスターの workernode0 に正しく解決されます。 

	構成を変更するには、次の手順を実行します。

	1. 仮想マシンへの RDP 接続を確立します。 
	2. **[ローカル グループ ポリシー エディター]** を開きます。実行可能ファイルは、gpedit.msc です。
	3. **[コンピューターの構成]**、**[管理用テンプレート]**、**[ネットワーク]** の順に展開し、**[DNS クライアント]** をクリックします。 
	- **[プライマリ DNS サフィックス]** を手順 2. で取得した値に設定します。 

		![hdinsight.hbase.primary.dns.suffix][img-primary-dns-suffix]
	4. **[OK]** をクリックします。 
	5. 仮想マシンを再起動します。
-->

仮想マシンが HBase クラスターと通信できることを確認するには、仮想マシンからコマンド `ping headnode0.<dns suffix>` を使用します。たとえば、「ping headnode0.mycluster.b1.cloudapp.net」のように入力します。

Java アプリケーションでこの情報を使用するには、[HDInsight (Hadoop) 環境の HBase を使用する Java アプリケーションを Maven で構築する方法](azure.microsoft.com/ja-jp/documentation/articles/hdinsight-hbase-build-java-maven/)に関するページの手順に従って、アプリケーションを作成します。アプリケーションをリモート HBase サーバーに接続するには、ZooKeeper の FQDN を使用するように、この例の **hbase-site.xml** ファイルを変更します。次に例を示します。

	<property>
    	<name>hbase.zookeeper.quorum</name>
    	<value>zookeeper0.<dns suffix>,zookeeper1.<dns suffix>,zookeeper2.<dns suffix></value>
	</property>

> [AZURE.NOTE] 独自の DNS サーバーの使用方法を含め、Azure Virtual Network の名前解決の詳細については、「[名前解決 (DNS)](http://msdn.microsoft.com/ja-jp/library/azure/jj156088.aspx)」を参照してください。

##<a id="powershell"></a>Azure PowerShell を使用して HBase クラスターをプロビジョニングする

**Azure PowerShell を使用して HBase クラスターをプロビジョニングするには、次の手順を実行します。**

1. PowerShell ISE を開きます。
2. 次のコマンドをコピーしてスクリプト ウィンドウに貼り付けます。

		$hbaseClusterName = "<HBaseClusterName>"
		$hadoopUserName = "<HBaseClusterUsername>"
		$hadoopUserPassword = "<HBaseClusterUserPassword>"
		$location = "<HBaseClusterLocation>"  #i.e. "West US"
		$clusterSize = <HBaseClusterSize>  
		$vnetID = "<AzureVirtualNetworkID>"
		$subNetName = "<AzureVirtualNetworkSubNetName>"
		$storageAccountName = "<AzureStorageAccountName>" # Do not use the full name here
		$storageAccountKey = "<AzureStorageAccountKey>"
		$storageContainerName = "<AzureBlobStorageContainer>"
		
		$password = ConvertTo-SecureString $hadoopUserPassword -AsPlainText -Force
		$creds = New-Object System.Management.Automation.PSCredential ($hadoopUserName, $password) 
		
		New-AzureHDInsightCluster -Name $hbaseClusterName `
		                          -ClusterType HBase `
		                          -Version 3.1 `
		                          -Location $location `
		                          -ClusterSizeInNodes $clusterSize `
		                          -Credential $creds `
		                          -VirtualNetworkId $vnetID `
		                          -SubnetName $subNetName `
		                          -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" `
		                          -DefaultStorageAccountKey $storageAccountKey `
		                          -DefaultStorageContainerName $storageContainerName


3. **[スクリプトの実行]** をクリックするか、**F5** キーを押します。
4. クラスターを検証するために、管理ポータルからクラスターを確認するか、または下部のウィンドウから次の PowerShell コマンドレットを実行することができます。

	Get-AzureHDInsightCluster 

##<a id="nextsteps"></a>次のステップ

このチュートリアルでは、HBase クラスターのプロビジョニング方法を学習しました。詳細については、次を参照してください。

- [HDInsight の概要][hdinsight-get-started]に関するページ
- [HDInsight での Hadoop クラスターのプロビジョニング][hdinsight-provision] 
- [HDInsight の Hadoop 環境での HBase の使用][hbase-get-started]
- [HDInsight 環境の HBase で Twitter のセンチメントを分析する][hbase-twitter-sentiment]
- [仮想ネットワークの概要][vnet-overview].


[1]: http://azure.microsoft.com/ja-jp/services/virtual-network/
[2]: http://technet.microsoft.com/ja-jp/library/ee176961.aspx
[3]: http://technet.microsoft.com/ja-jp/library/hh847889.aspx

[hbase-get-started]: ../hdinsight-hbase-get-started/
[hbase-twitter-sentiment]: ../hdinsight-hbase-twitter-sentiment/
[vnet-overview]: http://msdn.microsoft.com/library/azure/jj156007.aspx
[vm-create]: ../virtual-machines-windows-tutorial/

[azure-portal]: https://manage.windowsazure.com
[azure-create-storageaccount]: ../storage-create-storage-account/ 
[azure-purchase-options]: http://azure.microsoft.com/ja-jp/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/ja-jp/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/ja-jp/pricing/free-trial/

[hdinsight-admin-powershell]: ../hdinsight-administer-use-powershell/
[hdinsight-admin-portal]: ../hdinsight-administer-use-management-portal/#rdp

[hdinsight-powershell-reference]: http://msdn.microsoft.com/ja-jp/library/windowsazure/dn479228.aspx


[twitter-streaming-api]: https://dev.twitter.com/docs/streaming-apis
[twitter-statuses-filter]: https://dev.twitter.com/docs/api/1.1/post/statuses/filter


[powershell-install]: ../install-configure-powershell


[hdinsight-customize-cluster]: ../hdinsight-hadoop-customize-cluster/
[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-get-started]: ../hdinsight-get-started/
[hdinsight-storage-powershell]: ../hdinsight-use-blob-storage/#powershell
[hdinsight-analyze-flight-delay-data]: ../hdinsight-analyze-flight-delay-data/
[hdinsight-storage]: ../hdinsight-use-blob-storage/
[hdinsight-use-sqoop]: ../hdinsight-use-sqoop/
[hdinsight-power-query]: ../hdinsight-connect-excel-power-query/
[hdinsight-hive-odbc]: ../hdinsight-connect-excel-hive-ODBC-driver/

[img-dns-surffix]: ./media/hdinsight-hbase-provision-vnet/DNSSuffix.png
[img-primary-dns-suffix]: ./media/hdinsight-hbase-provision-vnet/PrimaryDNSSuffix.png
[img-provision-cluster-page1]: ./media/hdinsight-hbase-provision-vnet/hbasewizard1.png "Provision details for the new HBase cluster"
[img-provision-cluster-page5]: ./media/hdinsight-hbase-provision-vnet/hbasewizard5.png "Use Script Action to customize an HBase cluster"<!--HONumber=42-->
