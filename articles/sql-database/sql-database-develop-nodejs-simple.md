---
title: Node.js を使用して SQL Database に接続する | Microsoft Docs
description: Azure SQL Database への接続に使用できる Node.js コード サンプルについて説明します。
services: sql-database
documentationcenter: ''
author: meet-bhagdev
manager: jhubbard
editor: ''

ms.service: sql-database
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 06/16/2016
ms.author: meetb

---
# Node.js を使用して SQL Database に接続する
[!INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]

このトピックでは、Node.js を使用して Azure SQL Database に接続し、クエリを実行する方法について説明します。このサンプルは、Windows、Ubuntu Linux、または Mac のプラットフォームから実行できます。

## 手順 1: 開発環境を設定する
[Tedious Node.js Driver for SQL Server 使用の前提条件](https://msdn.microsoft.com/library/mt652094.aspx)

## 手順 2: SQL Database を作成する
「[作業の開始](sql-database-get-started.md)」ページで、サンプル データベースを作成する方法についてご確認ください。ガイドに従って、**AdventureWorks データベースのテンプレート**を作成することが重要です。以下に示す例は、**AdventureWorks スキーマ** とのみ動作します。

## 手順 3: 接続の詳細を取得する
[!INCLUDE [sql-database-include-connection-string-details-20-portalshots](../../includes/sql-database-include-connection-string-details-20-portalshots.md)]

## 手順 4: サンプル コードを実行する
[Node.js を使用した SQL 接続の概念実証](https://msdn.microsoft.com/library/mt715784.aspx)

## 次のステップ
* 「[SQL Database の開発: 概要](sql-database-develop-overview.md)」の確認
* [Microsoft Node.js Driver for SQL Server](https://msdn.microsoft.com/library/mt652093.aspx) の詳細

## その他のリソース
* [Azure SQL Database を使用するマルチテナント SaaS アプリケーションの設計パターン](sql-database-design-patterns-multi-tenancy-saas-applications.md)
* [SQL Database の機能](https://azure.microsoft.com/services/sql-database/)すべての確認

<!---HONumber=AcomDC_0622_2016-->