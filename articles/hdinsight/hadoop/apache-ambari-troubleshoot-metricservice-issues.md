---
title: Azure HDInsight での Apache Ambari メトリック コレクターの問題
description: Azure HDInsight での Apache Ambari メトリック コレクターの問題のトラブルシューティング
ms.service: hdinsight
ms.author: linjzhu
author: jacky-hdi
ms.topic: troubleshooting
ms.date: 11/03/2021
ms.openlocfilehash: 6fbfc4f47cb14966a7d03d9ef378bb03f519183c
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/09/2021
ms.locfileid: "132063923"
---
# <a name="apache-ambari-metrics-collector-issues-in-azure-hdinsight"></a>Azure HDInsight での Apache Ambari メトリック コレクターの問題

この記事では、Azure HDInsight クラスターと対話するときの問題のトラブルシューティング手順と可能な解決策について説明します。

## <a name="scenario-outofmemoryerror-or-unresponsive-apache-ambari-metrics-collector"></a>シナリオ: OutOfMemoryError または応答しない Apache Ambari メトリック コレクター

### <a name="issue"></a>問題

* Ambari UI で **"メトリック コレクター プロセス"** という重大なアラートが表示され、同様のメッセージが下に表示されることがあります。
    `Connection failed: timed out to <headnode fqdn>:6188`
* ヘッドノードで Ambari メトリック コレクターが頻繁に再起動される可能性があります
* 一部の Apache Ambari メトリックが表示されない場合があります。 たとえば、**NAMENODE** では、**Active/Standby** 状態ではなく **Started** と表示されます 


### <a name="cause"></a>原因

これらの問題の原因として、次のシナリオが考えられます。

#### <a name="an-out-of-memory-exception-happens-frequently"></a>メモリ不足の例外が頻繁に発生する

Apache Ambari メトリック コレクター ログ `/var/log/ambari-metrics-collector/ambari-metrics-collector.log*` を確認します。

```
19:59:45,457 ERROR [325874797@qtp-2095573052-22] log:87 - handle failed
java.lang.OutOfMemoryError: Java heap space

19:59:45,457 FATAL [MdsLoggerSenderThread] YarnUncaughtExceptionHandler:51 - Thread Thread[MdsLoggerSenderThread,5,main] threw an Error.  Shutting down now...
java.lang.OutOfMemoryError: Java heap space
```

#### <a name="busy-garbage-collection"></a>ガベージ コレクションがビジー状態である

1. Apache Ambari メトリック コレクターが hbase-ams ログ `/var/log/ambari-metrics-collector/hbase-ams-master-hn*.log` の **6188** でリッスンしていません

   ```
   2021-04-13 05:57:37,546 INFO  [timeline] timeline.HadoopTimelineMetricsSink: No live collector to send metrics to. Metrics to be sent will be discarded. This message will be skipped for the next 20 times.
   ```
   
2. Apache Ambari メトリック コレクター pid を取得し、GC パフォーマンスを確認します

   ```
   ps -fu ams | grep 'org.apache.ambari.metrics.AMSApplicationServer'
   ```
       
3. `jstat -gcutil <pid> 1000 100` を使用してガベージ コレクションの状態を確認します。 **FGCT** が短時間で増加している場合、Apache Ambari メトリック コレクターが完全な GC でビジー状態であり、他の要求を処理できないことを示しています。

### <a name="resolution"></a>解決策

これらのシナリオを回避するには、次のいずれかのオプションの使用を検討してください。

1. Apache Ambari メトリック コレクターのヒープ メモリを **[Ambari]**  >  **[Ambari メトリック コレクター]**  >  **[構成]**  >  **[メトリック コレクターのヒープ サイズ]** から増やします

   :::image type="content" source="./media/apache-ambari-troubleshoot-ams-issues/editing-ams-configuration-ambari.png" alt-text="Ambari メトリック サービス構成プロパティの編集のスクリーンショット。" border="true":::

2. 次の手順に従って、Ambari メトリック サービス (AMS) データをクリーンアップします。

   > [!NOTE]
   > AMS データをクリーンアップすると、使用可能なすべての AMS データの履歴が削除されます。 履歴が必要な場合、これが最適なオプションではない可能性があります。

   1.  Ambari ポータルにログインします  
    1.  AMS をメンテナンスに設定します  
    2.  Ambari から AMS を停止します  
    3.  **[AMS 構成]** 画面から次を特定します  
                1.  `hbase.rootdir` (既定値は `file:///mnt/data/ambari-metrics-collector/hbase`)  
                2.  `hbase.tmp.dir`(既定値は `/var/lib/ambari-metrics-collector/hbase-tmp`)  
   2. Apache Ambari メトリック コレクターが存在するヘッドノードに SSH 接続します。 スーパーユーザーの場合:
    1. `'hbase.tmp.dir'/zookeeper` のコンテンツを **バックアップ** して削除することで、AMS Zookeeper データを削除します
    2. `<hbase.tmp.dir>/phoenix-spool` フォルダーから Phoenix スプール ファイルを削除します 
    3. ***(この手順をスキップし、最初に AMS を再起動して、問題が解決したかどうかを確認することが大切です。AMS の起動にまだ失敗する場合は、この手順を試してください)***  
            AMS データは、上記で特定した `hbase.rootdir` に格納されています。 通常 OS コマンドを使用して、ファイルをバックアップして削除します。 例:     
            `tar czf /mnt/backupof-ambari-metrics-collector-hbase-$(date +%Y%m%d-%H%M%S).tar.gz /mnt/data/ambari-metrics-collector/hbase`  
   3.  Ambari を使用して AMS を再起動します。


## <a name="next-steps"></a>次の手順

[!INCLUDE [troubleshooting next steps](../includes/hdinsight-troubleshooting-next-steps.md)]
