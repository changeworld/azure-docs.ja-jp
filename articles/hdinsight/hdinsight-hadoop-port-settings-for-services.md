<properties
pageTitle="HDInsight で使用されるポート | Azure"
description="HDInsight で実行されている Hadoop サービスで使用されるポートの一覧。"
services="hdinsight"
documentationCenter=""
authors="Blackmist"
manager="jhubbard"
editor="cgronlun"/>

<tags
ms.service="hdinsight"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="big-data"
ms.date="09/13/2016"
ms.author="larryfr"/>

# HDInsight で使用されるポートと URI

このドキュメントでは、Linux ベースの HDInsight クラスターで実行されている Hadoop サービスで使用されるポートの一覧を示します。また、SSH を使用したクラスターへの接続に使用されるポートの情報も提供します。

## パブリック ポートと非パブリック ポート

Linux ベースの HDInsight クラスターでは、22、23、443 の 3 つのポートだけがインターネット上で公開されます。これらのポートは、SSH を使用してクラスターに安全にアクセスし、セキュリティで保護された HTTPS プロトコルを介して公開されるサービスにアクセスする際に使用されます。

内部的には、HDInsight は Azure Virtual Network 上で実行される複数の Azure Virtual Network (クラスター内のノード) によって実装されます。仮想ネットワーク内から、インターネット経由で公開されていないポートにアクセスできます。たとえば、SSH を使用してヘッド ノードのいずれかに接続すると、そのヘッド ノードから、クラスター ノードで実行されているサービスに直接アクセスできます。

> [AZURE.IMPORTANT] HDInsight クラスターの作成時に、構成オプションとして Azure Virtual Network を指定しなかった場合、Azure Virtual Network が自動的に作成されます。ただし、この自動的に作成された仮想ネットワークには、他のマシン (他の Azure Virtual Machines やクライアント開発用コンピューターなど) を参加させることはできません。

仮想ネットワークに他のマシンを参加させるには、まず仮想ネットワークを作成し、HDInsight クラスターの作成時にその仮想ネットワークを指定する必要があります。詳細については、「[Azure Virtual Network を使用した HDInsight 機能の拡張](hdinsight-extend-hadoop-virtual-network.md)」をご覧ください。

## パブリック ポート

HDInsight クラスターのすべてのノードは Azure Virtual Network 内にあり、インターネットから直接アクセスすることはできません。パブリック ゲートウェイにより、すべての HDInsight クラスターの種類に共通する次のポートへのインターネット アクセスが提供されます。

| サービス | ポート | プロトコル | Description |
| ---- | ---------- | -------- | ----------- | ----------- |
| sshd | 22 | SSH | プライマリ ヘッドノードの sshd にクライアントを接続します。[Linux ベースの HDInsight での SSH の使用](hdinsight-hadoop-linux-use-ssh-windows.md)に関する記事をご覧ください。 |
| sshd | 22 | SSH | エッジ ノードの sshd にクライアントを接続します (HDInsight Premium のみ)。[HDInsight の R Server の使用開始](hdinsight-hadoop-r-server-get-started.md)に関する記事をご覧ください。 |
| sshd | 23 | SSH | セカンダリ ヘッドノードの sshd にクライアントを接続します。[Linux ベースの HDInsight での SSH の使用](hdinsight-hadoop-linux-use-ssh-windows.md)に関する記事をご覧ください。 |
| Ambari | 443 | HTTPS | Ambari Web UI。[Ambari Web UI を使用した HDInsight の管理](hdinsight-hadoop-manage-ambari.md)に関する記事をご覧ください。 |
| Ambari | 443 | HTTPS | Ambari REST API。[Ambari REST API を使用した HDInsight の管理](hdinsight-hadoop-manage-ambari-rest-api.md)に関する記事をご覧ください。 |
| WebHCat | 443 | HTTPS | HCatalog REST API。[Curl での Hive の使用](hdinsight-hadoop-use-Pig-curl.md)、[Curl での Pig の使用](hdinsight-hadoop-use-Pig-curl.md)、[Curl での MapReduce の使用](hdinsight-hadoop-use-mapreduce-curl.md)に関する記事をご覧ください。 |
| HiveServer2 | 443 | ODBC | ODBC を使用して Hive に接続します。[Microsoft ODBC ドライバーを使用した Excel から HDInsight への接続](hdinsight-connect-excel-hive-odbc-driver.md)に関する記事をご覧ください。 |
| HiveServer2 | 443 | JDBC | JDBC を使用して Hive に接続します。[Hive JDBC ドライバーを使用した HDInsight の Hive への接続](hdinsight-connect-hive-jdbc-driver.md)に関する記事をご覧ください。 |

次のポートは、特定のクラスターの種類で使用できます。

| サービス | ポート | プロトコル |クラスターの種類 | Description |
| ------------ | ---- |  ----------- | --- | ----------- |
| Stargate | 443 | HTTPS | HBase | HBase REST API。[HBase の使用開始](hdinsight-hbase-tutorial-get-started-linux.md)に関する記事をご覧ください。 |
| Livy | 443 | HTTPS | Spark | Spark REST API。[Livy を使用した Spark ジョブのリモートでの送信](hdinsight-apache-spark-livy-rest-interface.md)に関する記事をご覧ください。 |
| Storm | 443 | HTTPS | Storm | Storm Web UI。[HDInsight での Storm トポロジのデプロイと管理](hdinsight-storm-deploy-monitor-topology-linux.md)に関する記事をご覧ください。

### 認証

インターネット上で公開されるすべてのサービスを認証する必要があります。

| ポート | 資格情報 |
| ---- | ----------- |
| 22 または 23 | クラスターの作成時に指定した SSH ユーザー資格情報 |
| 443 | ログイン名 (既定値: admin) と、クラスターの作成時に設定したパスワード |

## 非パブリック ポート

> [AZURE.NOTE] 一部のサービスは、特定のクラスターの種類でのみ利用できます。たとえば、HBase を利用できるのは、クラスターの種類が HBase の場合のみです。

### HDFS ポート

| サービス | ノード | ポート | プロトコル | Description |
| ------- | ------- | ---- | -------- | ----------- | 
| NameNode Web UI | ヘッド ノード | 30070 | HTTPS | 現在の状態を表示する Web UI |
| NameNode メタデータ サービス | ヘッド ノード | 8020 | IPC | ファイル システム メタデータ 
| DataNode | すべての worker ノード | 30075 | HTTPS | 状態、ログなどを表示する Web UI |
| DataNode | すべての worker ノード | 30010 | &nbsp; | データ転送 |
| DataNode | すべての worker ノード | 30020 | IPC | メタデータ操作 |
| セカンダリ NameNode | ヘッド ノード | 50090 | HTTP | NameNode メタデータのチェックポイント |

### YARN ポート

| サービス | ノード | ポート | プロトコル | Description |
| ------- | ------- | ---- | -------- | ----------- |
| Resource Manager Web UI | ヘッド ノード | 8088 | HTTP | Resource Manager の Web UI |
| Resource Manager Web UI | ヘッド ノード | 8090 | HTTPS | Resource Manager の Web UI |
| Resource Manager 管理インターフェイス | ヘッド ノード | 8141 | IPC | アプリケーション送信用 (Hive、Hive サーバー、Pig など) |
| Resource Manager スケジューラ | ヘッド ノード | 8030 | HTTP | 管理インターフェイス |
| Resource Manager アプリケーション インターフェイス | ヘッド ノード | 8050 | HTTP |アプリケーション マネージャー インターフェイスのアドレス |
| NodeManager | すべての worker ノード | 30050 | &nbsp; | コンテナー マネージャーのアドレス |
| NodeManager Web UI | すべての worker ノード | 30060 | HTTP | Resource Manager インターフェイス |
| Timeline アドレス | ヘッド ノード | 10200 | RPC | Timeline サービスの RPC サービス |
| Timeline Web UI | ヘッド ノード | 8181 | HTTP | Timeline サービス Web UI |

### Hive ポート

| サービス | ノード | ポート | プロトコル | Description |
| ------- | ------- | ---- | -------- | ----------- |
| HiveServer2 | ヘッド ノード | 10001 | Thrift | プログラムによって Hive (Thrift/JDBC) に接続するためのサービス |
| HiveServer | ヘッド ノード | 10000 | Thrift | プログラムによって Hive (Thrift/JDBC) に接続するためのサービス |
| Hive メタストア | ヘッド ノード | 9083 | Thrift | プログラムによって Hive メタデータ (Thrift/JDBC) に接続するためのサービス |

### WebHCat ポート

| サービス | ノード | ポート | プロトコル | Description |
| ------- | ------- | ---- | -------- | ----------- |
| WebHCat サーバー | ヘッド ノード | 30111 | HTTP | HCatalog および他の Hadoop サービス上の Web API |

### MapReduce ポート

| サービス | ノード | ポート | プロトコル | Description |
| ------- | ------- | ---- | -------- | ----------- |
| JobHistory | ヘッド ノード | 19888 | HTTP | MapReduce JobHistory Web UI |
| JobHistory | ヘッド ノード | 10020 | &nbsp; | MapReduce JobHistory サーバー |
| ShuffleHandler | &nbsp; | 13562 | &nbsp; | 中間 Map 出力を要求元 Reducer に転送 |

### Oozie

| サービス | ノード | ポート | プロトコル | Description |
| ------- | ------- | ---- | -------- | ----------- |
| Oozie サーバー | ヘッド ノード | 11000 | HTTP | Oozie サービスの URL |
| Oozie サーバー | ヘッド ノード | 11001 | HTTP | Oozie 管理用ポート |

### Ambari メトリック

| サービス | ノード | ポート | プロトコル | Description |
| ------- | ------- | ---- | -------- | ----------- |
| TimeLine (アプリケーション履歴) | ヘッド ノード | 6188 | HTTP | Timeline サービス Web UI |
| TimeLine (アプリケーション履歴) | ヘッド ノード | 30200 | RPC | Timeline サービス Web UI |

### HBase ポート

| サービス | ノード | ポート | プロトコル | Description |
| ------- | ------- | ---- | -------- | ----------- |
| HMaster | ヘッド ノード | 16000 | &nbsp; | &nbsp; |
| HMaster 情報 Web UI | ヘッド ノード | 16010 | HTTP | HBase Master Web UI のポート |
| リージョン サーバー | すべての worker ノード | 16020 | &nbsp; | &nbsp; |
| &nbsp; | &nbsp; | 2181 | &nbsp; | クライアントが ZooKeeper への接続に使用するポート |

### Kafka ポート

| サービス | ノード | ポート | プロトコル | Description |
| ------- | ------- | ---- | -------- | ----------- |
| ブローカー | ワーカー ノード | 9092 | [Kafka Wire Protocol](http://kafka.apache.org/protocol.html) | クライアント通信に使用 |
| &nbsp; | Zookeeper ノード | 2181 | &nbsp; | クライアントが ZooKeeper への接続に使用するポート |

<!---HONumber=AcomDC_0921_2016-->