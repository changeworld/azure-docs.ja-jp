<properties 
   pageTitle="2 つのデータセンター間での HBase レプリケーションの構成 | Microsoft Azure" 
   description="2 つのデータ センター間で HBase のレプリケーションを構成する方法と、クラスターのレプリケーションの用途について説明します。" 
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
   ms.date="04/02/2015"
   ms.author="jgao"/>

# HDInsight での HBase geo レプリケーションの構成

> [AZURE.SELECTOR]
- [Configure VPN connectivity](../hdinsight-hbase-geo-replication-configure-VNETs.md)
- [Configure DNS](hdinsight-hbase-geo-replication-configure-DNS.md)
- [Configure HBase replication](hdinsight-hbase-geo-replication.md) 
 
2 つのデータ センター間に HBase レプリケーションを構成する方法について説明します。クラスターのレプリケーションには次のような用途があります。

- バックアップと障害復旧
- データの集計
- 地理的なデータの分散
- オフライン データ分析と組み合わされたオンラインでのデータの取り込み

クラスターのレプリケーションでは、ソース プッシュの手法が使用されます。HBase クラスターは、ソースまたはターゲットになることも、両方のロールを同時に満たすこともできます。レプリケーションは非同期であり、レプリケーションの目的は最終的な整合性です。レプリケーションが有効になった列ファミリに対する編集をソースが受け取ると、その編集はすべてのターゲット クラスターに伝達されます。クラスター間でデータがレプリケートされるときは、ソース クラスターとそのデータを既に消費されているすべてのクラスターが追跡されて、レプリケーション ループが防止されます。このチュートリアルでは、ソースとターゲットの間のレプリケーションの構成について詳しく説明します。他のクラスター トポロジについては、[Apache HBase のリファレンス ガイド](http://hbase.apache.org/book.html#_cluster_replication)を参照してください。

これは、シリーズの第 3 部です。

- [2 つの仮想ネットワーク間に VPN 接続を構成します][hdinsight-hbase-replication-vnet]
- [仮想ネットワーク用に DNS を構成します][hdinsight-hbase-replication-dns]
- HBase の geo レプリケーションを構成します (このチュートリアル)

次の図は、[2 つの仮想ネットワーク間の VPN 接続の構成][hdinsight-hbase-geo-replication-vnet]に関するページおよび [2 つの仮想ネットワーク間の DNS の構成][hdinsight-hbase-replication-dns]に関するページで作成した 2 つの仮想ネットワークとネットワーク接続を示したものです。

![HDInsight HBase レプリケーション仮想ネットワークの図][img-vnet-diagram]

## <a id="prerequisites"></a>前提条件

このチュートリアルを読み始める前に、次の項目を用意する必要があります。

- **Azure サブスクリプション**。[Azure 無料試用版の取得](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)に関するページを参照してください。

- **Azure PowerShell を実行できるワークステーション**。[Azure PowerShell のインストールおよび使用](http://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/)に関するページを参照してください。PowerShell スクリプトを実行するには、Azure PowerShell を管理者として実行し、実行ポリシーを *RemoteSigned* に設定する必要があります。「Set-ExecutionPolicy コマンドレットの使用」を参照してください。

- **VPN 接続と DNS が構成された 2 つの Azure 仮想ネットワーク**。方法については、「[2 つの Azure 仮想ネットワーク間の VPN 接続の構成][hdinsight-hbase-replication-vnet]」および「[2 つの Azure 仮想ネットワーク間の DNS の構成][hdinsight-hbase-replication-dns]」を参照してください。


	PowerShell スクリプトを実行する前に、次のコマンドレットを使用して Azure サブスクリプションに接続されていることを確認します。

		Add-AzureAccount

	Azure サブスクリプションが複数ある場合は、次のコマンドレットを使用して、現在のサブスクリプションを設定します。

		Select-AzureSubscription <AzureSubscriptionName>



## HDInsight での HBase クラスターのプロビジョニング

「[2 つの Azure 仮想ネットワーク間の VPN 接続の構成][hdinsight-hbase-replication-vnet]」では、ヨーロッパと米国のデータ センターに仮想ネットワークを作成しました。2 つの仮想ネットワークは VPN で接続されています。このセッションでは、各仮想ネットワークに HBase クラスターをプロビジョニングします。このチュートリアルの後半では、一方の HBase クラスターが他の HBase クラスターにレプリケートするようにします。

Azure ポータルでは、カスタム構成オプションで HDInsight クラスターをプロビジョニングすることはできません。たとえば、*hbase.replication* を *true* に設定するような場合です。クラスターをプロビジョニングした後で構成ファイルの値を設定した場合、クラスターが再イメージ化された後で設定は失われます。詳細については、「[HDInsight での Hadoop クラスターのプロビジョニング][hdinsight-provision]」を参照してください。カスタム オプションで HDInsight クラスターをプロビジョニングする方法の 1 つは、Azure PowerShell を使用することです。


**Contoso-VNet-EU で HBase クラスターをプロビジョニングするには**

1. ワークステーションで、Windows PowerShell ISE を開きます。
2. スクリプトの先頭にある変数を設定し、スクリプトを実行します。

		# create hbase cluster with replication enabled
		
		$azureSubscriptionName = "[AzureSubscriptionName]"
		
		$hbaseClusterName = "Contoso-HBase-EU" # This is the HBase cluster name to be used.
		$hbaseClusterSize = 1   # You must provision a cluster that contains at least 3 nodes for high availability of the HBase services.
		$hadoopUserLogin = "[HadoopUserName]"
		$hadoopUserpw = "[HadoopUserPassword]"
		
		$vNetName = "Contoso-VNet-EU"  # This name must match your Europe virtual network name.
		$subNetName = 'Subnet-1' # This name must match your Europe virtual network subnet name.  The default name is "Subnet-1".
		
		$storageAccountName = 'ContosoStoreEU'  # The script will create this storage account for you.  The storage account name doesn't support hyphens. 
		$storageAccountName = $storageAccountName.ToLower() #Storage account name must be in lower case.
		$blobContainerName = $hbaseClusterName.ToLower()  #Use the cluster name as the default container name.
		
		#connect to your Azure subscription
		Add-AzureAccount 
		Select-AzureSubscription $azureSubscriptionName
		
		# Create a storage account used by the HBase cluster
		$location = Get-AzureVNetSite -VNetName $vNetName | %{$_.Location} # use the virtual network location
		New-AzureStorageAccount -StorageAccountName $storageAccountName -Location $location
		
		# Create a blob container used by the HBase cluster
		$storageAccountKey = Get-AzureStorageKey -StorageAccountName $storageAccountName | %{$_.Primary}
		$storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
		New-AzureStorageContainer -Name $blobContainerName -Context $storageContext
		
		# Create provision configuration object
		$hbaseConfigValues = new-object 'Microsoft.WindowsAzure.Management.HDInsight.Cmdlet.DataObjects.AzureHDInsightHBaseConfiguration'
		    
		$hbaseConfigValues.Configuration = @{ "hbase.replication"="true" } #this modifies the hbase-site.xml file
		
		# retrieve vnet id based on vnetname
		$vNetID = Get-AzureVNetSite -VNetName $vNetName | %{$_.id}
		
		$config = New-AzureHDInsightClusterConfig `
		                -ClusterSizeInNodes $hbaseClusterSize `
		                -ClusterType HBase `
		                -VirtualNetworkId $vNetID `
		                -SubnetName $subNetName `
		            | Set-AzureHDInsightDefaultStorage `
		                -StorageAccountName $storageAccountName `
		                -StorageAccountKey $storageAccountKey `
		                -StorageContainerName $blobContainerName `
		            | Add-AzureHDInsightConfigValues `
		                -HBase $hbaseConfigValues
		
		# provision HDInsight cluster
		$hadoopUserPassword = ConvertTo-SecureString -String $hadoopUserpw -AsPlainText -Force     
		$credential = New-Object System.Management.Automation.PSCredential($hadoopUserLogin,$hadoopUserPassword)
		
		$config | New-AzureHDInsightCluster -Name $hbaseClusterName -Location $location -Credential $credential



**Contoso-VNet-US で HBase クラスターをプロビジョニングするには**

- 次の値を指定して同じスクリプトを使用します。

		$hbaseClusterName = "Contoso-HBase-US" # This is the HBase cluster name to be used.
		$vNetName = "Contoso-VNet-US"  # This name must match your Europe virtual network name.
		$storageAccountName = 'ContosoStoreUS'	

	Azure アカウントに既に接続しているので、次のコマンドレットをこれ以上実行する必要ありません。

		Add-AzureAccount 
		Select-AzureSubscription $azureSubscriptionName




## DNS 条件付フォワーダーの構成

[仮想ネットワークの DNS の構成][hdinsight-hbase-replication-dns]に関するページでは、2 つのネットワークに DNS サーバーを構成しました。HBase クラスターには異なるドメイン サフィックスがあります。したがって、新しく DNS 条件付フォワーダーを構成する必要があります。

条件付フォワーダーを構成するには、2 つの HBase クラスターのドメイン サフィックスを知っている必要があります。

**2 つの HBase クラスターのドメイン サフィックスを調べるには**

1. RDP で **Contoso-HBase-EU** に接続します。方法については、「[Azure ポータルを使用した HDInsight での Hadoop クラスターの管理][hdinsight-manage-portal]」を参照してください。これは、実際にはクラスターの headnode0 です。
2. Windows PowerShell コンソールまたはコマンド プロンプトを開きます。
3. **ipconfig** を実行し、**接続固有の DNS サフィックス**の値を記録します。
4. RDP セッションを閉じないでください。後でドメイン名の解決をテストするために必要です。
5. 同じ手順を繰り返し、**Contoso-HBase-US** の**接続固有の DNS サフィックス**を調べます。


**DNS フォワーダーを構成するには**
 
1.	RDP で **Contoso-DNS-EU** に接続します。 
2.	左下の Windows キーをクリックします。
2.	**[管理ツール]** をクリックします。
3.	**[DNS]** をクリックします。
4.	左側のウィンドウで、**[DSN]**、**[Contoso-DNS-EU]** の順に展開します。
5.	**[条件付フォワーダー]** をクリックし、**[新規条件付フォワーダー]** をクリックします。 
5.	次の情報を入力します。
	- **DNS ドメイン**: Contoso-HBase-US の DNS サフィックスを入力します。例: Contoso-HBase-US.f5.internal.cloudapp.net
	- **マスター サーバーの IP アドレス**: 「10.2.0.4」と入力します。これは、Contoso-DNS-US の IP アドレスです。IP を確認してください。実際の DNS サーバーでは IP アドレスが異なる場合があります。
6.	**Enter** キーを押し、**[OK]** をクリックします。以上で、Contoso-DNS-EU から Contoso-DNS-US の IP アドレスを解決できるようになります。
7.	同じ手順を繰り返し、次の値を使用して、Contoso-DNS-US 仮想マシン上の DNS サービスへの DNS 条件付フォワーダーを追加します。
	- **DNS ドメイン**: Contoso-HBase-EU の DNS サフィックスを入力します。 
	- **マスター サーバーの IP アドレス**: 「10.2.0.4」と入力します。これは、Contoso-DNS-EU の IP アドレスです。

**ドメイン名の解決をテストするには**

1. Contoso-HBase-EU の RDP ウィンドウに切り替えます。
2. コマンド プロンプトを開きます。
3. ping コマンドを実行します。

		ping headnode0.[DNS suffix of Contoso-HBase-US]

	ICM プロトコルは、HBase クラスターのワーカー ノードで有効になっています。

4. RDP セッションを閉じないでください。後で必要になります。
5. 同じ手順を繰り返し、Contoso-HBase-US から Contoso-HBase-EU の headnode0 に対して ping を実行します。

>[AZURE.IMPORTANT]次の手順に進む前に、DNS が動作している必要があります。

## HBase テーブル間のレプリケーションの有効化

次に、サンプルの HBase テーブルを作成し、レプリケーションを有効にしてから、いくつかのデータでテストします。使用するサンプル テーブルには、Personal と Office という 2 つの列ファミリがあります。

このチュートリアルでは、ヨーロッパの HBase クラスターをソース クラスターとして使用し、米国の HBase クラスターをターゲット クラスターとして使用します。

ターゲット クラスターが受け取ったデータを格納する場所がわかるように、ソース クラスターとターゲット クラスターの両方に名前と列ファミリが同じ HBase テーブルを作成します。HBase シェルの使用の詳細については、「[Get started with Apache HBase in HDInsight (HDInsight での Apache HBase の使用)][hdinsight-hbase-get-started]」を参照してください。

**Contoso-HBase-EU で HBase テーブルを作成するには**

1. **Contoso-HBase-EU** の RDP ウィンドウに切り替えます。
2. デスクトップで **[Hadoop コマンド ライン]** をクリックします。
2. 次のようにフォルダーを HBase ホーム ディレクトリに変更します。

		cd %HBASE_HOME%\bin
3. 次のように HBase シェルを開きます。

		hbase shell
4. HBase テーブルを作成します。

		create 'Contacts', 'Personal', 'Office'
5. RDP セッションも Hadoop コマンド ライン ウィンドウも閉じないでください。後で必要になります。
	
**Contoso-HBase-US で HBase テーブルを作成するには**

- 同じ手順を繰り返して、Contoso-HBase-US に同じテーブルを作成します。


**レプリケーション ピアとして Contoso-HBase-US を追加するには**

1. **Contoso-HBase-EU** の RDP ウィンドウに切り替えます。
2. HBase シェル ウィンドウで、次の例のように、ピアとしてターゲット クラスター (Contoso-HBase-US) を追加します。

		add_peer '1', 'zookeeper0.contoso-hbase-us.d4.internal.cloudapp.net,zookeeper1.contoso-hbase-us.d4.internal.cloudapp.net,zookeeper2.contoso-hbase-us.d4.internal.cloudapp.net:2181:/hbase'

	サンプルでは、ドメインのサフィックスは *contoso-hbase-us.d4.internal.cloudapp.net* です。米国の HBase クラスターの実際のドメイン サフィックスと一致するように、更新する必要があります。ホスト名の間にスペースがないことを確認してください。

**ソース クラスターでレプリケートされるように各列ファミリを構成するには**

1. **Contso-HBase-EU** RDP セッションの HBase シェル ウィンドウで、レプリケートされるように各列ファミリを構成します。

		disable 'Contacts'
		alter 'Contacts', {NAME => 'Personal', REPLICATION_SCOPE => '1'}
		alter 'Contacts', {NAME => 'Office', REPLICATION_SCOPE => '1'}
		enable 'Contacts'

**HBase テーブルにデータを一括アップロードするには**

サンプルのデータ ファイルは、次の URL でパブリック Azure BLOB コンテナーにアップロードされます。

		wasb://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt

ファイルの内容は次のとおりです。

		8396	Calvin Raji	230-555-0191	5415 San Gabriel Dr.
		16600	Karen Wu	646-555-0113	9265 La Paz
		4324	Karl Xie	508-555-0163	4912 La Vuelta
		16891	Jonathan Jackson	674-555-0110	40 Ellis St.
		3273	Miguel Miller	397-555-0155	6696 Anchor Drive
		3588	Osarumwense Agbonile	592-555-0152	1873 Lion Circle
		10272	Julia Lee	870-555-0110	3148 Rose Street
		4868	Jose Hayes	599-555-0171	793 Crawford Street
		4761	Caleb Alexander	670-555-0141	4775 Kentucky Dr.
		16443	Terry Chander	998-555-0171	771 Northridge Drive

同じデータ ファイルを HBase クラスターにアップロードし、そこからデータをインポートできます。

1. **Contoso-HBase-EU** の RDP ウィンドウに切り替えます。
2. デスクトップで **[Hadoop コマンド ライン]** をクリックします。
3. 次のようにフォルダーを HBase ホーム ディレクトリに変更します。

		cd %HBASE_HOME%\bin

4. データをアップロードします。

		hbase org.apache.hadoop.hbase.mapreduce.ImportTsv -Dimporttsv.columns="HBASE_ROW_KEY,Personal:Name, Personal:HomePhone, Office:Address" -Dimporttsv.bulk.output=/tmpOutput Contacts wasb://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt

		hbase org.apache.hadoop.hbase.mapreduce.LoadIncrementalHFiles /tmpOutput Contacts


## データのレプリケーションが実行されていることを確認します。

次の HBase シェル コマンドで両方のクラスターのテーブルをスキャンすることにより、レプリケーションが行われていることを確認できます。

		Scan 'Contacts'


## 次のステップ

このチュートリアルでは、2 つのデータ センター間に HBase レプリケーションを構成する方法を説明しました。HDInsight と HBase の詳細については、以下を参照してください。

- [HDInsight サービスのページ](http://azure.microsoft.com/services/hdinsight/)
- [HDInsight のドキュメント](http://azure.microsoft.com/documentation/services/hdinsight/)
- [Get started with Apache HBase in HDInsight (HDInsight での Apache HBase の使用)][hdinsight-hbase-get-started]
- [HDInsight HBase の概要][hdinsight-hbase-overview]
- [Azure Virtual Network での HBase クラスターのプロビジョニング][hdinsight-hbase-provision-vnet]
- [HBase で Twitter のセンチメントをリアルタイム分析する][hdinsight-hbase-twitter-sentiment]
- [HDInsight (Hadoop) での Storm と HBase を使用したセンサー データの分析][hdinsight-sensor-data]

[hdinsight-hbase-geo-replication-vnet]: hdinsight-hbase-geo-replication-configure-VNets.md
[hdinsight-hbase-geo-replication-dns]: ../hdinsight-hbase-geo-replication-configure-VNet.md


[img-vnet-diagram]: ./media/hdinsight-hbase-geo-replication/HDInsight.HBase.Replication.Network.diagram.png

[powershell-install]: ../install-configure-powershell.md
[hdinsight-hbase-get-started]: ../hdinsight-hbase-get-started.md
[hdinsight-manage-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-hbase-replication-vnet]: hdinsight-hbase-geo-replication-configure-VNets.md
[hdinsight-hbase-replication-dns]: hdinsight-hbase-geo-replication-configure-DNS.md
[hdinsight-hbase-twitter-sentiment]: hdinsight-hbase-analyze-twitter-sentiment.md
[hdinsight-sensor-data]: hdinsight-storm-sensor-data-analysis.md
[hdinsight-hbase-overview]: hdinsight-hbase-overview.md
[hdinsight-hbase-provision-vnet]: hdinsight-hbase-provision-vnet.md
[hdinsight-hbase-get-started]: ../hdinsight-hbase-get-started.md

<!---HONumber=July15_HO2-->