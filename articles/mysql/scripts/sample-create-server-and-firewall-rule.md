---
title: "単一の Azure Database for MySQL サーバーを作成してファイアウォール規則を構成する Azure CLI サンプル | Microsoft Docs"
description: "このサンプル CLI スクリプトは、Azure Database for MySQL サーバーを作成して、サーバー レベルのファイアウォール規則を構成するものです。"
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonh
ms.assetid: 
ms.service: mysql-database
ms.tgt_pltfrm: portal
ms.devlang: azurecli
ms.topic: article
ms.custom: sample
ms.date: 05/10/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: fa961f123dddc2e6243d1efbd1d72b994179eb79
ms.contentlocale: ja-jp
ms.lasthandoff: 05/10/2017

---

# <a name="create-a-mysql-server-and-configure-a-firewall-rule-using-the-azure-cli"></a>Azure CLI での MySQL サーバーの作成とファイアウォール規則の構成
このサンプル CLI スクリプトは、Azure Database for MySQL サーバーを作成して、サーバー レベルのファイアウォール規則を構成するものです。 スクリプトが正常に実行されると、すべての Azure サービスと構成済みの IP アドレスから MySQL サーバーにアクセスできるようになります。

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>サンプル スクリプト
```bash
#!/bin/bash

# Create a resource group
az group create \
--name myresource \
--location westus

# Create a MySQL server in the resource group
# Name of a server maps to DNS name and is thus required to be globally unique in Azure.
# Substitute the <server_admin_password> with your own value.
az mysql server create \
--name mysqlserver4demo \
--resource-group myresource \
--location westus \
--admin-user myadmin \
--admin-password <server_admin_password> \
--performance-tier Basic \
--compute-units 50 \

# Configure a firewall rule for the server
# The ip address range that you want to allow to access your server
az mysql server firewall-rule create \
--resource-group myresource \
--server mysqlserver4demo \
--name AllowIps \
--start-ip-address 0.0.0.0 \
--end-ip-address 255.255.255.255

# Default database ‘postgres’ gets created on the server.
```

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ
 スクリプト サンプルの実行後は、次のコマンドを使用してリソース グループとすべての関連リソースを削除することができます。
```azurecli
az group delete --name myresource
```
## <a name="script-explanation"></a>スクリプトの説明
このスクリプトでは、次のコマンドを使用します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| **コマンド** | **メモ** |
|-------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------|
| [az group create](https://docs.microsoft.com/en-us/cli/azure/group#create) | すべてのリソースを格納するリソース グループを作成します。 |
| az mysql server create | データベースのホストとなる MySQL サーバーを作成します。 |
| az mysql server firewall create | 対象のサーバーとそこにあるデータベースに対し、入力した IP アドレス範囲からアクセスできるようにするファイアウォール規則を作成します。 |
| [az group delete](https://docs.microsoft.com/en-us/cli/azure/resource#delete) | 入れ子になったリソースすべてを含むリソース グループを削除します。 |

## <a name="next-steps"></a>次のステップ
[Azure Database for MySQL 用 Azure CLI サンプル](../sample-scripts-azure-cli.md)

