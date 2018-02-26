---
title: "Azure CLI を使用した Azure Database for PostgreSQL ファイアウォール規則の作成と管理 | Microsoft Docs"
description: "この記事では、Azure CLI コマンド ラインを使って Azure Database for PostgreSQL ファイアウォール規則を作成し、管理する方法について説明します。"
services: postgresql
author: jasonwhowell
ms.author: jasonh
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.devlang: azure-cli
ms.topic: article
ms.date: 02/12/2018
ms.openlocfilehash: 4fbb0adabac3cefa0b889279eed9dfd03fe1b1f5
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/14/2018
---
# <a name="create-and-manage-azure-database-for-postgresql-firewall-rules-using-azure-cli"></a>Azure CLI を使用した Azure Database for PostgreSQL ファイアウォール規則の作成と管理
サーバーレベルのファイアウォール規則を使用すると、管理者は特定の IP アドレスまたは IP アドレス範囲からの Azure Database for PostgreSQL サーバーへのアクセスを管理できます。 便利な Azure CLI コマンドを使用すると、サーバーを管理するためのファイアウォール規則の作成、更新、削除、一覧化、表示などができます。 Azure Database for PostgreSQL ファイアウォール規則の概要については、「[Azure Database for PostgreSQL サーバーのファイアウォール規則](concepts-firewall-rules.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件
このハウツー ガイドの手順を実行するには、以下が必要です。
- [Azure CLI 2.0](/cli/azure/install-azure-cli) コマンド ライン ユーティリティをインストールするか、ブラウザーで Azure Cloud Shell を使用します。
- [Azure Database for PostgreSQL サーバーとデータベース](quickstart-create-server-database-azure-cli.md)。

## <a name="configure-firewall-rules-for-azure-database-for-postgresql"></a>Azure Database for PostgreSQL のファイアウォール規則の構成
ファイアウォール規則を構成するには、[az postgres server firewall-rule](/cli/azure/postgres/server/firewall-rule) コマンドを使用します。

## <a name="list-firewall-rules"></a>ファイアウォール規則の一覧表示 
既存のサーバー ファイアウォール規則の一覧を表示するには、[az postgres server firewall-rule list](/cli/azure/postgres/server/firewall-rule#az_postgres_server_firewall_rule_list) コマンドを実行します。
```azurecli-interactive
az postgres server firewall-rule list --resource-group myresourcegroup --server-name mydemoserver
```
既定では、JSON 形式でファイアウォール規則の一覧が出力されます (規則が存在する場合)。 出力をもっとわかりやすい表形式にする場合は、`--output table` スイッチを使用することもできます。
```azurecli-interactive
az postgres server firewall-rule list --resource-group myresourcegroup --server-name mydemoserver --output table
```
## <a name="create-firewall-rule"></a>ファイアウォール規則の作成
サーバーに新しいファイアウォール規則を作成するには、[az postgres server firewall-rule create](/cli/azure/postgres/server/firewall-rule#az_postgres_server_firewall_rule_create) コマンドを実行します。 

次の例では、`--start-ip-address` として 0.0.0.0、`--end-ip-address` として 255.255.255.255 の範囲を指定することで、すべての IP アドレスに **mydemoserver.postgres.database.azure.com** サーバーへのアクセスを許可しています。
```azurecli-interactive
az postgres server firewall-rule create --resource-group myresourcegroup --server-name mydemoserver --name AllowIpRange --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255
```
1 つの IP アドレスにのみアクセスを許可するには、次の例のように、`--start-ip-address` と `--end-ip-address` に同じアドレスを指定します。
```azurecli-interactive
az postgres server firewall-rule create --resource-group myresourcegroup --server-name mydemoserver --name AllowSingleIpAddress --start-ip-address 13.83.152.1 --end-ip-address 13.83.152.1
```
正常に完了すると、作成したファイアウォール規則の詳細が、既定では JSON 形式で、コマンドの出力として一覧表示されます。 失敗した場合は、代わりにエラー メッセージが出力されます。

## <a name="update-firewall-rule"></a>ファイアウォール規則の更新 
サーバー上の既存のファイアウォール規則を更新するには、[az postgres server firewall-rule update](/cli/azure/postgres/server/firewall-rule#az_postgres_server_firewall_rule_update) コマンドを使用します。 更新する既存のファイアウォール規則の名前、開始 IP、終了 IP 属性を入力します。
```azurecli-interactive
az postgres server firewall-rule update --resource-group myresourcegroup --server-name mydemoserver --name AllowIpRange --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.255
```
正常に完了すると、更新したファイアウォール規則の詳細が、既定では JSON 形式で、コマンドの出力として一覧表示されます。 失敗した場合は、代わりにエラー メッセージが出力されます。
> [!NOTE]
> ファイアウォール規則が存在しない場合は、更新コマンドによって新しい規則が作成されます。

## <a name="show-firewall-rule-details"></a>ファイアウォール規則の詳細の表示
[az postgres server firewall-rule show](/cli/azure/postgres/server/firewall-rule#az_postgres_server_firewall_rule_show) コマンドを実行することで、既存のサーバー レベルのファイアウォール規則の詳細を表示することもできます。
```azurecli-interactive
az postgres server firewall-rule show --resource-group myresourcegroup --server-name mydemoserver --name AllowIpRange
```
正常に完了すると、指定したファイアウォール規則の詳細が、既定では JSON 形式で、コマンドの出力として一覧表示されます。 失敗した場合は、代わりにエラー メッセージが出力されます。

## <a name="delete-firewall-rule"></a>ファイアウォール規則の削除
サーバーへの特定の IP 範囲のアクセスを取り消すには、[az postgres server firewall-rule delete](/cli/azure/postgres/server/firewall-rule#az_postgres_server_firewall_rule_delete) コマンドを実行して既存のファイアウォール規則を削除します。 既存のファイアウォール規則の名前を入力します。
```azurecli-interactive
az postgres server firewall-rule delete --resource-group myresourcegroup --server-name mydemoserver --name AllowIpRange
```
正常に完了すると、出力はありません。 失敗した場合は、エラー メッセージ テキストが返されます。

## <a name="next-steps"></a>次の手順
- 同様に、Web ブラウザー上で [Azure Portal を使用して Azure Database for PostgreSQL ファイアウォール規則の作成と管理](howto-manage-firewall-using-portal.md)を行うことができます。
- [Azure Database for PostgreSQL サーバーのファイアウォール規則](concepts-firewall-rules.md)について理解を深めます。
- Azure Database for PostgreSQL サーバーに接続する方法については、「[Azure Database for PostgreSQL の接続ライブラリ](concepts-connection-libraries.md)」をご覧ください。
