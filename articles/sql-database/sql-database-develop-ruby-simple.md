---
title: "Ruby を使用して SQL Database に接続する | Microsoft Docs"
description: "Azure SQL Database に接続するための Ruby コード サンプルを提供します。"
services: sql-database
documentationcenter: 
author: ajlam
manager: jhubbard
editor: 
ms.assetid: 94fec528-58ba-4352-ba0d-25ae4b273e90
ms.service: sql-database
ms.custom: development
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: ruby
ms.topic: article
ms.date: 10/03/2016
ms.author: andrela
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: c66726e199e897b08825798031caf20de181d5a1


---
# <a name="connect-to-sql-database-by-using-ruby"></a>Ruby を使用して SQL Database に接続する
[!INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]

このトピックでは、Ruby を使用して Azure SQL Database に接続し、クエリを実行する方法について説明します。 このサンプルは、Windows、Ubuntu Linux、または Mac のプラットフォームから実行できます。

## <a name="step-1-configure-development-environment"></a>手順 1: 開発環境を設定する
[TinyTDS Ruby Driver for SQL Server 使用の前提条件](https://msdn.microsoft.com/library/mt711041.aspx)

## <a name="step-2-create-a-sql-database"></a>手順 2: SQL Database を作成する
「 [作業の開始](sql-database-get-started.md) 」ページで、サンプル データベースを作成する方法についてご確認ください。  ガイドに従って、 **AdventureWorks データベースのテンプレート**を作成することが重要です。 以下に示す例は、 **AdventureWorks スキーマ**とのみ動作します。

## <a name="step-3-get-connection-details"></a>手順 3: 接続の詳細を取得する
[!INCLUDE [sql-database-include-connection-string-details-20-portalshots](../../includes/sql-database-include-connection-string-details-20-portalshots.md)]

## <a name="step-4-run-sample-code"></a>手順 4: サンプル コードを実行する
[Ruby を使用した SQL 接続の概念実証](http://msdn.microsoft.com/library/mt715797.aspx)

## <a name="next-steps"></a>次のステップ
* 「 [SQL Database の開発: 概要](sql-database-develop-overview.md)
*  [Microsoft Ruby Driver for SQL Server](https://msdn.microsoft.com/library/mt691981.aspx)

## <a name="additional-resources"></a>その他のリソース
* [Azure SQL Database を使用するマルチテナント SaaS アプリケーションの設計パターン](sql-database-design-patterns-multi-tenancy-saas-applications.md)
*  [SQL Database の機能](https://azure.microsoft.com/services/sql-database/)




<!--HONumber=Nov16_HO3-->


