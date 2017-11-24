---
title: "Azure SQL Database マルチテナント アプリの例 - Wingtip SaaS | Microsoft Docs"
description: "Azure SQL Database を使用するマルチテナント アプリケーションのサンプルを使って、Wingtip SaaS の例について説明します"
keywords: "SQL データベース チュートリアル"
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.custom: scale out apps
ms.workload: On Demand
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/12/2017
ms.author: sstein
ms.openlocfilehash: ddd51c23c7e7d01e38b02c79c27d1951eea61e70
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/14/2017
---
# <a name="introduction-to-a-sql-database-multi-tenant-saas-app-example"></a>SQL Database のマルチ テナント SaaS アプリの例の概要

*Wingtip SaaS* アプリケーションは、SQL Database の独自のメリットを示すためのサンプルのマルチテナント アプリです。 このアプリは、SaaS アプリケーション パターンとしてテナント単位のデータベースを使用して、複数のテナントにサービスを提供します。 アプリの目的は、複数の SaaS の設計と管理のパターンを含む各種の SaaS シナリオを実現している Azure SQL Database の機能を紹介することにあります。 このアプリをすぐに使い始めるために、Wingtip SaaS アプリを 5 分以内でデプロイできます。

アプリケーションのソース コードと管理スクリプトは、[WingtipSaaS](https://github.com/Microsoft/WingtipSaaS) GitHub リポジトリから入手できます。 スクリプトを実行するには、[Learning Modules](#download-and-unblock-the-wingtip-saas-scripts) フォルダーをローカル コンピューターにダウンロードします。

## <a name="application-architecture"></a>アプリケーションのアーキテクチャ

Wingtip SaaS アプリでは、テナント単位のデータベース モデルを使用します。また、SQL エラスティック プールを使用して効率を最大化します。 テナントをプロビジョニングしてデータにマッピングするために、カタログ データベースが使用されます。 主要な Wingtip SaaS アプリケーションは、3 つのサンプル テナントと 1 つのカタログ データベースを備えたプールを使用します。 多数の Wingtip SaaS チュートリアルを完了すると、分析データベース、複数データベース スキーマ管理などを導入することで、結果的に初期デプロイへのアドオンになります。


![Wingtip SaaS のアーキテクチャ](media/saas-dbpertenant-wingtip-app-overview/app-architecture.png)


チュートルアルを実施してアプリを操作する際は、SaaS パターンに注目することが重要です。これは、SaaS パターンがデータ層に関連付けられているためです。 つまり、データ層に注目し、アプリ自体を過剰に分析しないでください。 これらの SaaS パターンの実装を理解することが、特定のビジネス要件に必要な変更を考慮しながら、アプリケーションにこれらのパターンを実装するための鍵となります。

## <a name="sql-database-wingtip-saas-tutorials"></a>SQL Database Wingtip SaaS のチュートリアル

アプリをデプロイしたら、初期デプロイを前提とする以下のチュートリアルを参照してください。 これらのチュートリアルでは、SQL Database、SQL Data Warehouse、およびその他の Azure サービスに組み込まれた機能を活用した一般的な SaaS パターンを確認できます。 チュートリアルには、PowerShell スクリプトとその詳細な説明が含まれています。これらを利用すると、SaaS 管理パターンの理解、およびお使いのアプリケーションでの同じ SaaS 管理パターンの実装を大幅に簡略化できます。


| チュートリアル | Description |
|:--|:--|
| [Azure SQL Database のマルチテナント SaaS アプリ例のガイダンスとヒント](saas-dbpertenant-wingtip-app-guidance-tips.md) | **ここから開始** PowerShell スクリプトをダウンロードして実行し、アプリケーションの各部分を準備します。 |
|[Wingtip SaaS アプリケーションをデプロイして調査する](saas-dbpertenant-get-started-deploy.md)|  Wingtip SaaS アプリケーションをお使いの Azure サブスクリプションにデプロイして調査する |
|[テナントのプロビジョニングおよびカタログ登録を行う](saas-dbpertenant-provision-and-catalog.md)| アプリケーションからカタログ データベースを使用するテナントに接続する方法、カタログがテナントをデータにマッピングする方法について説明します。 |
|[パフォーマンスを監視および管理する](saas-dbpertenant-performance-monitoring.md)| SQL Database の監視機能の使用方法と、パフォーマンスのしきい値が超過した場合のアラートの設定方法について説明します。 |
|[Log Analytics (OMS) を使って監視する](saas-dbpertenant-log-analytics.md) | [Log Analytics](../log-analytics/log-analytics-overview.md) を使って、複数のプールにある大量のリソースを監視する方法を説明します。 |
|[シングル テナントを復元する](saas-dbpertenant-restore-single-tenant.md)| テナント データベースを前のポイント イン タイムに復元する方法を説明します。 既存のテナント データベースをオンラインにしたまま、並列のデータベースに復元する手順も含まれています。 |
|[テナント スキーマを管理する](saas-tenancy-schema-management.md)| すべての Wingtip SaaS テナントにあるスキーマの更新および参照データの更新を行う方法について説明します。 |
|[アドホック分析を実行する](saas-tenancy-adhoc-analytics.md) | アドホック分析データベースを作成し、すべてのテナントに対してリアルタイムに配布されたクエリを実行します。  |
|[テナント分析を実行する](saas-tenancy-tenant-analytics.md) | 実行中のオフライン分析クエリに対応する分析データベースまたはデータ ウェアハウスに、テナント データを抽出します。 |


## <a name="next-steps"></a>次のステップ

- [Azure SQL Database のマルチテナント SaaS アプリ例のガイダンスとヒント](saas-dbpertenant-wingtip-app-guidance-tips.md)

- [Wingtip SaaS アプリケーションのデプロイ](saas-dbpertenant-get-started-deploy.md)
