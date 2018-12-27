---
title: Azure Database for PostgreSQL の接続ライブラリ
description: この記事では、アプリケーションをコーディングして PostgreSQL for Azure Database に接続およびクエリを実行するときに、開発者が使用できるさまざまなライブラリやドライバーについて説明します。
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: edbd3b36e1c40cda3b39c85f3deb4c9e8540fd1b
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/24/2018
ms.locfileid: "49984963"
---
# <a name="connection-libraries-for-azure-database-for-postgresql"></a>Azure Database for PostgreSQL の接続ライブラリ
この記事では、Azure Database for PostgreSQL に接続およびクエリを実行するアプリケーションの開発に使用できるライブラリとドライバーを紹介します。

## <a name="client-interfaces"></a>クライアント インターフェイス
PostgreSQL サーバーへの接続に使用されるほとんどの言語クライアント ライブラリは外部プロジェクトであり、個別に配布されています。 一覧に示されたライブラリは、Windows、Linux、および Macプラットフォームで Azure Database for PostgreSQL への接続のためにサポートされています。 「次のステップ」セクションで、いくつかのクイックスタートの例を示します。

| **言語** | **クライアント インターフェイス** | **追加情報** | **ダウンロード** |
|--------------|----------------------------------------------------------------|-------------------------------------|--------------------------------------------------------------------|
| Python | [psycopg](http://initd.org/psycopg/) | DB API 2.0 に準拠している | [ダウンロード](http://initd.org/psycopg/download/) |
| PHP | [php-pgsql](https://secure.php.net/manual/en/book.pgsql.php) | データベース拡張機能 | [インストール](https://secure.php.net/manual/en/pgsql.installation.php) |
| Node.js | [Pg npm パッケージ](https://www.npmjs.com/package/pg) | 純粋な JavaScript 非ブロッキング クライアント | [インストール](https://www.npmjs.com/package/pg) |
| Java | [JDBC](https://jdbc.postgresql.org/) | Type 4 JDBC ドライバー | [ダウンロード](https://jdbc.postgresql.org/download.html)  |
| Ruby | [Pg gem](https://deveiate.org/code/pg/) | Ruby インターフェイス | [ダウンロード](https://rubygems.org/downloads/pg-0.20.0.gem) |
| Go | [Package pq](https://godoc.org/github.com/lib/pq) | 純粋な Go postgres ドライバー | [インストール](https://github.com/lib/pq/blob/master/README.md) |
| C\#/ .NET | [Npgsql](https://www.npgsql.org/) | ADO.NET データ プロバイダー | [ダウンロード](https://www.microsoft.com/net/) |
| ODBC | [psqlODBC](https://odbc.postgresql.org/) | ODBC ドライバー | [ダウンロード](https://www.postgresql.org/ftp/odbc/versions/) |
| C | [libpq](https://www.postgresql.org/docs/9.6/static/libpq.html) | プライマリ C 言語インターフェイス | あり |
| C++ | [libpqxx](http://pqxx.org/) | 新しいスタイルの C++ インターフェイス | [ダウンロード](http://pqxx.org/download/software/) |

## <a name="next-steps"></a>次の手順
これらのクイックスタートを読み、次の言語を使用して Azure Database for PostgreSQL に接続およびクエリを実行する方法について確認します。

[Python](./connect-python.md) | [Node.JS](./connect-nodejs.md) | [Java](./connect-java.md) | [Ruby](./connect-ruby.md) | [PHP](./connect-php.md) | [.NET (C#)](./connect-csharp.md) | [Go](./connect-go.md)
