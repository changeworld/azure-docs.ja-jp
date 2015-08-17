<properties
	pageTitle="HDFS と互換性のある BLOB ストレージのデータのクエリ | Microsoft Azure"
	description="HDInsight では、BLOB ストレージを HDFS のビッグ データ ストアとして使用します。BLOB ストレージのデータに対するクエリを実行し、分析結果を格納する方法について説明します。"
	services="hdinsight,storage"
	documentationCenter=""
	authors="mumian"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="06/10/2015"
	ms.author="jgao"/>


#HDInsight の Hadoop での HDFS と互換性のある Azure BLOB ストレージの使用

このチュートリアルでは、HDInsight で低コストの Azure BLOB ストレージを使用する方法、Azure ストレージ アカウントと BLOB ストレージ コンテナーを作成する方法、内部のデータのアドレス指定を行う方法について説明します。

Azure BLOB ストレージは、堅牢な汎用ストレージ ソリューションであり、HDInsight とシームレスに統合されます。Hadoop 分散ファイル システム (HDFS) のインターフェイスを利用して、HDInsight のすべてのコンポーネントが BLOB ストレージの構造化データまたは非構造化データを直接操作できます。

BLOB ストレージにデータを格納した場合、計算で使用する HDInsight クラスターを削除してもユーザー データは失われません。

> [AZURE.NOTE]HDInsight バージョン 3.0 クラスターでは、**asv://* 構文はサポートされません。つまり、**asv://* 構文を明示的に使用するジョブを HDInsight バージョン 3.0 クラスターに送信すると、そのジョブは失敗します。**wasb://* 構文を代わりに使用する必要があります。また、既存のメタストアにより、asv:// 構文を使用してリソースへの明示的な参照を含むジョブを作成し、HDInsight バージョン 3.0 クラスターに送信すると、そのジョブは失敗します。これらのメタストアは、wasb:// 構文を使用してリソースをアドレス指定するように再作成する必要があります。

> HDInsight では現在、ブロック blob のみがサポートされています。

> ほとんどの HDFS コマンド (<b>ls</b>、<b>copyFromLocal</b>、<b>mkdir</b> など) は通常と同じように機能します。ただし、<b>fschk</b> や <b>dfsadmin</b> など、HDFS ネイティブ実装 (DFS) に固有のコマンドについては、Azure BLOB ストレージ上で実行した場合に動作が異なります。

HDInsight クラスターのプロビジョニングについては、「[HDInsight の概要][hdinsight-get-started]」または「[HDInsight クラスターのプロビジョニング][hdinsight-provision]」ご覧ください。


##<a id="architecture"></a>HDInsight のストレージ アーキテクチャ
次の図は、HDInsight のストレージ アーキテクチャを示しています。

![Hadoop クラスターは、HDFS API を使用して、BLOB ストレージの構造化データおよび非構造化データへのアクセスと保存を実行します。](./media/hdinsight-hadoop-use-blob-storage/HDI.WASB.Arch.png "HDInsight のストレージ アーキテクチャ")

HDInsight では、それぞれのコンピューティング ノードにローカルに割り当てられている分散ファイル システムにアクセスします。このファイル システムには、完全修飾 URI を使用してアクセスできます。次に例を示します。

	hdfs://<namenodehost>/<path>

さらに、HDInsight では、Azure BLOB ストレージに格納されたデータにアクセスすることもできます。の構文は次のとおりです。

	wasb[s]://<containername>@<accountname>.blob.core.windows.net/<path>


Hadoop は、既定のファイル システムの概念をサポートしています。既定のファイル システムは、既定のスキームとオーソリティを意味します。これは相対パスの解決に使用することもできます。HDInsight のプロビジョニング プロセスを実行するときに、Azure ストレージ アカウントと、そのアカウントに対応する特定の Azure BLOB ストレージ コンテナーを既定のファイル システムとして指定します。

プロビジョニング プロセスを実行するときに、このストレージ アカウントに加えて、同じ Azure サブスクリプションか、別の Azure サブスクリプションに属する付加的なストレージ アカウントを追加することもできます。ストレージ アカウントをさらに追加する手順については、「[HDInsight クラスターのプロビジョニング][hdinsight-provision]」を参照してください。

- **クラスターに接続されているストレージ アカウント内のコンテナー:** アカウントの名前とキーはプロビジョニング中にクラスターと関連付けられるため、これらのコンテナー内の BLOB に対するフル アクセス許可が与えられます。

- **クラスターに接続されていないストレージ アカウント内のパブリック コンテナーまたはパブリック BLOB:** コンテナー内の BLOB に対する読み取り専用のアクセス許可が与えられます。

	> [AZURE.NOTE]> パブリック コンテナーの場合、そのコンテナー内に配置されているすべての BLOB のリストとコンテナー メタデータを取得できます。パブリック BLOB の場合、正確な URL がわかっているときのみ、その BLOB にアクセスできます。詳細については、「<a href="http://msdn.microsoft.com/library/windowsazure/dd179354.aspx">コンテナーと BLOB へのアクセスの制限</a>」をご覧ください。

- **クラスターに接続されていないストレージ アカウント内のプライベート コンテナー:** WebHCat ジョブを送信するときに、ストレージ アカウントを定義しない限り、コンテナー内の BLOB にアクセスできません。詳しくは、この記事の後半で説明します。


プロビジョニング処理で定義されたストレージ アカウントとそのキーは、クラスター ノードの %HADOOP\_HOME%/conf/core-site.xml に格納されます。HDInsight の既定の動作では、core-site.xml ファイルに定義されたストレージ アカウントが使用されます。core-site.xml ファイルを編集することは推奨されません。クラスター ヘッドノード (マスター) はいつでも再イメージングしたり移行したりでき、そうなると core-site.xml ファイルに加えた変更がすべて失われるためです。

Hive、MapReduce、Hadoop ストリーミング、Pig など、複数の WebHCat ジョブを利用して、ストレージ アカウントの説明とそのメタデータを伝達できます (現在、ストレージ アカウントについては Pig が対応していますが、メタデータについては対応していません)。 この記事の「[Azure PowerShell を使用した BLOB へのアクセス](#powershell)」で、この機能のサンプルを紹介しています。詳細については、「[Using an HDInsight Cluster with Alternate Storage Accounts and Metastores (代替のストレージ アカウントおよびメタストアでの HDInsight クラスターの使用)](http://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx)」を参照してください。

BLOB ストレージは、構造化データと非構造化データに使用できます。BLOB ストレージ コンテナーには、"キーと値のペア" としてデータが格納されます。ディレクトリ階層はありません。ただし、キー名でスラッシュ (/) を使用すれば、ファイルがディレクトリ階層に保存されているように見せかけることができます。たとえば、BLOB のキー名を「*input/log1.txt*」とします。この場合、*input* ディレクトリは実際に存在しませんが、キー名でスラッシュが使用されているのでファイル パスのように見えます。

###<a id="benefits"></a>BLOB ストレージの利点
コンピューティング クラスターとストレージ リソースを同じ場所で併置しないとなるとパフォーマンスの低下が懸念されますが、これは、Azure データ センター内のストレージ アカウント リソースの近くにコンピューティング クラスターを配置することで軽減されます。高速ネットワークが整備されているので、コンピューティング ノードは Azure BLOB ストレージ内のデータにきわめて効率的にアクセスできます。

HDFS ではなく、Azure BLOB ストレージにデータを格納することにはいくつかの利点があります。

* **データの再使用と共有:** HDFS のデータはコンピューティング クラスター内に配置されます。HDFS API を使用してデータを操作できるのは、コンピューティング クラスターへのアクセスが許可されているアプリケーションだけです。Azure BLOB ストレージ内のデータは、HDFS API または [BLOB ストレージ REST API][blob-storage-restAPI] を使用してアクセスできます。したがって、さまざまなアプリケーション (その他の HDInsight クラスターを含む) やツールを使用してデータの生成と利用ができます。
* **データのアーカイブ:** Azure BLOB ストレージにデータを格納した場合、計算で使用する HDInsight クラスターを削除してもユーザー データは失われません。
* **データ ストレージ コスト:** コンピューティング クラスターは Azure BLOB ストレージ コンテナーよりコストがかかるため、DFS に長期間データを格納すると、Azure BLOB ストレージにデータを格納した場合よりコストが高くなります。さらに、コンピューティング クラスターを生成するたびにデータを読み込む必要がないので、データの読み込みコストも節約できます。
* **柔軟なスケールアウト:** HDFS は大規模なファイル システムを提供しますが、規模を拡張するにはクラスターのノード数を増やさなければならないので、作業が複雑になります。一方、Azure BLOB ストレージには柔軟なスケール機能がもともと備わっています。
* **geo レプリケーション:** Azure BLOB ストレージ コンテナーは、Azure ポータルを使用して別の拠点に geo レプリケートできます。災害発生時には別の拠点でデータを回復でき、データの冗長性が高まりますが、geo レプリケートした別拠点へのフェールオーバーはパフォーマンスに大きな影響を与え、追加コストが発生する可能性もあります。geo レプリケーションを利用するときは、追加コストがかかっても保護する価値のあるデータかどうかを十分に考慮してください。

MapReduce の一部のジョブやパッケージでは中間結果が生成されますが、Azure BLOB ストレージ コンテナーには保存したくない場合もあります。このような場合、中間結果データをローカルの HDFS に保存できます。実際、HDInsight では、Hive ジョブやその他のプロセスで生成される中間結果の一部が DFS に格納されます。



##<a id="preparingblobstorage"></a>BLOB コンテナーの作成

BLOB を使用するには、まず、[Azure ストレージ アカウント][azure-storage-create]を作成します。その一環として、このアカウントを使用して作成するオブジェクトを格納する Azure データセンターを指定します。クラスターとストレージ アカウントは、同じデータセンターに置く必要があります。Hive メタストア SQL Server データベースと Oozie メタストア SQL Server データベースも、同じデータセンターに配置する必要があります。

作成される各 BLOB は、どこにあるとしても、Azure ストレージ アカウント内のコンテナーに属します。このコンテナーは、HDInsight の外部で作成された既存の BLOB ストレージ コンテナーか、HDInsight クラスター用に作成されたコンテナーであってもかまいません。

既定のストレージ コンテナーと複数の HDInsight クラスターを共有しないでください。共有のコンテナーを使用して複数の HDInsight クラスターのデータへのアクセスを提供する必要がある場合は、クラスター構成で追加のストレージ アカウントとして追加する必要があります。詳細については、「[HDInsight クラスターのプロビジョニング][hdinsight-provision]」をご覧ください。ただし、元の HDInsight クラスターを削除した後でも既定のストレージ コンテナーを再利用できます。HBase クラスターでは、削除された HBase クラスターで使用される既定の blob ストレージ コンテナーを使用して、新しい HBase クラスターをプロビジョニングすることで、HBase テーブルのスキーマとデータを実際に保持できます。


###Azure ポータルの使用

Azure ポータルから HDInsight クラスターをプロビジョニングする場合は、**簡易作成**と**カスタム作成**という 2 つの方法があります。簡易作成オプションを使用する場合は、事前に Azure ストレージ アカウントを作成しておく必要があります。詳しい手順については、「[ストレージ アカウントの作成方法][azure-storage-create]」を参照してください。

簡易作成オプションを使用する場合、既存のストレージ アカウントを選択できます。簡易作成では、HDInsight クラスターと同じ名前の新しいコンテナーが作成されます。同じ名前のコンテナーが既に存在する場合は、<clusterName>-<x> が使用されます。たとえば、*myHDIcluster-1* などの名前になります。このコンテナーが既定のファイル システムとして使用されます。

![Azure ポータルの HDInsight で新しい Hadoop クラスターの簡易作成を使用する。][img-hdi-quick-create]

カスタム作成を使用する場合、既定のストレージ アカウントについて次のいずれかのオプションを選択できます。

- 既存のストレージを使用する
- 新しいストレージを作成する
- 別のサブスクリプションのストレージを使用する

さらに、独自のコンテナーを作成するという方法や、既存のコンテナーを使用する方法もあります。

![HDInsight クラスター用の既存のストレージ アカウントを使用するオプション。][img-hdi-custom-create-storage-account]

###Azure CLI の使用

[Azure CLI のインストールと構成](../xplat-cli.md)が完了している場合は、次のコマンドを使用して、ストレージ アカウントとコンテナーを作成できます。

	azure storage account create <storageaccountname> --type LRS

> [AZURE.NOTE]`--type` パラメーターは、ストレージ アカウントのレプリケート方法を指定します。詳細については、[Azure ストレージのレプリケーション](../storage-redundancy.md)に関するページを参照してください。

ストレージ アカウントを配置する地理的リージョンを指定するよう要求されます。ストレージ アカウントは、HDInsight クラスターの作成を計画しているリージョンと同じリージョンに作成する必要があります。

ストレージ アカウントを作成した後は、次のコマンドを使用して、ストレージ アカウント キーを取得します。

	azure storage account keys list <storageaccountname>

コンテナーを作成するには、次のコマンドを使用します。

	azure storage container create <containername> --account-name <storageaccountname> --account-key <storageaccountkey>

###Azure PowerShell の使用

[Azure PowerShell のインストールと構成][powershell-install]が完了している場合は、Azure PowerShell プロンプトで次のスクリプトを使用して、ストレージ アカウントとコンテナーを作成できます。

	$subscriptionName = "<SubscriptionName>"	# Azure subscription name
	$storageAccountName = "<AzureStorageAccountName>" # The storage account that you will create
	$containerName="<BlobContainerToBeCreated>" # The Blob container name that you will create

	# Connect to your Azure account and selec the current subscription
	Add-AzureAccount # The connection will expire in 12 hours.
	Select-AzureSubscription $subscriptionName #only required if you have multiple subscriptions

	# Create a storage context object
	$storageAccountkey = get-azurestoragekey $storageAccountName | %{$_.Primary}
	$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  

	# Create a Blob storage container
	New-AzureStorageContainer -Name $containerName -Context $destContext


##<a id="addressing"></a>BLOB ストレージ内のファイルの指定

HDInsight から BLOB ストレージ内のファイルにアクセスするための URI スキームは次のとおりです。

	wasb[s]://<BlobStorageContainerName>@<StorageAccountName>.blob.core.windows.net/<path>


> [AZURE.NOTE](HDInsight のエミュレーター上で実行されている) ストレージ エミュレーター上にあるファイルを指定するための構文は、<i>wasb://&lt;ContainerName&gt;@storageemulator</i> です。



この URI スキームは、暗号化なしのアクセス (*wasb:* プレフィックス) と SSL で暗号化されたアクセス (*wasbs*) に対応しています。同じ Azure のデータ センター内のデータにアクセスする場合でも、できる限り *wasbs* を使用することをお勧めします。

&lt;BlobStorageContainerName&gt; では、Azure BLOB ストレージでコンテナーの名前を指定します。&lt;StorageAccountName&gt; では、Azure ストレージ アカウントの名前を指定します。完全修飾ドメイン名 (FQDN) を指定する必要があります。

&lt;BlobStorageContainerName&gt; と &lt;StorageAccountName&gt; を両方とも指定しない場合は、既定のファイル システムが使用されます。既定のファイル システム上にあるファイルに関しては、相対パスか絶対パスを使用できます。たとえば、HDInsight クラスターに付属している *hadoop-mapreduce-examples.jar* ファイルは、次のどちらかを使用して確認できます。

	wasb://mycontainer@myaccount.blob.core.windows.net/example/jars/hadoop-mapreduce-examples.jar
	wasb:///example/jars/hadoop-mapreduce-examples.jar
	/example/jars/hadoop-mapreduce-examples.jar

> [AZURE.NOTE]HDInsight バージョン 2.1 クラスターと 1.6 クラスターでは、ファイル名は <i>hadoop-examples.jar</i> です。


&lt;path&gt; は、ファイルまたはディレクトリの HDFS パス名です。Azure BLOB ストレージのコンテナーにはキーと値のペアが格納されるだけであるため、階層ファイル システムは存在しません。ただし、BLOB キー内でスラッシュ (/) を使用すると、ディレクトリの区切りと見なされます。たとえば、*hadoop-mapreduce-examples.jar* に対応する BLOB 名は、次のとおりです。

	example/jars/hadoop-mapreduce-examples.jar

> [AZURE.NOTE]HDInsight の外部から BLOB を操作する場合、ほとんどのユーティリティで WASB 形式が認識されず、代わりに `example/jars/hadoop-mapreduce-examples.jar` などの基本的なパス形式が要求されます。

##<a id="azurecli"></a>Azure CLI を使用した BLOB へのアクセス

BLOB 関連のコマンドを一覧表示するには、次のコマンドを使用します。

	azure storage blob

**Azure CLI を使用してファイルをアップロードする例**

	azure storage blob upload <sourcefilename> <containername> <blobname> --account-name <storageaccountname> --account-key <storageaccountkey>

**Azure CLI を使用してファイルをダウンロードする例**

	azure storage blob download <containername> <blobname> <destinationfilename> --account-name <storageaccountname> --account-key <storageaccountkey>

**Azure CLI を使用してファイルを削除する例**

	azure storage blob delete <containername> <blobname> --account-name <storageaccountname> --account-key <storageaccountkey>

**Azure CLI を使用してファイルを一覧表示する例**

	azure storage blob list <containername> <blobname|prefix> --account-name <storageaccountname> --account-key <storageaccountkey>

##<a id="powershell"></a>Azure PowerShell を使用した BLOB へのアクセス

> [AZURE.NOTE]このセクションでは、PowerShell を使用して BLOB に格納されているデータにアクセスする基本的なコマンドの例を紹介します。HDInsight を使用した操作にカスタマイズされた高度な例については、[HDInsight ツール](https://github.com/Blackmist/hdinsight-tools)に関するページを参照してください。

BLOB 関連のコマンドレットを一覧表示するには、次のコマンドを使用します。

	Get-Command *blob*

![BLOB 関連の PowerShell コマンドレットの一覧。][img-hdi-powershell-blobcommands]


**Azure PowerShell を使用して、ファイルをアップロードする例**

「[データを HDInsight にアップロードする方法][hdinsight-upload-data]」を参照してください。

**Azure PowerShell を使用して、ファイルをダウンロードする例**

次のスクリプトは、ブロック BLOB を現在のフォルダーにダウンロードします。スクリプトを実行する前に、書き込みアクセス許可があるフォルダーにディレクトリを変更します。


	$storageAccountName = "<AzureStorageAccountName>"   # The storage account used for the default file system specified at provision.
	$containerName = "<BlobStorageContainerName>"  # The default file system container has the same name as the cluster.
	$blob = "example/data/sample.log" # The name of the blob to be downloaded.

	# Use Add-AzureAccount if you haven't connected to your Azure subscription
	#Add-AzureAccount # The connection is good for 12 hours

	# Use these two commands if you have multiple subscriptions
	#$subscriptionName = "<SubscriptionName>"
	#Select-AzureSubscription $subscriptionName

	Write-Host "Create a context object ... " -ForegroundColor Green
	$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
	$storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  

	Write-Host "Download the blob ..." -ForegroundColor Green
	Get-AzureStorageBlobContent -Container $ContainerName -Blob $blob -Context $storageContext -Force

	Write-Host "List the downloaded file ..." -ForegroundColor Green
	cat "./$blob"

**Azure PowerShell を使用して、ファイルを削除する例**

次のスクリプトで、ファイルを削除する方法を説明します。

	$storageAccountName = "<AzureStorageAccountName>"   # The storage account used for the default file system specified at provision.
	$containerName = "<BlobStorageContainerName>"  # The default file system container has the same name as the cluster.
	$blob = "example/data/sample.log" # The name of the blob to be downloaded.

	# Use Add-AzureAccount if you haven't connected to your Azure subscription
	#Add-AzureAccount # The connection is good for 12 hours

	# Use these two commands if you have multiple subscriptions
	#$subscriptionName = "<SubscriptionName>"
	#Select-AzureSubscription $subscriptionName

	Write-Host "Create a context object ... " -ForegroundColor Green
	$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
	$storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  

	Write-Host "Delete the blob ..." -ForegroundColor Green
	Remove-AzureStorageBlob -Container $containerName -Context $storageContext -blob $blob


**Azure PowerShell を使用して、フォルダーでファイルを一覧表示する例**

次のスクリプトで、フォルダーの内部にあるファイルを一覧表示する方法を説明します (次の例では、**Invoke-Hive** コマンドレットを使用して **dfs ls** コマンドを実行し、フォルダーの内容を一覧表示する方法を示しています)。

	$storageAccountName = "<AzureStorageAccountName>"   # The storage account used for the default file system specified at provision.
	$containerName = "<BlobStorageContainerName>"  # The default file system container has the same name as the cluster.
	$blobPrefix = "example/data/"

	# Use Add-AzureAccount if you haven't connected to your Azure subscription
	#Add-AzureAccount # The connection is good for 12 hours

	# Use these two commands if you have multiple subscriptions
	#$subscriptionName = "<SubscriptionName>"
	#Select-AzureSubscription $subscriptionName

	Write-Host "Create a context object ... " -ForegroundColor Green
	$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
	$storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  

	Write-Host "List the files in $blobPrefix ..."
	Get-AzureStorageBlob -Container $containerName -Context $storageContext -prefix $blobPrefix

**Azure PowerShell を使用して、定義されていないストレージ アカウントを使用した Hive クエリを実行する例**

このサンプルでは、プロビジョニング処理中に定義されていないストレージ アカウントのフォルダーの内容を一覧表示する方法を示しています。$clusterName = "<HDInsightClusterName>"

	$undefinedStorageAccount = "<UnboundedStorageAccountUnderTheSameSubscription>"
	$undefinedContainer = "<UnboundedBlobContainerAssociatedWithTheStorageAccount>"

	$undefinedStorageKey = Get-AzureStorageKey $undefinedStorageAccount | %{ $_.Primary }

	Use-AzureHDInsightCluster $clusterName

	$defines = @{}
	$defines.Add("fs.azure.account.key.$undefinedStorageAccount.blob.core.windows.net", $undefinedStorageKey)

	Invoke-Hive -Defines $defines -Query "dfs -ls wasb://$undefinedContainer@$undefinedStorageAccount.blob.core.windows.net/;"

##<a id="nextsteps"></a>次のステップ

この記事では、HDInsight で HDFS と互換性のある Azure BLOB ストレージを使用する方法と、Azure BLOB ストレージが HDInsight の重要なコンポーネントであることについて説明しました。Azure BLOB ストレージを使用すれば、収集したデータを長期にわたって格納できるスケーラビリティに優れたソリューションを構築できます。さらに HDInsight を使用することで、格納されている構造化データおよび非構造化データから有益な情報を得ることができます。

詳細については、次の記事を参照してください。

* [Azure HDInsight の概要][hdinsight-get-started]
* [HDInsight へのデータのアップロード][hdinsight-upload-data]
* [HDInsight での Hive の使用][hdinsight-use-hive]
* [HDInsight での Pig の使用][hdinsight-use-pig]

[powershell-install]: ../install-configure-powershell.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-get-started]: hdinsight-hadoop-tutorial-get-started-windows.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md

[blob-storage-restAPI]: http://msdn.microsoft.com/library/windowsazure/dd135733.aspx
[azure-storage-create]: ../storage-create-storage-account.md

[img-hdi-powershell-blobcommands]: ./media/hdinsight-hadoop-use-blob-storage/HDI.PowerShell.BlobCommands.png
[img-hdi-quick-create]: ./media/hdinsight-hadoop-use-blob-storage/HDI.QuickCreateCluster.png
[img-hdi-custom-create-storage-account]: ./media/hdinsight-hadoop-use-blob-storage/HDI.CustomCreateStorageAccount.png
 

<!---HONumber=August15_HO6-->