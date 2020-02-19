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
ms.openlocfilehash: 68d39b4f363794d50fd05c2067502fc55d5d0170
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/12/2020
ms.locfileid: "77153249"
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

## <a name="next-steps"></a>次のステップ

- [Azure Synapse アーキテクチャ](massively-parallel-processing-mpp-architecture.md)の詳細
- [SQL プールの簡易作成](create-data-warehouse-portal.md)
- [サンプル データを読み込む](sql-data-warehouse-load-sample-databases.md)
- [ビデオ](https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse)を確認する

または、次の Azure Synapse リソースも確認できます。  
* [ブログ](https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/)を検索する
* [機能要求](https://feedback.azure.com/forums/307516-sql-data-warehouse)を送信する
* [サポート チケットを作成](sql-data-warehouse-get-started-create-support-ticket.md)
* [MSDN フォーラム](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureSQLDataWarehouse)を検索する
* [Stack Overflow フォーラム](https://stackoverflow.com/questions/tagged/azure-sqldw)を検索する