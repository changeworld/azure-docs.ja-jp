<properties
   pageTitle="Distcp を使用して Data Lake Store と WASB の間でデータをコピーする| Microsoft Azure"
   description="Distcp ツールを使用して Azure Storage BLOB と Data Lake Store の間でデータをコピーする"
   services="data-lake-store"
   documentationCenter=""
   authors="nitinme"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="08/02/2016"
   ms.author="nitinme"/>

# Distcp を使用して Azure Storage BLOB と Data Lake Store の間でデータをコピーする

> [AZURE.SELECTOR]
- [DistCp を使用](data-lake-store-copy-data-wasb-distcp.md)
- [AdlCopy を使用](data-lake-store-copy-data-azure-storage-blob.md)


Data Lake Store アカウントにアクセスする HDInsight クラスターを作成した後、Distcp などの Hadoop エコシステム ツールを使用し、HDInsight クラスター ストレージ (WASB) と Data Lake Store アカウントの**間**でデータをコピーできます。この記事では、これを実現する方法について説明します。

##前提条件

この記事を読み始める前に、次の項目を用意する必要があります。

- **Azure サブスクリプション**。[Azure 無料試用版の取得](https://azure.microsoft.com/pricing/free-trial/)に関するページを参照してください。
- Data Lake Store のパブリック プレビューに対して、**Azure サブスクリプションを有効にする**。[手順](data-lake-store-get-started-portal.md#signup)を参照してください。
- Data Lake Store アカウントにアクセスできる **Azure HDInsight クラスター**「[Data Lake Store で HDInsight クラスターを作成する](data-lake-store-hdinsight-hadoop-use-portal.md)」を参照してください。クラスターのリモート デスクトップが有効になっていることを確認します。

## ビデオで速習する

DistCp を使用して Azure Storage BLOB と Data Lake Store の間でデータをコピーする方法については、[こちらのビデオ](https://mix.office.com/watch/1liuojvdx6sie)をご覧ください。

## リモート デスクトップ (Windows クラスター) または SSH (Linux クラスター) から Distcp を使用します。

HDInsight クラスターには Distcp ユーティリティが付属しています。これを使用してさまざまなソースから HDInsight クラスターにデータをコピーできます。追加の記憶域として Data Lake Store を使用する HDInsight クラスターを構成している場合、Distcp ユーティリティをすぐに使用し、Data Lake Store アカウント間でもデータをコピーできます。このセクションでは、Distcp ユーティリティを使用する方法について説明します。

1. Windows クラスターがある場合、Data Lake Store アカウントにアクセスする HDInsight クラスターにリモート接続します。手順については、「[RDP を使用したクラスターへの接続](../hdinsight/hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp)」を参照してください。クラスターのデスクトップから、Hadoop コマンド ラインを開きます。

	Linux クラスターがある場合は、SSH を使用してクラスターに接続します。「[Linux ベースの HDInsight クラスターへの接続](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md#connect-to-a-linux-based-hdinsight-cluster)」を参照してください。SSH プロンプトからコマンドを実行します。

3. Azure Storage BLOB (WASB) にアクセスできるかどうかを確認します。次のコマンドを実行します。

		hdfs dfs –ls wasb://<container_name>@<storage_account_name>.blob.core.windows.net/

	これにより、ストレージ BLOB の内容の一覧が表示されます。

4. 同様に、クラスターから Data Lake Store アカウントにアクセスできるかどうかを確認します。次のコマンドを実行します。

		hdfs dfs -ls adl://<data_lake_store_account>.azuredatalakestore.net:443/

	これにより、Data Lake Store アカウントのファイル/フォルダーの一覧が表示されます。

5. Distcp を使用し、WASB から Data Lake Store アカウントにデータをコピーします。

		hadoop distcp wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg adl://<data_lake_store_account>.azuredatalakestore.net:443/myfolder

	これにより、WASB の **/example/data/gutenberg/** フォルダーの内容が Data Lake Store アカウントの **/myfolder** にコピーされます。

6. 同様に、Distcp を使用し、Data Lake Store アカウントと WASB の間でデータをコピーします。

		hadoop distcp adl://<data_lake_store_account>.azuredatalakestore.net:443/myfolder wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg

	これにより、Data Lake Store アカウントの **/myfolder** の内容が WASB の **/example/data/gutenberg/** フォルダーにコピーされます。

## 関連項目

- [Azure Storage BLOB から Data Lake Store へのデータのコピー](data-lake-store-copy-data-azure-storage-blob.md)
- [Data Lake Store のデータをセキュリティで保護する](data-lake-store-secure-data.md)
- [Data Lake Store で Azure Data Lake Analytics を使用する](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [Data Lake Store で Azure HDInsight を使用する](data-lake-store-hdinsight-hadoop-use-portal.md)

<!---HONumber=AcomDC_0803_2016-->