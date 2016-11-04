---
title: Windows 上で JDBC を含む Java を使用して、SQL Database に接続する | Microsoft Docs
description: Azure SQL Database への接続に使用できる Java コード サンプルについて説明します。サンプルは JDBC を使用し、Windows クライアント コンピューター上で実行されます。
services: sql-database
documentationcenter: ''
author: LuisBosquez
manager: jhubbard
editor: genemi

ms.service: sql-database
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 06/16/2016
ms.author: lbosq

---
# Windows 上で JDBC を含む Java を使用して、SQL Database に接続する
[!INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]

このトピックでは、Azure SQL Database への接続に使用できる Java コード サンプルについて説明します。Java サンプルは、Java Development Kit (JDK) バージョン 1.8 に依存します。サンプルは、JDBC ドライバーを使用して、Azure SQL Database に接続されます。

## 手順 1: 開発環境を設定する
[Java 開発用の開発環境を構成する](https://msdn.microsoft.com/library/mt720658.aspx)

## 手順 2: SQL Database を作成する
「[作業の開始](sql-database-get-started.md)」ページで、データベースを作成する方法についてご確認ください。

## 手順 3: 接続文字列を取得する
[!INCLUDE [sql-database-include-connection-string-jdbc-20-portalshots](../../includes/sql-database-include-connection-string-jdbc-20-portalshots.md)]

> [!NOTE]
> JTDS JDBC ドライバーを使用している場合は、接続文字列の URL に "ssl=require" を追加し、JVM の次のオプションを設定する必要があります。"-Djsse.enableCBCProtection=false"。この JVM オプションはセキュリティの脆弱性を修正するプログラムを無効にするため、このオプションを設定する前に、どのようなリスクがあるかを必ず理解しておいてください。
> 
> 

## 手順 4: サンプル コードを実行する
* [Java を使用した SQL 接続の概念実証](https://msdn.microsoft.com/library/mt720656.aspx)

## 次のステップ
* [Java Developer Center](/develop/java/) を参照して下さい。
* 「[SQL Database の開発: 概要](sql-database-develop-overview.md)」の確認
* [Microsoft JDBC Driver for SQL Server](https://msdn.microsoft.com/library/mt484311.aspx) の詳細

## その他のリソース
* [Azure SQL Database を使用するマルチテナント SaaS アプリケーションの設計パターン](sql-database-design-patterns-multi-tenancy-saas-applications.md)
* [SQL Database の機能](https://azure.microsoft.com/services/sql-database/)すべてを確認します。

<!---HONumber=AcomDC_0622_2016-->