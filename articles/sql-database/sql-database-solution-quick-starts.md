<properties
   pageTitle="Azure SQL Database ソリューション クイック スタート ガイド | Microsoft Azure"
   description="Azure SQL Database ソリューションの詳細"
   services="sql-database"
   documentationCenter=""
   authors="carlrabeler"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="sqldb-quickstart"
   ms.date="06/22/2016"
   ms.author="carlrab"/>

# Azure SQL Database ソリューション クイック スタート ガイド

この記事には、Azure SQL Database ソリューション クイック スタート ガイドの概要が掲載されています。クイック スタート ガイドでは、実際のシナリオに基づく包括的なソリューションで SQL Database を使用する方法についてご紹介します。Azure SQL Database 各機能の使用方法について説明した手順のチュートリアルは、 「[SQL Database チュートリアルの確認](sql-database-explore-tutorials.md)」を参照してください。

## WingTipTickets デモとハンズオン ラボ

[Azure SQL Database WingTipTickets](https://github.com/microsoft/wingtiptickets) デモとハンズオン ラボ。これらのファイルによって、コンサートのチケットの販売に使用される、Azure SQL Database および Azure Search をベースにしたサンプル アプリケーションのデモを行うハンズオン ラボが構成されています。

## 複数のプールのリソース使用状況データを収集して監視する

このソリューション クイック スタート ガイドでは、サブスクリプションの複数のプールから Azure SQL Database リソースの使用状況データを収集して監視するためのソリューションをご紹介します。サブスクリプションに多数のデータベースがある場合、各エラスティック プールを個別に監視するのは面倒です。これを解決するには、SQL Database の PowerShell コマンドレットと T-SQL クエリを組み合わせて、複数のプールおよびそのデータベースからリソース使用状況データを収集し、リソース使用状況の監視と分析を行います。

GitHub SQL Server サンプル リポジトリの [PowerShell と Power BI を使用した SQL Database における複数のエラスティック プールの管理](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-sql-db-elastic-pools)に関するページでは、PowerShell スクリプトと T-SQL クエリを掲載し、その働きと使用方法に関するドキュメントを提供しています。

## SaaS シナリオでエラスティック プールを使用する

このソリューション クイック スタート ガイドでは、エラスティック プールを活用して、コスト効率性に優れたスケーラブルな SaaS アプリケーションのデータベース バックエンドを提供する、ソリューションとしてのソフトウェア (SaaS) シナリオに対するソリューションをご紹介します。こちらのソリューションでは、 Azure ポータルを補完するカスタム ダッシュボードを使用して、ロード ジェネレーターがエラスティック プールに作成したロードを視覚化する、 Web アプリの実装について説明します。

GitHub SQL Server サンプル リポジトリの [Saas のためのエラスティック プール カスタム ダッシュボード](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-sql-db-elastic-pools-custom-dashboard)に関するページでは、ロード ジェネレーターと監視 Web アプリについて紹介し、その働きと使用方法に関するドキュメントを提供しています。

## Entity Framework と Code First 開発を使用した Azure SQL Database の作成

このビデオとサンプルでは、新しいデータベースを対象とした Code First 開発の概要について説明します。このシナリオには、まだ存在せず、Code First によって作成するデータベース、または、Code First によって新しいテーブルを追加する空のデータベースが対象として含まれます。Code First では、C# または VB.Net クラスを使用してモデルを定義できます。追加の構成は、必要に応じて、クラスやプロパティの属性、または fluent API を使用して実行できます。「[新しいデータベースの Code First](https://msdn.microsoft.com/data/jj193542.aspx)」をご覧ください。

## Entity Framework アプリケーションへの Elastic Database ツールの統合

このサンプルでは、[Elastic Database ツール](sql-database-elastic-scale-get-started.md)と統合するために必要な、Entity Framework アプリケーションへの変更について説明します。ここでは、Entity Framework の Code First アプローチを使用して、[シャード マップの管理](sql-database-elastic-scale-shard-map-management.md)と[データ依存ルーティング](sql-database-elastic-scale-data-dependent-routing.md)を構成する方法を重点的に説明します。このサンプル全体の実際の例として、[EF 向けの新しいデータベースを対象とした Code First サンプル](http://msdn.microsoft.com/data/jj193542.aspx)をご覧ください。このドキュメントに付属するサンプル コードは、Visual Studio のコード サンプルに含まれるエラスティック データベース ツールのサンプルの一部です。「[Entity Framework による Elastic Database クライアント ライブラリ](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md)」をご覧ください。

## 弾力性データベース ツールと行レベルのセキュリティを使用したマルチテナント アプリケーション

このサンプルでは、[Elastic Database ツール](sql-database-elastic-scale-get-started.md)と[行レベル セキュリティ](https://msdn.microsoft.com/library/dn765131)を統合するために必要な、Entity Framework アプリケーションへの変更について説明します。このサンプルでは、これらのテクノロジを使用して、ADO.NET SqlClient と Entity Framework を使用してマルチテナントのシャードをサポートする拡張性の高いデータ層を持つアプリケーションを構築する方法について説明します。このサンプルでは、マルチテナント シャード データベースのサポートを追加することで、[Entity Framework によって Elastic Database クライアント ライブラリ](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md)を拡張します。4 つのテナントと 2 つのマルチテナント シャード データベースを含む、ブログや投稿を作成するための簡単なコンソール アプリケーションを構築します。「[エラスティック データベース ツールと行レベルのセキュリティを使用したマルチテナント アプリケーション](sql-database-elastic-tools-multi-tenant-row-level-security.md)」をご覧ください。

## Tailspin Surveys アプリケーション

このサンプルは、Survey と呼ばれるマルチテナント Web アプリケーションです。このアプリケーションでは、オンライン アンケートを作成できます。このサンプルは、サインアップ、認証、承認、アプリ ロールなど、マルチテナント アプリケーションでユーザー ID を管理するときの重要な課題をいくつか示しています。このサンプルの実行については、「[How to run the Tailspin Surveys sample application (Tailspin Survey サンプル アプリケーションを実行する方法)](https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/docs/running-the-app.md)」をご覧ください。

## Contoso クリニック デモ アプリケーション

このサンプルでは、Azure SQL DB (V12) のセキュリティ機能を紹介します。このサンプルを実行するには、「[Contoso Clinic Demo Application (Contoso クリニック デモ アプリケーション)](https://github.com/Microsoft/azure-sql-security-sample)」をご覧ください。

## 次のステップ

[Azure SQL Database チュートリアルの確認](sql-database-explore-tutorials.md)

<!---HONumber=AcomDC_0720_2016-->