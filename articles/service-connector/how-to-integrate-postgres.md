---
title: Azure Database for PostgreSQL を Service Connector と統合する
description: Service Connector を使用するアプリケーションに Azure Database for PostgreSQL を統合する
author: shizn
ms.author: xshi
ms.service: serviceconnector
ms.topic: how-to
ms.date: 10/29/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 6c8cfb866da9a57c54f443558212c5d88269fe0a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131017443"
---
# <a name="integrate-azure-database-for-postgresql-with-service-connector"></a>Azure Database for PostgreSQL を Service Connector と統合する

このページでは、Service Connector を使用する Azure Database for PostgreSQL でサポートされている認証の種類とクライアントの種類を示します。 Service Connector を使用しなくても、他のプログラミング言語内で Azure Database for PostgreSQL に引き続き接続できる場合があります。 このページには、サービス接続を作成するときに取得する既定の環境変数の名前と値 (Spring Boot 構成) も示されています。 [Service Connector 環境変数の名前付け規則](concept-service-connector-internals.md)の詳細を参照してください。

## <a name="supported-compute-service"></a>サポートされているコンピューティング サービス

- Azure App Service
- Azure Spring Cloud

## <a name="supported-authentication-types-and-client-types"></a>サポートされている認証の種類とクライアントの種類

| クライアントの種類 | システム割り当てマネージド ID | ユーザー割り当てマネージド ID | Secret/ConnectionString | サービス プリンシパル |
| --- | --- | --- | --- | --- |
| .Net (ADO.NET) | | | ![[はい] アイコン](./media/green-check.png) | |
| Java (JDBC) | | | ![[はい] アイコン](./media/green-check.png) | |
| Java - Spring Boot (JDBC) | | | ![[はい] アイコン](./media/green-check.png) | |
| Node.js (pg) | | | ![[はい] アイコン](./media/green-check.png) | |
| Python (psycopg2) | | | ![[はい] アイコン](./media/green-check.png) | |
| Python-Django | | | ![[はい] アイコン](./media/green-check.png) | |
| Go (pg) | | | ![[はい] アイコン](./media/green-check.png) | |
| PHP (ネイティブ) | | | ![[はい] アイコン](./media/green-check.png) | |
| Ruby (ruby-pg) | | | ![[はい] アイコン](./media/green-check.png) | |

## <a name="default-environment-variable-names-or-application-properties"></a>既定の環境変数名またはアプリケーション プロパティ

### <a name="net-adonet"></a>.NET (ADO.NET) 

**Secret/ConnectionString**

| 既定の環境変数名 | 説明 | 値の例 |
| --- | --- | --- |
| AZURE_POSTGRESQL_CONNECTIONSTRING | ADO.NET PostgreSQL 接続文字列 | `Server={your-postgres-server-name}.postgres.database.azure.com;Database={database-name};Port=5432;Ssl Mode=Require;User Id={username}@{servername};Password=****;` |

### <a name="java-jdbc"></a>Java (JDBC)

**Secret/ConnectionString**

| 既定の環境変数名 | 説明 | 値の例 |
| --- | --- | --- |
| AZURE_POSTGRESQL_CONNECTIONSTRING | JDBC PostgreSQL 接続文字列 | `jdbc:postgresql://{your-postgres-server-name}.postgres.database.azure.com:5432/{database-name}?sslmode=require&user={username}%40{servername}l&password=****` |

### <a name="java---spring-boot-jdbc"></a>Java - Spring Boot (JDBC)

**Secret/ConnectionString**

| Application properties | 説明 | 値の例 |
| --- | --- | --- |
| spring.datatsource.url | データベース URL | `jdbc:postgresql://{your-postgres-server-name}.postgres.database.azure.com:5432/{database-name}?sslmode=require` |
| spring.datatsource.username | データベース ユーザー名 | `{username}@{servername}` |
| spring.datatsource.password | データベースのパスワード | `****` |

### <a name="nodejs-pg"></a>Node.js (pg) 

**Secret/ConnectionString**

| 既定の環境変数名 | 説明 | 値の例 |
|---------|---------|---------|
| AZURE_POSTGRESQL_HOST | データベース ホスト URL | `{your-postgres-server-name}.postgres.database.azure.com` |
| AZURE_POSTGRESQL_USER | データベース ユーザー名 | `{username}@{servername}` |
| AZURE_POSTGRESQL_PASSWORD | データベースのパスワード | `****` |
| AZURE_POSTGRESQL_DATABASE | データベース名 | `{database-name}` |
| AZURE_POSTGRESQL_PORT | ポート番号  | `5432` |
| AZURE_POSTGRESQL_SSL | SSL オプション  | `true` |

### <a name="python-psycopg2"></a>Python (psycopg2)

**Secret/ConnectionString**

| 既定の環境変数名 | 説明 | 値の例 |
| --- | --- | --- |
| AZURE_POSTGRESQL_CONNECTIONSTRING | psycopg2 接続文字列 | `dbname={database-name} host={your-postgres-server-name}.postgres.database.azure.com port=5432 sslmode=require user={username}@{servername} password=****` |

### <a name="python-django"></a>Python-Django

**Secret/ConnectionString**

| 既定の環境変数名 | 説明 | 値の例 |
| --- | --- | --- |
| AZURE_POSTGRESQL_HOST | データベース ホスト URL | `{your-postgres-server-name}.postgres.database.azure.com` |
| AZURE_POSTGRESQL_USER | データベース ユーザー名 | `{username}@{servername}` |
| AZURE_POSTGRESQL_PASSWORD | データベースのパスワード | `****` |
| AZURE_POSTGRESQL_NAME | データベース名 | `{database-name}` |


### <a name="go-pg"></a>Go (pg)

**Secret/ConnectionString**

| 既定の環境変数名 | 説明 | 値の例 |
| --- | --- | --- |
| AZURE_POSTGRESQL_CONNECTIONSTRING | Go pg 接続文字列 | `host={your-postgres-server-name}.postgres.database.azure.com dbname={database-name} sslmode=require user={username}@{servername} password=****` |


### <a name="php-native"></a>PHP (ネイティブ)

**Secret/ConnectionString**

| 既定の環境変数名 | 説明 | 値の例 |
| --- | --- | --- |
| AZURE_POSTGRESQL_CONNECTIONSTRING | PHP ネイティブ postgres 接続文字列 | `host={your-postgres-server-name}.postgres.database.azure.com port=5432 dbname={database-name} sslmode=requrie user={username}@{servername} password=****` |

### <a name="ruby-ruby-pg"></a>Ruby (ruby-pg)

**Secret/ConnectionString**

| 既定の環境変数名 | 説明 | 値の例 |
| --- | --- | --- |
| AZURE_POSTGRESQL_CONNECTIONSTRING | Ruby pg 接続文字列 | `host={your-postgres-server-name}.postgres.database.azure.com port=5432 dbname={database-name} sslmode=require user={username}@{servername} password=****` |

## <a name="next-steps"></a>次のステップ

Service Connector の詳細については、以下のチュートリアルに従ってください。

> [!div class="nextstepaction"]
> [Service Connector の概念について学習する](./concept-service-connector-internals.md)
