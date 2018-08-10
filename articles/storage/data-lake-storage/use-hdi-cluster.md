---
title: Azure HDInsight クラスターで Azure Data Lake Storage Gen2 プレビューを使用する
description: Azure Data Lake Storage Gen2 プレビューのデータに対してクエリを実行し、分析結果を格納する方法について説明します。
keywords: HDFS, 構造化データ, 非構造化データ, Data Lake Store, Hadoop 入力, Hadoop 出力, Hadoop ストレージ, HDFS 入力, HDFS 出力, HDFS ストレージ, WASB Azure
services: hdinsight,storage
tags: azure-portal
author: jamesbak
ms.component: data-lake-storage-gen2
ms.service: storage
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: article
ms.date: 06/27/2018
ms.author: jamesbak
ms.openlocfilehash: 4a9f79b292e58331dcd2f7cb656e24b244aa89ba
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2018
ms.locfileid: "39528510"
---
# <a name="use-azure-data-lake-storage-gen2-preview-with-azure-hdinsight-clusters"></a>Azure HDInsight クラスターで Azure Data Lake Storage Gen2 プレビューを使用する

HDInsight クラスターのデータを分析するには、Azure Storage、Azure Data Lake Storage Gen1、または Azure Data Lake Storage Gen2 プレビューのいずれかの組み合わせでデータを格納します。 いずれのストレージ オプションでも、計算に使用される HDInsight クラスターを安全に削除できます。このとき、ユーザー データは失われません。

Hadoop は、既定のファイル システムの概念をサポートしています。 既定のファイル システムは、既定のスキームとオーソリティを意味します。 これは相対パスの解決に使用することもできます。 HDInsight クラスターの作成プロセス時に、既定のファイル システムとして Azure Storage または Azure Data Lake Storage に BLOB コンテナーを指定できます。 また、HDInsight 3.5 を使用して、Azure Storage または Azure Data Lake Storage のいずれかを既定のファイル システムとして選択することもできますが、いくつか例外があります。

この記事では、HDInsight クラスターでの Azure Data Lake Storage Gen2 の動作について説明します。 HDInsight クラスターの作成方法の詳細については、[Azure Data Lake Storage と Hadoop、Spark、Kafka などを使用した HDInsight クラスターの設定](quickstart-create-connect-hdi-cluster.md)に関する記事を参照してください。

Azure Storage は、堅牢な汎用ストレージ ソリューションであり、HDInsight とシームレスに統合されます。 HDInsight は Azure Data Lake Storage をクラスターの既定のファイル システムとして使用できます。 Hadoop 分散ファイル システム (HDFS) のインターフェイスを利用して、HDInsight のコンポーネントすべてが、Azure Data Lake Storage のファイルに対して直接操作できます。

ビジネス データの格納には、既定のファイル システムを使用しないことをお勧めします。 ストレージ コストを削減するために、既定のファイル システムの使用後、コンテナーを毎回削除することをお勧めします。 既定のコンテナーには、アプリケーション ログとシステム ログが格納されます。 コンテナーを削除する前に、ログを取り出してください。

1 つのファイル システムを複数のクラスターで共有することはサポートされていません。

## <a name="hdinsight-storage-architecture"></a>HDInsight のストレージ アーキテクチャ

次の図は、Azure Storage を使う HDInsight のストレージ アーキテクチャを示しています。

![Hadoop クラスターは、HDFS API を使用して、BLOB ストレージの構造化データおよび非構造化データへのアクセスと保存を実行します。](./media/use-hdi-cluster/HDI.ABFS.Arch.png "HDInsight のストレージ アーキテクチャ")

HDInsight では、それぞれのコンピューティング ノードにローカルに割り当てられている分散ファイル システムにアクセスします。 このファイル システムには、完全修飾 URI を使用してアクセスできます。次に例を示します。

    hdfs://<NAME_NODE_HOST>/<PATH>

さらに、HDInsight では、Azure Data Lake Storage に格納されたデータにアクセスすることもできます。 の構文は次のとおりです。

    abfs[s]://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/<path>

HDInsight クラスターで Azure Storage アカウントを使用するときの考慮事項を次に示します。

* **クラスターに接続されているストレージ アカウントのファイル**には、作成時にクラスターと関連付けられたアカウント名とキーがあります。 この構成では、ファイル システム内のファイルに対してフル アクセス権が付与されます。

* **クラスターに接続されていないストレージ アカウントのパブリック ファイル**の場合、ファイル システム内のファイルに対して読み取り専用アクセス許可が付与されます。
  
  > [!NOTE]
  > パブリック ファイル システムを使用すると、ファイル システムで使用可能なすべてのファイルの一覧を取得し、メタデータにアクセスできます。 パブリック ファイル システムの場合、正確な URL がわかっているときのみ、そのファイルにアクセスできます。 詳細については、「[Restrict access to containers and blobs](http://msdn.microsoft.com/library/windowsazure/dd179354.aspx)」(コンテナーと BLOB へのアクセスを制限する) を参照してください (コンテナーと BLOB のルールはファイルとファイル システムで同様に機能します)。
 
* **クラスターに接続されていないストレージ アカウントのプライベート ファイル システム**では、WebHCat ジョブを送信するときにストレージ アカウントを定義しない限り、ファイル システム内のファイルにはアクセスできません。 この制限の理由については、この記事で後述します。

作成プロセスで定義されたストレージ アカウントとそのキーは、クラスター ノードの *%HADOOP_HOME%/conf/core-site.xml* に格納されます。 HDInsight の既定の動作では、*core-site.xml* ファイルに定義されたストレージ アカウントが使用されます。 この設定は、[Ambari](../../hdinsight/hdinsight-hadoop-manage-ambari.md) を使用して変更できます。

Hive、MapReduce、Hadoop ストリーミング、Pig など、複数の WebHCat ジョブを利用して、ストレージ アカウントの説明とそのメタデータを伝達できます。 (このアプローチは、現在、ストレージ アカウントについては Pig が対応していますが、メタデータについては対応していません)。詳細については、「 [Using an HDInsight Cluster with Alternate Storage Accounts and Metastores (代替のストレージ アカウントおよびメタストアでの HDInsight クラスターの使用)](http://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx)」を参照してください。

## <a id="benefits"></a>Azure Storage の利点

コンピューティング クラスターとストレージ リソースを同じ場所で併置しないとなるとパフォーマンスの低下が懸念されますが、これは、Azure リージョン内のストレージ アカウント リソースの近くにコンピューティング クラスターを作成することで軽減されます。高速ネットワークが整備されているので、コンピューティング ノードは Azure Storage 内のデータに効率的にアクセスできます。

HDFS ではなく、Azure Storage にデータを格納することにはいくつかの利点があります。

* **データの再使用と共有:** HDFS のデータはコンピューティング クラスター内に配置されます。 HDFS API を使用してデータを操作できるのは、コンピューティング クラスターへのアクセスが許可されているアプリケーションだけです。 Azure Storage 内のデータには、HDFS API または [Blob Storage REST API][blob-storage-restAPI] を使用してアクセスできます。 したがって、さまざまなアプリケーション (その他の HDInsight クラスターを含む) やツールを使用してデータの生成と利用ができます。

* **データのアーカイブ:** Azure Storage にデータを格納した場合、計算で使用する HDInsight クラスターを削除してもユーザー データは失われません。

* **データ ストレージ コスト:** コンピューティング クラスターは Azure Storage よりコストがかかるため、ネイティブ HDFS に長期間データを格納すると、Azure Storage にデータを格納した場合よりコストが高くなります。 さらに、コンピューティング クラスターを生成するたびにデータを読み込む必要がないので、データの読み込みコストも節約できます。

* **柔軟なスケールアウト:** HDFS は大規模なファイル システムを提供しますが、規模を拡張するにはクラスターに対して作成するノードの数を増やさなければならないので、作業が複雑になります。 一方、Azure Storage には柔軟なスケール機能がもともと備わっています。

* **geo レプリケーション:** Azure Storage データは、別の拠点に geo レプリケートできます。 この機能は、災害発生時に別の拠点にデータを回復でき、データの冗長性を高めますが、geo レプリケートした別拠点へのフェールオーバーのサポートはパフォーマンスに大きな影響を与え、追加コストが発生する可能性もあります。 このため、geo レプリケーションを利用するときは、追加コストがかかっても保護する価値のあるデータかどうかを十分に考慮してください。

* **データ ライフ サイクル管理:** すべてのファイル システムのすべてのデータが独自のライフサイクルを経過します。 多くの場合、データは、非常に価値があり頻繁にアクセスされる状態から始まり、徐々に価値とアクセス頻度が下がり、最終的にはアーカイブに保存されるか削除されます。 Azure Storage には、データのライフサイクル段階に合わせて適切にデータを階層化するデータの階層化機能とライフサイクル管理ポリシーが用意されています。

MapReduce の一部のジョブやパッケージでは中間結果が生成されますが、Azure Storage には保存したくない場合もあります。 このような場合、中間結果データをローカルの HDFS に保存できます。 実際、HDInsight では、Hive ジョブやその他のプロセスで生成される中間結果の一部で、HDFS のネイティブ実装 (DFS と呼ばれます) が使用されます。

> [!NOTE]
> ほとんどの HDFS コマンド (`ls`、`copyFromLocal`、`mkdir` など) は通常と同じように機能します。 ただし、`fschk` や `dfsadmin` など、DFS に固有のコマンドについては、Azure Storage 上で実行した場合に動作が異なります。

## <a name="create-an-data-lake-storage-file-system"></a>Data Lake Storage ファイル システムを作成する

ファイル システムを使用するには、まず、[Azure Storage アカウント][azure-storage-create]を作成します。 このプロセスの一環として、ストレージ アカウントを作成する Azure リージョンを指定します。 クラスターとストレージ アカウントは、同じリージョンに置く必要があります。 Hive メタストア SQL Server データベースと Oozie メタストア SQL Server データベースも、同じリージョンに配置する必要があります。

作成される各ファイル システムは、どこにあるとしても、Azure Data Lake Storage アカウント内のコンテナーに属します。 

既定の Data Lake Storage ファイル システムには、ジョブ履歴やログなどのクラスター固有の情報が格納されます。 既定の Data Lake Storage ファイル システムは複数の HDInsight クラスターと共有しないでください。 ジョブ履歴が破損する場合があります。 各クラスターで別のファイル システムを使用し、既定のストレージ アカウントではなく、関連するすべてのクラスターのデプロイメントで指定された、リンクされているストレージ アカウントに共有データを格納することをお勧めします。 リンクされているストレージ アカウントの構成の詳細については、[HDInsight クラスターの作成][hdinsight-creation]に関するページを参照してください。 ただし、元の HDInsight クラスターを削除した後でも既定のストレージ ファイル システムを再利用できます。 HBase クラスターでは、削除された HBase クラスターで使用される既定の BLOB コンテナーを使用して、新しい HBase クラスターを作成することで、HBase テーブルのスキーマとデータを保持できます。

[!INCLUDE [secure-transfer-enabled-storage-account](../../../includes/hdinsight-secure-transfer.md)]

### <a name="use-the-azure-portal"></a>Azure ポータルの使用

ポータルから HDInsight クラスターを作成するとき、ストレージ アカウントの詳細を提供するオプション (次のスクリーンショットを参照) を使用できます。 また、クラスターに関連付けられている追加のストレージ アカウントが必要かどうかを指定し、必要な場合は、追加のストレージとして利用できるストレージ オプションのいずれかから選択することもできます。

![HDInsight Hadoop 作成データ ソース](./media/use-hdi-cluster/create-storage-account.png)

> [!WARNING]
> HDInsight クラスター以外の場所で追加のストレージ アカウントを使用することはできません。

### <a name="use-azure-powershell"></a>Azure PowerShell の使用

[Azure PowerShell のインストールと構成][powershell-install]が完了している場合は、Azure PowerShell プロンプトで次のコードを使用して、ストレージ アカウントとコンテナーを作成できます。

[!INCLUDE [upgrade-powershell](../../../includes/hdinsight-use-latest-powershell.md)]

    $SubscriptionID = "<Your Azure Subscription ID>"
    $ResourceGroupName = "<New Azure Resource Group Name>"
    $Location = "WEST US 2"

    $StorageAccountName = "<New Azure Storage Account Name>"
    $containerName = "<New Azure Blob Container Name>"

    Connect-AzureRmAccount
    Select-AzureRmSubscription -SubscriptionId $SubscriptionID

    # Create resource group
    New-AzureRmResourceGroup -name $ResourceGroupName -Location $Location

    # Create default storage account
    New-AzureRmStorageAccount -ResourceGroupName $ResourceGroupName `
      -Name StorageAccountName `
      -Location $Location `
      -SkuName Standard_LRS `
      -Kind StorageV2 
      -HierarchialNamespace $True

    # Create default blob containers
    $storageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -StorageAccountName $StorageAccountName)[0].Value
    $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  
    New-AzureStorageContainer -Name $containerName -Context $destContext

> [!NOTE]
> コンテナーの作成は、Azure Data Lake Storage でファイル システムを作成することと同じ意味です。

### <a name="use-azure-cli"></a>Azure CLI の使用

[!INCLUDE [use-latest-version](../../../includes/hdinsight-use-latest-cli.md)]

[Azure CLI のインストールと構成](../../cli-install-nodejs.md)が完了している場合は、次のコマンドを使用して、ストレージ アカウントとコンテナーを作成できます。

```bash
az storage account create \
    --name <STORAGE_ACCOUNT_NAME> \
    --resource-group <RESOURCE_GROUP_NAME> \
    --location westus2 \
    --sku Standard_LRS \
    --kind StorageV2 \
    --Enable-hierarchical-namespace true
```

> [!NOTE]
> Data Lake Storage Gen2 のパブリック プレビュー期間中は、`--sku Standard_LRS` のみがサポートされています。

ストレージ アカウントの作成先となる地理的リージョンを指定するよう要求されます。 ストレージ アカウントは、HDInsight クラスターの作成を計画しているリージョンと同じリージョンに作成してください。

ストレージ アカウントを作成した後は、次のコマンドを使用して、ストレージ アカウント キーを取得します。

    azure storage account keys list <STORAGE_ACCOUNT_NAME>

コンテナーを作成するには、次のコマンドを使用します。

    azure storage container create <CONTAINER_NAME> --account-name <STORAGE_ACCOUNT_NAME> --account-key <STORAGE_ACCOUNT_KEY>

> [!NOTE]
> コンテナーの作成は、Azure Data Lake Storage でファイル システムを作成することと同じ意味です。

## <a name="address-files-in-azure-storage"></a>Azure Storage 内のファイルの指定

HDInsight から Azure Storage 内のファイルにアクセスするための URI スキームは次のとおりです。

    abfs[s]://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.widows.net/<PATH>

この URI スキームは、暗号化なしのアクセス (*abfs:* プレフィックス) と SSL で暗号化されたアクセス (*abfss*) に対応しています。 同じ Azure リージョン内のデータにアクセスする場合でも、できる限り *abfss* を使用することをお勧めします。

* &lt;FILE_SYSTEM_NAME&gt; には、Azure Data Lake Storage ファイル システムのパスを指定します。
* &lt;ACCOUNT_NAME&gt; には、Azure Storage アカウントを指定します。 完全修飾ドメイン名 (FQDN) を指定する必要があります。

    &lt;FILE_SYSTEM_NAME&gt; と &lt;ACCOUNT_NAME&gt; の値をどちらも指定しない場合は、既定のファイル システムが使用されます。 既定のファイル システム上にあるファイルに関しては、相対パスか絶対パスを使用できます。 たとえば、HDInsight クラスターに付属している *hadoop-mapreduce-examples.jar* ファイルは、次のいずれかのパスを使用して参照できます。
    
        abfs://myfilesystempath@myaccount.dfs.core.widows.net/example/jars/hadoop-mapreduce-examples.jar
        abfs:///example/jars/hadoop-mapreduce-examples.jar
        /example/jars/hadoop-mapreduce-examples.jar

> [!NOTE]
> HDInsight バージョン 2.1 クラスターと 1.6 クラスターでは、ファイル名は *hadoop-examples.jar* です。

* &lt;PATH&gt; は、ファイルまたはディレクトリの HDFS パス名です。

> [!NOTE]
> HDInsight の外部からファイルを操作する場合、ほとんどのユーティリティで ABFS 形式が認識されず、代わりに `example/jars/hadoop-mapreduce-examples.jar` などの基本的なパス形式が要求されます。
 
## <a name="use-additional-storage-accounts"></a>追加ストレージ アカウントの使用

HDInsight クラスターを作成しているときに、そのクラスターに関連付ける Azure ストレージ アカウントを指定します。 作成プロセス時またはクラスターが作成された後に、このストレージ アカウントに加えて、同じ Azure サブスクリプションか、別の Azure サブスクリプションに属するストレージ アカウントをさらに追加することもできます。 ストレージ アカウントをさらに追加する手順については、[HDInsight クラスターの作成](../../hdinsight/hdinsight-hadoop-provision-linux-clusters.md)に関するページをご覧ください。

> [!WARNING]
> HDInsight クラスター以外の場所で追加のストレージ アカウントを使用することはできません。

## <a name="next-steps"></a>次の手順

この記事では、HDInsight で HDFS と互換性のある Azure Storage を使う方法について説明しました。 このアプローチにより、収集したデータを長期にわたって格納できるスケーラブルなソリューションを構築できます。さらに HDInsight を使用すると、格納されている構造化データと非構造化データから有益な情報を得ることができます。

詳細については、次を参照してください。

* [Azure Data Lake Storage Gen2 用の ABFS Hadoop ファイルシステム ドライバー](abfs-driver.md)
* [Azure Data Lake Storage の概要](introduction.md)
* [Azure Data Lake Storage と Hadoop、Spark、Kafka などを使用して HDInsight クラスターを設定する](quickstart-create-connect-hdi-cluster.md)
* [distcp を使用して Azure Data Lake Storage にデータを取り込む](use-distcp.md)

[powershell-install]: /powershell/azureps-cmdlets-docs
[hdinsight-creation]: ../../hdinsight/hdinsight-hadoop-provision-linux-clusters.md

[blob-storage-restAPI]: http://msdn.microsoft.com/library/windowsazure/dd135733.aspx
[azure-storage-create]: ../common/storage-create-storage-account.md

[img-hdi-powershell-blobcommands]: ./media/use-hdi-cluster/HDI.PowerShell.BlobCommands.png
