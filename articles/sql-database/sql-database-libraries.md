---
title: SQL Database の接続ライブラリ | Microsoft Docs
description: 各種のクライアント プログラミング言語から SQL Server および SQL Database への接続を可能にするモジュールをダウンロードするためのリンクを示します。
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: ''
manager: craigg
ms.date: 12/04/2018
ms.openlocfilehash: b8e41b77bfb47a08d443fb05e9d59f3f0f958358
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2018
ms.locfileid: "52967914"
---
# <a name="connectivity-libraries-and-frameworks-for-sql-server"></a>SQL Server の接続ライブラリとフレームワーク

C#、Java、Node.js、PHP、Python などのプログラミング言語を手軽に始めるには、[開始にあたってのチュートリアル](https://aka.ms/sqldev)をご覧ください。 次に Linux、Windows、または macOS 上の Docker で SQL Server を使用してアプリを構築します。

次の表は、クライアント アプリケーションがさまざまな言語から SQL Server への接続に使用できる接続ライブラリや*ドライバー*を示しています。クライアント アプリケーションはオンプレミスおよびクラウドで実行される SQL Server と共にこれらを使用できます。 Linux、Windows、または Docker でこれらを使用して、Azure SQL Database や Azure SQL Data Warehouse に接続できます。 

| 言語 | プラットフォーム | その他のリソース | ダウンロード | 作業開始 |
| :-- | :-- | :-- | :-- | :-- |
| C# | Windows、Linux、macOS | [SQL Server 用 Microsoft ADO.NET](https://docs.microsoft.com/sql/connect/ado-net/microsoft-ado-net-for-sql-server) | [ダウンロード](https://www.microsoft.com/net/download/) | [作業開始](https://www.microsoft.com/sql-server/developer-get-started/csharp/ubuntu)
| Java | Windows、Linux、macOS | [SQL Server 用 Microsoft JDBC ドライバー](https://msdn.microsoft.com/library/mt484311.aspx) | [ダウンロード](https://go.microsoft.com/fwlink/?linkid=852460) |  [作業開始](https://www.microsoft.com/sql-server/developer-get-started/java/ubuntu)
| PHP | Windows、Linux、macOS| [SQL Server 用 PHP SQL ドライバー](https://docs.microsoft.com/sql/connect/php/microsoft-php-driver-for-sql-server) | [ダウンロード](https://docs.microsoft.com/sql/connect/php/download-drivers-php-sql-server) | [作業開始](https://www.microsoft.com/en-us/sql-server/developer-get-started/php/ubuntu/)
| Node.js | Windows、Linux、macOS | [SQL Server 用 Node.js ドライバー](https://msdn.microsoft.com/library/mt652093.aspx) | [インストール](https://msdn.microsoft.com/library/mt652094.aspx) |  [作業開始](https://www.microsoft.com/sql-server/developer-get-started/node/ubuntu)
| Python | Windows、Linux、macOS | [Python SQL ドライバー](https://msdn.microsoft.com/library/mt652092.aspx) | インストール オプション:  <br/> \* [pymssql](https://msdn.microsoft.com/library/mt694094.aspx) <br/> \* [pyodbc](https://msdn.microsoft.com/library/mt763257.aspx) |  [作業開始](https://www.microsoft.com/sql-server/developer-get-started/python/ubuntu)
| Ruby | Windows、Linux、macOS | [SQL Server 用 Ruby ドライバー](https://msdn.microsoft.com/library/mt691981.aspx) | [インストール](https://msdn.microsoft.com/library/mt711041.aspx) | [作業開始](https://www.microsoft.com/sql-server/developer-get-started/ruby/ubuntu)
| C++ | Windows、Linux、macOS | [SQL Server 用 Microsoft ODBC ドライバー](https://msdn.microsoft.com/library/mt654048(v=sql.1).aspx) | [ダウンロード](https://msdn.microsoft.com/library/mt654048(v=sql.1).aspx) |  

次の表は、クライアント アプリケーションがオンプレミスまたはクラウドで SQL Server と共に使用できる、オブジェクト リレーショナル マッピング (ORM) フレームワークおよび Web フレームワークの例を示しています。 Linux、Windows、または Docker でフレームワークを使用して SQL Database および SQL Data Warehouse に接続できます。 

| 言語 | プラットフォーム | ORM |
| :-- | :-- | :-- |
| C# | Windows、Linux、macOS | [Entity Framework](https://docs.microsoft.com/ef)<br>[Entity Framework Core](https://docs.microsoft.com/ef/core/index) |
| Java | Windows、Linux、macOS |[Hibernate ORM](http://hibernate.org/orm)|
| PHP | Windows、Linux、macOS | [Laravel (Eloquent)](https://laravel.com/docs/5.0/eloquent) |
| Node.js | Windows、Linux、macOS | [Sequelize ORM](http://docs.sequelizejs.com) |
| Python | Windows、Linux、macOS |[Django](https://www.djangoproject.com/) |
| Ruby | Windows、Linux、macOS | [Ruby on Rails](http://rubyonrails.org/) |
||||

## <a name="related-links"></a>関連リンク
- クライアント アプリケーションからの接続に使用する [SQL Server 用ドライバー](https://msdn.microsoft.com/library/mt654049.aspx)
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

