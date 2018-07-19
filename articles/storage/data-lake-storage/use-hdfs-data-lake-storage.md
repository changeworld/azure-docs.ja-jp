---
title: Azure Data Lake Storage Gen2 プレビューで HDFS CLI を使用する
description: Data Lake Storage Gen2 プレビューの HDFS CLI の概要
services: storage
documentationcenter: ''
author: artemuwka
manager: jahogg
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2018
ms.author: artek
ms.component: data-lake-storage-gen2
ms.openlocfilehash: 591d8ea7670bf9b29450695ee7cbee5fa39baaac
ms.sourcegitcommit: 756f866be058a8223332d91c86139eb7edea80cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2018
ms.locfileid: "37344724"
---
# <a name="using-the-hdfs-cli-with-data-lake-storage-gen2"></a>Data Lake Storage Gen2 で HDFS CLI を使用する

Azure Data Lake Storage Gen2 プレビューでは、[Hadoop Distributed File System (HDFS)](http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html) の場合と同様にデータの管理やアクセスを行うことができます。 HDInsight クラスターをアタッチする場合でも、Azure Databricks を使用して Apache Spark ジョブを実行し、Azure Data Lake Storage Gen2 に保存されているデータを分析する場合でも、コマンド ライン インターフェイス (CLI) を使用して、読み込まれたデータを取得したり、操作したりできます。 以下の記事では、[Azure Storage チームによる Azure Storage Explorer と Azure portal のサポート追加への取り組み](https://azure.microsoft.com/roadmap/)の間に選択できるオプションについて説明します。

## <a name="hdfs-cli-with-hdinsight"></a>HDInsight での HDFS CLI

HDInsight では、それぞれのコンピューティング ノードにローカルに割り当てられている分散ファイル システムにアクセスします。 このファイル システムにアクセスするには、Hadoop でサポートされる HDFS などのファイル システムと直接対話するシェルを使用する必要があります。 よく使用されるコマンドと、役立つリソースへのリンクを以下に示します。

>[!IMPORTANT]
>HDInsight クラスターの課金は、クラスターが作成されると開始し、クラスターが削除されると停止します。 課金は分単位なので、クラスターを使わなくなったら必ず削除してください ([クラスターの削除](../../hdinsight/hdinsight-delete-cluster.md)方法を確認ください)。 ただし、HDInsight クラスターを削除しても、Azure Data Lake Storage Gen2 に保存されているデータは削除されません。

ファイルまたはディレクトリの一覧を取得するには:

    hdfs dfs -ls <args>
ディレクトリを作成するには:

    hdfs dfs -mkdir [-p] <paths>
ファイルまたはディレクトリを削除するには:

    hdfs dfs -rm [-skipTrash] URI [URI ...]


ここで例として、HDInsight Hadoop クラスターを Linux 上に配置してみましょう。 HDFS CLI を使用するには、最初に[サービスに対してリモート アクセス](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-linux-information#remote-access-to-services)を確立する必要があります。 [SSH](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix) を選択した場合、PowerShell のサンプル コードは、次のようになります。
```PowerShell
#Connect to the cluster via SSH.
ssh sshuser@clustername-ssh.azurehdinsight.net
#Execute basic HDFS commands. Display the hierarchy.
hdfs dfs -ls /
#Create a sample directory.
hdfs dfs -mkdir /samplefolder
```

接続文字列は、Azure portal 内の HDInsight クラスター ブレードの "SSH + Cluster login" セクションで確認できます。 SSH 資格情報は、クラスターの作成時に指定されています。

HDFS CLI の詳細については、[公式ドキュメント](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html)、および「[HDFS Permissions Guide](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html)」 (HDFS 権限ガイド) を参照してください。

## <a name="hdfs-cli-with-azure-databricks"></a>Azure Databricks での HDFS CLI

Databricks は、Databricks REST API 上に構築された使いやすい CLI を備えています。 このオープンソース プロジェクトは、[GitHub](https://github.com/databricks/databricks-cli) 上でホストされています。 よく使用されるコマンドを次に示します。

ファイルまたはディレクトリの一覧を取得するには:

    dbfs ls [-l]
ディレクトリを作成するには:

    dbfs mkdirs
ファイルを削除するには:

    dbfs rm [-r]

Databricks と対話するもう 1 つの方法は、ノートブックです。 ノートブックには 1 次言語がありますが、セルの先頭で言語のマジック コマンド %language を指定することにより、複数の言語を混在させることができます。 特に、%sh を使用すると、前述した HDInsight の例の場合と同様に、ノートブックでシェル コードを実行できます。

ファイルまたはディレクトリの一覧を取得するには:

    %sh ls <args>
ディレクトリを作成するには:

    %sh mkdir [-p] <paths>
ファイルまたはディレクトリを削除するには:

    %sh rm [-skipTrash] URI [URI ...]

Azure Databricks で Spark クラスターを起動した後、新しいノートブックを作成します。 ノートブックのスクリプトの例は、次のようになります。

    #Execute basic HDFS commands invoking the shell. Display the hierarchy.
    %sh ls /
    #Create a sample directory.
    %sh mkdir /samplefolder

Databricks CLI の詳細については、[公式ドキュメント](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html)を参照してください。 ノートブックの詳細については、このドキュメントの「[notebooks](https://docs.azuredatabricks.net/user-guide/notebooks/index.html)」 (ノートブック) セクションを参照してください。

## <a name="next-steps"></a>次の手順

- [Create an HDInsight cluster with Azure Data Lake Storage Gen2](./quickstart-create-connect-hdi-cluster.md) (Azure Data Lake Storage Gen2 で HDInsight クラスターを作成する)
- [Use an Azure Data Lake Storage Gen2 capable account in Azure Databricks](./quickstart-create-databricks-account.md) (Azure Databricks で Azure Data Lake Storage Gen2 対応アカウントを使用する) 