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
   ms.date="06/01/2016"
   ms.author="carlrab"/>

# Azure SQL Database ソリューション クイック スタート ガイド

この記事には、Azure SQL Database ソリューション クイック スタート ガイドの概要が掲載されています。クイック スタート ガイドでは、実際のシナリオに基づく包括的なソリューションで SQL Database を使用する方法についてご紹介します。Azure SQL Database 各機能の使用方法について説明した手順のチュートリアルは、 「[SQL Database チュートリアルの確認](sql-database-explore-tutorials.md)」を参照してください。

## 複数のプールのリソース使用状況データを収集して監視する

このソリューション クイック スタート ガイドでは、サブスクリプションの複数のプールから Azure SQL Database リソースの使用状況データを収集して監視するためのソリューションをご紹介します。サブスクリプションに多数のデータベースがある場合、各エラスティック プールを個別に監視するのは面倒です。これを解決するには、SQL Database の PowerShell コマンドレットと T-SQL クエリを組み合わせて、複数のプールおよびそのデータベースからリソース使用状況データを収集し、リソース使用状況の監視と分析を行います。

GitHub SQL Server サンプル リポジトリの「[Manage Mulitiple Elastic Pools in SQL Database Using PowerShell and Power BI ](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-sql-db-elastic-pools)」 (PowerShell および Power BI を使用した、SQL Database の複数のエラスティック プールの管理) では、PowerShell スクリプトと T-SQL クエリを掲載し、その働きと使用方法に関するドキュメントを提供しています。

## SaaS シナリオでエラスティック プールを使用する

このソリューション クイック スタート ガイドでは、エラスティック プールを活用して、コスト効率性に優れたスケーラブルな SaaS アプリケーションのデータベース バックエンドを提供する、ソリューションとしてのソフトウェア (SaaS) シナリオに対するソリューションをご紹介します。こちらのソリューションでは、 Azure ポータルを補完するカスタム ダッシュボードを使用して、ロード ジェネレーターがエラスティック プールに作成したロードを視覚化する、 Web アプリの実装について説明します。

GitHub SQL Server サンプル リポジトリの[エラスティック プールを活用した SaaS シナリオ](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-sql-db-elastic-pools)に関する記事では、ロード ジェネレーターと監視 Web アプリについて紹介し、その働きと使用方法に関するドキュメントを提供しています。

## 次のステップ

[Azure SQL Database チュートリアルの確認](sql-database-explore-tutorials.md)

<!---HONumber=AcomDC_0608_2016-->