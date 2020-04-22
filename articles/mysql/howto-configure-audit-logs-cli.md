---
title: 監査ログにアクセスする - Azure CLI - Azure Database for MySQL
description: この記事では、Azure CLI から Azure Database for MySQL の監査ログを構成し、それにアクセスする方法について説明します。
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 4/13/2020
ms.openlocfilehash: d532e1990586d80d675a8ccb247c0c9f7908bb6f
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/14/2020
ms.locfileid: "81384120"
---
# <a name="configure-and-access-audit-logs-in-the-azure-cli"></a>Azure CLI での監査ログの構成とアクセス

Azure CLI を使用して、[Azure Database for MySQL の監査ログ](concepts-audit-logs.md)を構成することができます。

> [!IMPORTANT]
> 監査ログ機能は現在、プレビュー段階です。

## <a name="prerequisites"></a>前提条件

このハウツー ガイドの手順を実行するには、以下が必要です。

- [Azure Database for MySQL サーバー](quickstart-create-mysql-server-database-using-azure-portal.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> このガイドで説明する方法では、Azure CLI バージョン 2.0 以降を使う必要があります。 バージョンを確認するには、Azure CLI コマンド プロンプトで「`az --version`」と入力します。 インストールまたはアップグレードする必要には、「[Azure CLI のインストール]( /cli/azure/install-azure-cli)」をご覧ください。

## <a name="configure-audit-logging"></a>監査ログを構成する

次の手順に従って、監査ログを有効にし、構成します。

1. **audit_logs_enabled** パラメーターを "ON" に設定して、監査ログをオンにします。 
    ```azurecli-interactive
    az mysql server configuration set --name audit_log_enabled --resource-group myresourcegroup --server mydemoserver --value ON
    ```

1. **audit_log_events** パラメーターを更新して、ログに記録する[イベントの種類](concepts-audit-logs.md#configure-audit-logging)を選択します。
    ```azurecli-interactive
    az mysql server configuration set --name audit_log_events --resource-group myresourcegroup --server mydemoserver --value "ADMIN,CONNECTION"
    ```

1. **audit_log_exclude_users** パラメーターを更新して、ログから除外する MySQL ユーザーを追加します。 ユーザーは MySQL ユーザー名で指定します。
    ```azurecli-interactive
    az mysql server configuration set --name audit_log_exclude_users --resource-group myresourcegroup --server mydemoserver --value "azure_superuser"
    ```

1. **audit_log_include_users** パラメーターを更新して、ログに含める特定の MySQL ユーザーを追加します。 ユーザーは MySQL ユーザー名で指定します。
    ```azurecli-interactive
    az mysql server configuration set --name audit_log_include_users --resource-group myresourcegroup --server mydemoserver --value "sampleuser"
    ```

## <a name="next-steps"></a>次のステップ
- Azure Database for MySQL の[監査ログ](concepts-audit-logs.md)の詳細について学習します
- [Azure portal](howto-configure-audit-logs-portal.md) で監査ログを構成する方法を学習します