---
title: ゾーン冗長の高可用性を管理する - Azure CLI - Azure Database for PostgreSQL - フレキシブル サーバー
description: この記事では、Azure CLI を使用して Azure Database for PostgreSQL フレキシブル サーバーでゾーン冗長による高可用性を構成する方法について説明します。
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: how-to
ms.date: 05/29/2021
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: b9d4a5faccd228e4eba25b6818b3dbcc0df5402a
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/02/2021
ms.locfileid: "111026632"
---
# <a name="manage-zone-redundant-high-availability-in-azure-database-for-postgresql-flexible-server-with-azure-cli"></a>Azure CLI を使用して Azure Database for PostgreSQL フレキシブル サーバーでゾーン冗長による高可用性を管理する

> [!NOTE]
> Azure Database for PostgreSQL フレキシブル サーバーはパブリック プレビュー段階です。 

この記事では、フレキシブル サーバーでサーバーの作成時にゾーン冗長による高可用性構成を有効または無効にする方法について説明します。 サーバーの作成後にゾーン冗長による高可用性を無効にすることもできます。 サーバーの作成後にゾーン冗長による高可用性を有効にすることはサポートされていません。

高可用性機能では、別々のゾーンに物理的に分けられたプライマリ レプリカとスタンバイ レプリカをプロビジョニングします。 詳細については、[高可用性の概念に関するドキュメント](./concepts/../concepts-high-availability.md)を参照してください。 高可用性の有効化または無効化によって、VNET 構成、ファイアウォール設定、バックアップ保有期間などの他の設定は変更されません。 高可用性を無効にしても、アプリケーションの接続と操作には影響しません。

> [!IMPORTANT]
> ゾーン冗長の高可用性をサポートするリージョンの一覧については、 [こちらで](./overview.md#azure-regions)サポートされているリージョンを確認してください。 

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

## <a name="enable-high-availability-during-server-creation"></a>サーバーの作成時に高可用性を有効にする
高可用性を備えたサーバーの作成に使用できるのは、汎用またはメモリ最適化の価格レベルのみです。 サーバーの高可用性は、作成時にのみ有効にすることができます。

**使用法:**

```azurecli
az postgres flexible-server create [--high-availability {Disabled, Enabled}]
                                [--resource-group]
                                [--name]
```

**例:**
```azurecli
az postgres flexible-server create --name myservername --sku-name Standard-D2ds_v4 --resource-group myresourcegroup --high-availability Enabled
```

## <a name="disable-high-availability"></a>高可用性を無効にする

高可用性を無効にするには、[az postgres flexible-server update](/cli/azure/postgres/flexible-server#az_postgres_flexible_server_update) コマンドを使用します。 高可用性の無効化は、サーバーが高可用性を備えた状態で構成された場合にのみサポートされることに注意してください。 

```azurecli
az postgres flexible-server update [--high-availability {Disabled, Enabled}]
                                [--resource-group]
                                [--name]
```

**例:**
```azurecli
az postgres flexible-server update --resource-group myresourcegroup --name myservername --high-availability Disabled
```


## <a name="next-steps"></a>次のステップ

-   [ビジネス継続性](./concepts-business-continuity.md)について確認する
-   [ゾーン冗長による高可用性](./concepts-high-availability.md)について確認する