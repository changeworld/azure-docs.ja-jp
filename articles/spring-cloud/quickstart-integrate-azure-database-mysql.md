---
title: クイックスタート - Azure Database for MySQL との統合
description: Azure Database for MySQL インスタンスをプロビジョニングして準備し、それを 1 つのコマンドのみで永続的なデータベースとして使用するように Azure Spring Cloud で Pet Clinic を構成する方法について説明します。
author: karlerickson
ms.author: karler
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/15/2021
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 9f9043b7ff667903ed9d2318e4ec20363cae71f9
ms.sourcegitcommit: 4abfec23f50a164ab4dd9db446eb778b61e22578
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2021
ms.locfileid: "130063661"
---
# <a name="quickstart-integrate-azure-spring-cloud-with-azure-database-for-mysql"></a>クイック スタート: Azure Spring Cloud と Azure Database for MySQL との統合

既定の構成 「[クイック スタート: アプリをビルドして Azure Spring Cloud にデプロイする](quickstart-deploy-apps.md)」でデプロイされた Pet Clinic では、起動時にデータが入力されるメモリ内データベース (HSQLDB) が使用されます。 このクイックスタートでは、Azure Database for MySQL インスタンスをプロビジョニングして準備し、それを 1 つのコマンドのみで永続的なデータベースとして使用するように Azure Spring Cloud で Pet Clinic を構成する方法について説明します。

## <a name="variables-preparation"></a>変数の準備

次の値を使用します。 エラーを回避するために、これらをテキスト ファイルまたは環境変数に保存します。 パスワードは長さが 8 文字以上で、英大文字、英小文字、数字、英数字以外の文字 (!、$、#、% など) を 1 つ以上含んでいる必要があります。

```bash
export RESOURCE_GROUP=<resource-group-name> # customize this
export MYSQL_SERVER_NAME=<mysql-server-name> # customize this
export MYSQL_SERVER_FULL_NAME=${MYSQL_SERVER_NAME}.mysql.database.azure.com
export MYSQL_SERVER_ADMIN_NAME=<admin-name> # customize this
export MYSQL_SERVER_ADMIN_LOGIN_NAME=${MYSQL_SERVER_ADMIN_NAME}\@${MYSQL_SERVER_NAME}
export MYSQL_SERVER_ADMIN_PASSWORD=<password> # customize this
export MYSQL_DATABASE_NAME=petclinic
```

## <a name="prepare-an-azure-database-for-mysql-instance"></a>Azure Database for MySQL インスタンスを準備する

1. 前のクイックスタートで次のコマンドを実行していない場合は、CLI の既定値を設定します。
    ```azcli
    az configure --defaults group=<resource group name> spring-cloud=<service name>
    ```
1. Azure Database for MySQL サーバーを作成します。 

    ```azcli
    az mysql server create --resource-group ${RESOURCE_GROUP} \
        --name ${MYSQL_SERVER_NAME} \
        --admin-user ${MYSQL_SERVER_ADMIN_NAME} \
        --admin-password ${MYSQL_SERVER_ADMIN_PASSWORD} \
        --sku-name GP_Gen5_2 \
        --ssl-enforcement Disabled \
        --version 5.7
    ```

1. Azure リソースからのアクセスを許可します。

    ```azcli
    az mysql server firewall-rule create --name allAzureIPs \
     --server ${MYSQL_SERVER_NAME} \
     --resource-group ${RESOURCE_GROUP} \
     --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
    ```

1. テスト用の開発マシンからのアクセスを許可します。

    ```azcli
    az mysql server firewall-rule create --name devMachine \
     --server ${MYSQL_SERVER_NAME} \
     --resource-group ${RESOURCE_GROUP} \
     --start-ip-address <ip-address-of-your-dev-machine> \
     --end-ip-address <ip-address-of-your-dev-machine>
    ```

1. 接続タイムアウトを増やします。

    ```azcli
    az mysql server configuration set --name wait_timeout \
     --resource-group ${RESOURCE_GROUP} \
     --server ${MYSQL_SERVER_NAME} --value 2147483
    ```

1. MySQL サーバーにデータベースを作成し、対応する設定を指定します。

    ```sql
    // SUBSTITUTE values
    mysql -u ${MYSQL_SERVER_ADMIN_LOGIN_NAME} \
     -h ${MYSQL_SERVER_FULL_NAME} -P 3306 -p

    Enter password:
    Welcome to the MySQL monitor.  Commands end with ; or \g.
    Your MySQL connection id is 64379
    Server version: 5.6.39.0 MySQL Community Server (GPL)

    Copyright (c) 2000, 2018, Oracle and/or its affiliates. All rights reserved.

    Oracle is a registered trademark of Oracle Corporation and/or its
    affiliates. Other names may be trademarks of their respective
    owners.

    Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

    mysql> CREATE DATABASE petclinic;
    Query OK, 1 row affected (0.10 sec)

    mysql> CREATE USER 'root' IDENTIFIED BY 'petclinic';
    Query OK, 0 rows affected (0.11 sec)

    mysql> GRANT ALL PRIVILEGES ON petclinic.* TO 'root';
    Query OK, 0 rows affected (1.29 sec)

    mysql> CALL mysql.az_load_timezone();
    Query OK, 3179 rows affected, 1 warning (6.34 sec)

    mysql> SELECT name FROM mysql.time_zone_name;
    ...

    mysql> quit
    Bye
    ```

1. タイムゾーンを設定します。

    ```azcli
    az mysql server configuration set --name time_zone \
     --resource-group ${RESOURCE_GROUP} \
     --server ${MYSQL_SERVER_NAME} --value "US/Pacific"
    ```

## <a name="update-apps-to-use-mysql-database"></a>MySQL データベースを使用するようにアプリを更新する

サンプル アプリのデータベースとして MySQL を有効にするには、アクティブなプロファイル MySQL とデータベース資格情報を環境変数として使用して *customer-service* アプリを更新するだけです。

```azcli
az spring-cloud app update --name customers-service \
    --jvm-options="-Xms2048m -Xmx2048m -Dspring.profiles.active=mysql" \
    --env MYSQL_SERVER_FULL_NAME=${MYSQL_SERVER_FULL_NAME} \
        MYSQL_DATABASE_NAME=${MYSQL_DATABASE_NAME} \
        MYSQL_SERVER_ADMIN_LOGIN_NAME=${MYSQL_SERVER_ADMIN_LOGIN_NAME} \
        MYSQL_SERVER_ADMIN_PASSWORD=${MYSQL_SERVER_ADMIN_PASSWORD}
```

## <a name="update-extra-apps"></a>追加のアプリを更新する

```azcli
az spring-cloud app update --name api-gateway \
    --jvm-options="-Xms2048m -Xmx2048m -Dspring.profiles.active=mysql"
az spring-cloud app update --name admin-server \
    --jvm-options="-Xms2048m -Xmx2048m -Dspring.profiles.active=mysql"
az spring-cloud app update --name customers-service \
    --jvm-options="-Xms2048m -Xmx2048m -Dspring.profiles.active=mysql" \
    --env MYSQL_SERVER_FULL_NAME=${MYSQL_SERVER_FULL_NAME} \
        MYSQL_DATABASE_NAME=${MYSQL_DATABASE_NAME} \
        MYSQL_SERVER_ADMIN_LOGIN_NAME=${MYSQL_SERVER_ADMIN_LOGIN_NAME} \
        MYSQL_SERVER_ADMIN_PASSWORD=${MYSQL_SERVER_ADMIN_PASSWORD}
az spring-cloud app update --name vets-service \
    --jvm-options="-Xms2048m -Xmx2048m -Dspring.profiles.active=mysql" \
    --env MYSQL_SERVER_FULL_NAME=${MYSQL_SERVER_FULL_NAME} \
        MYSQL_DATABASE_NAME=${MYSQL_DATABASE_NAME} \
        MYSQL_SERVER_ADMIN_LOGIN_NAME=${MYSQL_SERVER_ADMIN_LOGIN_NAME} \
        MYSQL_SERVER_ADMIN_PASSWORD=${MYSQL_SERVER_ADMIN_PASSWORD}
az spring-cloud app update --name visits-service \
    --jvm-options="-Xms2048m -Xmx2048m -Dspring.profiles.active=mysql" \
    --env MYSQL_SERVER_FULL_NAME=${MYSQL_SERVER_FULL_NAME} \
        MYSQL_DATABASE_NAME=${MYSQL_DATABASE_NAME} \
        MYSQL_SERVER_ADMIN_LOGIN_NAME=${MYSQL_SERVER_ADMIN_LOGIN_NAME} \
        MYSQL_SERVER_ADMIN_PASSWORD=${MYSQL_SERVER_ADMIN_PASSWORD}
```

## <a name="next-steps"></a>次のステップ

* [Azure Database for MySQL インスタンスを Azure Spring Cloud 内のアプリケーションにバインドする](how-to-bind-mysql.md)
* [マネージド ID を使用して Azure SQL Database を Azure Spring Cloud 内のアプリに接続する](./connect-managed-identity-to-azure-sql.md)
