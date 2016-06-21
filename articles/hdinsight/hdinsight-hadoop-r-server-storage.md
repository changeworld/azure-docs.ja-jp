<properties
   pageTitle="HDInsight の R Server (プレビュー) の Azure Storage オプション | Azure"
   description="HDInsight の R Server (プレビュー) でユーザーが利用できるさまざまなストレージ オプションを紹介します。"
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
   ms.date="06/01/2016"
   ms.author="jeffstok"
/>

# HDInsight の R Server (プレビュー) の Azure Storage オプション

HDInsight の R Server (プレビュー) は、データ、コード、分析の結果オブジェクトなどを保持するための手段として、Azure BLOB と [Azure Data Lake ストレージ](https://azure.microsoft.com/services/data-lake-store/)の両方にアクセスできます。

HDInsight で Hadoop クラスターを作成するときに、Azure Storage アカウントを指定します。そのアカウントの特定の Blob Storage コンテナーが、作成したクラスターのファイル システム (つまり、Hadoop 分散ファイル システム (HDFS)) を保持するように指定されます。パフォーマンス上の理由から、HDInsight クラスターは、指定したプライマリ ストレージ アカウントと同じデータ センターに作成されます。詳細については、「[HDInsight での Azure BLOB ストレージの使用](hdinsight-hadoop-use-blob-storage.md "HDInsight での Azure BLOB ストレージの使用")」をご覧ください。


## 複数の Azure BLOB ストレージ アカウントの使用

必要に応じて、HDI クラスターを持つ複数の Azure ストレージ アカウントまたはコンテナーにアクセスすることができます。そのためには、クラスターの作成時に UI で追加のストレージ アカウントを指定し、次の手順に従ってそのストレージ アカウントを R で使用する必要があります。

1.	"storage1" というストレージ アカウント名で、既定のコンテナー "container1" を持つ HDInsight クラスターを作成するとします。さらに、追加のストレージ アカウント "storage2" も指定します。  
2.	そして "mycsv.csv" ファイルを "/share" ディレクトリにコピーし、このファイルに対して分析を実行します。  

````
hadoop fs –mkdir /share
hadoop fs –copyFromLocal myscsv.scv /share  
````

3.	R コードで、名前ノードを "default" に設定し、ディレクトリと処理するファイルを設定します。  

````
myNameNode <- "default"
myPort <- 0
````

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

## Azure Data Lake Store の使用

HDInsight アカウントで Azure Data Lake Store を使用するには、使用する各 Azure Data Lake Store にクラスター アクセスを許可する必要があります。次に上記で説明したセカンダリ Azure Storage アカウントを使用する場合と同じ方法で R スクリプトでストアを参照します。

## Azure Data Lake Store へのクラスター アクセスの追加

Azure Data Lake Store へのアクセスは、HDInsight クラスターに関連付けられている Azure Active Directory (AAD) サービス プリンシパルを使用して確立されます。HDInsight クラスターを作成する場合にサービス プリンシパルを追加するには、[データ ソース] タブから [クラスター AAD ID] オプションをクリックして、サービス プリンシパルの [新規作成] をクリックします。名前とパスワードを設定すると新しいタブが開き、サービス プリンシパルを Azure Data Lake Store と関連付けることができます。

Azure Data Lake Store へのアクセスを後から追加するには、Azure ポータルで Azure Data Lake Store を開いて、[データ エクスプローラー]、[アクセス] の順に移動します。サービス プリンシパルを作成して、"rkadl11" Azure Data Lake Store と関連付けるサンプル ダイアログを以下に示します。

![ADL Store のサービス プリンシパルを作成する 1](./media/hdinsight-hadoop-r-server-storage/hdinsight-hadoop-r-server-storage-adls-sp1.png)


![ADL Store のサービス プリンシパルを作成する 2](./media/hdinsight-hadoop-r-server-storage/hdinsight-hadoop-r-server-storage-adls-sp2.png)

## R Server と Azure Data Lake Store の使用
クラスターのサービス プリンシパルを使用して Azure Data Lake Store へのアクセスを付与すると、セカンダリの Azure Storage アカウントと同じ方法で、HDInsight での R Server で使用することができます。唯一の違いとして、wasb:// プレフィックスが adl:// などに変わります。

````
# point to the ADL store (e.g. ADLtest) 
myNameNode <- "adl://rkadl1.azuredatalakestore.net"
myPort <- 0

# Location of the data (assumes a /share directory on the ADL account) 
bigDataDirRoot <- "/share"  

# define Spark compute context
mySparkCluster <- RxSpark(consoleOutput=TRUE)

# set compute context
rxSetComputeContext(mySparkCluster)

# define HDFS file system
hdfsFS <- RxHdfsFileSystem(hostName=myNameNode, port=myPort)

# specify the input file in HDFS to analyze
inputFile <-file.path(bigDataDirRoot,"AirlineDemoSmall.csv")

# create Factors for days of the week
colInfo <- list(DayOfWeek = list(type = "factor",
               levels = c("Monday", "Tuesday", "Wednesday", "Thursday",
                          "Friday", "Saturday", "Sunday")))

# define the data source 
airDS <- RxTextData(file = inputFile, missingValueString = "M",
                    colInfo  = colInfo, fileSystem = hdfsFS)

# Run a linear regression
model <- rxLinMod(ArrDelay~CRSDepTime+DayOfWeek, data = airDS)
````

> [AZURE.NOTE] RevoShare ディレクトリを含む Azure Data Lake Storage アカウントを構成し、上記例のサンプル CSV ファイルを追加するコマンドを次に示します。

````
hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user 
hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user/RevoShare 
hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user/RevoShare/<user>

hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/share

hadoop fs -copyFromLocal /usr/lib64/R Server-7.4.1/library/RevoScaleR/SampleData/AirlineDemoSmall.csv adl://rkadl1.azuredatalakestore.net/share

hadoop fs –ls adl://rkadl1.azuredatalakestore.net/share
````

## エッジ ノードでの Azure Files の使用 

エッジ ノードで使用できる便利なデータ ストレージ オプションとして、[Azure Files](../storage/storage-how-to-use-files-linux.md "Azure Files") があります。これを利用すれば、Azure Storage ファイル共有を Linux ファイル システムにマウントできます。これは、HDFS ではなくエッジ ノード上でネイティブ ファイル システムを使用する方がよい場合に、後で必要となる可能性があるデータ ファイル、R スクリプト、結果オブジェクトを格納するのに便利です。Azure Files を使用する大きな利点は、サポートされている OS (Win、Linux) が使用されている任意のシステム (たとえば、自分またはチームのだれかが所有している別の HDInsight クラスター、Azure VM、オンプレミスのシステム) でファイル共有をマウントおよび使用できることにあります。


## 次のステップ

ここでは、R Server を含む新しい HDInsight クラスターを作成する方法と、SSH セッションから R コンソールを使用する方法の基本を説明しました。次のリンクを使用して、HDInsight で R Server を使用するための他の方法を見てみましょう。

- [Hadoop での R Server の概要](hdinsight-hadoop-r-server-overview.md)
- [Hadoop での R Server の使用開始](hdinsight-hadoop-r-server-get-started.md)
- [HDInsight Premium への RStudio Server の追加](hdinsight-hadoop-r-server-install-r-studio.md)
- [HDInsight Premium での R Server のコンピューティング コンテキストのオプション](hdinsight-hadoop-r-server-compute-contexts.md)

<!---HONumber=AcomDC_0608_2016-->