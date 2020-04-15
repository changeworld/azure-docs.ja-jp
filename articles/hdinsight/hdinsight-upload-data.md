---
title: HDInsight で Apache Hadoop ジョブのデータをアップロードする
description: HDInsight で Apache Hadoop ジョブのデータをアップロードしてアクセスする方法について説明します。 Azure クラシック CLI、Azure Storage Explorer、Azure PowerShell、Hadoop コマンド ライン、または Sqoop を使用します。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdiseo17may2017
ms.date: 04/07/2020
ms.openlocfilehash: c862633245e75613f9e4f9956486f872b96239f8
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/08/2020
ms.locfileid: "80875012"
---
# <a name="upload-data-for-apache-hadoop-jobs-in-hdinsight"></a>HDInsight で Apache Hadoop ジョブのデータをアップロードする

HDInsight では、Azure Storage および Azure Data Lake Storage 上で Hadoop 分散ファイル システム (HDFS) を利用できます。 このストレージには、Gen1 と Gen2 が含まれています。 Azure Storage と Data Lake Storage Gen1 および Gen2 は、HDFS 拡張機能として設計されています。 Hadoop 環境のすべてのコンポーネントを使用し、管理対象のデータを直接操作できます。 Azure Storage、Data Lake Storage Gen1、Gen2 は個別のファイル システムです。 システムは、データを格納し、そのデータを計算するために最適化されています。 Azure Storage を使用するメリットの詳細については、[HDInsight での Azure Storage の使用](hdinsight-hadoop-use-blob-storage.md)に関する記事をご覧ください。 また、[HDInsight での Data Lake Storage Gen1 の使用](hdinsight-hadoop-use-data-lake-store.md)および [HDInsight での Data Lake Storage Gen2 の使用](hdinsight-hadoop-use-data-lake-storage-gen2.md)に関する記事も参照してください。

## <a name="prerequisites"></a>前提条件

開始する前に、次の要件にご注意ください。

* Azure HDInsight クラスター。 手順については、[Azure HDInsight の概要](hadoop/apache-hadoop-linux-tutorial-get-started.md)または [HDInsight クラスターの作成](hdinsight-hadoop-provision-linux-clusters.md)に関するページを参照してください。
* 次の記事に関する知識
    * [HDInsight での Azure Storage の使用](hdinsight-hadoop-use-blob-storage.md)
    * [HDInsight での Data Lake Storage Gen1 の使用](hdinsight-hadoop-use-data-lake-store.md)
    * [HDInsight での Data Lake Storage Gen2 の使用](hdinsight-hadoop-use-data-lake-storage-gen2.md)  

## <a name="upload-data-to-azure-storage"></a>Azure Storage へのデータのアップロード

## <a name="utilities"></a>Utilities

Microsoft では、Azure Storage を操作する次のユーティリティを提供しています。

| ツール | Linux | OS X | Windows |
| --- |:---:|:---:|:---:|
| [Azure Portal](../storage/blobs/storage-quickstart-blobs-portal.md) |✔ |✔ |✔ |
| [Azure CLI](../storage/blobs/storage-quickstart-blobs-cli.md) |✔ |✔ |✔ |
| [Azure PowerShell](../storage/blobs/storage-quickstart-blobs-powershell.md) | | |✔ |
| [AzCopy](../storage/common/storage-use-azcopy-v10.md) |✔ | |✔ |
| [Hadoop コマンド](#hadoop-command-line) |✔ |✔ |✔ |

> [!NOTE]  
> Hadoop コマンドは、HDInsight クラスター上でのみ使用できます。 このコマンドでは、ローカル ファイル システムのデータを Azure Storage 内に読み込むことのみが可能です。  

## <a name="hadoop-command-line"></a>Hadoop コマンド ライン

Hadoop コマンド ラインは、クラスターのヘッド ノードに既にデータが存在している場合に、Azure Storage BLOB にデータを格納する際にのみ役立ちます。

Hadoop コマンドを使用するには、最初に [SSH または PuTTY](hdinsight-hadoop-linux-use-ssh-unix.md) を使用してヘッドノードに接続する必要があります。

接続したら、次の構文を使用してファイルをストレージにアップロードすることができます。

```bash
hadoop fs -copyFromLocal <localFilePath> <storageFilePath>
```

たとえば、`hadoop fs -copyFromLocal data.txt /example/data/data.txt` のように指定します。

HDInsight の既定のファイル システムは Azure Storage 内にあるため、/example/data/data.txt は実際には Azure Storage 内にあります。 このファイルは次のように表すこともできます。

    wasbs:///example/data/data.txt

or

    wasbs://<ContainerName>@<StorageAccountName>.blob.core.windows.net/example/data/davinci.txt

ファイルに使用するその他の Hadoop コマンドの一覧は、[https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html) をご覧ください。

> [!WARNING]  
> Apache HBase クラスターでは、データ書き込み時に使われる既定のブロック サイズは 256 KB です。 HBase API または REST API を使うときは問題なく動きますが、`hadoop` または `hdfs dfs` コマンドを使って 12 GB より大きいデータを書き込むとエラーになります。 詳細については、この記事の「[BLOB への書き込みに関するストレージ例外](#storage-exception-for-write-on-blob)」セクションをご覧ください。

## <a name="graphical-clients"></a>グラフィカル クライアント

Azure Storage を操作するためのグラフィカル インターフェイスを提供するアプリケーションもいくつかあります。 これらのアプリケーションの一部を次の一覧表に示します。

| Client | Linux | OS X | Windows |
| --- |:---:|:---:|:---:|
| [Microsoft Visual Studio Tools for HDInsight](hadoop/apache-hadoop-visual-studio-tools-get-started.md#explore-linked-resources) |✔ |✔ |✔ |
| [Azure 記憶域エクスプローラー](../storage/blobs/storage-quickstart-blobs-storage-explorer.md) |✔ |✔ |✔ |
| [`Cerulea`](https://www.cerebrata.com/products/cerulean/features/azure-storage) | | |✔ |
| [CloudXplorer](https://clumsyleaf.com/products/cloudxplorer) | | |✔ |
| [Microsoft Azure 用の CloudBerry Explorer](https://www.cloudberrylab.com/free-microsoft-azure-explorer.aspx) | | |✔ |
| [Cyberduck](https://cyberduck.io/) | |✔ |✔ |

## <a name="mount-azure-storage-as-local-drive"></a>Azure Storage をローカル ドライブとしてマウントする

「[Mount Azure Storage as Local Drive (Azure Storage をローカル ドライブとしてマウントする)](https://blogs.msdn.com/b/bigdatasupport/archive/2014/01/09/mount-azure-blob-storage-as-local-drive.aspx)」を参照してください。

## <a name="upload-using-services"></a>サービスを使用してアップロードする

### <a name="azure-data-factory"></a>Azure Data Factory

Azure Data Factory はフル マネージドのサービスで、データの保存、処理、移動の各サービスを効率的で適用性のある信頼性の高いデータ生成パイプラインとして構成します。

|ストレージの種類|ドキュメント|
|----|----|
|Azure BLOB ストレージ|[Copy data to or from Azure Blob storage by using Azure Data Factory (Azure Data Factory を使用して、Azure Blob ストレージをコピー先、またはコピー元にして、データをコピーする)](../data-factory/connector-azure-blob-storage.md)|
|Azure Data Lake Storage Gen1|[Azure Data Factory を使用して Azure Data Lake Storage Gen1 との間でデータをコピーする](../data-factory/connector-azure-data-lake-store.md)|
|Azure Data Lake Storage Gen2 |[Load data into Azure Data Lake Storage Gen2 with Azure Data Factory (Azure Data Factory を使用して Azure Data Lake Storage Gen2 内にデータを読み込む)](../data-factory/load-azure-data-lake-storage-gen2.md)|

### <a name="apache-sqoop"></a>Apache Sqoop

Sqoop は、Hadoop とリレーショナル データベース間でデータを転送するためのツールです。 これを使用して、SQL Server、MySQL、Oracle などのリレーショナル データベース管理システム (RDBMS) からデータをインポートします。 その後、Hadoop 分散ファイル システム (HDFS) にデータをインポートし、 MapReduce または Hive を使用して Hadoop のデータを変換し、そのデータを RDBMS へ取り込んだりできます。

詳細については、[HDInsight での Sqoop の使用](hadoop/hdinsight-use-sqoop.md)に関するページを参照してください。

### <a name="development-sdks"></a>開発 SDK

Azure Storage には、次のプログラミング言語で Azure SDK を使用してアクセスすることもできます。

* .NET
* Java
* Node.js
* PHP
* Python
* Ruby

Azure SDK のインストールの詳細については、 [Azure のダウンロード](https://azure.microsoft.com/downloads/)

## <a name="troubleshooting"></a>トラブルシューティング

### <a name="storage-exception-for-write-on-blob"></a>BLOB への書き込みに関するストレージ例外

**現象**:`hadoop` または `hdfs dfs` コマンドを使って HBase クラスターで 12 GB 以上のファイルを書き込むと、次のエラーが発生する可能性があります。

    ERROR azure.NativeAzureFileSystem: Encountered Storage Exception for write on Blob : example/test_large_file.bin._COPYING_ Exception details: null Error Code : RequestBodyTooLarge
    copyFromLocal: java.io.IOException
            at com.microsoft.azure.storage.core.Utility.initIOException(Utility.java:661)
            at com.microsoft.azure.storage.blob.BlobOutputStream$1.call(BlobOutputStream.java:366)
            at com.microsoft.azure.storage.blob.BlobOutputStream$1.call(BlobOutputStream.java:350)
            at java.util.concurrent.FutureTask.run(FutureTask.java:262)
            at java.util.concurrent.Executors$RunnableAdapter.call(Executors.java:471)
            at java.util.concurrent.FutureTask.run(FutureTask.java:262)
            at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1145)
            at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:615)
            at java.lang.Thread.run(Thread.java:745)
    Caused by: com.microsoft.azure.storage.StorageException: The request body is too large and exceeds the maximum permissible limit.
            at com.microsoft.azure.storage.StorageException.translateException(StorageException.java:89)
            at com.microsoft.azure.storage.core.StorageRequest.materializeException(StorageRequest.java:307)
            at com.microsoft.azure.storage.core.ExecutionEngine.executeWithRetry(ExecutionEngine.java:182)
            at com.microsoft.azure.storage.blob.CloudBlockBlob.uploadBlockInternal(CloudBlockBlob.java:816)
            at com.microsoft.azure.storage.blob.CloudBlockBlob.uploadBlock(CloudBlockBlob.java:788)
            at com.microsoft.azure.storage.blob.BlobOutputStream$1.call(BlobOutputStream.java:354)
            ... 7 more

**原因**:HDInsight クラスター上の HBase では、Azure Storage に書き込むときに既定のブロック サイズは 256 KB です。 HBase API または REST API では問題ありませんが、`hadoop` または `hdfs dfs` コマンドライン ユーティリティを使うとエラーになります。

**解決方法**:`fs.azure.write.request.size` を使ってさらに大きいブロック サイズを指定します。 この変更は、`-D` パラメーターを使うことで、利用状況に応じて指定できます。 `hadoop` コマンドでこのパラメーターを使う例を次に示します。

```bash
hadoop -fs -D fs.azure.write.request.size=4194304 -copyFromLocal test_large_file.bin /example/data
```

Apache Ambari を使うことで、`fs.azure.write.request.size` の値をグローバルに増やすこともできます。 Ambari Web UI で値を変更するには、次の手順を使えます。

1. ブラウザーで、クラスターの Ambari Web UI に移動します。 URL は `https://CLUSTERNAME.azurehdinsight.net` です。`CLUSTERNAME` はクラスターの名前です。 プロンプトが表示されたら、クラスターの管理者名とパスワードを入力します。
2. 画面の左側にある **[HDFS]** を選び、 **[Configs (構成)]** を選びます。
3. **[Filter... (フィルター...)]** フィールドに「`fs.azure.write.request.size`」と入力します。
4. 値を 262144 (256 KB) から新しい値に変更します。 たとえば、4194304 (4 MB) に変更します。

    ![Ambari Web UI で値を変更する画像](./media/hdinsight-upload-data/hbase-change-block-write-size.png)

Ambari の使用について詳しくは、「[Apache Ambari Web UI を使用した HDInsight クラスターの管理](hdinsight-hadoop-manage-ambari.md)」をご覧ください。

## <a name="next-steps"></a>次のステップ

ここでは、HDInsight にデータを取り込む方法を説明しました。次の記事でデータの分析について学習してください。

* [Azure HDInsight の概要](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [プログラムによる Apache Hadoop ジョブの送信](hadoop/submit-apache-hadoop-jobs-programmatically.md)
* [HDInsight での Apache Hive の使用](hadoop/hdinsight-use-hive.md)
* [HDInsight での Apache Pig の使用](./use-pig.md)
