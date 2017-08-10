---
title: "STORM のトラブルシューティング - Azure HDInsight | Microsoft Docs"
description: "Storm のよくある質問を利用して、Azure HDInsight プラットフォーム上の Storm に関する一般的な質問に対する回答を示します。"
keywords: "Azure HDInsight, Storm, FAQ, トラブルシューティング ガイド, よくある質問"
services: Azure HDInsight
documentationcenter: na
author: raviperi
manager: 
editor: 
ms.assetid: 74E51183-3EF4-4C67-AA60-6E12FAC999B5
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 7/7/2017
ms.author: raviperi
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: b52462096ce977b94ff9514df650607b05e17030
ms.contentlocale: ja-jp
ms.lasthandoff: 07/21/2017

---

# <a name="storm-troubleshooting"></a>STORM のトラブルシューティング

この記事では、Apache Ambari での Storm ペイロードの操作に関連する主な問題とその解決策について説明します。

## <a name="how-do-i-access-storm-ui-on-a-cluster"></a>クラスターの Storm UI にアクセスする方法

### <a name="issue"></a>問題: 
ブラウザーから Storm UI にアクセスするには 2 つの方法があります。

#### <a name="ambari-ui"></a>Ambari UI
1. Ambari ダッシュ ボードに移動します。
1. 左のサービスの一覧から [Storm] を選択します。
1. [クイック リンク] ドロップ ダウン メニューから [Storm UI] オプションを選択します。

#### <a name="direct-link"></a>直接リンク
Storm UI に次の URL からアクセスできます。 

https://\<ClusterDnsName\>/stormui

例: https://stormcluster.azurehdinsight.net/stormui

## <a name="how-do-i-transfer-storm-eventhub-spout-checkpoint-information-from-one-topology-to-another"></a>Storm EventHub Spout チェックポイント情報の 1 つのトポロジから別のトポロジに転送する方法

### <a name="issue"></a>問題: 
HDInsight の Storm EventHub Spout jar を使用して EventHubs から読み取られたトポロジを開発するとき、新しいクラスター上で同じ名前でトポロジをデプロイできますが、zookeeeper にコミットされたチェックポイント データは古いクラスターに保持されます。

#### <a name="where-is-checkpoint-data-stored"></a>チェックポイント データの保存場所
オフセットのチェックポイント データは、EventHub によって 2つのルート パスの Zookeeper に保存されます。
- トランザクションではないスパウト チェックポイントは、/eventhubspout 下に保存されます。
- トランザクション スパウト チェックアウトのデータは、/transactional 下に保存されます。

#### <a name="how-to-restore"></a>復元方法
Zookeeper からデータをエクスポートして、新しい名前でインポートするスクリプトとライブラリは、https://github.com/hdinsight/hdinsight-storm-examples/tree/master/tools/zkdatatool-1.0 にあります。

Lib フォルダーには、インポート/エクスポート操作の実装を含む Jar ファイルがあります。
Bash フォルダー内には、古いクラスター上の Zookeeper サーバーからデータをエクスポートし、新しいクラスター上の Zookeeper サーバーにインポートしてもどす方法を示すサンプルスクリプトがあります。

データをインポート/エクスポートするには、[Stormmeta.sh](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/tools/zkdatatool-1.0/bash/stormmeta.sh)スクリプトを、Zookeeper ノードから実行する必要があります。
HDP バージョンの文字列を修正するよう、スクリプトを更新する必要があります。
(HDInsight では、これらのスクリプトをエンド ユーザーによる修正なしでクラスター内のどのノードからも実行できるよう、汎用スクリプトにするよう取り組んでいます)。

エクスポート コマンドは、指定した場所の HDFS パス (BLOB または ADSL ストア) にメタデータを書き込みます。

### <a name="examples"></a>例

##### <a name="export-offset-metadata"></a>オフセットのメタデータのエクスポート: 
1. チェックポイント オフセットをエクスポートする必要のある古いクラスター上の Zookeeper クラスターに SSH 接続します。
1. (hdp バージョンの文字列を更新した後) 次のコマンドを実行して Zookeeper オフセット データを /stormmetadta/zkdata の HDFS パスにエクスポートします。

```apache   
   java -cp ./*:/etc/hadoop/conf/*:/usr/hdp/2.5.1.0-56/hadoop/*:/usr/hdp/2.5.1.0-56/hadoop/lib/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/lib/*:/etc/failover-controller/conf/*:/etc/hadoop/* com.microsoft.storm.zkdatatool.ZkdataImporter export /eventhubspout /stormmetadata/zkdata
```

##### <a name="import-offset-metadata"></a>オフセットのメタデータのインポート
1. チェックポイント オフセットをエクスポートする必要のある古いクラスター上の Zookeeper クラスターに SSH 接続します。
1. (hdp バージョンの文字列を更新した後) 次のコマンドを実行して Zookeeper オフセット データを HDFS パス /stormmetadata/zkdata からターゲット クラスターの Zookeeper サーバーにインポートします。

```apache
   java -cp ./*:/etc/hadoop/conf/*:/usr/hdp/2.5.1.0-56/hadoop/*:/usr/hdp/2.5.1.0-56/hadoop/lib/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/lib/*:/etc/failover-controller/conf/*:/etc/hadoop/* com.microsoft.storm.zkdatatool.ZkdataImporter import /eventhubspout /home/sshadmin/zkdata
```
   
##### <a name="delete-offset-metadata-so-topologies-can-start-processing-data-from-either-beginning-or-timestamp-of-user-choice"></a>トポロジがデータの処理を開始 (先頭またはユーザー指定のタイムスタンプから) できるようオフセット メタデータを削除します。
1. チェックポイント オフセットをエクスポートする必要のある古いクラスター上の Zookeeper クラスターに SSH 接続します。
1. (hdp バージョンの文字列を更新した後) 次のコマンドを実行し、現在のクラスターのすべての Zookeeper オフセット データを削除します。

```apache
   java -cp ./*:/etc/hadoop/conf/*:/usr/hdp/2.5.1.0-56/hadoop/*:/usr/hdp/2.5.1.0-56/hadoop/lib/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/lib/*:/etc/failover-controller/conf/*:/etc/hadoop/* com.microsoft.storm.zkdatatool.ZkdataImporter delete /eventhubspout
```

## <a name="how-do-i-locate-storm-binaries-on-a-cluster"></a>クラスター上の Storm バイナリを見つける方法

### <a name="issue"></a>問題: 
 HDInsight クラスター上の Storm サービスのバイナリの場所を把握する

### <a name="resolution-steps"></a>解決手順: 

現在の HDP スタックの Storm バイナリは、/usr/hdp/current/storm-client にあります。

この場所は、ヘッド ノードやワーカー ノードと同じです。
 
/usr/hdp には、HDP 固有の複数のバイナリがある場合があります (例: /usr/hdp/2.5.0.1233/storm)。

ただし、/usr/hdp/current/storm-client は、クラスターで実行する最新バージョンにシンボリック リンクされます。

### <a name="further-reading"></a>参考資料: 
 [SSH を使用して HDInsight クラスターに接続する](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix) [Storm](http://storm.apache.org/)
 
## <a name="how-do-i-determine-the-deployment-topology-of-a-storm-cluster"></a>Storm クラスターのデプロイ トポロジを特定する方法
 
### <a name="issue"></a>問題: 
 
HDInsight Storm でインストールされているすべてのコンポーネントを特定する。
 
Storm クラスターは、4 つのカテゴリのノードで構成されます
1. ゲートウェイ
1. ヘッド ノード
1. Zookeeper ノード
1. ワーカー ノード
 
#### <a name="gateway-nodes"></a>ゲートウェイ ノード
アクティブな Ambari 管理サービスへのパブリック アクセスを可能にするゲートウェイであり、リバース プロキシ サービスで、Ambari リーダーの選択を処理します。
 
#### <a name="zookeeper-nodes"></a>Zookeeper ノード
HDInsight には、3 つの Zookeeper クォーラムがあります。
クォーラムのサイズは固定されており、構成を変更することはできません。
 
クラスターの Storm サービスは、ZK クォーラムを自動的に使用するよう構成されています。
 
#### <a name="head-nodes"></a>ヘッド ノード
Storm ヘッド ノードは、次のサービスを実行します。
1. Nimbus
1. Ambari サーバー
1. Ambari メトリック サーバー
1. Ambari メトリック コレクター
 
#### <a name="worker-nodes"></a>ワーカー ノード
 Storm ワーカー ノードは、次のサービスを実行します。
1. Supervisor
1. トポロジを実行するためのワーカーの JVM
1. Ambari エージェント
 
## <a name="how-do-i-locate-storm-eventhub-spout-binaries-for-development"></a>開発用に Storm-EventHub-Spout バイナリを見つける方法
 
### <a name="issue"></a>問題: 
Storm eventhub スパウト jar をトポロジとともに使用する方法の詳細はどこで確認できますか。
 
#### <a name="msdn-articles-on-how-to"></a>操作方法に関する MSDN 記事
 
##### <a name="java-based-topology"></a>Java ベースのトポロジ
https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-storm-develop-java-event-hub-topology
 
##### <a name="c-based-topology-using-mono-on-hdi-34-linux-storm-clusters"></a>C# ベースのトポロジ (Linux Storm クラスター上の HDI 3.4 以降の Mono を使用)
https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-storm-develop-csharp-event-hub-topology
 
##### <a name="latest-storm-eventhub-spout-binaries-for-hdi35-linux-storm-clusters"></a>HDI3.5 + Linux Storm クラスター用の最新の Storm EventHub スパウト バイナリ
HDI3.5+ Linux Storm クラスターで動作する最新の Storm eventhub スパウトの使用法については、https://github.com/hdinsight/mvn-repo/blob/master/README.md をご覧ください。
 
##### <a name="source-code-examples"></a>ソース コード例: 
https://github.com/Azure-Samples/hdinsight-java-storm-eventhub
 
## <a name="how-do-i-locate-storm-log4j-configuration-files-on-clusters"></a>クラスター上の Storm Log4J 構成ファイルを見つける方法
 
### <a name="issue"></a>問題: 
 
Storm サービスの Log4J 構成ファイルを特定する。
 
#### <a name="on-headnodes"></a>ヘッド ノード: 
Nimbus Log4J の構成は、/usr/hdp/<HDPVersion>/storm/log4j2/cluster.xml から読み取られます
 
#### <a name="worker-nodes"></a>ワーカー ノード
Supervisor's Log4J の構成は、/usr/hdp/<HDPVersion>/storm/log4j2/cluster.xml から読み取られます。
 
Worker Log4J の構成ファイルは、/usr/hdp/<HDPVersion>/storm/log4j2/worker.xml から読み取られます。
 
例: /usr/hdp/2.6.0.2-76/storm/log4j2/cluster.xml /usr/hdp/2.6.0.2-76/storm/log4j2/worker.xml







