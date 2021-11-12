---
title: クラスターを最新版に移行する
titleSuffix: Azure HDInsight
description: Azure HDInsight クラスターを新しいバージョンに移行するためのガイドラインを説明します。
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 01/31/2020
ms.openlocfilehash: 6fa655092de1c3e103381ccd58bf840b70a3809e
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130249763"
---
# <a name="migrate-hdinsight-cluster-to-a-newer-version"></a>HDInsight クラスターを新しいバージョンに移行する

最新の HDInsight 機能を利用するために、HDInsight クラスターを最新版に定期的に移行することをお勧めします。 HDInsight では、既存のクラスターが新しいコンポーネント バージョンにアップグレードされるインプレース アップグレードがサポートされていません。 希望するコンポーネントとプラットフォーム バージョンで新しいクラスターを作成し、その新しいクラスターを使用するために、お使いのアプリケーションを移行する必要があります。 HDInsight クラスターのバージョンを移行するには、下のガイドラインに従います。

> [!NOTE]  
> HDInsight のサポートされているバージョンについては、[HDInsight コンポーネントのバージョン](hdinsight-component-versioning.md#supported-hdinsight-versions)に関するページを参照してください。

## <a name="migration-tasks"></a>移行タスク

HDInsight クラスターをアップグレードするワークフローは次のとおりです。
:::image type="content" source="./media/hdinsight-upgrade-cluster/upgrade-workflow-diagram.png" alt-text="HDInsight アップグレード ワークフローの図" border="false":::

1. このドキュメントの各セクションを読んで、HDInsight クラスターに移行するときに必要になる場合がある変更について理解します。
2. テスト/品質保証環境として、クラスターを作成します。 クラスターの作成の詳細については、「[HDInsight での Linux ベースの Hadoop クラスターの作成](hdinsight-hadoop-provision-linux-clusters.md)」を参照してください。
3. 既存のジョブ、データ ソース、およびシンクを新しい環境にコピーします。
4. 検証テストを実行し、新しいクラスターで予期したとおりにジョブが動作していることを確認します。

すべて予期したとおりに動作していることを確認したら、移行のダウンタイムをスケジュールします。 このダウンタイム中に、次の操作を実行します。

1. クラスター ノードでローカルに格納されている一時的なデータをバックアップします。 たとえば、ヘッド ノードに直接データを格納している場合です。
1. [既存のクラスターを削除します](./hdinsight-delete-cluster.md)。
1. 同じ VNET サブネットの中に、前のクラスターで使用していたのと同じ既定のデータ ストアを使用する、最新の (またはサポートされている) バージョンの HDI クラスターを作成します。 これで、新しいクラスターで既存の運用データを引き続き使用できます。
1. バックアップしたすべての一時的なデータをインポートします。
1. 新しいクラスターを使用して、ジョブを開始または処理を続行します。

## <a name="workload-specific-guidance"></a>ワークロード固有のガイダンス

特定のワークロードを移行する方法については、次のドキュメントにガイドラインが記載されています。

* [HBase を移行する](./hbase/apache-hbase-migrate-new-version.md)
* [Kafka を移行する](./kafka/migrate-versions.md)
* [Hive/Interactive Query を移行する](./interactive-query/apache-hive-migrate-workloads.md)

## <a name="backup-and-restore"></a>バックアップと復元

データベースのバックアップと復元の詳細については、[自動データベース バックアップを使用した Azure SQL Database 内のデータベースの復旧](../azure-sql/database/recovery-using-backups.md)に関する記事を参照してください。

## <a name="upgrade-scenarios"></a>アップグレードのシナリオ

前述のように、新しい機能と修正プログラムを活用するために、HDInsight クラスターを定期的に最新バージョンに移行することをお勧めします。 クラスターを削除して再デプロイすることを要求する理由を次の一覧で確認してください。

* クラスターのバージョンが[インベントリから削除済み](hdinsight-retired-versions.md)か [Basic サポート](hdinsight-36-component-versioning.md)にあり、クラスターの問題が発生している場合、新しいバージョンで解決されます。
* クラスターの問題の根本原因は、小さな VM に関連していると判断されます。 [Microsoft の推奨されるノード構成を表示します](hdinsight-supported-node-configuration.md#all-supported-regions-except-brazil-south-and-japan-west)。
* お客様がサポートケースを開き、Microsoft のエンジニアリングチームが、新しいクラスター バージョンで既に問題が修正されていることを確認します。
* 既定のメタストア データベース (Ambari、Hive、Oozie、Ranger) が使用率の上限に達しました。 Microsoft から、[カスタム メタストア](hdinsight-use-external-metadata-stores.md#custom-metastore) データベースを使用してクラスターを再作成するように求めるメッセージが表示されます。
* クラスターの問題の根本原因は、**サポートされていない操作** が原因です。 サポートされていない一般的な操作の一部を次に示します。
     * **Ambari でのサービスの移動または追加**。 Ambari のクラスター サービスの情報を表示するときに、[サービス アクション] メニューから実行できる操作の 1 つは、 **[サービス名] の移動** です。 別のアクションとして **[サービス名] を追加** します。 これらのオプションはどちらもサポートされていません。
     * **Python パッケージが破損しています**。 HDInsight クラスターは、組み込みの Python 環境 (Python 2.7 と Python 3.5) に依存しています。 これらの既定の組み込み環境にカスタム パッケージを直接インストールすると、予期しないライブラリ バージョンの変更が発生し、クラスターが壊れる可能性があります。 Spark アプリケーションの[カスタム外部 Python パッケージを安全にインストールする](./spark/apache-spark-python-package-installation.md#safely-install-external-python-packages)方法を参照してください。
     * **サードパーティ製ソフトウェア**。 お客様は、HDInsight クラスターにサードパーティ製ソフトウェアをインストールすることができます。ただし、既存の機能が壊れている場合は、クラスターを再作成することをお勧めします。
     * **同じクラスターに複数のワークロードがあります**。 HDInsight 4.0 では、Hive Warehouse Connector には、Spark ワークロードと Interactive Query ワークロード用に、個別のクラスターが必要です。 [次の手順に従って、Azure HDInsight に両方のクラスターを設定します。](interactive-query/apache-hive-warehouse-connector.md) 同様に、[Spark と HBASE](hdinsight-using-spark-query-hbase.md) を統合するには、2 つの異なるクラスターが必要です。
     * **カスタム Ambari DB パスワードが変更されました**。 Ambari DB パスワードは、クラスターの作成時に設定され、更新するための現在のメカニズムはありません。 顧客が[カスタム Ambari DB](hdinsight-custom-ambari-db.md) を使用してクラスターをデプロイする場合、そのユーザーは SQL DB の DB パスワードを変更することができます。ただし、実行中の HDInsight クラスターに対してこのパスワードを更新する方法はありません。

## <a name="next-steps"></a>次のステップ

* [Linux ベースの HDInsight クラスターを作成する方法を確認する](hdinsight-hadoop-provision-linux-clusters.md)
* [SSH を使用して HDInsight に接続する](hdinsight-hadoop-linux-use-ssh-unix.md)
* [Apache Ambari を使用して Linux ベースのクラスターを管理する](hdinsight-hadoop-manage-ambari.md)
* [HDInsight リリース ノート](./hdinsight-version-release.md)
