---
title: "HBase のトラブルシューティング - Azure HDInsight | Microsoft Docs"
description: "リージョンのチェーンに生じた空洞のトラブルシューティング"
services: hdinsight
documentationcenter: 
author: nitinver
manager: ashitg
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 7/7/2017
ms.author: nitinver
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 29b1776d6b0c456515738aae3c5fd836c9a0295d
ms.contentlocale: ja-jp
ms.lasthandoff: 07/21/2017

---

# <a name="hbase-troubleshooting"></a>HBASE のトラブルシューティング

この記事では、Apache Ambari での HBASE ペイロードの操作に関連する主な問題とその解決策について説明します。

## <a name="how-do-i-run-hbck-command-reports-with-multiple-unassigned-regions"></a>複数の未割り当てリージョンで hbck コマンド レポートを実行する方法

HBase ユーザーが "hbase hbck" コマンドを実行する際の一般的な問題として、"複数のリージョンが未割り当て状態 (リージョン チェーンにおける空洞)" に見えることがあります。

この場合、HBase Master UI には、全リージョン サーバーにおけるアンバランスになっているリージョンの数が表示されます。 そのときユーザーは、"hbase hbck" コマンドを実行して、リージョン チェーンにおける空洞を把握できます。

ユーザーはまず、割り当てを修正する必要があります。なぜなら、こうしたオフラインのリージョンが空洞の原因になっている可能性があるためです。 

以下の手順に従って、未割り当てのリージョンを通常の状態に戻してください。

1. SSH を使用して HDInsight HBase クラスターにログインします。
1. "hbase zkcli" コマンドを実行して Zookeeper シェルに接続します。
1. "rmr /hbase/regions-in-transition" または "rmr /hbase-unsecure/regions-in-transition" コマンドを実行します。
1. "exit" コマンドを使用して "hbase zkcli" シェルを終了します。
1. Ambari UI を開き、Ambari から Active HBase Master サービスを再起動します。
1. "hbase hbck" コマンドを (オプションは付けずに) もう一度実行します。

手順 6. のコマンドの出力を見て、すべてのリージョンが割り当てられていることを確認します。

---

## <a name="how-do-i-fix-timeout-issues-with-hbck-commands-for-region-assignments"></a>リージョンの割り当てに関する hbck コマンドで生じるタイムアウトの問題を解決する方法

### <a name="probable-cause"></a>考えられる原因

この問題の原因として考えられるのは、複数のリージョンが長時間にわたって "移行中" 状態にあることです。 HBase Master UI には、これらのリージョンがオフラインとして表示される可能性があります。 移行を試みているリージョンが多数存在すると、HBase Master がタイムアウトする可能性があり、その結果それらのリージョンをオンライン状態に戻すことができなくなります。

### <a name="resolution-steps"></a>解決手順

hbck がタイムアウトする問題の解決手順を次に示します。

1. SSH を使用して HDInsight HBase クラスターにログインします。
1. "hbase zkcli" コマンドを実行して Zookeeper シェルに接続します。
1. "rmr /hbase/regions-in-transition" または "rmr /hbase-unsecure/regions-in-transition" コマンドを実行します。
1. "exit" コマンドを使用して "hbase zkcli" シェルを終了します。
1. Ambari UI を開き、Ambari から Active HBase Master サービスを再起動します。
1. もう一度 "hbase hbck -fixAssignments" コマンドを実行します。今回は、タイムアウトせずに実行されます。

## <a name="how-do-i-force-disable-hdfs-safe-mode-in-an-cluster"></a>クラスターで HDFS のセーフ モードを強制的に無効にする方法

### <a name="issue"></a>問題: 

HDInsight クラスター上のローカル HDFS がセーフ モードでスタックします。   

### <a name="detailed-description"></a>詳しい説明:

次の単純な HDFS コマンドの実行に失敗します。

```apache
hdfs dfs -D "fs.default.name=hdfs://mycluster/" -mkdir /temp
```

上記のコマンドを実行しようとしているときに発生したエラーは次のとおりです。

```apache
hdiuser@hn0-spark2:~$ hdfs dfs -D "fs.default.name=hdfs://mycluster/" -mkdir /temp
17/04/05 16:20:52 WARN retry.RetryInvocationHandler: Exception while invoking ClientNamenodeProtocolTranslatorPB.mkdirs over hn0-spark2.2oyzcdm4sfjuzjmj5dnmvscjpg.dx.internal.cloudapp.net/10.0.0.22:8020. Not retrying because try once and fail.
org.apache.hadoop.ipc.RemoteException(org.apache.hadoop.hdfs.server.namenode.SafeModeException): Cannot create directory /temp. Name node is in safe mode.
It was turned on manually. Use "hdfs dfsadmin -safemode leave" to turn safe mode off.
        at org.apache.hadoop.hdfs.server.namenode.FSNamesystem.checkNameNodeSafeMode(FSNamesystem.java:1359)
        at org.apache.hadoop.hdfs.server.namenode.FSNamesystem.mkdirs(FSNamesystem.java:4010)
        at org.apache.hadoop.hdfs.server.namenode.NameNodeRpcServer.mkdirs(NameNodeRpcServer.java:1102)
        at org.apache.hadoop.hdfs.protocolPB.ClientNamenodeProtocolServerSideTranslatorPB.mkdirs(ClientNamenodeProtocolServerSideTranslatorPB.java:630)
        at org.apache.hadoop.hdfs.protocol.proto.ClientNamenodeProtocolProtos$ClientNamenodeProtocol$2.callBlockingMethod(ClientNamenodeProtocolProtos.java)
        at org.apache.hadoop.ipc.ProtobufRpcEngine$Server$ProtoBufRpcInvoker.call(ProtobufRpcEngine.java:640)
        at org.apache.hadoop.ipc.RPC$Server.call(RPC.java:982)
        at org.apache.hadoop.ipc.Server$Handler$1.run(Server.java:2313)
        at org.apache.hadoop.ipc.Server$Handler$1.run(Server.java:2309)
        at java.security.AccessController.doPrivileged(Native Method)
        at javax.security.auth.Subject.doAs(Subject.java:422)
        at org.apache.hadoop.security.UserGroupInformation.doAs(UserGroupInformation.java:1724)
        at org.apache.hadoop.ipc.Server$Handler.run(Server.java:2307)
        at org.apache.hadoop.ipc.Client.getRpcResponse(Client.java:1552)
        at org.apache.hadoop.ipc.Client.call(Client.java:1496)
        at org.apache.hadoop.ipc.Client.call(Client.java:1396)
        at org.apache.hadoop.ipc.ProtobufRpcEngine$Invoker.invoke(ProtobufRpcEngine.java:233)
        at com.sun.proxy.$Proxy10.mkdirs(Unknown Source)
        at org.apache.hadoop.hdfs.protocolPB.ClientNamenodeProtocolTranslatorPB.mkdirs(ClientNamenodeProtocolTranslatorPB.java:603)
        at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
        at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
        at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
        at java.lang.reflect.Method.invoke(Method.java:498)
        at org.apache.hadoop.io.retry.RetryInvocationHandler.invokeMethod(RetryInvocationHandler.java:278)
        at org.apache.hadoop.io.retry.RetryInvocationHandler.invoke(RetryInvocationHandler.java:194)
        at org.apache.hadoop.io.retry.RetryInvocationHandler.invoke(RetryInvocationHandler.java:176)
        at com.sun.proxy.$Proxy11.mkdirs(Unknown Source)
        at org.apache.hadoop.hdfs.DFSClient.primitiveMkdir(DFSClient.java:3061)
        at org.apache.hadoop.hdfs.DFSClient.mkdirs(DFSClient.java:3031)
        at org.apache.hadoop.hdfs.DistributedFileSystem$24.doCall(DistributedFileSystem.java:1162)
        at org.apache.hadoop.hdfs.DistributedFileSystem$24.doCall(DistributedFileSystem.java:1158)
        at org.apache.hadoop.fs.FileSystemLinkResolver.resolve(FileSystemLinkResolver.java:81)
        at org.apache.hadoop.hdfs.DistributedFileSystem.mkdirsInternal(DistributedFileSystem.java:1158)
        at org.apache.hadoop.hdfs.DistributedFileSystem.mkdirs(DistributedFileSystem.java:1150)
        at org.apache.hadoop.fs.FileSystem.mkdirs(FileSystem.java:1898)
        at org.apache.hadoop.fs.shell.Mkdir.processNonexistentPath(Mkdir.java:76)
        at org.apache.hadoop.fs.shell.Command.processArgument(Command.java:273)
        at org.apache.hadoop.fs.shell.Command.processArguments(Command.java:255)
        at org.apache.hadoop.fs.shell.FsCommand.processRawArguments(FsCommand.java:119)
        at org.apache.hadoop.fs.shell.Command.run(Command.java:165)
        at org.apache.hadoop.fs.FsShell.run(FsShell.java:297)
        at org.apache.hadoop.util.ToolRunner.run(ToolRunner.java:76)
        at org.apache.hadoop.util.ToolRunner.run(ToolRunner.java:90)
        at org.apache.hadoop.fs.FsShell.main(FsShell.java:350)
mkdir: Cannot create directory /temp. Name node is in safe mode.
```

### <a name="probable-cause"></a>考えられる原因:

HDFS レプリケーション係数未満かそれに近いごく少数のノードにまで、HDInsight クラスターがスケールダウンされました。

### <a name="resolution-steps"></a>解決手順: 

- 次のコマンドを使用して、HDInsight クラスター上の HDFS の状態をレポートします。

```apache
hdfs dfsadmin -D "fs.default.name=hdfs://mycluster/" -report
```

```apache
hdiuser@hn0-spark2:~$ hdfs dfsadmin -D "fs.default.name=hdfs://mycluster/" -report
Safe mode is ON
Configured Capacity: 3372381241344 (3.07 TB)
Present Capacity: 3138625077248 (2.85 TB)
DFS Remaining: 3102710317056 (2.82 TB)
DFS Used: 35914760192 (33.45 GB)
DFS Used%: 1.14%
Under replicated blocks: 0
Blocks with corrupt replicas: 0
Missing blocks: 0
Missing blocks (with replication factor 1): 0

-------------------------------------------------
Live datanodes (8):

Name: 10.0.0.17:30010 (10.0.0.17)
Hostname: 10.0.0.17
Decommission Status : Normal
Configured Capacity: 421547655168 (392.60 GB)
DFS Used: 5288128512 (4.92 GB)
Non DFS Used: 29087272960 (27.09 GB)
DFS Remaining: 387172253696 (360.58 GB)
DFS Used%: 1.25%
DFS Remaining%: 91.85%
Configured Cache Capacity: 0 (0 B)
Cache Used: 0 (0 B)
Cache Remaining: 0 (0 B)
Cache Used%: 100.00%
Cache Remaining%: 0.00%
Xceivers: 2
Last contact: Wed Apr 05 16:22:00 UTC 2017
...

```
- 次のコマンドで、HDInsight クラスター上の HDFS の整合性をチェックすることもできます。

```apache
hdiuser@hn0-spark2:~$ hdfs fsck -D "fs.default.name=hdfs://mycluster/" /
```

```apache
Connecting to namenode via http://hn0-spark2.2oyzcdm4sfjuzjmj5dnmvscjpg.dx.internal.cloudapp.net:30070/fsck?ugi=hdiuser&path=%2F
FSCK started by hdiuser (auth:SIMPLE) from /10.0.0.22 for path / at Wed Apr 05 16:40:28 UTC 2017
....................................................................................................

....................................................................................................
..................Status: HEALTHY
 Total size:    9330539472 B
 Total dirs:    37
 Total files:   2618
 Total symlinks:                0 (Files currently being written: 2)
 Total blocks (validated):      2535 (avg. block size 3680686 B)
 Minimally replicated blocks:   2535 (100.0 %)
 Over-replicated blocks:        0 (0.0 %)
 Under-replicated blocks:       0 (0.0 %)
 Mis-replicated blocks:         0 (0.0 %)
 Default replication factor:    3
 Average block replication:     3.0
 Corrupt blocks:                0
 Missing replicas:              0 (0.0 %)
 Number of data-nodes:          8
 Number of racks:               1
FSCK ended at Wed Apr 05 16:40:28 UTC 2017 in 187 milliseconds


The filesystem under path '/' is HEALTHY
```

- 欠落しているブロックや破損しているブロック、レプリケート数不足のブロックが存在しないことがわかっている場合や、それらのブロックを無視できる場合は、次のコマンドを実行して、ネーム ノードのセーフ モードを解除します。

```apache
hdfs dfsadmin -D "fs.default.name=hdfs://mycluster/" -safemode leave
```


## <a name="how-do-i-fix-jdbc-or-sqlline-connectivity-issues-with-apache-phoenix"></a>Apache Phoenix との JDBC 接続または sqlline 接続の問題を解決する方法

### <a name="resolution-steps"></a>解決手順:

Apache Phoenix に接続するには、アクティブな Zookeeper ノードの IP を指定する必要があります。 sqlline.py で接続しようとしている Zookeeper サービスが稼働していることを確認してください。
1. HDInsight クラスターへの SSH ログインを実行します。
1. 次のコマンドを試します。
        
```apache
        "/usr/hdp/current/phoenix-client/bin/sqlline.py <IP of machine where Active Zookeeper is running"
```     
    Note: The IP of Active Zooker node can be identified from Ambari UI, by following the links to HBase -> "Quick Links" -> "ZK* (Active)" -> "Zookeeper Info". 

sqlline.py が Apache Phoenix に接続していて、タイムアウトしない場合は、次のコマンドを実行して、Apache Phoenix の可用性と正常性を確認してください。

```apache
        !tables
        !quit
```      
- 上記のコマンドが正常に動作すれば、問題は存在しません。 ユーザーによって指定された IP に誤りがある可能性があります。
   
    ただしコマンドが長時間一時停止した後で下記のエラーがスローされた場合は、引き続き以下のトラブルシューティング ガイドに従ってください。

```apache
        Error while connecting to sqlline.py (Hbase - phoenix) Setting property: [isolation, TRANSACTION_READ_COMMITTED] issuing: !connect jdbc:phoenix:10.2.0.7 none none org.apache.phoenix.jdbc.PhoenixDriver Connecting to jdbc:phoenix:10.2.0.7 SLF4J: Class path contains multiple SLF4J bindings. 
```

- ヘッドノード (hn0) から次のコマンドを実行して、phoenix SYSTEM.CATALOG テーブルの状態を診断します。

```apache
        hbase shell
        
        count 'SYSTEM.CATALOG'
```        
- このコマンドからは、次のようなエラーが返されます。 

```apache
        ERROR: org.apache.hadoop.hbase.NotServingRegionException: Region SYSTEM.CATALOG,,1485464083256.c0568c94033870c517ed36c45da98129. is not online on 10.2.0.5,16020,1489466172189) 
```
- 次の手順に従って、Ambari UI からすべての Zookeeper ノードの HMaster サービスを再起動します。

    a. HBase のサマリー セクションの [HBase] -> [Active HBase Master] リンクに移動します。 
    a. コンポーネント セクションで、HBase Master サービスを再起動します。
    a. 残りの "Standby HBase Master" サービスについて以上の手順を繰り返します。 

HBase Master サービスの動作が安定し復旧が完了するまでに最大 5 分程度かかる可能性があります。 数分待ってから sqlline.py コマンドを繰り返し、システム カタログ テーブルが正常に機能していることと、このテーブルに対してクエリを実行できることを確認します。 

"SYSTEM.CATALOG" テーブルが正常な状態に戻れば、Apache Phoenix に対する接続の問題は自動的に解決します。


## <a name="what-causes-a-master-server-to-fail-to-start"></a>マスター サーバーが起動に失敗する原因

### <a name="error"></a>エラー: 

Atomic renaming failure (アトミックな名前変更の失敗)

### <a name="detailed-description"></a>詳しい説明:

スタートアップ プロセス中、HMaster はさまざまな初期化ステップを実行します。たとえばデータを scratch (.tmp) フォルダーから data フォルダーに移動する処理や、WALs (Write Ahead Logs) フォルダーを参照して停止しているリージョン サーバーがないか確認する処理などです。 

そのいずれの状況においても、これらのフォルダーに対する基本的な "list" コマンドが実行されます。 いずれかのフォルダーに予期しないファイルが見つかった場合は、例外がスローされて起動に失敗します。  

### <a name="probable-cause"></a>考えられる原因:

そのファイルが作成された時間帯を特定し、前後にプロセスのクラッシュが発生していないか、リージョン サーバーのログで調べてみてください (この作業について不明な点があれば HBase 担当者に問い合わせてください)。 その情報は、このバグを回避しながら正常にプロセスをシャットダウンできる、より堅牢な機構を実現するために活用させていただきます。

### <a name="resolution-steps"></a>解決手順:

このような現象が発生した場合は、呼び出し履歴をチェックし、どのフォルダーが問題の原因になっているかを調べます (WALs フォルダー、.tmp フォルダーなど)。 その後、クラウド エクスプローラーまたは hdfs コマンドを使用して、問題のファイルを特定します。通常、*-renamePending.json ファイルが該当します (このファイルは、WASB ドライバーで Atomic Rename 操作を実装するために使用されるジャーナル ファイルです。その実装にバグがあるために、プロセスのクラッシュ時に、こうしたファイルが取り残されることがあります)。 このようなファイルは、クラウド エクスプローラーを使用して強制的に削除してください。 

また、この場所には $$$.$$$ という一時ファイルが存在することもあります。このファイルをクラウド エクスプローラーで表示することはできません。hdfs ls コマンドで表示する必要があります。 hdfs コマンド "hdfs dfs -rm /<the path>/\$\$\$.\$\$\$" を使用すると、このファイルを削除できます。  

この作業が完了すれば、HMaster はすぐに起動します。 

### <a name="error-no-server-address-listed-in"></a>エラー: No server address listed in (サーバーのアドレスがリストされていません) 

No server address listed in hbase: meta for region xxx (リージョン xxx の hbase: meta にサーバー アドレスがリストされていません)

### <a name="detailed-description"></a>詳しい説明:

ユーザーの Linux クラスターで、hbase: meta テーブルがオンラインになっていないことが問題になっています。 hbck を実行すると、"hbase: meta table replicaId 0 is not found on any region (hbase: meta テーブルの replicaId 0 がいずれのリージョンにも見つかりません)" が報告されます。 HBase の再起動後に、hmaster を初期化できない状態になります。 hmaster のログには、"No server address listed in hbase: meta for region hbase: backup <region name> (リージョン hbase: backup <リージョン名> の hbase: meta にサーバーのアドレスがリストされていません)" が記録されています。  

### <a name="resolution-steps"></a>解決手順:

- HBase シェルに次のように入力します (適宜、実際の値に置き換えてください)。  

```apache
> scan 'hbase:meta'  
```

```apache
> delete 'hbase:meta','hbase:backup <region name>','<column name>'  
```

- hbase: namespace のエントリを削除します (hbase: namespace テーブルをスキャンする際に同じエラーが報告される場合があるため)。

- Ambari UI からアクティブ HMaster を再起動し、HBase を実行状態にします。  

- HBase シェルで次のコマンドを実行し、オフライン状態のすべてのテーブルを立ち上げます。

```apache 
hbase hbck -ignorePreCheckPermission -fixAssignments 
```

### <a name="further-reading"></a>参考情報:

[HBase テーブルを処理できない](http://stackoverflow.com/questions/4794092/unable-to-access-hbase-table)


### <a name="error"></a>エラー:

HMaster times out with fatal exception like java.io.IOException: Timedout 300000ms waiting for namespace table to be assigned (java.io.IOException などの致命的な例外が発生すると HMaster はタイムアウトします。namespace テーブルの割り当てを 300000 ms 待ち、タイムアウトしました)

### <a name="detailed-description"></a>詳しい説明:

この問題は、明らかに多数のテーブルとリージョンが存在する場合に、HMaster サービスの再起動時にそれらをフラッシュしないと発生します。 再起動は、上記のメッセージが表示されて失敗します。 それ以外のエラーはありません。  

### <a name="probable-cause"></a>考えられる原因:

これは、HMaster の既知の "不具合" です。一般的なクラスターのスタートアップ タスクに時間がかかる場合があり、namespace テーブルが割り当てられていないことが原因で HMaster がシャットダウンする、というものです。この現象が発生するのは、フラッシュされていないデータが大量に存在し、5 分のタイムアウトでは間に合わない状況に限られます。
  
### <a name="resolution-steps"></a>解決手順:

- Ambari UI の [HBase] -> [Configs]/(構成/) に移動し、カスタム hbase-site.xml に次の設定を追加します。 

```apache
Key: hbase.master.namespace.init.timeout Value: 2400000  
```

- 必要なサービスを再起動します (主に HMaster サービス。場合によっては他の HBase サービスも含む)。  



## <a name="what-causes-a-restart-failure-on-a-region-server"></a>リージョン サーバーで再起動に失敗する原因

### <a name="probable-cause"></a>考えられる原因:

まず、このような現象は、ベスト プラクティスに従うことで防止できます。 HBase リージョン サーバーを再起動する予定があるときは、負荷の大きいワークロード アクティビティを一時停止することをお勧めします。 シャットダウンの進行中にアプリケーションがリージョン サーバーに接続し続けると、リージョン サーバーの再起動処理が数分遅れます。 また、「[HDInsight HBase: How to Improve HBase cluster restart time by Flushing tables (HDInsight HBase: テーブルをフラッシュして HBase クラスターの再起動時間を短縮する方法)](https://blogs.msdn.microsoft.com/azuredatalake/2016/09/19/hdinsight-hbase-how-to-improve-hbase-cluster-restart-time-by-flushing-tables/)」を参考にして、すべてのテーブルをフラッシュすることをお勧めします。

ユーザーが Ambari UI から HBase リージョン サーバーで再起動処理を開始した場合、 リージョン サーバーはすぐにシャットダウンされますが、起動に極端に時間がかかります。 

このとき水面下では次のことが起こっています。 

1. Ambari エージェントがリージョン サーバーに停止要求を送信します。
1. その後 Ambari エージェントは、リージョン サーバーが正常にシャットダウンされるのを 30 秒間待ちます。 
1. ユーザーのアプリケーションがリージョン サーバーへの接続を継続した場合、すぐにはシャットダウンされないため、先に 30 秒が経過してタイムアウトすることになります。 
1. 30 秒の待ち時間が経過すると、Ambari エージェントは強制終了 (kill -9) をリージョン サーバーに送信します。 そのようすは、Ambari エージェントのログ (各 worker ノードの /var/log/ ディレクトリ) で確認できます。

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
このように突然シャットダウンされるために、リージョン サーバーのプロセスが強制終了されても、プロセスに関連付けられているポートが解放されない場合があります。 この状況によって、リージョン サーバーを起動するときに AddressBindException が引き起こされる場合があります (以下のログを参照)。 そのようすは、リージョン サーバーの起動に失敗する worker ノードの region-server.log (/var/log/hbase ディレクトリ) で確認できます。 

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

### <a name="resolution-steps"></a>解決手順:

この現象が発生している間に、以下の回避策を試してみてください。 

- 再起動を開始する前に HBase リージョン サーバーの負荷を軽減します。 

- または (上記の手順でうまく行かない場合)、worker ノードで次のコマンドを使用してリージョン サーバーを手動で再起動します。

```apache
      sudo su - hbase -c "/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh stop regionserver"
      sudo su - hbase -c "/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh start regionserver"   
```



