---
title: Azure SQL Data Warehouse リリース ノート 2018 年 12 月 | Microsoft Docs
description: Azure SQL Data Warehouse のリリース ノート。
services: sql-data-warehouse
author: twounder
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 12/12/2018
ms.author: mausher
ms.reviewer: twounder
ms.openlocfilehash: 21baa89293c74ec49720bffc2506e20789fe9e55
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/14/2018
ms.locfileid: "53411381"
---
# <a name="whats-new-in-azure-sql-data-warehouse-december-2018"></a>Azure SQL Data Warehouse の新機能 2018 年 12 月
Azure SQL Data Warehouse では、継続的に機能強化を図っています。 この記事では、2018 年 12 月に導入された新しい機能と変更点について説明します。

## <a name="virtual-network-service-endpoints-generally-available"></a>仮想ネットワーク サービス エンドポイントの一般提供
このリリースでは、すべての Azure リージョンで、Azure SQL Data Warehouse 用の仮想ネットワーク (VNet) サービス エンドポイントの一般提供が開始されます。 VNet サービス エンドポイントを利用すると、お客様の仮想ネットワーク内にある特定のサブネットまたは一連のサブネットからお客様の論理サーバーへの接続を分離できます。 お客様の VNet から Azure SQL Data Warehouse へのトラフィックは、常に Azure バックボーン ネットワーク内に留まります。 この直接ルートは、仮想アプライアンス経由またはオンプレミス経由のインターネット トラフィックを受け取る特定のルートより優先されます。 サービス エンドポイント経由の仮想ネットワーク アクセスに対して別途課金されることはありません。 [Azure SQL Data Warehouse](https://azure.microsoft.com/pricing/details/sql-data-warehouse/gen2/) の現在の価格モデルがそのまま適用されます。

このリリースでは、[Azure BLOB ファイル システム](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-abfs-driver) (ABFS) ドライバーを使用した、[Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction) (ADLS) への PolyBase 接続も可能になりました。 Azure Data Lake Storage Gen2 は、分析データのライフサイクル全体に必要なすべての特性を Azure Storage にもたらします。 Azure Blob Storage と Azure Data Lake Storage Gen1 という、既にある 2 つの Azure ストレージ サービスの機能が集約されています。 ファイル システム セマンティクス、ファイルレベルのセキュリティ、スケーリングなど、[Azure Data Lake Storage Gen1](https://docs.microsoft.com/azure/data-lake-store/index) に由来する機能が、[Azure Blob Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction) の低コストの階層型ストレージ、高可用性およびディザスター リカバリー機能と組み合わされています。 

PolyBase を使用すると、VNet に対してセキュリティで保護された Azure Storage のデータを Azure SQL Data Warehouse にインポートすることもできます。 同様に、VNet に対してセキュリティで保護された Azure Storage に Azure SQL Data Warehouse からデータをエクスポートすることも、PolyBase によってサポートされています。 

Azure SQL Data Warehouse における VNet サービス エンドポイントの詳細については、[ブログ記事](https://azure.microsoft.com/blog/general-availability-of-vnet-service-endpoints-for-azure-sql-data-warehouse/)または[ドキュメント](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview?toc=/azure/sql-data-warehouse/toc.json)を参照してください。

## <a name="automatic-performance-monitoring-preview"></a>自動パフォーマンス監視 (プレビュー)
Azure SQL Data Warehouse では、[クエリ ストア](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store?view=sql-server-2017)がプレビューで使用できるようになりました。 クエリ ストアは、クエリ パフォーマンスのトラブルシューティングを支援するように設計されています。クエリ、クエリ プラン、ランタイム統計、およびクエリ履歴を追跡することにより、お客様のデータ ウェアハウスのアクティビティとパフォーマンスを監視できるようにします。 クエリ ストアは、内部ストアと動的管理ビュー (DMV) のセットであり、以下のことを行えます。

- 最もリソース消費量の多いクエリを特定し調整する
- アドホックなワークロードを識別して改善する
- 統計、インデックス、またはシステム サイズ (DWU 設定) の変化によって、クエリのパフォーマンスとプランへの影響を評価する
- 実行されたすべてのクエリの完全なクエリ テキストを表示する

クエリ ストアには、3 つの実際のストアが含まれています。  
- 実行プラン情報を保持するためのプラン ストア 
- ランタイム統計情報を保持するためのランタイム統計ストア
- 待機統計情報を保持するための待機統計ストア 

SQL Data Warehouse では、これらのストアが自動的に管理され、過去 7 日間に保存された無制限の数のクエリが追加料金なしで提供されます。 クエリ ストアを有効にするのは、ALTER DATABASE T-SQL ステートメントを実行するのと同じくらい簡単です。

```sql
ALTER DATABASE [Database Name] SET QUERY_STORE = ON;
```
Azure SQL Data Warehouse のクエリ ストアの詳細については、「[クエリのストアを使用した、パフォーマンスの監視](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store?view=sql-server-2017)」という記事や、[sys.query_store_query](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-query-transact-sql?view=sql-server-2017) などのクエリ ストア DMV を参照してください。 リリースを発表した[ブログ記事](https://azure.microsoft.com/blog/automatic-performance-monitoring-in-azure-sql-data-warehouse-with-query-store/)はこちらです。

## <a name="lower-compute-tiers-for-azure-sql-data-warehouse-gen2"></a>Azure SQL Data Warehouse Gen2 の下位コンピューティング レベル
Azure SQL Data Warehouse Gen2 で下位コンピューティング レベルがサポートされるようになりました。 お客様は、Azure SQL Data Warehouse の優れたパフォーマンス、柔軟性、およびセキュリティ機能を 100 cDWU ([Data Warehouse ユニット](https://docs.microsoft.com/azure/sql-data-warehouse/what-is-a-data-warehouse-unit-dwu-cdwu)) から数分で 30,000 cDWU まで拡張できます。 2018 年 12 月半ばから、こちらの[リージョン](https://docs.microsoft.com/azure/sql-data-warehouse/gen2-lower-tier-regions)では、Gen2 のパフォーマンスと柔軟性を下位のコンピューティング レベルでご利用いただけます。その他のリージョンでは、2019 年中に利用できるようになります。

Microsoft は次世代データ ウェアハウスのエントリ ポイントを下げ、セキュリティで保護された高パフォーマンスなデータ ウェアハウスの利点をすべて評価する必要のある価値重視型のお客様に門戸を開いています。お客様が最適な試用環境を推測する必要はありません。 現在の 500 cDWU のエントリ ポイントではなく、100 cDWU から始められます。 SQL Data Warehouse Gen2 では、一時停止操作と再開操作が引き続きサポートされます。また、単なるコンピューティングの柔軟性以外の利点もあります。 Gen2 は、無制限の列ストア ストレージ容量、1 クエリあたり 2.5 倍以上のメモリ、最大 128 件の同時クエリ、および[アダプティブ キャッシング](https://azure.microsoft.com/blog/adaptive-caching-powers-azure-sql-data-warehouse-performance-gains/)機能もサポートしています。 これらの機能は、同じ料金の Gen1 の同じ Data Warehouse ユニットと比較して、平均 5 倍以上のパフォーマンスを実現しています。 Gen2 では地理的冗長バックアップが標準であり、保証付きデータ保護が組み込まれています。 Azure SQL Data Warehouse Gen2 は必要に応じてスケーリングすることができます。

## <a name="next-steps"></a>次の手順
SQL Data Warehouse の概要について学習したので、次は[SQL Data Warehouse を簡単に作成する][create a SQL Data Warehouse]方法について学習してください。 Azure に慣れていない場合に新しい用語を調べるには、[Azure 用語集][Azure glossary]が役立ちます。 または、次の SQL Data Warehouse リソースも確認できます。  

* [顧客の成功事例]
* [ブログ]
* [機能に関する要求]
* [ビデオ]
* [Customer Advisory Team のブログ]
* [Stack Overflow フォーラム]
* [Twitter]


[ブログ]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[Customer Advisory Team のブログ]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[顧客の成功事例]: https://azure.microsoft.com/case-studies/?service=sql-data-warehouse
[機能に関する要求]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Stack Overflow フォーラム]: http://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[ビデオ]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[create a SQL Data Warehouse]: ./create-data-warehouse-portal.md
[Azure glossary]: ../azure-glossary-cloud-terminology.md
