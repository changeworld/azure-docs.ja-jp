---
title: Azure CLI を使用して Azure Database for MySQL フレキシブル サーバーの読み取りレプリカを管理する
description: Azure CLI を使用して Azure Database for MySQL フレキシブル サーバーの読み取りレプリカを設定し、管理する方法について説明します。
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 10/23/2021
ms.custom: devx-track-azurecli
ms.openlocfilehash: 7188cb16d6718fddb049a518b56b32c84ea66ec1
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131422750"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mysql-flexible-server-using-the-azure-cli"></a>Azure CLI を使用して Azure Database for MySQL フレキシブル サーバーの読み取りレプリカを作成し、管理する方法

[[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

> [!IMPORTANT]
> Azure Database for MySQL - フレキシブル サーバーの読み取りレプリカは、プレビュー段階です。

この記事では、Azure CLI を使用して Azure Database for MySQL フレキシブル サーバーの読み取りレプリカを作成および管理する方法について説明します。 読み取りレプリカの詳細については、[概要](concepts-read-replicas.md)を参照してください。

> [!Note]
>
> * 高可用性が有効になっているサーバーでは、レプリカはサポートされていません。
>
> * プライマリ サーバーで GTID が有効になっている場合 (`gtid_mode` = ON)、新しく作成されたレプリカでも GTID が有効になり、GTID ベースのレプリケーションが使用されます。 詳細については、「[グローバル トランザクション識別子 (GTID)](concepts-read-replicas.md#global-transaction-identifier-gtid)」を参照してください。

## <a name="azure-cli"></a>Azure CLI

Azure CLI を使用して、読み取りレプリカを作成して管理できます。

### <a name="prerequisites"></a>前提条件

- [Azure CLI 2.0 のインストール](/cli/azure/install-azure-cli)
- ソース サーバーとして使用される [Azure Database for MySQL フレキシブル サーバー](quickstart-create-server-cli.md)。

### <a name="create-a-read-replica"></a>読み取りレプリカを作成します

> [!IMPORTANT]
>既存のレプリカがないソースのレプリカを作成すると、ソースは最初に、レプリケーションの準備をするために再起動します。 これを考慮して、これらの操作はオフピーク期間中に実行してください。

読み取りレプリカ サーバーは、次のコマンドを使用して作成できます。

```azurecli-interactive
az mysql flexible-server replica create --replica-name mydemoreplicaserver --source-server mydemoserver --resource-group myresourcegroup
```

> [!NOTE]
> ソースと同じサーバー構成で、読み取りレプリカが作成されます。 作成された後、レプリカ サーバーの構成を変更できます。 レプリカ サーバーは、ソース サーバーと同じリソース グループ、同じ場所、および同じサブスクリプションに常に作成されます。 レプリカ サーバーを別のリソース グループや別のサブスクリプションに作成したい場合は、作成後に[レプリカ サーバーを移動](../../azure-resource-manager/management/move-resource-group-and-subscription.md)します。 レプリカが確実にソースに追随できるように、レプリカ サーバーの構成をソースと同じかそれ以上の値にしておくことをお勧めします。


### <a name="list-replicas-for-a-source-server"></a>ソース サーバーのレプリカを一覧表示する

特定のソース サーバーのレプリカをすべて表示するには、次のコマンドを実行します。

```azurecli-interactive
az mysql flexible-server replica list --server-name mydemoserver --resource-group myresourcegroup
```

### <a name="stop-replication-to-a-replica-server"></a>レプリカ サーバーへのレプリケーションを停止します。

> [!IMPORTANT]
>サーバーへのレプリケーションの停止は、元に戻すことができません。 ソースとレプリカの間のレプリケーションを停止すると、元に戻すことはできません。 レプリカ サーバーはスタンドアロン サーバーになり、読み取りと書き込みをサポートするようになります。 このサーバーをもう一度レプリカにすることはできません。

読み取りレプリカ サーバーへのレプリケーションは、次のコマンドを使用して停止できます。

```azurecli-interactive
az mysql flexible-server replica stop-replication --replica-name mydemoreplicaserver --resource-group myresourcegroup
```

### <a name="delete-a-replica-server"></a>レプリカ サーバーを削除します

読み取りレプリカ サーバーの削除は、 **[az mysql server delete](/cli/azure/mysql/server)** コマンドを使用して行うことができます。

```azurecli-interactive
az mysql flexible-server delete --resource-group myresourcegroup --name mydemoreplicaserver
```

### <a name="delete-a-source-server"></a>ソース サーバーの削除

> [!IMPORTANT]
>ソース サーバーを削除すると、すべてのレプリカ サーバーへのレプリケーションを停止し、ソース サーバー自体を削除します。 これでレプリカ サーバーは、読み取りと書き込みの両方をサポートするスタンドアロン サーバーになります。

ソース サーバーを削除するには、 **[az mysql flexible-server delete](/cli/azure/mysql/flexible-server)** コマンドを実行します。

```azurecli-interactive
az mysql flexible-server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>次のステップ

- [レプリカの読み取り](concepts-read-replicas.md) の詳細を確認する