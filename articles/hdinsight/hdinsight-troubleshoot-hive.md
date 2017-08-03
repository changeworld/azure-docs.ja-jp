---
title: "HIVE のトラブルシューティング - Azure HDInsight | Microsoft Docs"
description: "Hive のよくある質問を使用して、Azure HDInsight プラットフォームの Hive に関する一般的な質問に回答します。"
keywords: "Azure HDInsight、Hive、よくある質問、トラブルシューティング ガイド、一般的な質問"
services: Azure HDInsight
documentationcenter: na
author: dharmeshkakadia
manager: 
editor: 
ms.assetid: 15B8D0F3-F2D3-4746-BDCB-C72944AA9252
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 7/7/2017
ms.author: dharmeshkakadia
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 67b096c0585f7b73a57b784683944052d1cfaa33
ms.contentlocale: ja-jp
ms.lasthandoff: 07/21/2017

---

# <a name="hive-troubleshooting"></a>HIVE のトラブルシューティング

この記事では、Apache Ambari での HIVE ペイロードの操作に関連する主な問題とその解決策について説明します。

## <a name="how-do-i-export-a-hive-metastore-and-import-it-on-another-cluster"></a>Hive metastore をエクスポートして別のクラスターにインポートする方法

### <a name="issue"></a>問題:

Hive metastore をエクスポートして別の HDInsight クラスターにインポートする必要があります。  

### <a name="resolution-steps"></a>解決手順: 

1. Secure Shell (SSH) クライアントで HDInsight クラスターに接続します (以下の「参考資料」セクションを参照)。
1. metastore をエクスポートする HDInsight クラスターで次のコマンドを実行します。

```apache
for d in `hive -e "show databases"`; do echo "create database $d; use $d;" >> alltables.sql ; for t in `hive --database $d -e "show tables"` ; do ddl=`hive --database $d -e "show create table $t"`; echo "$ddl ;" >> alltables.sql ; echo "$ddl" | grep -q "PARTITIONED\s*BY" && echo "MSCK REPAIR TABLE $t ;" >> alltables.sql ; done; done
```

これにより、`allatables.sql` という名前のファイルが生成されます。

- 新しい HDInsight クラスターにファイル `alltables.sql` をコピーし、次のコマンドを実行します。

```apache
hive -f alltables.sql
```

このコードでは、新しいクラスターのデータ パスが元のクラスターのパスと同じであると想定しています。 同じでない場合は、生成された  
`alltables.sql` ファイルを手動で変更できます。

### <a name="further-reading"></a>参考資料:

- [SSH を使用して HDInsight クラスターに接続する](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix)


## <a name="how-do-locate-hive-logs-on-a-cluster"></a>クラスターにある Hive のログを特定する方法

### <a name="issue"></a>問題:

HDInsight クラスターにある Hive クライアント、metastore、および hiveserver のログを見つける必要があります。  

### <a name="resolution-steps"></a>解決手順: 

- Secure Shell (SSH) クライアントで HDInsight クラスターに接続します (以下の「参考資料」セクションを参照)。
- Hive クライアントのログは次の場所にあります。

```apache
/tmp/<username>/hive.log 
```

- Hive metastore のログは次の場所にあります。

```apache
/var/log/hive/hivemetastore.log 
```

- Hiveserver のログは次の場所にあります。

```apache
/var/log/hive/hiveserver2.log 
```

### <a name="further-reading"></a>参考資料:

- [SSH を使用して HDInsight クラスターに接続する](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix)

## <a name="how-do-i-launch-hive-shell-with-specific-configurations-on-a-cluster"></a>クラスター上の特定の構成でHive シェルを起動する方法

### <a name="issue"></a>問題:

起動時に HDInsight クラスター上の Hive シェルの構成をオーバーライドする、または構成を特定する必要があります。  

### <a name="resolution-steps"></a>解決手順: 

- Hive シェルの開始時に構成のキーと値のペアを指定します (以下の「参考資料」セクションを参照)。

```apache
hive -hiveconf a=b 
```

- 次のコマンドで Hive シェルの効果的な構成をすべて一覧表示します。

```apache
hive> set;
```

たとえば、デバッグ ログの記録が有効になっている Hive シェルを開始するには、コンソールで次のコマンドを使用します。
             
```apache
hive -hiveconf hive.root.logger=ALL,console 
```

### <a name="further-reading"></a>参考資料:

- [Hive 構成のプロパティ](https://cwiki.apache.org/confluence/display/Hive/Configuration+Properties)


## <a name="how-do-i-analyze-tez-dag-data-on-a-cluster-critical-path"></a>クラスターのクリティカル パスで Tez DAG データを分析する方法

### <a name="issue"></a>問題:

HDInsight クラスターのクリティカル パスで Tez Directed Acyclic Graph (DAG) 情報を分析する必要があります。

### <a name="resolution-steps"></a>解決手順:
 
- Secure Shell (SSH) クライアントで HDInsight クラスターに接続します (以下の「参考資料」セクションを参照)。

- コマンド プロンプトで次のコマンドを実行します。
   
```apache
hadoop jar /usr/hdp/current/tez-client/tez-job-analyzer-*.jar CriticalPath --saveResults --dagId <DagId> --eventFileName <DagData.zip> 
```

- 次のコマンドで、Tez DAG の分析に使用できる他のアナライザーの一覧を表示します。

```apache
hadoop jar /usr/hdp/current/tez-client/tez-job-analyzer-*.jar
```

サンプル プログラムは最初の引数として指定する必要があります。

有効なプログラム名: ContainerReuseAnalyzer: DAG のコンテナーの再利用を印刷します。CriticalPath: DAG のクリティカル パスを見つけます。LocalityAnalyzer: DAG のローカリティの詳細を印刷します。ShuffleTimeAnalyzer: DAG のシャッフル時間の詳細を分析します。SkewAnalyzer: DAG の傾斜の詳細を分析します。SlowNodeAnalyzer: DAG のノードの詳細を印刷します。SlowTaskIdentifier: DAG の遅いタスクの詳細を印刷します。SlowestVertexAnalyzer: DAG の最も遅い頂点の詳細を印刷します。SpillAnalyzer: DAG の巣ピルの詳細を印刷します。TaskConcurrencyAnalyzer: DAG のタスク同時実行の詳細を印刷します。VertexLevelCriticalPathAnalyzer: DAG の頂点レベルのクリティカル パスを見つけます。


### <a name="further-reading"></a>参考資料:

- [SSH を使用して HDInsight クラスターに接続する](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix)



## <a name="how-do-i-download-tez-dag-data-from-a-cluster"></a>クラスターから Tez DAG データをダウンロードする方法

#### <a name="issue"></a>問題:

HDInsight クラスターから Tez Directed Acyclic Graph (DAG) 情報をダウンロードする必要があります。

#### <a name="resolution-steps"></a>解決手順:

Tez DAG データは次の 2 つの方法で収集できます。

- コマンドラインから:
 
    Secure Shell (SSH) クライアントで HDInsight クラスターに接続します。 コマンド プロンプトで次のコマンドを実行します。
   
```apache
hadoop jar /usr/hdp/current/tez-client/tez-history-parser-*.jar org.apache.tez.history.ATSImportTool -downloadDir . -dagId <DagId> 
```

- Ambari Tez ビューを使用して:
   
Ambari に移動する --> Tez ビューに移動する (右上隅のタイル アイコンの裏に隠れています) --> 目的の DAG をクリックする --> ダウンロード データをクリックする。

#### <a name="further-reading"></a>参考資料:

1) [SSH を使用して HDInsight クラスターに接続する](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix)








