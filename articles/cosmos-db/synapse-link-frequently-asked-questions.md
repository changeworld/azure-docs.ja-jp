---
title: Azure Synapse Link for Azure Cosmos DB についてよく寄せられる質問
description: 課金、分析ストア、セキュリティ、分析ストアでの Time to live などの分野で Synapse Link for Azure Cosmos DB についてよく寄せられる質問に回答します。
author: Rodrigossz
ms.author: rosouz
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/09/2020
ms.openlocfilehash: f6a348ceb30806259035cb71bb4165b736949272
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/23/2020
ms.locfileid: "92480073"
---
# <a name="frequently-asked-questions-about-azure-synapse-link-for-azure-cosmos-db"></a>Azure Synapse Link for Azure Cosmos DB についてよく寄せられる質問

Azure Synapse Link for Azure Cosmos DB により、Azure Cosmos DB と Azure Synapse Analytics の間が緊密に統合されます。 これにより、お客様は、トランザクション ワークロードからパフォーマンスを完全に分離し、ETL パイプラインを使用せずに、運用データに対してほぼリアルタイムの分析を実行できます。 この記事では、Synapse Link for Azure Cosmos DB についてよく寄せられる質問に回答します。

## <a name="general-faq"></a>一般的な FAQ

### <a name="is-azure-synapse-link-supported-for-all-azure-cosmos-db-apis"></a>Azure Synapse Link はすべての Azure Cosmos DB API でサポートされますか?

パブリック プレビュー リリースでは、Azure Synapse Link が、Azure Cosmos DB SQL (Core) API、および MongoDB 用 Azure Cosmos DB API に対してのみサポートされます。 

### <a name="is-azure-synapse-link-supported-for-multi-region-azure-cosmos-db-accounts"></a>Azure Synapse Link は複数リージョンの Azure Cosmos DB アカウントに対してサポートされますか?

はい。複数リージョンの Azure Cosmos アカウントの場合、分析ストアに格納されているデータもグローバルに分散されます。 単一の書き込みリージョンか複数の書き込みリージョンかに関係なく、Azure Synapse Analytics から実行される分析クエリは、最も近いローカル リージョンから提供できます。

分析ストアのサポートを含めて複数リージョンの Azure Cosmos DB アカウントを構成しようとしている場合は、アカウントの作成時に必要なすべてのリージョンを追加することをお勧めします。

### <a name="can-i-choose-to-enable-azure-synapse-link-for-only-certain-region-and-not-all-regions-in-a-multi-region-account-set-up"></a>複数リージョン アカウント構成内のすべてのリージョンではなく、特定のリージョンに対してのみ Azure Synapse Link を有効にすることを選択できますか?

プレビュー リリースでは、複数リージョンのアカウントに対して Azure Synapse Link が有効になっている場合、すべてのリージョンで分析ストアが作成されます。 基になるデータは、トランザクション ストアでのスループットとトランザクションの一貫性を確保するために最適化されます。

### <a name="is-backup-and-restore-supported-for-azure-synapse-link-enabled-accounts"></a>Azure Synapse Link が有効になっているアカウントでは、バックアップと復元はサポートされますか?

分析ストアが有効になっているコンテナーの場合、現時点では分析ストアでのデータの自動バックアップと復元がサポートされていません。 

データベース アカウントで Synapse Link が有効になっている場合、Azure Cosmos DB によって、コンテナーのトランザクション ストア (のみ) のデータをスケジュールされたバックアップ間隔で引き続き自動[バックアップ](./online-backup-and-restore.md)されます。 分析ストアが有効になっているコンテナーを新しいアカウントに復元すると、トランザクション ストアのみが有効な状態 (つまり分析ストアは有効でない状態) でコンテナーが復元されるので注意してください。 

### <a name="can-i-disable-the-azure-synapse-link-feature-for-my-azure-cosmos-db-account"></a>Azure Cosmos DB アカウントに対して Azure Synapse Link 機能を無効にすることはできますか?

現時点では、Synapse Link 機能がアカウント レベルで有効になっている場合は、無効にすることはできません。 アカウント レベルで Synapse Link 機能が有効になっていて、分析ストアが有効になっているコンテナーが存在しない場合は、課金への影響はありません。 

この機能を無効にする必要がある場合は、2 つのオプションがあります。 1 つは、Azure Cosmos DB アカウントを削除してから新規に再作成し、必要に応じてデータを移行するというものです。 もう 1 つのオプションは、サポート チケットを開いて、別のアカウントへのデータ移行に関するヘルプを取得するというものです。

## <a name="azure-cosmos-db-analytical-store"></a>Azure Cosmos DB 分析ストア

### <a name="can-i-enable-analytical-store-on-existing-containers"></a>既存のコンテナーで分析ストアを有効にすることはできますか?

現時点では、分析ストアは (新規アカウントの場合も既存のアカウントの場合も) 新しいコンテナーに対してのみ有効にすることができます。

### <a name="can-i-disable-analytical-store-on-my-azure-cosmos-db-containers-after-enabling-it-during-container-creation"></a>分析ストアを、コンテナーの作成中に有効にした後、Azure Cosmos DB コンテナーで無効にすることはできますか?

現時点では、Azure Cosmos DB コンテナーの作成中に分析ストアを有効にした場合、後で無効にすることはできません。

### <a name="is-analytical-store-supported-for-azure-cosmos-db-containers-with-autoscale-provisioned-throughput"></a>自動スケーリングによってプロビジョニングされたスループットを使用する Azure Cosmos DB コンテナーによって分析ストアはサポートされますか?

はい。自動スケーリングによってプロビジョニングされたスループットを使用するコンテナーで、分析ストアを有効にすることができます。

### <a name="is-there-any-effect-on-azure-cosmos-db-transactional-store-provisioned-rus"></a>Azure Cosmos DB トランザクション ストアにプロビジョニングされた RU に影響はありますか?

Azure Cosmos DB では、トランザクション ワークロードと分析ワークロード間のパフォーマンスの分離が保証されています。 コンテナーで分析ストアを有効にしても、Azure Cosmos DB トランザクション ストアにプロビジョニングされた RU には影響しません。 分析ストアのトランザクション (読み取りおよび書き込み) とストレージのコストは個別に課金されます。 詳細については、[Azure Cosmos DB 分析ストアの価格](analytical-store-introduction.md#analytical-store-pricing)に関するセクションを参照してください。

### <a name="are-delete-and-update-operations-on-the-transactional-store-reflected-in-the-analytical-store"></a>トランザクション ストアに対する削除と更新の操作は分析ストアに反映されますか?

はい。トランザクション ストア内のデータの削除と更新は分析ストアに反映されます。 履歴データを含めるようにコンテナーで Time to live (TTL) を構成して、分析 TTL の条件を満たすすべてのバージョンの項目が分析ストアに保持されるようにすることができます。 詳細については、[分析 TTL の概要](analytical-store-introduction.md#analytical-ttl)に関するセクションを参照してください。

### <a name="can-i-connect-to-analytical-store-from-analytics-engines-other-than-azure-synapse-analytics"></a>Azure Synapse Analytics 以外の分析エンジンから分析ストアに接続できますか?

分析ストアに対するアクセスとクエリを実行できるのは、Azure Synapse Analytics によって提供されるさまざまなランタイムを使用する場合のみです。 分析ストアに対してクエリと分析を実行するには、以下を使用します。

* Scala、Python、SparkSQL、C# が完全にサポートされている Synapse Spark。 Synapse Spark は Data Engineering とデータ サイエンスのシナリオにおける中心的存在です。
* T-SQL 言語を使用する SQL サーバーレスと、使い慣れた BI ツール (Power BI Premium など) に対するサポート

### <a name="can-i-connect-to-analytical-store-from-synapse-sql-provisioned"></a>プロビジョニングされた Synapse SQL から分析ストアに接続できますか?

現時点では、プロビジョニングされた Synapse SQL から分析ストアにはアクセスできません。

### <a name="can-i-write-back-the-query-aggregation-results-from-synapse-back-to-the-analytical-store"></a>クエリの集計結果を Synapse から分析ストアに書き戻すことはできますか?

分析ストアは、Azure Cosmos DB コンテナー内の読み取り専用ストアです。 そのため、集計結果を分析ストアに直接書き戻すことはできませんが、別のコンテナーの Azure Cosmos DB のトランザクション ストアに書き込むことができます。これは、後でサービス レイヤーとして利用できます。

### <a name="is-the-autosync-replication-from-transactional-store-to-the-analytical-store-asynchronous-or-synchronous-and-what-are-the-latencies"></a>トランザクション ストアから分析ストアへの自動同期レプリケーションは非同期、同期のどちらで、待機時間はどの程度ですか?

自動同期の待機時間は通常 2 分以内です。 コンテナーを多数備えた共有スループット データベースの場合は、個々のコンテナーの自動同期の待機時間が長くなり、最大で 5 分かかる可能性があります。 この待機時間がお客様のシナリオにどのように適合するかについて、詳細を把握したいと考えています。 そのため、[Azure Cosmos DB チーム](mailto:cosmosdbsynapselink@microsoft.com)までご連絡ください。

### <a name="are-there-any-scenarios-where-the-items-from-the-transactional-store-are-not-automatically-propagated-to-the-analytical-store"></a>トランザクション ストアの項目が分析ストアに自動的に反映されないシナリオはありますか?

コンテナー内の特定の項目が[分析用に適切に定義されたスキーマ](analytical-store-introduction.md#analytical-schema)に違反している場合、それらは分析ストアに含まれません。 分析用に適切に定義されたスキーマによってシナリオが妨げられる場合は、[Azure Cosmos DB チーム](mailto:cosmosdbsynapselink@microsoft.com)に電子メールを送信して支援を求めてください。

### <a name="can-i-partition-the-data-in-analytical-store-differently-from-transactional-store"></a>トランザクション ストアとは異なる方法で分析ストアのデータをパーティション分割できますか?

分析ストア内のデータは、トランザクション ストアのシャードの行方向のパーティション分割に基づいてパーティション分割されます。 現時点では、分析ストアに別のパーティション分割方式を選択することはできません。

### <a name="can-i-customize-or-override-the-way-transactional-data-is-transformed-into-columnar-format-in-the-analytical-store"></a>分析ストアでトランザクション データを列形式に変換する方法をカスタマイズまたはオーバーライドできますか?

現時点では、データ項目がトランザクション ストアから分析ストアに自動的に反映される場合、それらのデータ項目を変換できません。 この制限によってシナリオが妨げられる場合は、[Azure Cosmos DB チーム](mailto:cosmosdbsynapselink@microsoft.com)に電子メールを送信してください。

### <a name="is-analytical-store-supported-by-terraform"></a>Terraform で分析ストアはサポートされていますか?

現在、Terraform では分析ストア コンテナーはサポートされていません。 詳細については、[Terraform GitHub の問題](https://github.com/hashicorp/terraform/issues)を確認してください。

## <a name="analytical-time-to-live-ttl"></a>分析の Time to live (TTL)

### <a name="is-ttl-for-analytical-data-supported-at-both-container-and-item-level"></a>分析データの TTL はコンテナーと項目の両方のレベルでサポートされますか?

現時点では、分析データの TTL はコンテナー レベルでのみ構成でき、項目レベルでの分析の TTL の設定はサポートされていません。

### <a name="after-setting-the-container-level--analytical-ttl-on-an-azure-cosmos-db-container-can-i-change-to-a-different-value-later"></a>Azure Cosmos DB コンテナーでコンテナー レベルの分析の TTL を設定した後に、別の値に変更できますか?

はい。分析の TTL は任意の有効な値に更新できます。 分析の TTL の詳細については、[分析の TTL](analytical-store-introduction.md#analytical-ttl) に関する記事を参照してください。

### <a name="can-i-update-or-delete-an-item-from-the-analytical-store-after-it-has-been-ttld-out-from-the-transactional-store"></a>項目がトランザクション ストアから TTL で除外された後に、分析ストアからその項目を更新または削除できますか?

トランザクションのすべての更新と削除は分析ストアにコピーされますが、トランザクション ストアから削除された項目は、分析ストアでは更新できません。 詳細については、[分析 TTL](analytical-store-introduction.md#analytical-ttl) に関する記事を参照してください。

## <a name="billing"></a>課金

### <a name="what-is-the-billing-model-of-azure-synapse-link-for-azure-cosmos-db"></a>Azure Synapse Link for Azure Cosmos DB の課金モデルはどのようなものですか?

[Azure Cosmos DB 分析ストア](analytical-store-introduction.md)は、2020 年 8 月30 日までは、分析ストアに対する料金はかからずにパブリック プレビューで利用できます。 Synapse Spark と Synapse SQL は、[Synapse サービスの使用](https://azure.microsoft.com/pricing/details/synapse-analytics/)を通じて課金されます。

## <a name="security"></a>Security

### <a name="what-are-the-ways-to-authenticate-with-the-analytical-store"></a>分析ストアでの認証にはどのような方法がありますか?

分析ストアでの認証は、トランザクション ストアと同じです。 特定のデータベースに対して、主キーまたは読み取り専用キーを使用して認証できます。 Azure Synapse Studio 内のリンクされたサービスを利用すれば、Azure Cosmos DB のキーが Spark ノートブックに貼り付けられないようにすることができます。 このリンクされたサービスへのアクセスは、ワークスペースにアクセスできるすべてのユーザーが利用できます。

## <a name="synapse-run-times"></a>Synapse ランタイム

### <a name="what-are-the-currently-supported-synapse-run-times-to-access-azure-cosmos-db-analytical-store"></a>Azure Cosmos DB 分析ストアにアクセスするために現在サポートされている Synapse ランタイムは何ですか?

|Azure Synapse ランタイム |現在のサポート |
|---------|---------|
|Azure Synapse Spark プール | 読み取り、書き込み (トランザクション ストア経由)、テーブル、一時ビュー |
|Azure Synapse SQL サーバーレス プール    | 読み取り、表示 |
|プロビジョニングされた Azure Synapse SQL   |  使用不可 |

### <a name="do-my-azure-synapse-spark-tables-sync-with-my-azure-synapse-sql-serverless-tables-the-same-way-they-do-with-azure-data-lake"></a>Azure Synapse Spark テーブルは、Azure Data Lake の場合と同じように Azure Synapse SQL サーバーレス テーブルと同期されますか?

現時点では、この機能は使用できません。

### <a name="can-i-do-spark-structured-streaming-from-analytical-store"></a>分析ストアから Spark 構造化ストリーミングを実行できますか?

現在、Azure Cosmos DB に対する Spark 構造化ストリーミングのサポートは、トランザクション ストアの変更フィード機能を使用して実装されていて、分析ストアからはまだサポートされません。

## <a name="azure-synapse-studio"></a>Azure Synapse Studio

### <a name="in-the-azure-synapse-studio-how-do-i-recognize-if-im-connected-to-an-azure-cosmos-db-container-with-the-analytics-store-enabled"></a>Azure Synapse Studio で、分析ストアが有効な Azure Cosmos DB コンテナーに接続されていることを確認するにはどうすればよいですか?

分析ストアが有効な Azure Cosmos DB コンテナーのアイコンは、次のようになっています。

:::image type="content" source="./media/synapse-link-frequently-asked-questions/analytical-store-icon.png" alt-text="分析ストアが有効な Azure Cosmos DB コンテナーのアイコン":::

トランザクション ストア コンテナーは、次のアイコンで表されます。

:::image type="content" source="./media/synapse-link-frequently-asked-questions/transactional-store-icon.png" alt-text="分析ストアが有効な Azure Cosmos DB コンテナーのアイコン":::
 
### <a name="how-do-you-pass-azure-cosmos-db-credentials-from-azure-synapse-studio"></a>Azure Synapse Studio から Azure Cosmos DB 資格情報を渡すにはどうすればよいですか?

現時点では、Azure Cosmos DB 資格情報は、Azure Cosmos DB データベースにアクセスできるユーザーが、リンクされたサービスを作成するときに渡されます。 そのストアへのアクセスは、ワークスペースにアクセスできる他のユーザーが利用できます。

## <a name="next-steps"></a>次のステップ

* [Azure Synapse Link の利点](synapse-link.md#synapse-link-benefits)について学習します。

* [Azure Synapse Link と Azure Cosmos DB の統合](synapse-link.md#synapse-link-integration)について学習します。