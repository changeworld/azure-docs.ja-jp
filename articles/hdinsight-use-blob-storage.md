<properties urlDisplayName="Blob Storage with  Hadoop in HDInsight" pageTitle="HDInsight の Hadoop での BLOB ストレージの使用 | Azure" metaKeywords="" description="Learn how HDInsight uses Blob storage as the underlying data store for HDFS and how you can query data from the store." metaCanonical="" services="storage,hdinsight" documentationCenter="" title="Use Azure Blob storage with Hadoop in HDInsight" authors="jgao" solutions="" manager="paulettm" editor="mollybos" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jgao" />




#HDInsight の Hadoop での Azure BLOB ストレージの使用




Azure の HDInsight は、データ格納用として Hadoop 分散ファイル システム (HDFS) と Azure BLOB ストレージの両方をサポートしています。BLOB ストレージは、信頼性に優れた汎用的な Azure ストレージ ソリューションです。BLOB ストレージは高度な HDFS インターフェイスを備えているので、データに対して Hadoop エコシステムのすべてのコンポーネントを直接実行できます (既定の設定)。ユーザーは、データがどこに保存されているかを意識する必要がありません。BLOB ストレージは単なる低コストのソリューションではありません。BLOB ストレージにデータを格納した場合、計算で使用する HDInsight クラスターを削除してもユーザー データは失われません。 

> [WACOM.NOTE]asv://** 構文は HDInsight クラスター バージョン 3.0 でサポートされていません。また、それ以降のバージョンでもサポートされません。つまり、"asv://" 構文を明示的に使用するジョブを HDInsight クラスター Version 3.0 に送信すると、そのジョブは失敗します。代わりに、*wasb://* 構文を使用する必要があります。また、既存のメタストアにより、asv:// 構文を使用してリソースへの明示的な参照を含むジョブを作成し、HDInsight クラスター バージョン 3.0 に送信すると、そのジョブは失敗します。これらのメタストアは、wasb:// を使用してリソースをアドレス指定するように再作成する必要があります。

> [WACOM.NOTE] HDInsight では現在、ブロック BLOB のみがサポートされています。

> [WACOM.NOTE]
> ほとんどの HDFS コマンド (<b>ls</b>、<b>copyFromLocal</b>、<b>mkdir</b> など) は通常と同じように機能します。ただし、<b>fschk</b> や <b>dfsadmin</b> など、HDFS ネイティブ実装 (DFS) に固有のコマンドについては、Azure BLOB ストレージ上で実行した場合に動作が異なります。

HDInsight クラスターのプロビジョニングについては、「[HDInsight で Hadoop 2.4 を使用する][hdinsight-get-started]」または「[カスタム オプションを使用した HDInsight での Hadoop クラスターのプロビジョニング][hdinsight-provision]」を参照してください。

##この記事の内容

* [HDInsight のストレージ アーキテクチャ](#architecture)
* [Azure BLOB ストレージの利点](#benefits)
* [BLOB ストレージのコンテナーの準備](#preparingblobstorage)
* [BLOB ストレージ内のファイルの指定](#addressing)
* [PowerShell を使用して BLOB にアクセス](#powershell)
* [次のステップ](#nextsteps)

##<a id="architecture"></a>HDInsight のストレージ アーキテクチャ
次の図は、HDInsight のストレージ アーキテクチャを示しています。

![HDI.ASVArch](./media/hdinsight-use-blob-storage/HDI.ASVArch.png "HDInsight Storage Architecture")
  
HDInsight では、それぞれの計算ノードにローカルに割り当てられている分散ファイル システムにアクセスします。このファイル システムには、完全修飾 URI を使用してアクセスできます。次に例を示します。 

	hdfs://<namenodehost>/<path>

さらに、HDInsight では、BLOB ストレージに格納されたデータにアクセスすることもできます。BLOB ストレージにアクセスするための構文は次のとおりです。

	wasb[s]://<containername>@<accountname>.blob.core.windows.net/<path>


Hadoop は、既定のファイル システムの概念をサポートしています。既定のファイル システムとは "既定のスキームとオーソリティ" を意味しており、これらを使用して相対パスを解決することもできます。HDInsight のプロビジョニング プロセスを実行するときに、Azure Storage アカウントと、そのアカウントに対応する特定の BLOB ストレージ コンテナーを、既定のファイル システムとして指定します。

プロビジョニング プロセスを実行するときに、このストレージ アカウントに加えて、同じ Azure サブスクリプションまたは別の Azure サブスクリプションに属する付加的なストレージ アカウントを追加することもできます。付加的なストレージ アカウントを追加する方法の詳細については、「[カスタム オプションを使用した HDInsight での Hadoop クラスターのプロビジョニング][hdinsight-provision]」を参照してください。 

- **  クラスター**に接続されたストレージ アカウントのコンテナー: *アカウント名とキーが *core-site.xml に保存されるので、ユーザーには、そのようなコンテナー内の BLOB に対するフル アクセス許可が与えられます。
- **クラスターに接続されていないストレージ アカウントのパブリック コンテナーとパブリック BLOB: ** ユーザーには、コンテナー内の BLOB に対する読み取り専用アクセス許可が与えられます。

	> [WACOM.NOTE]
        >  パブリック コンテナーの場合、そのコンテナー内に配置されているすべての BLOB のリストとコンテナー メタデータを取得できます。  パブリック BLOB の場合、正確な URL がわかっているときのみ、その BLOB にアクセスできます。詳細については、「<a href="http://msdn.microsoft.com/ja-jp/library/windowsazure/dd179354.aspx">コンテナーと BLOB へのアクセスの制限</a>」を参照してください。

- **クラスターに接続されていないストレージ アカウントのプライベート コンテナー: **WebHCat ジョブを送信するときにストレージ アカウントを定義しなければ、コンテナー内の BLOB にアクセスすることはできません。詳しくは、この記事で後ほど説明します。


プロビジョニング処理で定義されたストレージ アカウントとそのキーは、%HADOOP_HOME%/conf/core-site.xml に格納されます。  HDInsight の既定の動作では、core-site.xml ファイルに定義されたストレージ アカウントが使用されます。core-site.xml ファイルを編集することは推奨されません。クラスター ヘッドノード (マスター) はいつでも再イメージングしたり移行したりでき、そうなると core-site.xml ファイルに加えた変更がすべて失われるためです。

Hive、MapReduce、Hadoop ストリーミング、Pig など、複数の WebHCat ジョブを利用して、ストレージ アカウントの説明とそのメタデータを伝達できます (現在、ストレージ アカウントについては Pig が対応していますが、メタデータについては対応していません)。この記事の「[PowerShell を使用して BLOB にアクセス](#powershell)」で、この機能のサンプルを紹介しています。詳細については、「[Using an HDInsight Cluster with Alternate Storage Accounts and Metastores (代替のストレージ アカウントおよびメタストアでの HDInsight クラスターの使用)](http://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx)」を参照してください。

BLOB ストレージ コンテナーには、"キーと値のペア" としてデータが格納されます。ディレクトリ階層はありません。ただし、キー名でスラッシュ (/) を使用すれば、ファイルがディレクトリ階層に保存されているように見せかけることができます。たとえば、BLOB のキー名を「*input/log1.txt*」とします。この場合、*input* ディレクトリは実際には存在しませんが、キー名でスラッシュ (/) が使用されているのでファイル パスのように見えます。










##<a id="benefits"></a>Azure BLOB ストレージの利点
計算処理とストレージを同じ場所で実行しないとなるとパフォーマンスの低下が懸念されますが、これは、Azure データ センター内のストレージ アカウント リソースの近くに計算クラスターを配置することで軽減されます。高速ネットワークが整備されているので、計算ノードは BLOB ストレージ内のデータにきわめて効率的にアクセスできます。

HDFS ではなく、BLOB ストレージにデータを格納することにはいくつかの利点があります。

* **データの再使用と共有: **HDFS のデータは計算クラスター内に配置されます。HDFS API を使用してデータを操作できるのは、計算クラスターへのアクセスが許可されているアプリケーションだけです。BLOB ストレージ内のデータは、HDFS API または [BLOB ストレージ REST API][ blob-storage-restAPI] を使用してアクセスできます。したがって、さまざまなアプリケーション (その他の HDInsight クラスターを含む) やツールを使用してデータの生成と利用ができます。
* **データのアーカイブ: ** BLOB ストレージにデータを格納した場合、計算で使用する HDInsight クラスターを削除してもユーザー データは失われません。 
* **データ ストレージ コスト: **計算クラスターは BLOB ストレージ コンテナーよりコストがかかるため、DFS に長期間データを格納すると、BLOB ストレージにデータを格納した場合よりコストが高くなってしまいます。さらに、計算クラスターを生成するたびにデータを読み込む必要がないので、データの読み込みコストも節約できます。
* **柔軟な拡張: **HDFS は大規模なファイル システムを提供しますが、規模を拡張するにはクラスターのノード数を増やさなければならないので、作業が複雑になります。一方、BLOB ストレージには柔軟な拡張機能がもともと備わっています。
* **ジオ (主要地域) レプリケーション: **BLOB ストレージ コンテナーは、Azure ポータルを使用して別の拠点にジオ (主要地域) レプリケートできます。災害発生時には別の拠点でデータを回復でき、データの冗長性が高まりますが、ジオ (主要地域) レプリケートした別拠点へのフェールオーバーはパフォーマンスに大きな影響を与え、追加コストが発生する可能性もあります。ジオ (主要地域) レプリケーションを利用するときは、追加コストがかかっても保護する価値のあるデータかどうかを十分に考慮してください。

MapReduce の一部のジョブやパッケージでは中間結果が生成されますが、それらは BLOB ストレージ コンテナーに保存する必要がありません。このような場合、中間結果データをローカルの HDFS に保存することができます。実際、HDInsight では、Hive ジョブやその他のプロセスで生成される中間結果の一部が DFS に格納されます。 





##<a id="preparingblobstorage"></a>BLOB ストレージのコンテナーの準備
BLOB を使用するには、まず、[Azure ストレージ アカウント][ azure-storage-create] を作成します。その一環として、このアカウントを使用して作成するオブジェクトを格納する Azure データ センターを指定します。クラスターとストレージ アカウントの両方を、同じデータ センターでホストする必要があります (Hive メタストア SQL データベースと Oozie メタストア SQL データベースも、同じデータ センター内に配置する必要があります)。作成される各 BLOB は、どこにあるとしても、ストレージ アカウント内のいずれかのコンテナーに属します。このコンテナーは、HDInsight の外部で作成された既存の BLOB ストレージ コンテナー、または HDInsight クラスター用に作成されたコンテナーであってもかまいません。 



###管理ポータルを使用した HDInsight の BLOB コンテナーの作成

Azure の管理ポータルから HDInsight クラスターをプロビジョニングする場合は、簡易作成およびカスタム作成という 2 つの方法があります。簡易作成オプションを使用する場合は、事前に Azure ストレージ アカウントを作成しておく必要があります。  作成方法については、「[ストレージ アカウントの作成方法][ azure-storage-create]」を参照してください。 

簡易作成オプションを使用する場合、既存のストレージ アカウントを選択できます。簡易作成では、HDInsight クラスターと同じ名前の新しいコンテナーが作成されます。同じ名前のコンテナーが既に存在する場合は、<clusterName>-<x> が使用されます。たとえば、myHDIcluster-1 などの名前になります。このコンテナーが既定のファイル システムとして使用されます。

![HDI.QuickCreate][img-hdi-quick-create]
 
カスタム作成を使用する場合、既定のストレージ アカウントについて次のいずれかの方法を選ぶことができます。

- 既存のストレージを使用する
- 新しいストレージを作成する
- 別のサブスクリプションのストレージを使用する

さらに、独自の BLOB コンテナーを作成するという方法や、既存の BLOB コンテナーを使用する方法もあります。
 
![HDI.CustomCreateStorageAccount][img-hdi-custom-create-storage-account]
  




### Azure PowerShell を使用してコンテナーを作成する
[Azure PowerShell][ powershell-install] を使用して BLOB コンテナーを作成することができます。次に、サンプルの PowerShell スクリプトを示します。

	$subscriptionName = "<SubscriptionName>"	# Azure サブスクリプション名
	$storageAccountName = "<AzureStorageAccountName>" # これから作成するストレージ アカウント
	$containerName="<BlobContainerToBeCreated>" # これから作成する BLOB コンテナー名

	# 使用する Azure アカウントに接続して現在のサブスクリプションを選択する
	Add-AzureAccount # 接続は数時間で期限切れになります。
	Select-AzureSubscription $subscriptionName # 複数のサブスクリプションがある場合にのみ必須
	
	# ストレージ コンテキスト オブジェクトを作成
	$storageAccountkey = get-azurestoragekey $storageAccountName | %{$_.Primary}
	$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  
	
	# BLOB ストレージ コンテナーを作成する
	New-AzureStorageContainer -Name $containerName -Context $destContext 


##<a id="addressing"></a>BLOB ストレージ内のファイルの指定

BLOB ストレージ内のファイルにアクセスするための URI スキームは次のとおりです。 

	wasb[s]://<BlobStorageContainerName>@<StorageAccountName>.blob.core.windows.net/<path>


> [WACOM.NOTE]ストレージ エミュレータ― (HDInsight エミュレータ―上で実行) でファイルをアドレス指定するための構文は、<i>wasb://&lt;ContainerName&gt;@storageemulator</i> です。



この URI スキームは、暗号化なしのアクセス (*WASB*) と SSL で暗号化されたアクセス (*WASBS*) の両方に対応しています。同じ Azure データ センター内のデータにアクセスする場合でも、できる限り *wasbs* を使用することをお勧めします。
	
&lt;BlobStorageContainerName&gt; では、BLOB ストレージ コンテナーの名前を指定します。
The &lt;StorageAccountName&gt; では、Azure ストレージ アカウント名を指定します。完全修飾ドメイン名 (FQDN) を指定する必要があります。
	
&lt;BlobStorageContainerName&gt; と &lt;StorageAccountName&gt; のどちらも指定しない場合は、既定のファイル システムが使用されます。既定のファイル システム上にあるファイルに関しては、相対パスまたは絶対パスのどちらかを使用できます。たとえば、HDInsight クラスターに付属している hadoop-mapreduce-examples.jar ファイルは、次のどちらかを使用して参照できます。

	wasb://mycontainer@myaccount.blob.core.windows.net/example/jars/hadoop-mapreduce-examples.jar
	wasb:///example/jars/hadoop-mapreduce-examples.jar
	/example/jars/hadoop-mapreduce-examples.jar
	
> [WACOM.NOTE]HDInsight クラスター Version 1.6 および 2.1 では、ファイル名は <i>hadoop-examples.jar</i> です。


&lt;path&gt; は、ファイルまたはディレクトリの HDFS パス名です。BLOB ストレージ コンテナーには "キーと値" のペアが格納されるだけであるため、階層ファイル システムは存在しません。ただし、BLOB キー内でスラッシュ (/) を使用すると、ディレクトリの区切りと見なされます。たとえば、*hadoop-mapreduce-examples.jar* に対応する BLOB 名は、次のとおりです。

	example/jars/hadoop-mapreduce-examples.jar
	

##<a id="powershell"></a>Azure PowerShell を使用した BLOB へのアクセス

コンピューターに Azure PowerShell をインストールして構成する方法については、「[Azure PowerShell のインストールおよび構成方法][ powershell-install]」を参照してください。Azure PowerShell コンソール ウィンドウまたは PowerShell_ISE を使用して、PowerShell コマンドレットを実行することができます。 

BLOB 関連のコマンドレットを一覧表示するには、次のコマンドを使用します。

	Get-Command *blob*

![Blob.PowerShell.cmdlets][img-hdi-powershell-blobcommands]


**ファイルをアップロードするための PowerShell サンプル**

「[HDInsight での Hadoop ジョブ用データのアップロード][ hdinsight-upload-data]」を参照してください。

**ファイルをダウンロードするための PowerShell サンプル**

次のスクリプトは、ブロック BLOB を現在のフォルダーにダウンロードします。スクリプトを実行する前に、書き込みアクセス許可があるフォルダーに移動します。 


	$storageAccountName = "<AzureStorageAccountName>"   # プロビジョニング時に既定のファイル システムに対して使用したストレージ アカウント。
	$containerName = "<BlobStorageContainerName>"  # 既定のファイル システム コンテナーの名前はクラスターと同じです。
	$blob = "example/data/sample.log" # ダウンロードする BLOB の名前。
	
	# Azure サブスクリプションに接続していない場合は Add-AzureAccount を使用
	#Add-AzureAccount # 接続は 12 時間有効
	
	# 複数のサブスクリプションがある場合はこれら 2 つのコマンドを使用
	#$subscriptionName = "<SubscriptionName>"       
	#Select-AzureSubscription $subscriptionName
	
	Write-Host "Create a context object ... " -ForegroundColor Green
	$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
	$storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  
	
	Write-Host "Download the blob ..." -ForegroundColor Green
	Get-AzureStorageBlobContent -Container $ContainerName -Blob $blob -Context $storageContext -Force
	
	Write-Host "List the downloaded file ..." -ForegroundColor Green
	cat "./$blob"

**ファイルを削除するための PowerShell サンプル**

次のスクリプトで、ファイルを削除する方法を説明します。
	$storageAccountName = "<AzureStorageAccountName>"   # プロビジョニング時に既定のファイル システムに対して使用したストレージ アカウント。
	$containerName = "<BlobStorageContainerName>"  # 既定のファイル システム コンテナーの名前はクラスターと同じです。
	$blob = "example/data/sample.log" # ダウンロードする BLOB の名前。
	
	# Azure サブスクリプションに接続していない場合は Add-AzureAccount を使用
	#Add-AzureAccount # 接続は 12 時間有効
	
	# 複数のサブスクリプションがある場合はこれら 2 つのコマンドを使用
	#$subscriptionName = "<SubscriptionName>"       
	#Select-AzureSubscription $subscriptionName
	
	Write-Host "Create a context object ... " -ForegroundColor Green
	$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
	$storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  
	
	Write-Host "Delete the blob ..." -ForegroundColor Green
	Remove-AzureStorageBlob -Container $containerName -Context $storageContext -blob $blob 
	

**フォルダー内のファイルを一覧表示するための PowerShell サンプル**

次のスクリプトで、"フォルダー" の内部にあるファイルを一覧表示する方法を説明します。その次の例では、Invoke-Hive コマンドレットを使用して dfs ls コマンドを実行し、フォルダーの内容を一覧表示する方法を示しています。

	$storageAccountName = "<AzureStorageAccountName>"   # プロビジョニング時に既定のファイル システムに対して使用したストレージ アカウント。
	$containerName = "<BlobStorageContainerName>"  # 既定のファイル システム コンテナーの名前はクラスターと同じです。
	$blobPrefix = "example/data/"
	
	# Azure サブスクリプションに接続していない場合は Add-AzureAccount を使用
	#Add-AzureAccount # 接続は 12 時間有効
	
	# 複数のサブスクリプションがある場合はこれら 2 つのコマンドを使用
	#$subscriptionName = "<SubscriptionName>"       
	#Select-AzureSubscription $subscriptionName
	
	Write-Host "Create a context object ... " -ForegroundColor Green
	$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
	$storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  

	Write-Host "List the files in $blobPrefix ..."
	Get-AzureStorageBlob -Container $containerName -Context $storageContext -prefix $blobPrefix

**定義されていないストレージ アカウントにアクセスする PowerShell サンプル**
	
このサンプルでは、プロビジョニング処理中に定義されていないストレージ アカウントのフォルダーの内容を一覧表示する方法を示しています。
	$clusterName = "<HDInsightClusterName>"
	
	$undefinedStorageAccount = "<UnboundedStorageAccountUnderTheSameSubscription>"
	$undefinedContainer = "<UnboundedBlobContainerAssociatedWithTheStorageAccount>"
	
	$undefinedStorageKey = Get-AzureStorageKey $undefinedStorageAccount | %{ $_.Primary }
	
	Use-AzureHDInsightCluster $clusterName
	
	$defines = @{}
	$defines.Add("fs.azure.account.key.$undefinedStorageAccount.blob.core.windows.net", $undefinedStorageKey)

	Invoke-Hive -Defines $defines -Query "dfs -ls wasb://$undefinedContainer@$undefinedStorageAccount.blob.core.windows.net/;"
 
##<a id="nextsteps"></a>次のステップ

この記事では、HDInsight で BLOB ストレージを使用する方法について説明しました。BLOB ストレージは HDInsight の重要なコンポーネントです。Azure BLOB ストレージを使用すれば、収集したデータを長期にわたって格納できる拡張性に優れたソリューションを構築できます。さらに HDInsight を使用することで、格納したデータから有益な情報を得ることができます。

詳細については、次の記事を参照してください。

* [Get started with Azure HDInsight (HDInsight で Hadoop 2.4 を使用する)][hdinsight-get-started]
* [HDInsight での Hadoop ジョブ用データのアップロード][ hdinsight-upload-data]
* [Use Hive with HDInsight (HDInsight での Hive と Hadoop の使用)][hdinsight-use-hive]
* [Use Pig with HDInsight (HDInsight の Hadoop での Pig の使用)][hdinsight-use-pig]

[Powershell-install]: ../install-configure-powershell/
[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-get-started]: ../hdinsight-get-started/
[hdinsight-upload-data]: ../hdinsight-upload-data/
[hdinsight-use-hive]: ../hdinsight-use-hive/
[hdinsight-use-pig]: ../hdinsight-use-pig/

[Powershell-install]: ../install-configure-powershell/
[blob-storage-restAPI]: http://msdn.microsoft.com/ja-jp/library/windowsazure/dd135733.aspx
[azure-storage-create]: ../storage-create-storage-account/

[img-hdi-powershell-blobcommands]: ./media/hdinsight-use-blob-storage/HDI.PowerShell.BlobCommands.png 
[img-hdi-quick-create]: ./media/hdinsight-use-blob-storage/HDI.QuickCreateCluster.png
[img-hdi-custom-create-storage-account]: ./media/hdinsight-use-blob-storage/HDI.CustomCreateStorageAccount.png  
