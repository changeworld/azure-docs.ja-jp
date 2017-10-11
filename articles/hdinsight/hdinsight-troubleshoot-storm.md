---
title: "Azure HDInsight を使用して Storm のトラブルシューティング |Microsoft ドキュメント"
description: "Azure HDInsight での Apache Storm の使用に関する一般的な質問の回答を得ます。"
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
ms.openlocfilehash: 70a3d762431d90acdd6ed2a432a569f34d0ce447
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/29/2017
---
# <a name="troubleshoot-storm-by-using-azure-hdinsight"></a>Azure HDInsight を使用して Storm をトラブルシューティングします。

上位の問題とその解決策で Apache Ambari Apache Storm のペイロードを扱うためについて説明します。

## <a name="how-do-i-access-the-storm-ui-on-a-cluster"></a>クラスターの Storm UI をアクセスする方法
ブラウザーから Storm UI にアクセスするための 2 つのオプションがあります。

### <a name="ambari-ui"></a>Ambari UI
1. Ambari ダッシュ ボードに移動します。
2. サービスの一覧で選択**Storm**です。
3. **クイック リンク**メニューの  **Storm UI**です。

### <a name="direct-link"></a>直接リンク
次の URL で Storm UI にアクセスすることができます。

https://\<クラスター DNS 名\>/stormui

例:

 https://stormcluster.azurehdinsight.net/stormui

## <a name="how-do-i-transfer-storm-event-hub-spout-checkpoint-information-from-one-topology-to-another"></a>移す方法 Storm イベント ハブ注ぎ口チェックポイント情報を 1 つのトポロジ間

Azure Event Hubs から読み取られたトポロジを開発する場合は、HDInsight Storm イベント ハブを使用して spout .jar ファイルを新しいクラスターで同じ名前を持つトポロジを配置する必要があります。 ただし、以前のクラスターでの Apache 飼育にコミットされたチェックポイント データを保持する必要があります。

### <a name="where-checkpoint-data-is-stored"></a>チェックポイント データの保存場所
オフセットのチェックポイント データは、イベント ハブ注ぎ口飼育で 2 つのルート パス内で格納されます。
- 非トランザクション注ぎ口チェックポイントは、/eventhubspout に格納されます。
- トランザクションで、トランザクション注ぎ口チェックポイントのデータが格納されます。

### <a name="how-to-restore"></a>復元する方法
スクリプトおよび飼育からデータをエクスポートし、新しい名前を付けて飼育にデータをインポートするために使用するライブラリを取得するを参照してください。 [HDInsight Storm 例](https://github.com/hdinsight/hdinsight-storm-examples/tree/master/tools/zkdatatool-1.0)です。

Lib フォルダーには、エクスポート/インポート操作の実装を含む jar ファイルがあります。 バッシュ フォルダーには、以前のクラスターで飼育サーバーからデータをエクスポートし、それを新しいクラスターで飼育サーバーにインポートする方法を示すスクリプトの例があります。

実行、 [stormmeta.sh](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/tools/zkdatatool-1.0/bash/stormmeta.sh)エクスポートし、データをインポートする飼育ノードからスクリプト。 正しい Hortonworks Data Platform (HDP) バージョンにスクリプトを更新します。 (に取り組んでいるところ HDInsight に汎用的なこれらのスクリプトを作成します。 汎用スクリプトを実行できる任意のノードから変更することがなく、クラスターで、ユーザーがします。)

[エクスポート] コマンドは、設定した場所に (Azure Blob Storage または Azure Data Lake Store ストア) 内の Apache Hadoop 分散ファイル システム (HDFS) パスのメタデータを書き込みます。

### <a name="examples"></a>例

#### <a name="export-offset-metadata"></a>オフセットのメタデータをエクスポートします。
1. SSH を使用して、チェックポイントがエクスポートする必要がありますをオフセットするクラスターの飼育クラスターに移動します。
2. /Stormmetadta/zkdata HDFS パスに飼育オフセットのデータをエクスポートする (HDP バージョン文字列を更新すると) 後、次のコマンドを実行します。

    ```apache   
    java -cp ./*:/etc/hadoop/conf/*:/usr/hdp/2.5.1.0-56/hadoop/*:/usr/hdp/2.5.1.0-56/hadoop/lib/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/lib/*:/etc/failover-controller/conf/*:/etc/hadoop/* com.microsoft.storm.zkdatatool.ZkdataImporter export /eventhubspout /stormmetadata/zkdata
    ```

#### <a name="import-offset-metadata"></a>オフセットのメタデータをインポートします。
1. SSH を使用して、チェックポイントがエクスポートする必要がありますをオフセットするクラスターの飼育クラスターに移動します。
2. 飼育オフセットからデータをインポート HDFS パス/stormmetadata/zkdata 飼育サーバー、ターゲット クラスターに (HDP バージョン文字列を更新すると) 後、次のコマンドを実行します。

    ```apache
    java -cp ./*:/etc/hadoop/conf/*:/usr/hdp/2.5.1.0-56/hadoop/*:/usr/hdp/2.5.1.0-56/hadoop/lib/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/lib/*:/etc/failover-controller/conf/*:/etc/hadoop/* com.microsoft.storm.zkdatatool.ZkdataImporter import /eventhubspout /home/sshadmin/zkdata
    ```
   
#### <a name="delete-offset-metadata-so-that-topologies-can-start-processing-data-from-the-beginning-or-from-a-timestamp-that-the-user-chooses"></a>トポロジは、最初からまたはユーザーが選択したタイムスタンプからのデータ処理を開始できるように、オフセットのメタデータを削除します。
1. SSH を使用して、チェックポイントがエクスポートする必要がありますをオフセットするクラスターの飼育クラスターに移動します。
2. 現在のクラスター内のすべての飼育オフセット データを削除する (後、HDP バージョン文字列を更新すると)、次のコマンドを実行します。

    ```apache
    java -cp ./*:/etc/hadoop/conf/*:/usr/hdp/2.5.1.0-56/hadoop/*:/usr/hdp/2.5.1.0-56/hadoop/lib/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/lib/*:/etc/failover-controller/conf/*:/etc/hadoop/* com.microsoft.storm.zkdatatool.ZkdataImporter delete /eventhubspout
    ```

## <a name="how-do-i-locate-storm-binaries-on-a-cluster"></a>クラスター上の Storm バイナリを見つける方法
現在 HDP スタックの storm バイナリは、/usr/hdp/current/storm-client ではあります。 場所は、ヘッド ノードとの worker ノードの両方で同じです。
 
特定の HDP バージョン/usr/hdp (たとえば、/usr/hdp/2.5.0.1233/storm) で複数のバイナリである可能性があります。 /Usr/hdp/current/storm-client フォルダーは、クラスターで実行されている最新バージョンへのシンボリック リンクです。

詳細については、次を参照してください。 [SSH を使用して HDInsight クラスターへの接続](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix)と[Storm](http://storm.apache.org/)です。
 
## <a name="how-do-i-determine-the-deployment-topology-of-a-storm-cluster"></a>Storm クラスターのデプロイ トポロジを特定する方法
まず、HDInsight Storm でインストールされているすべてのコンポーネントを特定します。 Storm クラスターは、4 つのノードのカテゴリで構成されます。

* ゲートウェイ ノード
* ヘッド ノード
* 飼育ノード
* ワーカー ノード
 
### <a name="gateway-nodes"></a>ゲートウェイ ノード
ゲートウェイ ノードとは、ゲートウェイとリバース プロキシ サービスをアクティブな Ambari 管理サービスへのパブリック アクセスを有効にします。 Ambari リーダーの選択も処理します。
 
### <a name="head-nodes"></a>ヘッド ノード
Storm ヘッド ノードは、次のサービスを実行します。
* Nimbus
* Ambari サーバー
* Ambari メトリック サーバー
* Ambari メトリック コレクター
 
### <a name="zookeeper-nodes"></a>飼育ノード
HDInsight は、3 つのノード飼育クォーラムに付属します。 クォーラムのサイズは、固定されており、再構成することはできません。
 
飼育クォーラムを自動的に使用するクラスターの storm サービスが構成されます。
 
### <a name="worker-nodes"></a>ワーカー ノード
Storm ワーカー ノードは、次のサービスを実行します。
* Supervisor
* ワーカーの Java 仮想マシン (Jvm) のトポロジの実行
* Ambari エージェント
 
## <a name="how-do-i-locate-storm-event-hub-spout-binaries-for-development"></a>開発の Storm イベント ハブ注ぎ口バイナリを検出する方法
 
詳細については、トポロジと Storm イベント ハブ注ぎ口 .jar ファイルを使用して、次のリソースを参照してください。
 
### <a name="java-based-topology"></a>Java ベースのトポロジ
[Process events from Azure Event Hubs with Storm on HDInsight (Java) (HDInsight で Storm を使用して Azure Event Hubs のイベントを処理する (Java))](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-storm-develop-java-event-hub-topology)
 
### <a name="c-based-topology-mono-on-hdinsight-34-linux-storm-clusters"></a>C# のトポロジ (モノラル HDInsight 3.4 以降 Linux Storm クラスター上) のベース
[HDInsight で Storm を使用して Azure Event Hubs のイベントを処理する (C#)](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-storm-develop-csharp-event-hub-topology)
 
### <a name="latest-storm-event-hub-spout-binaries-for-hdinsight-35-linux-storm-clusters"></a>最新の Storm イベント ハブ spout バイナリ HDInsight 3.5 + Linux Storm クラスターを
HDInsight 3.5 + Linux Storm クラスターで動作する最新 Storm イベント ハブ注ぎ口を使用する方法については、mvn リポジトリを参照してください。 [readme ファイル](https://github.com/hdinsight/mvn-repo/blob/master/README.md)です。
 
### <a name="source-code-examples"></a>ソース コードの例
参照してください[例](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub)の Azure HDInsight クラスターで (Java で記述された) Apache Storm トポロジを使用して Azure Event Hub から読み取りし、書き込みをする方法です。
 
## <a name="how-do-i-locate-storm-log4j-configuration-files-on-clusters"></a>クラスター上の Storm Log4J 構成ファイルを見つける方法
 
Storm サービス用の Apache Log4J 構成ファイルを確認します。
 
### <a name="on-head-nodes"></a>ヘッド ノードで
/Usr hdp/から Nimbus Log4J 構成は読み取り\<HDP バージョン\>/storm/log4j2/cluster.xml です。
 
### <a name="on-worker-nodes"></a>Worker ノードの
/Usr hdp/からスーパーバイザー Log4J 構成は読み取り\<HDP バージョン\>/storm/log4j2/cluster.xml です。
 
/Usr hdp/からワーカー Log4J 構成ファイルの読み取りは\<HDP バージョン\>/storm/log4j2/worker.xml です。
 
例:/usr/hdp/2.6.0.2-76/storm/log4j2/cluster.xml/usr/hdp/2.6.0.2-76/storm/log4j2/worker.xml

