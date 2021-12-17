---
title: Azure Cache for Redis を Azure Spring Cloud のアプリケーションにバインドする
description: Azure Cache for Redis を Azure Spring Cloud のアプリケーションにバインドする方法について説明します
author: karlerickson
ms.service: spring-cloud
ms.topic: how-to
ms.date: 10/31/2019
ms.author: karler
ms.custom: devx-track-java
ms.openlocfilehash: 9f357526b64b34d7348114ce71840a5f4aafc394
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2021
ms.locfileid: "132492502"
---
# <a name="bind-azure-cache-for-redis-to-your-application-in-azure-spring-cloud"></a>Azure Cache for Redis を Azure Spring Cloud のアプリケーションにバインドする

**この記事の適用対象:** ✔️ Java

Spring Boot アプリケーションを手動で構成するのではなく、Azure Spring Cloud を使用して、選択した Azure サービスをアプリケーションに自動的にバインドすることができます。 この記事では、アプリケーションを Azure Cache for Redis にバインドする方法について説明します。

## <a name="prerequisites"></a>前提条件

* デプロイ済みの Azure Spring Cloud インスタンス
* Azure Cache for Redis サービス インスタンス
* Azure CLI 用の Azure Spring Cloud 拡張機能

Azure Spring Cloud インスタンスをデプロイしていない場合は、[Azure Spring Cloud アプリのデプロイに関するクイックスタート](./quickstart.md)の手順を実行してください。

## <a name="prepare-your-java-project"></a>Java プロジェクトを準備する

1. プロジェクトの pom.xml ファイルに次の依存関係を追加します。

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-redis-reactive</artifactId>
    </dependency>
    ```

1. `application.properties` ファイルからすべての `spring.redis.*` プロパティを削除します

1. `az spring-cloud app update` を使用して現在のデプロイを更新するか、`az spring-cloud app deployment create` を使用して新しいデプロイを作成します。

## <a name="bind-your-app-to-the-azure-cache-for-redis"></a>アプリを Azure Cache for Redis にバインドする

#### <a name="service-binding"></a>[サービス バインド](#tab/Service-Binding)
1. Azure portal で、自分のAzure Spring Cloud サービスのページに移動します。 **アプリケーション ダッシュボード** に移動し、Azure Cache for Redis にバインドするアプリケーションを選択します。 このアプリケーションは、前の手順で更新またはデプロイしたものと同じです。

1. **[サービス バインド]** を選択し、 **[サービス バインドの作成]** を選択します。 フォームに入力します。このとき、必ず **[バインドの種類]** 値に **[Azure Cache for Redis]** 、実際の Azure Cache for Redis サーバー、 **[主]** キー オプションを選択します。

1. アプリを再起動します。 これでバインドが機能するようになります。

1. サービスのバインドが正常であることを確認するには、バインド名を選択し、その詳細を確認します。 `property` フィールドはこのようになります。

    ```properties
    spring.redis.host=some-redis.redis.cache.windows.net
    spring.redis.port=6380
    spring.redis.password=abc******
    spring.redis.ssl=true
    ```

#### <a name="terraform"></a>[Terraform](#tab/Terraform)

次の Terraform スクリプトは、Azure Cache for Redis を使用して Azure Spring Cloud アプリを設定する方法を示しています。

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

resource "azurerm_redis_cache" "redis" {
  name                = "redis-${var.application_name}-001"
  resource_group_name = azurerm_resource_group.example.name
  location            = azurerm_resource_group.example.location
  capacity            = 0
  family              = "C"
  sku_name            = "Standard"
  enable_non_ssl_port = false
  minimum_tls_version = "1.2"
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
    "spring.redis.host"     = azurerm_redis_cache.redis.hostname
    "spring.redis.password" = azurerm_redis_cache.redis.primary_access_key
    "spring.redis.port"     = "6380"
    "spring.redis.ssl"      = "true"
  }
}

resource "azurerm_spring_cloud_active_deployment" "example" {
  spring_cloud_app_id = azurerm_spring_cloud_app.example.id
  deployment_name     = azurerm_spring_cloud_java_deployment.example.name
}
```

---

## <a name="next-steps"></a>次のステップ

この記事では、Azure Spring Cloud のアプリケーションを Azure Cache for Redis にバインドする方法を学習しました。 サービスをアプリケーションにバインドする方法については、[Azure Database for MySQL インスタンスへのバインド](./how-to-bind-mysql.md)に関するページを参照してください。
