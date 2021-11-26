---
title: Azure Cosmos DB を Azure Spring Cloud のアプリケーションにバインドする
description: Azure Cosmos DB を Azure Spring Cloud のアプリケーションにバインドする方法について説明します
author: karlerickson
ms.service: spring-cloud
ms.topic: how-to
ms.date: 10/06/2019
ms.author: karler
ms.custom: devx-track-java
ms.openlocfilehash: 6d9a5007ba4b5bf2c5138ab584e774d81f23bd03
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2021
ms.locfileid: "132493630"
---
# <a name="bind-an-azure-cosmos-db-database-to-your-application-in-azure-spring-cloud"></a>Azure Cosmos DB データベースを Azure Spring Cloud のアプリケーションにバインドする

**この記事の適用対象:** ✔️ Java

Spring Boot アプリケーションを手動で構成するのではなく、Azure Spring Cloud を使用して、選択した Azure サービスをアプリケーションに自動的にバインドすることができます。 この記事では、アプリケーションを Azure Cosmos DB データベースにバインドする方法を示します。

前提条件:

* デプロイされた Azure Spring Cloud インスタンス。 [Azure CLI を使用したデプロイに関するクイックスタート](./quickstart.md)に従って作業を開始してください。
* 共同作成者の最小アクセス許可レベルを持つ Azure Cosmos DB アカウント。

## <a name="prepare-your-java-project"></a>Java プロジェクトを準備する

1. 次の依存関係のいずれかをアプリケーションの pom.xml ファイルに追加します。 API の種類に適した依存関係を選択します。

    * API の種類: コア (SQL)

      ```xml
      <dependency>
          <groupId>com.azure.spring</groupId>
          <artifactId>azure-spring-boot-starter-cosmos</artifactId>
          <version>3.6.0</version>
      </dependency>
      ```

    * API の種類: MongoDB

      ```xml
      <dependency>
          <groupId>org.springframework.boot</groupId>
          <artifactId>spring-boot-starter-data-mongodb</artifactId>
      </dependency>
      ```

    * API の種類: Cassandra

      ```xml
      <dependency>
          <groupId>org.springframework.boot</groupId>
          <artifactId>spring-boot-starter-data-cassandra</artifactId>
      </dependency>
      ```

    * API の種類: Azure テーブル

      ```xml
      <dependency>
          <groupId>com.microsoft.azure</groupId>
          <artifactId>azure-storage-spring-boot-starter</artifactId>
          <version>2.0.5</version>
      </dependency>
      ```

1. `az spring-cloud app deploy` を実行して現在のアプリを更新するか、`az spring-cloud app deployment create` を実行してこの変更のための新しいデプロイを作成します。

## <a name="bind-your-app-to-the-azure-cosmos-db"></a>アプリを Azure Cosmos DB にバインドする

#### <a name="service-binding"></a>[サービス バインド](#tab/Service-Binding)
Azure Cosmos DB では、5 種類の API でバインドがサポートされています。 その使用方法を次の手順で説明します。

1. Azure Cosmos DB のデータベースを作成します。 詳細については、[データベースの作成](../cosmos-db/create-cosmosdb-resources-portal.md)に関するクイックスタートを参照してください。

1. データベースの名前を記録します。 この手順では、データベース名は **testdb** です。

1. Azure portal で、自分のAzure Spring Cloud サービスのページに移動します。 **アプリケーション ダッシュボード** に移動し、Azure Cosmos DB にバインドするアプリケーションを選択します。 このアプリケーションは、前の手順で更新またはデプロイしたものと同じです。

1. **[サービス バインド]** を選択し、 **[サービス バインドの作成]** を選択します。 フォームに入力するには、次のように選択します。
   * **[バインドの種類]** 値に **[Azure Cosmos DB]** 。
   * API の種類。
   * データベース名。
   * Azure Cosmos DB アカウント。

    > [!NOTE]
    > Cassandra を使用している場合は、データベース名にキー スペースを使用します。

1. アプリケーション ページで **[再起動]** を選択して、アプリケーションを再起動します。

1. サービスを確実に正しくバインドするには、バインド名を選択し、その詳細を確認します。 `property` フィールドは次の例のようになります。

    ```properties
    azure.cosmosdb.uri=https://<some account>.documents.azure.com:443
    azure.cosmosdb.key=abc******
    azure.cosmosdb.database=testdb
    ```

#### <a name="terraform"></a>[Terraform](#tab/Terraform)
次の Terraform スクリプトは、Azure Cosmos DB MongoDB API を使用して Azure Spring Cloud アプリを設定する方法を示しています。

```terraform
provider "azurerm" {
  features {}
}

variable "application_name" {
  type        = string
  description = "The name of your application"
  default     = "demo-abc"
}

resource "azurerm_resource_group" "example" {
  name     = "example-resources"
  location = "West Europe"
}

resource "azurerm_cosmosdb_account" "cosmosdb" {
  name                = "cosmosacct-${var.application_name}-001"
  resource_group_name = azurerm_resource_group.example.name
  location            = azurerm_resource_group.example.location
  offer_type          = "Standard"
  kind                = "MongoDB"

  consistency_policy {
    consistency_level = "Session"
  }

  geo_location {
    failover_priority = 0
    location          = azurerm_resource_group.example.location
  }
}

resource "azurerm_cosmosdb_mongo_database" "cosmosdb" {
  name                = "cosmos-${var.application_name}-001"
  resource_group_name = azurerm_cosmosdb_account.cosmosdb.resource_group_name
  account_name        = azurerm_cosmosdb_account.cosmosdb.name
}

resource "azurerm_spring_cloud_service" "example" {
  name                = "${var.application_name}"
  resource_group_name = azurerm_resource_group.example.name
  location            = azurerm_resource_group.example.location
}

resource "azurerm_spring_cloud_app" "example" {
  name                = "${var.application_name}-app"
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
    "azure.cosmosdb.uri" : azurerm_cosmosdb_account.cosmosdb.connection_strings[0]
    "azure.cosmosdb.database" : azurerm_cosmosdb_mongo_database.cosmosdb.name
  }
}

resource "azurerm_spring_cloud_active_deployment" "example" {
  spring_cloud_app_id = azurerm_spring_cloud_app.example.id
  deployment_name     = azurerm_spring_cloud_java_deployment.example.name
}
```

---

## <a name="next-steps"></a>次のステップ

この記事では、Azure Spring Cloud のアプリケーションを Azure Cosmos DB データベースにバインドする方法について説明しました。 サービスをアプリケーションにバインドする方法については、[Azure Cache for Redis キャッシュへのバインド](./how-to-bind-redis.md)に関するページを参照してください。
