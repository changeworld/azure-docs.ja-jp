---
title: "Azure CLI を使用した PostgreSQL のサーバー ログの構成とアクセス | Microsoft Docs"
description: "Azure Database for PostgreSQL のサーバー ログの構成方法とアクセス方法について説明します。"
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
ms.openlocfilehash: dc047e6d5194035586931b3cacc4d5cd07f6cfc2
ms.contentlocale: ja-jp
ms.lasthandoff: 05/10/2017

---
# <a name="configure-and-access-server-logs-using-azure-cli"></a>Azure CLI を使用した PostgreSQL のサーバー ログの構成とアクセス
Azure PostgreSQL サーバーのエラー ログは、コマンド ライン インターフェイスを使用して表示およびダウンロードできます。 ただし、トランザクション ログへのアクセスはサポートされていません。 

## <a name="prerequisites"></a>前提条件
このハウツー ガイドの手順を実行するには、以下が必要です。
- [Azure Database for PostgreSQL サーバー](quickstart-create-server-database-azure-cli.md)
- [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) コマンド ライン ユーティリティのインストール

## <a name="configure-logging-for-azure-database-for-postgresql"></a>Azure Database for PostgreSQL のログ記録の構成
クエリ ログとエラー ログにアクセスするサーバーを構成できます。 エラー ログには、自動バキューム、接続、チェックポイントなどの情報を含めることができます。
1. ログ記録を有効にする
2. log\_statement と log\_min\_duration\_statement を更新し、クエリのログ記録を有効にする
3. 保持期間を更新する

詳細については、[サーバーの構成パラメーターのカスタマイズ](howto-configure-server-parameters-using-cli.md)に関するページをご覧ください。

## <a name="list-logs-for-azure-postgresql-server"></a>Azure PostgreSQL サーバーのログを一覧表示する
サーバーの利用可能なログ ファイルを一覧表示するには、**az postgres server-logs** コマンドを次の例のように実行します。
```azurecli
az postgres server-logs list --resource-group <resource group name> --server <server name> [ --file-last-written --filename-contains --max-file-size ]
```
たとえば、Azure PostgreSQL サーバー **mypgserver.postgres.database.azure.com** のリソース グループ **myresourcegroup** のログ ファイルを一覧表示し、それを **log\_files\_list.txt と呼ばれるテキスト ファイルに出力します。 **
```azurecli
az postgres server-logs list --resource-group **myresourcegroup** --server **mypgserver** > log\_files\_list.txt
```
## <a name="download-logs-locally-from-the-server"></a>サーバーからローカルにログをダウンロードする
Azure PostgreSQL サーバーの個別のログ ファイルをダウンロードすることもできます。 
```azurecli
az postgres server-logs download –name <log file name> --resource-group <resource group name> --server <server name>
```
この例では、Azure PostgreSQL サーバー **mypgserver.postgres.database.azure.com** のリソース グループ **myresourcegroup** の特定のログ ファイルをローカル環境にダウンロードします。
```azurecli
az postgres server-logs download --name 20170414-mypgserver-postgresql.log --resource-group **myresourcegroup** --server **mypgserver**
```
## <a name="next-steps"></a>次のステップ
- サーバー ログの詳細については、「[Azure Database for PostgreSQL のサーバー ログ](concepts-server-logs.md)」をご覧ください。
- サーバー パラメーターの詳細については、「[サーバー構成パラメーターのカスタマイズ](howto-configure-server-parameters-using-cli.md)」をご覧ください。
