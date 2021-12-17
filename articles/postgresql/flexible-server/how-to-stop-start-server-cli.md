---
title: 停止または起動する - Azure CLI - Azure Database for PostgreSQL フレキシブル サーバー
description: この記事では、Azure CLI を使用して Azure Database for PostgreSQL で操作を停止または起動する方法について説明します。
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: how-to
ms.date: 05/29/2021
ms.openlocfilehash: bebcc9e29620c8e739735553dd64e791c5bcdb00
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/02/2021
ms.locfileid: "111027304"
---
# <a name="stopstart-azure-database-for-postgresql---flexible-server-preview-using-azure-cli"></a>Azure CLI を使用して Azure Database for PostgreSQL - フレキシブル サーバー (プレビュー) を停止または起動する

> [!IMPORTANT]
> Azure Database for PostgreSQL - フレキシブル サーバーは現在パブリック プレビュー段階です。

この記事では、Azure CLI を使用してフレキシブル サーバーの再起動、開始、および停止を実行する方法について説明します。

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

- PostgreSQL フレキシブル サーバーをまだ作成していない場合は、```az postgres flexible-server create``` コマンドを使用して作成します。

    ```azurecli
    az postgres flexible-server create --resource-group myresourcegroup --name myservername
    ```

## <a name="stop-a-running-server"></a>実行中のサーバーを停止する
サーバーを停止するには、```az postgres flexible-server stop``` コマンドを実行します。 [ローカル コンテキスト](/cli/azure/config/param-persist)を使用している場合は、引数を指定する必要はありません。

**使用法:**
```azurecli
az postgres flexible-server stop  [--name]
                               [--resource-group]
                               [--subscription]
```

**ローカル コンテキストを使用しない例:**
```azurecli
az postgres flexible-server stop  --resource-group --name myservername
```

**ローカル コンテキストを使用する例**
```azurecli
az postgres flexible-server stop
```

## <a name="start-a-stopped-server"></a>停止したサーバーを起動する
サーバーを起動するには、```az postgres flexible-server start``` コマンドを実行します。 [ローカル コンテキスト](/cli/azure/config/param-persist)を使用している場合は、引数を指定する必要はありません。

**使用法:**
```azurecli
az postgres flexible-server start [--name]
                               [--resource-group]
                               [--subscription]
```

**ローカル コンテキストを使用しない例:**
```azurecli
az postgres flexible-server start  --resource-group --name myservername
```

**ローカル コンテキストを使用する例**
```azurecli
az postgres flexible-server start
```

> [!IMPORTANT]
> サーバーが正常に起動すると、フレキシブル サーバーですべての管理操作を使用できるようになります。

## <a name="next-steps"></a>次のステップ
- [Azure Database for PostgreSQL フレキシブル サーバーの再起動](./how-to-restart-server-cli.md)の詳細を確認する


