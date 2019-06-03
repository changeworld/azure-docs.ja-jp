---
title: Azure Database for PostgreSQL - Single Server の接続ライブラリ
description: この記事では、Azure Database for PostgreSQL - Single Server の接続およびクエリを実行するアプリケーションをコーディングするときに開発者が使用できるいくつかのライブラリとドライバーについて説明します。
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 85110126f9bdec225b1644860814cd89832132a1
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/06/2019
ms.locfileid: "65073604"
---
# <a name="connection-libraries-for-azure-database-for-postgresql---single-server"></a>Azure Database for PostgreSQL - Single Server の接続ライブラリ
この記事では、Azure Database for PostgreSQL に接続およびクエリを実行するアプリケーションの開発に使用できるライブラリとドライバーを紹介します。

## <a name="client-interfaces"></a>クライアント インターフェイス
PostgreSQL サーバーへの接続に使用されるほとんどの言語クライアント ライブラリは外部プロジェクトであり、個別に配布されています。 一覧に示されたライブラリは、Windows、Linux、および Macプラットフォームで Azure Database for PostgreSQL への接続のためにサポートされています。 「次のステップ」セクションで、いくつかのクイックスタートの例を示します。

| **Language** | **クライアント インターフェイス** | **追加情報** | **ダウンロード** |
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
