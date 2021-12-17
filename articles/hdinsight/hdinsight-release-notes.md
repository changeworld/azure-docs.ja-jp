---
title: Azure HDInsight のリリース ノート
description: Azure HDInsight の最新のリリース ノート。 Hadoop、Spark、Hive などの開発に関するヒントや詳細を紹介します。
ms.custom: references_regions
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/15/2021
ms.openlocfilehash: 7cb93adaa00e11b88dcbe4b104e10acf84a009b9
ms.sourcegitcommit: 147910fb817d93e0e53a36bb8d476207a2dd9e5e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/18/2021
ms.locfileid: "130131412"
---
# <a name="azure-hdinsight-release-notes"></a>Azure HDInsight リリース ノート

この記事では、**最近** の Azure HDInsight リリース更新に関する情報を提供します。 以前のリリースについて詳しくは、「[HDInsight リリース ノートのアーカイブ](hdinsight-release-notes-archive.md)」を参照してください。

## <a name="summary"></a>まとめ

Azure HDInsight は、Azure 上でオープン ソース分析を行うエンタープライズのお客様の間で最も人気のあるサービスの 1 つです。

リリース ノートをサブスクライブする場合は、[この GitHub リポジトリ](https://github.com/hdinsight/release-notes/releases)のリリースをご覧ください。

## <a name="release-date-07272021"></a>リリース日: 2021 年 7 月 27 日

このリリースは HDInsight 3.6 と HDInsight 4.0 の両方に適用されます。 HDInsight リリースは、数日以内にすべてのリージョンでご利用になれます。 ここのリリース日は、最初のリージョンのリリース日です。 以下の変更が見られない場合は、お客様のリージョンで数日以内にリリースがライブになるまでお待ちください。

このリリースの OS バージョンは次のとおりです。
- HDInsight 3.6: Ubuntu 16.04.7 LTS
- HDInsight 4.0: Ubuntu 18.04.5 LTS

## <a name="new-features"></a>新機能
### <a name="azure-hdinsight-support-for-restricted-public-connectivity-is-generally-available-on-oct-15-2021"></a>制限付きパブリック接続に対する Azure HDInsight のサポートは、2021 年 10 月 15 日に一般提供されます
すべてのリージョンの Azure HDInsight で、制限付きパブリック接続がサポートされるようになります。 この機能の主な特徴を次に示します。 

- クラスターからリソース プロバイダーに送信されるように、リソース プロバイダーからクラスターへの通信を反転する機能 
- HDinsight クラスターがプライベート ネットワークのみを使用してリソースにアクセスするため、独自の Private Link 対応リソース (ストレージ、SQL、キー コンテナーなど) の持ち込みのサポート 
- パブリック IP アドレスはプロビジョニングされたリソースではありません 

この新機能を使用すると、HDInsight 管理 IP のインバウンド ネットワーク セキュリティ グループ (NSG) のサービス タグ規則をスキップすることもできます。  [パブリック接続の制限](./hdinsight-restrict-public-connectivity.md)の詳細を確認してください

### <a name="azure-hdinsight-support-for-azure-private-link-is-generally-available-on-oct-15-2021"></a>Azure Private Link に対する Azure HDInsight のサポートは、2021 年 10 月 15 日に一般提供されます
プライベート エンドポイントを使用して、プライベート リンク経由で HDInsight クラスターに接続できるようになります。プライベート リンクは、VNET ピアリングが使用できない、または有効になっていないクロス VNET シナリオで利用できます。 

Azure Private Link を使用すると、仮想ネットワーク内の [プライベート エンドポイント](../private-link/private-endpoint-overview.md)経由で、Azure PaaS サービス (Azure Storage、SQL Database など) と Azure でホストされている顧客所有の、またはパートナーのサービスにアクセスできます。  

仮想ネットワークとサービスの間のトラフィックは、Microsoft のバックボーン ネットワークを通ります。 パブリック インターネットにサービスを公開する必要はありません。 

 [プライベート リンクの有効化](./hdinsight-private-link.md)の詳細を確認してください。  

### <a name="new-azure-monitor-integration-experience-preview"></a>新しい Azure Monitor 統合エクスペリエンス (プレビュー)
新しい Azure Monitor 統合エクスペリエンスは、このリリースで、米国東部と西ヨーロッパでプレビュー段階になります。 新しい Azure Monitor エクスペリエンスについては、[こちら](./log-analytics-migration.md#migrate-to-the-new-azure-monitor-integration)をご覧ください。

## <a name="deprecation"></a>非推奨
### <a name="basic-support-for-hdinsight-36-starting-july-1-2021"></a>HDInsight 3.6 の Basic サポート (2021 年 7 月 1 日以降)
2021 年 7 月 1 日以降、Microsoft では特定の種類の HDInsight 3.6 クラスターに対する [Basic サポート](hdinsight-component-versioning.md#support-options-for-hdinsight-versions)を提供します。 Basic サポート プランは 2022 年 4 月 3 日まで利用できます。 Basic サポートには 2021 年 7 月 1 日から自動的に登録されます。 オプトインするために必要な操作はありません。 Basic サポートに含まれるクラスターの種類については、[こちらのドキュメント](hdinsight-36-component-versioning.md)を参照してください。 

HDInsight 3.6 で新しいソリューションを構築することはお勧めしません。既存の 3.6 の環境に対する変更を凍結してください。 [クラスターを HDInsight 4.0 に移行](hdinsight-version-release.md#how-to-upgrade-to-hdinsight-40)することをお勧めします。 HDInsight 4.0 の新機能については、[こちら](hdinsight-version-release.md#whats-new-in-hdinsight-40)を参照してください。

## <a name="behavior-changes"></a>動作の変更
### <a name="hdinsight-interactive-query-only-supports-schedule-based-autoscale"></a>HDInsight Interactive Query がスケジュールベースの自動スケーリングのみをサポート
お客様のシナリオがますます成熟し、多様化する中、Interactive Query (LLAP) の負荷ベースの自動スケーリングに制限がいくつかあることがわかりました。 これらの制限は、LLAP クエリ ダイナミクスの性質、将来の負荷予測の精度の問題、LLAP スケジューラのタスク再配布の問題によるものです。 こうした制限により、自動スケーリングが有効になっていると、LLAP クラスター上でのクエリの実行が遅くなる可能性があります。 パフォーマンスへの影響は、自動スケーリングのコスト面での利点を上回る可能性があります。

2021 年 7 月以降、HDInsight 内の Interactive Query ワークロードでは、スケジュールベースの自動スケーリングのみがサポートされます。 新しい Interactive Query クラスター上では負荷ベースの自動スケーリングを有効にできません。 実行中の既存のクラスターについては、前述の既知の制限付きで引き続き実行できます。 

Microsoft では、LLAP のスケジュールベースの自動スケーリングに移行することを推奨しています。  Grafana Hive ダッシュボードを使用すると、クラスターの現在の使用パターンを分析できます。 詳細については、「[Azure HDInsight クラスターを自動的にスケール調整する](hdinsight-autoscale-clusters.md)」を参照してください。 

## <a name="upcoming-changes"></a>今後の変更
今後のリリースでは、次の変更が行われます。

### <a name="built-in-llap-component-in-esp-spark-cluster-will-be-removed"></a>ESP Spark クラスターの組み込み LLAP コンポーネントが削除されます
HDInsight 4.0 ESP Spark クラスターには、両方のヘッド ノードで実行される組み込みの LLAP コンポーネントがあります。 ESP Spark クラスターの LLAP コンポーネントは、もともと HDInsight 3.6 ESP Spark 用に追加されましたが、HDInsight 4.0 ESP Spark の実際のユーザー ケースはありません。 2021 年 9 月に予定されている次のリリースでは、HDInsight で HDInsight 4.0 ESP Spark クラスターから組み込みの LLAP コンポーネントが削除されます。 この変更により、ヘッド ノードのワークロードをオフロードできるようになり、ESP Spark と ESP Interactive Hive クラスターの種類の間の混乱を回避できます。

## <a name="new-region"></a>新しいリージョン
- 米国西部 3
- JIO インド西部
- オーストラリア中部

## <a name="component-version-change"></a>コンポーネントのバージョンの変更
このリリースでは、次のコンポーネント バージョンが変更されました。
- 1\.5.1 から 1.5.9 への ORC バージョン

HDInsight 4.0 と HDInsight 3.6 の現在のコンポーネント バージョンについては、[こちらのドキュメント](./hdinsight-component-versioning.md)を参照してください。

## <a name="back-ported-jiras"></a>移植された JIRA
このリリースで移植された Apache JIRA を次に示します。

| 影響を受ける機能    |   Apache JIRA                                                      |
|---------------------|--------------------------------------------------------------------|
| 日付/タイムスタンプ    | [HIVE-25104](https://issues.apache.org/jira/browse/HIVE-25104)     |
|                     | [HIVE-24074](https://issues.apache.org/jira/browse/HIVE-24074)     |
|                     | [HIVE-22840](https://issues.apache.org/jira/browse/HIVE-22840)     |
|                     | [HIVE-22589](https://issues.apache.org/jira/browse/HIVE-22589)     |
|                     | [HIVE-22405](https://issues.apache.org/jira/browse/HIVE-22405)     |
|                     | [HIVE-21729](https://issues.apache.org/jira/browse/HIVE-21729)     |
|                     | [HIVE-21291](https://issues.apache.org/jira/browse/HIVE-21291)     |
|                     | [HIVE-21290](https://issues.apache.org/jira/browse/HIVE-21290)     |
| UDF                 | [HIVE-25268](https://issues.apache.org/jira/browse/HIVE-25268)     |
|                     | [HIVE-25093](https://issues.apache.org/jira/browse/HIVE-25093)     |
|                     | [HIVE-22099](https://issues.apache.org/jira/browse/HIVE-22099)     |
|                     | [HIVE-24113](https://issues.apache.org/jira/browse/HIVE-24113)     |
|                     | [HIVE-22170](https://issues.apache.org/jira/browse/HIVE-22170)     |
|                     | [HIVE-22331](https://issues.apache.org/jira/browse/HIVE-22331)     |
| ORC                 | [HIVE-21991](https://issues.apache.org/jira/browse/HIVE-21991)     |
|                     | [HIVE-21815](https://issues.apache.org/jira/browse/HIVE-21815)     |
|                     | [HIVE-21862](https://issues.apache.org/jira/browse/HIVE-21862)     |
| テーブル スキーマ        | [HIVE-20437](https://issues.apache.org/jira/browse/HIVE-20437)     |
|                     | [HIVE-22941](https://issues.apache.org/jira/browse/HIVE-22941)     |
|                     | [HIVE-21784](https://issues.apache.org/jira/browse/HIVE-21784)     |
|                     | [HIVE-21714](https://issues.apache.org/jira/browse/HIVE-21714)     |
|                     | [HIVE-18702](https://issues.apache.org/jira/browse/HIVE-18702)     |
|                     | [HIVE-21799](https://issues.apache.org/jira/browse/HIVE-21799)     |
|                     | [HIVE-21296](https://issues.apache.org/jira/browse/HIVE-21296)     |
| ワークロード管理 | [HIVE-24201](https://issues.apache.org/jira/browse/HIVE-24201)     |
| 圧縮          | [HIVE-24882](https://issues.apache.org/jira/browse/HIVE-24882)     |
|                     | [HIVE-23058](https://issues.apache.org/jira/browse/HIVE-23058)     |
|                     | [HIVE-23046](https://issues.apache.org/jira/browse/HIVE-23046)     |
| 具体化されたビュー   | [HIVE-22566](https://issues.apache.org/jira/browse/HIVE-22566)     |
