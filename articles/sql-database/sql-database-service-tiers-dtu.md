---
title: Azure SQL Database サービス - DTU | Microsoft Docs
description: 単一のデータベースとプールされたデータベースに対してパフォーマンス レベルとストレージ サイズを提供するサービス レベルについて説明します。
services: sql-database
author: CarlRabeler
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: article
ms.date: 04/09/2018
manager: craigg
ms.author: carlrab
ms.openlocfilehash: 6e282291a6e6e219bb275dd4da91f3815590adc1
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2018
ms.locfileid: "32196005"
---
# <a name="dtu-based-purchasing-model-for-azure-sql-database"></a>Azure SQL Database の DTU ベースの購入モデル 


[Azure SQL Database](sql-database-technical-overview.md) には、コンピューティング、ストレージ、および IO リソースに対して 2 つの購入モデルが用意されています。1 つは DTU ベースの購入モデルで、もう 1 つは仮想コアベースの購入モデル (プレビュー) です。 次の表とグラフは、この 2 つの購入モデルを比較しています。

> [!IMPORTANT]
> 仮想コア ベースの購入モデル (プレビュー) については、[仮想コア ベースの購入モデル](sql-database-service-tiers-vcore.md)に関する記事を参照してください。


|**購入モデル**|**説明**|**最適な用途**|
|---|---|---|
|DTU ベースのモデル|このモデルは、コンピューティング、ストレージ、および IO リソースのバンドルされた測定値に基づいています。 パフォーマンス レベルは、単一データベースの場合はデータベース トランザクション ユニット (DTU) で、エラスティック プールの場合はエラスティック データベース トランザクション ユニット (eDTU) で表されます。 DTU と eDTU の詳細については、[DTU と eDTU の概要](sql-database-what-is-a-dtu.md)に関する記事をご覧ください。|シンプルな構成済みリソースのオプションを希望するお客様に最適です。| 
|仮想コアベースのモデル|このモデルでは、コンピューティングおよびストレージ リソースを個別にスケーリングできます。 また、SQL Server 向けの Azure ハイブリッド特典を使用して、コストを削減することもできます。|柔軟性、制御、および透明性を重視するお客様に最適です。|
||||  

![価格モデル](./media/sql-database-service-tiers/pricing-model.png)

## <a name="dtu-based-purchasing-model"></a>DTU ベースの購入モデル

データベース スループット ユニット (DTU) は、CPU、メモリ、読み取り、書き込みの測定値を組み合わせて算出されます。 DTU ベースの購入モデルは、事前構成済みコンピューティング リソースと付属ストレージのバンドル セットを提供することで、さまざまなレベルのアプリケーション パフォーマンスを実現します。 事前構成済みバンドルと毎月支払う料金が決まっているというシンプルさを好むお客様については、この DTU ベースのモデルがニーズに適している可能性があります。 DTU ベースの購入モデルでは、[単一データベース](sql-database-single-database-resources.md)と[エラスティック プール](sql-database-elastic-pool.md)の両方について、お客様が **Basic**、**Standard**、**Premium** のいずれかのサービス レベルを選択できます。 サービス レベルは、固定の付属ストレージ容量、固定のバックアップ リテンション期間、および固定の価格を持つさまざまなパフォーマンス レベルによって区別されます。 すべてのサービス レベルで、ダウンタイムなしでパフォーマンス レベルを柔軟に変更できます。 単一データベースとエラスティック プールは、サービス レベルおよびパフォーマンス レベルに基づいて、時間単位で課金されます。

> [!IMPORTANT]
> SQL Database マネージ インスタンスは、現在、パブリック プレビュー段階であり、DTU ベースの購入モデルをサポートしていません。 詳細については、[Azure SQL Database マネージ インスタンスに関するページ](sql-database-managed-instance.md)を参照してください。 

## <a name="choosing-a-service-tier-in-the-dtu-based-purchasing-model"></a>DTU ベースの購入モデルでのサービス レベルの選択

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

## <a name="performance-level-and-storage-size-limits-in-the-dtu-based-purchasing-model"></a>DTU ベースの購入モデルのパフォーマンス レベルとストレージ サイズの制限

パフォーマンス レベルは、単一データベースの場合はデータベース トランザクション ユニット (DTU) で、エラスティック プールの場合はエラスティック データベース トランザクション ユニット (eDTU) で表されます。 DTU と eDTU の詳細については、[DTU と eDTU の概要](sql-database-what-is-a-dtu.md)に関する記事をご覧ください。

### <a name="single-databases"></a>Single Database

||Basic|標準|Premium|
| :-- | --: | --: | --: | --: |
| 最大ストレージ サイズ* | 2 GB | 1 TB (テラバイト) | 4 TB  | 
| 最大 DTU | 5 | 3000 | 4000 | |
||||||

単一データベースに対して選択できる具体的なパフォーマンス レベルとストレージ サイズの詳細については、[単一データベースに対する SQL Database の DTU ベースのリソース制限](sql-database-dtu-resource-limits.md#single-database-storage-sizes-and-performance-levels)に関するページをご覧ください。

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
> -  付属のストレージ容量を超えるストレージ サイズはプレビュー段階であり、追加料金が適用されます。 詳細については、「 [SQL Database の価格](https://azure.microsoft.com/pricing/details/sql-database/)」をご覧ください。 
> -  Premium レベルでは、1 TB を超えるストレージは、現時点では次のリージョンで利用できます。オーストラリア東部、オーストラリア南東部、ブラジル南部、カナダ中部、カナダ東部、米国中部、フランス中部、ドイツ中部、東日本、西日本、韓国中部、米国中北部、北ヨーロッパ、米国中南部、東南アジア、英国南部、英国西部、米国東部 2、米国西部、米国政府バージニア、および西ヨーロッパ。 [P11 ～ P15 の現時点での制限](sql-database-dtu-resource-limits.md#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb)に関するページを参照してください。  

エラスティック プールに対して選択できる具体的なパフォーマンス レベルとストレージ サイズの詳細については、[SQL Database の DTU ベースのリソース制限](sql-database-dtu-resource-limits.md#elastic-pool-storage-sizes-and-performance-levels)に関するページをご覧ください。



## <a name="next-steps"></a>次の手順

- 選択できる具体的なパフォーマンス レベルとストレージ サイズの詳細については、[SQL Database の DTU ベースのリソース制限](sql-database-dtu-resource-limits.md)および [SQL Database の仮想コアベースのリソース制限](sql-database-vcore-resource-limits.md)に関するページをご覧ください。
- よく寄せられる質問の回答については、「[SQL Database に関する FAQ](sql-database-faq.md)」を参照してください。
- 「[Azure サブスクリプションとサービスの制限、クォータ、制約](../azure-subscription-service-limits.md)」をご覧ください。
