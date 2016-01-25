<properties
	pageTitle="Virtual Network での HBase クラスターのプロビジョニング | Microsoft Azure"
	description="Azure HDInsight での HBase の使用の概要Azure Virtual Network での HDInsight HBase クラスターの作成方法について説明します。"
	keywords=""
	services="hdinsight,virtual-network"
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
   ms.date="11/18/2015"
   ms.author="jgao"/>

# Azure Virtual Network での HBase クラスターのプロビジョニング 

[Azure Virtual Network][1] での Azure HDInsight HBase クラスターの作成方法について説明します。

[AZURE.INCLUDE [hdinsight-azure-preview-portal](../../includes/hdinsight-azure-preview-portal.md)]

* [Azure Virtual Network での HBase クラスターのプロビジョニング](hdinsight-hbase-provision-vnet-v1.md)

アプリケーションが HBase と直接通信できるように、仮想ネットワーク統合を使用して、HBase クラスターをアプリケーションと同じ仮想ネットワークにデプロイできます。次の利点があります。

- Web アプリケーションと HBase クラスターのノード間の直接接続により、HBase Java リモート プロシージャ コール (RPC) API を使用した通信が可能になります。
- トラフィックが複数のゲートウェイやロード バランサーを経由しないためパフォーマンスが向上します。
- 機密情報は、パブリック エンドポイントに公開されることなく、より安全な方法で処理できます。

##前提条件
このチュートリアルを読み始める前に、次の項目を用意する必要があります。

- **Azure サブスクリプション**。[Azure 無料試用版の取得](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)に関するページを参照してください。

- **Azure PowerShell を実行できるワークステーション**。[Azure PowerShell のインストールおよび使用](http://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/)に関するページを参照してください。手順については、[Azure PowerShell のインストールおよび構成に関するページ](../install-configure-powershell.md)を参照してください。PowerShell スクリプトを実行するには、Azure PowerShell を管理者として実行し、実行ポリシーを *RemoteSigned* に設定する必要があります。「[Set-ExecutionPolicy コマンドレットの使用][2]」をご覧ください。

	Azure PowerShell スクリプトを実行する前に、次のコマンドレットを使用して Azure サブスクリプションに接続されていることを確認します。

		Add-AzureAccount

	Azure サブスクリプションが複数ある場合は、次のコマンドレットを使用して、現在のサブスクリプションを設定します。

		Select-AzureSubscription <AzureSubscriptionName>


## HBase クラスターを仮想ネットワークへプロビジョニングする

アプリケーションは、通常、多数のコンポーネントで構成されます。このチュートリアルでは、以下を使用します。

- Azure 仮想ネットワーク
- Azure ストレージ アカウント
- Azure HDInsight HBase クラスター
- (省略可能) DNS サーバーとして機能する Azure 仮想マシン

Azure リソース マネージャーを使用すると、アプリケーション内の複数リソースを 1 つのグループと見なして作業できます。アプリケーションのこれらすべてのリソースを、1 回の連携した操作でデプロイ、更新、または削除できます。デプロイにはテンプレートを使用しますが、このテンプレートは、テスト、ステージング、運用環境などのさまざまな環境に使用できます。グループ全体のロールアップ コストを表示すると、組織の課金ついて明確に把握できます。

**リソース グループを作成するには**

1. [Azure ポータル](https://portal.azure.com)にサインインします。
2. **[新規]**、**[管理]**、**[リソース グループ]** の順にクリックします。
3. 次の値を入力または選択します。

	- **リソース グループ名**: リソース グループの名前を入力します。
	- **サブスクリプション**: このリソース グループで使用する Azure サブスクリプションを選択します。
	- **リソース グループの場所**: Azure データセンターを選択します。この場所は、HDInsight クラスターの場所と一致させる必要はありません。

4. **[作成]** をクリックします。

HBase クラスターをプロビジョニングする前に、Azure 仮想ネットワークが必要になります。

**Azure ポータルを使用して仮想ネットワークを作成するには**

1. [ポータル](https://portal.azure.com)にサインインします。
2. **[新規]**、**[ネットワーク]**、**[仮想ネットワーク]** の順にクリックします。
3. Windows ベースの HDInsight クラスターを使用する場合は、**[デプロイ モデルの選択]** で **[クラシック]** を選択し、Linux ベースの HDInsight クラスターを使用する場合は、**[リソース マネージャー]** を選択します。最後に、**[作成]** をクリックします。

    > [AZURE.NOTE]Windows ベースのクラスターでは、v1 (クラシック) Virtual Network が必要で、Linux ベースのクラスターでは、v2 (Azure リソース マネージャー) Virtual Network が必要です。ネットワークの種類が正しくない場合、クラスターの作成には使用できません。
    >
    > 作成を計画しているクラスターでは使用できない Virtual Network 上にリソースがある場合、クラスターで使用できる新しい Virtual Network を作成し、それを互換性のない Virtual Network に接続できます。その後、必要なネットワーク バージョンでクラスターを作成し、2 つは結合されているので別のネットワークのリソースにアクセスが可能となります。従来の Virtual Network と新しい Virtual Network を接続する方法の詳細については、「[従来の Vnet を新しい Vnet に接続する](../virtual-network/virtual-networks-arm-asm-s2s.md)」を参照してください。
    
4. 次の値を入力または選択します。

	- **名前**: 仮想ネットワークの名前
	- **アドレス空間**: クラスター内のすべてのノードにアドレスを提供するために十分な大きさの、仮想ネットワークのアドレス空間を選択します。そうでないと、プロビジョニングは失敗します。このチュートリアルでは、既定値を使用できます。**[OK]** をクリックして変更を保存します。
    
        > [AZURE.NOTE]この Virtual Network を複数の HDInsight クラスターで使用する場合は、各クラスターにサブネットを 1 つ指定することを強く勧めします。
         
	- **リソース グループ**: このチュートリアルで先ほど作成したリソース グループを選択します。
	- **サブスクリプション**: この仮想ネットワークで使用する Azure サブスクリプションを選択します。
	- **場所**: 場所は作成する HBase クラスターと同じである必要があります。
    
        > [AZURE.NOTE]Azure HDInsight は場所ベースの仮想ネットワークのみをサポートし、アフィニティ グループ ベースの仮想ネットワークは現在取り扱っていません。
        
    Virtual Network の具体的な構成要件など、Virtual Network で HDInsight を使用する方法については、「[Azure Virtual Network を使用した HDInsight 機能の拡張](hdinsight-extend-hadoop-virtual-network.md)」をご覧ください。

5. **[作成]** をクリックします。

既定では、仮想ネットワークは、Azure によって提供される内部ドメイン ネーム システム (DNS) サーバーを使用します。カスタム DNS サーバーを使用した、より高度なネットワーク構成もサポートされています。詳細なガイダンスについては、「[名前解決 (DNS)](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)」をご覧ください。

**(省略可能) DNS サーバー仮想マシンを仮想ネットワークに追加するには**

DNS サーバーはオプションですが、場合によっては必要になります。手順は「[2 つの Azure Virtual Network 間の DNS の構成][hdinsight-hbase-replication-dns]」で説明されています。基本的には次の手順を実行する必要があります。

1. Azure 仮想マシンを仮想ネットワークに追加する
2. 仮想マシンに静的 IP アドレスを設定する
3. 仮想マシンの DNS サーバー ロールを追加する
4. 仮想ネットワークへ DNS サーバーを割り当てる

**Azure ポータルを使用して HBase クラスターをプロビジョニングするには**

> [AZURE.NOTE]Azure PowerShell を使用した新しい HBase クラスターのプロビジョニングの詳細については、「[Azure PowerShell を使用して HBase クラスターをプロビジョニングする](#powershell)」をご覧ください。


**HDInsight クラスターを作成するには**

1. [Azure ポータル](https://portal.azure.com)にサインインします。
2. **[新規]**、**[データ分析]**、**[HDInsight]** の順にクリックします。

    ![Azure ポータルでの新しいクラスターの作成](./media/hdinsight-provision-clusters/HDI.CreateCluster.1.png "Azure ポータルでの新しいクラスターの作成")

3. 次の値を入力または選択します。

  - **[クラスター名]**: クラスターの名前を入力します。その名前を使用できる場合は、クラスター名の横に緑色のチェック マークが表示されます。
  - **クラスターの種類**: **[HBase]** を選択します
  - **クラスターのオペレーティング システム**: **[Windows Server 2012 R2 Datacenter]** を選択します。
  - **サブスクリプション**: このクラスターをプロビジョニングするために使用する Azure サブスクリプションを選択します。
  - **リソース グループ**: このチュートリアルで先ほど作成したリソース グループを選択します。
  - **資格情報**: Hadoop ユーザー (HTTP ユーザー) のユーザー名とパスワードを構成します。クラスターのリモート デスクトップを有効にする場合は、リモート デスクトップ ユーザーのユーザー名とパスワード、アカウントの有効期限を構成する必要があります。下部にある **[選択]** をクリックして、変更を保存します。
  - **データ ソース**: クラスターの既定のファイル システムとして使用する既存の Azure ストレージ アカウントを指定するか、新しく作成します。既定のコンテナーの既定の名前は、クラスター名です。ストレージ アカウントの場所は、クラスターの場所も決定します。
  - **ノード価格レベル**: 学習または評価目的では、コストを最小限に抑えるために 1 つのリージョン ノードを選択します。

  	- **選択方法**: すべてのサブスクリプションのストレージ アカウントを参照できるようにする場合は、**[すべてのサブスクリプションから]** を設定します。既存のストレージ アカウントの **[ストレージ名]** と **[アクセス キー]** を入力する場合は、**[アクセス キー]** を設定します。
  	- **ストレージ アカウントの選択/新規作成**: クラスターに関連付ける既存のストレージ アカウントを参照して選択する場合は **[ストレージ アカウントの選択]** をクリックします。新しいストレージ アカウントを作成する場合は **[新規作成]** をクリックします。表示されたフィールドに、ストレージ アカウントの名前を入力します。名前を使用できる場合は、緑色のチェック マークが表示されます。
    - **[既定のコンテナーの選択]**: これを使用して、クラスターで使用する既定のコンテナーの名前を入力します。任意の名前を入力できますが、コンテナーが特定のクラスターで使用されていることを簡単に認識できるように、クラスターと同じ名前を使用することをお勧めします。
  	- **場所**: ストレージ アカウントが存在するリージョン、またはその中にストレージ アカウントが作成されるリージョン。この場所は、クラスターの場所を決定します。クラスターとその既定のストレージ アカウントは、同じデータセンター内に配置されている必要があります。

  - **ノード価格レベル**: クラスターで必要なワーカー ノードの数を設定します。クラスターの推定コストがブレード内に表示されます。
	- **オプションの構成**: このチュートリアルでは、**Virtual Network** のみ構成する必要があります。このチュートリアルで先ほど作成した仮想ネットワークを選択します。サブネットも選択していることを確認します。

4. **[作成]** をクリックします。


新しい HBase クラスターの使用を開始する場合は、「[HDInsight の Hadoop 環境で HBase を使用する](../hdinsight-hbase-get-started.md)」に記載されている手順を使用できます。

##HBase Java RPC API を使用して、仮想ネットワーム内でプロビジョニングされた HBase クラスターへ接続する

1.	サービスとしてのインフラストラクチャ (IaaS) 仮想マシンを同じ Azure 仮想ネットワークと同じサブネットに対してプロビジョニングします。それにより、仮想マシンと HBase クラスターはどちらも同じ内部 DNS サーバーを使用してホスト名を解決します。そのためには、**[ギャラリーから]** を選択し、データ センターの代わりに仮想ネットワークを選択する必要があります。手順については、「[Windows Server を実行する仮想マシンの作成](../virtual-machines-windows-tutorial.md)」を参照してください。標準の Windows Server 2012 イメージとサイズの小さい VM で十分です。

2.	Java アプリケーションを使用して HBase にリモートで接続する場合は、完全修飾ドメイン名 (FQDN) を使用する必要があります。これを確認するには、HBase クラスターの接続固有の DNS サフィックスを取得する必要があります。それには、Curl を使用して Ambari を照会するか、リモート デスクトップを使用してクラスターに接続します。

	* **Curl**: 次のコマンドを実行します。

			curl -u <username>:<password> -k https://<clustername>.azurehdinsight.net/ambari/api/v1/clusters/<clustername>.azurehdinsight.net/services/hbase/components/hbrest

		返された JavaScript Object Notation (JSON) データで、"host\_name" エントリを見つけます。これには、クラスターのノードの FQDN が含まれています。次に例を示します。

			...
			"host_name": "wordkernode0.<clustername>.b1.cloudapp.net
			...

		クラスター名で始まるドメイン名の部分は、DNS サフィックスです。たとえば、mycluster.b1.cloudapp.net です。

	* **Azure PowerShell** - 次の Azure PowerShell スクリプトを使用して、DNS サフィックスを返すことができる **Get-ClusterDetail** 関数を登録します。

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
			     Displays information to facilitate an HDInsight cluster-to-cluster scenario within the same virtual network.
				.Description
				 This command shows the following 4 properties of an HDInsight cluster:
				 1. ZookeeperQuorum (supports only HBase type cluster)
					Shows the value of HBase property "hbase.zookeeper.quorum".
				 2. ZookeeperClientPort (supports only HBase type cluster)
					Shows the value of HBase property "hbase.zookeeper.property.clientPort".
				 3. HBaseRestServers (supports only HBase type cluster)
					Shows a list of host FQDNs that run the HBase REST server.
				 4. FQDNSuffix (supports all cluster types)
					Shows the FQDN suffix of hosts in the cluster.
			    .EXAMPLE
			     Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName ZookeeperQuorum
			     This command shows the value of HBase property "hbase.zookeeper.quorum".
			    .EXAMPLE
			     Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName ZookeeperClientPort
			     This command shows the value of HBase property "hbase.zookeeper.property.clientPort".
			    .EXAMPLE
			     Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName HBaseRestServers
			     This command shows a list of host FQDNs that run the HBase REST server.
			    .EXAMPLE
			     Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName FQDNSuffix
			     This command shows the FQDN suffix of hosts in the cluster.
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

		Azure PowerShell スクリプトを実行した後、次のコマンドで **Get-ClusterDetail** 関数を使用して DNS サフィックスを返します。このコマンドを使用する場合は、HDInsight HBase のクラスター名、管理者名、および管理者のパスワードを指定します。

			Get-ClusterDetail -ClusterDnsName <yourclustername> -PropertyName FQDNSuffix -Username <clusteradmin> -Password <clusteradminpassword>

		これは、DNS サフィックスを返します。たとえば、**yourclustername.b4.internal.cloudapp.net** です。

	> [AZURE.NOTE]リモート デスクトップを使用して HBase クラスターに接続し (ヘッド ノードに接続されます)、コマンド プロンプトから **ipconfig** を実行して DNS サフィックスを取得することもできます。RDP を有効にし、リモート デスクトップ プロトコル (RDP) を使用してクラスターに接続する手順については、「[Azure ポータルを使用した HDInsight での Hadoop クラスターの管理][hdinsight-admin-portal]」をご覧ください。
	>
	> ![hdinsight.hbase.dns.surffix][img-dns-surffix]


<!--
3.	Change the primary DNS suffix configuration of the virtual machine. This enables the virtual machine to automatically resolve the host name of the HBase cluster without explicit specification of the suffix. For example, the *workernode0* host name will be correctly resolved to workernode0 of the HBase cluster.

	To make the configuration change:

	1. RDP into the virtual machine.
	2. Open **Local Group Policy Editor**. The executable is gpedit.msc.
	3. Expand **Computer Configuration**, expand **Administrative Templates**, expand **Network**, and then click **DNS Client**.
	- Set **Primary DNS Suffix** to the value obtained in step 2:

		![hdinsight.hbase.primary.dns.suffix][img-primary-dns-suffix]
	4. Click **OK**.
	5. Reboot the virtual machine.
-->

仮想マシンが HBase クラスターと通信できることを確認するには、仮想マシンからコマンド `ping headnode0.<dns suffix>` を使用します。たとえば、「ping headnode0.mycluster.b1.cloudapp.net」のように入力します。

Java アプリケーションでこの情報を使用するには、「[HDInsight (Hadoop) 環境の HBase を使用する Java アプリケーションを Maven で構築](hdinsight-hbase-build-java-maven.md)」の手順に従って、アプリケーションを作成します。アプリケーションをリモート HBase サーバーに接続するには、Zookeeper の FQDN を使用するように、この例の **hbase-site.xml** ファイルを変更します。次に例を示します。

	<property>
    	<name>hbase.zookeeper.quorum</name>
    	<value>zookeeper0.<dns suffix>,zookeeper1.<dns suffix>,zookeeper2.<dns suffix></value>
	</property>

> [AZURE.NOTE]独自の DNS サーバーの使用方法を含め、Azure Virtual Network の名前解決の詳細については、「[名前解決 (DNS)](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)」をご覧ください。

##Azure PowerShell を使用して HBase クラスターをプロビジョニングする

**Azure PowerShell を使用して HBase クラスターをプロビジョニングするには**

1. Azure PowerShell Integrated Scripting Environment (ISE) を開きます。
2. 次のコマンドをコピーしてスクリプト ウィンドウに貼り付けます。

		$hbaseClusterName = "<HBaseClusterName>"
		$hadoopUserName = "<HBaseClusterUsername>"
		$hadoopUserPassword = "<HBaseClusterUserPassword>"
		$location = "<HBaseClusterLocation>"  #i.e. "West US"
		$clusterSize = <HBaseClusterSize>  
		$resourceGroup = "<AzureResourceGroupName>"
		$vnetID = "<AzureVirtualNetworkID>"
		$subNetName = "<AzureVirtualNetworkSubNetName>"
		$storageAccountName = "<AzureStorageAccountName>" # Do not use the full name here
		$storageAccountKey = "<AzureStorageAccountKey>"
		$storageContainerName = "<AzureBlobStorageContainer>"

		$password = ConvertTo-SecureString $hadoopUserPassword -AsPlainText -Force
		$creds = New-Object System.Management.Automation.PSCredential ($hadoopUserName, $password)

		New-AzureHDInsightCluster -ResourceGroupName $resourceGroup `
		                          -ClusterName $hbaseClusterName `
				                    	-ClusterType HBase `
				                    	-Location $location `
				                    	-ClusterSizeInNodes $clusterSize `
		                          -HttpCredential $creds `
				                    	-VirtualNetworkId $vnetID `
				                    	-SubnetName $subNetName `
				                    	-DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" `
				                    	-DefaultStorageAccountKey $storageAccountKey `
		                          -DefaultStorageContainer $storageContainerName


3. **[スクリプトの実行]** をクリックするか、**F5** キーを押します。
4. クラスターを検証するために、Azure ポータルからクラスターを確認するか、下部のウィンドウから次の Azure PowerShell コマンドレットを実行することができます。

	Get-AzureHDInsightCluster

##次のステップ

このチュートリアルでは、HBase クラスターのプロビジョニング方法を学習しました。詳細については、次を参照してください。

- [HDInsight の概要](../hdinsight-get-started.md)
- [HDInsight での HBase レプリケーションの構成](hdinsight-hbase-geo-replication.md)
- [HDInsight での Hadoop クラスターのプロビジョニング](hdinsight-provision-clusters.md)
- [HDInsight の Hadoop 環境での HBase の使用](../hdinsight-hbase-get-started.md)
- [HDInsight 環境の HBase で Twitter のセンチメントをリアルタイム分析する](../hdinsight-hbase-twitter-sentiment.md)
- [仮想ネットワークの概要][vnet-overview]


[1]: http://azure.microsoft.com/services/virtual-network/
[2]: http://technet.microsoft.com/library/ee176961.aspx
[3]: http://technet.microsoft.com/library/hh847889.aspx

[hbase-get-started]: ../hdinsight-hbase-get-started.md
[hbase-twitter-sentiment]: ../hdinsight-hbase-twitter-sentiment.md
[vnet-overview]: ../virtual-network/virtual-networks-overview.md
[vm-create]: ../virtual-machines-windows-tutorial.md

[azure-portal]: https://portal.azure.com
[azure-create-storageaccount]: ../storage-create-storage-account.md
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md#rdp

[hdinsight-powershell-reference]: https://msdn.microsoft.com/library/dn858087.aspx


[twitter-streaming-api]: https://dev.twitter.com/docs/streaming-apis
[twitter-statuses-filter]: https://dev.twitter.com/docs/api/1.1/post/statuses/filter


[powershell-install]: ../install-configure-powershell.md


[hdinsight-customize-cluster]: hdinsight-hadoop-customize-cluster.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-get-started]: ../hdinsight-get-started.md
[hdinsight-storage-powershell]: ../hdinsight-use-blob-storage.md#powershell
[hdinsight-analyze-flight-delay-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-storage]: ../hdinsight-use-blob-storage.md
[hdinsight-use-sqoop]: hdinsight-use-sqoop.md
[hdinsight-power-query]: hdinsight-connect-excel-power-query.md
[hdinsight-hive-odbc]: hdinsight-connect-excel-hive-ODBC-driver.md
[hdinsight-hbase-replication-dns]: hdinsight-hbase-geo-replication-configure-DNS.md

[img-dns-surffix]: ./media/hdinsight-hbase-provision-vnet/DNSSuffix.png
[img-primary-dns-suffix]: ./media/hdinsight-hbase-provision-vnet/PrimaryDNSSuffix.png
[img-provision-cluster-page1]: ./media/hdinsight-hbase-provision-vnet/hbasewizard1.png "新しい HBase クラスターのプロビジョニングの詳細"
[img-provision-cluster-page5]: ./media/hdinsight-hbase-provision-vnet/hbasewizard5.png "Script Action を使って HBase クラスターをカスタマイズする"

[azure-preview-portal]: https://portal.azure.com

<!---HONumber=AcomDC_0114_2016-->