---
title: "Azure CLI スクリプト - Azure Database for PostgreSQL のスケーリング | Microsoft Docs"
description: "Azure CLI スクリプト サンプル - Azure Database for PostgreSQL サーバーのメトリックを照会したうえで、異なるパフォーマンス レベルにスケーリングします。"
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
ms.date: 04/30/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 36134ca1a899c25ab896577815ce9108cae0d563
ms.contentlocale: ja-jp
ms.lasthandoff: 05/10/2017

---
# <a name="monitor-and-scale-a-single-postgresql-server-using-azure-cli"></a>Azure CLI での単一の PostgreSQL サーバーの監視とスケーリング
このサンプル CLI スクリプトは、単一の Azure Database for PostgreSQL サーバーのメトリックを照会したうえで、異なるパフォーマンス レベルにスケーリングするものです。 

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

# Monitor usage metrics - Compute
az monitor metrics list \
--resource-id "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforPostgreSQL/servers/mypgserver-20170401" \
--metric-names compute_consumption_percent \
--time-grain PT1M

# Monitor usage metrics - Storage
az monitor metrics list \
--resource-id "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforPostgreSQL/servers/mypgserver-20170401" \
--metric-names storage_used \
--time-grain PT1M

# Scale up the server to provision more Compute Units within the same Tier
az postgres server update \
--resource-group myresourcegroup \
--name mypgserver-20170401 \
--compute-units 100
```

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ
スクリプト サンプルの実行後は、次のコマンドを使用してリソース グループとすべての関連リソースを削除することができます。
```azurecli
az group delete --name myresourcegroup
```

## <a name="next-steps"></a>次のステップ
- Azure CLI の詳細については、[Azure CLI のドキュメント](https://docs.microsoft.com/cli/azure/overview)のページをご覧ください。
- Azure Database for PostgreSQL に関する CLI スクリプト サンプルは、[Azure Database for PostgreSQL のドキュメント](../sample-scripts-azure-cli.md)にも用意されています。
- スケーリングの詳細については、[サービス プラン](../concepts-service-tiers.md)に関するページおよび[コンピューティング ユニットとストレージ ユニット](../concepts-compute-unit-and-storage.md)に関するページを参照してください。
