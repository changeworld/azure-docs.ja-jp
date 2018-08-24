---
title: Azure HDInsight での対話型クエリの使用
description: HDInsight での対話型クエリ (Hive LLAP) の使用方法について説明します。
services: hdinsight
ms.service: hdinsight
author: jasonwhowell
ms.author: jasonh
editor: jasonwhowell
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/22/2018
ms.openlocfilehash: 73f7e523ed0abc7d0453096cf783761dd6a884ba
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/08/2018
ms.locfileid: "39628666"
---
# <a name="use-interactive-query-with-hdinsight"></a>HDInsight での対話型クエリの使用
対話型クエリ (別名 Hive LLAP または [Low Latency Analytical Processing](https://cwiki.apache.org/confluence/display/Hive/LLAP)) は、Azure HDInsight の[クラスターの一種](../hdinsight-hadoop-provision-linux-clusters.md#cluster-types)です。 対話型クエリではインメモリ キャッシュがサポートされるため、Hive クエリの速度と対話性が向上します。

[!INCLUDE [hdinsight-price-change](../../../includes/hdinsight-enhancements.md)] 

対話型クエリ クラスターは、Hadoop クラスターとは異なり、 Hive サービスのみが含まれます。 

> [!NOTE]
> 対話型クエリ クラスターの Hive サービスには、Ambari Hive View、Beeline、および Microsoft Hive Open Database Connectivity ドライバー (Hive ODBC) からのみアクセスできます。 Hive コンソール、Templeton、Azure コマンドライン ツール (Azure CLI)、Azure PowerShell からはアクセスできません。 
> 
> 

## <a name="create-an-interactive-query-cluster"></a>対話型クエリ クラスターの作成
HDInsight クラスターの作成について詳しくは、[HDInsight での Hadoop クラスターの作成](../hdinsight-hadoop-provision-linux-clusters.md)に関するページをご覧ください。 対話型クエリのクラスターの種類を選択します。

## <a name="execute-hive-queries-from-interactive-query"></a>対話型クエリから Hive クエリを実行する
Hive クエリを実行するには、次のオプションがあります。

* Power BI を使用する

    「[Azure HDInsight の Power BI で対話型クエリの Hive データを視覚化する](./apache-hadoop-connect-hive-power-bi-directquery.md)」をご覧ください。「[Azure HDInsight の Power BI でビッグ データを視覚化する](../hadoop/apache-hadoop-connect-hive-power-bi.md)」をご覧ください。
 
* Zeppelin を使用する

    「[Zeppelin を使用して Azure HDInsight で Hive クエリを実行する](../hdinsight-connect-hive-zeppelin.md)」をご覧ください。

* Visual Studio を使用する

    「[Data Lake Tools for Visual Studio を使用した Azure HDInsight への接続と Hive クエリの実行](../hadoop/apache-hadoop-visual-studio-tools-get-started.md#run-interactive-hive-queries)」をご覧ください。

* Visual Studio Code を使用する

    [Hive、LLAP、pySpark に Visual Studio Code を使用する](../hdinsight-for-vscode.md)方法に関するページをご覧ください。
* Ambari Hive ビューを使って Hive を実行する。
  
    [Azure HDInsight での Hive ビューと Hadoop の使用](../hadoop/apache-hadoop-use-hive-ambari-view.md)に関するページをご覧ください。
* Beeline を使って Hive を実行する。
  
    「[Beeline による HDInsight での Hive と Hadoop の使用](../hadoop/apache-hadoop-use-hive-beeline.md)」をご覧ください。
  
    Beeline はヘッド ノードまたは空のエッジ ノードから使用できます。 空のエッジ ノードから Beeline を使用することをお勧めします。 空のエッジ ノードを使って HDInsight クラスターを作成する方法の詳細については、「[HDInsight での空のエッジ ノードの使用](../hdinsight-apps-use-edge-node.md)」を参照してください。
* Hive ODBC を使って Hive を実行する。
  
    [Microsoft Hive ODBC ドライバーを使用した Excel から Hadoop への接続](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md)に関するページをご覧ください。

Java Database Connectivity (JDBC) 接続文字列は次の方法で調べることができます。

1. https://\<クラスター名\>.AzureHDInsight.net という URL を使用して Ambari にサインインします。
2. 左側のメニューで **[Hive]** を選択します。
3. URL をコピーするには、クリップボード アイコンを選択します。
   
   ![HDInsight Hadoop 対話型クエリ LLAP JDBC](./media/apache-interactive-query-get-started/hdinsight-hadoop-use-interactive-hive-jdbc.png)

## <a name="next-steps"></a>次の手順

* [HDInsight で対話型クエリ クラスターを作成する](../hdinsight-hadoop-provision-linux-clusters.md)方法を学ぶ。
* [Azure HDInsight の Power BI でビッグ データを視覚化する](../hadoop/apache-hadoop-connect-hive-power-bi.md)方法を学ぶ。
* [Zeppelin を使用して Azure HDInsight で Hive クエリを実行する](../hdinsight-connect-hive-zeppelin.md)方法を学ぶ。
* [Data Lake Tools for Visual Studio を使用して Hive クエリを実行する](../hadoop/apache-hadoop-visual-studio-tools-get-started.md#run-interactive-hive-queries)方法を学ぶ。
* [HDInsight Tools for Visual Studio Code を使用する](../hdinsight-for-vscode.md)方法を学ぶ。
* [HDInsight で Hive View と Hadoop を使用する](../hadoop/apache-hadoop-use-hive-ambari-view.md)方法を学ぶ。
* [Beeline を使用して HDInsight で Hive クエリを実行する](../hadoop/apache-hadoop-use-hive-beeline.md)方法を学ぶ。
* [Microsoft Hive ODBC ドライバーを使用して Excel から Hadoop に接続する](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md)方法を学ぶ。

