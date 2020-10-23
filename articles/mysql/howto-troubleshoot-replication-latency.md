---
title: レプリケーション待機時間のトラブルシューティング - Azure Database for MySQL
description: Azure Database for MySQL 読み取りレプリカでレプリケーション待機時間をトラブルシューティングする方法に関する詳細
keywords: mysql, トラブルシューティング, レプリケーション待機時間 (秒)
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: troubleshooting
ms.date: 10/08/2020
ms.openlocfilehash: 16a502a53b4441faf68ea342e0bc865731d38b1a
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2020
ms.locfileid: "91876867"
---
# <a name="troubleshoot-replication-latency-in-azure-database-for-mysql"></a>Azure Database for MySQL のレプリケーション待機時間のトラブルシューティング

[読み取りレプリカ](concepts-read-replicas.md)機能を使用すると、Azure Database for MySQL サーバーから、読み取り専用のレプリカ サーバーにデータをレプリケートできます。 読み取りレプリカは、読み取りまたはレポート クエリをアプリケーションから複数のレプリカ サーバーにルーティングすることによって、ワークロードをスケールアウトするために使用されます。 これにより、プライマリ サーバーの負荷が軽減され、アプリケーションのスケーリングに合わせて全体的なパフォーマンスと待機時間が改善されます。 レプリカは、MySQL エンジンのネイティブなバイナリ ログ (binlog) ファイルの位置ベースのレプリケーション テクノロジを使用して、非同期で更新されます。 binlog レプリケーションの詳細については、[MySQL binlog レプリケーションの概要](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html)に関する記事を参照してください。 

セカンダリの読み取りレプリカのレプリケーション ラグの要因は多数あり、たとえば次のようなものがあります。ただし、これにだけに限りません。 

- ネットワーク待ち時間
- ソース サーバーのトランザクション量
- ソースとセカンダリの読み取りレプリカ サーバーのコンピューティング レベル
- プライマリおよびセカンダリ サーバーで実行されているクエリ 

このドキュメントでは、Azure Database for MySQL でレプリケーション待機時間をトラブルシューティングする方法について説明します。 さらに、レプリカ サーバーでのレプリケーション待機時間が増加する一般的な原因についてもいくつか説明します。

## <a name="replication-concepts"></a>レプリケーションの概念

バイナリ ログが有効になっている場合、コミットされたトランザクションはソース サーバーによってバイナリ ログに書き込まれます。これは、レプリケーションに使用されます。 既定では、新しくプロビジョニングされるサーバーのうち、サポートされるストレージが 16 TB までのものはすべて、バイナリ ログが有効になります。 レプリカ サーバーでは、レプリカ サーバーごとに 2 つのスレッドが実行されています。1 つは IO スレッド、もう 1 つは SQL スレッドと呼ばれます。

- **IO スレッド** ではソース サーバーへの接続と、更新されたバイナリ ログの要求が行われます。 バイナリ ログの更新は、このスレッドによって受け取られた後、レプリカ サーバーのリレー ログというローカル ログに保存されます。
- **SQL スレッド** では、リレー ログの読み取りと、レプリカ サーバー上のデータ変更の適用が行われます。

## <a name="monitoring-replication-latency"></a>レプリケーション待機時間の監視

Azure Database for MySQL では、[Azure Monitor](concepts-monitoring.md) に [Replication lag in seconds]\(レプリケーションのラグ (秒)\) メトリックを提供しています。 このメトリックは、読み取りレプリカ サーバーのみで使用できます。 このメトリックは、MySQL で使用できる seconds_behind_master メトリックを使用して計算されます。 レプリケーション待機時間が増加する根本原因を理解するには、[MySQL Workbench](connect-workbench.md) または [Azure Cloud Shell](https://shell.azure.com) を使用してレプリカ サーバーに接続し、次のコマンドを実行します。

 値は実際のレプリカ サーバー名と管理者ユーザーのログイン名に置き換えてください。 管理者ユーザー名には、Azure Database for MySQL の "@\<servername>" が必要です。

  ```azurecli-interactive
  mysql --host=myreplicademoserver.mysql.database.azure.com --user=myadmin@mydemoserver -p 
  ```

  Cloud Shell ターミナルでのエクスペリエンスは次のようになります
  ```
  Requesting a Cloud Shell.Succeeded.
  Connecting terminal...

  Welcome to Azure Cloud Shell

  Type "az" to use Azure CLI
  Type "help" to learn about Cloud Shell

  user@Azure:~$mysql -h myreplicademoserver.mysql.database.azure.com -u myadmin@mydemoserver -p
  Enter password:
  Welcome to the MySQL monitor.  Commands end with ; or \g.
  Your MySQL connection id is 64796
  Server version: 5.6.42.0 Source distribution

  Copyright (c) 2000, 2020, Oracle and/or its affiliates. All rights reserved.

  Oracle is a registered trademark of Oracle Corporation and/or its
  affiliates. Other names may be trademarks of their respective
  owners.

  Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
  mysql>
  ```
  同じ Azure Cloud Shell ターミナルで、次のコマンドを実行します

  ```
  mysql> SHOW SLAVE STATUS;
  ```

  一般的な出力結果は次のようになります。
  
>[!div class="mx-imgBorder"]
> :::image type="content" source="./media/howto-troubleshoot-replication-latency/show-status.png" alt-text="レプリケーション待機時間の監視&quot;:::


出力結果には大量の情報が含まれていますが、通常、以下の列のメトリックを確認するのみで構いません。

|メトリック|説明|
|---|---|
|Slave_IO_State| IO スレッドの現在の状態。 通常、同期している場合は、状態は &quot;Waiting for master to send event&quot; (マスターがイベントを送信するのを待機しています) です。 ただし、&quot;Connecting to master&quot; (マスターに接続しています) などの状態が表示された場合、レプリカとマスター サーバーとの接続が切断されています。 マスターが実行されているか、またはファイアウォールによって接続がブロックされていないかを確認してください。|
|Master_Log_File| マスターの書き込み先のバイナリ ログ ファイル。|
|Read_Master_Log_Pos| マスターの書き込み先である上記のバイナリ ログ ファイル内の位置を表します。|
|Relay_Master_Log_File| レプリカ サーバーによってマスターから読み取られているバイナリ ログ ファイルを表します。|
|Slave_IO_Running| IO スレッドが実行されているかどうかを示します。 &quot;Yes&quot; になっている必要があります。 &quot;NO&quot; の場合、レプリケーションが壊れている可能性があります。|
|Slave_SQL_Running| SQL スレッドが実行されているかどうかを示します。 &quot;Yes&quot; になっている必要があります。 &quot;NO&quot; の場合、レプリケーションが壊れている可能性があります。|
|Exec_Master_Log_Pos| レプリカによって適用されている Relay_Master_Log_File の位置を表示します。 待機時間がある場合、この位置シーケンスは Read_Master_Log_Pos より小さいはずです。|
|Relay_Log_Space|リレー ログのサイズの上限を表示します。 サイズを確認するには、クエリ show global variables like &quot;relay_log_space_limit&quot; を実行します。|
|Seconds_Behind_Master| レプリケーション待機時間を秒単位で表示します。|
|Last_IO_Errno|IO スレッドのエラー コードを表示します (存在する場合)。 これらのコードの詳細については、[MySQL のドキュメント](https://dev.mysql.com/doc/refman/5.7/en/server-error-reference.html)をご覧ください。|
|Last_IO_Error| IO スレッドのエラー メッセージを表示します (存在する場合)。|
|Last_SQL_Errno|SQL スレッドのエラー コードを表示します (存在する場合)。 これらのコードの詳細については、[MySQL のドキュメント](https://dev.mysql.com/doc/refman/5.7/en/server-error-reference.html)をご覧ください。|
|Last_SQL_Error|SQL スレッドのエラー メッセージを表示します (存在する場合)。|
|Slave_SQL_Running_State| 現在の SQL スレッドの状態を示します。 この状態で表示される &quot;System lock&quot; (システム ロック) は正常な動作です。 &quot;Waiting for dependent transaction to commit" (依存トランザクションのコミットを待機しています) という状態が表示されるのは正常です。 これは、コミットされたトランザクションがマスターによって更新されるのをレプリカが待機していることを示します。|

Slave_IO_Running が Yes で、Slave_SQL_Running が Yes の場合、レプリケーションは正常に実行されています。 

次に、Last_IO_Errno、Last_IO_Error、Last_SQL_Errno、Last_SQL_Error を確認する必要があります。  これらのフィールドには、SQL スレッドの停止の原因となった最新のエラーのエラー番号とエラー メッセージが保持されます。 エラー番号 0 と空のメッセージは、エラーがないことを意味します。 エラーの値が 0 以外の場合は、[MySQL のドキュメント](https://dev.mysql.com/doc/refman/5.7/en/server-error-reference.html)でエラー コードを検索してさらに調査する必要があります。

## <a name="common-scenarios-for-high-replication-latency"></a>レプリケーション待機時間が長い場合の一般的なシナリオ

### <a name="network-latency-or-high-cpu-on-source-server"></a>ネットワーク待機時間またはソース サーバーの高 CPU 使用率

次の値が確認された場合、レプリケーション待機時間の原因として最も一般的なのは、ネットワーク待機時間が長いか、またはソース サーバーの CPU 使用量が高いことです。 この場合、IO スレッドは実行中であり、マスターを待機しています。 マスター (ソース サーバー) では既にバイナリ ログ ファイル #20 に書き込みを行っていますが、レプリカではファイル #10 までしか受信していません。 このシナリオでレプリケーション待機時間が長くなる主な要因は、ネットワーク速度またはソース サーバーの高い CPU 使用率です。  Azure のネットワーク待機時間は、1 つのリージョンの中なら一般にミリ秒単位ですが、リージョンをまたぐと秒単位にまで上がることがあります。 ほとんどの場合、ソース サーバーに接続するための IO スレッドの遅延は、ソース サーバーの CPU 使用率が高いために IO スレッドの処理速度が遅くなることが原因で発生します。 これは、Azure Monitor を使用して、CPU 使用率を監視し、ソース サーバー上のコンカレント接続数を観察すれば検出できます。

ソース サーバーの CPU 使用率が高くない場合、考えられる原因はネットワーク待機時間です。 突然、ネットワーク待機時間が異常に長くなった場合は [Azure の状態のページ](https://status.azure.com/status)を確認して、既知の問題や障害がないことを確認することをお勧めします。 

```
Slave_IO_State: Waiting for master to send event
Master_Log_File: the binary file sequence is larger then Relay_Master_Log_File, e.g. mysql-bin.00020
Relay_Master_Log_File: the file sequence is smaller than Master_Log_File, e.g. mysql-bin.00010
```

### <a name="heavy-burst-of-transactions-on-source-server"></a>ソース サーバーでのトランザクションの大量バースト

次の値が確認された場合、レプリケーション待機時間の原因として最も一般的なのは、ソース サーバーでのトランザクションの大量バーストです。 以下の出力結果の場合、レプリカではマスターの背後にあるバイナリ ログを取得できますが、レプリカの IO スレッドでは、リレー ログ領域が既にいっぱいであることが示されています。 レプリカでは既に可能な限り高速でキャッチアップを試みているので、ネットワーク速度が遅延の原因ではないということになります。 むしろ、更新されたバイナリ ログのサイズがリレー ログ領域の上限を超えています。 この問題をさらにトラブルシューティングするには、マスター サーバーで[低速クエリ ログ](concepts-server-logs.md)を有効にする必要があります。 低速クエリ ログを使用すると、ソース サーバーで長時間トランザクションを特定できます。 特定されたクエリは、サーバーの待機時間を短縮するために調整する必要があります。 

```
Slave_IO_State: Waiting for the slave SQL thread to free enough relay log space
Master_Log_File: the binary file sequence is larger then Relay_Master_Log_File, e.g. mysql-bin.00020
Relay_Master_Log_File: the file sequence is smaller then Master_Log_File, e.g. mysql-bin.00010
```

このカテゴリの待機時間の一般的な原因を次に示します。

#### <a name="replication-latency-due-to-data-load-on-source-server"></a>ソース サーバーでのデータ読み込みが原因のレプリケーション待機時間
場合によっては、ソース サーバーで週単位または月単位のデータ読み込みが実行されることがあります。 残念ながら、この場合、レプリケーション待機時間を避けることはできません。 このシナリオでは、ソース サーバーでデータ読み込みが完了した後、レプリカ サーバーによって最終的なキャッチアップが行われます。


### <a name="slowness-on-the-replica-server"></a>レプリカ サーバーのパフォーマンスの低下

次の値が確認された場合、最も一般的な原因として考えられるのはレプリカ サーバー上の問題であり、さらに調査が必要です。 このシナリオでは、出力結果に示されているように、IO と SQL の両方のスレッドが正常に実行されており、レプリカでは、マスターによって書き込まれているものと同じバイナリ ログ ファイルが読み取られています。 ただし、レプリカ サーバーでは、ソース サーバーからの同じトランザクションを反映するのに多少の待機時間が発生しています。 

```
Slave_IO_State: Waiting for master to send event
Master_Log_File: The binary log file sequence equals to Relay_Master_Log_File, e.g. mysql-bin.000191
Read_Master_Log_Pos: The position of master server written to the above file is larger than Relay_Log_Pos, e.g. 103978138
Relay_Master_Log_File: mysql-bin.000191
Slave_IO_Running: Yes
Slave_SQL_Running: Yes
Exec_Master_Log_Pos: The position of slave reads from master binary log file is smaller than Read_Master_Log_Pos, e.g. 13468882
Seconds_Behind_Master: There is latency and the value here is greater than 0
```

このカテゴリの待機時間の一般的な原因を次に示します。

#### <a name="no-primary-or-unique-key-on-a-table"></a>テーブルに主キーまたは一意キーがない

Azure Database for MySQL では行ベースのレプリケーションが使用されます。 行ベースのレプリケーションでは、マスター サーバーにより、個々のテーブル行の変更に関するイベントがバイナリ ログに書き込まれます。 次いで、SQL スレッドにより、レプリカ サーバー上の対応するテーブル行に対してこれらの変更が実行されます。 テーブルに主キーまたは一意キーがないことは、レプリケーション待機時間が発生する一般的な原因の 1 つです。 主キーまたは一意キーがないと、変更を適用するために、ターゲット テーブル内のすべての行が SQL スレッドによってスキャンされることになります。

MySQL で主キーは、高速なクエリのパフォーマンスを得られる関連インデックスです。これに NULL 値を含めることができないためです。 InnoDB ストレージ エンジンによりテーブル データは、主キーに基づいて超高速な検索と並べ替えを行えるよう物理的に編成されます。 そのため、レプリカ サーバーを作成する前に、ソース サーバーのテーブルに主キーを追加することをお勧めします。 このシナリオでは、レプリケーション待機時間を改善するために、ソース サーバーに主キーを追加し、読み取りレプリカを再作成する必要があります。

次のクエリを使用すると、ソース サーバーに主キーがないテーブルを特定できます。

```sql 
select tab.table_schema as database_name, tab.table_name 
from information_schema.tables tab left join 
information_schema.table_constraints tco 
on tab.table_schema = tco.table_schema 
and tab.table_name = tco.table_name 
and tco.constraint_type = 'PRIMARY KEY' 
where tco.constraint_type is null 
and tab.table_schema not in('mysql', 'information_schema', 'performance_schema', 'sys') 
and tab.table_type = 'BASE TABLE' 
order by tab.table_schema, tab.table_name;

```

#### <a name="replication-latency-due-to-long-running-queries-on-replica-server"></a>レプリカ サーバーでの実行時間の長いクエリが原因で発生するレプリケーション待機時間

レプリカ サーバーのワークロードが原因で SQL スレッドが IO スレッドに追従できなくなる可能性があります。 これは、レプリカ サーバーで実行時間の長いクエリがある場合に、レプリケーション待機時間が長くなる一般的な原因の 1 つです。 この場合、問題のトラブルシューティングに役立つように、レプリカ サーバーで[低速クエリ ログ](concepts-server-logs.md)を有効にする必要があります。 低速クエリはリソースの消費の増加や、サーバーの速度の低下の原因となることがあり、そのためにレプリカはマスターに追いつけなくなります。 このシナリオでは、低速クエリを調整する必要があります。 クエリを高速化すると、SQL スレッドのブロックが回避され、レプリケーション待機時間が大幅に短縮されます。


#### <a name="replication-latency-due-to-ddl-queries-on-source-server"></a>ソース サーバーでの DDL クエリが原因のレプリケーション待機時間
ソース サーバーで [ALTER TABLE](https://dev.mysql.com/doc/refman/5.7/en/alter-table.html) のような実行時間の長い DDL コマンドが実行されていて、実行に 1 時間かかったとします。 その間、ソース サーバーでは何千もの他のクエリが並列実行されている可能性があります。 DDL がレプリカにレプリケートされるとき、データベースの一貫性を確保するために、MySQL エンジンでは DDL を単一のレプリケーション スレッドで実行する必要があります。 そのため、レプリケートされた他のクエリはすべてブロックされ、レプリカ サーバーで DDL 操作が完了するまで 1 時間以上待つ必要があります。 これは、オンライン DDL 操作の有無に関係なく当てはまります。 DDL 操作では、レプリケーションでレプリケーション待機時間が長くなることが予想されます。

ソース サーバーで[低速クエリ ログ](concepts-server-logs.md)を有効にした場合、低速クエリ ログを参照して、ソース サーバーで DDL コマンドが実行されたかどうかを確認することでこのシナリオを検出できます。 インデックスの削除、名前の変更、作成では ALTER TABLE の INPLACE アルゴリズムが使用されますが、これにはテーブル データのコピーやテーブルの再構築を伴う場合があります。 通常、INPLACE アルゴリズムでは同時実行の DML がサポートされますが、操作の準備と実行のフェーズ中に一時的にテーブルの排他的なメタデータ ロックがかけられることがあります。 そのため、CREATE INDEX ステートメントでは、ALGORITHM 句と LOCK 句を使用して、テーブルのコピー方法および読み取りと書き込みのコンカレンシーのレベルに影響を与えることができますが、その場合でも、FULLTEXT または SPATIAL インデックスを追加することで DML 操作を回避できます。 ALGORITHM 句と LOCK 句を使用してインデックスを作成する例を以下に示します。

```sql
ALTER TABLE table_name ADD INDEX index_name (column), ALGORITHM=INPLACE, LOCK=NONE;
```

残念ながら、ロックを必要とする DDL ステートメントでは、レプリケーション待機時間を回避することはできません。その代わり、このような種類の DDL 操作は、影響の可能性を下げるために、ピーク時以外の時間帯、たとえば夜間に実行する必要があります。

#### <a name="replication-latency-due-to-replica-server-lower-sku"></a>レプリカ サーバーの下位 SKU が原因のレプリケーション待機時間

Azure Database for MySQL では、マスター サーバーと同じサーバー構成で読み取りレプリカが作成されます。 作成された後、レプリカ サーバーの構成を変更できます。 ただし、レプリカ サーバーをダウングレードすると、ワークロードによってリソースの消費量が増加し、その結果、レプリケーション待機時間が発生する可能性があります。 これは、Azure Monitor からレプリカ サーバーの CPU とメモリの使用量を監視すれば確認できます。 このシナリオでは、レプリカがマスターに追随できるようにするために、レプリカ サーバーの構成をソースと同じかそれ以上の値にしておくようお勧めします。

#### <a name="improving-replication-latency-using-server-parameter-tuning-on-source-server"></a>ソース サーバーでのサーバー パラメーターの調整を使用したレプリケーション待機時間の改善

Azure Database for MySQL では、レプリケーションは既定で、レプリカ上の並列スレッドで実行されるように最適化されています。 ソース サーバー上のコンカレンシーの高いワークロードでレプリカ サーバーがキャッチアップに失敗する場合、ソース サーバーでパラメーター binlog_group_commit_sync_delay を構成することで、レプリケーション待機時間を改善できます。 このパラメーターでは、バイナリ ログのコミットによってバイナリ ログ ファイルが同期されるまでに待機する時間 (マイクロ秒単位) を制御します。 利点は、トランザクションがコミットされるたびに直ちに適用する代わりに、マスターからバイナリ ログの更新が一括送信されることです。 これにより、レプリカの IO が減少し、パフォーマンスを向上させることができます。 このシナリオでは、binlog_group_commit_sync_delay を 1000 程度に設定し、レプリケーション待機時間を監視するのが有効と考えられます。 このパラメーターは慎重に設定し、コンカレンシーの高いワークロードにのみ使用する必要があります。 シングルトン トランザクションの数が多く、コンカレンシーの低いシナリオで binlog_group_commit_sync_delay を設定すると、コミットされるトランザクションが少ないにもかかわらず、IO スレッドがバイナリ ログの一括更新を待機するため、待機時間が増加するおそれがあります。 

## <a name="next-steps"></a>次のステップ
[MySQL binlog レプリケーションの概要](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html)をご確認ください。
