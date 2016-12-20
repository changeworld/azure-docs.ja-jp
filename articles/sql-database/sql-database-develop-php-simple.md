---
title: "Windows 上で PHP を使用して SQL Database に接続する | Microsoft Docs"
description: "Windows クライアントから、Azure SQL Database に接続して、クライアントが必要とするソフトウェア コンポーネントへのリンクを提供するサンプル PHP プログラムを示します。"
services: sql-database
documentationcenter: 
author: meet-bhagdev
manager: jhubbard
editor: 
ms.assetid: 4e71db4a-a22f-4f1c-83e5-4a34a036ecf3
ms.service: sql-database
ms.custom: development
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: php
ms.topic: article
ms.date: 10/03/2016
ms.author: meetb
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 162c89b07a0bc690600f0cc59e3f4c70e02eac91


---
# <a name="connect-to-sql-database-by-using-php-on-windows"></a>Windows 上で PHP を使用して SQL Database に接続する
[!INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]

このトピックでは、Windows 上で実行される PHP で記述されたクライアント アプリケーションから Azure SQL Database に接続する方法について説明します。

## <a name="step-1-configure-development-environment"></a>手順 1: 開発環境を設定する
[PHP 開発用の開発環境を構成する](https://msdn.microsoft.com/library/mt720663.aspx)

## <a name="step-2-create-a-sql-database"></a>手順 2: SQL Database を作成する
「 [作業の開始](sql-database-get-started.md) 」ページで、サンプル データベースを作成する方法についてご確認ください。  ガイドに従って、 **AdventureWorks データベースのテンプレート**を作成することが重要です。 以下に示す例は、 **AdventureWorks スキーマ**とのみ動作します。

## <a name="step-3-get-connection-details"></a>手順 3: 接続の詳細を取得する
[!INCLUDE [sql-database-include-connection-string-details-20-portalshots](../../includes/sql-database-include-connection-string-details-20-portalshots.md)]

## <a name="step-4-run-sample-code"></a>手順 4: サンプル コードを実行する
* [PHP を使用した SQL 接続の概念実証](https://msdn.microsoft.com/library/mt720665.aspx)
* [PHP を使用して SQL に弾性的に接続する](https://msdn.microsoft.com/library/mt720667.aspx)

## <a name="next-steps"></a>次のステップ
* 「 [SQL Database の開発: 概要](sql-database-develop-overview.md)
*  [Microsoft PHP Driver for SQL Server](https://msdn.microsoft.com/library/dn865013.aspx)
* PHP のインストールと使用に関する詳細については、「 [Accessing SQL Server Databases with PHP (PHP を使用して SQL Server のデータベースにアクセスする)](http://social.technet.microsoft.com/wiki/contents/articles/1258.accessing-sql-server-databases-from-php.aspx)」をご覧ください。

## <a name="additional-resources"></a>その他のリソース
* [Azure SQL Database を使用するマルチテナント SaaS アプリケーションの設計パターン](sql-database-design-patterns-multi-tenancy-saas-applications.md)
*  [SQL Database の機能](https://azure.microsoft.com/services/sql-database/)




<!--HONumber=Nov16_HO3-->


