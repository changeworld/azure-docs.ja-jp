---
title: 監査ログにアクセスする - Azure CLI - Azure Database for MySQL
description: この記事では、Azure CLI から Azure Database for MySQL の監査ログを構成し、それにアクセスする方法について説明します。
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.custom: devx-track-azurecli
ms.date: 6/24/2020
ms.openlocfilehash: 249fdc83e42384e4d989293078e842a8bb2a8583
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/30/2021
ms.locfileid: "122652590"
---
# <a name="configure-and-access-audit-logs-in-the-azure-cli"></a>Azure CLI での監査ログの構成とアクセス

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]

Azure CLI を使用して、[Azure Database for MySQL の監査ログ](concepts-audit-logs.md)を構成することができます。

## <a name="prerequisites"></a>前提条件

この攻略ガイドの手順を実行するには、以下が必要です。

- [Azure Database for MySQL サーバー](quickstart-create-mysql-server-database-using-azure-portal.md)が必要です。

[!INCLUDE[azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- この記事では、Azure CLI のバージョン 2.0 以降が必要です。 Azure Cloud Shell を使用している場合は、最新バージョンが既にインストールされています。

## <a name="configure-audit-logging"></a>監査ログを構成する

> [!IMPORTANT]
> サーバーのパフォーマンスに大きな影響を与えないように、監査のために必要なイベントの種類とユーザーのみをログに記録することをお勧めします。

次の手順に従って、監査ログを有効にし、構成します。

1. **audit_logs_enabled** パラメーターを "ON" に設定して、監査ログをオンにします。 
    ```azurecli-interactive
    az mysql server configuration set --name audit_log_enabled --resource-group myresourcegroup --server mydemoserver --value ON
    ```

2. **audit_log_events** パラメーターを更新して、ログに記録する [イベントの種類](concepts-audit-logs.md#configure-audit-logging)を選択します。
    ```azurecli-interactive
    az mysql server configuration set --name audit_log_events --resource-group myresourcegroup --server mydemoserver --value "ADMIN,CONNECTION"
    ```

3. **audit_log_exclude_users** パラメーターを更新して、ログから除外する MySQL ユーザーを追加します。 ユーザーは MySQL ユーザー名で指定します。
    ```azurecli-interactive
    az mysql server configuration set --name audit_log_exclude_users --resource-group myresourcegroup --server mydemoserver --value "azure_superuser"
    ```

4. **audit_log_include_users** パラメーターを更新して、ログに含める特定の MySQL ユーザーを追加します。 ユーザーは MySQL ユーザー名で指定します。

    ```azurecli-interactive
    az mysql server configuration set --name audit_log_include_users --resource-group myresourcegroup --server mydemoserver --value "sampleuser"
    ```

## <a name="next-steps"></a>次のステップ
- Azure Database for MySQL の[監査ログ](concepts-audit-logs.md)の詳細について学習します
- [Azure portal](howto-configure-audit-logs-portal.md) で監査ログを構成する方法を学習します
