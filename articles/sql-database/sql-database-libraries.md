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
ms.date: 04/01/2018
ms.openlocfilehash: 40de6a93516a556958c1fd0cd3f861304e55a600
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/25/2018
ms.locfileid: "47165519"
---
# <a name="connectivity-libraries-and-frameworks-for-sql-server"></a>SQL Server の接続ライブラリとフレームワーク

C#、Java、Node.js、PHP、Python などのプログラミング言語を手軽に始めるには、[開始にあたってのチュートリアル](http://aka.ms/sqldev)をご覧ください。 次に Linux、Windows、または macOS 上の Docker で SQL Server を使用してアプリを構築します。

次の表は、クライアント アプリケーションがさまざまな言語から SQL Server への接続に使用できる接続ライブラリや*ドライバー*を示しています。クライアント アプリケーションはオンプレミスおよびクラウドで実行される SQL Server と共にこれらを使用できます。 Linux、Windows、または Docker でこれらを使用して、Azure SQL Database や Azure SQL Data Warehouse に接続できます。 

| Language | プラットフォーム | その他のリソース | [ダウンロード] | 作業開始 |
| :-- | :-- | :-- | :-- | :-- |
| C#の場合 | Windows、Linux、macOS | [SQL Server 用 Microsoft ADO.NET](https://docs.microsoft.com/sql/connect/ado-net/microsoft-ado-net-for-sql-server) | [ダウンロード](https://www.microsoft.com/net/download/) | [はじめに](https://www.microsoft.com/sql-server/developer-get-started/csharp/ubuntu)
| Java | Windows、Linux、macOS | [SQL Server 用 Microsoft JDBC ドライバー](http://msdn.microsoft.com/library/mt484311.aspx) | [ダウンロード](https://go.microsoft.com/fwlink/?linkid=852460) |  [はじめに](https://www.microsoft.com/sql-server/developer-get-started/java/ubuntu)
| PHP | Windows、Linux、macOS| [SQL Server 用 PHP SQL ドライバー](http://msdn.microsoft.com/library/dn865013.aspx) | オペレーティング システム: <br/> \* [Windows](https://www.microsoft.com/download/details.aspx?id=55642) <br/> \* [Linux](https://github.com/Microsoft/msphpsql/tree/dev#install-unix) <br/> \* [macOS](https://github.com/Microsoft/msphpsql/tree/dev#install-unix) |  [はじめに](https://www.microsoft.com/sql-server/developer-get-started/php/ubuntu)
| Node.js | Windows、Linux、macOS | [SQL Server 用 Node.js ドライバー](http://msdn.microsoft.com/library/mt652093.aspx) | [インストール](https://msdn.microsoft.com/library/mt652094.aspx) |  [はじめに](https://www.microsoft.com/sql-server/developer-get-started/node/ubuntu)
| Python | Windows、Linux、macOS | [Python SQL ドライバー](http://msdn.microsoft.com/library/mt652092.aspx) | インストール オプション:  <br/> \* [pymssql](https://msdn.microsoft.com/library/mt694094.aspx) <br/> \* [pyodbc](http://msdn.microsoft.com/library/mt763257.aspx) |  [はじめに](https://www.microsoft.com/sql-server/developer-get-started/python/ubuntu)
| Ruby | Windows、Linux、macOS | [SQL Server 用 Ruby ドライバー](http://msdn.microsoft.com/library/mt691981.aspx) | [インストール](https://msdn.microsoft.com/library/mt711041.aspx) | [はじめに](https://www.microsoft.com/sql-server/developer-get-started/ruby/ubuntu)
| C++ | Windows、Linux、macOS | [SQL Server 用 Microsoft ODBC ドライバー](https://msdn.microsoft.com/library/mt654048(v=sql.1).aspx) | [ダウンロード](https://msdn.microsoft.com/library/mt654048(v=sql.1).aspx) |  

次の表は、クライアント アプリケーションがオンプレミスまたはクラウドで SQL Server と共に使用できる、オブジェクト リレーショナル マッピング (ORM) フレームワークおよび Web フレームワークの例を示しています。 Linux、Windows、または Docker でフレームワークを使用して SQL Database および SQL Data Warehouse に接続できます。 

| Language | プラットフォーム | ORM |
| :-- | :-- | :-- |
| C#の場合 | Windows、Linux、macOS | [Entity Framework](https://docs.microsoft.com/ef)<br>[Entity Framework Core](https://docs.microsoft.com/ef/core/index) |
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

