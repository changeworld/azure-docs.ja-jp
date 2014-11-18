<properties urlDisplayName="Blob Storage with  Hadoop in HDInsight" pageTitle="HDInsight の Hadoop での BLOB ストレージの使用 | Azure" metaKeywords="" description="HDInsight で、HDFS の基盤データ ストアとして BLOB ストレージがどのように使用されるのかを説明します。また、ストア内のデータを照会する方法も説明します。" metaCanonical="" services="storage,hdinsight" documentationCenter="" title="HDInsight の Hadoop での Azure BLOB ストレージの使用" authors="jgao" solutions="" manager="paulettm" editor="mollybos" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jgao" />

# HDInsight の Hadoop での Azure BLOB ストレージの使用

Azure の HDInsight は、データ格納用として Hadoop 分散ファイル システム (HDFS) と Azure BLOB ストレージの両方をサポートしています。BLOB ストレージは、信頼性に優れた汎用的な Azure ストレージ ソリューションです。BLOB ストレージは高度な HDFS インターフェイスを備えているので、データに対して Hadoop エコシステムのすべてのコンポーネントを直接実行できます (既定の設定)。ユーザーは、データがどこに保存されているかを意識する必要がありません。BLOB ストレージは単なる低コストのソリューションではありません。BLOB ストレージにデータを格納した場合、計算で使用する HDInsight クラスターを削除してもユーザー データは失われません。

> [WACOM.NOTE] *asv://* 構文は HDInsight クラスター Version 3.0 でサポートされていません。また、それ以降のバージョンでもサポートされません。つまり、"asv://" 構文を明示的に使用するジョブを HDInsight クラスター Version 3.0 に送信すると、そのジョブは失敗します。代わりに、*wasb://* 構文を使用する必要があります。また、既存のメタストアにより、asv:// 構文を使用してリソースへの明示的な参照を含むジョブを作成し、HDInsight クラスター バージョン 3.0 に送信すると、そのジョブは失敗します。これらのメタストアは、wasb:// を使用してリソースをアドレス指定するように再作成する必要があります。

> [WACOM.NOTE] HDInsight では現在、ブロック BLOB のみがサポートされています。

> [WACOM.NOTE]
> ほとんどの HDFS コマンド (**ls**、**copyFromLocal**、**mkdir** など) は通常と同じように機能します。ただし、**fschk** や **dfsadmin** など、HDFS ネイティブ実装 (DFS) に固有のコマンドについては、Azure BLOB ストレージ上で実行した場合に動作が異なります。

HDInsight クラスターのプロビジョニングについては、「[Windows Azure の HDInsight の概要][Windows Azure の HDInsight の概要]」または「[HDInsight クラスターのプロビジョニング][HDInsight クラスターのプロビジョニング]」を参照してください。

## この記事の内容

-   [HDInsight のストレージ アーキテクチャ][HDInsight のストレージ アーキテクチャ]
-   [Azure BLOB ストレージの利点][Azure BLOB ストレージの利点]
-   [BLOB ストレージのコンテナーの準備][BLOB ストレージのコンテナーの準備]
-   [BLOB ストレージ内のファイルの指定][BLOB ストレージ内のファイルの指定]
-   [PowerShell を使用して BLOB にアクセス][PowerShell を使用して BLOB にアクセス]
-   [次のステップ][次のステップ]

## <span id="architecture"></span></a>HDInsight のストレージ アーキテクチャ

次の図は、HDInsight のストレージ アーキテクチャを示しています。

![HDI.ASVArch][HDI.ASVArch]

HDInsight では、それぞれの計算ノードにローカルに割り当てられている分散ファイル システムにアクセスします。このファイル システムには、完全修飾 URI を使用してアクセスできます。次に例を示します。

    hdfs://<namenodehost>/<path>

さらに、HDInsight では、BLOB ストレージに格納されたデータにアクセスすることもできます。BLOB ストレージにアクセスするための構文は次のとおりです。

    wasb[s]://<containername>@<accountname>.blob.core.windows.net/<path>

Hadoop は、既定のファイル システムの概念をサポートしています。既定のファイル システムとは "既定のスキームとオーソリティ" を意味しており、これらを使用して相対パスを解決することもできます。HDInsight のプロビジョニング プロセスを実行するときに、Azure Storage アカウントと、そのアカウントに対応する特定の BLOB ストレージ コンテナーを、既定のファイル システムとして指定します。

プロビジョニング プロセスを実行するときに、このストレージ アカウントに加えて、同じ Azure サブスクリプションまたは別の Azure サブスクリプションに属する付加的なストレージ アカウントを追加することもできます。付加的なストレージ アカウントを追加する方法の詳細については、「[HDInsight クラスターのプロビジョニング][HDInsight クラスターのプロビジョニング]」を参照してください。

-   **クラスターに接続されたストレージ アカウントのコンテナー:** アカウント名とキーが *core-site.xml* に保存されるので、ユーザーには、そのようなコンテナー内の BLOB に対するフル アクセス許可が与えられます。
-   **クラスターに接続されていないストレージ アカウントのパブリック コンテナーとパブリック BLOB:** ユーザーには、コンテナー内の BLOB に対する読み取り専用アクセス許可が与えられます。

    > [WACOM.NOTE]
    >  \> パブリック コンテナーの場合、そのコンテナー内に配置されているすべての BLOB のリストとコンテナー メタデータを取得できます。パブリック BLOB の場合、正確な URL がわかっているときのみ、その BLOB にアクセスできます。詳細については、「[コンテナーと BLOB へのアクセスの制限][コンテナーと BLOB へのアクセスの制限]」を参照してください。

-   **クラスターに接続されていないストレージ アカウントのプライベート コンテナー:** WebHCat ジョブを送信するときにストレージ アカウントを定義しなければ、コンテナー内の BLOB にアクセスすることはできません。詳しくは、この記事で後ほど説明します。

プロビジョニング処理で定義されたストレージ アカウントとそのキーは、%HADOOP\_HOME%/conf/core-site.xml に格納されます。HDInsight の既定の動作では、core-site.xml ファイルで定義されたストレージ アカウントが使用されます。core-site.xml ファイルを編集することは推奨されません。クラスター ヘッドノード (マスター) はいつでも再イメージングしたり移行したりでき、そうなると core-site.xml ファイルに加えた変更がすべて失われるためです。

Hive、MapReduce、Hadoop ストリーミング、Pig など、複数の WebHCat ジョブを利用して、ストレージ アカウントの説明とそのメタデータを伝達できます (現在、ストレージ アカウントについては Pig が対応していますが、メタデータについては対応していません)。この記事の「[PowerShell を使用して BLOB にアクセス][PowerShell を使用して BLOB にアクセス]」で、この機能のサンプルを紹介しています。詳細については、「[Using an HDInsight Cluster with Alternate Storage Accounts and Metastores (代替のストレージ アカウントおよびメタストアでの HDInsight クラスターの使用)][Using an HDInsight Cluster with Alternate Storage Accounts and Metastores (代替のストレージ アカウントおよびメタストアでの HDInsight クラスターの使用)]」を参照してください。

BLOB ストレージ コンテナーには、"キーと値のペア" としてデータが格納されます。ディレクトリ階層はありません。ただし、キー名でスラッシュ (/) を使用すれば、ファイルがディレクトリ階層に保存されているように見せかけることができます。たとえば、BLOB のキー名を「*input/log1.txt*」とします。この場合、*input* ディレクトリは実際に存在しませんが、キー名でスラッシュ (/) が使用されているのでファイル パスのように見えます。

## <span id="benefits"></span></a>Azure BLOB ストレージの利点

計算処理とストレージを同じ場所で実行しないとなるとパフォーマンスの低下が懸念されますが、これは、Azure データ センター内のストレージ アカウント リソースの近くに計算クラスターを配置することで軽減されます。高速ネットワークが整備されているので、計算ノードは BLOB ストレージ内のデータにきわめて効率的にアクセスできます。

HDFS ではなく、BLOB ストレージにデータを格納することにはいくつかの利点があります。

-   **データの再使用と共有:** HDFS のデータは計算クラスター内に配置されます。HDFS API を使用してデータを操作できるのは、計算クラスターへのアクセスが許可されているアプリケーションだけです。BLOB ストレージ内のデータは、HDFS API または [BLOB ストレージ REST API][BLOB ストレージ REST API] を使用してアクセスできます。したがって、さまざまなアプリケーション (その他の HDInsight クラスターを含む) やツールを使用してデータの生成と利用ができます。
-   **データのアーカイブ:** BLOB ストレージにデータを格納した場合、計算で使用する HDInsight クラスターを削除してもユーザー データは失われません。
-   **データ ストレージ コスト:** 計算クラスターは BLOB ストレージ コンテナーよりコストがかかるため、DFS に長期間データを格納すると、BLOB ストレージにデータを格納した場合よりコストが高くなってしまいます。さらに、計算クラスターを生成するたびにデータを読み込む必要がないので、データの読み込みコストも節約できます。
-   **柔軟な拡張:** HDFS は大規模なファイル システムを提供しますが、規模を拡張するにはクラスターのノード数を増やさなければならないので、作業が複雑になります。一方、BLOB ストレージには柔軟な拡張機能がもともと備わっています。
-   **ジオ (主要地域) レプリケーション:** BLOB ストレージ コンテナーは、Azure ポータルを使用して別の拠点にジオ (主要地域) レプリケートできます。災害発生時には別の拠点でデータを回復でき、データの冗長性が高まりますが、ジオ (主要地域) レプリケートした別拠点へのフェールオーバーはパフォーマンスに大きな影響を与え、追加コストが発生する可能性もあります。ジオ (主要地域) レプリケーションを利用するときは、追加コストがかかっても保護する価値のあるデータかどうかを十分に考慮してください。

MapReduce の一部のジョブやパッケージでは中間結果が生成されますが、それらは BLOB ストレージ コンテナーに保存する必要がありません。このような場合、中間結果データをローカルの HDFS に保存することができます。実際、HDInsight では、Hive ジョブやその他のプロセスで生成される中間結果の一部が DFS に格納されます。

## <span id="preparingblobstorage"></span></a>BLOB ストレージのコンテナーの準備

BLOB を使用するには、まず、[Azure Storage アカウント][Azure Storage アカウント]を作成します。その一環として、このアカウントを使用して作成するオブジェクトを格納する Azure データ センターを指定します。クラスターとストレージ アカウントの両方を、同じデータ センターでホストする必要があります (Hive メタストア SQL データベースと Oozie メタストア SQL データベースも、同じデータ センター内に配置する必要があります)。作成される各 BLOB は、どこにあるとしても、ストレージ アカウント内のいずれかのコンテナーに属します。このコンテナーは、HDInsight の外部で作成された既存の BLOB ストレージ コンテナー、または HDInsight クラスター用に作成されたコンテナーであってもかまいません。

### 管理ポータルを使用した HDInsight の BLOB コンテナーの作成

Azure 管理ポータルから HDInsight クラスターを作成する場合、"簡易作成" と "カスタム作成" という 2 つの方法があります。簡易作成オプションを使用する場合は、事前に Azure Storage アカウントを作成しておく必要があります。詳しい手順については、「[ストレージ アカウントの作成方法][Azure Storage アカウント]」を参照してください。

簡易作成オプションを使用する場合、既存のストレージ アカウントを選択できます。簡易作成では、HDInsight クラスターと同じ名前の新しいコンテナーが作成されます。同じ名前のコンテナーが既に存在する場合は、<clustername>-<x> が使用されます。たとえば、myHDIcluster-1 などの名前になります。このコンテナーが既定のファイル システムとして使用されます。

![HDI.QuickCreate][HDI.QuickCreate]

カスタム作成を使用する場合、既定のストレージ アカウントについて次のいずれかの方法を選ぶことができます。

-   既存のストレージを使用する
-   新しいストレージを作成する
-   別のサブスクリプションのストレージを使用する

さらに、独自の BLOB コンテナーを作成するという方法や、既存の BLOB コンテナーを使用する方法もあります。

![HDI.CustomCreateStorageAccount][HDI.CustomCreateStorageAccount]

### Azure PowerShell を使用してコンテナーを作成する

[Azure PowerShell][Azure PowerShell] を使用して BLOB コンテナーを作成することができます。次に、サンプルの PowerShell スクリプトを示します。

    $subscriptionName = "<SubscriptionName>"    # Azure subscription name
    $storageAccountName = "<AzureStorageAccountName>" # The storage account that you will create
    $containerName="<BlobContainerToBeCreated>" # The Blob container name that you will create

    # Connect to your Azure account and selec the current subscription
    Add-AzureAccount # The connection will expire in a few hours.
    Select-AzureSubscription $subscriptionName #only required if you have multiple subscriptions

    # Create a storage context object
    $storageAccountkey = get-azurestoragekey $storageAccountName | %{$_.Primary}
    $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  

    # Create a Blob storage container
    New-AzureStorageContainer -Name $containerName -Context $destContext 

## <span id="addressing"></span></a>BLOB ストレージ内のファイルの指定

BLOB ストレージ内のファイルにアクセスするための URI スキームは次のとおりです。

    wasb[s]://<BlobStorageContainerName>@<StorageAccountName>.blob.core.windows.net/<path>

> [WACOM.NOTE] ストレージ エミュレーター (HDInsight エミュレーター上で実行) でファイルをアドレス指定するための構文は、*wasb://\<ContainerName\>@storageemulator* です。

この URI スキームは、暗号化なしのアクセス (*wasb:* プレフィックス) と SSL で暗号化されたアクセス (*wasbs*) の両方に対応しています。同じ Azure データ センター内のデータにアクセスする場合でも、できる限り *wasbs* を使用することをお勧めします。

\<BlobStorageContainerName\> では、BLOB ストレージ コンテナーの名前を指定します。
\<StorageAccountName\> は Azure Storage アカウント名です。完全修飾ドメイン名 (FQDN) を指定する必要があります。

\<BlobStorageContainerName\> と \<StorageAccountName\> を両方とも指定しない場合は、既定のファイル システムが使用されます。既定のファイル システム上にあるファイルに関しては、相対パスまたは絶対パスのどちらかを使用できます。たとえば、HDInsight クラスターに付属している hadoop-mapreduce-examples.jar ファイルは、次のどちらかを使用して参照できます。

    wasb://mycontainer@myaccount.blob.core.windows.net/example/jars/hadoop-mapreduce-examples.jar
    wasb:///example/jars/hadoop-mapreduce-examples.jar
    /example/jars/hadoop-mapreduce-examples.jar

> [WACOM.NOTE] HDInsight クラスター Version 1.6 および 2.1 では、ファイル名は *hadoop-examples.jar* です。

\<path\> は、ファイルまたはディレクトリの HDFS パス名です。BLOB ストレージ コンテナーには "キーと値" のペアが格納されるだけであるため、階層ファイル システムは存在しません。ただし、BLOB キー内でスラッシュ (/) を使用すると、ディレクトリの区切りと見なされます。たとえば、*hadoop-mapreduce-examples.jar* に対応する BLOB 名は、次のとおりです。

    example/jars/hadoop-mapreduce-examples.jar

## <span id="powershell"></span></a>Azure PowerShell を使用した BLOB へのアクセス

コンピューターに Azure PowerShell をインストールして構成する方法については、「[Azure PowerShell のインストールおよび構成][Azure PowerShell]」を参照してください。Azure PowerShell コンソール ウィンドウまたは PowerShell\_ISE を使用して、PowerShell コマンドレットを実行することができます。

BLOB 関連のコマンドレットを一覧表示するには、次のコマンドを使用します。

    Get-Command *blob*

![Blob.PowerShell.コマンドレット][Blob.PowerShell.コマンドレット]

**ファイルをアップロードするための PowerShell サンプル**

「[データを HDInsight にアップロードする方法][データを HDInsight にアップロードする方法]」を参照してください。

**ファイルをダウンロードするための PowerShell サンプル**

次のスクリプトは、ブロック BLOB を現在のフォルダーにダウンロードします。スクリプトを実行する前に、書き込みアクセス許可があるフォルダーに移動します。

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

**ファイルを削除するための PowerShell サンプル**

次のスクリプトで、ファイルを削除する方法を説明します。
$storageAccountName = "<azurestorageaccountname>" \# プロビジョニング時に既定のファイル システムに対して使用したストレージ アカウント。
$containerName = "<blobstoragecontainername>" \# 既定のファイル システム コンテナーの名前はクラスターと同じです。
$blob = "example/data/sample.log" \# ダウンロードする BLOB の名前。

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

**フォルダー内のファイルを一覧表示するための PowerShell サンプル**

次のスクリプトで、"フォルダー" の内部にあるファイルを一覧表示する方法を説明します。その次の例では、Invoke-Hive コマンドレットを使用して dfs ls コマンドを実行し、フォルダーの内容を一覧表示する方法を示しています。

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

**定義されていないストレージ アカウントにアクセスする PowerShell サンプル**

このサンプルでは、プロビジョニング処理中に定義されていないストレージ アカウントのフォルダーの内容を一覧表示する方法を示しています。
$clusterName = "<hdinsightclustername>"

    $undefinedStorageAccount = "<UnboundedStorageAccountUnderTheSameSubscription>"
    $undefinedContainer = "<UnboundedBlobContainerAssociatedWithTheStorageAccount>"

    $undefinedStorageKey = Get-AzureStorageKey $undefinedStorageAccount | %{ $_.Primary }

    Use-AzureHDInsightCluster $clusterName

    $defines = @{}
    $defines.Add("fs.azure.account.key.$undefinedStorageAccount.blob.core.windows.net", $undefinedStorageKey)

    Invoke-Hive -Defines $defines -Query "dfs -ls wasb://$undefinedContainer@$undefinedStorageAccount.blob.core.windows.net/;"

## <span id="nextsteps"></span></a>次のステップ

この記事では、HDInsight で BLOB ストレージを使用する方法について説明しました。BLOB ストレージは HDInsight の重要なコンポーネントです。Azure BLOB ストレージを使用すれば、収集したデータを長期にわたって格納できる拡張性に優れたソリューションを構築できます。さらに HDInsight を使用することで、格納したデータから有益な情報を得ることができます。

詳細については、次の記事を参照してください。

-   [Azure HDInsight の概要][Windows Azure の HDInsight の概要]
-   [HDInsight へのデータのアップロード][データを HDInsight にアップロードする方法]
-   [HDInsight での Hive の使用][HDInsight での Hive の使用]
-   [HDInsight での Pig の使用][HDInsight での Pig の使用]

  [Windows Azure の HDInsight の概要]: ../hdinsight-get-started/
  [HDInsight クラスターのプロビジョニング]: ../hdinsight-provision-clusters/
  [HDInsight のストレージ アーキテクチャ]: #architecture
  [Azure BLOB ストレージの利点]: #benefits
  [BLOB ストレージのコンテナーの準備]: #preparingblobstorage
  [BLOB ストレージ内のファイルの指定]: #addressing
  [PowerShell を使用して BLOB にアクセス]: #powershell
  [次のステップ]: #nextsteps
  [HDI.ASVArch]: ./media/hdinsight-use-blob-storage/HDI.ASVArch.png "HDInsight のストレージ アーキテクチャ"
  [コンテナーと BLOB へのアクセスの制限]: http://msdn.microsoft.com/ja-jp/library/windowsazure/dd179354.aspx
  [Using an HDInsight Cluster with Alternate Storage Accounts and Metastores (代替のストレージ アカウントおよびメタストアでの HDInsight クラスターの使用)]: http://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx
  [BLOB ストレージ REST API]: http://msdn.microsoft.com/ja-jp/library/windowsazure/dd135733.aspx
  [Azure Storage アカウント]: ../storage-create-storage-account/
  [HDI.QuickCreate]: ./media/hdinsight-use-blob-storage/HDI.QuickCreateCluster.png
  [HDI.CustomCreateStorageAccount]: ./media/hdinsight-use-blob-storage/HDI.CustomCreateStorageAccount.png
  [Azure PowerShell]: ../install-configure-powershell/
  [Blob.PowerShell.コマンドレット]: ./media/hdinsight-use-blob-storage/HDI.PowerShell.BlobCommands.png
  [データを HDInsight にアップロードする方法]: ../hdinsight-upload-data/
  [HDInsight での Hive の使用]: ../hdinsight-use-hive/
  [HDInsight での Pig の使用]: ../hdinsight-use-pig/
