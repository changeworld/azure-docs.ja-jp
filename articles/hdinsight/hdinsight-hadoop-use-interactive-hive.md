---
title: "Azure HDInsight での対話型 Hive の使用 | Microsoft Docs"
description: "HDInsight での対話型 Hive (Hive LLAP) の使用方法について説明します。"
keywords: 
services: hdinsight
documentationcenter: 
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 0957643c-4936-48a3-84a3-5dc83db4ab1a
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/06/2017
ms.author: jgao
ms.translationtype: HT
ms.sourcegitcommit: 190ca4b228434a7d1b30348011c39a979c22edbd
ms.openlocfilehash: 6c4e3d0e97eb9ad4500d684c7da3b19c5669994f
ms.contentlocale: ja-jp
ms.lasthandoff: 09/09/2017

---
# <a name="use-interactive-hive-with-hdinsight-preview"></a>HDInsight での対話型 Hive の使用 (プレビュー)
対話型 Hive (別名 Hive LLAP または [Live Long and Process](https://cwiki.apache.org/confluence/display/Hive/LLAP)) は新しい HDInsight [クラスター タイプ](hdinsight-hadoop-provision-linux-clusters.md#cluster-types)です。 対話型 Hive ではインメモリ キャッシュがサポートされるため、Hive クエリの速度と対話性が向上します。 

対話型 Hive クラスターは、Hadoop クラスターとは異なり、 Hive サービスのみが含まれます。 

> [!NOTE]
> MapReduce、Pig、Sqoop、Oozie、およびその他のサービスは、間もなくこのクラスター タイプから削除されます。
> 対話型 Hive クラスターの Hive サービスには、Ambari Hive View、Beeline、および Microsoft Hive Open Database Connectivity ドライバー (Hive ODBC) からのみアクセスできます。 Hive コンソール、Templeton、Azure コマンドライン ツール (Azure CLI)、Azure PowerShell からはアクセスできません。 
> 
> 

## <a name="create-an-interactive-hive-cluster"></a>対話型 Hive クラスターの作成
対話型の Hive クラスターは、Linux ベースのクラスターでのみ使用できます。 HDInsight クラスターの作成方法の詳細については、「[HDInsight での Linux ベースの Hadoop クラスターの作成](hdinsight-hadoop-provision-linux-clusters.md)」を参照してください。

## <a name="execute-hive-queries-from-interactive-hive"></a>対話型 Hive から Hive クエリを実行する
Hive クエリを実行するには、次のオプションがあります。

* Ambari Hive ビューを使って Hive を実行する。
  
    Hive ビューの使用方法の詳細については、「[HDInsight での Hive ビューと Hadoop の使用](hdinsight-hadoop-use-hive-ambari-view.md)」を参照してください。
* Beeline を使って Hive を実行する。
  
    HDInsight での Beeline の使用方法の詳細については、「[Beeline による HDInsight での Hive と Hadoop の使用](hdinsight-hadoop-use-hive-beeline.md)」を参照してください。
  
    Beeline はヘッド ノードまたは空のエッジ ノードから使用できます。 空のエッジ ノードから Beeline を使用することをお勧めします。 空のエッジ ノードを使って HDInsight クラスターを作成する方法の詳細については、「[HDInsight での空のエッジ ノードの使用](hdinsight-apps-use-edge-node.md)」を参照してください。
* Hive ODBC を使って Hive を実行する。
  
    Hive ODBC の使い方の詳細については、「[Microsoft Hive ODBC ドライバーを使用した Excel から Hadoop への接続](hdinsight-connect-excel-hive-odbc-driver.md)」を参照してください。

Java Database Connectivity (JDBC) 接続文字列は次の方法で調べることができます。

1. https://\<クラスター名\>.AzureHDInsight.net という URL を使用して Ambari にサインインします。
2. 左側のメニューで **[Hive]** を選択します。
3. URL をコピーするには、クリップボード アイコンを選択します。
   
   ![HDInsight Hadoop 対話型 Hive LLAP JDBC](./media/hdinsight-hadoop-use-interactive-hive/hdinsight-hadoop-use-interactive-hive-jdbc.png)

## <a name="next-steps"></a>次のステップ
* [HDInsight で対話型 Hive クラスターを作成する](hdinsight-hadoop-provision-linux-clusters.md)方法を学ぶ。
* [Beeline を使用して HDInsight で Hive クエリを実行する](hdinsight-hadoop-use-hive-beeline.md)方法を学ぶ。
* [Microsoft Hive ODBC ドライバーを使用して Excel から Hadoop に接続する](hdinsight-connect-excel-hive-odbc-driver.md)方法を学ぶ。


