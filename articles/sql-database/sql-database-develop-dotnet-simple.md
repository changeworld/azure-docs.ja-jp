<properties
	pageTitle=".NET (C#) からの SQL Database 使用 | Microsoft Azure"
	description="このクイック スタートのコード サンプルを使用して、C# で最新のアプリケーションを構築し、Azure SQL Database を使用してクラウドの強力なリレーショナル データベースでバックアップします。"
	services="sql-database"
	documentationCenter=""
	authors="tobbox"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.workload="drivers"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="06/16/2016"
	ms.author="tobiast"/>

# .NET (C#) を使用して SQL Database に接続する

[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]

## 手順 1: 開発環境を設定する

[ADO.NET 開発用の開発環境を構成する](https://msdn.microsoft.com/library/mt718321.aspx)

## 手順 2: SQL Database を作成する

「[作業の開始](sql-database-get-started.md)」ページで、サンプル データベースを作成する方法についてご確認ください。ガイドに従って、**AdventureWorks データベースのテンプレート**を作成することが重要です。以下に示す例は、**AdventureWorks スキーマ** とのみ動作します。

## 手順 3: 接続文字列を取得する

[AZURE.INCLUDE [sql-database-include-connection-string-dotnet-20-portalshots](../../includes/sql-database-include-connection-string-dotnet-20-portalshots.md)]

## 手順 4: サンプル コードを実行する

* [ADO.NET を使用した SQL 接続の概念実証](https://msdn.microsoft.com/library/mt718320.aspx)
* [ADO.NET を使用して SQL に弾性的に接続する](https://msdn.microsoft.com/library/mt703195.aspx)

## 次のステップ

* [認証および SQL DB を使用する ASP.NET MVC アプリの作成と、Azure App Service へのデプロイ](../app-service-web/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md)
* [Mobile Services .NET バックエンドによる既存の SQL データベースを使用するサービスの作成](../mobile-services/mobile-services-dotnet-backend-use-existing-sql-database.md)
* 「[SQL Database の開発: 概要](sql-database-develop-overview.md)」の確認
* [Microsoft ADO.Net Driver for SQL Server](https://msdn.microsoft.com/library/mt657768.aspx) の詳細

## その他のリソース 

* [Azure SQL Database を使用するマルチテナント SaaS アプリケーションの設計パターン](sql-database-design-patterns-multi-tenancy-saas-applications.md)
* すべての [SQL Database の機能](https://azure.microsoft.com/services/sql-database/)を確認

<!---HONumber=AcomDC_0622_2016-->