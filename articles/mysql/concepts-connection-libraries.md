---
title: "Azure Database for MySQL の接続ライブラリ | Microsoft Docs"
description: "この記事では、クライアント プログラムが Azure Database for MySQL に接続するときに使用できる各ライブラリまたはドライバーを示します。"
services: mysql
author: mswutao
ms.author: wutao
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 06/08/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: e3f04c20dbd0329149fdd7de56338f5baaedf5ce
ms.contentlocale: ja-jp
ms.lasthandoff: 05/10/2017

---

# <a name="connection-libraries-for-azure-database-for-mysql"></a>Azure Database for MySQL の接続ライブラリ
このトピックでは、クライアント プログラムが Azure Database for MySQL に接続するときに使用できる各ライブラリまたはドライバーを示します。

## <a name="client-interfaces"></a>クライアント インターフェイス
MySQL では、業界標準の ODBC および JDBC と互換性のあるアプリケーションとツールで MySQL を使用するための、標準的なデータベース ドライバー接続を利用できます。 ODBC や JDBC 対応のすべてのシステムが MySQL を使用できます。

| **言語** | **プラットフォーム** | **追加リソース** | **ダウンロード** |
| :----------- | :------------| :-----------------------| :------------|
| PHP | Windows、Linux | [PHP 用 MySQL Native Driver - mysqlnd](https://dev.mysql.com/downloads/connector/php-mysqlnd/) | [ダウンロード](http://php.net/downloads.php) |
| ODBC | Windows、Linux、Mac OS X、Unix プラットフォーム | [MySQL Connector/ODBC 開発者ガイド](https://dev.mysql.com/doc/connector-odbc/en/) | [ダウンロード](https://dev.mysql.com/downloads/connector/odbc/) |
| ADO.NET | Windows | [MySQL Connector/Net 開発者ガイド](https://dev.mysql.com/doc/connector-net/en/) | [ダウンロード](https://dev.mysql.com/downloads/connector/net/) |
| JDBC | プラットフォームに依存しない | [MySQL Connector/J 5.1 開発者ガイド](https://dev.mysql.com/doc/connector-j/5.1/en/) | [ダウンロード](https://dev.mysql.com/downloads/connector/j/) |
| Node.js | Windows、Linux、Mac OS X | [sidorares/node-mysql2](https://github.com/sidorares/node-mysql2/tree/master/documentation) | [ダウンロード](https://github.com/sidorares/node-mysql2) |
| Python | Windows、Linux、Mac OS X | [MySQL Connector/Python 開発者ガイド](https://dev.mysql.com/doc/connector-python/en/) | [ダウンロード](https://dev.mysql.com/downloads/connector/python/) |
| C++ | Windows、Linux、Mac OS X | [MySQL Connector/C++ 開発者ガイド](https://dev.mysql.com/doc/connector-cpp/en/) | [ダウンロード](https://dev.mysql.com/downloads/connector/python/) |
| C | Windows、Linux、Mac OS X | [MySQL Connector/C 開発者ガイド](https://dev.mysql.com/doc/connector-c/en/) | [ダウンロード](https://dev.mysql.com/downloads/connector/c/)


## <a name="next-steps"></a>次のステップ
[Azure Database for MySQL の概要](./overview.md)
[Azure Database for MySQL のサーバーの概念](./concepts-servers.md)

