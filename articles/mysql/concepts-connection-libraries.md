---
title: 接続ライブラリ - Azure Database for MySQL
description: この記事では、クライアント プログラムが Azure Database for MySQL に接続するときに使用できる各ライブラリまたはドライバーを示します。
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 276adcb796c6fcdf1ef2d38458ca8f6ac73e5f5a
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/03/2019
ms.locfileid: "74765277"
---
# <a name="connection-libraries-for-azure-database-for-mysql"></a>Azure Database for MySQL の接続ライブラリ
この記事では、クライアント プログラムが Azure Database for MySQL に接続するときに使用できる各ライブラリまたはドライバーを示します。

## <a name="client-interfaces"></a>クライアント インターフェイス
MySQL では、業界標準の ODBC および JDBC と互換性のあるアプリケーションとツールで MySQL を使用するための、標準的なデータベース ドライバー接続を利用できます。 ODBC や JDBC 対応のすべてのシステムが MySQL を使用できます。

| **Language** | **プラットフォーム** | **追加リソース** | **ダウンロード** |
| :----------- | :------------| :-----------------------| :------------|
| PHP | Windows、Linux | [MySQL native driver for PHP - mysqlnd](https://dev.mysql.com/downloads/connector/php-mysqlnd/) | [ダウンロード](https://secure.php.net/downloads.php) |
| ODBC | Windows、Linux、Mac OS X、Unix プラットフォーム | [MySQL Connector/ODBC 開発者ガイド](https://dev.mysql.com/doc/connector-odbc/en/) | [ダウンロード](https://dev.mysql.com/downloads/connector/odbc/) |
| ADO.NET | Windows | [MySQL Connector/Net 開発者ガイド](https://dev.mysql.com/doc/connector-net/en/) | [ダウンロード](https://dev.mysql.com/downloads/connector/net/) |
| JDBC | プラットフォームに依存しない | [MySQL Connector/J 5.1 開発者ガイド](https://dev.mysql.com/doc/connector-j/5.1/en/) | [ダウンロード](https://dev.mysql.com/downloads/connector/j/) |
| Node.js | Windows、Linux、Mac OS X | [sidorares/node-mysql2](https://github.com/sidorares/node-mysql2/tree/master/documentation) | [ダウンロード](https://github.com/sidorares/node-mysql2) |
| Python | Windows、Linux、Mac OS X | [MySQL Connector/Python 開発者ガイド](https://dev.mysql.com/doc/connector-python/en/) | [ダウンロード](https://dev.mysql.com/downloads/connector/python/) |
| C++ | Windows、Linux、Mac OS X | [MySQL Connector/C++ 開発者ガイド](https://dev.mysql.com/doc/connector-cpp/en/) | [ダウンロード](https://dev.mysql.com/downloads/connector/python/) |
| C | Windows、Linux、Mac OS X | [MySQL Connector/C 開発者ガイド](https://dev.mysql.com/doc/refman/8.0/en/c-api.html) | [ダウンロード](https://dev.mysql.com/downloads/connector/c/)
| Perl | Windows、Linux、Mac OS X、Unix プラットフォーム | [DBD::MySQL](https://metacpan.org/pod/DBD::mysql) | [ダウンロード](https://metacpan.org/pod/DBD::mysql) |


## <a name="next-steps"></a>次の手順
これらのクイックスタートを読み、次の言語を使用して Azure Database for MySQL に接続およびクエリを実行する方法について確認します。

[PHP](./connect-php.md) | [Java](./connect-java.md) |  [.NET (C#)](./connect-csharp.md) | [Python](./connect-python.md) | [Node.JS](./connect-nodejs.md) | [Ruby](./connect-ruby.md) | [C++](connect-cpp.md) | [Go](./connect-go.md)

