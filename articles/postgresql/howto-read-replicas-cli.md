---
title: Azure CLI から Azure Database for PostgreSQL - 単一サーバーの読み取りレプリカを管理する
description: Azure CLI から Azure Database for PostgreSQL - 単一サーバーの読み取りレプリカを管理する方法について説明します。
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/04/2019
ms.openlocfilehash: 5946c74d0075e04112e840d78dd9f5f57bec7475
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/04/2019
ms.locfileid: "70309394"
---
# <a name="create-and-manage-read-replicas-from-the-azure-cli"></a>Azure CLI から読み取りレプリカを作成および管理する

この記事では、Azure CLI から Azure Database for PostgreSQL の読み取りレプリカを作成および管理する方法について説明します。 読み取りレプリカの詳細については、[概要](concepts-read-replicas.md)を参照してください。

## <a name="prerequisites"></a>前提条件
- マスター サーバーになる [Azure Database for PostgreSQL サーバー](quickstart-create-server-up-azure-cli.md)。

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

CLI をローカルにインストールして使用する場合、この記事では、Azure CLI バージョン 2.0 以降を実行していることが要件です。 インストールされているバージョンを確認するには、`az --version` コマンドを実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール]( /cli/azure/install-azure-cli)に関するページを参照してください。 


## <a name="prepare-the-master-server"></a>マスター サーバーの準備
汎用またはメモリ最適化レベルにあるマスター サーバーを準備するには、次の手順を使用する必要があります。

マスター サーバーでは、`azure.replication_support` パラメーターを **[REPLICA]** に設定する必要があります。 この静的パラメーターが変更された場合、その変更を有効にするにはサーバーの再起動が必要です。

1. `azure.replication_support` は REPLICA に設定します。

   ```azurecli-interactive
   az postgres server configuration set --resource-group myresourcegroup --server-name mydemoserver --name azure.replication_support --value REPLICA
   ```

2. 再起動して変更をサーバーに適用します。

   ```azurecli-interactive
   az postgres server restart --name mydemoserver --resource-group myresourcegroup
   ```

## <a name="create-a-read-replica"></a>読み取りレプリカを作成します

[az postgres server replica create](/cli/azure/postgres/server/replica?view=azure-cli-latest#az-postgres-server-replica-create) コマンドには、次のパラメーターが必要です。

| Setting | 値の例 | 説明  |
| --- | --- | --- |
| resource-group | myresourcegroup |  レプリカ サーバーが作成されるリソース グループ。  |
| 名前 | mydemoserver-replica | 作成する新しいレプリカ サーバーの名前。 |
| source-server | mydemoserver | レプリケート元の既存のマスター サーバーの名前またはリソース ID。 |

以下の CLI の例では、レプリカはマスターと同じリージョンに作成されます。

```azurecli-interactive
az postgres server replica create --name mydemoserver-replica --source-server mydemoserver --resource-group myresourcegroup
```

リージョン間の読み取りレプリカを作成するには、`--location` パラメーターを使用します。 次の CLI の例では、米国西部にレプリカを作成します。

```azurecli-interactive
az postgres server replica create --name mydemoserver-replica --source-server mydemoserver --resource-group myresourcegroup --location westus
```

> [!NOTE]
> レプリカを作成できるリージョンの詳細については、[読み取りレプリカの概念に関する記事](concepts-read-replicas.md)を参照してください。 

汎用またはメモリ最適化マスター サーバーで `azure.replication_support` パラメーターを **[REPLICA]** に設定しておらず、サーバーを再起動していないと、エラーが返されます。 レプリカを作成する前に、この 2 つの手順を済ませておいてください。

レプリカは、マスターと同じコンピューティングとストレージの設定を使用して作成されます。 レプリカが作成されたら、マスター サーバーとは独立にいくつかの設定 (コンピューティング世代、仮想コア、ストレージ、およびバックアップ保持期間) を変更できます。 価格レベルも独立して変更できます (Basic レベルへの変更や Basic レベルからの変更を除く)。

> [!IMPORTANT]
> マスター サーバーの設定が新しい値に更新される前に、レプリカの設定をそれと同等以上の値に更新します。 このアクションは、レプリカがマスターに対するあらゆる変更に追従できるようにするのに役立ちます。

## <a name="list-replicas"></a>レプリカの一覧表示
マスター サーバーのレプリカの一覧を表示するには、[az postgres server replica list](/cli/azure/postgres/server/replica?view=azure-cli-latest#az-postgres-server-replica-list) コマンドを使用します。

```azurecli-interactive
az postgres server replica list --server-name mydemoserver --resource-group myresourcegroup 
```

## <a name="stop-replication-to-a-replica-server"></a>レプリカ サーバーへのレプリケーションを停止します。
マスター サーバーと読み取りレプリカの間のレプリケーションを停止するには、[az postgres server replica stop](/cli/azure/postgres/server/replica?view=azure-cli-latest#az-postgres-server-replica-stop) コマンドを使用します。

マスター サーバーと読み取りレプリカへのレプリケーションを停止した後、それを元に戻すことはできません。 読み取りレプリカは、読み取りと書き込みの両方をサポートするスタンドアロン サーバーになります。 スタンドアロン サーバーをもう一度レプリカにすることはできません。

```azurecli-interactive
az postgres server replica stop --name mydemoserver-replica --resource-group myresourcegroup 
```

## <a name="delete-a-master-or-replica-server"></a>マスター サーバーまたはレプリカ サーバーの削除
マスター サーバーまたはレプリカ サーバーを削除するには、[az postgres server delete](/cli/azure/postgres/server?view=azure-cli-latest#az-postgres-server-delete) コマンドを使用します。

マスター サーバーを削除すると、すべての読み取りレプリカへのレプリケーションが停止されます。 読み取りレプリカは、読み取りと書き込みの両方をサポートするようになったスタンドアロン サーバーになります。

```azurecli-interactive
az postgres server delete --name myserver --resource-group myresourcegroup
```

## <a name="next-steps"></a>次の手順
* [Azure Database for PostgreSQL の読み取りレプリカ](concepts-read-replicas.md)について確認してください。
* [Azure portal で読み取りレプリカを作成および管理する](howto-read-replicas-portal.md)方法を確認する。