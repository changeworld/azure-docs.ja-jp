---
title: "MySQL ドライバーと管理ツールの互換性 | Microsoft Docs"
description: "Azure Database for MySQL に対する MySQL ドライバーと管理ツールの互換性"
services: mysql
author: seanli1988
ms.author: seanli
editor: jasonwhowell
manager: jhubbard
ms.service: mysql-database
ms.topic: article
ms.date: 10/27/2017
ms.openlocfilehash: 1042f7919b8761bdbc23ae19871703c53dff28f0
ms.sourcegitcommit: 4ea06f52af0a8799561125497f2c2d28db7818e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/21/2017
---
# <a name="mysql-drivers-and-management-tools-compatible-with-azure-database-for-mysql"></a>Azure Database for MySQL に対する MySQL ドライバーと管理ツールの互換性
この記事では、Azure Database for MySQL との、ドライバーと管理ツールの互換性について説明します。

## <a name="mysql-drivers"></a>MySQL ドライバー
Azure Database for MySQL では、MySQL データベースの世界で最も人気のある Community Edition を使用しています。 そのため、さまざまな種類のプログラミング言語とドライバーと互換性があります。 目標は、MySQL ドライバーの 3 つの最新バージョンと、常に MySQL ドライバーの機能と使いやすさを向上させ続けているオープン ソース コミュニティの作成者の努力をサポートすることです。 テストによって Azure Database for MySQL 5.6 および 5.7 と互換性のあることがわかっているドライバーの一覧を次の表に示します。

| **ドライバー** | **リンク** | **互換性のあるバージョン** | **互換性のないバージョン** | **メモ** |
| :-------- | :------------------------ | :----------- | :---------------------- | :--------------------------------------- |
| PHP | http://php.net/downloads.php | 5.5 5.6 7.x | 5.3 | SSL MySQLi との PHP 7.0 接続では、接続文字列に MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT を追加します。 <br> ```mysqli_real_connect($conn, $host, $username, $password, $db_name, 3306, NULL, MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT);```<br> PDO の設定: ```PDO::MYSQL_ATTR_SSL_VERIFY_SERVER_CERT``` オプションを false に設定します。|
| .NET | [Github 上の MySqlConnector]: https://github.com/mysql-net/MySqlConnector/releases <br> [Nuget のインストール パッケージ]:<br> https://www.nuget.org/packages/MySqlConnector/ | 0.27 以降 | 0.26.5 以前 | |
| Nodejs |  [Github 上の MySQLjs]:<br> https://github.com/mysqljs/mysql/releases <br> [NPM のインストール パッケージ]:<br> NPM から “npm install mysql” を実行 | 2.15 | 2.14.1 以前 | |
| GO | https://github.com/go-sql-driver/mysql/releases | 1.3 | 1.2 以前 | 接続文字列で allowNativePasswords=true を使用 |
| Python | https://pypi.python.org/pypi/mysql-connector-python | 1.2.3、2.0、2.1、2.2 | 1.2.2 以前 | |
| Java | https://downloads.mariadb.org/connector-java/ | 2.1 2.0 1.6 | 1.5.5 以前 | |

## <a name="management-tools"></a>管理ツール
互換性の利点は、データベース管理ツールにも拡張されています。 データベース操作がユーザーのアクセス許可の範囲内で動作する限り、既存のツールは引き続き Azure Database for MySQL でも機能します。 テストによって Azure Database for MySQL 5.6 および 5.7 と互換性のあることがわかっている 3 つの一般的なデータベース管理ツールの一覧を次の表に示します。

|                                     | **MySQL Workbench 6.x 以降** | **Navicat 12** | **PHPMyAdmin 4.x 以降** |
| :---------------------------------- | :----------------------------- | :------------- | :-------------------------|
| 作成、更新、読み取り、書き込み、削除 | ○ | ○ | ○ |
| SSL 接続 | ○ | ○ | ○ |
| SQL クエリのオート コンプリート | ○ | ○ |  |
| データのインポートとエクスポート | ○ | ○ | ○ |
| 複数の形式へのエクスポート | ○ | ○ | ○ |
| バックアップと復元 |  | ○ |  |
| サーバー パラメーターの表示 | ○ | ○ | ○ |
| クライアント接続の表示 | ○ | ○ | ○ |
