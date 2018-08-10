---
title: Azure HDInsight のリリース ノート (アーカイブ)
description: Azure HDInsight のリリース ノートとバージョン (アーカイブ)。
services: hdinsight
editor: jasonwhowell
author: jasonwhowell
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 03/20/2018
ms.author: jasonh
ms.openlocfilehash: 34536316fef5f30df4ef1e3c89ddaaafed188c5d
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/07/2018
ms.locfileid: "39600086"
---
# <a name="archived-release-notes-for-azure-hdinsight"></a>Azure HDInsight のリリース ノート (アーカイブ)

**最新**の Azure HDInsight リリース更新プログラムについては、[HDInsight リリース ノート](hdinsight-release-notes.md)のページをご覧ください。

> [!IMPORTANT]
> Linux は、バージョン 3.4 以上の HDInsight で使用できる唯一のオペレーティング システムです。 詳細については、[HDInsight のバージョン管理に関する記事](hdinsight-component-versioning.md)に関するページを参照してください。

## <a name="notes-for-06272018---release-of-new-open-source-versions-adls-gen2-etc-on-hdinsight-36"></a>2018 年 6 月 27 日のメモ: HDInsight 3.6 上の新しいオープン ソース バージョン (ADLS Gen2 など) のリリース
HDInsight の 2018 年 6 月のリリースは、ユーザーに役立つ多数の新しい更新と機能を追加した重要なリリースです。 詳細については、こちらの[投稿](https://azure.microsoft.com/en-us/blog/enterprises-get-deeper-insights-with-hadoop-and-spark-updates-on-azure-hdinsight/)を参照してください。

主な特長は次のとおりです。 詳細なリリース ノート、バグの修正、既知の問題については、「[Azure HDInsight の Hadoop コンポーネントのリリース ノート](hdinsight-release-notes.md)」をご覧ください。

- **Hadoop と他のオープン ソース プロジェクトの更新**: この更新では、20 以上のオープン ソース プロジェクト全体で 1,000 個以上のバグが修正され、さらに新しいバージョンの Spark (2.3) と Kafka (1.0) も追加されました。
- **Microsoft R Server 9.1 から Machine Learning Services 9.3 への更新**: このリリースでは、データ サイエンティストとエンジニア向けに先進的なアルゴリズムと使いやすさで強化したオープン ソースを提供しています。すべての機能を Apache Spark の速度で、好みの言語で利用できます。 このリリースでは、Microsoft R Server で提供されている機能に基づいて拡張し、Python のサポートを追加したため、クラスター名を Microsoft R Server から ML Services に変更しました。 
- **Azure Data Lake Storage Gen2 のサポート**: HDInsight は、Azure Data Lake Storage Gen2 のプレビュー リリースをサポートします。 利用可能なリージョンのお客様は、HDInsight クラスターのストアとして ADLS Gen2 アカウントを選択できるようになります。
- **HDInsight Enterprise セキュリティ パッケージの更新プログラム (プレビュー)**: (プレビュー) Virtual Network サービス エンドポイントは、Azure Blob Storage、ADLS Gen1、Cosmos DB、および Azure DB をサポートします。 

## <a name="notes-for-03202018---release-of-spark-22-on-hdinsight-36"></a>2018 年 3 月 20 日 - HDInsight 3.6 での Spark 2.2 のリリース

- Spark 2.2.0 では、Spark Core、SQL、ML での安定性が向上し、Structured Streaming が GA 状態になります。 Spark 2.2.0 は現在 HDInsight 3.6 で利用可能です。


## <a name="notes-for-08012017-release-of-hdinsight"></a>HDInsight の 2017 年 8 月 1 日リリース

| タイトル | 説明 | 影響を受ける領域  | クラスターの種類  | 
| --- | --- | --- | --- | --- |
| HDInsight での Microsoft R Server 9.1 の リリース |HDInsight では、HDInsight での Microsoft R Server 9.1 クラスターのプロビジョニングをサポートするようになりました。 Microsoft R Server 9.1 リリースの詳細については、[このブログ](https://blogs.technet.microsoft.com/dataplatforminsider/2017/04/19/introducing-microsoft-r-server-9-1-release/)をご覧ください。 |Service |R Server |
| HDInsight 3.6 に Hadoop スタックの新バージョンを搭載|<ul><li>更新されたバージョンの詳細な一覧については、[HDInsight で使用可能な Hadoop コンポーネント バージョン](hdinsight-component-versioning.md#hadoop-components-available-with-different-hdinsight-versions)に関するページをご覧ください。</li><li>Hadoop スタックの最新バージョンで修正されたバグの一覧については、「[Apache Patch Information (Apache パッチ情報)](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.1/bk_release-notes/content/patch_parent.html)」をご覧ください。</li><li>HDP 2.6.1 (現在 HDInsight 3.6 で利用可能) の間での破壊的変更の一覧については、[https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.1/bk_release-notes/content/behavior_changes.html](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.1/bk_release-notes/content/behavior_changes.html) を参照してください。</li><li>HDP 2.6.1 の既知の問題の一覧については、「[Known issues (既知の問題)](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.1/bk_release-notes/content/known_issues.html)」をご覧ください。</li></ul> |Service |All |該当なし |
| インタラクティブ Hive (プレビュー) クラスターの更新 |<ul><li><b>機能改善。</b> メタデータをキャッシュすることによってバックエンド SQL への負荷を削減し、すべてのメタデータ操作のパフォーマンスを向上させるキャッシュ metastore を実装。  この改善は、すべてのインタラクティブ Hive クラスターに適用されています。 詳細については、[https://issues.apache.org/jira/browse/HIVE-16520](https://issues.apache.org/jira/browse/HIVE-16520) を参照してください。</li><li><b>機能改善。</b> パーティションの動的読み込みを最適化。 詳細については、[https://issues.apache.org/jira/browse/HIVE-14204] (https://issues.apache.org/jira/browse/HIVE-14204) を参照してください。</li><li><b>機能改善。</b> Linux 上の HDInsight の構成を最適化。</li><li><b>バグ修正。</b> `CredentialProviderFactory$getProviders` がスレッドセーフではない。 この問題は解決されました。 詳細については、[https://issues.apache.org/jira/browse/HADOOP-14195](https://issues.apache.org/jira/browse/HADOOP-14195) を参照してください。</li><li><b>バグ修正。</b> WASB ドライバーの `liststatus` API の CPU 使用率が高いため、ATS のパフォーマンスが悪化する。 この問題は解決されました。 詳細については、[https://github.com/Azure/azure-storage-java/pull/154](https://github.com/Azure/azure-storage-java/pull/154) を参照してください。</li></ul> |Service |対話型 Hive (プレビュー) |
| Hadoop クラスターの更新 |Templeton ジョブ操作の信頼性が向上。 詳細については、[https://issues.apache.org/jira/browse/HIVE-15947](https://issues.apache.org/jira/browse/HIVE-15947) を参照してください |Service |Hadoop |
| YARN の更新 | HDInsight で (コストを増やすことなく) 250 GB の Ambari データベースが作成されるようになり、カスタマー エクスペリエンスが向上。 この変更により、ATS がいっぱいになる状態が回避されるため、パフォーマンスの改善が見込めます。 |Service |All |
| Spark の更新 | Spark 2.1.1 のリリース。 詳細については、「[Spark Release 2.1.1 (Spark リリース 2.1.1)](https://spark.apache.org/releases/spark-release-2-1-1.html)」をご覧ください。 | Service | Spark |

  



## <a name="04062017---general-availability-of-hdinsight-36"></a>2017 年 4 月 6 日 - HDInsight 3.6 の一般提供

* このリリースでは、Azure HDInsight に、HDP 2.6 に基づくバージョン 3.6 が追加されます。 HDP 2.6 リリース ノートについては、[こちら](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.0/bk_release-notes/content/ch_relnotes.html)を参照してください。また、HDInsight バージョンの詳細については、[こちら](hdinsight-component-versioning.md)を参照してください。 HDInsight 3.6 は、次のワークロードに使用できます。

    * Hadoop v2.7.3
    * HBase v1.1.2
    * Storm v1.1.0
    * Spark v2.1.0
    * Interactive Hive v2.1.0

* **Hive View 2.0 のサポート**。 これによって、Interactive Hive のユーザー エクスペリエンスが向上します。 詳細については、[Hortonworks のドキュメント](http://docs.hortonworks.com/HDPDocuments/Ambari-2.5.0.3/bk_ambari-views/content/ch_using_hive_view.html)を参照してください。

* **Hive LLAP のパフォーマンス強化**。 詳細については、[Hortonworks のドキュメント](https://hortonworks.com/blog/top-5-performance-boosters-with-apache-hive-llap/)を参照してください。

* **Hive の新機能**。 [Hortonworks のドキュメント](https://hortonworks.com/apache/hive/#section_4)をご覧ください。

* **Hive CLI の廃止**: Hive CLI は非推奨になる予定です。お客様には代わりに Beeline を使用することをお勧めします。 詳細については、[Apache のドキュメント](https://cwiki.apache.org/confluence/display/Hive/Replacing+the+Implementation+of+Hive+CLI+Using+Beeline)を参照してください。 HDInsight で Beeline を使用する方法については、[「HDInsight Hadoop クラスターで Beeline を使用する」](hadoop/apache-hadoop-use-hive-beeline.md)を参照してください。

* **Apache Phoenix および HBase の新機能**。
    * ストレージ クォータのサポート: マルチ テナント環境でよく使用され、テーブルごとと名前空間レベルごとに限られたストレージ スペースを許可します。
    * Phoenix のインデックス作成機能の向上: インクリメンタル インデックス作成と以前の障害からのインデックス作成の再構築/再開。
    * Phoenix データ整合性ツール: スキーマ、インデックス、およびその他のメタデータの検証をサポートします。


* **HBase での問題**: CSV 一括アップロード MapReduce ジョブの実行時、次の構文がエラーになることがあります。

        HADOOP_CLASSPATH=$(hbase mapredcp):/path/to/hbase/conf hadoop jar phoenix-<version>-client.jar org.apache.phoenix.mapreduce.CsvBulkLoadTool --table EXAMPLE --input /data/example.csv

    代わりに、次の構文を使用してください。

        HADOOP_CLASSPATH=/path/to/hbase-protocol.jar:/path/to/hbase/conf hadoop jar phoenix-<version>-client.jar org.apache.phoenix.mapreduce.CsvBulkLoadTool --table EXAMPLE --input /data/example.csv


## <a name="02282017---release-of-spark-21-on-hdinsight-36-preview"></a>2017 年 2 月 28 日 - HDInsight 3.6 プレビューでの Spark 2.1 のリリース ノート
* [Spark 2.1](http://spark.apache.org/releases/spark-release-2-1-0.html) では、以前のバージョンにあった安定性と使いやすさに関するさまざまな問題が改善されます。 Spark Core、SQL、ML、ストリーミングなど、すべての Spark ワークロードに及ぶ新機能も導入します。
* 構造化ストリーミングでは、イベント時刻の透かしと Kafka 0.10 コネクタのサポートによってスケーラビリティが向上しています。
* Spark SQL のパーティション分割は、新しいスケーラブルなパーティション処理メカニズムを使用して処理されるようになりました。 アップグレードする方法の詳細については、[ここ](http://spark.apache.org/releases/spark-release-2-1-0.html)を参照してください。
* Azure HDInsight 3.6 プレビュー上の Spark 2.1 は、現在 ODBC ドライバーを使用した BI ツールの接続をサポートしていません。
* このプレビューでは、Spark 2.1 クラスターから Azure Data Lake Store へのアクセスはサポートされていません。


## <a name="11182016---release-of-spark-201-on-hdinsight-35"></a>2016 年 11 月 18 日 - HDInsight 3.5 での Spark 2.0.1 の リリース
Spark 2.0.1 は、現在 Spark クラスター (HDInsight バージョン 3.5) で公開されています。

## <a name="11162016---release-of-r-server-90-on-hdinsight-35-spark-20"></a>2016 年 11 月 16 日 - HDinsight 3.5 (Spark 2.0) での R Server 9.0 の リリース
*   R Server クラスターのバージョン オプションに、HDI 3.5 (Spark 2.0) の R Server 9.0 と HDI 3.4 (Spark 1.6) の R Server 8.0 の 2 つが追加されました。
*   HDI 3.5 (Spark 2.0) の R Server 9.0 は R 3.3.2 上に構築され、Hive および Parquet から直接 Spark DataFrames にデータを読み込み ScaleR で分析するための、RxHiveData と RxParquetData という新しい ScaleR データ ソース関数が追加されています。 詳細については、R の **?RxHiveData** コマンドおよび **?RxParquetData** コマンドを使用して表示されるこれらの関数のインライン ヘルプを参照してください。
*   RStudio コミュニティ エディションが、プロビジョニング フローの一環として、[クラスターの構成] ブレードにおいて既定で (オプトアウト オプションで) インストールされるようになりました。

## <a name="11092016---release-of-spark-20-on-hdinsight"></a>2016 年 11 月 9 日 - HDInsight での Spark 2.0 の リリース
* HDInsight 3.5 の Spark 2.0 クラスターで、Livy サービスおよびJupyter サービスがサポートされるようになりました。

## <a name="10262016---release-of-r-server-on-hdinsight"></a>2016 年 10 月 26 日 - HDInsight での R Server のリリース
* エッジ ノードへのアクセス用の URI が、**clustername**-ed-ssh.azurehdinsight.net に変更されました。
* HDInsight クラスターでの R Server のプロビジョニングの効率が向上しています。
* HDInsight の R Server が、標準の HDInsight "R Server" クラスター タイプとして使用可能になりました。今後は別の HDInsight アプリケーションとしてインストールする必要はありません。 エッジ ノードと R Server バイナリは、R Server クラスター デプロイメントの一部としてプロビジョニングされるようになりました。 これにより、プロビジョニングの速度と信頼性が向上します。 これに伴い、R Server の価格モデルも更新されます。
* R Server クラスター タイプの価格は、Standard Tier の価格に R Server の追加料金を加えたものになります。 この変更は、実際の R Server の価格には影響しません。請求書の記載方法が変わるだけです。 既存のすべての R Server クラスターは引き続き実行でき、Resource Manager テンプレートは非推奨に関する通知が行われるまで使用できます。 **ただし、スクリプト化したデプロイメントが新しい Resource Manager テンプレートを使用するように更新することをお勧めします。**






