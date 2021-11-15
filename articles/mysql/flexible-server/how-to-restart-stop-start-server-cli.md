---
title: 再起動、停止、または開始 - Azure portal - Azure Database for MySQL フレキシブル サーバー
description: この記事では、Azure CLI を使用して Azure Database for MySQL で再起動、停止、または開始操作を実行する方法について説明します。
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 03/30/2021
ms.openlocfilehash: 31645daf978aa24dfbdc7a947d7d3d8a609936b7
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131460605"
---
# <a name="restartstopstart-an-azure-database-for-mysql-flexible-server"></a>Azure Database for MySQL フレキシブル サーバーを再起動、停止、または開始する

[[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

この記事では、Azure CLI を使用してフレキシブル サーバーの再起動、開始、および停止を実行する方法について説明します。

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

## <a name="stop-a-running-server"></a>実行中のサーバーを停止する
サーバーを停止するには、```az mysql flexible-server stop``` コマンドを実行します。 [ローカル コンテキスト](/cli/azure/config/param-persist)を使用している場合は、引数を指定する必要はありません。

**使用法:**
```azurecli
az mysql flexible-server stop  [--name]
                               [--resource-group]
                               [--subscription]
```

**ローカル コンテキストを使用しない例:**
```azurecli
az mysql flexible-server stop  --resource-group --name myservername
```

**ローカル コンテキストを使用する例**
```azurecli
az mysql flexible-server stop
```

## <a name="start-a-stopped-server"></a>停止したサーバーを起動する
サーバーを起動するには、```az mysql flexible-server start``` コマンドを実行します。 [ローカル コンテキスト](/cli/azure/config/param-persist)を使用している場合は、引数を指定する必要はありません。

**使用法:**
```azurecli
az mysql flexible-server start [--name]
                               [--resource-group]
                               [--subscription]
```

**ローカル コンテキストを使用しない例:**
```azurecli
az mysql flexible-server start  --resource-group --name myservername
```

**ローカル コンテキストを使用する例**
```azurecli
az mysql flexible-server start
```

> [!IMPORTANT]
>サーバーが正常に起動すると、フレキシブル サーバーですべての管理操作を使用できるようになります。

## <a name="restart-a-server"></a>サーバーを再起動する
サーバーを再起動するには、```az mysql flexible-server restart``` コマンドを実行します。 [ローカル コンテキスト](/cli/azure/config/param-persist)を使用している場合は、引数を指定する必要はありません。

**使用法:**
```azurecli
az mysql flexible-server restart [--name]
                                 [--resource-group]
                                 [--subscription]
```

**ローカル コンテキストを使用しない例:**
```azurecli
az mysql flexible-server restart  --resource-group --name myservername
```

**ローカル コンテキストを使用する例**
```azurecli
az mysql flexible-server restart
```


> [!IMPORTANT]
>サーバーが正常に起動すると、フレキシブル サーバーですべての管理操作を使用できるようになります。

## <a name="next-steps"></a>次のステップ
- [Azure Database for MySQL フレキシブル サーバーでのネットワーク](./concepts-networking.md)の詳細を確認します
- [Azure portal を使用して Azure Database for MySQL フレキシブル サーバーの仮想ネットワークを作成および管理します](./how-to-manage-virtual-network-portal.md)。

