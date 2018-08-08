---
title: Azure SQL Database サービス レベル - DTU | Microsoft Docs
description: 単一のデータベースとプールされたデータベースに対してパフォーマンス レベルとストレージ サイズを提供するサービス レベルについて説明します。
services: sql-database
author: sachinpMSFT
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: conceptual
ms.date: 08/01/2018
manager: craigg
ms.author: carlrab
ms.openlocfilehash: 5d16763fc8f3331082b98216d25190b945d95b60
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39411822"
---
# <a name="choosing-a-dtu-based-service-tier-performance-level-and-storage-resources"></a>DTU ベースのサービス レベル、パフォーマンス レベル、ストレージ リソースの選択 

サービス レベルは、固定の付属ストレージ容量、固定のバックアップ保有期間、および固定の価格を持つさまざまなパフォーマンス レベルによって区別されます。 すべてのサービス レベルで、ダウンタイムなしでパフォーマンス レベルを柔軟に変更できます。 単一データベースとエラスティック プールは、サービス レベルおよびパフォーマンス レベルに基づいて、時間単位で課金されます。

> [!IMPORTANT]
> SQL Database Managed Instance は、現在、パブリック プレビュー段階であり、DTU ベースの購入モデルをサポートしていません。 詳細については、[Azure SQL Database Managed Instance に関するページ](sql-database-managed-instance.md)を参照してください。 

## <a name="choosing-a-dtu-based-service-tier"></a>DTU ベースのサービス レベルの選択

サービス レベルの選択は、主に、ビジネス継続性、ストレージ、およびパフォーマンスの要件に依存します。
||Basic|標準|Premium|
| :-- | --: |--:| --:| --:| 
|対象のワークロード|開発、運用|開発、運用|開発、運用||
|アップタイム SLA|99.99%|99.99%|99.99%|プレビューは対象外|
|バックアップ保有期間|7 日|35 日|35 日|
|CPU|低|低、中、高|中、高|
|IO スループット (概算) |DTU あたり 2.5 IOPS| DTU あたり 2.5 IOPS | DTU あたり 48 IOPS|
|IO 待機時間 (概算)|5 ミリ秒 (読み取り)、10 ミリ秒 (書き込み)|5 ミリ秒 (読み取り)、10 ミリ秒 (書き込み)|2 ミリ秒 (読み取り/書き込み)|
|列ストア インデックス作成 |該当なし|S3 以上|サポートされています|
|インメモリ OLTP|該当なし|該当なし|サポートされています|
|||||

## <a name="single-database-dtu-and-storage-limits"></a>Single Database の DTU と容量の上限

パフォーマンス レベルは、単一データベースの場合はデータベース トランザクション ユニット (DTU) で、エラスティック プールの場合はエラスティック データベース トランザクション ユニット (eDTU) で表されます。 DTU と eDTU の詳細については、[DTU と eDTU の概要](sql-database-service-tiers.md#what-are-database-transaction-units-dtus)に関する記事をご覧ください。

||Basic|標準|Premium|
| :-- | --: | --: | --: | --: |
| 最大ストレージ サイズ | 2 GB | 1 TB (テラバイト) | 4 TB  | 
| 最大 DTU | 5 | 3000 | 4000 | |
||||||

> [!IMPORTANT]
> 場合によっては、未使用領域を再利用できるようにデータベースを縮小する必要があります。 詳細については、「[Manage file space in Azure SQL Database](sql-database-file-space-management.md)」(Azure SQL Database でファイル領域を管理する) を参照してください。

## <a name="elastic-pool-edtu-storage-and-pooled-database-limits"></a>エラスティック プールの eDTU、ストレージ、プールされているデータベースの上限

| | **Basic** | **Standard** | **Premium** | 
| :-- | --: | --: | --: | --: |
| データベースあたりの最大ストレージ サイズ  | 2 GB | 1 TB (テラバイト) | 1 TB (テラバイト) | 
| プールあたりの最大ストレージ サイズ | 156 GB | 4 TB | 4 TB | 
| データベースあたりの最大 eDTU 数 | 5 | 3000 | 4000 | 
| プールあたりの最大 eDTU 数 | 1600 | 3000 | 4000 | 
| プールあたりのデータベースの最大数 | 500  | 500 | 100 | 
||||||

> [!IMPORTANT]
> 現在、米国中西部、中国東部、US DoD 中部、ドイツ中部、US DoD 東部、US Gov 南西部、USGov アイオワ、ドイツ北東部、中国北部を除くすべてのリージョンでは、Premium レベルで 1 TB を超えるストレージを使用できます。 それ以外のリージョンでは、Premium レベルのストレージの最大容量は 1 TB です。 [P11 ～ P15 の現時点での制限](sql-database-dtu-resource-limits-single-databases.md#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb)に関するページを参照してください。  

> [!IMPORTANT]
> 場合によっては、未使用領域を再利用できるようにデータベースを縮小する必要があります。 詳細については、「[Manage file space in Azure SQL Database](sql-database-file-space-management.md)」(Azure SQL Database でファイル領域を管理する) を参照してください。

## <a name="next-steps"></a>次の手順

- 単一データベースに対して選択できる具体的なパフォーマンス レベルとストレージ サイズの詳細については、[単一データベースに対する SQL Database の DTU ベースのリソース制限](sql-database-dtu-resource-limits-single-databases.md#single-database-storage-sizes-and-performance-levels)に関するページをご覧ください。
- エラスティック プールに対して選択できる具体的なパフォーマンス レベルとストレージ サイズの詳細については、[SQL Database の DTU ベースのリソース制限](sql-database-dtu-resource-limits-elastic-pools.md#elastic-pool-storage-sizes-and-performance-levels)に関するページをご覧ください。
