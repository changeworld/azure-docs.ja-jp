---
title: ゾーン冗長による高可用性を管理する - Azure CLI - Azure Database for MySQL フレキシブル サーバー
description: この記事では、Azure CLI を使用して Azure Database for MySQL フレキシブル サーバーでゾーン冗長による高可用性を構成する方法について説明します。
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 04/1/2021
ms.custom: references_regions
ms.openlocfilehash: c364a27b7885877231e76a694472a7ad81fe3e29
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2021
ms.locfileid: "108147015"
---
# <a name="manage-zone-redundant-high-availability-in-azure-database-for-mysql-flexible-server-with-azure-cli"></a>Azure CLI を使用して Azure Database for MySQL フレキシブル サーバーでゾーン冗長による高可用性を管理する

> [!NOTE]
> Azure Database for MySQL フレキシブル サーバーは、パブリック プレビュー段階です。 

この記事では、フレキシブル サーバーでサーバーの作成時にゾーン冗長による高可用性構成を有効または無効にする方法について説明します。 サーバーの作成後にゾーン冗長による高可用性を無効にすることもできます。 サーバーの作成後にゾーン冗長による高可用性を有効にすることはサポートされていません。

高可用性機能では、別々のゾーンに物理的に分けられたプライマリ レプリカとスタンバイ レプリカをプロビジョニングします。 詳細については、[高可用性の概念に関するドキュメント](./concepts/../concepts-high-availability.md)を参照してください。 高可用性の有効化または無効化によって、VNET 構成、ファイアウォール設定、バックアップ保有期間などの他の設定は変更されません。 高可用性を無効にしても、アプリケーションの接続と操作には影響しません。

> [!IMPORTANT]
> ゾーン冗長による高可用性は、限定された一連のリージョンで利用できます。 サポートされているリージョンは[こちら](./overview.md#azure-regions)で確認してください。 

## <a name="prerequisites"></a>前提条件
- Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。
- Azure CLI をインストールするか、最新バージョンにアップグレードします。 [Azure CLI のインストール](/cli/azure/install-azure-cli)に関するページを参照してください。
-  [az login](/cli/azure/reference-index#az-login) コマンドを使用して Azure アカウントにログインします。 **id** プロパティに注意してください。これは、お使いの Azure アカウントの **サブスクリプション ID** を参照します。

    ```azurecli-interactive
    az login
    ````

- 複数のサブスクリプションがある場合は、```az account set``` コマンドを使用して、サーバーを作成する適切なサブスクリプションを選択します。
`
    ```azurecli
    az account set --subscription <subscription id>
    ```

## <a name="enable-high-availability-during-server-creation"></a>サーバーの作成時に高可用性を有効にする
高可用性を備えたサーバーの作成に使用できるのは、汎用またはメモリ最適化の価格レベルのみです。 サーバーの高可用性は、作成時にのみ有効にすることができます。

**使用法:**

```azurecli
az mysql flexible-server create [--high-availability {Disabled, Enabled}]
                                [--resource-group]
                                [--name]
```

**例:**
```azurecli
az mysql flexible-server create --name myservername --sku-name Standard-D2ds_v4 --resource-group myresourcegroup --high-availability Enabled
```

## <a name="disable-high-availability"></a>高可用性を無効にする

高可用性を無効にするには、[az mysql flexible-server update](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_update) コマンドを使用します。 高可用性の無効化は、サーバーが高可用性を備えた状態で作成された場合にのみサポートされることに注意してください。 

```azurecli
az mysql flexible-server update [--high-availability {Disabled, Enabled}]
                                [--resource-group]
                                [--name]
```

**例:**
```azurecli
az mysql flexible-server update --resource-group myresourcegroup --name myservername --high-availability Disabled
```


## <a name="next-steps"></a>次のステップ

-   [ビジネス継続性](./concepts-business-continuity.md)について確認する
-   [ゾーン冗長による高可用性](./concepts-high-availability.md)について確認する