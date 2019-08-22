---
title: Azure HDInsight を使用した HBase のトラブルシューティング
description: HBase と Azure HDInsight の操作についてよく寄せられる質問とその回答を示します。
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.custom: hdinsightactive, seodec18
ms.topic: troubleshooting
ms.date: 08/16/2019
ms.openlocfilehash: cf44c27f51bf6312ef546b424646833f69ba0283
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2019
ms.locfileid: "69638430"
---
# <a name="troubleshoot-apache-hbase-by-using-azure-hdinsight"></a>Azure HDInsight を使用した Apache HBase のトラブルシューティング

Apache Ambari で Apache HBase ペイロードを操作するときに発生する主な問題とその解決策について説明します。

## <a name="how-do-i-fix-jdbc-or-sqlline-connectivity-issues-with-apache-phoenix"></a>Apache Phoenix との JDBC 接続または SQLLine 接続の問題を解決する方法

### <a name="resolution-steps"></a>解決手順

Apache Phoenix に接続するには、アクティブな Apache ZooKeeper ノードの IP アドレスを指定する必要があります。 sqlline.py が接続しようとしている Zookeeper サービスが稼働していることを確認します。
1. SSH を使用して HDInsight クラスターにサインインします。
2. 次のコマンドを入力します。
                
   ```apache
           "/usr/hdp/current/phoenix-client/bin/sqlline.py <IP of machine where Active Zookeeper is running"
   ```

   > [!Note] 
   > アクティブな Zookeeper ノードの IP アドレスは、Ambari UI から取得できます。 **[HBase]**  >  **[Quick Links]\(クイック リンク\)**  >  **[ZK\* (Active)]\(ZK (アクティブ)\)**  >  **[Zookeeper Info]\(Zookeeper 情報\)** に移動します。

3. sqlline.py が Phoenix に接続し、タイムアウトしていない場合は、次のコマンドを実行して Phoenix の可用性と正常性を検証します。

   ```apache
           !tables
           !quit
   ```      
4. 上記のコマンドが動作すれば、問題はありません。 ユーザーが指定した IP アドレスに誤りがあると考えられます。 ただし、コマンドが長時間一時停止した後で次のエラーが表示された場合は、手順 5. に進んでください。

   ```apache
           Error while connecting to sqlline.py (Hbase - phoenix) Setting property: [isolation, TRANSACTION_READ_COMMITTED] issuing: !connect jdbc:phoenix:10.2.0.7 none none org.apache.phoenix.jdbc.PhoenixDriver Connecting to jdbc:phoenix:10.2.0.7 SLF4J: Class path contains multiple SLF4J bindings. 
   ```

5. ヘッド ノード (hn0) から次のコマンドを実行して、Phoenix の SYSTEM.CATALOG テーブルの状態を診断します。

   ```apache
            hbase shell
                
           count 'SYSTEM.CATALOG'
   ```

   このコマンドは次のようなエラーを返します。 

   ```apache
           ERROR: org.apache.hadoop.hbase.NotServingRegionException: Region SYSTEM.CATALOG,,1485464083256.c0568c94033870c517ed36c45da98129. is not online on 10.2.0.5,16020,1489466172189) 
   ```
6. Apache Ambari UI で、次の手順に従って、すべての ZooKeeper ノード上で HMaster サービスを再起動します。

    1. HBase の **[Summary]\(概要\)** セクションで、 **[HBase]**  >  **[Active HBase Master]** に移動します。 
    2. **[Components]\(コンポーネント\)** セクションで、HBase Master サービスを再起動します。
    3. 残りのすべての **Standby HBase Master** サービスに対して、上記の手順を繰り返します。 

HBase Master サービスの動作が安定し、回復プロセスが完了するまでに最大 5 分かかることがあります。 数分後、sqlline.py コマンドを繰り返して、SYSTEM.CATALOG テーブルが正常に機能していることと、このテーブルに対してクエリを実行できることを確認します。 

SYSTEM.CATALOG テーブルが正常な状態に戻れば、Phoenix への接続の問題は自動的に解決されます。

## <a name="what-causes-a-restart-failure-on-a-region-server"></a>リージョン サーバーでの再起動が失敗する原因

### <a name="issue"></a>問題

リージョン サーバーでの再起動の失敗は、ベスト プラクティスに従うことで防ぐことができます。 HBase リージョン サーバーの再起動を予定しているときは、負荷の大きいワークロード アクティビティを一時停止することをお勧めします。 シャットダウンの進行中に、アプリケーションが引き続きリージョン サーバーに接続していると、リージョン サーバーの再起動操作が数分遅れます。 また、最初にすべてのテーブルをフラッシュしておくことをお勧めします。 テーブルをフラッシュする方法については、[HDInsight HBase:テーブルをフラッシュして Apache HBase クラスターの再起動時間を短縮する方法](https://web.archive.org/web/20190112153155/https://blogs.msdn.microsoft.com/azuredatalake/2016/09/19/hdinsight-hbase-how-to-improve-hbase-cluster-restart-time-by-flushing-tables/) に関するページを参照してください。

Apache Ambari UI から HBase リージョン サーバー上での再起動操作を開始すると、リージョン サーバーが停止しても、すぐには再起動していないことがすぐにわかります。 

バックグラウンドで次のことが行われています。 

1. Ambari エージェントがリージョン サーバーに停止要求を送信します。
2. Ambari エージェントは、リージョン サーバーが正常にシャットダウンするのを 30 秒間待ちます。 
3. アプリケーションが引き続きリージョン サーバーに接続していると、サーバーはすぐにはシャットダウンしません。 シャットダウンが発生する前に 30 秒が経過してタイムアウトになります。 
4. 30 秒が経過すると、Ambari エージェントは強制終了 (`kill -9`) コマンドをリージョン サーバーに送信します。 これは、(各ワーカー ノードの /var/log/ ディレクトリにある) Ambari エージェント ログで確認できます。

   ```apache
           2017-03-21 13:22:09,171 - Execute['/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh --config /usr/hdp/current/hbase-regionserver/conf stop regionserver'] {'only_if': 'ambari-sudo.sh  -H -E t
           est -f /var/run/hbase/hbase-hbase-regionserver.pid && ps -p `ambari-sudo.sh  -H -E cat /var/run/hbase/hbase-hbase-regionserver.pid` >/dev/null 2>&1', 'on_timeout': '! ( ambari-sudo.sh  -H -E test -
           f /var/run/hbase/hbase-hbase-regionserver.pid && ps -p `ambari-sudo.sh  -H -E cat /var/run/hbase/hbase-hbase-regionserver.pid` >/dev/null 2>&1 ) || ambari-sudo.sh -H -E kill -9 `ambari-sudo.sh  -H 
           -E cat /var/run/hbase/hbase-hbase-regionserver.pid`', 'timeout': 30, 'user': 'hbase'}
           2017-03-21 13:22:40,268 - Executing '! ( ambari-sudo.sh  -H -E test -f /var/run/hbase/hbase-hbase-regionserver.pid && ps -p `ambari-sudo.sh  -H -E cat /var/run/hbase/hbase-hbase-regionserver.pid` >
           /dev/null 2>&1 ) || ambari-sudo.sh -H -E kill -9 `ambari-sudo.sh  -H -E cat /var/run/hbase/hbase-hbase-regionserver.pid`'. Reason: Execution of 'ambari-sudo.sh su hbase -l -s /bin/bash -c 'export  
           PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/var/lib/ambari-agent ; /usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh --config /usr/hdp/curre
           nt/hbase-regionserver/conf stop regionserver was killed due timeout after 30 seconds
           2017-03-21 13:22:40,285 - File['/var/run/hbase/hbase-hbase-regionserver.pid'] {'action': ['delete']}
           2017-03-21 13:22:40,285 - Deleting File['/var/run/hbase/hbase-hbase-regionserver.pid']
   ```
   強制シャットダウンにより、リージョン サーバー プロセスが停止されても、プロセスに関連付けられているポートが解放されない場合があります。 この状況により、次のログに示すように、リージョン サーバーの起動時に AddressBindException が発生する可能性があります。 これは、リージョン サーバーが起動に失敗したワーカー ノードの /var/log/hbase ディレクトリにある region-server.log で確認できます。 

   ```apache

   2017-03-21 13:25:47,061 ERROR [main] regionserver.HRegionServerCommandLine: Region server exiting
   java.lang.RuntimeException: Failed construction of Regionserver: class org.apache.hadoop.hbase.regionserver.HRegionServer
   at org.apache.hadoop.hbase.regionserver.HRegionServer.constructRegionServer(HRegionServer.java:2636)
   at org.apache.hadoop.hbase.regionserver.HRegionServerCommandLine.start(HRegionServerCommandLine.java:64)
   at org.apache.hadoop.hbase.regionserver.HRegionServerCommandLine.run(HRegionServerCommandLine.java:87)
   at org.apache.hadoop.util.ToolRunner.run(ToolRunner.java:76)
   at org.apache.hadoop.hbase.util.ServerCommandLine.doMain(ServerCommandLine.java:126)
   at org.apache.hadoop.hbase.regionserver.HRegionServer.main(HRegionServer.java:2651)
        
   Caused by: java.lang.reflect.InvocationTargetException
   at sun.reflect.NativeConstructorAccessorImpl.newInstance0(Native Method)
   at sun.reflect.NativeConstructorAccessorImpl.newInstance(NativeConstructorAccessorImpl.java:57)
   at sun.reflect.DelegatingConstructorAccessorImpl.newInstance(DelegatingConstructorAccessorImpl.java:45)
   at java.lang.reflect.Constructor.newInstance(Constructor.java:526)
   at org.apache.hadoop.hbase.regionserver.HRegionServer.constructRegionServer(HRegionServer.java:2634)
   ... 5 more
        
   Caused by: java.net.BindException: Problem binding to /10.2.0.4:16020 : Address already in use
   at org.apache.hadoop.hbase.ipc.RpcServer.bind(RpcServer.java:2497)
   at org.apache.hadoop.hbase.ipc.RpcServer$Listener.<init>(RpcServer.java:580)
   at org.apache.hadoop.hbase.ipc.RpcServer.<init>(RpcServer.java:1982)
   at org.apache.hadoop.hbase.regionserver.RSRpcServices.<init>(RSRpcServices.java:863)
   at org.apache.hadoop.hbase.regionserver.HRegionServer.createRpcServices(HRegionServer.java:632)
   at org.apache.hadoop.hbase.regionserver.HRegionServer.<init>(HRegionServer.java:532)
   ... 10 more
        
   Caused by: java.net.BindException: Address already in use
   at sun.nio.ch.Net.bind0(Native Method)
   at sun.nio.ch.Net.bind(Net.java:463)
   at sun.nio.ch.Net.bind(Net.java:455)
   at sun.nio.ch.ServerSocketChannelImpl.bind(ServerSocketChannelImpl.java:223)
   at sun.nio.ch.ServerSocketAdaptor.bind(ServerSocketAdaptor.java:74)
   at org.apache.hadoop.hbase.ipc.RpcServer.bind(RpcServer.java:2495)
   ... 15 more
   ```

### <a name="resolution-steps"></a>解決手順

1. 再起動を開始する前に、HBase リージョン サーバーの負荷を軽減してみます。 
2. 代わりに (手順 1. では効果がない場合)、次のコマンドを使用して、ワーカー ノードでリージョン サーバーを手動で再起動してみます。

   ```apache
   sudo su - hbase -c "/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh stop regionserver"
   sudo su - hbase -c "/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh start regionserver"   
   ```

## <a name="next-steps"></a>次の手順

問題がわからなかった場合、または問題を解決できない場合は、次のいずれかのチャネルでサポートを受けてください。

* [Azure コミュニティのサポート](https://azure.microsoft.com/support/community/)を通じて Azure エキスパートから回答を得る。

* [@AzureSupport](https://twitter.com/azuresupport) (カスタマー エクスペリエンスを向上させるための Microsoft Azure の公式アカウント) に連絡する。 Azure コミュニティで適切なリソース (回答、サポート、エキスパートなど) につながる。

* さらにヘルプが必要な場合は、[Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) からサポート リクエストを送信できます。 メニュー バーから **[サポート]** を選択するか、 **[ヘルプとサポート]** ハブを開いてください。 詳細については、「[Azure サポート要求を作成する方法](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)」を参照してください。 サブスクリプション管理と課金サポートへのアクセスは、Microsoft Azure サブスクリプションに含まれていますが、テクニカル サポートはいずれかの [Azure のサポート プラン](https://azure.microsoft.com/support/plans/)を通して提供されます。
