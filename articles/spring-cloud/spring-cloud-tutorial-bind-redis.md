---
title: チュートリアル - Azure Cache for Redis を Azure Spring Cloud アプリケーションにバインドする方法
description: このチュートリアルでは、Azure Cache for Redis を Azure Spring Cloud アプリケーションにバインドする方法について説明します。
author: jpconnock
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 10/31/2019
ms.author: jeconnoc
ms.openlocfilehash: 1653db3619fd569238872ca1fcfd6d0c439e84c9
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/03/2019
ms.locfileid: "74708781"
---
# <a name="tutorial-bind-azure-services-to-your-azure-spring-cloud-application-azure-cache-for-redis"></a>チュートリアル:Azure サービスを Azure Spring Cloud アプリケーションにバインドする: Azure Cache for Redis

Azure Spring Cloud では、Spring Boot アプリケーションを手動で構成するのではなく、選択した Azure サービスをアプリケーションに自動的にバインドすることができます。 この記事では、アプリケーションを Azure Cache for Redis にバインドする方法を示します。

## <a name="prerequisites"></a>前提条件

* デプロイ済みの Azure Spring Cloud インスタンス
* Azure Cache for Redis サービス インスタンス
* Azure CLI 用の Azure Spring Cloud 拡張機能

Azure Spring Cloud インスタンスをデプロイしていない場合は、この[クイックスタート](spring-cloud-quickstart-launch-app-portal.md)の手順に従って最初の Spring Cloud アプリをデプロイします。

## <a name="bind-azure-cache-for-redis"></a>Azure Cache for Redis をバインドする

1. 次の依存関係を自分のプロジェクトの `pom.xml` に追加します

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-redis-reactive</artifactId>
    </dependency>
    ```
1. もしあれば、`application.properties` ファイルで `spring.redis.*` プロパティを削除します

1. `az spring-cloud app update` を使用して現在のデプロイを更新するか、`az spring-cloud app deployment create` を使用して新しいデプロイを作成します。

1. Azure portal で、自分のAzure Spring Cloud サービスのページに移動します。 **アプリケーション ダッシュボード**を探し、Azure Cache for Redis にバインドするアプリケーションを選択します。  これは、前の手順で更新またはデプロイしたのと同じアプリケーションです。 次に、[`Service binding`]\(サービスのバインド\) を選択し、[`Create service binding`]\(サービス バインドの作成\) ボタンを選択します。 **バインドの種類** `Azure Cache for Redis`、自分の Redis サーバー、主キーのオプションを必ず選択するようにして、フォームに入力します。 

1. アプリを再起動すると、このバインドが機能するようになります。

1. サービスのバインドが正常であることを確認するには、バインド名を選択し、その詳細を確認します。 `property` フィールドはこのようになります。
    ```
    spring.redis.host=some-redis.redis.cache.windows.net
    spring.redis.port=6380
    spring.redis.password=abc******
    spring.redis.ssl=true
    ```

## <a name="next-steps"></a>次の手順

このチュートリアルでは、Azure Spring Cloud アプリケーションを Azure Redis キャッシュにバインドする方法について学習しました。  アプリケーションへのバインド サービスについて詳しく学習するには、MySQL DB へのアプリケーションのバインドに関するチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [Azure MySql サービスを Azure Spring Cloud サービスにバインドする方法について学習する](spring-cloud-tutorial-bind-mysql.md)。