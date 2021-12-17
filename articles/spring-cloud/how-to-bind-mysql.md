---
title: Azure Database for MySQL インスタンスを Azure Spring Cloud 内のアプリケーションにバインドする方法
description: Azure Database for MySQL インスタンスを Azure Spring Cloud 内のアプリケーションにバインドする方法について説明します
author: karlerickson
ms.service: spring-cloud
ms.topic: how-to
ms.date: 11/04/2019
ms.author: karler
ms.custom: devx-track-java
ms.openlocfilehash: 7c48b9eee1ff727cbd3d2ab7204045d962a21f44
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2021
ms.locfileid: "132490347"
---
# <a name="bind-an-azure-database-for-mysql-instance-to-your-application-in-azure-spring-cloud"></a>Azure Database for MySQL インスタンスを Azure Spring Cloud 内のアプリケーションにバインドする

**この記事の適用対象:** ✔️ Java

Azure Spring Cloud では、Spring Boot アプリケーションを手動で構成せずに、選択した Azure サービスをアプリケーションに自動的にバインドできます。 この記事では、アプリケーションを Azure Database for MySQL インスタンスにバインドする方法について説明します。

## <a name="prerequisites"></a>前提条件

* デプロイ済みの Azure Spring Cloud インスタンス
* Azure Database for MySQL アカウント
* Azure CLI

デプロイされている Azure Spring Cloud インスタンスがない場合は、[クイックスタート: Azure portal を使用して Azure Spring Cloud 内のアプリケーションを起動する](./quickstart.md)方法に関する記事に記載されている手順に従って、初めての Spring Cloud アプリをデプロイしてください。

## <a name="prepare-your-java-project"></a>Java プロジェクトを準備する

1. プロジェクトの *pom.xml* ファイルに、次の依存関係を追加します。

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-jpa</artifactId>
    </dependency>
    ```

1. *application.properties* ファイルから、`spring.datasource.*` プロパティをすべて削除します。

1. `az spring-cloud app deploy` を実行して現在のアプリを更新するか、`az spring-cloud app deployment create` を実行してこの変更のための新しいデプロイを作成します。

## <a name="bind-your-app-to-the-azure-database-for-mysql-instance"></a>アプリを Azure Database for MySQL インスタンスにバインドする

#### <a name="service-binding"></a>[サービス バインド](#tab/Service-Binding)
1. 自分の Azure Database for MySQL アカウントの管理者ユーザー名とパスワードをメモしておきます。

1. サーバーに接続し、MySQL クライアントから **testdb** という名前のデータベースを作成し、新しい非管理者アカウントを作成します。

1. Azure portal の **Azure Spring Cloud** サービスのページで **アプリケーション ダッシュボード** を探し、Azure Database for MySQL インスタンスにバインドするアプリケーションを選択します。  これは、前の手順で更新またはデプロイしたのと同じアプリケーションです。

1. **[サービス バインド]** を選択し、 **[サービス バインドの作成]** ボタンを選択します。

1. **[バインドの種類]** として **[Azure MySQL]** を選択し、前に使用したのと同じデータベース名を使用し、最初の手順でメモしたのと同じユーザー名とパスワードを使用して、フォームに入力します。

1. アプリを再起動すると、このバインドが機能するようになります。

1. サービス バインドが正しいことを確認するには、バインド名を選択し、その詳細を確認します。 `property` フィールドはこのようになります。

    ```properties
    spring.datasource.url=jdbc:mysql://some-server.mysql.database.azure.com:3306/testdb?useSSL=true&requireSSL=false&useLegacyDatetimeCode=false&serverTimezone=UTC
    spring.datasource.username=admin@some-server
    spring.datasource.password=abc******
    spring.jpa.properties.hibernate.dialect=org.hibernate.dialect.MySQL5InnoDBDialect
    ```

#### <a name="terraform"></a>[Terraform](#tab/Terraform)

次の Terraform スクリプトは、Azure Database for MySQL を使用して Azure Spring Cloud アプリを設定する方法を示しています。

```terraform
provider "azurerm" {
  features {}
}

variable "application_name" {
  type        = string
  description = "The name of your application"
  default     = "demo-abc"
}

variable "administrator_login" {
  type        = string
  description = "The MySQL administrator login"
  default     = "myadmin"
}

resource "azurerm_resource_group" "example" {
  name     = "example-resources"
  location = "West Europe"
}

resource "random_password" "password" {
  length           = 32
  special          = true
  override_special = "_%@"
}

resource "azurerm_mysql_server" "database" {
  name                = "mysql-${var.application_name}-001"
  resource_group_name = azurerm_resource_group.example.name
  location            = azurerm_resource_group.example.location

  administrator_login          = var.administrator_login
  administrator_login_password = random_password.password.result

  sku_name                          = "B_Gen5_1"
  storage_mb                        = 5120
  version                           = "5.7"
  auto_grow_enabled                 = true
  backup_retention_days             = 7
  geo_redundant_backup_enabled      = false
  infrastructure_encryption_enabled = false
  public_network_access_enabled     = true
  ssl_enforcement_enabled           = true
  ssl_minimal_tls_version_enforced  = "TLS1_2"
}

resource "azurerm_mysql_database" "database" {
  name                = "mysqldb-${var.application_name}-001"
  resource_group_name = azurerm_resource_group.example.name
  server_name         = azurerm_mysql_server.database.name
  charset             = "utf8"
  collation           = "utf8_unicode_ci"
}

# This rule is to enable the 'Allow access to Azure services' checkbox
resource "azurerm_mysql_firewall_rule" "database" {
  name                = "mysqlfw-${var.application_name}-001"
  resource_group_name = azurerm_resource_group.example.name
  server_name         = azurerm_mysql_server.database.name
  start_ip_address    = "0.0.0.0"
  end_ip_address      = "0.0.0.0"
}

resource "azurerm_spring_cloud_service" "example" {
  name                = "example-springcloud"
  resource_group_name = azurerm_resource_group.example.name
  location            = azurerm_resource_group.example.location
}

resource "azurerm_spring_cloud_app" "example" {
  name                = "example-springcloudapp"
  resource_group_name = azurerm_resource_group.example.name
  service_name        = azurerm_spring_cloud_service.example.name
  is_public           = true
  https_only          = true
}

resource "azurerm_spring_cloud_java_deployment" "example" {
  name                = "default"
  spring_cloud_app_id = azurerm_spring_cloud_app.example.id
  cpu                 = 2
  memory_in_gb        = 4
  instance_count      = 2
  jvm_options         = "-XX:+PrintGC"
  runtime_version     = "Java_11"

  environment_variables = {
    "spring.datasource.url" : "jdbc:mysql://${azurerm_mysql_server.database.fqdn}:3306/${azurerm_mysql_database.database.name}?useSSL=true&requireSSL=false&useLegacyDatetimeCode=false&serverTimezone=UTC"
    "spring.datasource.username" : "${var.administrator_login}@${azurerm_mysql_server.database.name}"
    "spring.datasource.password" : random_password.password.result
    "spring.jpa.properties.hibernate.dialect" : "org.hibernate.dialect.MySQL5InnoDBDialect"
  }
}

resource "azurerm_spring_cloud_active_deployment" "example" {
  spring_cloud_app_id = azurerm_spring_cloud_app.example.id
  deployment_name     = azurerm_spring_cloud_java_deployment.example.name
}
```

---

## <a name="next-steps"></a>次のステップ

この記事では、Azure Spring Cloud 内のアプリケーションを Azure Database for MySQL インスタンスにバインドする方法について学習しました。 サービスをアプリケーションにバインドする方法の詳細については、「[Azure Cosmos DB データベースを Azure Spring Cloud アプリケーションにバインドする](./how-to-bind-cosmos.md)」を参照してください。
