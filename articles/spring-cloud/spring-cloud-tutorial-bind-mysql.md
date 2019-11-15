---
title: Azure Database for MySQL を Azure Spring Cloud アプリケーションにバインドする方法 | Microsoft Docs
description: この記事では、Azure MySQL を Azure Spring Cloud アプリケーションにバインドする方法について説明します
author: jpconnock
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 11/04/2019
ms.author: jeconnoc
ms.openlocfilehash: b6de5bb3b25c111d1b7775ea9570a4ae2cf45042
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2019
ms.locfileid: "73607590"
---
# <a name="tutorial-bind-azure-services-to-your-azure-spring-cloud-application-azure-database-for-mysql"></a>チュートリアル:Azure サービスを Azure Spring Cloud アプリケーションにバインドする: Azure Database for MySQL

Azure Spring Cloud では、Spring Boot アプリケーションを手動で構成するのではなく、選択した Azure サービスをアプリケーションに自動的にバインドすることができます。 このチュートリアルでは、アプリケーションを Azure MySQL にバインドする方法について説明します。

## <a name="prerequisites"></a>前提条件

* デプロイ済みの Azure Spring Cloud インスタンス
* Azure Database for MySQL アカウント
* Azure CLI

Azure Spring Cloud インスタンスをデプロイしていない場合は、この[クイックスタート](spring-cloud-quickstart-launch-app-portal.md)の手順に従って最初の Spring Cloud アプリをデプロイします。

## <a name="bind-azure-database-for-mysql"></a>Azure Database for MySQL をバインドする

1. 自分の Azure MySQL アカウントの管理者ユーザー名とパスワードをメモしておきます。 MySQL クライアントからサーバーに接続し、`testdb` という名前のデータベースを作成します。 新しい非管理者アカウントを作成します。

1. 次の依存関係を自分のプロジェクトの `pom.xml` に追加します

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-jpa</artifactId>
    </dependency>
    ```
1. もしあれば、`application.properties` ファイルで `spring.datasource.*` プロパティを削除します。

1. `az spring-cloud app update` を使用して現在のデプロイを更新するか、`az spring-cloud app deployment create` を使用してこの変更用に新しいデプロイを作成します。  これらのコマンドでは、新しい依存関係でアプリケーションが更新または作成されます。

1. Azure portal で、自分のAzure Spring Cloud サービスのページに移動します。 **アプリケーション ダッシュボード**を探し、Azure MySQL にバインドするアプリケーションを選択します。  これは、前の手順で更新またはデプロイしたのと同じアプリケーションです。 次に、[`Service binding`]\(サービス バインド\) を選択し、[`Create service binding`]\(サービス バインドの作成\) ボタンを選択します。 **バインドの種類** `Azure MySQL`、先ほど使用したのと同じデータベースの名前、最初の手順でメモしたのと同じユーザー名およびパスワードを必ず選択するようにして、フォームに入力します。

1. アプリを再起動すると、このバインドが機能するようになります。

1. サービスのバインドが正常であることを確認するには、バインド名を選択し、その詳細を確認します。 `property` フィールドはこのようになります。
    ```
    spring.datasource.url=jdbc:mysql://some-server.mysql.database.azure.com:3306/testdb?useSSL=true&requireSSL=false&useLegacyDatetimeCode=false&serverTimezone=UTC
    spring.datasource.username=admin@some-server
    spring.datasource.password=abc******
    spring.jpa.properties.hibernate.dialect=org.hibernate.dialect.MySQL5InnoDBDialect
    ```

## <a name="next-steps"></a>次の手順

このチュートリアルでは、MySQL DB への Azure Spring Cloud アプリケーションのバインドについて学習しました。  Azure Spring Cloud サービスの管理の詳細を学習する場合は、読み進めてサービスの検出と登録について確認してください。

> [!div class="nextstepaction"]
> [Spring Cloud Service Registry を使用してサービスの検出と登録を有効にする方法について学習する](spring-cloud-service-registration.md)。

