---
title: Azure Database for MySQL を Service Connector と統合する
description: Service Connector を使用するアプリケーションに Azure Database for MySQL を統合する
author: shizn
ms.author: xshi
ms.service: serviceconnector
ms.topic: how-to
ms.date: 10/29/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: ce2839d614298d33f5bb1697c328b258463b6269
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131089934"
---
# <a name="integrate-azure-database-for-mysql-with-service-connector"></a>Azure Database for MySQL を Service Connector と統合する

このページでは、Service Connector を使用する Azure Database for MySQL でサポートされている認証の種類とクライアントの種類を示します。 Service Connector を使用しなくても、他のプログラミング言語内で Azure Database for MySQL に引き続き接続できる場合があります。 このページには、サービス接続を作成するときに取得する既定の環境変数の名前と値 (Spring Boot 構成) も示されています。 [Service Connector 環境変数の名前付け規則](concept-service-connector-internals.md)の詳細を参照してください。

## <a name="supported-compute-service"></a>サポートされているコンピューティング サービス

- Azure App Service
- Azure Spring Cloud

## <a name="supported-authentication-types-and-client-types"></a>サポートされている認証の種類とクライアントの種類

| クライアントの種類 | システム割り当てマネージド ID | ユーザー割り当てマネージド ID | Secret/ConnectionString | サービス プリンシパル |
| --- | --- | --- | --- | --- |
| .NET (MySqlConnector) | | | ![[はい] アイコン](./media/green-check.png) | |
| Java (JDBC) | | | ![[はい] アイコン](./media/green-check.png) | |
| Java - Spring Boot (JDBC) | | | ![[はい] アイコン](./media/green-check.png) | |
| Node.js (mysql) | | | ![[はい] アイコン](./media/green-check.png) | |
| Python (mysql-connector-python) | | | ![[はい] アイコン](./media/green-check.png) | |
| Python-Django | | | ![[はい] アイコン](./media/green-check.png) | |
| Go (go-sql-driver for mysql) | | | ![[はい] アイコン](./media/green-check.png) | |
| PHP (mysqli) | | | ![[はい] アイコン](./media/green-check.png) | |
| Ruby (mysql2) | | | ![[はい] アイコン](./media/green-check.png) | |

## <a name="default-environment-variable-names-or-application-properties"></a>既定の環境変数名またはアプリケーション プロパティ

### <a name="net-mysqlconnector"></a>.NET (MySqlConnector) 

**Secret/ConnectionString**

| 既定の環境変数名 | 説明 | 値の例 |
| --- | --- | --- |
| AZURE_MYSQL_CONNECTIONSTRING | ADO.NET MySQL 接続文字列 | `Server={MySQLName}.mysql.database.azure.com;Database={MySQLDbName};Port=3306;SSL Mode=Required;User Id={MySQLUsername};Password={TestDbPassword}` |

### <a name="java-jdbc"></a>Java (JDBC)

**Secret/ConnectionString**

| 既定の環境変数名 | 説明 | 値の例 |
| --- | --- | --- |
| AZURE_MYSQL_CONNECTIONSTRING | JDBC MySQL 接続文字列 | `jdbc:mysql://{MySQLName}.mysql.database.azure.com:3306/{MySQLDbName}?sslmode=required&user={MySQLUsername}&password={Uri.EscapeDataString(TestDbPassword)}` |

### <a name="java---spring-boot-jdbc"></a>Java - Spring Boot (JDBC)

**Secret/ConnectionString**

| Application properties | 説明 | 値の例 |
| --- | --- | --- |
| spring.datatsource.url | Spring Boot JDBC データベース URL | `jdbc:mysql://{MySQLName}.mysql.database.azure.com:3306/{MySQLDbName}?sslmode=required` |
| spring.datatsource.username | データベース ユーザー名 | `{MySQLUsername}@{MySQLName}` |
| spring.datatsource.password | データベースのパスワード | `****` |

### <a name="nodejs-mysql"></a>Node.js (mysql) 

**Secret/ConnectionString**

| 既定の環境変数名 | 説明 | 値の例 |
|---------|---------|---------|
| AZURE_MYSQL_HOST | データベース ホスト URL  | `{MySQLName}.mysql.database.azure.com` |
| AZURE_MYSQL_USER | データベース ユーザー名 | `MySQLDbName` |
| AZURE_MYSQL_PASSWORD | データベースのパスワード | `****` |
| AZURE_MYSQL_DATABASE | データベース名  | `{MySQLUsername}@{MySQLName}` |
| AZURE_MYSQL_PORT | ポート番号 | `3306` |
| AZURE_MYSQL_SSL | SSL オプション | `true` |

### <a name="python-mysql-connector-python"></a>Python (mysql-connector-python)

**Secret/ConnectionString**

| 既定の環境変数名 | 説明 | 値の例 |
| --- | --- | --- |
| AZURE_MYSQL_HOST | データベース ホスト URL  | `{MySQLName}.mysql.database.azure.com` |
| AZURE_MYSQL_NAME | データベース名 | `{MySQLDbName}` |
| AZURE_MYSQL_PASSWORD | データベースのパスワード  | `****` |
| AZURE_MYSQL_USER | データベース ユーザー名  | `{MySQLUsername}@{MySQLName}` |

### <a name="python-django"></a>Python-Django

**Secret/ConnectionString**

| 既定の環境変数名 | 説明 | 値の例 |
| --- | --- | --- |
| AZURE_MYSQL_HOST | データベース ホスト URL  | `{MySQLName}.mysql.database.azure.com` |
| AZURE_MYSQL_USER | データベース ユーザー名 | `{MySQLUsername}@{MySQLName}` |
| AZURE_MYSQL_PASSWORD | データベースのパスワード | `****` |
| AZURE_MYSQL_NAME | データベース名 | `MySQLDbName` |


### <a name="go-go-sql-driver-for-mysql"></a>Go (go-sql-driver for mysql)

**Secret/ConnectionString**

| 既定の環境変数名 | 説明 | 値の例 |
| --- | --- | --- |
| AZURE_MYSQL_CONNECTIONSTRING | Go-sql-driver 接続文字列 | `{MySQLUsername}@{MySQLName}:{Password}@tcp({ServerHost}:{Port})/{Database}?tls=true` |


### <a name="php-mysqli"></a>PHP (mysqli)

**Secret/ConnectionString**

| 既定の環境変数名 | 説明 | 値の例 |
|---------|---------|---------|
| AZURE_MYSQL_HOST | データベース ホスト URL | `{MySQLName}.mysql.database.azure.com` |
| AZURE_MYSQL_USERNAME | データベース ユーザー名 | `{MySQLUsername}@{MySQLName}` |
| AZURE_MYSQL_PASSWORD | データベースのパスワード | `****` |
| AZURE_MYSQL_DBNAME | データベース名 | `{MySQLDbName}` |
| AZURE_MYSQL_PORT | ポート番号  | `3306` |
| AZURE_MYSQL_FLAG | SSL またはその他のフラグ | `MYSQLI_CLIENT_SSL` |

### <a name="ruby-mysql2"></a>Ruby (mysql2)

**Secret/ConnectionString**

| 既定の環境変数名 | 説明 | 値の例 |
|---------|---------|---------|
| AZURE_MYSQL_HOST | データベース ホスト URL  | `{MySQLName}.mysql.database.azure.com` |
| AZURE_MYSQL_USERNAME | データベース ユーザー名 | `{MySQLUsername}@{MySQLName}` |
| AZURE_MYSQL_PASSWORD | データベースのパスワード | `****` |
| AZURE_MYSQL_DATABASE | データベース名 | `{MySQLDbName}` |
| AZURE_MYSQL_SSLMODE | SSL オプション | `required` |

## <a name="next-steps"></a>次のステップ

Service Connector の詳細については、以下のチュートリアルに従ってください。

> [!div class="nextstepaction"]
> [Service Connector の概念について学習する](./concept-service-connector-internals.md)
