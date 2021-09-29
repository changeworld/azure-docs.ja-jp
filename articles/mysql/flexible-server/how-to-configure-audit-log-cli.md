---
title: Azure CLI を使用して監査ログを構成する - Azure Database for MySQL - フレキシブル サーバー
description: この記事では、Azure CLI から Azure Database for MySQL フレキシブル サーバーの監査ログを構成してアクセスする方法について説明します。
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 03/30/2021
ms.openlocfilehash: cc250484510580c6b57f015c657b7dca8277d03d
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "128649363"
---
# <a name="configure-and-access-audit-logs-for-azure-database-for-mysql---flexible-server-using-the-azure-cli"></a>Azure CLI を使用した Azure Database for MySQL - フレキシブル サーバーの監査ログの構成とアクセス

[[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

> [!IMPORTANT]
> Azure Database for MySQL - フレキシブル サーバーは現在、パブリック プレビュー段階にあります。

この記事では、Azure CLI を使用して MySQL フレキシブル サーバーの[監査ログ](concepts-audit-logs.md)を構成する方法について説明します。

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 

    [!INCLUDE [flexible-server-free-trial-note](../includes/flexible-server-free-trial-note.md)]
- Azure CLI をインストールするか、最新バージョンにアップグレードします。 [Azure CLI のインストール](/cli/azure/install-azure-cli)に関するページを参照してください。
-  [az login](/cli/azure/reference-index#az_login) コマンドを使用して Azure アカウントにログインします。 **id** プロパティに注意してください。これは、お使いの Azure アカウントの **サブスクリプション ID** を参照します。

    ```azurecli-interactive
    az login
    ````

- 複数のサブスクリプションがある場合は、```az account set``` コマンドを使用して、サーバーを作成する適切なサブスクリプションを選択します。
`
    ```azurecli
    az account set --subscription <subscription id>
    ```

- MySQL フレキシブル サーバーをまだ作成していない場合は、```az mysql flexible-server create``` コマンドを使用して作成します。

    ```azurecli
    az mysql flexible-server create --resource-group myresourcegroup --name myservername
    ```

## <a name="configure-audit-logging"></a>監査ログを構成する

>[!IMPORTANT]
> サーバーのパフォーマンスに大きな影響を与えないように、監査のために必要なイベントの種類とユーザーのみをログに記録することをお勧めします。

監査ログを有効化および構成します。

```azurecli
# Enable audit logs

az mysql flexible-server parameter set \
--name audit_log_enabled \
--resource-group myresourcegroup \
--server-name mydemoserver \
--value ON
```

## <a name="next-steps"></a>次のステップ
- [監査ログ](concepts-audit-logs.md)の詳細を確認します。
