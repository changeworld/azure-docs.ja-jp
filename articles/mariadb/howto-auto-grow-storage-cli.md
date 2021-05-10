---
title: ストレージの自動拡張 - Azure CLI - Azure Database for MariaDB
description: この記事では、Azure CLI を使用して Azure Database for MariaDB のストレージの自動拡張を有効にする方法について説明します。
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: how-to
ms.date: 3/18/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: f2ae7a890fc1dab29b6cc99e4cc88087dbc6e052
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/13/2021
ms.locfileid: "107366183"
---
# <a name="auto-grow-azure-database-for-mariadb-storage-using-the-azure-cli"></a>Azure CLI を使用した Azure Database for MariaDB のストレージの自動拡張
この記事では、ワークロードに影響を与えることなく拡張されるように Azure Database for MariaDB サーバーのストレージを構成する方法について説明します。

[ストレージの上限に到達](concepts-pricing-tiers.md#reaching-the-storage-limit)したサーバーは、読み取り専用に設定されます。 プロビジョニングされたストレージが 100 GB 未満のサーバーに対してストレージの自動拡張が有効にされている場合、空きストレージがプロビジョニングされたストレージの 1 GB または 10% のどちらか大きい方を下回ると、プロビジョニングされたストレージ サイズが 5 GB 単位ですぐに拡張されます。 プロビジョニングされたストレージが 100 GB を超えるサーバーの場合、空きストレージ領域が、プロビジョニングされたストレージ サイズの 10 GB を下回ると、プロビジョニングされたストレージ サイズは 5% 増加します。 [こちら](concepts-pricing-tiers.md#storage)で指定されているストレージの上限が適用されます。

## <a name="prerequisites"></a>前提条件

このガイドを完了するには、以下が必要です。

- [Azure Database for MariaDB サーバー](quickstart-create-mariadb-server-database-using-azure-cli.md)が必要です。

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- この記事では、Azure CLI のバージョン 2.0 以降が必要です。 Azure Cloud Shell を使用している場合は、最新バージョンが既にインストールされています。

## <a name="enable-mariadb-server-storage-auto-grow"></a>MariaDB サーバーのストレージの自動拡張を有効にする

次のコマンドを使用すると、既存のサーバー上でサーバーの自動拡張ストレージが有効にされます。

```azurecli-interactive
az mariadb server update --name mydemoserver --resource-group myresourcegroup --auto-grow Enabled
```

次のコマンドを使用して、新しいサーバーの作成時にサーバーの自動拡張ストレージを有効にします。

```azurecli-interactive
az mariadb server create --resource-group myresourcegroup --name mydemoserver  --auto-grow Enabled --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 --version 10.3
```

## <a name="next-steps"></a>次のステップ

[メトリックに関するアラートを作成する方法](howto-alert-metric.md)について学習します。
