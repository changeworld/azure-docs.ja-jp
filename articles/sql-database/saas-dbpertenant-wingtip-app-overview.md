---
title: Azure SQL Database マルチテナント アプリの例 - Wingtip SaaS | Microsoft Docs
description: Azure SQL Database を使用するマルチテナント アプリケーションのサンプルを使って、Wingtip SaaS の例について説明します
keywords: SQL データベース チュートリアル
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.custom: scale out apps
ms.topic: article
ms.date: 11/12/2017
ms.author: sstein
ms.openlocfilehash: 451e1fc87fc5f626e78760d8cd5c4115ea02bb0d
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/16/2018
---
# <a name="introduction-to-a-multi-tenant-saas-app-that-uses-the-database-per-tenant-pattern-with-sql-database"></a>SQL Database によるテナントごとのデータベース パターンを使用するマルチテナント SaaS アプリケーションの概要

*Wingtip SaaS* アプリケーションは、サンプルのマルチ テナント アプリです。 このアプリは、テナントごとにデータベースを使用する SaaS アプリケーション パターンで、複数のテナントにサービスを提供します。 このアプリは、さまざまな SaaS の設計と管理のパターンを使用する SaaS シナリオを可能にする Azure SQL Database の機能を紹介します。 このアプリをすぐに使い始めるために、Wingtip SaaS アプリを 5 分以内でデプロイできます。

アプリケーションのソース コードと管理スクリプトは、[WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) GitHub リポジトリから入手できます。 開始する前に、Wingtip Tickets 管理スクリプトをダウンロードしてブロック解除する手順に関する[一般的なガイダンス](saas-tenancy-wingtip-app-guidance-tips.md)を参照してください。

## <a name="application-architecture"></a>アプリケーションのアーキテクチャ

Wingtip SaaS アプリでは、テナント単位のデータベース モデルを使用します。また、SQL エラスティック プールを使用して効率を最大化します。 テナントをプロビジョニングしてデータにマッピングするために、カタログ データベースが使用されます。 主要な Wingtip SaaS アプリケーションは、3 つのサンプル テナントと 1 つのカタログ データベースを備えたプールを使用します。 多数の Wingtip SaaS チュートリアルを完了すると、分析データベース、複数データベース スキーマ管理などが導入されるため、結果的に初期デプロイのアドオンになります。


![Wingtip SaaS のアーキテクチャ](media/saas-dbpertenant-wingtip-app-overview/app-architecture.png)


チュートルアルを実施してアプリを操作する際は、SaaS パターンに注目することが重要です。これは、SaaS パターンがデータ層に関連付けられているためです。 つまり、データ層に注目し、アプリ自体を過剰に分析しないでください。 これらの SaaS パターンの実装を理解することが、特定のビジネス要件に必要な変更を考慮しながら、アプリケーションにこれらのパターンを実装するための鍵となります。

## <a name="sql-database-wingtip-saas-tutorials"></a>SQL Database Wingtip SaaS のチュートリアル

アプリをデプロイしたら、初期デプロイを前提とする以下のチュートリアルを参照してください。 これらのチュートリアルでは、SQL Database、SQL Data Warehouse、およびその他の Azure サービスに組み込まれた機能を活用した一般的な SaaS パターンを確認できます。 チュートリアルには、PowerShell スクリプトとその詳細な説明が含まれています。これらを利用すると、SaaS 管理パターンの理解、およびお使いのアプリケーションでの同じ SaaS 管理パターンの実装を大幅に簡略化できます。


| チュートリアル | [説明] |
|:--|:--|
| [Azure SQL Database のマルチテナント SaaS アプリ例のガイダンスとヒント](saas-tenancy-wingtip-app-guidance-tips.md) | **ここから開始** PowerShell スクリプトをダウンロードして実行し、アプリケーションの各部分を準備します。 |
|[Wingtip SaaS アプリケーションをデプロイして調査する](saas-dbpertenant-get-started-deploy.md)|  Wingtip SaaS アプリケーションをお使いの Azure サブスクリプションにデプロイして調査する |
|[テナントのプロビジョニングおよびカタログ登録を行う](saas-dbpertenant-provision-and-catalog.md)| アプリケーションからカタログ データベースを使用するテナントに接続する方法、カタログがテナントをデータにマッピングする方法について説明します。 |
|[パフォーマンスを監視および管理する](saas-dbpertenant-performance-monitoring.md)| SQL Database の監視機能の使用方法と、パフォーマンスのしきい値が超過した場合のアラートの設定方法について説明します。 |
|[Log Analytics (OMS) を使って監視する](saas-dbpertenant-log-analytics.md) | [Log Analytics](../log-analytics/log-analytics-overview.md) を使って、複数のプールにある大量のリソースを監視する方法を説明します。 |
|[シングル テナントを復元する](saas-dbpertenant-restore-single-tenant.md)| テナント データベースを前のポイント イン タイムに復元する方法を説明します。 既存のテナント データベースをオンラインにしたまま、並列のデータベースに復元する手順も含まれています。 |
|[テナント データベースのスキーマの管理に関するページ](saas-tenancy-schema-management.md)| すべてのテナント データベースに対してスキーマの更新と参照データの更新を行う方法について説明します。 |
|[テナント間で分散クエリを実行する](saas-tenancy-cross-tenant-reporting.md) | アドホック分析データベースを作成し、すべてのテナントに対してリアルタイムに配布されたクエリを実行します。  |
|[抽出されたテナント データに対して分析を実行する](saas-tenancy-tenant-analytics.md) | オフライン分析クエリに対応する分析データベースまたはデータ ウェアハウスにテナント データを抽出します。 |


## <a name="next-steps"></a>次の手順

- [Wingtip Tickets SaaS サンプル アプリをデプロイして使用する際の一般的なガイダンスとヒント](saas-tenancy-wingtip-app-guidance-tips.md)

- [Wingtip SaaS アプリケーションのデプロイ](saas-dbpertenant-get-started-deploy.md)
