---
title: "Azure Database for MySQL でのサービスのパラメーターの構成"
description: "この記事では、Azure CLI コマンド ライン ユーティリティを使って Azure Database for MySQL のサービス パラメーターを構成する方法について説明します。"
services: mysql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: mysql-database
ms.devlang: azure-cli
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 9caf6b164f2433ab3c1b701554f562211cc2de80
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/28/2018
---
# <a name="customize-server-configuration-parameters-by-using-azure-cli"></a>Azure CLI を使用したサーバー構成パラメーターのカスタマイズ
Azure コマンド ライン ユーティリティ である Azure CLI を使用して、Azure Database for MySQL サーバーの構成パラメーターを一覧表示、表示、および更新できます。 エンジン構成のサブセットは、サーバー レベルで公開され、変更が可能です。 

## <a name="prerequisites"></a>前提条件
このハウツー ガイドの手順を実行するには、以下が必要です。
- [Azure Database for MySQL サーバー](quickstart-create-mysql-server-database-using-azure-cli.md)
- [Azure CLI 2.0](/cli/azure/install-azure-cli) コマンド ライン ユーティリティ。または、ブラウザーで Azure Cloud Shell を使用する

## <a name="list-server-configuration-parameters-for-azure-database-for-mysql-server"></a>Azure Database for MySQL サーバーのサーバー構成パラメーターを一覧表示する
サーバー内の変更可能なすべてのパラメーターとその値を一覧表示するには、[az mysql server configuration list](/cli/azure/mysql/server/configuration#az_mysql_server_configuration_list) コマンドを実行します。

リソース グループ **myresourcegroup** にあるサーバー **mydemoserver.mysql.database.azure.com** のサーバー構成パラメーターを一覧表示できます。
```azurecli-interactive
az mysql server configuration list --resource-group myresourcegroup --server mydemoserver
```
一覧表示されている各パラメーターの定義については、「MySQL reference」(MySQL リファレンス) の「[Server System Variables](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html)」(サーバー システム変数) セクションをご覧ください。

## <a name="show-server-configuration-parameter-details"></a>サーバー構成パラメーター詳細を表示する
サーバーの特定の構成パラメーターに関する詳細を表示するには、[az mysql server configuration show](/cli/azure/mysql/server/configuration#az_mysql_server_configuration_show) コマンドを実行します。

この例は、リソース グループ **myresourcegroup** にあるサーバー **mydemoserver.mysql.database.azure.com** の **slow\_query\_log** サーバー構成パラメーターの詳細を示します。
```azurecli-interactive
az mysql server configuration show --name slow_query_log --resource-group myresourcegroup --server mydemoserver
```
## <a name="modify-a-server-configuration-parameter-value"></a>サーバー構成パラメーターの値を変更する
特定のサーバー構成パラメーターの値を変更することもでき、MySQL サーバー エンジンの基盤となる構成値が更新されます。 構成を更新するには、[az mysql server configuration set](/cli/azure/mysql/server/configuration#az_mysql_server_configuration_set) コマンドを使用します。 

リソース グループ **myresourcegroup** にあるサーバー **mydemoserver.mysql.database.azure.com** の **slow\_query\_log** サーバー構成パラメーターを更新するには、次のコマンドを実行します。
```azurecli-interactive
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
```
構成パラメーターの値をリセットする際に、省略可能な `--value` パラメーターを指定しない場合、サービスでは既定値を適用します。 上記の例の場合、以下のようになります。
```azurecli-interactive
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver
```
このコードは、**slow\_query\_log** 構成を既定値 **OFF** にリセットします。 

## <a name="next-steps"></a>次の手順
- [Azure ポータルでサーバー パラメーター](howto-server-parameters.md)を構成する方法