<properties linkid="manage-services-hdinsight-provision-hdinsight-clusters" urlDisplayName="HDInsight の管理" pageTitle="HDInsight クラスターのプロビジョニング | Azure" metaKeywords="hdinsight, hdinsight の管理, hdinsight の管理と azure" description="管理ポータル、PowerShell、コマンド ラインを使用して Azure HDInsight のクラスターをプロビジョニングする方法について説明します。" umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" services="hdinsight" documentationCenter="" title="HDInsight クラスターのプロビジョニング" authors="jgao" />

#HDInsight クラスターのプロビジョニング

この記事では、HDInsight クラスターをプロビジョニングするさまざまな方法を説明します。

**前提条件:**

この記事を読み始める前に、次の項目を用意する必要があります。

- Azure サブスクリプションが必要です。Azure はサブスクリプション方式のプラットフォームです。HDInsight PowerShell コマンドレットはサブスクリプションを使ってタスクを実行します。サブスクリプションの入手方法の詳細については、[購入オプション][azure-purchase-options]、[メンバー プラン][azure-member-offers]、または[無料評価版][azure-free-trial]に関するページを参照してください。

##この記事の内容

* [Azure 管理ポータルの使用](#portal)
* [Azure PowerShell の使用](#powershell)
* [クロスプラットフォーム コマンド ラインの使用](#cli)
* [HDInsight .NET SDK の使用](#sdk)
* [次のステップ](#nextsteps)

##<a id="portal"></a> Azure 管理ポータルの使用

HDInsight クラスターは、既定のファイル システムとして Azure BLOB ストレージ コンテナーを使用します。HDInsight クラスターを作成するには、同じデータ センターにある Azure ストレージ アカウントが必要です。詳細については、「[HDInsight での Azure BLOB ストレージの使用][hdinsight-storage]」を参照してください。Azure ストレージ アカウントの作成の詳細については、「[ストレージ アカウントの作成方法][azure-create-storageaccount]」を参照してください。


> [WACOM.NOTE] 現在、HDInsight クラスターをホストできるリージョンは、東南アジア、北ヨーロッパ、西ヨーロッパ、米国東部、米国西部のみです。

ここでは、[カスタム作成] オプションを使用して HDInsight クラスターを作成する手順について説明します。[簡易作成] オプションの使用法については、「[Azure HDInsight の概要][hdinsight-getting-started]」を参照してください。


**カスタム作成オプションを使用して HDInsight クラスターを作成するには**

1. [Azure 管理ポータル][azure-management-portal]にサインインします。
2. ページ下部の **[+ 新規]** をクリックし、**[データ サービス]**、**[HDINSIGHT]**、**[カスタム作成]** の順にクリックします。
3. [クラスターの詳細] ページで、次の値を入力または選択します。

	<table border='1'>
		<tr><th>プロパティ</th><th>値</th></tr>
		<tr><td>クラスター名</td>
			<td><p>クラスターの名前を指定します。</p>
				<ul>
				<li>DNS 名の最初と最後の文字は英数字にする必要があります。それ以外ではダッシュ (-) を使用できます。</li>
				<li>3 ～ 63 文字のクラスター名を入力できます。</li>
				</ul></td></tr>
		<tr><td>データ ノード</td>
			<td>クラスター内のノード数を指定します。既定値は 4 です。</td></tr>
		<tr><td>HDInsight のバージョン</td>
			<td>バージョンを選択します。既定値は、Hadoop 1.2 クラスターを実行する 2.0 です。3.0 は Hadoop 2.2 クラスターを使用します。詳細については、「<a href="http://www.windowsazure.com/ja-jp/manage/services/hdinsight/versioning-in-hdinsight/">What version of Hadoop is in Azure HDInsight? (Azure HDInsight でサポートされている Hadoop のバージョン)</a>」を参照してください。</td></tr>
		<tr><td>リージョン</td>
			<td>クラスターをインストールするデータ センターを指定します。既定のファイル システムとして使用する Azure BLOB ストレージと同じ場所にする必要があります。現在選択できるのは、*東南アジア*、*北ヨーロッパ*、*西ヨーロッパ*、*米国東部* または *米国西部*のみです。</td>
		</tr>
	</table>

	![HDI.CustomProvision.Page1][image-customprovision-page1]

4. ページの右下隅にある右矢印をクリックします。
5. [クラスター ユーザーの構成] ページで、次の値を入力または選択します。

	<table border='1'>
		<tr><th>プロパティ</th><th>値</th></tr>
		<tr><td>ユーザー名</td>
			<td>HDInsight クラスターのユーザー名を指定します。</td></tr>
		<tr><td><p>パスワード</p><p>パスワードの確認</p></td>
			<td>HDInsight クラスターのユーザー パスワードを指定します。</td></tr>
		<tr><td>Hive/Oozie メタストアの入力</td>
			<td>同じデータ センターにある Hive/Oozie メタストアとして使用する SQL データベースを指定します。</td></tr>
		<tr><td>Hive Meta/Ooziestore データベース</td>
			<td>Hive/OOzie のメタストアとして使用する Azure SQL データベースを指定します。この SQL データベースは、HDInsight クラスターと同じデータ センターにある必要があります。ボックスの一覧には、[クラスターの詳細] ページで指定したデータ センターにある SQL データベースしか表示されません。</td></tr>
		<tr><td>データベース ユーザー</td>
			<td>データベースへの接続時に使用する SQL データベース ユーザーを指定します。</td></tr>
		<tr><td>データベース ユーザー パスワード</td>
			<td>SQL データベース ユーザーのパスワードを指定します。</td></tr>
	</table>

	Version 2.0 の HDInsight クラスターの場合、ここで指定した資格情報でアクセスできるのはクラスター上のサービスのみです。リモート デスクトップは、クラスターの作成後、有効にできます。

	![HDI.CustomProvision.Page2][image-customprovision-page2]


6. ページの右下隅にある右矢印をクリックします。
7. [ストレージ アカウント] ページで、次の値を入力または選択します。

	<table border='1'>
		<tr><th>プロパティ</th><th>値</th></tr>
		<tr><td>ストレージ アカウント</td>
			<td>HDInsight クラスターの既定のファイル システムとして使用する Azure ストレージ アカウントを指定します。次の 3 つのオプションのいずれかを選択できます。
			<ul>
				<li>既存のストレージを使用する</li>
				<li>新しいストレージを作成する</li>
				<li>別のサブスクリプションのストレージを使用する</li>
			</ul>
			</td></tr>
		<tr><td>アカウント名</td>
			<td>[<b>既存のストレージを使用する</b>] を選択すると、ボックスの一覧には、同じデータ センターにあるストレージ アカウントしか表示されません。</td></tr>
		<tr><td>アカウント キー</td>
			<td>このフィールドは、[ストレージ アカウント] で <strong>[別のサブスクリプションのストレージを使用する]</strong> を選択した場合にだけ使用できます。</td></tr>	
		<tr><td>既定のコンテナー</td>
			<td>ストレージ アカウントの既定のコンテナーは、HDInsight クラスターの既定のファイル システムとして使用されます。[ストレージ アカウント] で <strong>[既存のストレージを使用する]</strong> を選択し、[既定のコンテナー] で <strong>[既定のコンテナーの作成]</strong> を選択した場合、既定のコンテナー名はクラスターと同じ名前になります。クラスター名と同じ名前のコンテナーが既に存在する場合は、コンテナー名に連番が付加されます。たとえば、mycontainer1、mycontainer2、などとなります。</td></tr>
		<tr><td>追加のストレージ アカウント</td>
			<td>HDInsight は複数のストレージ アカウントをサポートします。クラスターで使用できる追加ストレージ アカウントの数に制限はありません。ただし、管理ポータルを使用してクラスターを作成する場合は、UI の制約により 7 が上限になります。ここで指定した追加のストレージ アカウントごとに [ストレージ アカウント] ページが追加され、そこでアカウント情報を指定できます。たとえば、次のスクリーンショットでは、追加のストレージ アカウントが 1 つ選択され、ページ 4 がダイアログに追加されています。</td></tr>		
	</table>

	![HDI.CustomProvision.Page3][image-customprovision-page3]

8. ページの右下隅にある右矢印をクリックします。
9. [ストレージ アカウント] ページで、追加したストレージ アカウントのアカウント情報を入力します。

	![HDI.CustomProvision.Page4][image-customprovision-page4]

10. 右下隅にある [完了] ボタン (チェック マーク アイコン) をクリックすると、HDInsight クラスターのプロビジョニング処理が開始されます。

クラスターのプロビジョニングは数分かかる場合があります。プロビジョニング処理が正常に完了すると、クラスターの [状態] 列に **[実行中]** と表示されます。

> [WACOM.NOTE] HDInsight クラスター用の Azure ストレージ アカウントを選択した後は、そのアカウントを削除することも、別のアカウントに変更することもできません。













































##<a id="powershell"></a> Azure PowerShell の使用
Azure PowerShell は、Azure のワークロードの展開と管理を制御し自動化するために使用できる強力なスクリプティング環境です。コンピューターを構成して HDInsight Powershell コマンドレットを実行する方法については、「[Azure PowerShell のインストールおよび構成][powershell-install-configure]」を参照してください。HDInsight で PowerShell を使用する方法の詳細については、「[PowerShell を使用した HDInsight の管理][hdinsight-admin-powershell]」を参照してください。HDInsight PowerShell コマンドレットの一覧については、「[HDInsight コマンドレット リファレンス][hdinsight-powershell-reference]」を参照してください。

PowerShell を使用して HDInsight クラスターをプロビジョニングするには、以下の手順が必要です。

- Azure ストレージ アカウントの作成
- Azure BLOB コンテナーの作成
- HDInsight クラスターの作成

HDInsight は、既定のファイル システムとして Azure BLOB ストレージ コンテナーを使用します。HDInsight クラスターを作成するには Azure ストレージ アカウントとストレージ コンテナーが必要です。ストレージ アカウントは、HDInsight クラスターと同じデータ センターに置く必要があります。


**Azure ストレージ アカウントを作成するには**

- 次のコマンドを Azure PowerShell コンソール ウィンドウから実行します。

		$storageAccountName = "<StorageAcccountName>"
		$location = "<MicrosoftDataCenter>"		# For example, "West US"
		
		# Create an Azure storage account
		New-AzureStorageAccount -StorageAccountName $storageAccountName -Location $location
	
	既にストレージ アカウントを持っていて、アカウント名とアカウント キーがわからない場合は、次の PowerShell コマンドを使ってその情報を取得できます。
	
		# List storage accounts for the current subscription
		Get-AzureStorageAccount

		# List the keys for a storage account
		Get-AzureStorageKey "<StorageAccountName>"
	
**Azure ストレージ コンテナーを作成するには**

- 次のコマンドを Azure PowerShell ウィンドウから実行します。

		$storageAccountName = "<StorageAccountName>"
		$storageAccountKey = "<StorageAccountKey>"
		$containerName="<ContainerName>"

		# Create a storage context object
		$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName 
		                                       -StorageAccountKey $storageAccountKey  
		 
		# Create a Blob storage container
		New-AzureStorageContainer -Name $containerName -Context $destContext

ストレージ アカウントを用意して、BLOB コンテナーを準備したら、クラスターを作成する準備は整いました。

**HDInsight クラスターをプロビジョニングするには**

- 次のコマンドを Azure PowerShell ウィンドウから実行します。		

		$subscriptionName = "<SubscriptionName>"		# The name of the Azure subscription.
		$storageAccountName = "<StorageAccountName>"	# The Azure storage account that hosts the default container. The default container will be used as the default file system.
		$containerName = "<ContainerName>"				# The Azure Blob storage container that will be used as the default file system for the HDInsight cluster.

		$clusterName = "<HDInsightClusterName>"			# The name you will name your HDInsight cluster.
		$location = "<MicrosoftDataCenter>"				# The location of the HDInsight cluster. It must in the same data center as the storage account.
		$clusterNodes = <ClusterSizeInNodes>			# The number of nodes in the HDInsight cluster.

		# Get the storage primary key based on the account name
		Select-AzureSubscription $subscriptionName
		$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }

		# Create a new HDInsight cluster
		New-AzureHDInsightCluster -Name $clusterName -Location $location -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainerName $containerName  -ClusterSizeInNodes $clusterNodes

	クラスターのプロビジョニングが完了するまでに数分かかる場合があります。

	![HDI.CLI.Provision][image-hdi-ps-provision]

クラスターをプロビジョニングして、複数の Azure BLOB ストレージまたはカスタム Hive/Oozie メタストアに接続するように構成することもできます。この高度な機能を利用すると、データとメタデータの寿命を、クラスターの寿命と切り離すことができます。

**構成を使用して HDInsight クラスターをプロビジョニングするには**

- 次のコマンドを Windows PowerShell ウィンドウから実行します。

		$subscriptionName = "<SubscriptionName>"
		$clusterName = "<ClusterName>"
		$location = "<MicrosoftDataCenter>"
		$clusterNodes = <ClusterSizeInNodes>
		
		$storageAccountName_Default = "<DefaultFileSystemStorageAccountName>"
		$containerName_Default = "<DefaultFileSystemContainerName>"
		
		$storageAccountName_Add1 = "<AdditionalStorageAccountName>"
		
		$hiveSQLDatabaseServerName = "<SQLDatabaseServerNameForHiveMetastore>"
		$hiveSQLDatabaseName = "<SQLDatabaseDatabaseNameForHiveMetastore>"
		$oozieSQLDatabaseServerName = "<SQLDatabaseServerNameForOozieMetastore>"
		$oozieSQLDatabaseName = "<SQLDatabaseDatabaseNameForOozieMetastore>"
		
		# Get the storage account keys
		Select-AzureSubscription $subscriptionName
		$storageAccountKey_Default = Get-AzureStorageKey $storageAccountName_Default | %{ $_.Primary }
		$storageAccountKey_Add1 = Get-AzureStorageKey $storageAccountName_Add1 | %{ $_.Primary }
		
		$oozieCreds = Get-Credential -Message "Oozie metastore"
		$hiveCreds = Get-Credential -Message "Hive metastore"
		
		# Create a Blob storage container
		#$dest1Context = New-AzureStorageContext -StorageAccountName $storageAccountName_Default -StorageAccountKey $storageAccountKey_Default  
		#New-AzureStorageContainer -Name $containerName_Default -Context $dest1Context
		
		# Create a new HDInsight cluster
		$config = New-AzureHDInsightClusterConfig -ClusterSizeInNodes $clusterNodes |
		    Set-AzureHDInsightDefaultStorage -StorageAccountName "$storageAccountName_Default.blob.core.windows.net" -StorageAccountKey $storageAccountKey_Default -StorageContainerName $containerName_Default |
		    Add-AzureHDInsightStorage -StorageAccountName "$storageAccountName_Add1.blob.core.windows.net" -StorageAccountKey $storageAccountKey_Add1 |
		    Add-AzureHDInsightMetastore -SqlAzureServerName "$hiveSQLDatabaseServerName.database.windows.net" -DatabaseName $hiveSQLDatabaseName -Credential $hiveCreds -MetastoreType HiveMetastore |
		    Add-AzureHDInsightMetastore -SqlAzureServerName "$oozieSQLDatabaseServerName.database.windows.net" -DatabaseName $oozieSQLDatabaseName -Credential $oozieCreds -MetastoreType OozieMetastore |
		        New-AzureHDInsightCluster -Name $clusterName -Location $location

**HDInsight クラスターの一覧を表示するには**

- 次のコマンドを Azure PowerShell ウィンドウから実行します。

		Get-AzureHDInsightCluster -Name <ClusterName>











































































































##<a id="cli"></a>クロスプラットフォーム コマンド ラインの使用

HDInsight クラスターをプロビジョニングするもう 1 つの方法は、クロスプラットフォーム コマンド ライン インターフェイスです。コマンド ライン ツールは Node.js で実装されます。Windows、Mac、Linux など、Node.js をサポートするいずれのプラットフォームでも使用できます。コマンド ライン ツールはオープン ソースです。ソース コードは GitHub (<a href= "https://github.com/WindowsAzure/azure-sdk-tools-xplat">https://github.com/WindowsAzure/azure-sdk-tools-xplat</a>) で管理されています。コマンド ライン インターフェイスの使用方法の一般的ガイドについては、「[Mac および Linux 用 Azure コマンド ライン ツールの使用方法][azure-command-line-tools]」を参照してください。包括的なリファレンス ドキュメントについては、「[Mac および Linux 用 Azure コマンド ライン ツール][azure-command-line-tool]」を参照してください。この記事では、Windows からコマンド ライン インターフェイスを使用する方法だけを取り上げます。


クロスプラットフォーム コマンド ラインを使用して HDInsight クラスターをプロビジョニングするには、以下の手順が必要です。

- クロスプラットフォーム コマンド ラインのインストール
- Azure アカウントの発行設定のダウンロードとインポート
- Azure ストレージ アカウントの作成
- クラスターのプロビジョニング

コマンド ライン インターフェイスは *Node.js パッケージ マネージャー (NPM)* または Windows インストーラーを使用してインストールできます。

**NPM を使用してコマンド ライン インターフェイスをインストールするには**

1.	ブラウザーで **www.nodejs.org** を開きます。
2.	**[INSTALL]** をクリックし、指示に従います。設定は既定の設定を使います。
3.	コンピューターから**コマンド プロンプト** (または *Azure コマンド プロンプト*、または *VS2012 の開発者コマンド プロンプト*) を開きます。
4.	コマンド プロンプト ウィンドウで次のコマンドを実行します。

		npm install -g azure-cli

	> [WACOM.NOTE] NPM コマンドが見つからないというエラーが表示される場合、次のパスが PATH 環境変数の中にあることを確認します: <i>C:\Program Files (x86)\nodejs;C:\Users\[ユーザー名]\AppData\Roaming\npm</i> または <i>C:\Program Files\nodejs;C:\Users\[ユーザー名]\AppData\Roaming\npm</i>
	


5.	次のコマンドを実行してインストールを確認します。

		azure hdinsight -h

	*-h* スイッチを使うと、さまざまなレベルでヘルプ情報を表示できます。次に例を示します。
		
		azure -h
		azure hdinsight -h
		azure hdinsight cluster -h
		azure hdinsight cluster create -h

**Windows インストーラーを使用してコマンド ライン インターフェイスをインストールするには**

1.	ブラウザーで **http://www.windowsazure.com/ja-jp/downloads/** を開きます。
2.	下へスクロールして、**[コマンド ライン ツール]** セクションの **[クロスプラットフォーム コマンド ライン インターフェイス]** をクリックし、Web プラットフォーム インストーラー ウィザードの指示に従います。

コマンド ライン インターフェイスを使用する前に、自分のコンピューターと Azure との接続を構成する必要があります。Azure のサブスクリプション情報は、コマンド ライン インターフェイスがアカウントにアクセスする際に使用されます。この情報は、Azure から発行設定ファイルとして入手できます。発行設定ファイルは永続的なローカル構成設定としてインポートすることができます。インポートすると、コマンド ライン インターフェイスの以降の操作にはこのファイルが使用されます。発行設定のインポートは 1 回だけ行う必要があります。


> [WACOM.NOTE] 発行設定ファイルには、機密情報が含まれます。このファイルを削除するか、追加の作業を行ってファイルのある user フォルダーを暗号化することをお勧めします。Windows の場合、フォルダー プロパティを変更するか、または BitLocker を使用します。


**発行設定をダウンロードしてインポートするには**

1.	**コマンド プロンプト**を開きます。
2.	次のコマンドを実行して、発行設定ファイルをダウンロードします。

		azure account download
 
	![HDI.CLIAccountDownloadImport][image-cli-account-download-import]

	URL も含めて、ファイルのダウンロード方法が表示されます。

3.	**Internet Explorer** を開き、コマンド プロンプト ウィンドウに表示された URL にアクセスします。
4.	**[保存]** をクリックして、ファイルをコンピューターに保存します。
5.	コマンド プロンプト ウィンドウで次のコマンドを実行して、発行設定ファイルをインポートします。

		azure account import <file>

	先のスクリーンショットで、発行設定ファイルはコンピューターの C:\HDInsight フォルダーに保存されています。


HDInsight は、既定のファイル システムとして Azure BLOB ストレージ コンテナーを使用します。HDInsight クラスターを作成するには Azure ストレージ アカウントが必要です。ストレージ アカウントは、クラスターと同じデータ センターに配置する必要があります。

**Azure ストレージ アカウントを作成するには**

- コマンド プロンプト ウィンドウで次のコマンドを実行します。

		azure account storage create [options] <StorageAccountName>

Azure 管理ポータルを使った Azure ストレージ アカウントの作成については、「[ストレージ アカウントの作成方法][azure-create-storageaccount]」を参照してください。

既にストレージ アカウントを持っていて、アカウント名とアカウント キーがわからない場合は、次のコマンドを使ってその情報を取得できます。

	-- lists storage accounts
	azure account storage list
	-- Shows a storage account
	azure account storage show <StorageAccountName>
	-- Lists the keys for a storage account
	azure account storage keys list <StorageAccountName>

管理ポータルを使用して情報を取得する方法の詳細については、「[ストレージ アカウントの管理方法][azure-manage-storageaccount]」の「*方法: ストレージ アクセス キーの表示、コピーおよび再生成*」を参照してください。

*azure hdinsight cluster create* コマンドは、コンテナーが存在しない場合、コンテナーを作成します。コンテナーを事前に作成する場合は、次のコマンドを使用できます。

	azure storage container create --account-name <StorageAccountName> --account-key <StorageAccountKey> [ContainerName]
		
ストレージ アカウントを用意して、BLOB コンテナーを準備したら、クラスターを作成する準備は整いました。

**HDInsight クラスターを作成するには**

- コマンド プロンプト ウィンドウで次のコマンドを実行します。

		azure hdinsight cluster create --clusterName <ClusterName> --storageAccountName <StorageAccountName> --storageAccountKey <storageAccountKey> --storageContainer <StorageContainer> --nodes <NumberOfNodes> --location <DataCenterLocation> --username <HDInsightClusterUsername> --clusterPassword <HDInsightClusterPassword>

	![HDI.CLIClusterCreation][image-cli-clustercreation]

















通常は、HDInsight クラスターをプロビジョニングして、ジョブを実行した後、クラスターを削除してコストを削減します。コマンド ライン インターフェイスを使うと、構成をファイルに保存して、クラスターをプロビジョニングするたびにその構成を再利用することができます。

**構成ファイルを使用して HDInsight クラスターをプロビジョニングするには**

- コマンド プロンプト ウィンドウで次のコマンドを実行します。
 
		azure hdinsight cluster config create <file>
		 
		azure hdinsight cluster config set <file> --clusterName <ClusterName> --nodes <NumberOfNodes> --location "<DataCenterLocation>" --storageAccountName "<StorageAccountName>.blob.core.windows.net" --storageAccountKey "<StorageAccountKey>" --storageContainer "<BlobContainerName>" --username "<Username>" --clusterPassword "<UserPassword>"
		 
		azure hdinsight cluster config storage add <file> --storageAccountName "<StorageAccountName>.blob.core.windows.net"
		       --storageAccountKey "<StorageAccountKey>"
		 
		azure hdinsight cluster config metastore set <file> --type "hive" --server "<SQLDatabaseName>.database.windows.net"
		       --database "<HiveDatabaseName>" --user "<Username>" --metastorePassword "<UserPassword>"
		 
		azure hdinsight cluster config metastore set <file> --type "oozie" --server "<SQLDatabaseName>.database.windows.net"
		       --database "<OozieDatabaseName>" --user "<SQLUsername>" --metastorePassword "<SQLPassword>"
		 
		azure hdinsight cluster create --config <file>
		 


	![HDI.CLIClusterCreationConfig][image-cli-clustercreation-config]


**クラスターの一覧と詳細を表示するには**

- クラスターの一覧と詳細を表示するには、次のコマンドを使用します。

		azure hdinsight cluster list
		azure hdinsight cluster show <ClusterName>
	
	![HDI.CLIListCluster][image-cli-clusterlisting]


**クラスターを削除するには**

- クラスターを削除するには、次のコマンドを使用します。

		azure hdinsight cluster delete <ClusterName>



##<a id="sdk"></a> HDInsight .NET SDK の使用
HDInsight .NET SDK は、.NET から HDInsight を簡単に操作できる .NET クライアント ライブラリを提供します。

SDK を使用して HDInsight クラスターをプロビジョニングするには、以下の手順が必要です。

- HDInsight .NET SDK のインストール
- コンソール アプリケーションの作成
- アプリケーションの実行


**HDInsight .NET SDK をインストールするには**
公開されている最新の SDK を [NuGet](http://nuget.codeplex.com/wikipage?title=Getting%20Started) からインストールできます。次の手順で、具体的な方法を説明します。

**Visual Studio コンソール アプリケーションを作成するには**

1. Visual Studio 2012 を開きます。

2. [ファイル] メニューの **[新規作成]** をクリックし、**[プロジェクト]** をクリックします。

3. [新しいプロジェクト] で、次の値を入力または選択します。

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


5. **[ツール]** メニューで **[ライブラリ パッケージ マネージャー]**、**[パッケージ マネージャー コンソール]** の順にクリックします。

6. コンソールで次のコマンドを実行して、パッケージをインストールします。

		Install-Package Microsoft.WindowsAzure.Management.HDInsight


	このコマンドは、.NET ライブラリおよび .NET ライブラリへの参照を現在の Visual Studio プロジェクトに追加します。

7. ソリューション エクスプローラーで **Program.cs** をダブルクリックして、このファイルを開きます。

8. 次の using ステートメントをファイルの先頭に追加します。

		using System.Security.Cryptography.X509Certificates;
		using Microsoft.WindowsAzure.Management.HDInsight;
		using Microsoft.WindowsAzure.Management.HDInsight.ClusterProvisioning;

	
9. Main() 関数で、次のコードをコピーしてファイルに貼り付けます。
		
        string certfriendlyname = "<CertificateFriendlyName>";
        string subscriptionid = "<WindowsAzureSubscriptionID>";
        string clustername = "<HDInsightClusterName>";
        string location = "<MicrosoftDataCenter>";
        string storageaccountname = "<WindowsAzureStorageAccountName>";     // Full path must be used
        string storageaccountkey = "<WindowsAzureStorageAccountKey>";
        string containername = "<HDInsightDefaultContainerName>";
        string username = "<HDInsightUsername>";
        string password = "<HDInsightUserPassword>";
        int clustersize = <NumberOfNodesInTheCluster>;

        // Get the certificate object from certificate store using the friendly name to identify it
        X509Store store = new X509Store();
        store.Open(OpenFlags.ReadOnly);
        X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>().First(item => item.FriendlyName == certfriendlyname);

        // Create the storage account if it doesn't exist.

        // Create the container if it doesn't exist.

		// Create an HDInsightClient object
        HDInsightCertificateCredential creds = new HDInsightCertificateCredential(new Guid(subscriptionid), cert);
        var client = HDInsightClient.Connect(creds);

		// Supply th cluster information
        ClusterCreateParameters clusterInfo = new ClusterCreateParameters()
        {
            Name = clustername,
            Location = location,
            DefaultStorageAccountName = storageaccountname,
            DefaultStorageAccountKey = storageaccountkey,
            DefaultStorageContainer = containername,
            UserName = username,
            Password = password,
            ClusterSizeInNodes = clustersize
        };

		// Create the cluster
        Console.WriteLine("Creating the HDInsight cluster ...");

        ClusterDetails cluster = client.CreateCluster(clusterInfo);

        Console.WriteLine("Created cluster: {0}.", cluster.ConnectionUrl);
        Console.WriteLine("Press ENTER to continue.");
        Console.ReadKey();

10. main() 関数の先頭にある変数を置き換えます。

**アプリケーションを実行するには**

アプリケーションを Visual Studio で開いている間に、**F5** キーを押してアプリケーションを実行します。コンソール ウィンドウが開き、アプリケーションの状態が表示されます。HDInsight クラスターの作成は数分かかる場合があります。



##<a id="nextsteps"></a> 次のステップ
この記事では、HDInsight クラスターをプロビジョニングする方法をいくつか説明しました。詳細については、次の記事を参照してください。

* [Azure HDInsight の概要][hdinsight-getting-started]
* [PowerShell を使用した HDInsight の管理][hdinsight-admin-powershell]
* [プログラムによる Hadoop ジョブの送信][hdinsight-submit-jobs]
* [Azure HDInsight SDK のドキュメント][hdinsight-sdk-documentation]

[hdinsight-version]: /ja-jp/manage/services/hdinsight/versioning-in-hdinsight/
[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/ja-jp/library/dn479185.aspx
[hdinsight-getting-started]: /ja-jp/manage/services/hdinsight/get-started-hdinsight/
[hdinsight-storage]: /ja-jp/manage/services/hdinsight/howto-blob-store/
[hdinsight-admin-powershell]: /ja-jp/manage/services/hdinsight/administer-hdinsight-using-powershell/
[hdinsight-submit-jobs]: /ja-jp/manage/services/hdinsight/submit-hadoop-jobs-programmatically/
[hdinsight-configure-powershell]: /ja-jp/manage/services/hdinsight/install-and-configure-powershell-for-hdinsight/ 
[hdinsight-powershell-reference]: http://msdn.microsoft.com/ja-jp/library/windowsazure/dn479228.aspx

[azure-create-storageaccount]: /ja-jp/manage/services/storage/how-to-create-a-storage-account/ 
[azure-management-portal]: https://manage.windowsazure.com/
[azure-command-line-tools]: /ja-jp/develop/nodejs/how-to-guides/command-line-tools/
[azure-command-line-tool]: /ja-jp/manage/linux/other-resources/command-line-tools/
[azure-manage-storageaccount]: /ja-jp/manage/services/storage/how-to-manage-a-storage-account/
[azure-purchase-options]: https://www.windowsazure.com/ja-jp/pricing/purchase-options/
[azure-member-offers]: https://www.windowsazure.com/ja-jp/pricing/member-offers/
[azure-free-trial]: https://www.windowsazure.com/ja-jp/pricing/free-trial/

[Powershell-install-configure]: /ja-jp/documentation/articles/install-configure-powershell/


[image-customprovision-page1]: ./media/hdinsight-provision-clusters/HDI.CustomProvision.Page1.png 
[image-customprovision-page2]: ./media/hdinsight-provision-clusters/HDI.CustomProvision.Page2.png 
[image-customprovision-page3]: ./media/hdinsight-provision-clusters/HDI.CustomProvision.Page3.png 
[image-customprovision-page4]: ./media/hdinsight-provision-clusters/HDI.CustomProvision.Page4.png 

[image-cli-account-download-import]: ./media/hdinsight-provision-clusters/HDI.CLIAccountDownloadImport.png
[image-cli-clustercreation]: ./media/hdinsight-provision-clusters/HDI.CLIClusterCreation.png
[image-cli-clustercreation-config]: ./media/hdinsight-provision-clusters/HDI.CLIClusterCreationConfig.png
[image-cli-clusterlisting]: ./media/hdinsight-provision-clusters/HDI.CLIListClusters.png "List and show clusters"

[image-hdi-ps-provision]: ./media/hdinsight-provision-clusters/HDI.ps.provision.png


