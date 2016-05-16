<properties
	pageTitle="Windows 上で PHP を使用して SQL Database に接続する | Microsoft Azure"
	description="Windows クライアントから、Azure SQL Database に接続して、クライアントが必要とするソフトウェア コンポーネントへのリンクを提供するサンプル PHP プログラムを示します。"
	services="sql-database"
	documentationCenter=""
	authors="meet-bhagdev"
	manager="jhubbard"
	editor=""/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="php"
	ms.topic="article"
	ms.date="04/27/2016"
	ms.author="meetb"/>


# Windows 上で PHP を使用して SQL Database に接続する


[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]


このトピックでは、Windows 上で実行される PHP で記述されたクライアント アプリケーションから Azure SQL Database に接続する方法について説明します。

## 手順 1: 開発環境を設定する

[PHP 開発用の開発環境を構成する](https://msdn.microsoft.com/library/mt720663.aspx)

## 手順 2: SQL Database を作成する

「[作業の開始](sql-database-get-started.md)」ページで、サンプル データベースを作成する方法についてご確認ください。ガイドに従って、**AdventureWorks データベースのテンプレート**を作成することが重要です。以下に示す例は、**AdventureWorks スキーマ** とのみ動作します。


## 手順 3: 接続の詳細を取得する

[AZURE.INCLUDE [sql-database-include-connection-string-details-20-portalshots](../../includes/sql-database-include-connection-string-details-20-portalshots.md)]


## 手順 4: サンプル コードを実行する

* [PHP を使用した SQL 接続の概念実証](https://msdn.microsoft.com/library/mt720665.aspx)
* [PHP を使用して SQL に弾性的に接続する](https://msdn.microsoft.com/library/mt720667.aspx)


## 次のステップ


PHP のインストールと使用に関する詳細については、「[Accessing SQL Server Databases with PHP (PHP を使用して SQL Server のデータベースにアクセスする)](http://social.technet.microsoft.com/wiki/contents/articles/1258.accessing-sql-server-databases-from-php.aspx)」をご覧ください。

<!---HONumber=AcomDC_0504_2016-->