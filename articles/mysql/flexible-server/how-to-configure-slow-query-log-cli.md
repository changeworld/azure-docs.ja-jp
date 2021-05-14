---
title: Azure CLI を使用した監査ログと低速クエリ ログの構成 - Azure Database for MySQL - フレキシブル サーバー
description: この記事では、Azure CLI から Azure Database for MySQL フレキシブル サーバーの低速クエリ ログを構成し、それにアクセスする方法について説明します。
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 03/30/2021
ms.openlocfilehash: 72cf06049ae272e3e55017dd8a0207739c25d960
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/06/2021
ms.locfileid: "108738757"
---
# <a name="configure-slow-query-logs-for-azure-database-for-mysql---flexible-server-using-the-azure-cli"></a>Azure CLI を使用した Azure Database for MySQL フレキシブル サーバーの低速クエリ ログの構成

> [!IMPORTANT]
> Azure Database for MySQL - フレキシブル サーバーは現在、パブリック プレビュー段階にあります。

この記事では、Azure CLI を使用して、MySQL フレキシブル サーバーの[低速クエリ ログ](concepts-slow-query-logs.md)を構成する方法について説明します。 

## <a name="prerequisites"></a>前提条件
- Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。
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

## <a name="configure-slow-query-logs"></a>低速クエリ ログを構成する

>[!IMPORTANT]
> サーバーのパフォーマンスに大きな影響を与えないように、監査のために必要なイベントの種類とユーザーのみをログに記録することをお勧めします。

サーバーの低速クエリ ログを有効にして構成します。

```azurecli
# Turn on statement level log
az mysql flexible-server parameter set \
--name log_statement \
--resource-group myresourcegroup \
--server-name mydemoserver \
--value all


# Set log_min_duration_statement time to 10 sec
# This setting will log all queries executing for more than 10 sec. Please adjust this threshold based on your definition for slow queries
az mysql server configuration set \
--name log_min_duration_statement \
--resource-group myresourcegroup \
--server mydemoserver \
--value 10000

# Enable Slow query logs
az mysql flexible-server parameter set \
--name slow_query_log \
--resource-group myresourcegroup \
--server-name mydemoserver \
--value ON

```

## <a name="next-steps"></a>次のステップ
- [低速クエリ ログ](concepts-slow-query-logs.md)を確認する
