---
title: HBase クラスターを新しいバージョンに移行する - Azure HDInsight
description: Azure HDInsight で Apache HBase クラスターを新しいバージョンに移行する方法について説明します。
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 05/06/2021
ms.openlocfilehash: 1d6f2d66a00601334a9cab4695b4e78c77a67917
ms.sourcegitcommit: 3de22db010c5efa9e11cffd44a3715723c36696a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/10/2021
ms.locfileid: "109654800"
---
# <a name="migrate-an-apache-hbase-cluster-to-a-new-version"></a>Apache HBase クラスターを新しいバージョンに移行する

この記事では、Azure HDInsight で Apache HBase クラスターを新しいバージョンに更新する方法について説明します。

この記事は、移行元および移行先クラスターで同じ Azure Storage アカウントを使用する場合にのみ適用されます。 移行先クラスターで新しいストレージ アカウントまたは別のストレージ アカウントを使用してアップグレードを行うには、[Apache HBase を新しいストレージ アカウントを使用して新しいバージョンに移行する](apache-hbase-migrate-new-version-new-storage-account.md)方法に関するページを参照してください。

アップグレード中のダウンタイムは数分以内に収める必要があります。 このダウンタイムは、すべてのメモリ内データをフラッシュし、新しいクラスターでサービスを構成して再起動するための手順が原因で発生します。 結果は、ノード数、データ量、およびその他の可変要素によって異なります。

## <a name="review-apache-hbase-compatibility"></a>Apache HBase の互換性の確認

Apache HBase をアップグレードする前に、移行元クラスターと移行先クラスターの HBase バージョンの互換性を確保します。 HBase のバージョン互換性マトリックスと [HBase のリファレンス ガイド](https://hbase.apache.org/book.html#upgrading)のリリース ノートを確認して、ご自身のアプリケーションが新しいバージョンと互換性があることを確認します。

互換性マトリックスの例を次に示します。 Y は互換性を示し、N は潜在的な非互換性を示します。

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

HDInsigh のバージョンと互換性の詳細については、[Azure HDInsight のバージョン](../hdinsight-component-versioning.md)に関するページを参照してください。

## <a name="apache-hbase-cluster-migration-overview"></a>Apache HBase クラスターの移行の概要

Azure HDInsight で Apache HBase クラスターをアップグレードするには、次の基本手順を実行します。 詳細な使用説明については、詳細な手順とコマンドに関するセクションを参照してください。

次の操作を行い、移行元クラスターを準備します。
1. データ インジェストを停止します。
1. memstore データをフラッシュします。
1. Ambari から HBase を停止します。
1. 高速書き込みを使用するクラスターの場合は、先書きログ (WAL) ディレクトリをバックアップします。

次の操作を行い、移行先クラスターを準備します。
1. 移行先クラスターを作成します。
1. Ambari から HBase を停止します。
1. HDFS service config の `fs.defaultFS` を更新して、移行元クラスターの元のコンテナーを参照します。
1. 高速書き込みを使用するクラスターの場合は、HBase service config の `hbase.rootdir` を更新して、移行元クラスターの元のコンテナーを参照します。
1. Zookeeper データをクリーンアップします。

次の操作を行い、移行を完了します。
1. WAL をクリーンアップして移行します。
1. 移行先クラスターの既定のコンテナーから移行元の元のコンテナーにアプリをコピーします。
1. Ambari 移行先クラスターからすべてのサービスを開始します。
1. HBase を確認します。
1. 移行元クラスターを削除します。

## <a name="detailed-migration-steps-and-commands"></a>移行の詳細な手順とコマンド

これらの詳細な手順とコマンドを使用して、Apache HBase クラスターを移行します。

### <a name="prepare-the-source-cluster"></a>移行元クラスターを準備する

1. 移行元 HBase クラスターへのインジェストを停止します。
   
1. アップグレードする、移行元 HBase クラスターをフラッシュします。
   
   HBase では、*memstore* と呼ばれるメモリ内ストアに受信データが書き込まれます。 memstore が一定のサイズに達すると、HBase はクラスターのストレージ アカウントに長期保存するためにディスクにフラッシュします。 アップグレード後に移行元クラスターを削除すると、memstores 内のデータもすべて削除されます。 データを保持するには、アップグレードの前に、各テーブルの memstore をディスクに手動でフラッシュします。
   
   memstore データは、[Azure hbase-utils GitHub リポジトリ](https://github.com/Azure/hbase-utils/)から [flush_all_tables.sh](https://github.com/Azure/hbase-utils/blob/master/scripts/flush_all_tables.sh) スクリプトを実行することで、フラッシュできます。
   
   また、HDInsight クラスターから次の HBase シェル コマンドを実行して、memstore データをフラッシュすることもできます。
   
   ```bash
   hbase shell
   flush "<table-name>"
   ```
   
1. `https://<OLDCLUSTERNAME>.azurehdinsight.net` を使用して、移行元クラスター上の [Apache Ambari](https://ambari.apache.org/) にサインインし、HBase サービスを停止します。
   
1. 確認メッセージが表示されたら、チェックボックスをオンにして HBase のメンテナンス モードを起動します。
   
   Ambari への接続とその使用方法の詳細については、「[Ambari Web UI を使用した HDInsight クラスターの管理](../hdinsight-hadoop-manage-ambari.md)」を参照してください。
   
1. 移行元 HBase クラスターに[高速書き込み](apache-hbase-accelerated-writes.md)機能がない場合、この手順はスキップします。 高速書き込みがある移行元 HBase クラスターの場合は、移行元クラスターの任意の Zookeeper ノードまたはワーカー ノード上の SSH セッションから次のコマンドを実行して、HDFS の下の WAL ディレクトリをバックアップします。
   
   ```bash
   hdfs dfs -mkdir /hbase-wal-backup
   hdfs dfs -cp hdfs://mycluster/hbasewal /hbase-wal-backup
   ```
   
### <a name="prepare-the-destination-cluster"></a>移行先クラスターを準備する

1. Microsoft Azure Portal で、移行元クラスターと同じストレージ アカウントを使用して、[新しい移行先 HDInsight クラスターを設定](../hdinsight-hadoop-provision-linux-clusters.md)します。ただし、コンテナー名は違うものを使用してください。

   
1. `https://<NEWCLUSTERNAME>.azurehdinsight.net` からアクセスできる、新しいクラスター上の [Apache Ambari](https://ambari.apache.org/) にサインインして、HBase サービスを停止します。
   
1. **[サービス]**  >  **[HDFS]**  >  **[Configs]**  >  **詳細設定**  >  **詳細設定コアサイト** で、`fs.defaultFS` HDFS 設定を変更して、移行元クラスターの元のコンテナー名を指すように設定します。 たとえば、次のスクリーンショットの設定を "`wasbs://hbase-upgrade-old-2021-03-22`" に変更する必要があります。
   
   :::image type="content" source="./media/apache-hbase-migrate-new-version/hdfs-advanced-settings.png" alt-text="Ambari で、[サービス] > [HDFS] > [Configs] > 詳細設定 > 詳細設定コアサイト を選択し、コンテナー名を変更する。" border="false":::
   
1. 移行先クラスターに高速書き込み機能がある場合は、 `hbase.rootdir` パスを変更して、移行元クラスターの元のコンテナー名を指すように設定します。 たとえば、次のパスは `hbase-upgrade-old-2021-03-22` に変更する必要があります。 クラスターに高速書き込みがない場合、この手順はスキップします。
   
   :::image type="content" source="./media/apache-hbase-migrate-new-version/change-container-name-for-hbase-rootdir.png" alt-text="Ambari で、HBase rootdir のコンテナー名を変更する。" border="true":::
   
1. 任意の Zookeeper ノードまたはワーカー ノードで次のコマンドを実行して、移行先クラスターの Zookeeper データをクリーンアップします。
   
   ```bash
   hbase zkcli
   rmr /hbase-unsecure
   quit
   ```
   
### <a name="clean-and-migrate-wal"></a>WAL をクリーンアップして移行する

以下のコマンドを実行します。実行内容は、移行元 HDI のバージョンおよび、移行元と移行先のクラスターに高速書き込みがあるかどうかによって異なります。

- HDI 3.6 は Basic サポート 内にあり、新しいクラスターでは推奨されていないため、移行先クラスターは常に HDI バージョン 4.0 です。
- HDFS の copy コマンドは `hdfs dfs <copy properties starting with -D> -cp <source> <destination> # Serial execution` です。

> [!NOTE]  
> - ストレージ タイプ WASB の `<source-container-fullpath>` は `wasbs://<source-container-name>@<storageaccountname>.blob.core.windows.net` です。
> - ストレージ タイプ Azure Data Lake Storage Gen2 の `<source-container-fullpath>` は`abfs://<source-container-name>@<storageaccountname>.dfs.core.windows.net` です。

- [移行元クラスターが高速書き込みありの HDI 3.6 で、移行先クラスターに高速書き込みがある](#the-source-cluster-is-hdi-36-or-hdi-40-with-accelerated-writes-and-the-destination-cluster-has-accelerated-writes)。
- [移行元クラスターが高速書き込みなしの HDI 3.6 で、移行先クラスターに高速書き込みがある](#the-source-cluster-is-hdi-36-without-accelerated-writes-and-the-destination-cluster-has-accelerated-writes)。
- [移行元クラスターが高速書き込みなしの HDI 3.6 で、移行先クラスターに高速書き込みがない](#the-source-cluster-is-hdi-36-without-accelerated-writes-and-the-destination-cluster-doesnt-have-accelerated-writes)。
- [移行元クラスターが高速書き込みありの HDI 4.0 で、移行先クラスターに高速書き込みがある](#the-source-cluster-is-hdi-36-or-hdi-40-with-accelerated-writes-and-the-destination-cluster-has-accelerated-writes)。
- [移行元クラスターが高速書き込みなしの HDI 4.0 で、移行先クラスターに高速書き込みがある](#the-source-cluster-is-hdi-40-without-accelerated-writes-and-the-destination-cluster-has-accelerated-writes)。
- [移行元クラスターが高速書き込みなしの HDI 4.0 で、移行先クラスターに高速書き込みがない](#the-source-cluster-is-hdi-40-without-accelerated-writes-and-the-destination-cluster-doesnt-have-accelerated-writes)。

#### <a name="the-source-cluster-is-hdi-36-or-hdi-40-with-accelerated-writes-and-the-destination-cluster-has-accelerated-writes"></a>移行元クラスターが高速書き込みありの HDI 3.6 または HDI 4.0 で、移行先クラスターに高速書き込みがある

移行先クラスターの WAL FS データをクリーンアップし、移行元クラスターから移行先クラスターの HDFS に WAL ディレクトリをコピーします。 移行先クラスターの任意の Zookeeper ノードまたはワーカー ノードで次のコマンドを実行して、ディレクトリをコピーします。

```bash   
sudo -u hbase hdfs dfs -rm -r hdfs://mycluster/hbasewal
sudo -u hbase hdfs dfs -cp <source-container-fullpath>/hbase-wal-backup/hbasewal hdfs://mycluster/
```
#### <a name="the-source-cluster-is-hdi-36-without-accelerated-writes-and-the-destination-cluster-has-accelerated-writes"></a>移行元クラスターが高速書き込みなしの HDI 3.6 で、移行先クラスターに高速書き込みがある

移行先クラスターの WAL FS データをクリーンアップし、移行元クラスターから移行先クラスターの HDFS に WAL ディレクトリをコピーします。 移行先クラスターの任意の Zookeeper ノードまたはワーカー ノードで次のコマンドを実行して、ディレクトリをコピーします。

```bash
sudo -u hbase hdfs dfs -rm -r hdfs://mycluster/hbasewal
sudo -u hbase hdfs dfs -Dfs.azure.page.blob.dir="/hbase/WALs,/hbase/MasterProcWALs,/hbase/oldWALs" -cp <source-container>/hbase/*WALs hdfs://mycluster/hbasewal
```

#### <a name="the-source-cluster-is-hdi-36-without-accelerated-writes-and-the-destination-cluster-doesnt-have-accelerated-writes"></a>移行元クラスターが高速書き込みなしの HDI 3.6 で、移行先クラスターに高速書き込みがない

移行先クラスターの WAL FS データをクリーンアップし、移行元クラスターの WAL ディレクトリを、移行先クラスターの HDFS にコピーします。 ディレクトリをコピーするために、移行先クラスターの任意の Zookeeper ノードまたはワーカー ノードで次のコマンドを実行します。

```bash
sudo -u hbase hdfs dfs -rm -r /hbase-wals/*
sudo -u hbase hdfs dfs -Dfs.azure.page.blob.dir="/hbase/WALs,/hbase/MasterProcWALs,/hbase/oldWALs" -cp <source-container-fullpath>/hbase/*WALs /hbase-wals
```

#### <a name="the-source-cluster-is-hdi-40-without-accelerated-writes-and-the-destination-cluster-has-accelerated-writes"></a>移行元クラスターが高速書き込みなしの HDI 4.0 で、移行先クラスターに高速書き込みがある

移行先クラスターの WAL FS データをクリーンアップし、移行元クラスターから移行先クラスターの HDFS に WAL ディレクトリをコピーします。 移行先クラスターの任意の Zookeeper ノードまたはワーカー ノードで次のコマンドを実行して、ディレクトリをコピーします。

```bash
sudo -u hbase hdfs dfs -rm -r hdfs://mycluster/hbasewal
sudo -u hbase hdfs dfs -cp <source-container-fullpath>/hbase-wals/* hdfs://mycluster/hbasewal
   ```

#### <a name="the-source-cluster-is-hdi-40-without-accelerated-writes-and-the-destination-cluster-doesnt-have-accelerated-writes"></a>移行元クラスターが高速書き込みなしの HDI 4.0 で、移行先クラスターに高速書き込みがない

移行先クラスターの WAL FS データをクリーンアップし、移行元クラスターの WAL ディレクトリを、移行先クラスターの HDFS にコピーします。 ディレクトリをコピーするために、移行先クラスターの任意の Zookeeper ノードまたはワーカー ノードで次のコマンドを実行します。

```bash
sudo -u hbase hdfs dfs -rm -r /hbase-wals/*
sudo -u hbase hdfs dfs -Dfs.azure.page.blob.dir="/hbase-wals" -cp <source-container-fullpath>/hbase-wals /
```

### <a name="complete-the-migration"></a>移行を完了する

1. `sudo -u hdfs` ユーザー コンテキストを使用して、フォルダー `/hdp/apps/<new-version-name>` とその内容を、`<destination-container-fullpath>` から `<source-container-fullpath>` の下の `/hdp/apps` フォルダーにコピーします。 移行先クラスターで次のコマンドを実行して、フォルダーをコピーできます。
   
   ```bash   
   sudo -u hdfs hdfs dfs -cp /hdp/apps/<hdi-version> <source-container-fullpath>/hdp/apps
   ```
   
   次に例を示します。
   ```bash
   sudo -u hdfs hdfs dfs -cp /hdp/apps/4.1.3.6 wasbs://hbase-upgrade-old-2021-03-22@hbaseupgrade.blob.core.windows.net/hdp/apps
   ```
   
1. 移行先クラスターで変更を保存し、Ambari で示される、必要なすべてのサービスを再起動します。
   
1. アプリケーションを移行先クラスターを指すように設定します。
   
   > [!NOTE]  
   > アップグレードすると、アプリケーションの静的 DNS 名が変更されます。 この DNS 名をハードコーディングする代わりに、ドメイン名の DNS 設定でクラスターの名前を指す CNAME を構成できます。 もう 1 つの選択肢は、再デプロイせずに更新できる、アプリケーション用の構成ファイルを使用することです。
   
1. インジェストを開始します。
   
1. HBase の一貫性と、単純なデータ定義言語 (DDL) およびデータ操作言語 (DML) の動作を確認します。
   
1. 移行先クラスターに問題がない場合は、移行元クラスターを削除します。

## <a name="next-steps"></a>次のステップ

[Apache HBase](https://hbase.apache.org/) の詳細と HDInsight クラスターのアップグレードについては、次の記事を参照してください。

- [HDInsight クラスターを新しいバージョンにアップグレードする](../hdinsight-upgrade-cluster.md)
- [Apache Ambari Web UI を使用して Azure HDInsight を監視および管理する](../hdinsight-hadoop-manage-ambari.md)
- [Azure HDInsight のバージョン](../hdinsight-component-versioning.md)
- [Apache HBase の最適化](../optimize-hbase-ambari.md)
