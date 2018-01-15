---
title: "SQL Database の接続ライブラリ | Microsoft Docs"
description: "各種のクライアント プログラミング言語から SQL Server および SQL Database への接続を可能にするモジュールをダウンロードするためのリンクを示します。"
services: sql-database
documentationcenter: 
author: MightyPen
manager: jhubbard
editor: genemi
ms.assetid: 13d899d3-cf46-4e4d-8919-cf4b41ca836d
ms.service: sql-database
ms.custom: develop apps
ms.workload: On Demand
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2017
ms.author: genemi
ms.openlocfilehash: a45393804aa5398bc0c40ca3f3cb6c97b106b81c
ms.sourcegitcommit: 562a537ed9b96c9116c504738414e5d8c0fd53b1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/12/2018
---
# <a name="connectivity-libraries-and-frameworks-for-sql-server"></a>SQL Server の接続ライブラリとフレームワーク

C#、Java、Node.js、PHP、Python などのプログラミング言語を手軽に始めるには、[開始にあたってのチュートリアル](http://aka.ms/sqldev)をご覧ください。 次に Linux、Windows、または macOS 上の Docker で SQL Server を使用してアプリを構築します。

次の表は、クライアント アプリケーションがさまざまな言語から SQL Server への接続に使用できる接続ライブラリや*ドライバー*を示しています。クライアント アプリケーションはオンプレミスおよびクラウドで実行される SQL Server と共にこれらを使用できます。 Linux、Windows、または Docker でこれらを使用して、Azure SQL Database や Azure SQL Data Warehouse に接続できます。 

| 言語 | プラットフォーム | その他のリソース | [ダウンロード] | 作業開始 |
| :-- | :-- | :-- | :-- | :-- |
| C# | Windows、Linux、macOS | [SQL Server 用 Microsoft ADO.NET](https://docs.microsoft.com/sql/connect/ado-net/microsoft-ado-net-for-sql-server) | [ダウンロード](https://www.microsoft.com/net/download/) | [作業開始](https://www.microsoft.com/en-us/sql-server/developer-get-started/csharp/ubuntu)
| Java | Windows、Linux、macOS | [SQL Server 用 Microsoft JDBC ドライバー](http://msdn.microsoft.com/library/mt484311.aspx) | [ダウンロード](https://go.microsoft.com/fwlink/?linkid=852460) |  [作業開始](https://www.microsoft.com/en-us/sql-server/developer-get-started/java/ubuntu)
| PHP | Windows、Linux、macOS| [SQL Server 用 PHP SQL ドライバー](http://msdn.microsoft.com/library/dn865013.aspx) | オペレーティング システム: <br/> \* [Windows](https://www.microsoft.com/download/details.aspx?id=55642) <br/> \* [Linux](https://github.com/Microsoft/msphpsql/tree/dev#install-unix) <br/> \* [macOS](https://github.com/Microsoft/msphpsql/tree/dev#install-unix) |  [作業開始](https://www.microsoft.com/en-us/sql-server/developer-get-started/php/ubuntu)
| Node.js | Windows、Linux、macOS | [SQL Server 用 Node.js ドライバー](http://msdn.microsoft.com/library/mt652093.aspx) | [インストール](https://msdn.microsoft.com/library/mt652094.aspx) |  [作業開始](https://www.microsoft.com/en-us/sql-server/developer-get-started/node/ubuntu)
| Python | Windows、Linux、macOS | [Python SQL ドライバー](http://msdn.microsoft.com/library/mt652092.aspx) | インストール オプション:  <br/> \* [pymssql](https://msdn.microsoft.com/library/mt694094.aspx) <br/> \* [pyodbc](http://msdn.microsoft.com/library/mt763257.aspx) |  [作業開始](https://www.microsoft.com/en-us/sql-server/developer-get-started/python/ubuntu)
| Ruby | Windows、Linux、macOS | [SQL Server 用 Ruby ドライバー](http://msdn.microsoft.com/library/mt691981.aspx) | [インストール](https://msdn.microsoft.com/library/mt711041.aspx) | [作業開始](https://www.microsoft.com/en-us/sql-server/developer-get-started/ruby/ubuntu)
| C++ | Windows、Linux、macOS | [SQL Server 用 Microsoft ODBC ドライバー](https://msdn.microsoft.com/en-us/library/mt654048(v=sql.1).aspx) | [ダウンロード](https://msdn.microsoft.com/en-us/library/mt654048(v=sql.1).aspx) |  

次の表は、クライアント アプリケーションがオンプレミスまたはクラウドで SQL Server と共に使用できる、オブジェクト リレーショナル マッピング (ORM) フレームワークおよび Web フレームワークの例を示しています。 Linux、Windows、または Docker でフレームワークを使用して SQL Database および SQL Data Warehouse に接続できます。 

| 言語 | プラットフォーム | ORM |
| :-- | :-- | :-- |
| C# | Windows、Linux、macOS | [Entity Framework](https://docs.microsoft.com/ef)<br>[Entity Framework Core](https://docs.microsoft.com/ef/core/index) |
| Java | Windows、Linux、macOS |[Hibernate ORM](http://hibernate.org/orm)|
| PHP | Windows、Linux | [Laravel (Eloquent)](https://laravel.com/docs/5.0/eloquent) |
| Node.js | Windows、Linux、macOS | [Sequelize ORM](http://docs.sequelizejs.com) |
| Python | Windows、Linux、macOS |[Django](https://www.djangoproject.com/) |
| Ruby | Windows、Linux、macOS | [Ruby on Rails](http://rubyonrails.org/) |
||||

## <a name="related-links"></a>関連リンク
- クライアント アプリケーションからの接続に使用する [SQL Server 用ドライバー](http://msdn.microsoft.com/library/mt654049.aspx)
- SQL Database への接続:
    - [.NET (C#) を使用して SQL Database に接続する](sql-database-connect-query-dotnet.md)
    - [PHP を使用して SQL Database に接続する](sql-database-connect-query-php.md)
    - [Node.js を使用して SQL Database に接続する](sql-database-connect-query-nodejs.md)
    - [Java を使用して SQL Database に接続する](sql-database-connect-query-java.md)
    - [Python を使用して SQL Database に接続する](sql-database-connect-query-python.md)
    - [Ruby を使用して SQL Database に接続する](sql-database-connect-query-ruby.md)
- 再試行ロジックのコード例:
    - [ADO.NET を使用して SQL に弾性的に接続する][step-4-connect-resiliently-to-sql-with-ado-net-a78n]
    - [PHP を使用して SQL に弾性的に接続する][step-4-connect-resiliently-to-sql-with-php-p42h]


<!-- Link references. -->

[step-4-connect-resiliently-to-sql-with-ado-net-a78n]: https://docs.microsoft.com/sql/connect/ado-net/step-4-connect-resiliently-to-sql-with-ado-net

[step-4-connect-resiliently-to-sql-with-php-p42h]: https://docs.microsoft.com/sql/connect/php/step-4-connect-resiliently-to-sql-with-php

