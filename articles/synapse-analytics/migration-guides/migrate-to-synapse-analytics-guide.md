---
title: 'Azure Synapse Analytics: 移行ガイド'
description: このガイドに従って、データベースを Azure Synapse Analytics 専用 SQL プールに移行します。
ms.service: synapse-analytics
ms.subservice: sql
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: julieMSFT
ms.author: jrasnick
ms.reviewer: jrasnick
ms.date: 03/10/2021
ms.openlocfilehash: 704c30516e9daf047bf5837aa6e2ed08306193db
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2021
ms.locfileid: "107565646"
---
# <a name="migrate-a-data-warehouse-to-a-dedicated-sql-pool-in-azure-synapse-analytics"></a>Azure Synapse Analytics 専用 SQL プールへデータ ウェアハウスを移行する

次のセクションでは、既存のデータ ウェアハウス ソリューションを Azure Synapse Analytics 専用 SQL プールに移行する際に必要なことの概要を説明します。

## <a name="overview"></a>概要

移行を開始する前に、Azure Synapse Analytics がご自分のワークロードに最適なソリューションであることを確認する必要があります。 Azure Synapse Analytics は、大規模なデータの分析を目的として設計された分散システムです。 Azure Synapse Analytics に移行するには、理解するのは難しくなくても実装するのに多少時間がかかる設計変更が必要です。 ビジネスでエンタープライズ クラスのデータ ウェアハウスが必要な場合は、労力に見合うだけの利点があります。 一方、Azure Synapse Analytics ほどの能力を必要としない場合は、[SQL Server](https://docs.microsoft.com/sql/sql-server/) または [Azure SQL Database](https://docs.microsoft.com/azure/azure-sql/) を使う方が高い費用対効果を得られます。

Azure Synapse Analytics の使用を検討するのは、次のようなときです。

- 1 テラバイト以上のデータがある。
- かなりの量のデータで分析することを計画している。
- コンピューティングおよびストレージをスケーリングできる必要がある。
- 必要のないときはコンピューティング リソースを一時停止してコストを節約したい。

次のような運用 (OLTP) ワークロードの場合は、Azure Synapse Analytics でなく、他の選択肢を検討してください。

- 読み取りと書き込みの頻度が高い。
- 単一選択の数が多い。
- 単一行挿入の量が多い。
- 行単位の処理が必要である。
- 互換性のない形式 (JSON、XML など)。

## <a name="azure-synapse-pathway"></a>Azure Synapse Pathway

顧客にとって障壁となる大きな要因の 1 つが、あるシステムから別のものに移行するときにデータベース オブジェクトを変換することです。 [Azure Synapse Pathway](https://docs.microsoft.com/sql/tools/synapse-pathway/azure-synapse-pathway-overview) を使用すると、既存のデータ ウェアハウスのオブジェクト変換を自動化して、最新のデータ ウェアハウス プラットフォームにアップグレードできるようになります。 これは無料の直感的で使いやすいツールであり、Azure Synapse Analytics へのより迅速な移行を可能にするためにコード翻訳を自動化します。

## <a name="prerequisites"></a>前提条件

# <a name="migrate-from-sql-server"></a>[SQL Server からの移行](#tab/migratefromSQLServer)

SQL Server データ ウェアハウスを Azure Synapse Analytics に移行するには、次の前提条件を満たしていることを確認します。

- データ ウェアハウスまたは分析ワークロードがある。
- SQL Server オブジェクトを Azure Synapse オブジェクトに移行するための最新バージョンの [Azure Synapse Pathway](https://www.microsoft.com/en-us/download/details.aspx?id=102787) をダウンロードする。
- Azure Synapse ワークスペースの[専用 SQL プール](../get-started-create-workspace.md)がある。

# <a name="migrate-from-netezza"></a>[Netezza からの移行](#tab/migratefromNetezza)

Netezza データ ウェアハウスを Azure Synapse Analytics に移行するには、次の前提条件を満たしていることを確認します。

- SQL Server オブジェクトを Azure Synapse オブジェクトに移行するための最新バージョンの [Azure Synapse Pathway](https://www.microsoft.com/en-us/download/details.aspx?id=102787) をダウンロードする。
- Azure Synapse ワークスペースの[専用 SQL プール](../get-started-create-workspace.md)がある。

詳細については、「[Netezza の場合の Azure Synapse Analytics ソリューションと移行](https://docs.microsoft.com/azure/cloud-adoption-framework/migrate/azure-best-practices/analytics/analytics-solutions-netezza)」をご覧ください。

# <a name="migrate-from-snowflake"></a>[Snowflake からの移行](#tab/migratefromSnowflake)

Snowflake データ ウェアハウスを Azure Synapse Analytics に移行するには、次の前提条件を満たしていることを確認します。

- Snowflake オブジェクトを Azure Synapse オブジェクトに移行するための最新バージョンの [Azure Synapse Pathway](https://www.microsoft.com/en-us/download/details.aspx?id=102787) をダウンロードする。
- Azure Synapse ワークスペースの[専用 SQL プール](../get-started-create-workspace.md)がある。

# <a name="migrate-from-oracle"></a>[Oracle からの移行](#tab/migratefromOracle)

Oracle データ ウェアハウスを Azure Synapse Analytics に移行するには、次の前提条件を満たしていることを確認します。

- データ ウェアハウスまたは分析ワークロードがある。
- Oracle オブジェクトを SQL Server に変換するための SQL Server Migration Assistant for Oracle をダウンロードする。 詳細については、「[SQL Server への Oracle のデータの移行 (OracleToSQL)](https://docs.microsoft.com/sql/ssma/oracle/migrating-oracle-databases-to-sql-server-oracletosql)」をご覧ください。
- SQL Server オブジェクトを Azure Synapse オブジェクトに移行するための最新バージョンの [Azure Synapse Pathway](https://www.microsoft.com/download/details.aspx?id=102787) をダウンロードする。
- Azure Synapse ワークスペースの[専用 SQL プール](../get-started-create-workspace.md)がある。

詳細については、「[Oracle データ ウェアハウスの場合の Azure Synapse Analytics ソリューションと移行](https://docs.microsoft.com/azure/cloud-adoption-framework/migrate/azure-best-practices/analytics/analytics-solutions-exadata)」をご覧ください。

---

## <a name="pre-migration"></a>移行前

既存のソリューションを Azure Synapse Analytics に移行することを決定したら、作業を開始する前に移行を計画することが必要です。 計画の主な目的は、データ、テーブル スキーマ、およびコードで Azure Synapse Analytics との互換性を確保することです。 現在のシステムと Azure Synapse Analytics には、対処する必要がある互換性の相違がいくつかあります。 さらに、大量のデータを Azure に移行するには時間がかかります。 綿密な計画を立てることで、データを Azure に移行するプロセスを高速化できます。

計画のもう 1 つの重要な目的は、Azure Synapse Analytics で提供するよう意図されている高いクエリ パフォーマンスをソリューションで最大限に活用できるように設計を調整することです。 拡張性を考えてデータ ウェアハウスを設計すると独自の設計パターンが導入されるため、従来の手法が常に最適であるとは限りません。 一部の設計の調整を移行の後で行うことはできますが、プロセスの早い段階で変更を行えば、後で時間を節約できます。

## <a name="migrate"></a>Migrate

移行を成功させるには、テーブル スキーマ、コード、データを移行する必要があります。 これらのトピックに関する詳細なガイダンスについては、以下の記事をご覧ください。

- [テーブルの設計を検討する](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-tables-overview)
- [コードの変更を検討する](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-develop#development-recommendations-and-coding-techniques)
- [データの移行](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/design-elt-data-loading)
- [ワークロード管理を検討する](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-workload-management)

## <a name="more-resources"></a>その他のリソース

Customer Advisory Team の Azure Synapse Analytics (旧称 Azure SQL Data Warehouse) に関する優れたガイダンスが、ブログ投稿として公開されています。 移行の詳細については、「[Migrating data to Azure SQL Data Warehouse in practice (Azure SQL Data Warehouse へのデータの実践移行)](https://docs.microsoft.com/archive/blogs/sqlcat/migrating-data-to-azure-sql-data-warehouse-in-practice)」をご覧ください。

## <a name="migration-assets-from-real-world-engagements"></a>実際の取り組みから得られる移行資産

この移行シナリオの詳細については、次のリソースをご覧ください。 これらは、実際の移行プロジェクトの取り組みをサポートするために開発されました。

| タイトルとリンク                              | 説明                                                                                                                       |
| --------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------- |
| [データ ワークロード評価モデルとツール](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool) | このツールを使用すると、特定のワークロードに対して、推奨される "最適な" ターゲット プラットフォーム、クラウドの準備状況、アプリケーションまたはデータベースの修復レベルがわかります。 シンプルなワンクリックの計算とレポート生成機能があり、自動化された均一なターゲット プラットフォームの決定プロセスが用意されているので、大規模な不動産評価を加速させることができます。 |
| [Azure Synapse Analytics へのデータの読み込み中に発生するデータ エンコードに関する問題の処理](https://azure.microsoft.com/en-us/blog/handling-data-encoding-issues-while-loading-data-to-sql-data-warehouse/) | このブログ投稿は、PolyBase を使用してデータを SQL Data Warehouse に読み込むときに発生する可能性がある、いくつかのデータ エンコードに関する問題についての分析情報を提供しています。 この記事では、こうした問題を解決してデータを正常に読み込むために使用できるいくつかの選択肢についても説明しています。 |
| [Azure Synapse Analytics 専用 SQL プールでのテーブル サイズの取得](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Getting%20table%20sizes%20in%20SQL%20DW.pdf) | アーキテクトが実行する必要のある主なタスクの 1 つは、移行後の新しい環境に関するメトリックを取得することです。 たとえば、オンプレミスからクラウドへの読み込み時間の収集、PolyBase の読み込み時間の収集などです。 最も重要なタスクの 1 つは、顧客の現在のプラットフォームと比較した SQL Data Warehouse のストレージ サイズを決定することです。 |
| [オンプレミスの SQL Server ログインを Azure Synapse Analytics に移動するユーティリティ](https://github.com/Microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/MoveLogins) | PowerShell スクリプトは、ログインを再作成し、SQL Server のオンプレミス インスタンスから Azure SQL サービスとしてのプラットフォーム (PaaS) サービスに移行するデータベース ユーザーを選択するための T-SQL コマンド スクリプトを作成します。 このツールを使用すると、Windows Server Active Directory アカウントを Azure Active Directory アカウントに自動でマッピングでき、ログインごとにオンプレミス Windows Server Active Directory に対して UPN 参照を実行することもできます。 このツールは、必要に応じて SQL Server ネイティブ ログインも移動します。 カスタムのサーバーおよびデータベース ロールの他に、ロール メンバーシップ、データベース ロール、およびユーザー アクセス許可がスクリプト化されます。 包含データベースはサポートされていません。使用可能な SQL Server アクセス許可のサブセットのみがスクリプト化されます。 詳細についてはサポート ドキュメントをご覧ください。スクリプトには、分かりやすいようにコメントが付いています。 |

データ SQL エンジニアリング チームが、これらのリソースを開発しました。 このチームの主要な作業は、Microsoft の Azure データ プラットフォームへのデータ プラットフォーム移行プロジェクトの複雑な近代化を容易にし、迅速に進めることです。

## <a name="videos"></a>ビデオ

[Walgreens が、約 100 TB のデータがある小売り在庫システムを記録的な速さで Netezza から Azure Synapse Analytics に移行した](https://www.youtube.com/watch?v=86dhd8N1lH4)方法をご覧ください。
