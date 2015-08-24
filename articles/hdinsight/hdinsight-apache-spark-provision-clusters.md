<properties 
   pageTitle="Hdinsight での Apache Spark クラスターのプロビジョニング | Microsoft Azure" 
   description="Azure ポータル、Azure PowerShell、コマンド ライン、HDInsight .NET SDK を使用して Azure HDInsight に Spark クラスターをプロビジョニングする方法について説明します" 
   services="hdinsight" 
   documentationCenter="" 
   authors="nitinme" 
   manager="paulettm" 
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="07/10/2015"
   ms.author="nitinme"/>

# カスタム オプションを使用した HDInsight での Apache Spark クラスターのプロビジョニング

ほとんどのシナリオでは、「[HDInsight での Apache Spark の概要](hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql.md)」で説明されている簡易作成方法を使用して Spark クラスターをプロビジョニングできます。特定のシナリオでは、カスタマイズされたクラスターのプロビジョニングが必要になる場合があります。たとえば、外部メタデータ ストアをアタッチしてクラスターの有効期間より長く永続的 Hive メタデータを保持する場合や、クラスターで追加のストレージを使用する場合です。

このようなシナリオのため、この記事では、Azure ポータル、Azure PowerShell、または HDInsight .NET SDK を使用して、HDInsight 上のカスタマイズされた Spark クラスターをプロビジョニングする方法を説明します。


**前提条件:**

この記事の手順を開始する前に、Azure サブスクリプションが必要です。[Azure 無料試用版の取得](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)に関するページを参照してください。

##<a id="configuration"></a>さまざまな構成オプション

###その他のストレージ

構成中に、Azure BLOB ストレージ アカウントと既定のコンテナーを指定する必要があります。このコンテナーは、既定の保存先としてクラスターで使用されます。必要に応じて、クラスターに関連付ける追加の Azure ストレージ アカウントBLOB も指定できます。

>[AZURE.NOTE] 1 つの Blob ストレージ コンテナーを複数のクラスターで共有しないでください。これはサポートされていません。

従属的な BLOB ストアの使用の詳細については、「[HDInsight での Azure BLOB ストレージの使用](hdinsight-use-blob-storage.md)」をご覧ください。

###メタストア

Spark では、生データに対してスキーマおよび Hive テーブルを定義できます。これらのスキーマおよびテーブル メタデータは外部のメタストアに保存できます。メタストアを使用すると Hive メタデータを保持できるため、新しいクラスターをプロビジョニングするときに、Hive テーブルを再作成する必要はありません。既定では、Hive は組み込みデータベースを使用してこの情報を格納します。組み込みデータベースは、クラスターが削除されるとメタデータを保持できません。

### クラスターのカスタマイズ

追加コンポーネントをインストールするか、プロビジョニング中にスクリプトを使用してクラスターの構成をカスタマイズできます。このようなスクリプトは、**スクリプト操作**を使用して実行します。これは Azure ポータル、HDInsight Windows PowerShell コマンドレット、HDInsight .NET SDK で使用できる構成オプションです。詳細については、「[Script Action を使って HDInsight クラスターをカスタマイズする][hdinsight-customize-cluster]」をご覧ください。


###仮想ネットワーク

[Azure 仮想ネットワーク](http://azure.microsoft.com/documentation/services/virtual-network/)によって、ソリューションに必要なリソースを含む、セキュリティで保護された永続的なネットワークを作成できます。仮想ネットワークでは、次のことが可能になります。

* プライベート ネットワーク (クラウドのみ) 内でのクラウド リソース間の接続

	![diagram of cloud-only configuration](./media/hdinsight-apache-spark-provision-clusters/hdinsight-vnet-cloud-only.png)

* 仮想プライベート ネットワーク (VPN) を使用したローカル データセンター ネットワークへのクラウド リソースの接続 (サイト間またはポイント対サイト)

	サイト間構成では、ハードウェア VPN を使用するか、ルーティングとリモート アクセス サービスを使用して、データセンターから複数のリソースを Azure 仮想ネットワークに接続できます。

	![diagram of site-to-site configuration](./media/hdinsight-apache-spark-provision-clusters/hdinsight-vnet-site-to-site.png)

	ポイント対サイト構成では、ソフトウェア VPN を使用して、特定のリソースを Azure 仮想ネットワークに接続できます。

	![diagram of point-to-site configuration](./media/hdinsight-apache-spark-provision-clusters/hdinsight-vnet-point-to-site.png)

仮想ネットワークの機能、利点の詳細については、「[Azure 仮想ネットワークの概要](http://msdn.microsoft.com/library/azure/jj156007.aspx)」を参照してください。

> [AZURE.NOTE] クラスターをプロビジョニングする前に、Azure 仮想ネットワークを作成する必要があります。詳細については、「[仮想ネットワークの構成タスク](http://msdn.microsoft.com/library/azure/jj156206.aspx)」を参照してください。
>
> Azure HDInsight は場所ベースの仮想ネットワークのみをサポートし、アフィニティ グループ ベースの仮想ネットワークは現在取り扱っていません。
>
> 1 つのクラスターには単一のサブネットを指定することを強くお勧めします。

##<a id="portal"></a>Azure ポータルの使用

HDInsight 上の Spark クラスターは、既定のファイル システムとして Azure BLOB ストレージ コンテナーを使用します。HDInsight クラスターを作成するには、同じデータ センターにある Azure ストレージ アカウントが必要です。詳細については、「[HDInsight での Azure BLOB ストレージの使用](hdinsight-hadoop-use-blob-storage.md)」をご覧ください。Azure ストレージ アカウントの作成の詳細については、「[ストレージ アカウントの作成方法][azure-create-storageaccount]」をご覧ください。

**カスタム作成オプションを使用して HDInsight クラスターを作成するには**

1. [Azure ポータル][azure-management-portal]にサインインします。
2. ページ下部の **[+ 新規]** をクリックし、**[データ サービス]**、**[HDINSIGHT]**、**[カスタム作成]** の順にクリックします。
3. **[クラスターの詳細]** ページで、次の値を入力または選択します。

	![HDInsight 上の Spark クラスターの詳細を提供します](./media/hdinsight-apache-spark-provision-clusters/HDI.CustomProvision.Page1.png "HDInsight 上の Spark クラスターの詳細を提供します")

    <table border='1'>
	<tr><th>プロパティ</th><th>値</th></tr>
	<tr><td>クラスター名</td>
		<td><p>クラスターの名前を指定します。</p>
			<ul>
			<li>Domain Name System (DNS) 名の最初と最後の文字は英数字にする必要があります。それ以外ではダッシュ (-) を使用できます。</li>
			<li>3 ～ 63 文字のクラスター名を入力できます。</li>
			</ul></td></tr>
	<tr><td>クラスターの種類</td>
		<td>クラスターの種類では、<strong>[Spark]</strong> を選択します。</td></tr>
	<tr><td>オペレーティング システム</td>
		<td>Windows で実行する Spark クラスターをプロビジョニングするには、<b>[Windows Server 2012 R2 Data Center]</b> を選択します。</td></tr>
	<tr><td>HDInsight のバージョン</td>
		<td>バージョンを選択します。Spark の場合、使用できるバージョン オプションは <b>[HDInsight 3.2]</b> だけであり、これは Spark 1.3.1 を使用します。</td></tr>
	</table>
	表に示されている値を入力または選択し、右矢印をクリックします。

4. **[クラスターの構成]** ページで、次の値を入力または選択します。

	![クラスターのノードの数と種類を指定します](./media/hdinsight-apache-spark-provision-clusters/HDI.CustomProvision.Page2.png "クラスターのノードの数と種類を指定します")

	<table border="1">
<tr><th>名前</th><th>値</th></tr>
<tr><td>データ ノード</td><td>デプロイするデータ ノードの数です。テストでは、単一ノード クラスターを作成します。<br />クラスター サイズの制限は、Azure サブスクリプションによって変わります。制限値を上げるには、Azure の課金サポートにお問い合わせください。</td></tr>
<tr><td>リージョン/仮想ネットワーク</td><td><p>最後の手順で作成したストレージ アカウントと同じリージョンを選択します。HDInsight は、同じリージョンに配置されたストレージ アカウントを必要とします。これ以後の構成作業では、ここで指定した地域と同じリージョンにあるストレージ アカウントしか選択できません。</p>()。<br/>Azure 仮想ネットワークを作成した場合は、HDInsight クラスターで使用するよう構成するネットワークを選択できます。</p><p>Azure 仮想ネットワークの作成の詳細については、「<a href="http://msdn.microsoft.com/library/azure/jj156206.aspx">仮想ネットワークの構成タスク</a>」をご覧ください。</p></td></tr>
<tr><td>ヘッド ノード サイズ</td><td><p>ヘッド ノードの仮想マシン (VM) サイズを選択します。</p></td></tr>
<tr><td>データ ノード サイズ</td><td><p>データ ノードの VM サイズを選択します。</p></td></tr>
</table>
	>[AZURE.NOTE] VM の選択に基づき、料金が異なる場合があります。HDInsight は、クラスター ノードにすべて Standard レベルの VM を使用します。VM サイズに応じた料金の詳細については、「<a href="http://azure.microsoft.com/pricing/details/hdinsight/" target="_blank">HDInsight 料金</a>」をご覧ください。


5. **[クラスター ユーザーの構成]** ページで、次の情報を指定します。

    ![管理ユーザーと RDP ユーザーの詳細を提供します](./media/hdinsight-apache-spark-provision-clusters/HDI.CustomProvision.Page3.png "管理ユーザーと RDP ユーザーの詳細を提供します")

    <table border='1'>
	<tr><th>プロパティ</th><th>値</th></tr>
	<tr><td>HTTP ユーザー名</td>
		<td>HDInsight クラスターのユーザー名を指定します。</td></tr>
	<tr><td>HTTP パスワード / パスワードの確認</td>
		<td>HDInsight クラスターのユーザー パスワードを指定します。</td></tr>
	<tr><td>Enable remote desktop for cluster (クラスターのリモート デスクトップ アクセスを有効にする)</td>
		<td>このチェック ボックスをオンにして、プロビジョニングされるとクラスター ノードにリモートにできるリモート デスクトップ ユーザーのユーザー名、パスワード、有効期限を指定します。また、クラスターがプロビジョニングされた後にリモート デスクトップを有効にすることもできます。手順については、「<a href="hdinsight-administer-use-management-portal/#rdp" target="_blank">RDP を使用した HDInsight クラスターへの接続</a>」をご覧ください。</td></tr>
	<tr><td>Hive/Oozie メタストアの入力</td>
		<td>このチェックボックスをオンにして、クラスターと同じデータ センターにある Hive/Oozie メタストアとして使用する SQL データベースを指定します。このチェック ボックスを選択した場合、ウィザードの後続のページで Azure SQL データベースの詳細を指定する必要があります。これは、クラスターが削除された後でも Hive/Oozie ジョブに関するメタデータを保持する場合に便利です。メタストアの作成方法について詳しくは、「<a href="https://azure.microsoft.com/ja-jp/documentation/articles/sql-database-get-started/" target="_blank">最初の Azure SQL データベースを作成する</a>」をご覧ください。</td></tr>
	</td></tr>
</table>
	>[AZURE.NOTE] メタストアに使用される Azure SQL Database は、Azure HDInsight などの他の Azure サービスに接続できる必要があります。Azure SQL データベース ダッシュボードの右側に表示されているサーバー名をクリックします。これは、SQL データベース インスタンスが実行されているサーバーです。サーバー ビューが表示されたら、**[構成]** をクリックします。**[Windows** **Azure サービス]** に対して **[はい]** をクリックし、**[保存]** をクリックします。

    右矢印をクリックします。

6. **[Configure Hive/Oozie Metastore]** ページで、次の情報を指定します。

    ![Hive/Oozie メタストア データベースの詳細を指定します](./media/hdinsight-apache-spark-provision-clusters/HDI.CustomProvision.Page4.png "Hive/Oozie メタストア データベースの詳細を指定します")

	Hive/OOzie のメタストアとして使用する Azure SQL データベースを指定します。Hive メタストアと Oozie メタストアの両方に同じデータベースを指定できます。この SQL データベースは、HDInsight クラスターと同じデータ センターにある必要があります。リスト ボックスの一覧には、<strong>[クラスターの詳細]</strong> ページで指定したデータ センターにある SQL データベースしか表示されません。また、ユーザー名とパスワードを指定し、選択した Azure SQL データベースに接続します。

    >[AZURE.NOTE] メタストアに使用される Azure SQL Database は、Azure HDInsight などの他の Azure サービスに接続できる必要があります。Azure SQL データベース ダッシュボードの右側に表示されているサーバー名をクリックします。これは、SQL データベース インスタンスが実行されているサーバーです。サーバー ビューが表示されたら、**[構成]** をクリックします。**[Azure サービス]** に対して **[はい]** をクリックし、**[保存]** をクリックします。

    右矢印をクリックします。

7. **[ストレージ アカウント]** ページで、次の値を指定します。

    ![ストレージ アカウントの詳細を指定します](./media/hdinsight-apache-spark-provision-clusters/HDI.CustomProvision.Page5.png "ストレージ アカウントの詳細を指定します")

	<table border='1'>
	<tr><th>プロパティ</th><th>値</th></tr>
	<tr><td>ストレージ アカウント</td>
		<td>HDInsight クラスターの既定のファイル システムとして使用する Azure ストレージ アカウントを指定します。次の 3 つのオプションのいずれかを選択できます。
		<ul>
			<li><strong>既存のストレージを使用する</strong></li>
			<li><strong>新しいストレージを作成する</strong></li>
			<li><strong>別のサブスクリプションのストレージを使用する</strong></li>
		</ul>
		</td></tr>
	<tr><td>アカウント名</td>
		<td><ul>
			<li>既存のストレージを使用する場合は、<strong>[アカウント名]</strong> で既存のストレージ アカウントを選択します。ドロップダウンの一覧には、クラスターをプロビジョニングする対象として選択したデータ センターにあるストレージ アカウントのみが表示されます。</li>
			<li><strong>[新しいストレージを作成する]</strong> または <strong>[別のサブスクリプションのストレージを使用する]</strong> を選択した場合は、ストレージ アカウント名を指定する必要があります。</li>
		</ul></td></tr>
	<tr><td>アカウント キー</td>
		<td><strong>[別のサブスクリプションのストレージを使用する]</strong> を選択した場合は、そのストレージ アカウントのアカウント キーを指定します。</td></tr>
	<tr><td>既定のコンテナー</td>
		<td><p>ストレージ アカウントの既定のコンテナーを指定します。既定のコンテナーは、HDInsight クラスターの既定のファイル システムとして使用されます。<strong>[ストレージ アカウント]</strong> フィールドで <strong>[既存のストレージを使用する]</strong> を選択したにもかかわらずそのアカウントに既存のコンテナーがない場合は、既定でクラスターと同じ名前のコンテナーが作成されます。クラスター名と同じ名前のコンテナーが既に存在する場合は、コンテナー名に連番が付加されます。たとえば、mycontainer1、mycontainer2、などとなります。ただし、既存のストレージ アカウントに指定したクラスター名と異なる名前のコンテナーがある場合は、そのコンテナーを使用できます。</p>
        <p>新しいストレージの作成または別の Azure サブスクリプションのストレージの使用を選択した場合は、既定のコンテナー名を指定する必要があります。</p>
    </td></tr>
	<tr><td>追加のストレージ アカウント</td>
		<td>HDInsight は複数のストレージ アカウントをサポートします。クラスターで使用できる追加ストレージ アカウントの数に制限はありません。ただし、Azure ポータルを使用してクラスターを作成する場合は、UI の制約により 7 が上限になります。ここで指定した追加のストレージ アカウントのそれぞれに対する [**ストレージ アカウント**] ページがウィザードに追加され、そこでアカウント情報を指定できます。たとえば、上のスクリーンショットでは、追加のストレージ アカウントが 1 つ選択されたため、ページ 5 がダイアログに追加されています。</td></tr>
</table>

	右矢印をクリックします。

8. クラスターの追加の記憶域を構成する場合、**[ストレージ アカウント]** ページで、追加のストレージ アカウントのアカウント情報を入力します。

	![追加ストレージ アカウントの詳細を提供します](./media/hdinsight-apache-spark-provision-clusters/HDI.CustomProvision.Page6.png "追加ストレージ アカウントの詳細を提供します")

    ここでも、既存のストレージを使用する、新しいストレージを作成する、別の Azure サブスクリプションのストレージを使用する、のいずれかを選択できます。値を指定するための手順は、前の手順と同様です。

    > [AZURE.NOTE] HDInsight クラスター用の Azure ストレージ アカウントを選択した後は、そのアカウントを削除することも、別のアカウントに変更することもできません。

8. **[スクリプトのアクション]** ページで　**[スクリプト アクションの追加]** をクリックし、クラスターが作成される際に、クラスターのカスタマイズを実行するカスタム スクリプトに関する詳細を指定します。詳細については、「[Script Action を使って HDInsight クラスターをカスタマイズする][hdinsight-customize-cluster]」をご覧ください。

	スクリプト操作を使用する必要がある場合、次の入力を提供する必要があります。
	
	<table border='1'>
	<tr><th>プロパティ</th><th>値</th></tr>
	<tr><td>名前</td>
		<td>スクリプト アクションの名前を指定します。</td></tr>
	<tr><td>スクリプト URI</td>
		<td>クラスターをカスタマイズするために呼び出すスクリプトの Uniform Resource Identifier (URI) を指定します。</td></tr>
	<tr><td>ノードの種類</td>
		<td>カスタマイズ スクリプトが実行されるノードを指定します。<b>[すべてのノード]</b>、<b>[ヘッド ノードのみ]</b>、<b>[ワーカー ノードのみ]</b> から選択できます。
	<tr><td>パラメーター</td>
		<td>スクリプトで必要な場合は、パラメーターを指定します。</td></tr>
</table>
	複数のスクリプト操作を追加して、クラスターに複数のコンポーネントをインストールすることができます。スクリプトを追加した後、チェック マークをオンにして、クラスターのプロビジョニングを開始します。



##<a id="powershell"></a>Azure PowerShell の使用

このセクションでは、Azure PowerShell を使用して Azure HDInsight に Apache Spark クラスターをプロビジョニングする方法について説明します。ワークステーションを構成して HDInsight Windows Powershell コマンドレットを実行する方法については、「[Azure PowerShell のインストールおよび構成][powershell-install-configure]」をご覧ください。HDInsight で Azure PowerShell を使用する方法の詳細については、「[PowerShell を使用した HDInsight の管理][hdinsight-admin-powershell]」をご覧ください。HDInsight Windows PowerShell コマンドレットの一覧については、「[HDInsight コマンドレット リファレンス][hdinsight-powershell-reference]」をご覧ください。

> [AZURE.NOTE] このセクションのスクリプトは、Azure 仮想ネットワークの HDInsight クラスターの構成に使用できますが、Azure 仮想ネットワークは作成されません。Azure 仮想ネットワークの作成の詳細については、「[仮想ネットワークの構成タスク](http://msdn.microsoft.com/library/azure/jj156206.aspx)」をご覧ください。

Azure PowerShell を使用して HDInsight クラスターをプロビジョニングするには、以下の手順が必要です。

- Azure のストレージ アカウントの作成
- Azure BLOB コンテナーの作成
- HDInsight クラスターの作成

Windows PowerShell コンソールまたは Windows PowerShell Integrated Scripting Environment (ISE) のいずれかを使用して、スクリプトを実行できます。
 
HDInsight は、既定のファイル システムとして Azure BLOB ストレージ コンテナーを使用します。HDInsight クラスターを作成するには Azure ストレージ アカウントとストレージ コンテナーが必要です。ストレージ アカウントは、HDInsight クラスターと同じデータ センターに置く必要があります。

**Azure アカウントに接続するには**

		Add-AzureAccount 

Azure アカウント資格情報の入力を求められます。

**Azure ストレージ アカウントを作成するには**

		$storageAccountName = "<StorageAcccountName>"	# Provide a Storage account name
		$location = "<MicrosoftDataCenter>"				# For example, "West US"

		# Create an Azure Storage account
		New-AzureStorageAccount -StorageAccountName $storageAccountName -Location $location

既にストレージ アカウントを持っていて、アカウント名とアカウント キーがわからない場合は、次の Windows PowerShell コマンドを使ってその情報を取得できます。

		# List Storage accounts for the current subscription
		Get-AzureStorageAccount

		# List the keys for a Storage account
		Get-AzureStorageKey "<StorageAccountName>"

**Azure BLOB ストレージ コンテナーを作成するには**

		$storageAccountName = "<StorageAccountName>"	# Provide the Storage account name
		$containerName="<ContainerName>"				# Provide a container name

		# Create a storage context object
		$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
		$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName
		                                       -StorageAccountKey $storageAccountKey  

		# Create a Blob storage container
		New-AzureStorageContainer -Name $containerName -Context $destContext

ストレージ アカウントを用意して、BLOB コンテナーを準備したら、クラスターを作成する準備は整いました。

**HDInsight クラスターをプロビジョニングするには**

- 次のコマンドを Azure PowerShell コンソール ウィンドウから実行します。

		$subscriptionName = "<AzureSubscriptionName>"	  # The Azure subscription used for the HDInsight cluster to be created

		$storageAccountName = "<AzureStorageAccountName>" # HDInsight cluster requires an existing Azure Storage account to be used as the default file system

		$clusterName = "<HDInsightClusterName>"			  # The name for the HDInsight cluster to be created
		$clusterNodes = <ClusterSizeInNodes>              # The number of nodes in the HDInsight cluster
        $credentials = Get-Credential		              # User name and password for the Hadoop account. You will use this account to connect to the cluster and run jobs.
           
        # Get the storage primary key based on the account name
		Select-AzureSubscription $subscriptionName
		$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
		$containerName = $clusterName				# Azure Blob container that is used as the default file system for the HDInsight cluster

        # The location of the HDInsight cluster. It must be in the same data center as the Storage account.
        $location = Get-AzureStorageAccount -StorageAccountName $storageAccountName | %{$_.Location} 

		# Create a new HDInsight cluster
		New-AzureHDInsightCluster -Name $clusterName -Credential $credentials -Location $location -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainerName $containerName -ClusterSizeInNodes $clusterNodes -Version "3.2" -ClusterType Spark

	>[AZURE.NOTE] 指定する資格情報は、クラスターの Hadoop ユーザー アカウントの作成に使用されます。このアカウントを使用してクラスターに接続し、ジョブを実行します。Azure ポータルから [簡易作成] オプションを使用してクラスターをプロビジョニングする場合は、既定の Hadoop ユーザー名は "admin" になります。このアカウントと、リモート デスクトップ プロトコル (RDP) のユーザー アカウントとを混同しないでください。RDP ユーザー アカウントは Hadoop ユーザー アカウントとは異なっている必要があります。詳細については、「[Azure の管理ポータルを使用した HDInsight での Hadoop クラスターの管理][hdinsight-admin-portal]」をご覧ください。

	クラスターのプロビジョニングが完了するまでに数分かかる場合があります。

	![HDI.PS.Provision](./media/hdinsight-apache-spark-provision-clusters/hdi-spark-ps-provisioning.png "PowerShell で基本構成を使用して Spark クラスターをプロビジョニングします")


**カスタム構成オプションを使用して HDInsight クラスターをプロビジョニングするには**

クラスターをプロビジョニングするときに、複数の Azure BLOB ストレージ コンテナーに接続するか、Hive メタストアと Oozie メタストア用の Azure SQL データベースを使用するなどの他の構成オプションを使用できます。これにより、データとメタデータの寿命を、クラスターの寿命と切り離すことができます。

> [AZURE.NOTE] Windows PowerShell コマンドレットは、HDInsight クラスターの構成変数を変更する方法として唯一お勧めする方法です。リモート デスクトップ経由でクラスターに接続している間に Hadoop 構成ファイルに加えられた変更は、クラスター パッチの際に上書きされる可能性があります。Azure PowerShell 経由で設定された構成値は、クラスターにパッチが適用された場合でも保持されます。

- 次のコマンドを Windows PowerShell ウィンドウから実行します。

		$subscriptionName = "<Azure subscription>"
		$clusterName = "<cluster name>"
		$clusterNodes = <cluster size in nodes>
		                
		# Get credentials for Hadoop user and Hive/Oozie metastores
		$clusterCredentials = Get-Credential
		$oozieCreds = Get-Credential -Message "Oozie metastore"
		$hiveCreds = Get-Credential -Message "Hive metastore"
		
		# Get default storage account, storage container, and add-on storage account
		$storageAccountName_Default = "<Default storage account name>"
		$containerName_Default = $clusterName
		$storageAccountName_Add1 = "<Add-on storage account name>"
		
		# Get information about Hive and Oozie metastores
		$hiveSQLDatabaseServerName = "<SQLDatabaseServerNameForHiveMetastore>"
		$hiveSQLDatabaseName = "<SQLDatabaseNameForHiveMetastore>"
		$oozieSQLDatabaseServerName = "<SQLDatabaseServerNameForOozieMetastore>""
		$oozieSQLDatabaseName = "<SQLDatabaseNameForOozieMetastore>"

		# Get the virtual network ID and subnet name
		$vnetID = "<AzureVirtualNetworkID>"
		$subNetName = "<AzureVirtualNetworkSubNetName>"

		# Retrieve storage account keys and storage account location
		Select-AzureSubscription $subscriptionName
		$storageAccountKey_Default = Get-AzureStorageKey $storageAccountName_Default | %{ $_.Primary }
		$storageAccountKey_Add1 = Get-AzureStorageKey $storageAccountName_Add1 | %{ $_.Primary }
		$location = Get-AzureStorageAccount -StorageAccountName $storageAccountName_Default | %{$_.Location}
		
		# Specify custom configuration and cluster type
		$config = New-AzureHDInsightClusterConfig -ClusterSizeInNodes $clusterNodes -ClusterType Spark -VirtualNetworkId $vnetID -SubnetName $subNetName | Set-AzureHDInsightDefaultStorage -StorageAccountName "$storageAccountName_Default.blob.core.windows.net" -StorageAccountKey $storageAccountKey_Default -StorageContainerName $containerName_Default | Add-AzureHDInsightStorage -StorageAccountName "$storageAccountName_Add1.blob.core.windows.net" -StorageAccountKey $storageAccountKey_Add1 | Add-AzureHDInsightMetastore -SqlAzureServerName "$hiveSQLDatabaseServerName.database.windows.net" -DatabaseName $hiveSQLDatabaseName -Credential $hiveCreds -MetastoreType HiveMetastore | Add-AzureHDInsightMetastore -SqlAzureServerName "$oozieSQLDatabaseServerName.database.windows.net" -DatabaseName $oozieSQLDatabaseName -Credential $oozieCreds -MetastoreType OozieMetastore
		
		# Provision the cluster with custom configuration                
		New-AzureHDInsightCluster -Name $clusterName -Config $config -Credential $clusterCredentials -Location $location -Version "3.2"

	>[AZURE.NOTE] メタストアに使用される Azure SQL Database は、Azure HDInsight などの他の Azure サービスに接続できる必要があります。Azure SQL データベース ダッシュボードの右側に表示されているサーバー名をクリックします。これは、SQL データベース インスタンスが実行されているサーバーです。サーバー ビューが表示されたら、**[構成]** をクリックします。**[Windows** **Azure サービス]** に対して **[はい]** をクリックし、**[保存]** をクリックします。

	クラスターのプロビジョニングが完了するまでに数分かかる場合があります。

**HDInsight クラスターの一覧を表示するには**

- 次のコマンドを Azure PowerShell コンソール ウィンドウから実行して HDInsight クラスターを一覧表示し、クラスターが正常にプロビジョニングされたことを確認します。

		Get-AzureHDInsightCluster -Name <ClusterName>

##<a id="sdk"></a>HDInsight .NET SDK を使用する場合
HDInsight .NET SDK は、.NET Framework アプリケーションから HDInsight を簡単に操作できる .NET クライアント ライブラリを提供します。

SDK を使用して HDInsight クラスターをプロビジョニングするには、以下の手順を実行する必要があります。

- HDInsight .NET SDK のインストール
- 自己署名証明書の作成
- コンソール アプリケーションの作成
- アプリケーションの実行


**HDInsight .NET SDK をインストールするには**

公開されている SDK の最新のビルドを [NuGet](http://nuget.codeplex.com/wikipage?title=Getting%20Started) からインストールできます。次の手順で、具体的な方法を説明します。

**自己署名証明書を取得するには**

自己署名証明書を作成し、それをコンピューターにインストールして、さらに、Azure サブスクリプションにアップロードします。手順については、「[Create a self-signed certificate (自己署名証明書の作成)](http://go.microsoft.com/fwlink/?LinkId=511138)」を参照してください。


**Visual Studio コンソール アプリケーションを作成するには**

1. Visual Studio 2013 を開きます。

2. **[ファイル]** メニューの **[新規作成]** をクリックし、**[プロジェクト]** をクリックします。

3. **[新しいプロジェクト]** で、次の値を入力するか、選択します。

	<table style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse;">
<tr>
<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">プロパティ</th>
<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">値</th></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">カテゴリ</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px; padding-right:5px;">テンプレート/Visual C#/Windows</td></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">テンプレート</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">コンソール アプリケーション</td></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">名前</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">CreateHDICluster</td></tr>
</table>

4. **[OK]** をクリックしてプロジェクトを作成します。

5. **[ツール]** メニューで **[NuGet パッケージ マネージャー]**、**[パッケージ マネージャー コンソール]** の順にクリックします。

6. コンソールで次のコマンドを実行して、パッケージをインストールします。

		Install-Package Microsoft.WindowsAzure.Management.HDInsight

	このコマンドは、.NET ライブラリおよび .NET ライブラリへの参照を現在の Visual Studio プロジェクトに追加します。

7. ソリューション エクスプローラーで **Program.cs** をダブルクリックして、このファイルを開きます。

8. 次の using ステートメントをファイルの先頭に追加します。

		using System.Security.Cryptography.X509Certificates;
		using Microsoft.WindowsAzure.Management.HDInsight;
		using Microsoft.WindowsAzure.Management.HDInsight.ClusterProvisioning;
		using Microsoft.WindowsAzure.Management.HDInsight.ClusterProvisioning.Data;


9. main() 関数で、次のコードをコピーしてファイルに貼り付けます。

        string thumbprint = "<CertificateFriendlyName>";  
        string subscriptionid = "<AzureSubscriptionID>";
        string clustername = "<HDInsightClusterName>";
        string location = "<MicrosoftDataCenter>";
        string storageaccountname = "<AzureStorageAccountName>.blob.core.windows.net";
        string storageaccountkey = "<AzureStorageAccountKey>";
        string containername = "<HDInsightDefaultContainerName>";
        string username = "<HDInsightUsername>";
        string password = "<HDInsightUserPassword>";
        int clustersize = <NumberOfNodesInTheCluster>;

        // Get the certificate object from certificate store by using the friendly name to identify it
        X509Store store = new X509Store();
        store.Open(OpenFlags.ReadOnly);
        X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>().First(item => item.Thumbprint == thumbprint);

        // Create the Storage account if it doesn't exist

        // Create the container if it doesn't exist.

		// Create an HDInsightClient object
        HDInsightCertificateCredential creds = new HDInsightCertificateCredential(new Guid(subscriptionid), cert);
        var client = HDInsightClient.Connect(creds);

		// Supply the cluster information
        ClusterCreateParametersV2 clusterInfo = new ClusterCreateParametersV2()
		{
		    Name = clustername,
		    Location = location,
		    DefaultStorageAccountName = storageaccountname,
		    DefaultStorageAccountKey = storageaccountkey,
		    DefaultStorageContainer = containername,
		    UserName = username,
		    Password = password,
		    ClusterSizeInNodes = clustersize,
		    Version = "3.2",
		    OSType = OSType.Windows,
			ClusterType = ClusterType.Spark		    
		};


		// Create the cluster
        Console.WriteLine("Creating the HDInsight cluster ...");

        ClusterDetails cluster = client.CreateCluster(clusterInfo);

        Console.WriteLine("Created cluster: {0}.", cluster.ConnectionUrl);
        Console.WriteLine("Press ENTER to continue ...");
        Console.ReadKey();

10. main() 関数の先頭にある変数を置き換えます。

**アプリケーションを実行するには**

アプリケーションを Visual Studio で開いている間に、**F5** キーを押してアプリケーションを実行します。コンソール ウィンドウが開き、アプリケーションの状態が表示されます。HDInsight クラスターの作成は数分かかる場合があります。


##<a id="nextsteps"></a>次のステップ

* HDInsight での Apache Spark と Power BI や Tableau などの BI ツールを使用する方法については、「[HDInsight と BI ツールで Spark を使用した対話型データ分析の実行](hdinsight-apache-spark-use-bi-tools.md)」をご覧ください。 
* HDInsight の Apache Spark を使用して機械学習アプリケーションを作成する方法については、「[機械学習アプリケーションを作成するための HDInsight での Spark の使用](hdinsight-apache-spark-ipython-notebook-machine-learning.md)」をご覧ください。
* HDInsight の Apache Spark を使用してストリーミング アプリケーションを作成する方法については、「[リアルタイム ストリーミング アプリケーションを作成するための HDInsight での Spark の使用](hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming.md)」をご覧ください。
* リソース マネージャーを使用して Spark クラスターに割り当てられたリソースを管理する方法については、「[Azure HDInsight での Apache Spark クラスターのリソースの管理](hdinsight-apache-spark-resource-manager.md)」をご覧ください。


[hdinsight-sdk-documentation]: http://msdn.microsoft.com/library/dn479185.aspx
[hdinsight-hbase-custom-provision]: http://azure.microsoft.com/documentation/articles/hdinsight-hbase-get-started/

[hdinsight-customize-cluster]: ../hdinsight-hadoop-customize-cluster/
[hdinsight-get-started]: ../hdinsight-get-started/

[hdinsight-admin-powershell]: ../hdinsight-administer-use-powershell/
[hdinsight-admin-portal]: ../hdinsight-administer-use-management-portal/
[hadoop-hdinsight-intro]: ../hdinsight-hadoop-introduction/
[hdinsight-submit-jobs]: ../hdinsight-submit-hadoop-jobs-programmatically/
[hdinsight-powershell-reference]: https://msdn.microsoft.com/library/dn858087.aspx
[hdinsight-storm-get-started]: ../hdinsight-storm-getting-started/

[azure-management-portal]: https://manage.windowsazure.com/

[azure-command-line-tools]: ../xplat-cli/

[azure-create-storageaccount]: ../storage-create-storage-account/

[apache-hadoop]: http://go.microsoft.com/fwlink/?LinkId=510084
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[hdi-remote]: http://azure.microsoft.com/documentation/articles/hdinsight-administer-use-management-portal/#rdp


[powershell-install-configure]: ../install-configure-powershell/




[89e2276a]: /documentation/articles/hdinsight-use-sqoop/ "HDInsight での Sqoop の使用"

<!---HONumber=August15_HO7-->