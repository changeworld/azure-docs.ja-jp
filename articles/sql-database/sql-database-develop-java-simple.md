---
title: "Windows 上で JDBC を含む Java を使用して、SQL Database に接続する | Microsoft Docs"
description: "Azure SQL Database への接続に使用できる Java コード サンプルについて説明します。 サンプルは JDBC を使用し、Windows クライアント コンピューター上で実行されます。"
services: sql-database
documentationcenter: 
author: LuisBosquez
manager: jhubbard
editor: genemi
ms.assetid: 08fc49b1-cd48-4dcc-a293-ff22a4d2d62c
ms.service: sql-database
ms.custom: development
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 10/03/2016
ms.author: lbosq
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 5d8934ab6ac0fd29e5b389c2690b5ef42575e005


---
# <a name="connect-to-sql-database-by-using-java-with-jdbc-on-windows"></a>Windows 上で JDBC を含む Java を使用して、SQL Database に接続する
[!INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]

このトピックでは、Azure SQL Database への接続に使用できる Java コード サンプルについて説明します。 Java サンプルは、Java Development Kit (JDK) バージョン 1.8 に依存します。 サンプルは、JDBC ドライバーを使用して、Azure SQL Database に接続されます。

## <a name="step-1-configure-development-environment"></a>手順 1: 開発環境を設定する
[Java 開発用の開発環境を構成する](https://msdn.microsoft.com/library/mt720658.aspx)

## <a name="step-2-create-a-sql-database"></a>手順 2: SQL Database を作成する
「 [作業の開始](sql-database-get-started.md) 」ページで、データベースを作成する方法についてご確認ください。  

## <a name="step-3-get-connection-string"></a>手順 3: 接続文字列を取得する
[!INCLUDE [sql-database-include-connection-string-jdbc-20-portalshots](../../includes/sql-database-include-connection-string-jdbc-20-portalshots.md)]

> [!NOTE]
> JTDS JDBC ドライバーを使用している場合は、接続文字列の URL に "ssl=require" を追加し、JVM の次のオプションを設定する必要があります。"-Djsse.enableCBCProtection=false"。 この JVM オプションはセキュリティの脆弱性を修正するプログラムを無効にするため、このオプションを設定する前に、どのようなリスクがあるかを必ず理解しておいてください。
> 
> 

## <a name="step-4-run-sample-code"></a>手順 4: サンプル コードを実行する
* [Java を使用した SQL 接続の概念実証](https://msdn.microsoft.com/library/mt720656.aspx)

## <a name="next-steps"></a>次のステップ
* [Java Developer Center](/develop/java/)を参照して下さい。
* 「 [SQL Database の開発: 概要](sql-database-develop-overview.md)
*  [Microsoft JDBC Driver for SQL Server](https://msdn.microsoft.com/library/mt484311.aspx)

## <a name="additional-resources"></a>その他のリソース
* [Azure SQL Database を使用するマルチテナント SaaS アプリケーションの設計パターン](sql-database-design-patterns-multi-tenancy-saas-applications.md)
*  [SQL Database の機能](https://azure.microsoft.com/services/sql-database/)




<!--HONumber=Nov16_HO3-->


