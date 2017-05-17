---
title: "Azure Database for PostgreSQL の接続ライブラリ | Microsoft Docs"
description: "クライアント プログラムが Azure Database for PostgreSQL に接続するときに使用できる各ライブラリまたはドライバーを示します。"
keywords: azure cloud postgresql postgres
services: postgresql
author: SaloniSonpal
ms.author: salonis
manager: jhubbard
editor: jasonh
ms.assetid: 
ms.service: postgresql - database
ms.tgt_pltfrm: portal
ms.topic: article
ms.date: 05/10/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 6bb3f393a8e9533b86919e9a1ebe3490ab838714
ms.contentlocale: ja-jp
ms.lasthandoff: 05/10/2017

---
# <a name="connection-libraries-for-azure-database-for-postgresql"></a>Azure Database for PostgreSQL の接続ライブラリ
このトピックでは、クライアント プログラムが Azure Database for PostgreSQL に接続するときに使用できる各ライブラリまたはドライバーを示します。

## <a name="client-interfaces"></a>クライアント インターフェイス
PostgreSQL サーバーに接続するほとんどの言語クライアント ライブラリが外部プロジェクトです。このクライアント ライブラリは個別に配布され、 Windows、Linux、および Mac プラットフォームでサポートされます。 一般的なクライアント ドライバーをいくつか次に示します。
| **言語** | **クライアント インターフェイス** | **追加情報** | **ダウンロード** |
|--------------|----------------------------------------------------------------|-------------------------------------|--------------------------------------------------------------------|
| Python | [psycopg](http://initd.org/psycopg/) | DB API 2.0 対応 | [ダウンロード](http://initd.org/psycopg/download/) |
| PHP | [php-pgsql](https://php.net/manual/en/book.pgsql.php) | データベース拡張機能 | [インストール](https://secure.php.net/manual/en/pgsql.installation.php) |
| Node.js | [Pg npm パッケージ](https://www.npmjs.com/package/pg) | 純粋な JavaScript 非ブロッキング クライアント | [インストール](https://www.npmjs.com/package/pg) |
| Java | [JDBC](http://jdbc.postgresql.org/) | Type 4 JDBC ドライバー | [ダウンロード](https://jdbc.postgresql.org/download.html)  |
| Ruby | [Pg gem](https://deveiate.org/code/pg/) | Ruby インターフェイス | [ダウンロード](https://rubygems.org/downloads/pg-0.20.0.gem) |
| Go | [Package pq](https://godoc.org/github.com/lib/pq) | 純粋な Go postgres ドライバー | [インストール](https://github.com/lib/pq/blob/master/README.md) |
| C\#/ .NET | [Npgsql](http://www.npgsql.org/) | ADO.NET データ プロバイダー | [ダウンロード](https://www.microsoft.com/net/) |
| ODBC | [psqlODBC](https://odbc.postgresql.org/) | ODBC ドライバー | [ダウンロード](http://www.postgresql.org/ftp/odbc/versions/) |
| C | [libpq](https://www.postgresql.org/docs/9.6/static/libpq.html) | プライマリ C 言語インターフェイス | あり |
| C++ | [libpqxx](http://pqxx.org/) | 新しいスタイルの C++ インターフェイス | [ダウンロード](http://pqxx.org/download/software/) |

## <a name="next-steps"></a>次のステップ
- サービスの概要については、[Azure Database for PostgreSQL の概要](overview.md)に関するページをご覧ください。
- サーバーの詳細については、[Azure Database for PostgreSQL サーバー](concepts-servers.md)に関するページをご覧ください。
- 最初の PostgreSQL サーバーを作成するには、「[Create an Azure Database for PostgreSQL in the Azure portal (Azure Portal での Azure Database for PostgreSQL の作成)](quickstart-create-server-database-portal.md)」を参照してください。

