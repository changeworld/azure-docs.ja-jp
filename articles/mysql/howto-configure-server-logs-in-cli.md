---
title: "Azure CLI を使用した Azure Database for MySQL サーバー ログへのアクセス | Microsoft Docs"
description: "この記事では、Azure CLI コマンド ライン ユーティリティを使用して Azure Database for MySQL のサーバー ログにアクセスする方法について説明します。"
services: mysql
author: rachel-msft
ms.author: raagyema
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.devlang: azure-cli
ms.topic: article
ms.date: 10/18/2017
ms.openlocfilehash: 6ee2c2c6e6cff824d3167ea600a1ddc778ad011b
ms.sourcegitcommit: 2d1153d625a7318d7b12a6493f5a2122a16052e0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2017
---
# <a name="configure-and-access-server-logs-using-azure-cli"></a>Azure CLI を使用した PostgreSQL のサーバー ログの構成とアクセス
Azure CLI (Azure のコマンドライン ユーティリティ) を使用して Azure Database for MySQL のサーバー ログをダウンロードできます。

## <a name="prerequisites"></a>前提条件
このハウツー ガイドの手順を実行するには、以下が必要です。
- [Azure Database for MySQL サーバー](quickstart-create-mysql-server-database-using-azure-cli.md)
- [Azure CLI 2.0](/cli/azure/install-azure-cli)、またはブラウザーで Azure Cloud Shell を使用する。

## <a name="configure-logging-for-azure-database-for-mysql"></a>Azure Database for MySQL のログ記録の構成
MySQL 低速クエリ ログにアクセスするサーバーを構成できます。
1. **slow\_query\_log** パラメーターをオンに設定してログ記録を有効にします。
2. **long\_query\_time** や **log\_slow\_admin\_statements** などのパラメーターを調整します。

これらのパラメーターの値を Azure CLI で設定する方法について詳しくは、[サーバーのパラメーターを構成する方法](howto-configure-server-parameters-using-cli.md)に関する記事をご覧ください。

たとえば、次の CLI コマンドは低速クエリ ログをオンにし、長時間クエリを 10 秒に設定して、低速管理ステートメントのログ記録をオフにします。 最後に、確認のために構成のオプションが一覧表示されます。
```azurecli-interactive
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server myserver4demo --value ON
az mysql server configuration set --name long_query_time --resource-group myresourcegroup --server myserver4demo --value 10
az mysql server configuration set --name log_slow_admin_statements --resource-group myresourcegroup --server myserver4demo --value OFF
az mysql server configuration list --resource-group myresourcegroup --server myserver4demo
```

## <a name="list-logs-for-azure-database-for-mysql-server"></a>Azure Database for MySQL サーバーのログの一覧表示
サーバーの利用可能なログ ファイルを一覧表示するには、[az mysql server-logs list](/cli/azure/mysql/server-logs#list) コマンドを実行します。

リソース グループ **myresourcegroup** のサーバー **myserver4demo.mysql.database.azure.com** のログ ファイルを一覧表示し、それを **log\_files\_list.txt** と呼ばれるテキスト ファイルに出力できます。
```azurecli-interactive
az mysql server-logs list --resource-group myresourcegroup --server myserver4demo > log_files_list.txt
```
## <a name="download-logs-from-the-server"></a>サーバーからログをダウンロードする
[az mysql server-logs download](/cli/azure/mysql/server-logs#download) コマンドを使用すると、サーバーの個別のログ ファイルをダウンロードできます。 

この例では、リソース グループ **myresourcegroup** のサーバー **myserver4demo.mysql.database.azure.com** の特定のログ ファイルをローカル環境にダウンロードします。
```azurecli-interactive
az mysql server-logs download --name 20170414-myserver4demo-mysql.log --resource-group myresourcegroup --server myserver4demo
```

## <a name="next-steps"></a>次のステップ
- [Azure Database for MySQL のサーバー ログ](concepts-server-logs.md)について学習する
