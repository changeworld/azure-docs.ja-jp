---
title: オンプレミスの Apache Hadoop クラスターの Azure HDInsight への移行 - データ移行のベスト プラクティス
description: オンプレミスの Apache Hadoop クラスターを Azure HDInsight に移行することについてのデータ移行のベスト プラクティスについて説明します。
services: hdinsight
author: hrasheed-msft
ms.reviewer: ashishth
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/25/2018
ms.author: hrasheed
ms.openlocfilehash: 6b06b8eb8d5e18acd3107ec5cccac79fc7be7edc
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/31/2018
ms.locfileid: "50418179"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight---data-migration-best-practices"></a>オンプレミスの Apache Hadoop クラスターの Azure HDInsight への移行 - データ移行のベスト プラクティス

この記事では、Azure HDInsight へのデータ移行に関する推奨事項について説明します。 これはオンプレミスの Apache Hadoop システムを Azure HDInsight に移行することを支援するためのベスト プラクティスを提供するシリーズの一部です。

## <a name="migrate-data-from-on-premises-to-azure"></a>オンプレミスから Azure へのデータの移行

データをオンプレミスから Azure 環境に移行するには、主に次の 2 つのオプションがあります。

1.  TLS を使用してネットワーク経由でデータを転送する
    1.  インターネット経由
    2.  ExpressRoute
2.  データの発送
    1.  インポート/エクスポート サービス
        - 内部 SATA HDD または SSD のみ
        - 保存時に暗号化 (AES-128 / AES-256)
        - インポート ジョブでは最大 10 個のディスクを使用できる
        - すべてのパブリック リージョンおよび GA で利用可能
    1.  Data Box
        - データ ボックスごとに最大 80 TB のデータ
        - 保存時に暗号化 (AES-256)
        - NAS プロトコルを使用し、一般的なデータ コピー ツールをサポート
        - 耐久性の高いハードウェア
        - 米国のみパブリック プレビューで使用可能

次の表に、データ ボリュームとネットワーク帯域幅に基づくおおまかなデータ転送時間を示します。 データ移行に 3 週間以上かかることが予想される場合は Data Box を使用してください。

|**データ量**|**ネットワーク帯域幅**|||
|---|---|---|---|
|| **45 Mbps (T3)**|**100 Mbps**|**1 Gbps**|**10 Gbps**
|1 TB (テラバイト)|2 日|1 日| 2 時間|14 分|
|10 TB|22 日|10 日|1 日|2 時間|
|35 TB|76 日|34 日|3 日|8 時間|
|80 TB|173 日|78 日|8 日|19 時間|
|100 TB|216 日|97 日|10 日|1 日|
|200 TB|1 年|194 日|19 日|2 日|
|500 TB|3 年|1 年|49 日|5 日|
|1 PB|6 年|3 年|97 日|10 日|
|2 PB|12 年|5 年|194 日|19 日|

ネットワーク経由でデータを転送するために、DistCp、Azure Data Factory、AzureCp などの Azure のネイティブ ツールを使用できます。 同じ目的で、サード パーティ製ツール WANDisco も使用できます。 オンプレミスから Azure のストレージ システムへの継続的なデータ転送には、Kafka Mirrormaker および Sqoop を使用できます。

## <a name="performance-considerations-when-using-apache-distcp"></a>Apache DistCp を使用するときのパフォーマンスに関する考慮事項

DistCp は、 MapReduce マップ ジョブを使用して、データ転送、エラーの処理、およびそれらのエラー回復を行う Apache プロジェクトです。 ソース ファイルの一覧が、各マップ タスクに割り当てられます。 マップ タスクは、割り当て済みのすべてのファイルを宛先にコピーします。 いくつかの手法によって、DistCp のパフォーマンスを向上させることができます。

### <a name="increase-the-number-of-mappers"></a>マッパーの数を増やす

DistCp では、それぞれがほぼ同じバイト数をコピーするようにするために、マップ タスクを作成しようとしています。 既定では、DistCp ジョブは 20 個のマッパーを使用します。 Distcp に対して使用するマッパーを増やす (コマンド ラインで「m」パラメーターを指定する) と データ転送プロセス中の並列度が増加し、データ転送時間が減少します。 ただし、Mapper の数を増やすときに次の 2 つのことを考慮する必要があります。

1. DistCp の最小粒度は 1 つのファイルです。 マッパーの数をソース ファイルの数より多く指定しても効果はなく、使用可能なクラスター リソースが浪費されます。
1. マッパーの数を決定する場合はクラスターで使用可能な Yarn メモリを検討してください。 各マップ タスクは Yarn コンテナーとして起動されます。 負荷の大きい別のワークロードがクラスター上で実行中でない場合、マッパー数は m = (ワーカー ノードの数 \* worker ノードごとの YARN メモリ) / YARN コンテナーサイズという式で決定できます。 ただし、他のアプリケーションがメモリを使用している場合は、クラスターの YARN メモリで DistCp ジョブに対する部分のみを使用するように選択できます。

### <a name="use-more-than-one-distcp-job"></a>複数の DistCp ジョブを使用する

移動するデータセットのサイズが 1 TB より大きい場合は、複数の DistCp ジョブを使用します。 複数のジョブを使用すると、障害の影響が制限されます。 いずれかのジョブが失敗した場合、すべてのジョブではなく、その特定のジョブのみ再起動する必要があります。

### <a name="consider-splitting-files"></a>ファイルの分割を検討する

大きいファイルが少ししかない場合、マッパーを増やして潜在的な並列性の機能を高めるために、ファイルを 256 MB のファイル チャンクに分割することを検討してください。

### <a name="use-the-strategy-command-line-parameter"></a>'strategy' コマンドライン パラメーターを使用する

コマンド ラインで `strategy = dynamic` パラメーターを使用することを検討します。 `strategy` パラメーターの既定値は `uniform size` で、この場合、各マップはほぼ同じバイト数だけコピーします。 このパラメーターが `dynamic` に変更されると、リスティング ファイルはいくつかの「チャンク ファイル」に分割されます。 チャンク ファイルの数はマップの数の倍数です。 各マップ タスクには、いずれかのチャンク ファイルが割り当てられます。 チャンク内のすべてのパスが処理された後、現在のチャンクが削除され、新しいチャンクが取得されます。 この処理はチャンクがなくなるまで続きます。 この「動的」アプローチでは、高速なマップタスクが低速のものより多くのパスを使用できるため、DistCp ジョブが全体として高速化されます。

### <a name="increase-the-number-of-threads"></a>スレッドの数を増やす

`-numListstatusThreads` パラメーターを増やすとパフォーマンスが向上するかどうかを確認してみてください。 このパラメーターは、ファイルの一覧を構築するために使用するスレッドの数を制御し、最大値は 40 です。

### <a name="use-the-output-committer-algorithm"></a>出力コミッター アルゴリズムを使用する

`-Dmapreduce.fileoutputcommitter.algorithm.version=2` パラメーターを渡すと DistCp のパフォーマンスが向上するかどうかを確認してみてください。 この出力コミッター アルゴリズムは、保存先への出力ファイルの書き込みを最適化します。 次のコマンドは、さまざまなパラメーターの使用状況を示す例です。

```bash
hadoop distcp -Dmapreduce.fileoutputcommitter.algorithm.version=2 -numListstatusThreads 30 -m 100 -strategy dynamic hdfs://nn1:8020/foo/bar wasb://<container_name>@<storage_account_name>.blob.core.windows.net/foo/
```

## <a name="metadata-migration"></a>メタデータの移行

### <a name="hive"></a>Hive

Hive metastore はスクリプトを使用するか、DB のレプリケーションを使用して移行できます。

#### <a name="hive-metastore-migration-using-scripts"></a>スクリプトを使用した Hive metastore の移行

1. オンプレミス Hive metastore から Hive DDL を生成します。 この手順は [ラッパー bash スクリプト] を使用して行うことができます。(https://github.com/hdinsight/hdinsight.github.io/blob/master/hive/hive-export-import-metastore.md)
1. 生成された DDL を編集して、HDFS の url を WASB/ADLS/ABFS の URL に置き換えます
1. メタストア上の更新された DDL を HDInsight クラスターから実行します
1. Hive metastore のバージョンが、オンプレミスとクラウド間で互換性があるかどうかを確認します

#### <a name="hive-metastore-migration-using-db-replication"></a>DB レプリケーションを使用した Hive metastore の移行

- オンプレミス Hive metastore DB と HDInsight の metastore DB の間でデータベース レプリケーションをセットアップします
- 「Hive MetaTool」を使用して、HDFS の url を WASB/ABFS/WASB または ADLS の url で置き換えます。たとえば次のようにします

```bash
./hive --service metatool -updateLocation hdfs://nn1:8020/ wasb://<container_name>@<storage_account_name>.blob.core.windows.net/
```

### <a name="ranger"></a>Ranger

- オンプレミスの Ranger ポリシーを xml ファイルにエクスポートします。
- XSLT などのツールを使用して、オンプレミスの特定の HDFS ベースのパスを特定の WASB または ADLS に変換します。
- HDInsight で実行されている Ranger にポリシーをインポートします。

## <a name="next-steps"></a>次の手順

このシリーズの次の記事をお読みください。

- [オンプレミスから Azure HDInsight Hadoop への移行のためのセキュリティおよび DevOps のベスト プラクティス](apache-hadoop-on-premises-migration-best-practices-security-devops.md)