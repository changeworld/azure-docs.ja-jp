---
title: "Azure SQL Database ソリューション クイック スタート ガイド | Microsoft Docs"
description: "Azure SQL Database ソリューションの詳細"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 21399478-6296-4ad3-bde2-5e14ee49a8eb
ms.service: sql-database
ms.custom: overview
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: sqldb-quickstart
ms.date: 09/06/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 9bdd5e50412b627fd09d1e6db2f855a35cc01144


---
# <a name="explore-azure-sql-database-solution-quick-starts"></a>Azure SQL Database ソリューション クイック スタート ガイド
この記事には、Azure SQL Database ソリューション クイック スタート ガイドの概要が掲載されています。 クイック スタート ガイドは GitHub の SQL Server サンプル リポジトリにあり、実際のシナリオに基づく包括的なソリューションでの SQL Database の使用方法を示しています。 SQL Database 各機能の使用方法に関して説明した段階形式のチュートリアルについては、「 [Azure SQL Database チュートリアルの確認](sql-database-explore-tutorials.md)」を参照してください。

## <a name="try-the-wingtiptickets-demo-and-hands-on-lab"></a>WingTipTickets デモとハンズオン ラボを試す
[Azure SQL Database WingTipTickets](https://github.com/microsoft/wingtiptickets) デモとハンズオン ラボでは、コンサート チケットの販売に使用される、Azure SQL Database および Azure Search をベースにしたサンプル アプリケーションのデモを示します。

## <a name="collect-and-monitor-resource-usage-data-across-multiple-pools"></a>複数のプールのリソース使用状況データを収集して監視する
[Solution Quick Start: Elastic Pool telemetry using PowerShell (ソリューション クイック スタート ガイド: PowerShell でのエラスティック プール統計情報) (ソリューション クイック スタート ガイド: PowerShell でのエラスティック プール統計情報)](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-sql-db-elastic-pools) 」では、サブスクリプションの複数のプールから SQL Database リソースの使用状況データを収集して監視するためのソリューションを紹介します。 サブスクリプションに多数のデータベースがある場合、各エラスティック プールを個別に監視するのは面倒です。

この問題を解決するには、SQL Database の PowerShell コマンドレットと T-SQL クエリを組み合わせて、複数のプールおよびそのデータベースからリソース使用状況データを収集します。 これにより、リソースの使用状況をより効率的に監視、分析することができます。

このクイック スタート ガイドでは、一連の PowerShell スクリプトおよび T-SQL クエリに加えて、ソリューションの内容やその実装方法に関するドキュメントも紹介しています。

## <a name="get-started-with-elastic-database-in-an-saas-scenario"></a>SaaS シナリオでのエラスティック データベースの概要
 [Solution Quick Start: Elastic Pool custom dashboard for SaaS (ソリューション クイック スタート ガイド: SaaS 用エラスティック プール カスタム ダッシュボード) (ソリューション クイック スタート ガイド: SaaS 用エラスティック プール カスタム ダッシュボード)](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-sql-db-elastic-pools-custom-dashboard) 」では、ソリューションとしてのソフトウェア (SaaS) シナリオ向けに、SQL Database のエラスティック データベース機能を使用して、コスト効率性に優れ拡張可能な SaaS アプリケーション用データベース バックエンドを提供するソリューションを紹介します。

このソリューションでは、Web アプリの実装について説明します。 この Web アプリは、Azure ポータルの補完用カスタム ダッシュボードを使用するロード ジェネレーターによりエラスティック プールに作成されたロードを視覚化します。

このクイック スタート ガイドでは、ロード ジェネレーターと監視 Web アプリに加えて、アプリの働きと使用方法に関するドキュメントも提供します。

## <a name="create-an-azure-sql-database-by-using-code-first-development-and-the-entity-framework"></a>Code First 開発と Entity Framework を使用した Azure SQL Database の作成
「 [新しいデータベースの Code First](https://msdn.microsoft.com/data/jj193542.aspx) 」にあるビデオとサンプルでは、新しいデータベースを対象とした Code First 開発の概要について説明しています。 このシナリオの対象は、まだ存在しないが、Code First によって作成されるデータベースです。 また、シナリオでは、Code First が新しいテーブルを追加する空のデータベースを作成します。

Code First では、C# または Visual Basic .NET クラスを使用してモデルを定義できます。 必要に応じてクラスやプロパティの属性、または fluent API を使用して、追加の構成を実行できます。

## <a name="integrate-elastic-database-tools-into-an-entity-framework-application"></a>Entity Framework アプリケーションへのエラスティック データベース ツールの統合
「[Entity Framework による Elastic Database クライアント ライブラリ](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md)」のサンプルでは、[Elastic Database ツール](sql-database-elastic-scale-get-started.md)と統合するために Entity Framework アプリケーションに加える必要がある変更を示しています。 ここでは、Entity Framework の Code First アプローチを使用して、[シャード マップの管理](sql-database-elastic-scale-shard-map-management.md)と[データ依存ルーティング](sql-database-elastic-scale-data-dependent-routing.md)を構成する方法を重点的に説明します。

このサンプル全体の実際の例として、 [EF 向けの新しいデータベースを対象とした Code First サンプル](http://msdn.microsoft.com/data/jj193542.aspx) をご覧ください。 このドキュメントに付属するサンプル コードは、Visual Studio のコード サンプルに含まれるエラスティック データベース ツールのサンプルの一部です。

## <a name="integrate-elastic-database-tools-with-row-level-security"></a>行レベルのセキュリティとエラスティック データベース ツールの統合
「[弾力性データベース ツールと行レベルのセキュリティを使用したマルチテナント アプリケーション](sql-database-elastic-tools-multi-tenant-row-level-security.md)」では、[行レベルのセキュリティ](https://msdn.microsoft.com/library/dn765131)と [Elastic Database ツール](sql-database-elastic-scale-get-started.md)を統合するために Entity Framework アプリケーションに加える必要のある変更を示しています。 このサンプルでは、これらのテクノロジを使用して、マルチテナントのシャードをサポートする拡張性の高いデータ層を持つアプリケーションを構築する方法について説明します。

構築には、ADO.NET SqlClient または Entity Framework を使用します。 このサンプルでは、マルチテナント シャード データベースのサポートを追加することで、 [Entity Framework によって Elastic Database クライアント ライブラリ](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md) を拡張します。
4 つのテナントと 2 つのマルチテナント シャード データベースを含む、ブログや記事を作成するための簡単なコンソール アプリケーションを構築します。

## <a name="create-online-surveys-with-the-tailspin-surveys-application"></a>Tailspin Surveys アプリケーションを使用したオンライン アンケートの作成
この [Tailspin Surveys サンプル アプリケーション](https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/docs/running-the-app.md)は、Surveys と呼ばれるマルチテナント Web アプリケーションです。このアプリケーションでは、オンライン アンケートを作成できます。 このサンプルは、サインアップ、認証、承認、アプリ ロールなど、マルチテナント アプリケーションでユーザー ID を管理するときの重要な課題に対応しています。

## <a name="learn-about-the-latest-security-features-of-sql-database-with-the-contoso-clinic-demo-application"></a>Contoso クリニック デモ アプリケーションによる SQL Database の最新セキュリティ機能の紹介
この [Contoso クリニック デモ アプリケーション](https://github.com/Microsoft/azure-sql-security-sample) では、 SQL Database の最新のセキュリティ機能を紹介します。

## <a name="next-steps"></a>次のステップ
[Azure SQL Database チュートリアルの確認](sql-database-explore-tutorials.md)




<!--HONumber=Nov16_HO3-->


