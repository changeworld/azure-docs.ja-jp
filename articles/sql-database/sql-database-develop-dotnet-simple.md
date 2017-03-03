---
title: ".NET (C#) からの SQL Database 使用 | Microsoft Docs"
description: "このクイック スタートのコード サンプルを使用して、C# で最新のアプリケーションを構築し、Azure SQL Database を使用してクラウドの強力なリレーショナル データベースでバックアップします。"
services: sql-database
documentationcenter: 
author: tobbox
manager: jhubbard
editor: 
ms.assetid: 7faca033-24b4-4f64-9301-b4de41e73dfd
ms.service: sql-database
ms.custom: development
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 02/03/2017
ms.author: tobiast
translationtype: Human Translation
ms.sourcegitcommit: 1f1c6c89c492d18e0678fa4650b6c5744dc9f7d1
ms.openlocfilehash: cc756c662b97c64ab30a7d5bf2cc325f9e74d905
ms.lasthandoff: 02/07/2017


---
# <a name="connect-to-sql-database-by-using-net-c"></a>.NET (C#) を使用して SQL Database に接続する
[!INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]

## <a name="step-1--configure-development-environment"></a>手順 1: 開発環境を設定する
[ADO.NET 開発用の開発環境を構成する](https://docs.microsoft.com/sql/connect/ado-net/step-1-configure-development-environment-for-ado-net-development/)

## <a name="step-2-create-a-sql-database"></a>手順 2: SQL Database を作成する
「 [作業の開始](sql-database-get-started.md) 」ページで、サンプル データベースを作成する方法についてご確認ください。  ガイドに従って、 **AdventureWorks データベースのテンプレート**を作成することが重要です。 以下に示す例は、 **AdventureWorks スキーマ**とのみ動作します。  

## <a name="step-3--get-connection-string"></a>手順 3: 接続文字列を取得する
[!INCLUDE [sql-database-include-connection-string-dotnet-20-portalshots](../../includes/sql-database-include-connection-string-dotnet-20-portalshots.md)]

## <a name="step-4-run-sample-code"></a>手順 4: サンプル コードを実行する
* [ADO.NET を使用した SQL 接続の概念実証](https://docs.microsoft.com/sql/connect/ado-net/step-3-proof-of-concept-connecting-to-sql-using-ado-net/)
* [ADO.NET を使用して SQL に弾性的に接続する](https://docs.microsoft.com/sql/connect/ado-net/step-4-connect-resiliently-to-sql-with-ado-net/)

## <a name="next-steps"></a>次のステップ
* [認証および SQL DB を使用する ASP.NET MVC アプリの作成と、Azure App Service へのデプロイ](../app-service-web/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md)
* 「 [SQL Database の開発: 概要](sql-database-develop-overview.md)
* [Microsoft ADO.Net Driver for SQL Server](https://docs.microsoft.com/sql/connect/ado-net/microsoft-ado-net-for-sql-server/)

## <a name="additional-resources"></a>その他のリソース
* [Azure SQL Database を使用するマルチテナント SaaS アプリケーションの設計パターン](sql-database-design-patterns-multi-tenancy-saas-applications.md)
* [SQL Database の機能](https://azure.microsoft.com/services/sql-database/)


