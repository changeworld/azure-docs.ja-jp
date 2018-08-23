---
title: Azure Advisor のパフォーマンスに関する推奨事項 | Microsoft Docs
description: Advisor を使用して、Azure のデプロイのパフォーマンスを最適化します。
services: advisor
documentationcenter: NA
author: manbeenkohli
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: advisor
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/16/2016
ms.author: makohli
ms.openlocfilehash: c2d7cc21b1546c9594a2ef174927777f583a6841
ms.sourcegitcommit: 1af4bceb45a0b4edcdb1079fc279f9f2f448140b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/09/2018
ms.locfileid: "42141118"
---
# <a name="advisor-performance-recommendations"></a>Advisor のパフォーマンスに関する推奨事項

Azure Advisor のパフォーマンスに関する推奨事項は、ビジネスに不可欠なアプリケーションのスピードと応答性を向上させるために役立ちます。 Advisor のパフォーマンスに関する推奨事項は、Advisor ダッシュボードの **[パフォーマンス]** タブで取得できます。

## <a name="reduce-dns-time-to-live-on-your-traffic-manager-profile-to-fail-over-to-healthy-endpoints-faster"></a>Traffic Manager プロファイルの DNS Time to Live を短縮して迅速に正常なエンドポイントにフェールオーバーする

Traffic Manager プロファイルで [Time to Live (TTL) 設定](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-performance-considerations)を使用すると、特定のエンドポイントがクエリに応答しなくなった場合にエンドポイントを切り替える時間を指定できます。 TTL 値を減らすと、クライアントは機能しているエンドポイントに短時間でルーティングされるようになります。

Azure Advisor は、長い TTL が構成された Traffic Manager プロファイルを識別し、プロファイルが[高速フェールオーバー](https://azure.microsoft.com/roadmap/fast-failover-and-tcp-probing-in-azure-traffic-manager/)に構成されているかどうかに応じて TTL を 20 秒または 60 秒に構成することを推奨します。

## <a name="improve-database-performance-with-sql-db-advisor"></a>SQL DB Advisor によるデータベースのパフォーマンス向上

Advisor は、すべての Azure リソースに関する推奨事項を、一貫性がある統合された形で提示します。 SQL Database Advisor と統合して、SQL Azure Database のパフォーマンスを向上させるための推奨事項を生成します。 SQL Database Advisor は、SQL Azure Database の使用履歴を分析することで、パフォーマンスを評価します。 その後、データベースの一般的なワークロードを実行する上で最適な推奨事項を提示します。 

> [!NOTE]
> 推奨事項を取得するには、データベースを約 1 週間使用し、その週の間に、何らかの一貫性のあるアクティビティが行われている必要があります。 SQL Database Advisor は、ランダムでむらのあるアクティビティよりも、一貫性のあるアクティビティのクエリ パターンをより簡単に最適化できます。

SQL Database Advisor の詳細については「[SQL Database Advisor](https://azure.microsoft.com/documentation/articles/sql-database-advisor/)」を参照してください。

## <a name="improve-redis-cache-performance-and-reliability"></a>Redis Cache のパフォーマンスと信頼性の向上

Advisor は、メモリの高使用率、サーバーの負荷、ネットワークの帯域幅、または大量のクライアント接続がパフォーマンスに悪影響を与える可能性がある Redis Cache インスタンスを識別します。 Advisor は、潜在的な問題を回避するためのベスト プラクティスの推奨事項も提示します。 Redis Cache に関する推奨事項の詳細については、「[Redis Cache Advisor](https://azure.microsoft.com/documentation/articles/cache-configure/#redis-cache-advisor)」を参照してください。


## <a name="improve-app-service-performance-and-reliability"></a>App Service のパフォーマンスと信頼性の向上

Azure Advisor は、App Services のエクスペリエンスを向上させ、関連するプラットフォームの機能を検出するためのベスト プラクティスの推奨事項を統合します。 App Services に関する推奨事項の例を次に示します。
* アプリの実行時にメモリや CPU のリソースが使い尽くされるインスタンスの検出と軽減オプションの提供。
* Web アプリやデータベースなどのリソースの配置によってパフォーマンスの向上とコストの削減を実現できるインスタンスの検出。 

App Services に関する推奨事項の詳細については、「[Azure App Service のベスト プラクティス](https://azure.microsoft.com/documentation/articles/app-service-best-practices/)を参照してください。

## <a name="remove-data-skew-on-your-sql-data-warehouse-table-to-increase-query-performance"></a>SQL データ ウェアハウス テーブルのデータ スキューを除去してクエリのパフォーマンスを向上させる

データ スキューは、ワークロードの実行時に不要なデータの移動やリソースのボトルネックを引き起こす可能性があります。 Advisor は 15% を超える分散データスキューを検出し、データの再分散とテーブルの分散キーの再選択を行うことをお勧めします。 スキューの識別と除去の詳細については、[スキューのトラブルシューティング](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-distribute#how-to-tell-if-your-distribution-column-is-a-good-choice)に関する記事を参照してください。

## <a name="create-or-update-outdated-table-statistics-on-your-sql-data-warehouse-table-to-increase-query-performance"></a>SQL データ ウェアハウス テーブルの古いテーブル統計情報を作成するか更新してクエリのパフォーマンスを向上させる

Advisor は、最新の[テーブル統計](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-statistics)状態がないテーブルを識別し、テーブル統計を作成または更新することをお勧めします。 SQL データウェアハウスのクエリ オプティマイザーは、最新の統計を使用してクエリ結果のカーディナリティまたは行数を推定して、最速のパフォーマンスをもたらす高品質のクエリ プランを作成できるようにします。

## <a name="how-to-access-performance-recommendations-in-advisor"></a>Advisor のパフォーマンスに関する推奨事項にアクセスする方法

1. [Azure Portal](https://portal.azure.com) にサインインし、[Advisor](https://aka.ms/azureadvisordashboard) を開きます。

2.  Advisor ダッシュボードで、**[パフォーマンス]** タブをクリックします。

## <a name="next-steps"></a>次の手順

Advisor の推奨事項の詳細については、以下を参照してください。

* [Advisor 入門](advisor-overview.md)
* [Advisor の使用を開始する](advisor-get-started.md)
* [Advisor のコストに関する推奨事項](advisor-performance-recommendations.md)
* [Advisor の高可用性に関する推奨事項](advisor-high-availability-recommendations.md)
* [Advisor のセキュリティに関する推奨事項](advisor-security-recommendations.md)

