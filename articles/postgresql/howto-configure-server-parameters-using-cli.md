---
title: "Azure Database for PostgreSQL でのサービスのパラメーターの構成 | Microsoft Docs"
description: "Azure Database for PostgreSQL でサービスのパラメーターを構成する方法について説明します。"
services: postgresql
author: SaloniSonpal
ms.author: salonis
manager: jhubbard
editor: jasonh
ms.assetid: 
ms.service: postgresql-database
ms.tgt_pltfrm: portal
ms.devlang: azurecli
ms.topic: article
ms.date: 05/10/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 7e46e3d588782ec0f2ef89c58001e997b8fc2910
ms.contentlocale: ja-jp
ms.lasthandoff: 05/10/2017

---
# <a name="customize-server-configuration-parameters-using-azure-cli"></a>Azure CLI を使用したサーバー構成パラメーターのカスタマイズ
コマンド ライン インターフェイス (Azure CLI) を使用して、Azure PostgreSQL サーバーの構成パラメーターを一覧表示、表示、および更新できます。 ただし、サーバーレベルで公開され、変更できるのは、エンジンの構成のサブセットのみです。 

## <a name="prerequisites"></a>前提条件
このハウツー ガイドの手順を実行するには、以下が必要です。
- [Azure Database for PostgreSQL](quickstart-create-server-database-azure-cli.md) を作成するサーバーとデータベース
- [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) コマンド ライン ユーティリティのインストール

## <a name="list-server-configuration-parameters-for-azure-postgresql-server"></a>Azure PostgreSQL サーバーのサーバー構成パラメーターを一覧表示する
Azure PostgreSQL サーバーのすべての変更可能なパラメーターの一覧およびその値を取得するには、**az postgres server configuration** コマンドを次のように実行します。
> az postgres server configuration list --resource-group <resource group name> --server <server name>

たとえば、Azure PostgreSQL サーバー **mypgserver.postgres.database.azure.com** の、リソース グループ **myresourcegroup** のサーバー構成パラメーターを一覧表示できます。
```azurecli
az postgres server configuration list --resource-group myresourcegroup --server mypgserver
```
## <a name="show-server-configuration-parameter-details"></a>サーバー構成パラメーター詳細を表示する
Azure PostgreSQL サーバーの特定の構成パラメーターに関する詳細を表示するには、**az postgres server configuration** コマンドを実行します。
```azurecli
az postgres server configuration show --name <configuration name>
   --resource-group <resource group name> --server <server name>
```
たとえば、Azure PostgreSQL サーバー **mypgserver.postgres.database.azure.com** のリソース グループ **myresourcegroup の **log\_min\_messages** サーバー構成パラメーターの詳細を表示できます。 **
```azurecli
az postgres server configuration show --name log\_min\_messages --resource-group myresourcegroup --server mypgserver
```
## <a name="modify-server-configuration-parameter-value"></a>サーバー構成パラメーターの値を変更する
Azure PostgreSQL サーバーの特定の構成パラメーターの値を変更することができ、これによって PostgreSQL サーバー エンジンの基盤となる構成値が更新されます。 構成値を更新するには、次の **az postgres server configuration** コマンドを実行します。 
```azurecli
az postgres server configuration update --name <configuration name>
   --resource-group <resource group name> --server <server name>[--value]
```
たとえば、Azure PostgreSQL サーバー **mypgserver.postgres.database.azure.com** のリソース グループ **myresourcegroup の **log\_min\_messages** サーバー構成パラメーターを更新できます。 **
```azurecli
az postgres server configuration show --name log\_min\_messages 
   --resource-group myresourcegroup --server mypgserver --value INFO
```
構成パラメーターの値をリセットする場合、省略可能な -- 値パラメーターを除外すると、既定値が適用されます。 上記の例の場合は、次のようになります。
```azurecli
az postgres server configuration show --name log\_min\_messages
   --resource-group myresourcegroup --server mypgserver
```
これによって、log\_min\_messages 構成が規定値の WARNING にリセットされます。 サーバーの構成と指定できる値の詳細については、[サーバー構成](https://www.postgresql.org/docs/9.6/static/runtime-config.html)に関する PostgreSQL のドキュメントを参照してください。

## <a name="next-steps"></a>次のステップ
- サーバー ログの構成およびアクセスについては、「[Server Logs in Azure Database for PostgreSQL (Azure Database for PostgreSQL のサーバー ログ)](concepts-server-logs.md)」を参照してください。
