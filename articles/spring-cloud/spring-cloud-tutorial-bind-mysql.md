---
title: チュートリアル - Azure Database for MySQL インスタンスを Azure Spring Cloud アプリケーションにバインドする方法
description: このチュートリアルでは、Azure Database for MySQL インスタンスを Azure Spring Cloud アプリケーションにバインドする方法について説明します
author: jpconnock
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 11/04/2019
ms.author: jeconnoc
ms.openlocfilehash: a9911798e42db55d5aaae90c933cfb64945b244c
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/03/2019
ms.locfileid: "74708824"
---
# <a name="tutorial-bind-an-azure-database-for-mysql-instance-to-your-azure-spring-cloud-application"></a>チュートリアル:Azure Database for MySQL インスタンスを Azure Spring Cloud アプリケーションにバインドする 

Azure Spring Cloud では、Spring Boot アプリケーションを手動で構成せずに、選択した Azure サービスをアプリケーションに自動的にバインドできます。 このチュートリアルでは、アプリケーションを Azure Database for MySQL インスタンスにバインドする方法について説明します。

## <a name="prerequisites"></a>前提条件

* デプロイ済みの Azure Spring Cloud インスタンス
* Azure Database for MySQL アカウント
* Azure CLI

デプロイ済みの Azure Spring Cloud インスタンスがない場合は、「[クイックスタート: Azure portal を使用して Azure Spring Cloud アプリケーションを起動する](spring-cloud-quickstart-launch-app-portal.md)」の説明に従って、最初の Spring Cloud アプリをデプロイしてください。

## <a name="bind-your-app-to-your-azure-database-for-mysql-instance"></a>アプリを Azure Database for MySQL インスタンスにバインドする

1. 自分の Azure Database for MySQL アカウントの管理者ユーザー名とパスワードをメモしておきます。 

1. サーバーに接続し、MySQL クライアントから **testdb** という名前のデータベースを作成し、新しい非管理者アカウントを作成します。

1. プロジェクトの *pom.xml* ファイルに、次の依存関係を追加します。

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-jpa</artifactId>
    </dependency>
    ```
1. *application.properties* ファイルから、`spring.datasource.*` プロパティをすべて削除します。

1. `az spring-cloud app update` を実行して現在のデプロイを更新するか、`az spring-cloud app deployment create` を実行してこの変更のための新しいデプロイを作成します。  これらのコマンドは、新しい依存関係でアプリケーションを更新または作成します。

1. Azure portal の **Azure Spring Cloud** サービスのページで**アプリケーション ダッシュボード**を探し、Azure Database for MySQL インスタンスにバインドするアプリケーションを選択します。  これは、前の手順で更新またはデプロイしたのと同じアプリケーションです。 

1. **[サービス バインド]** を選択し、 **[サービス バインドの作成]** ボタンを選択します。 

1. **[バインドの種類]** として **[Azure MySQL]** を選択し、前に使用したのと同じデータベース名を使用し、最初の手順でメモしたのと同じユーザー名とパスワードを使用して、フォームに入力します。

1. アプリを再起動すると、このバインドが機能するようになります。

1. サービス バインドが正しいことを確認するには、バインド名を選択し、その詳細を確認します。 `property` フィールドはこのようになります。
    ```
    spring.datasource.url=jdbc:mysql://some-server.mysql.database.azure.com:3306/testdb?useSSL=true&requireSSL=false&useLegacyDatetimeCode=false&serverTimezone=UTC
    spring.datasource.username=admin@some-server
    spring.datasource.password=abc******
    spring.jpa.properties.hibernate.dialect=org.hibernate.dialect.MySQL5InnoDBDialect
    ```

## <a name="next-steps"></a>次の手順

このチュートリアルでは、Azure Spring Cloud アプリケーションを Azure Database for MySQL インスタンスにバインドする方法について学習しました。  Azure Spring Cloud サービスの管理の詳細を確認するには、サービスの検出と登録に関する記事を参照してください。

> [!div class="nextstepaction"]
> [Spring Cloud Service Registry を使用してサービスの検出と登録を有効にする](spring-cloud-service-registration.md)
