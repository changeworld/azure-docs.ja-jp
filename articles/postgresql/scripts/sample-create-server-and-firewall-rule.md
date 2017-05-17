---
title: "Azure CLI スクリプト - Azure Database for PostgreSQL の作成 | Microsoft Docs"
description: "Azure CLI スクリプト サンプル - Azure Database for PostgreSQL サーバーを作成して、サーバー レベルのファイアウォール規則を構成します。"
services: postgresql
author: salonisonpal
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
ms.openlocfilehash: 0f61358aa655a44ac7982c80eb1d796667c47bf4
ms.contentlocale: ja-jp
ms.lasthandoff: 05/10/2017

---

# <a name="create-an-azure-database-for-postgresql-server-and-configure-a-firewall-rule-using-the-azure-cli"></a>Azure CLI を使った Azure Database for PostgreSQL サーバーの作成とファイアウォール規則の構成
このサンプル CLI スクリプトは、Azure Database for PostgreSQL サーバーを作成して、サーバー レベルのファイアウォール規則を構成するものです。 スクリプトが正常に実行されると、すべての Azure サービスと構成済み IP アドレスから PostgreSQL サーバーにアクセスできるようになります。

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>サンプル スクリプト
```azurecli
#!/bin/bash

# Create a resource group
az group create \
--name myresourcegroup \
--location westus

# Create a PostgreSQL server in the resource group
# Name of a server maps to DNS name and is thus required to be globally unique in Azure.
# Substitute the <server_admin_password> with your own value.
az postgres server create \
--name mypgserver-20170401 \
--resource-group myresourcegroup \
--location westus \
--admin-user mylogin \
--admin-password <server_admin_password> \
--performance-tier Basic \
--compute-units 50 \

# Configure a firewall rule for the server
# The ip address range that you want to allow to access your server
az postgres server firewall-rule create \
--resource-group myresourcegroup \
--server mypgserver-20170401 \
--name AllowIps \
--start-ip-address 0.0.0.0 \
--end-ip-address 255.255.255.255

# Default database ‘postgres’ gets created on the server.
```

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ
スクリプト サンプルの実行後は、次のコマンドを使用してリソース グループとすべての関連リソースを削除することができます。
```azurecli
az group delete --name myresourcegroup
```

## <a name="next-steps"></a>次のステップ
- Azure CLI の詳細については、[Azure CLI のドキュメント](https://docs.microsoft.com/cli/azure/overview)のページをご覧ください。
- Azure Database for PostgreSQL に関する CLI スクリプト サンプルは、[Azure Database for PostgreSQL のドキュメント](../sample-scripts-azure-cli.md)にも用意されています。

