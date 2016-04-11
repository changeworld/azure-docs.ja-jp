<properties
   pageTitle="HDInsight Premium での R Server の Azure Storage オプション | Azure"
   description="HDInsight Premium での R Server でユーザーが利用できるさまざまなストレージ オプションを紹介します。"
   services="HDInsight"
   documentationCenter=""
   authors="jeffstokes72"
   manager="paulettem"
   editor="cgronlun"
/>

<tags
   ms.service="HDInsight"
   ms.devlang="R"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-services"
   ms.date="03/28/2016"
   ms.author="jeffstok"
/>

# HDInsight Premium での R Server の Azure Storage オプション

HDI クラスター上の R Server は、データ、コード、分析の結果オブジェクトなどを保持するための手段として、Azure BLOB と (間もなく) Azure Data Lake ストレージの両方にアクセスできます。

HDInsight で Hadoop クラスターを作成するときに、Azure Storage アカウントを指定します。そのアカウントの特定の Blob Storage コンテナーが、作成したクラスターのファイル システム (つまり、Hadoop 分散ファイル システム (HDFS)) を保持するように指定されます。パフォーマンス上の理由から、HDInsight クラスターは、指定したプライマリ ストレージ アカウントと同じデータ センターに作成されます。詳細については、「[HDInsight での Azure BLOB ストレージの使用](hdinsight-hadoop-use-blob-storage.md "HDInsight での Azure BLOB ストレージの使用")」をご覧ください。


## 複数の Azure BLOB ストレージ アカウントの使用

必要に応じて、HDI クラスターを持つ複数の Azure ストレージ アカウントまたはコンテナーにアクセスすることができます。そのためには、クラスターの作成時に UI で追加のストレージ アカウントを指定し、次の手順に従ってそのストレージ アカウントを R で使用する必要があります。

1.	"storage1" というストレージ アカウント名で、既定のコンテナー "container1" を持つ HDInsight クラスターを作成するとします。さらに、追加のストレージ アカウント "storage2" も指定します。  
2.	そして "mycsv.csv" ファイルを "/share" ディレクトリにコピーし、このファイルに対して分析を実行します。  

    hadoop fs –mkdir /share hadoop fs –copyFromLocal myscsv.scv /share

3.	R コードで、名前ノードを "default" に設定し、ディレクトリと処理するファイルを設定します。

    myNameNode <- "default" myPort <- 0

  データの場所

    bigDataDirRoot <- "/share"  

  Spark コンピューティング コンテキストを定義します。

    mySparkCluster <- RxSpark(consoleOutput=TRUE)

  コンピューティング コンテキストを設定します。

    rxSetComputeContext(mySparkCluster)

  HDFS ファイル システムを定義します。

    hdfsFS <- RxHdfsFileSystem(hostName=myNameNode, port=myPort)

  分析する HDFS 内の入力ファイルを指定します。

    inputFile <-file.path(bigDataDirRoot,"mycsv.csv")
 
ディレクトリとファイルの参照は、いずれもストレージ アカウント wasb://container1@storage1.blob.core.windows.net を指します。これは、このストレージ アカウントが、HDInsight クラスターに関連付けられている**既定のストレージ アカウント**であるためです。

ここで、ストレージ アカウント名 "storage2" のコンテナー "container2" の "/private" ディレクトリにあるファイル "mySpecial.csv" を処理するとします。

R コードで、名前ノード参照を "storage2" ストレージ アカウントに変更します。

    myNameNode <- "wasb://container2@storage2.blob.core.windows.net"
    myPort <- 0

  データの場所

    bigDataDirRoot <- "/private"

  Spark コンピューティング コンテキストを定義します。

    mySparkCluster <- RxSpark(consoleOutput=TRUE)

  コンピューティング コンテキストを設定します。

    rxSetComputeContext(mySparkCluster)

  HDFS ファイル システムを定義します。

    hdfsFS <- RxHdfsFileSystem(hostName=myNameNode, port=myPort)

  分析する HDFS 内の入力ファイルを指定します。

    inputFile <-file.path(bigDataDirRoot,"mySpecial.csv")
 
ディレクトリとファイルの参照は、いずれもストレージ アカウント wasb://container2@storage2.blob.core.windows.net を指します。これは、このストレージ アカウントが、指定した**名前ノード**であるためです。

ストレージ アカウント "storage2" で /user/RevoShare/<SSH username> ディレクトリを構成する必要があることに注意してください。

    hadoop fs -mkdir wasb://container2@storage2.blob.core.windows.net/user
    hadoop fs -mkdir wasb://container2@storage2.blob.core.windows.net/user/RevoShare
    hadoop fs -mkdir wasb://container2@storage2.blob.core.windows.net/user/RevoShare/<RDP username>


## エッジ ノードでの Azure Files の使用 

エッジ ノードで使用できる便利なデータ ストレージ オプションとして、[Azure Files](../storage/storage-how-to-use-files-linux.md "Azure Files") があります。これを利用すれば、Azure Storage ファイル共有を Linux ファイル システムにマウントできます。これは、HDFS ではなくエッジ ノード上でネイティブ ファイル システムを使用する方がよい場合に、後で必要となる可能性があるデータ ファイル、R スクリプト、結果オブジェクトを格納するのに便利です。Azure Files を使用する大きな利点は、サポートされている OS (Win、Linux) が使用されている任意のシステム (たとえば、自分またはチームのだれかが所有している別の HDInsight クラスター、Azure VM、オンプレミスのシステム) でファイル共有をマウントおよび使用できることにあります。


## 次のステップ

ここでは、R Server を含む新しい HDInsight クラスターを作成する方法と、SSH セッションから R コンソールを使用する方法の基本を説明しました。次のリンクを使用して、HDInsight で R Server を使用するための他の方法を見てみましょう。

- [Hadoop での R Server の概要](hdinsight-hadoop-r-server-overview.md)
- [Hadoop での R Server の概要](hdinsight-hadoop-r-server-get-started.md)
- [HDInsight Premium への RStudio Server の追加](hdinsight-hadoop-r-server-install-r-studio.md)
- [HDInsight Premium での R Server のコンピューティング コンテキストのオプション](hdinsight-hadoop-r-server-compute-contexts.md)

<!---HONumber=AcomDC_0330_2016------>