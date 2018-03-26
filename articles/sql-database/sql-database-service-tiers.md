---
title: Azure SQL Database サービス | Microsoft Docs
description: 単一のデータベースとプールされたデータベースに対してパフォーマンス レベルとストレージ サイズを提供するサービス レベルについて説明します。
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: article
ms.date: 03/15/2018
ms.author: carlrab
ms.openlocfilehash: 6153616de763eee1b20fff40d38816eca8b455de
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/17/2018
---
# <a name="what-are-azure-sql-database-service-tiers"></a>Azure SQL Database サービス レベルとは

[Azure SQL Database](sql-database-technical-overview.md) では、[単一データベース](sql-database-single-database-resources.md)と[エラスティック プール](sql-database-elastic-pool.md)の両方に対し、**Basic**、**Standard**、**Premium** の各サービス レベルが提供されます。 SQL Database は、[Azure SQL Database マネージ インスタンス](sql-database-managed-instance.md#managed-instance-service-tier)に対して、汎用サービス階層を提供します。 サービス レベルは、主に、パフォーマンス レベルとストレージ サイズの選択の範囲および価格によって区別されます。  すべてのサービス レベルでは、パフォーマンス レベルとストレージのサイズを柔軟に変更できます。  単一データベースとエラスティック プールは、サービス レベル、パフォーマンス レベル、およびストレージのサイズに基づいて、時間単位で課金されます。   

> [!IMPORTANT]
> SQL Database マネージ インスタンスは、現在、パブリック プレビューの最中で、単一の汎用サービス階層を提供しています。 詳細については、[Azure SQL Database マネージ インスタンスに関するページ](sql-database-managed-instance.md)を参照してください。 この記事の残りの部分は、マネージ インスタンスには適用されません。

## <a name="choosing-a-service-tier"></a>サービス階層の選択

サービス レベルの選択は、主に、ビジネス継続性、ストレージ、およびパフォーマンスの要件に依存します。
| | **Basic** | **Standard** |**Premium**  |
| :-- | --: |--:| --:| --:| 
|対象のワークロード|開発、運用|開発、運用|開発、運用||
|アップタイム SLA|99.99%|99.99%|99.99%|プレビューは対象外|
|バックアップ保有期間|7 日|35 日|35 日|
|CPU|低|低、中、高|中、高|
|IO スループット (概算) |DTU あたり 2.5 IOPS  | DTU あたり 2.5 IOPS | DTU あたり 48 IOPS|
|IO 待機時間 (概算)|5 ミリ秒 (読み取り)、10 ミリ秒 (書き込み)|5 ミリ秒 (読み取り)、10 ミリ秒 (書き込み)|2 ミリ秒 (読み取り/書き込み)|
|列ストア インデックス作成とインメモリ OLTP|該当なし|該当なし|サポートされています|
|||||

## <a name="performance-level-and-storage-size-limits"></a>パフォーマンス レベルとストレージ サイズの制限

パフォーマンス レベルは、単一データベースの場合はデータベース トランザクション ユニット (DTU) で、エラスティック プールの場合はエラスティック データベース トランザクション ユニット (eDTU) で表されます。 DTU と eDTU の詳細については、[DTU と eDTU の概要](sql-database-what-is-a-dtu.md)に関する記事を参照してください。

### <a name="single-databases"></a>Single Database

|  | **Basic** | **Standard** | **Premium** | 
| :-- | --: | --: | --: | --: |
| 最大ストレージ サイズ* | 2 GB | 1 TB (テラバイト) | 4 TB  | 
| 最大 DTU | 5 | 3000 | 4000 | |
||||||

### <a name="elastic-pools"></a>エラスティック プール

| | **Basic** | **Standard** | **Premium** | 
| :-- | --: | --: | --: | --: |
| データベースあたりの最大ストレージ サイズ*  | 2 GB | 1 TB (テラバイト) | 1 TB (テラバイト) | 
| プールあたりの最大ストレージ サイズ* | 156 GB | 4 TB | 4 TB | 
| データベースあたりの最大 eDTU 数 | 5 | 3000 | 4000 | 
| プールあたりの最大 eDTU 数 | 1600 | 3000 | 4000 | 
| プールあたりのデータベースの最大数 | 500  | 500 | 100 | 
||||||

> [!IMPORTANT]
> \* 付属のストレージ容量を超えるストレージ サイズはプレビュー段階であり、追加料金が適用されます。 詳細については、「 [SQL Database の価格](https://azure.microsoft.com/pricing/details/sql-database/)」をご覧ください。 
>
> \* Premium レベルでは現在、次のリージョンで 1 TB を超えるストレージが使用できます: オーストラリア東部、オーストラリア南東部、ブラジル南部、カナダ中部、カナダ東部、米国中部、フランス中部、ドイツ中部、東日本、西日本、韓国中部、米国中北部、北ヨーロッパ、米国中南部、東南アジア、英国南部、英国西部、米国東部 2、米国西部、米国政府バージニア、および西ヨーロッパ。 [P11 ～ P15 の現時点での制限](sql-database-resource-limits.md#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb)に関するページを参照してください。  
> 

選択できる具体的なパフォーマンス レベルとストレージ サイズについて詳しくは、「[Azure SQL Database のリソース制限](sql-database-resource-limits.md)」をご覧ください。


## <a name="next-steps"></a>次の手順

- [単一データベースのリソース](sql-database-single-database-resources.md)について学習してください。
- [エラスティック プール](sql-database-elastic-pool.md)について学習してください。
- [Azure サブスクリプションとサービスの制限、クォータ、制約](../azure-subscription-service-limits.md)について学習してください。
* [DTU と eDTU](sql-database-what-is-a-dtu.md) の詳細について学習してください。
* DTU の使用状況の監視については、「[監視とパフォーマンスのチューニング](sql-database-troubleshoot-performance.md)」をご覧ください。

