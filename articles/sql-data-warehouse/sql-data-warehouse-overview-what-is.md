---
title: "Azure SQL Data Warehouse の概要 | Microsoft Docs"
description: "エンタープライズ クラスの分散データベースであり、ペタバイト単位の量までリレーショナル データと非リレーショナル データを処理できます。 業界初のクラウド データ ウェアハウスであり、数秒で拡大、縮小、および一時停止できます。"
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: bjhubbard
editor: 
ms.assetid: 4006c201-ec71-4982-b8ba-24bba879d7bb
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: overview
ms.date: 10/23/2017
ms.author: jrj;barbkess
ms.openlocfilehash: d5ad5b566bd8d40ab6d7a9151af54890fd47cc88
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/25/2017
---
# <a name="what-is-azure-sql-data-warehouse"></a>Azure SQL Data Warehouse の概要

SQL Data Warehouse は、超並列処理 (MPP) を利用して、ペタバイト単位のデータに対して複雑なクエリを短時間で実行するクラウドベースのエンタープライズ データ ウェアハウス (EDW) です。 ビッグ データ ソリューションの主要コンポーネントとして SQL Data Warehouse を使用してください。 簡単な PolyBase T-SQL クエリで SQL Data Warehouse にビッグ データをインポートしてから、MPP の機能を利用して高パフォーマンスの分析を実行します。 統合と分析を進めると、企業が洞察時に信頼できる 1 つの真実がデータ ウェアハウスに構築されます。  


## <a name="key-component-of-big-data-solution"></a>ビッグ データ ソリューションの主要コンポーネント
SQL Data Warehouse は、クラウドのエンドツーエンドなビッグ データ ソリューションの主要コンポーネントです。

![データ ウェアハウス ソリューション](media/sql-data-warehouse-overview-what-is/data-warehouse-solution.png) 

クラウド データ ソリューションでは、多様なソースのデータがビッグ データ ストアに取り込まれます。 ビッグ データ ストアに取り込まれた後は、Hadoop、Spark、および機械学習アルゴリズムがデータの準備とトレーニングを行います。 データを複雑な分析にかける準備が整ったら、SQL Data Warehouse で PolyBase を使用してビッグ データ ストアのクエリが実行されます。 PolyBase は、SQL Data Warehouse にデータを読み込むために標準の T-SQL クエリを使用します。
 
SQL Data Warehouse はカラム型ストレージを使用してリレーショナル テーブルにデータを格納します。 この形式にすると、データのストレージ コストが大幅に少なくなり、クエリのパフォーマンスが改善されます。 データを SQL Data Warehouse に格納すると、大規模な分析を実行できます。 従来のデータベース システムと比較すると、分単位かかっていた分析クエリが秒単位に、日単位が時間単位に短縮されます。 

分析結果は世界中のレポート データベースやアプリケーションに移動できます。 また、ビジネスの分析に洞察を加え、適切な情報に基づいた決断を下すことができるようになります。

## <a name="optimization-choices"></a>最適化の選択肢

SQL Data Warehouse は、データのニーズ、規模の大小に合わせて柔軟に設計された[パフォーマンス レベル](performance-tiers.md)を提供しています。 そのため、弾力性やコンピューティングに合わせて最適化された、データ ウェアハウスを選択できます。 

- **弾力性パフォーマンス レベル用の最適化**では、アーキテクチャのコンピューティング層とストレージ層を分けます。 この選択肢は、短期間のピーク活動をサポートするために頻繁にスケーリングすることで、コンピューティングとストレージの分離をフルに活用できるワークロードを得意としています。 このコンピューティング層は、最低レベルの価格であり、スケーリングでお客様のワークロードの大半をサポートできます。

- **コンピューティング パフォーマンス レベル用の最適化**では、最新の Azure ハードウェアを使用して、新しい NVMe ソリッド ステート ディスク キャッシュを導入し、最も頻繁にアクセスされるデータ (必要なデータ) に CPU を継続的に割り当てます。 このパフォーマンス レベルはストレージの階層処理を自動的に実行し、すべての IO がコンピューティング層のローカルで行われるので、複雑なクエリを得意としています。 さらに、SQL Data Warehouse にサイズ制限なしでデータを格納できるように列ストアは強化されています。 コンピューティング パフォーマンス レベル用の最適化では、最高レベルのスケーラビリティが提供されるので、最大 30,000 コンピューティング Data Warehouse ユニット (cDWU) までスケール アップできます。 継続的で超高速なパフォーマンスが必要なワークロードにこの層を選択してください。

## <a name="next-steps"></a>次のステップ
SQL Data Warehouse の概要については学習したので、次はすばやく [SQL Data Warehouse を作成][create a SQL Data Warehouse]し、[サンプル データを読み込む][load sample data]方法について学習してください。 Azure に慣れていない場合に新しい用語を調べるには、[Azure 用語集][Azure glossary]が役立ちます。 または、次の SQL Data Warehouse リソースも確認できます。  

* [顧客の成功事例]
* [ブログ]
* [機能に関する要求]
* [ビデオ]
* [Customer Advisory Team のブログ]
* [サポート チケットを作成する]
* [MSDN フォーラム]
* [Stack Overflow フォーラム]
* [Twitter]

<!--Image references-->
[1]: ./media/sql-data-warehouse-overview-what-is/dwarchitecture.png

<!--Article references-->
[サポート チケットを作成する]: ./sql-data-warehouse-get-started-create-support-ticket.md
[load sample data]: ./sql-data-warehouse-load-sample-databases.md
[create a SQL Data Warehouse]: ./sql-data-warehouse-get-started-provision.md
[Migration documentation]: ./sql-data-warehouse-overview-migrate.md
[SQL Data Warehouse solution partners]: ./sql-data-warehouse-partner-business-intelligence.md
[Integrated tools overview]: ./sql-data-warehouse-overview-integrate.md
[Backup and restore overview]: ./sql-data-warehouse-restore-database-overview.md
[Azure glossary]: ../azure-glossary-cloud-terminology.md

<!--MSDN references-->

<!--Other Web references-->
[顧客の成功事例]: https://azure.microsoft.com/case-studies/?service=sql-data-warehouse
[ブログ]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[Customer Advisory Team のブログ]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[機能に関する要求]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[MSDN フォーラム]: https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=AzureSQLDataWarehouse
[Stack Overflow フォーラム]: http://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[ビデオ]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[SLA for SQL Data Warehouse]: https://azure.microsoft.com/en-us/support/legal/sla/sql-data-warehouse/v1_0/
[Volume Licensing]: http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=37
[Service Level Agreements]: https://azure.microsoft.com/en-us/support/legal/sla/
