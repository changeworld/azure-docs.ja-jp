---
title: General Purpose および Business Critical サービス レベル
titleSuffix: Azure SQL Database & SQL Managed Instance
description: この記事では、Azure SQL Database と Azure SQL Managed Instance で使用される仮想コアベースの購入モデルにおける General Purpose と Business Critical のサービス レベルについて説明します。
services: sql-database
ms.service: sql-db-mi
ms.subservice: service-overview
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: dimitri-furman
ms.author: dfurman
ms.reviewer: mathoma
ms.date: 11/15/2021
ms.openlocfilehash: 0192b9ce0e0d32b53ce8b99e9e8d16ce104c0056
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132705343"
---
# <a name="azure-sql-database-and-azure-sql-managed-instance-service-tiers"></a>Azure SQL Database と Azure SQL Managed Instance のサービス レベル
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

 2 つの[仮想コア](service-tiers-vcore.md) サービス レベルは、Azure SQL Database と Azure SQL Managed Instance の両方で使用できます。

- [General Purpose](service-tier-general-purpose.md) は、一般的なパフォーマンスと可用性の要件を持つほとんどのワークロード向けに設計された、予算を抑えたレベルです。
- [Business Critical](service-tier-business-critical.md) レベルは、厳しい可用性要件を持つパフォーマンスに依存するワークロード用に設計されています。

Azure SQL Database では、Hyperscale サービス レベルも提供されます。 

- [Hyperscale](service-tier-hyperscale.md) は、ほとんどのビジネス ワークロード向けに設計されており、拡張性の高いストレージ、読み取りスケールアウト、およびデータベースの高速復元機能を備えています。

仮想コアベースの購入モデルと DTU ベースの購入モデルとの比較については、[購入モデルとリソース](purchasing-models.md)に関する記事をご覧ください。

## <a name="service-tier-comparison"></a>サービス レベルの比較

次の表は、サービス レベル間の主な違いを説明しています。

|-| リソースの種類 | General Purpose | ハイパースケール | Business Critical |
|:---:|:---:|:---:|:---:|:---:|
| **最適な用途** | |  予算重視のバランスの取れたコンピューティングおよびストレージ オプションを提供します。 | ほとんどのビジネス ワークロード。 最大 100 TB までのストレージ サイズの自動スケーリング、垂直および水平方向へのなめらかなコンピューティング スケーリング、データベースの高速復元。 | トランザクション レートが高く IO 待ち時間が低い OLTP アプリケーション。 同期的に更新された複数のレプリカを使用して、最高の耐障害性と高速フェールオーバーを提供します。|
| **リソースの種類で使用可能:** ||SQL Database/SQL Managed Instance | 単一の Azure SQL Database | SQL Database/SQL Managed Instance |
| **コンピューティング サイズ**| SQL Database | 1 - 80 の仮想コア | 1 - 80 の仮想コア | 1 - 128 の仮想コア |
| | SQL Managed Instance | 4、8、16、24、32、40、64、80 の仮想コア | 該当なし | 4、8、16、24、32、40、64、80 の仮想コア |
| | SQL Managed Instance のプール | 2、4、8、16、24、32、40、64、80 の仮想コア | 該当なし | 該当なし |
| **ストレージの種類** | All | リモート ストレージ | 階層化されたリモートおよびローカル SSD ストレージ | ローカル SSD ストレージ |
| **データベースのサイズ** | SQL Database | 1 GB – 4 TB | 40 GB - 100 TB | 1 GB – 4 TB |
| | SQL Managed Instance  | 32 GB – 16 TB| 該当なし | 32 GB – 16 TB |
| **ストレージ サイズ** | SQL Database | 1 GB – 4 TB | 40 GB - 100 TB | 1 GB – 4 TB |
| | SQL Managed Instance  | 32 GB – 16 TB | 該当なし | 32 GB – 16 TB |
| **TempDB のサイズ** | SQL Database | [仮想コアあたり 32 GB](resource-limits-vcore-single-databases.md) | [仮想コアあたり 32 GB](resource-limits-vcore-single-databases.md) | [仮想コアあたり 32 GB](resource-limits-vcore-single-databases.md) |
| | SQL Managed Instance  | [仮想コアあたり 24 GB](../managed-instance/resource-limits.md#service-tier-characteristics) | 該当なし | 最大 4 TB - [ストレージ サイズによる制限](../managed-instance/resource-limits.md#service-tier-characteristics) |
<<<<<<< HEAD
| **ログ書き込みスループット** | SQL Database | [仮想コアあたり 1.875 MB/秒 (最大 30 MB/秒)](resource-limits-vcore-single-databases.md#general-purpose---provisioned-compute---gen4) | 100 MB/秒 | [仮想コアあたり 6 MB/秒 (最大 96 MB/秒)](resource-limits-vcore-single-databases.md#business-critical---provisioned-compute---gen4) |
=======
| **ログ書き込みスループット** | SQL Database | 単一データベース: [仮想コアあたり 4.5 MB/秒 (最大 50 MB/秒)](resource-limits-vcore-single-databases.md) <br> エラスティック プール: [仮想コアあたり 6 MB/秒 (最大 62.5 MB/秒)](resource-limits-vcore-elastic-pools.md)| 100 MB/秒 | 単一データベース: [仮想コアあたり 12 MB/秒 (最大 96 MB/秒)](resource-limits-vcore-single-databases.md) <br> エラスティック プール: [仮想コアあたり 15 MB/秒 (最大 120 MB/秒)](resource-limits-vcore-elastic-pools.md)|
>>>>>>> repo_sync_working_branch
| | SQL Managed Instance | [仮想コアあたり 3 MB/秒 (最大 22 MB/秒)](../managed-instance/resource-limits.md#service-tier-characteristics) | 該当なし | [仮想コアあたり 4 MB/秒 (最大 48 MB/秒)](../managed-instance/resource-limits.md#service-tier-characteristics) |
|**可用性**|SQL Database ([SLA](https://azure.microsoft.com/support/legal/sla/azure-sql-database/))| 99.99% | [セカンダリ レプリカが 1 つで 99.95%、それ以上のレプリカで 99.99%](service-tier-hyperscale-frequently-asked-questions-faq.yml#what-slas-are-provided-for-a-hyperscale-database-) | 99.99% <br/> [ゾーン冗長単一データベースで 99.995%](https://azure.microsoft.com/blog/understanding-and-leveraging-azure-sql-database-sla/) |
| |SQL マネージド インスタンス ([SLA](https://azure.microsoft.com/support/legal/sla/azure-sql-sql-managed-instance/))| 99.99% |  [セカンダリ レプリカが 1 つで 99.95%、それ以上のレプリカで 99.99%](service-tier-hyperscale-frequently-asked-questions-faq.yml#what-slas-are-provided-for-a-hyperscale-database-) | 99.99% <br/> [ゾーン冗長単一データベースで 99.995%](https://azure.microsoft.com/blog/understanding-and-leveraging-azure-sql-database-sla/) |
|**バックアップ**|All|RA-GRS、1 ～ 35 日 (既定では 7 日) | RA-GRS、7 日、高速のポイントインタイム リストア (PITR) | RA-GRS、1 ～ 35 日 (既定では 7 日) |
|**インメモリ OLTP** | | 該当なし | 部分的なサポート。 メモリ最適化テーブル型、テーブル変数、ネイティブ コンパイル モジュールがサポートされています。 | 利用可能 |
|**読み取り専用レプリカ**| | 0 組み込み <br> 0 - 4 [geo レプリケーション](active-geo-replication-overview.md)を使用 | 0 - 4 組み込み | 1 組み込み、価格に含まれます <br> 0 - 4 [geo レプリケーション](active-geo-replication-overview.md)を使用 |
|**価格/課金** | SQL Database | [仮想コア、予約ストレージ、バックアップ ストレージ](https://azure.microsoft.com/pricing/details/sql-database/single/)に対して請求されます。 <br/>IOPS に対しては請求されません。 | [レプリカごとの仮想コアと使用されたストレージ](https://azure.microsoft.com/pricing/details/sql-database/single/)に対して請求されます。 <br/>IOPS に対してはまだ請求されません。 | [仮想コア、予約ストレージ、バックアップ ストレージ](https://azure.microsoft.com/pricing/details/sql-database/single/)に対して請求されます。 <br/>IOPS に対しては請求されません。 |
|| SQL Managed Instance | [仮想コア、予約ストレージ、バックアップ ストレージ](https://azure.microsoft.com/pricing/details/sql-database/managed/)に対して請求されます。 <br/>IOPS に対しては請求されません。| 該当なし | [仮想コア、予約ストレージ、バックアップ ストレージ](https://azure.microsoft.com/pricing/details/sql-database/managed/)に対して請求されます。 <br/>IOPS に対しては請求されません。| 
|**割引モデル**| | [予約インスタンス](reserved-capacity-overview.md)<br/>[Azure ハイブリッド特典](../azure-hybrid-benefit.md) (開発テスト サブスクリプションでは利用不可)<br/>[Enterprise](https://azure.microsoft.com/offers/ms-azr-0148p/) および [Pay-As-You-Go](https://azure.microsoft.com/offers/ms-azr-0023p/) (従量課金制) Dev/Test (開発テスト) サブスクリプション| [Azure ハイブリッド特典](../azure-hybrid-benefit.md) (開発テスト サブスクリプションでは利用不可)<br/>[Enterprise](https://azure.microsoft.com/offers/ms-azr-0148p/) および [Pay-As-You-Go](https://azure.microsoft.com/offers/ms-azr-0023p/) (従量課金制) Dev/Test (開発テスト) サブスクリプション| [予約インスタンス](reserved-capacity-overview.md)<br/>[Azure ハイブリッド特典](../azure-hybrid-benefit.md) (開発テスト サブスクリプションでは利用不可)<br/>[Enterprise](https://azure.microsoft.com/offers/ms-azr-0148p/) および [Pay-As-You-Go](https://azure.microsoft.com/offers/ms-azr-0023p/) (従量課金制) Dev/Test (開発テスト) サブスクリプション|

> [!NOTE]
> サービス レベル アグリーメント (SLA) の詳細については、「[Azure SQL Database の SLA](https://azure.microsoft.com/support/legal/sla/azure-sql-database/)」と「[Azure SQL Managed Instance の SLA](https://azure.microsoft.com/support/legal/sla/azure-sql-sql-managed-instance/)」を参照してください。

### <a name="resource-limits"></a>リソース制限

リソース制限の詳細については、以下を参照してください。

 - [Azure SQL Database (仮想コア)](resource-limits-vcore-single-databases.md)
 - [単一の Azure SQL データベース (DTU)](resource-limits-dtu-single-databases.md)
 - [プールされた Azure SQL データベース (DTU)](resource-limits-dtu-elastic-pools.md)
 - [Azure SQL Managed Instance](../managed-instance/resource-limits.md)

## <a name="data-and-log-storage"></a>データとログのストレージ

次の要因は、データ ファイルとログ ファイルに使用されるストレージの量に影響し、General Purpose レベルと Business Critical レベルに適用されます。 Hyperscale のデータ ストレージとログ ストレージの詳細については、「[ハイパースケール サービス レベル](service-tier-hyperscale.md)」を参照してください。

- 各コンピューティング サイズでは、既定で 32 GB の最大データ サイズがサポートされます。
- 最大データ サイズを構成すると、追加の 30% のストレージがログ ファイル用に自動的に追加されます。
- 1 GB とサポートされているストレージ サイズの最大値 (1 GB 単位) の間で任意の最大データ サイズを選択できます。
- General Purpose サービス レベルでは、`tempdb` によってローカル SSD が使用され、このストレージ コストは、仮想コアの価格に含まれます。
- Business Critical サービス レベルでは、`tempdb` によって、データ ファイルやログ ファイルとローカル SSD が共有され、`tempdb` のストレージ コストは、仮想コアの価格に含まれます。
- SQL Managed Instance での最大ストレージ サイズは、32 GB の倍数で指定する必要があります。

> [!IMPORTANT]
> General Purpose レベルと Business Critical レベルでは、データベース、エラスティック プール、またはマネージド インスタンス用に構成された最大ストレージ サイズに対して課金されます。 ハイパースケール レベルでは、割り当てられたデータ ストレージに対して課金されます。

SQL Database で現在割り当てられ、使用されているデータ ストレージのサイズを監視するには、*allocated_data_storage* と *ストレージ* の Azure Monitor [メトリック](../../azure-monitor/essentials/metrics-supported.md#microsoftsqlserversdatabases)をそれぞれ使用します。 SQL Managed Instance の消費されている合計インスタンス ストレージ サイズを監視するには、*storage_space_used_mb* [メトリック](../../azure-monitor/essentials/metrics-supported.md#microsoftsqlmanagedinstances)を使用します。 T-SQL を使用して、データベース内の個々のデータ ファイルとログ ファイルの現在割り当てられ、使用されているストレージ サイズを監視するには、[sys.database_files](/sql/relational-databases/system-catalog-views/sys-database-files-transact-sql) ビューと [FILEPROPERTY(... , 'SpaceUsed')](/sql/t-sql/functions/fileproperty-transact-sql) 関数を使用します。

> [!TIP]
> 場合によっては、未使用領域を再利用できるようにデータベースを縮小する必要があります。 詳細については、「[Manage file space in Azure SQL Database](file-space-manage.md)」(Azure SQL Database でファイル領域を管理する) を参照してください。

## <a name="backups-and-storage"></a>バックアップとストレージ

データベース バックアップ用のストレージは、SQL Database と SQL Managed Instance の[ポイントインタイム リストア (PITR)](recovery-using-backups.md) および[長期保有 (LTR)](long-term-retention-overview.md) 機能をサポートするために割り当てられます。 このストレージは、データファイルとログファイルのストレージとは別に、別途請求されます。

- **PITR**: General Purpose レベルと Business Critical レベルでは、個々のデータベース バックアップは、[読み取りアクセス geo 冗長 (RA-GRS) ストレージ](../../storage/common/geo-redundant-design.md)に自動的にコピーされます。 ストレージ サイズは、新しいバックアップが作成されるにつれて、動的に増大します。 ストレージは、完全バックアップ、差分バックアップ、およびトランザクション ログ バックアップで使われます。 ストレージの使用量は、データベースの変化率とバックアップに構成された保有期間に応じて異なります。 保有期間は、データベースごとに、SQL Database の場合は 1 日から 35 日、SQL Managed Instance の場合は 0 日から 35 日の間で個別に構成できます。 構成済みの最大データ サイズに等しいバックアップ ストレージ容量が、追加料金なしで提供されます。
- **LTR**: 最大 10 年間の完全バックアップとなる長期保存を構成することもできます。 LTR ポリシーを設定した場合、これらのバックアップは、RA-GRS ストレージに自動的に格納されますが、バックアップがコピーされる頻度は制御できます。 さまざまなコンプライアンス要件を満たすために、毎週、毎月、毎年のバックアップに対して異なるリテンション期間を選択することができます。 選択した構成によって、LTR バックアップに使用されるストレージ容量が決まります。 詳細については、「[Long-term backup retention](long-term-retention-overview.md)」(長期バックアップ リテンション) をご覧ください。

## <a name="next-steps"></a>次のステップ

仮想コア サービス レベルで使用可能な特定のコンピューティング サイズおよびストレージ サイズの詳細については、以下を参照してください。 

- [Azure SQL Database の仮想コアベースのリソース制限](resource-limits-vcore-single-databases.md)
- [プールされた Azure SQL Database データベースの仮想コアベースのリソース制限](resource-limits-vcore-elastic-pools.md)
- [Azure SQL Managed Instance の仮想コアベースのリソース制限](../managed-instance/resource-limits.md)