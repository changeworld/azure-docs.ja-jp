---
title: HBase クラスターを新しいバージョンおよび Storage アカウントに移行する - Azure HDInsight
description: Azure HDInsight の Apache HBase クラスターを、異なる Azure Storage アカウントを使用して、新しいバージョンに移行する方法について説明します。
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 05/06/2021
ms.openlocfilehash: 4ed4de8c134575bba1b961f918216eb3cca9b6b1
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/10/2021
ms.locfileid: "111963993"
---
# <a name="migrate-apache-hbase-to-a-new-version-and-storage-account"></a>Apache HBase を新しいバージョンおよびストレージ アカウントに移行する

この記事では、Azure HDInsight の Apache HBase クラスターを、異なる Azure Storage アカウントを使用して、新しいバージョンに更新する方法を説明します。

この記事の内容は、移行元と移行先のクラスターで異なる Storage アカウントを使用する場合にのみ当てはまります。 移行元と移行先で同じ Storage アカウントを使用してバージョンをアップグレードするには、[Apache HBase を新しいバージョンに移行する](apache-hbase-migrate-new-version.md)方法に関する記事をご覧ください。

アップグレード時のダウンタイムは数分間だけです。 すべてのメモリ内データをフラッシュし、新しいクラスターでサービスを構成、再起動する間、このダウンタイムが発生します。 結果は、ノード数、データ量、およびその他の可変要素によって異なります。

## <a name="review-apache-hbase-compatibility"></a>Apache HBase の互換性の確認

Apache HBase をアップグレードする前に、移行元クラスターと移行先クラスターの HBase バージョンの互換性を確保します。 [HBase Reference Guide](https://hbase.apache.org/book.html#upgrading) の HBase バージョン互換性表とリリース ノートを見て、新しいバージョンに対する互換性がアプリケーションにあることを確認してください。

これは互換性表の例です。 Y は互換性を示し、N は潜在的な非互換性を示します。

| 互換性の種類 | メジャー バージョン| マイナー バージョン | 修正プログラム |
| --- | --- | --- | --- |
| クライアント/サーバー間の接続の互換性 | N | Y | Y |
| サーバー/サーバー間の互換性 | N | Y | Y |
| ファイル形式の互換性 | N | Y | Y |
| クライアント API の互換性 | N | Y | Y |
| クライアント バイナリの互換性 | N | N | Y |
| **サーバー側の制限付きの API 互換性** |  |  |  |
| Stable | N | Y | Y |
| 進化 | N | N | Y |
| 不安定 | N | N | N |
| 依存関係の互換性 | N | Y | Y |
| 運用の互換性 | N | N | Y |

重要な非互換性は、HBase のバージョン リリース ノートに記載されます。 HDInsight および HBase のターゲット バージョンを実行しているクラスターでアプリケーションをテストします。

HDInsight のバージョンと互換性の詳細は「[Azure HDInsight のバージョン](../hdinsight-component-versioning.md)」をご覧ください。

## <a name="apache-hbase-cluster-migration-overview"></a>Apache HBase クラスターの移行の概要

Azure HDInsight の Apache HBase クラスターをアップグレードして新しいストレージ アカウントに移行する大まかな手順は次のとおりです。 詳しい説明については、詳しい手順とコマンドをご覧ください。

移行元クラスターの準備をする
1. データ インジェストを停止します。
1. memstore のデータをフラッシュします。
1. Ambari で HBase を停止します。
1. 高速書き込みを使用しているクラスターは、先行書き込みログ (WAL) のディレクトリをバックアップします。

移行先クラスターの準備をする
1. 移行先クラスターを作成します。
1. Ambari で HBase を停止します。
1. Zookeeper のデータを消去します。
1. ユーザーを HBase に切り替えます。

移行を完了する
1. 移行先のファイル システムを消去し、データを移行し、`/hbase/hbase.id` を削除します。
1. WAL を消去、移行します。
1. 移行先の Ambari クラスターですべてのサービスを開始します。
1. HBase を確認します。
1. 移行元クラスターを削除します。

## <a name="detailed-migration-steps-and-commands"></a>詳しい移行手順とコマンド

これらの詳しい手順とコマンドにより、新しいストレージ アカウントを使用して、Apache HBase クラスターを移行します。

### <a name="prepare-the-source-cluster"></a>移行元クラスターの準備をする

1. 移行元の HBase クラスターへのインジェストを停止します。
   
1. アップグレードする 移行元 HBase クラスターをフラッシュします。
   
   HBase では、*memstore* というメモリ内の保存場所に受信データを書き込みます。 memstore が一定のサイズに達すると、HBase はクラスターのストレージ アカウントに長期保存するためにディスクにフラッシュします。 アップグレード後に移行元クラスターを削除すると、memstore のデータもすべて削除されます。 データを保持するには、アップグレードする前に、各テーブルの memstore を手動でディスクにフラッシュします。
   
   [GitHub の hbase-utils リポジトリ](https://github.com/Azure/hbase-utils/)にある [flush_all_tables.sh](https://github.com/Azure/hbase-utils/blob/master/scripts/flush_all_tables.sh) スクリプトを実行すれば memstore のデータをフラッシュできます。
   
   また、HDInsight クラスター内で次の HBase シェル コマンドを実行しても、memstore のデータをフラッシュできます。
   
   ```bash
   hbase shell
   flush "<table-name>"
   ```
   
1. `https://<OLDCLUSTERNAME>.azurehdinsight.net` で 移行元クラスターの [Apache Ambari](https://ambari.apache.org/) にサインインし、HBase のサービスを停止します。
   
1. 確認ウィンドウで該当するボックスを選択し、HBase のメンテナンス モードを有効にします。
   
   Ambari への接続とその使用方法の詳細については、「[Ambari Web UI を使用した HDInsight クラスターの管理](../hdinsight-hadoop-manage-ambari.md)」を参照してください。
   
1. 移行元 HBase クラスターに[高速書き込み](apache-hbase-accelerated-writes.md)機能がない場合はこの手順を飛ばしてください。 移行元 HBase クラスターで高速書き込みを使用している場合、移行元クラスターの任意の Zookeeper ノードまたはワーカー ノード上の SSH セッションで次のコマンドを実行し、HDFS の WAL ディレクトリをバックアップします。
   
   ```bash
   hdfs dfs -mkdir /hbase-wal-backup
   hdfs dfs -cp hdfs://mycluster/hbasewal /hbase-wal-backup
   ```

### <a name="prepare-the-destination-cluster"></a>移行先クラスターの準備をする

1. Azure portal で、移行元クラスターと異なるストレージ アカウントを使用して、[移行先 HDInsight クラスターを新たにセットアップします](../hdinsight-hadoop-provision-linux-clusters.md)。
   
1. `https://<NEWCLUSTERNAME>.azurehdinsight.net` で新しいクラスター上の [Apache Ambari](https://ambari.apache.org/) にサインインし、HBase のサービスを停止します。
   
1. 任意の Zookeeper ノードまたはワーカー ノードで次のコマンドを実行し、移行先クラスターの Zookeeper のデータを消去します。
   
   ```bash
   hbase zkcli
   rmr /hbase-unsecure
   quit
   ```
   
1. `sudo su hbase` を実行してユーザーを HBase に切り替えます。

### <a name="clean-and-migrate-the-file-system-and-wal"></a>ファイル システムと WAL を消去、移行する

移行元でどの HDI バージョンを使用しているか、また、移行元および移行先クラスターで高速書き込みを使用しているかどうかに応じて、次のいずれかのコマンドを実行します。 HDI 3.6 は Basic サポートであり新しいクラスターでの使用は推奨されないので、移行先クラスターでは常に HDI バージョン 4.0 を使用します。

- [移行元クラスターが HDI 3.6 で高速書き込みを使用しており、移行先クラスターでも高速書き込みを使用します](#the-source-cluster-is-hdi-36-or-hdi-40-with-accelerated-writes-and-the-destination-cluster-has-accelerated-writes)。
- [移行元クラスターが HDI 3.6 で高速書き込みを使用しておらず、移行先クラスターでは高速書き込みを使用します](#the-source-cluster-is-hdi-36-without-accelerated-writes-and-the-destination-cluster-has-accelerated-writes)。
- [移行元クラスターが HDI 3.6 で高速書き込みを使用しておらず、移行先クラスターでも高速書き込みを使用しません](#the-source-cluster-is-hdi-36-without-accelerated-writes-and-the-destination-cluster-doesnt-have-accelerated-writes)。
- [移行元クラスターが HDI 4.0 で高速書き込みを使用しており、移行先クラスターでも高速書き込みを使用します](#the-source-cluster-is-hdi-36-or-hdi-40-with-accelerated-writes-and-the-destination-cluster-has-accelerated-writes)。
- [移行元クラスターが HDI 4.0 で高速書き込みを使用しておらず、移行先クラスターでは高速書き込みを使用します](#the-source-cluster-is-hdi-40-without-accelerated-writes-and-the-destination-cluster-has-accelerated-writes)。
- [移行元クラスターが HDI 4.0 で高速書き込みを使用しておらず、移行先クラスターでも高速書き込みを使用しません](#the-source-cluster-is-hdi-40-without-accelerated-writes-and-the-destination-cluster-doesnt-have-accelerated-writes)。

ストレージ アカウントの `<container-endpoint-url>` は `https://<storageaccount>.blob.core.windows.net/<container-name>` です。 ストレージ アカウントの SAS トークンを URL の末尾に配置します。

- WASB ストレージの `<container-fullpath>` は `wasbs://<container-name>@<storageaccount>.blob.core.windows.net` です
- Azure Data Lake Storage Gen2 の `<container-fullpath>` は `abfs://<container-name>@<storageaccount>.dfs.core.windows.net` です。

#### <a name="copy-commands"></a>コピー コマンド

HDFS のコピー コマンドは `hdfs dfs <copy properties starting with -D> -cp` です
 
ページ BLOB 以外でファイルをコピーするときは、`hadoop distcp` を使用する方がパフォーマンスがいいです: `hadoop distcp <copy properties starting with -D>`
 
ストレージ アカウントのキーを渡すには次のようにします。
- `-Dfs.azure.account.key.<storageaccount>.blob.core.windows.net='<storage account key>'`
- `-Dfs.azure.account.keyprovider.<storageaccount>.blob.core.windows.net=org.apache.hadoop.fs.azure.SimpleKeyProvider`

HBase のデータ ファイルをコピーするときは [AzCopy](../../storage/common/storage-ref-azcopy.md) も使用でき、こちらの方がパフォーマンスがいいです。
   
1. AzCopy コマンドを実行します。
   
   ```bash
   azcopy cp "<source-container-endpoint-url>/hbase" "<target-container-endpoint-url>" --recursive
   ```

1. 移行先のストレージ アカウントが Azure Blob ストレージである場合は、コピー後にこの手順を実行します。 移行先のストレージ アカウントが Data Lake Storage Gen2 である場合は、この手順を飛ばします。
   
   Hadoop WASB ドライバーでは、すべてのディレクトリに対応している、サイズが 0 の特別な BLOB を使用します。 AzCopy ではコピー時にこれらのファイルを無視します。 WASB の操作にはこれらの BLOB を使用するものがあるので、移行先のクラスターにそれらを作成する必要があります。 BLOB を作成するには、移行先クラスターの任意のノードから次の Hadoop コマンドを実行します。
   
   ```bash
   sudo -u hbase hadoop fs -chmod -R 0755 /hbase
   ```

AzCopy は「[AzCopy を使ってみる](../../storage/common/storage-use-azcopy-v10.md)」でダウンロードできます。 AzCopy の詳しい使用方法は「[azcopy copy](../../storage/common/storage-ref-azcopy-copy.md)」をご覧ください。

#### <a name="the-source-cluster-is-hdi-36-or-hdi-40-with-accelerated-writes-and-the-destination-cluster-has-accelerated-writes"></a>移行元クラスターが HDI 3.6 または HDI 4.0 で高速書き込みを使用しており、移行先クラスターでも高速書き込みを使用する

1. ファイル システムを消去してデータを移行するには、次のコマンドを実行します。
   
   ```bash
   hdfs dfs -rm -r /hbase 
   hadoop distcp <source-container-fullpath>/hbase /
   ```
   
1. `hdfs dfs -rm /hbase/hbase.id` を実行して `hbase.id` を削除します
   
1. WAL を消去、移行するには、次のコマンドを実行します。
   
   ```bash
   hdfs dfs -rm -r hdfs://<destination-cluster>/hbasewal
   hdfs dfs -cp <source-container-fullpath>/hbase-wal-backup/hbasewal hdfs://<destination-cluster>/hbasewal
   ```

#### <a name="the-source-cluster-is-hdi-36-without-accelerated-writes-and-the-destination-cluster-has-accelerated-writes"></a>移行元クラスターが HDI 3.6 で高速書き込みを使用しておらず、移行先クラスターでは高速書き込みを使用する

1. ファイル システムを消去してデータを移行するには、次のコマンドを実行します。
   
   ```bash
   hdfs dfs -rm -r /hbase 
   hdfs dfs -Dfs.azure.page.blob.dir="/hbase/WALs,/hbase/MasterProcWALs,/hbase/oldWALs,/hbase-wals" -cp <source-container-fullpath>/hbase /
   hdfs dfs -rm -r /hbase/*WALs
   ```
   
1. `hdfs dfs -rm /hbase/hbase.id` を実行して `hbase.id` を削除します
   
1. WAL を消去、移行するには、次のコマンドを実行します。
   
   ```bash
   hdfs dfs -rm -r hdfs://<destination-cluster>/hbasewal/*
   hdfs dfs -Dfs.azure.page.blob.dir="/hbase/WALs,/hbase/MasterProcWALs,/hbase/oldWALs,/hbase-wals" -cp <source-container-fullpath>/hbase/*WALs hdfs://<destination-cluster>/hbasewal
   ```

#### <a name="the-source-cluster-is-hdi-36-without-accelerated-writes-and-the-destination-cluster-doesnt-have-accelerated-writes"></a>移行元クラスターが HDI 3.6 で高速書き込みを使用しておらず、移行先クラスターでも高速書き込みを使用する

1. ファイル システムを消去してデータを移行するには、次のコマンドを実行します。
   
   ```bash
   hdfs dfs -rm -r /hbase 
   hdfs dfs -Dfs.azure.page.blob.dir="/hbase/WALs,/hbase/MasterProcWALs,/hbase/oldWALs,/hbase-wals" -cp <source-container-fullpath>/hbase /
   hdfs dfs -rm -r /hbase/*WALs
   ```
   
1. `hdfs dfs -rm /hbase/hbase.id` を実行して `hbase.id` を削除します
   
1. WAL を消去、移行するには、次のコマンドを実行します。
   
   ```bash
   hdfs dfs -rm -r /hbase-wals/*
   hdfs dfs -Dfs.azure.page.blob.dir="/hbase/WALs,/hbase/MasterProcWALs,/hbase/oldWALs,/hbase-wals" -cp <source-container-fullpath>/hbase/*WALs /hbase-wals
   ```

#### <a name="the-source-cluster-is-hdi-40-without-accelerated-writes-and-the-destination-cluster-has-accelerated-writes"></a>移行元クラスターが HDI 4.0 で高速書き込みを使用しておらず、移行先クラスターでは高速書き込みを使用する

1. ファイル システムを消去してデータを移行するには、次のコマンドを実行します。
   
   ```bash
   hdfs dfs -rm -r /hbase 
   hadoop distcp <source-container-fullpath>/hbase /
   ```
   
1. `hdfs dfs -rm /hbase/hbase.id` を実行して `hbase.id` を削除します
   
1. WAL を消去、移行するには、次のコマンドを実行します。
   
   ```bash
   hdfs dfs -rm -r hdfs://<destination-cluster>/hbasewal
   hdfs dfs -Dfs.azure.page.blob.dir="/hbase-wals" -cp <source-container-fullpath>/hbase-wals hdfs://<destination-cluster>/hbasewal
   ```

#### <a name="the-source-cluster-is-hdi-40-without-accelerated-writes-and-the-destination-cluster-doesnt-have-accelerated-writes"></a>移行元クラスターが HDI 4.0 で高速書き込みを使用しておらず、移行先クラスターでも高速書き込みを使用する

1. ファイル システムを消去してデータを移行するには、次のコマンドを実行します。
   
   ```bash
   hdfs dfs -rm -r /hbase 
   hadoop distcp <source-container-fullpath>/hbase /
   ```
   
1. `hdfs dfs -rm /hbase/hbase.id` を実行して `hbase.id` を削除します
   
1. WAL を消去、移行するには、次のコマンドを実行します。
   
   ```bash
   hdfs dfs -rm -r /hbase-wals/*
   hdfs dfs -Dfs.azure.page.blob.dir="/hbase-wals" -cp <source-container-fullpath>/hbase-wals /
   ```

### <a name="complete-the-migration"></a>移行を完了する

1. 移行先クラスターで変更を保存し、Ambari の指示どおりに必要なすべてのサービスを再起動します。
   
1. アプリケーションで移行先クラスターを指定します。
   
   > [!NOTE]  
   > アップグレードを行うと、アプリケーションの静的 DNS 名が変わります。 この DNS をハードコーディングする代わりに、当該のドメイン名の DNS 設定で、目的のクラスター名を参照する CNAME を構成できます。 もう 1 つの選択肢は、再デプロイせずに更新できる、アプリケーション用の構成ファイルを使用することです。
   
1. インジェストを開始します。
   
1. HBase の一貫性と、データ定義言語 (DDL) およびデータ操作言語 (DML) の基本操作を確認します。
   
1. 移行先クラスターに問題がなければ、移行元クラスターを削除します。

## <a name="next-steps"></a>次のステップ

[Apache HBase](https://hbase.apache.org/) の詳細と HDInsight クラスターのアップグレードについては、次の記事を参照してください。

- [HDInsight クラスターを新しいバージョンにアップグレードする](../hdinsight-upgrade-cluster.md)
- [Apache Ambari Web UI を使用して Azure HDInsight を監視および管理する](../hdinsight-hadoop-manage-ambari.md)
- [Azure HDInsight のバージョン](../hdinsight-component-versioning.md)
- [Apache HBase の最適化](../optimize-hbase-ambari.md)