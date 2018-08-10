---
title: Distcp を使用して Azure Data Lake Storage Gen2 プレビューにデータをコピーする| Microsoft Docs
description: Distcp ツールを使用して Data Lake Storage Gen2 プレビューとの間でデータをコピーする
services: storage
author: seguler
ms.component: data-lake-storage-gen2
ms.service: storage
ms.topic: how-to
ms.date: 06/27/2018
ms.author: seguler
ms.openlocfilehash: 065c4c4315bda209484cc1b2449980e55d4ac798
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2018
ms.locfileid: "39522698"
---
# <a name="use-distcp-to-copy-data-between-azure-storage-blobs-and-data-lake-storage-gen2-preview"></a>Distcp を使用して Azure Storage BLOB と Data Lake Storage Gen2 プレビュー の間でデータをコピーする

Azure Data Lake Storage Gen2 プレビューにアクセスできる HDInsight クラスターがある場合、[Distcp](https://hadoop.apache.org/docs/stable/hadoop-distcp/DistCp.html) などの Hadoop エコシステム ツールを使用し、HDInsight クラスター記憶域 (WASB) と Lake Storage Gen2 対応アカウント**との間**でデータをコピーできます。 この記事では、Distcp ツールの使用方法について説明します。

## <a name="prerequisites"></a>前提条件

* **Azure サブスクリプション**。 [Azure 無料試用版の取得](https://azure.microsoft.com/pricing/free-trial/)に関するページを参照してください。
* **Azure Data Lake Storage (プレビュー) 機能 が使用可能な Azure Storage アカウント**。 作成手順については、「[Azure Data Lake Storage Gen2 プレビュー ストレージ アカウントを作成する](quickstart-create-account.md)」を参照してください。
* Data Lake Storage アカウントにアクセスできる **Azure HDInsight クラスター**。 「[Use Azure Data Lake Storage Gen2 with Azure HDInsight clusters](use-hdi-cluster.md)」 (Azure HDInsight クラスターで Azure Data Lake Storage Gen2 を使用する) を参照してください。 クラスターのリモート デスクトップが有効になっていることを確認します。

## <a name="use-distcp-from-an-hdinsight-linux-cluster"></a>HDInsight Linux クラスターから Distcp を使用する

HDInsight クラスターには Distcp ユーティリティが付属しています。これを使用してさまざまなソースから HDInsight クラスターにデータをコピーできます。 Azure Blob Storage と Azure Data Lake Storage を一緒に使用するように HDInsight クラスターが構成されている場合は、Distcp ユーティリティを使用して、両者の間で簡単にデータをコピーすることもできます。 このセクションでは、Distcp ユーティリティを使用する方法について説明します。

1. デスクトップから、SSH を使用してクラスターに接続します。 「 [Linux ベースの HDInsight クラスターへの接続](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md)」を参照してください。 SSH プロンプトからコマンドを実行します。

2. Azure Storage BLOB (WASB) にアクセスできるかどうかを確認します。 次のコマンドを実行します。

        hdfs dfs –ls wasb://<CONTAINER_NAME>@<STORAGE_ACCOUNT_NAME>.blob.core.windows.net/

    出力には、ストレージ BLOB の内容の一覧が表示されます。

3. 同様に、クラスターから Data Lake Storage アカウントにアクセスできるかどうかを確認します。 次のコマンドを実行します。

        hdfs dfs -ls abfs://<FILE_SYSTEM_NAME>@<STORAGE_ACCOUNT_NAME>.dfs.core.windows.net/

    出力には、Data Lake Storage アカウントのファイル/フォルダーの一覧が表示されます。

4. Distcp を使用し、WASB から Data Lake Storage アカウントにデータをコピーします。

        hadoop distcp wasb://<CONTAINER_NAME>@<STORAGE_ACCOUNT_NAME>.blob.core.windows.net/example/data/gutenberg abfs://<FILE_SYSTEM_NAME>@<STORAGE_ACCOUNT_NAME>.dfs.core.windows.net/myfolder

    このコマンドを実行すると、Blob Storage の **/example/data/gutenberg/** フォルダーの内容が Data Lake Storage アカウントの **/myfolder** にコピーされます。

5. 同様に、Distcp を使用し、Data Lake Storage アカウントから Blob Storage (WASB) にデータをコピーします。

        hadoop distcp abfs://<FILE_SYSTEM_NAME>@<STORAGE_ACCOUNT_NAME>.dfs.core.windows.net/myfolder wasb://<CONTAINER_NAME>@<STORAGE_ACCOUNT_NAME>.blob.core.windows.net/example/data/gutenberg

    このコマンドを実行すると、Data Lake Store アカウントの **/myfolder** の内容が WASB の **/example/data/gutenberg/** フォルダーにコピーされます。

## <a name="performance-considerations-while-using-distcp"></a>DistCp を使用するときのパフォーマンスに関する考慮事項

DistCp の最小粒度は 1 ファイルであるため、DistCp を Data Lake Storage に対して最適化するうえで最も重要なのが、同時コピーの最大数を設定するパラメーターです。 同時コピー数を制御するには、コマンド ラインでマッパー (**m**) パラメーターの数を設定します。 このパラメーターで、データをコピーするときに使用されるマッパーの最大数を指定します。 既定値は 20 です。

**例**

    hadoop distcp wasb://<CONTAINER_NAME>@<STORAGE_ACCOUNT_NAME>.blob.core.windows.net/example/data/gutenberg abfs://<FILE_SYSTEM_NAME>@<STORAGE_ACCOUNT_NAME>.dfs.core.windows.net/myfolder -m 100

### <a name="how-do-i-determine-the-number-of-mappers-to-use"></a>使用するマッパーの数を特定するにはどうすればよいですか。

使用できるガイダンスがいくつかあります。

* **手順 1: 合計 YARN メモリを確認する** - 最初に DistCp ジョブを実行するクラスターで利用できる YARN メモリを確認します。 この情報は、クラスターに関連付けられている Ambari ポータルで確認できます。 YARN に移動し、[Configs (構成)] タブで YARN メモリを表示します。 合計 YARN メモリを取得するには、ノードあたりの YARN メモリと、クラスター内にあるのノードの数を掛けます。

* **手順 2: マッパーの数を計算する** - **m** の値は、合計 YARN メモリを YARN コンテナーのサイズで割った値と等しくなります。 YARN コンテナー サイズの情報は、Ambari ポータルでも入手できます。 YARN に移動し、[Configs] \(構成) タブを表示します。YARN コンテナーのサイズは、このウィンドウに表示されます。 マッパーの数 (**m**) を求めるための式を次に示します

        m = (number of nodes * YARN memory for each node) / YARN container size

**例**

クラスター内に 4 D14v2s ノードがあり、10 個のフォルダーから 10 TB のデータを転送しようしているとします。 各フォルダーに含まれるデータ量はさまざまです。また、各フォルダー内のファイル サイズも異なります。

* **合計 YARN メモリ**: Ambari ポータルから、D14 ノードの YARN メモリが 96 GB であることがわかります。 したがって、4 ノード クラスターの合計 YARN メモリは次のとおりです。 

        YARN memory = 4 * 96GB = 384GB

* **マッパーの数**: Ambari ポータルから、D14 ノードの YARN コンテナー サイズが 3072 であることがわかります。 したがって、マッパーの数は次のとおりです。

        m = (4 nodes * 96GB) / 3072MB = 128 mappers

他のアプリケーションがメモリを使用している場合は、クラスターの YARN メモリで DistCp に対する部分のみを使用するように選択できます。

### <a name="copying-large-datasets"></a>大規模なデータセットのコピー

移動するデータセットのサイズが大きい場合 (たとえば 1 TB を超える場合)、または、さまざまなフォルダーが多数ある場合は、複数の DistCp ジョブを使用することを検討します。 パフォーマンスの向上はないと思われますが、ジョブが分散されるため、あるジョブが失敗したときに、再開する必要があるのは、ジョブ全体ではなく特定のジョブのみになります。

### <a name="limitations"></a>制限事項

* DistCp では、パフォーマンスを最適化するために、同じようなサイズのマッパーを作成しようとします。 マッパー数を増やしても、必ずしもパフォーマンスが向上するとは限りません。

* DistCp では、ファイルあたりのマッパーの数が 1 つに制限されています。 したがって、ファイル数よりも多くのマッパーを持つことはできません。 DistCp がファイルに割り当てることができるマッパーの数は 1 つだけのため、大きなファイルのコピーに使用できる同時実行量は制限されます。

* 大きなファイルが少数だけ存在する場合は、潜在的な同時実行性を高めるために、そのファイルを 256 MB ファイル チャンクに分割します。 
