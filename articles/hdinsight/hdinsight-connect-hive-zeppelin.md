---
title: Azure HDInsight で Apache Zeppelin を使用して Apache Hive クエリを実行する
description: Apache Zeppelin を使用して Apache Hive クエリを実行する方法について説明します。
keywords: hdinsight,hadoop,hive,対話型クエリ,LLAP
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,
ms.topic: conceptual
ms.date: 11/05/2018
ms.author: hrasheed
ms.openlocfilehash: 75ec0e17e9866d2cd3420ff6ecf648bf22a8ae8e
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/08/2018
ms.locfileid: "51277955"
---
# <a name="use-apache-zeppelin-to-run-apache-hive-queries-in-azure-hdinsight"></a>Azure HDInsight で Apache Zeppelin を使用して Apache Hive クエリを実行する 

HDInsight 対話型クエリ クラスターには、対話型 Hive クエリを実行するために使用できる Apache Zeppelin ノートブックが含まれています。 この記事では、Azure HDInsight で Apache Zeppelin を使用して Apache Hive クエリを実行する方法について説明します。 

## <a name="prerequisites"></a>前提条件
この記事の操作を始める前に、以下を用意する必要があります。

* **HDInsight 対話型クエリ クラスター**。 HDInsight クラスターの作成については、「[クラスターの作成](hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster)」を参照してください。  クエリの種類では対話型クエリを必ず選択してください。 

## <a name="create-a-apache-zeppelin-note"></a>Apache Zeppelin Note を作成する

1. 次の URL を参照します。

        https://CLUSTERNAME.azurehdinsight.net/zeppelin
    **CLUSTERNAME** をクラスターの名前に置き換えます。

2. Hadoop ユーザー名とパスワードを入力します。 Zeppelin ページで、新しいノートを作成するか、既存のノートを開きます。 HiveSample には、サンプル Hive クエリが含まれています。  

    ![HDInsight 対話型クエリ Zeppelin](./media/hdinsight-connect-hive-zeppelin/hdinsight-hive-zeppelin.png)
3. **[Note の新規作成]** をクリックします。
4. 次の値を入力または選択します。

    - Note 名: ノートの名前を入力します。
    - 既定のインタープリター: **[JDBC]** を選択します。

5. **[Note の作成]** をクリックします。
6. 次の Hive クエリを実行します。

        %jdbc(hive)
        show tables

    ![HDInsight 対話型クエリ Zeppelin でクエリを実行する](./media/hdinsight-connect-hive-zeppelin/hdinsight-hive-zeppelin-query.png)

    最初の行の **%jdbc(hive)** ステートメントは、Hive JDBC インタープリターを使用するようにノートブックに指示します。

    クエリは *hivesampletable* という名前の Hive テーブルを返します。

    次は、hivesampletable に対して実行できるさらに 2 つの Hive クエリです。 

        %jdbc(hive)
        select * from hivesampletable limit 10

        %jdbc(hive)
        select ${group_name}, count(*) as total_count
        from hivesampletable
        group by ${group_name=market,market|deviceplatform|devicemake}
        limit ${total_count=10}

    従来の Hive と比較すると、クエリ結果が返されるまでの時間が短くなりました。


## <a name="next-steps"></a>次の手順
この記事では、Power BI を使用して HDInsight からデータを視覚化する方法について学習しました。  詳細については、次の記事を参照してください。

* [Azure HDInsight の Microsoft Power BI で Hive データを視覚化する](hadoop/apache-hadoop-connect-hive-power-bi.md)。
* [Azure HDInsight の Power BI で対話型クエリの Hive データを視覚化する](./interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md)。
* [Microsoft Hive ODBC Driver を使用して Excel を HDInsight に接続する](hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md)。
* [Power Query を使用して Excel を Hadoop に接続する](hadoop/apache-hadoop-connect-excel-power-query.md)。
* [Data Lake Tools for Visual Studio を使用して Azure HDInsight に接続し、Hive クエリを実行する](hadoop/apache-hadoop-visual-studio-tools-get-started.md)。
* [Azure HDInsight Tool for Visual Studio Code の使用](hdinsight-for-vscode.md)。
* [HDInsight にデータをアップロードする](./hdinsight-upload-data.md)。
