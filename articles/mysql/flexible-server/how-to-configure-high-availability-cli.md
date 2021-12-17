---
title: ゾーン冗長による高可用性を管理する - Azure CLI - Azure Database for MySQL フレキシブル サーバー
description: この記事では、Azure CLI を使用して Azure Database for MySQL フレキシブル サーバーでゾーン冗長による高可用性を構成する方法について説明します。
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 04/1/2021
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 3ed931d0f972caa2e4a49012ad09afe9df9d3233
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131422769"
---
# <a name="manage-zone-redundant-high-availability-in-azure-database-for-mysql-flexible-server-with-azure-cli"></a>Azure CLI を使用して Azure Database for MySQL フレキシブル サーバーでゾーン冗長による高可用性を管理する

[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]


この記事では、フレキシブル サーバーでサーバーの作成時にゾーン冗長による高可用性構成を有効または無効にする方法について説明します。 サーバーの作成後にゾーン冗長による高可用性を無効にすることもできます。 サーバーの作成後にゾーン冗長による高可用性を有効にすることはサポートされていません。

高可用性機能では、別々のゾーンに物理的に分けられたプライマリ レプリカとスタンバイ レプリカをプロビジョニングします。 詳細については、[高可用性の概念に関するドキュメント](./concepts/../concepts-high-availability.md)を参照してください。 高可用性の有効化または無効化によって、VNET 構成、ファイアウォール設定、バックアップ保有期間などの他の設定は変更されません。 高可用性を無効にしても、アプリケーションの接続と操作には影響しません。

> [!IMPORTANT]
> ゾーン冗長による高可用性は、限定された一連のリージョンで利用できます。 サポートされているリージョンは[こちら](./overview.md#azure-regions)で確認してください。

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 

    [!INCLUDE [flexible-server-free-trial-note](../includes/flexible-server-free-trial-note.md)]
- Azure CLI をインストールするか、最新バージョンにアップグレードします。 [Azure CLI のインストール](/cli/azure/install-azure-cli)に関するページを参照してください。
- [az login](/cli/azure/reference-index#az_login) コマンドを使用して Azure アカウントにログインします。 **id** プロパティに注意してください。これは、お使いの Azure アカウントの **サブスクリプション ID** を参照します。

    ```azurecli-interactive
    az login
    ````

- 複数のサブスクリプションがある場合は、```az account set``` コマンドを使用して、サーバーを作成する適切なサブスクリプションを選択します。

    ```azurecli
    az account set --subscription <subscription id>
    ```

## <a name="enable-high-availability-during-server-creation"></a>サーバーの作成時に高可用性を有効にする

高可用性を備えたサーバーの作成に使用できるのは、汎用またはメモリ最適化の価格レベルのみです。 ゾーン冗長の高可用性をサーバーに対して有効にできるのは、作成時のみです。

**使用法:**

   ```azurecli
    az mysql flexible-server create [--high-availability {Disabled, SameZone, ZoneRedundant}]
                                    [--sku-name]
                                    [--tier]
                                    [--resource-group]
                                    [--location]
                                    [--name]
   ```

**例:**

   ```azurecli
    az mysql flexible-server create --name myservername --sku-name Standard_D2ds_v4 --tier Genaralpurpose --resource-group myresourcegroup --high-availability ZoneRedundant --location eastus
   ```

## <a name="disable-high-availability"></a>高可用性を無効にする

高可用性を無効にするには、[az mysql flexible-server update](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_update) コマンドを使用します。 高可用性の無効化は、サーバーが高可用性を備えた状態で作成された場合にのみサポートされることに注意してください。

```azurecli
az mysql flexible-server update [--high-availability {Disabled, SameZone, ZoneRedundant}]
                                [--resource-group]
                                [--name]
```
>[!Note]
>ZoneRedundant から SameZone に移行する場合は、まず高可用性を無効にしてから、同じゾーンを有効にする必要があります。

**例:**

   ```azurecli
    az mysql flexible-server update --resource-group myresourcegroup --name myservername --high-availability Disabled
   ```

## <a name="next-steps"></a>次のステップ

- [ビジネス継続性](./concepts-business-continuity.md)について確認する
- [ゾーン冗長による高可用性](./concepts-high-availability.md)について確認する