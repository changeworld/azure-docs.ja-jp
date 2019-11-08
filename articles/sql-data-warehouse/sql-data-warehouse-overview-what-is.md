---
title: Azure Synapse Analytics (旧称 SQL DW) とは
description: Azure Synapse Analytics (旧称 SQL DW) は、エンタープライズ データ ウェアハウスとビッグ データの分析を同時に備えた無制限の分析サービスです。
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: overview
ms.subservice: design
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 07bf3070e42af8a03ce1fd7ea4445fc76557e8a3
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/06/2019
ms.locfileid: "73645509"
---
# <a name="what-is-azure-synapse-analytics-formerly-sql-dw"></a>Azure Synapse Analytics (旧称 SQL DW) とは

Azure Synapse は、エンタープライズ データ ウェアハウスとビッグ データ分析がまとめられた無制限の分析サービスです。 サーバーレスのオンデマンド リソースまたはプロビジョニング済みのリソースを使用しながら大規模に、各自の条件で自由にデータを照会することができます。 Azure Synapse では、これら 2 つの環境を 1 つにした統合エクスペリエンスを使用して、データの取り込み、準備、管理、提供を行い、BI と機械学習の差し迫ったニーズに対応できます。

Azure Synapse には、次の 4 つのコンポーネントがあります。
- SQL Analytics:完全な T-SQL ベースの分析 – 一般公開
    - SQL プール (DWU プロビジョニング単位の課金) 
    - SQL オンデマンド (処理された TB 単位の課金) – (プレビュー)
- Spark:緊密に統合された Apache Spark (プレビュー) 
- データ統合:ハイブリッド データ統合 (プレビュー)
- Studio:統一ユーザー エクスペリエンス  (プレビュー)

> [!NOTE]
> Azure Synapse のプレビュー機能にアクセスするには、[こちら](https://aka.ms/synapsepreview)からアクセス権を要求してください。 Microsoft は、すべての要求をトリアージし、できるだけ早く対応します。

## <a name="sql-analytics-and-sql-pool-in-azure-synapse"></a>Azure Synapse の SQL Analytics と SQL プール

SQL Analytics は、Azure Synapse で一般提供されているエンタープライズ データ ウェアハウス機能を表します。 

SQL プールは、SQL Analytics を使用しているときにプロビジョニングされる分析リソースのコレクションを表します。 SQL プールのサイズは、Data Warehouse ユニット (DWU) によって決まります。

簡単な [PolyBase](/sql/relational-databases/polybase/polybase-guide?view=sql-server-2017&viewFallbackFrom=azure-sqldw-latest) T-SQL クエリを使用してビッグ データをインポートしてから、MPP の機能を利用してハイ パフォーマンスの分析を実行します。 統合と分析を進めると、企業が洞察時に信頼できる 1 つの真実が SQL Analytics に構築され、その洞察も高速かつ堅牢になります。  

## <a name="key-component-of-a-big-data-solution"></a>ビッグ データ ソリューションの主要コンポーネント

データ ウェアハウジングは、クラウドベースのエンドツーエンドなビッグ データ ソリューションの主要コンポーネントです。

![データ ウェアハウス ソリューション](media/sql-data-warehouse-overview-what-is/data-warehouse-solution.png) 

クラウド データ ソリューションでは、多様なソースのデータがビッグ データ ストアに取り込まれます。 ビッグ データ ストアに取り込まれた後は、Hadoop、Spark、および機械学習アルゴリズムがデータの準備とトレーニングを行います。 データを複雑な分析にかける準備が整ったら、SQL Analytics で PolyBase を使用してビッグ データ ストアのクエリが実行されます。 PolyBase は、SQL Analytics テーブルにデータを読み込むために標準の T-SQL クエリを使用します。
 
SQL Analytics は列指向のストレージを使用してリレーショナル テーブルにデータを格納します。 この形式にすると、データのストレージ コストが大幅に少なくなり、クエリのパフォーマンスが改善されます。 データを格納すると、大規模な分析を実行できます。 従来のデータベース システムと比較すると、分単位かかっていた分析クエリが秒単位に、日単位が時間単位に短縮されます。 

分析結果は世界中のレポート データベースやアプリケーションに移動できます。 また、ビジネスの分析に洞察を加え、適切な情報に基づいた決断を下すことができるようになります。

## <a name="next-steps"></a>次の手順

- [Azure Synapse アーキテクチャ](/azure/sql-data-warehouse/massively-parallel-processing-mpp-architecture)の詳細
- [SQL プールの簡易作成](create-data-warehouse-portal.md)
- [サンプル データを読み込む][load sample data]
- [ビデオ](/azure/sql-data-warehouse/sql-data-warehouse-videos)を確認する

または、次の Azure Synapse リソースも確認できます。  
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
[create a data warehouse]: ./sql-data-warehouse-get-started-provision.md
[Migration documentation]: ./sql-data-warehouse-overview-migrate.md
[Azure Synapse Analytics solution partners]: ./sql-data-warehouse-partner-business-intelligence.md
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
[SLA for Azure Synapse Analytics]: https://azure.microsoft.com/support/legal/sla/sql-data-warehouse/v1_0/
[Volume Licensing]: https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=37
[Service Level Agreements]: https://azure.microsoft.com/support/legal/sla/
