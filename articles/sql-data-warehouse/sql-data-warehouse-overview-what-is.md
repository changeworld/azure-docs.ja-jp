---
title: Azure SQL Data Warehouse の概要 | Microsoft Docs
description: ペタバイト単位の量のリレーショナル データと非リレーショナル データを処理可能なエンタープライズクラスの分散型データベース。 これは、数秒で拡大、縮小、および一時停止する機能を持つ業界初のクラウド データ ウェアハウスです。
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: overview
ms.subservice: design
ms.date: 05/30/2019
ms.author: martinle
ms.reviewer: igorstan
mscustom: sqlfreshmay19
ms.openlocfilehash: a9126e9023091dd8c3df71f2aa2558a01227a8be
ms.sourcegitcommit: ef06b169f96297396fc24d97ac4223cabcf9ac33
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/31/2019
ms.locfileid: "66428035"
---
# <a name="what-is-azure-sql-data-warehouse"></a>Azure SQL Data Warehouse の概要

SQL Data Warehouse は、超並列処理 (MPP) を使用してペタバイト単位のデータに複雑なクエリを短時間で実行する、クラウドベースのエンタープライズ データ ウェアハウス (EDW) です。 ビッグ データ ソリューションの主要コンポーネントとして SQL Data Warehouse を使用してください。 簡単な [PolyBase](/sql/relational-databases/polybase/polybase-guide?view=sql-server-2017&viewFallbackFrom=azure-sqldw-latest) T-SQL クエリを使用して SQL Data Warehouse にビッグ データをインポートしてから、MPP の機能を利用してハイ パフォーマンスの分析を実行します。 統合と分析を進めると、企業が洞察時に信頼できる 1 つの真実がデータ ウェアハウスに構築されます。  

## <a name="key-component-of-big-data-solution"></a>ビッグ データ ソリューションの主要コンポーネント

SQL Data Warehouse は、クラウドのエンドツーエンドなビッグ データ ソリューションの主要コンポーネントです。

![データ ウェアハウス ソリューション](media/sql-data-warehouse-overview-what-is/data-warehouse-solution.png) 

クラウド データ ソリューションでは、多様なソースのデータがビッグ データ ストアに取り込まれます。 ビッグ データ ストアに取り込まれた後は、Hadoop、Spark、および機械学習アルゴリズムがデータの準備とトレーニングを行います。 データを複雑な分析にかける準備が整ったら、SQL Data Warehouse で PolyBase を使用してビッグ データ ストアのクエリが実行されます。 PolyBase は、SQL Data Warehouse にデータを読み込むために標準の T-SQL クエリを使用します。
 
SQL Data Warehouse はカラム型ストレージを使用してリレーショナル テーブルにデータを格納します。 この形式にすると、データのストレージ コストが大幅に少なくなり、クエリのパフォーマンスが改善されます。 データを SQL Data Warehouse に格納すると、大規模な分析を実行できます。 従来のデータベース システムと比較すると、分単位かかっていた分析クエリが秒単位に、日単位が時間単位に短縮されます。 

分析結果は世界中のレポート データベースやアプリケーションに移動できます。 また、ビジネスの分析に洞察を加え、適切な情報に基づいた決断を下すことができるようになります。

## <a name="next-steps"></a>次の手順

- [Azure SQL Data Warehouse アーキテクチャ](/azure/sql-data-warehouse/massively-parallel-processing-mpp-architecture)を確認する
- 迅速に [SQL Data Warehouse を作成する][create a SQL Data Warehouse]
- [サンプル データを読み込む][load sample data]
- [ビデオ](/azure/sql-data-warehouse/sql-data-warehouse-videos)を確認する

または、次の SQL Data Warehouse リソースも確認できます。  
* [ブログ]を検索する
* [機能要求]を送信する
* [Customer Advisory Team のブログ]を検索する
* [サポート チケットを作成]
* [MSDN フォーラム]を検索する
* [Stack Overflow フォーラム]を検索する


<!--Image references-->
[1]: ./media/sql-data-warehouse-overview-what-is/dwarchitecture.png

<!--Article references-->
[サポート チケットを作成]: ./sql-data-warehouse-get-started-create-support-ticket.md
[load sample data]: ./sql-data-warehouse-load-sample-databases.md
[create a SQL Data Warehouse]: ./sql-data-warehouse-get-started-provision.md
[Migration documentation]: ./sql-data-warehouse-overview-migrate.md
[SQL Data Warehouse solution partners]: ./sql-data-warehouse-partner-business-intelligence.md
[Integrated tools overview]: ./sql-data-warehouse-overview-integrate.md
[Backup and restore overview]: ./sql-data-warehouse-restore-database-overview.md
[Azure glossary]: ../azure-glossary-cloud-terminology.md

<!--MSDN references-->

<!--Other Web references-->
[ブログ]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[Customer Advisory Team のブログ]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[機能要求]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[MSDN フォーラム]: https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=AzureSQLDataWarehouse
[Stack Overflow フォーラム]: https://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Videos]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[SLA for SQL Data Warehouse]: https://azure.microsoft.com/support/legal/sla/sql-data-warehouse/v1_0/
[Volume Licensing]: https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=37
[Service Level Agreements]: https://azure.microsoft.com/support/legal/sla/
