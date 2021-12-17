---
title: 読み取りレプリカの管理 - Azure PowerShell - Azure Database for MySQL
description: PowerShell を使用して Azure Database for MySQL の読み取りレプリカを設定し、管理する方法について説明します。
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 06/17/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: f4980692be64c2a8b3918d2dbaab5ef9c983f453
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121778827"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mysql-using-powershell"></a>PowerShell を使用して Azure Database for MySQL の読み取りレプリカを作成し、管理する方法

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]

この記事では、PowerShell を使用して Azure Database for MySQL サービスの読み取りレプリカを作成および管理する方法を学習します。 読み取りレプリカの詳細については、[概要](concepts-read-replicas.md)を参照してください。

## <a name="azure-powershell"></a>Azure PowerShell

PowerShell を使用して、読み取りレプリカを作成して管理できます。

## <a name="prerequisites"></a>前提条件

このハウツー ガイドを完了するには、次が必要です。

- ローカルにインストールされた [Az PowerShell モジュール](/powershell/azure/install-az-ps)、またはブラウザーの [Azure Cloud Shell](https://shell.azure.com/)
- [Azure Database for MySQL サーバー](quickstart-create-mysql-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> Az.MySql PowerShell モジュールがプレビュー段階にある間は、次のコマンドを使用して、Az PowerShell モジュールとは別にこれをインストールする必要があります: `Install-Module -Name Az.MySql -AllowPrerelease`。
> Az.MySql PowerShell モジュールは、一般提供された段階で将来の Az PowerShell モジュール リリースの一部となり、Azure Cloud Shell 内からネイティブに使用できるようになります。

PowerShell をローカルで使用する場合は、[Connect-AzAccount](/powershell/module/az.accounts/Connect-AzAccount) コマンドレットを使用して Azure アカウントに接続します。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> 読み取りレプリカ機能は、汎用とメモリ最適化のどちらかの価格レベルにおける Azure Database for MySQL サーバーにのみ使用可能です。 ソース サーバーがこれらの価格レベルのいずれであるかを確認します。
>
>プライマリ サーバーで GTID が有効になっている場合 (`gtid_mode` = ON)、新しく作成されたレプリカでも GTID が有効になり、GTID ベースのレプリケーションが使用されます。 詳細については、「[グローバル トランザクション識別子 (GTID)](concepts-read-replicas.md#global-transaction-identifier-gtid)」を参照してください。

### <a name="create-a-read-replica"></a>読み取りレプリカを作成します

> [!IMPORTANT]
> 既存のレプリカがないソースのレプリカを作成すると、ソースは最初に、レプリケーションの準備をするために再起動します。 これを考慮して、これらの操作はオフピーク期間中に実行してください。

読み取りレプリカ サーバーは、次のコマンドを使用して作成できます。

```azurepowershell-interactive
Get-AzMySqlServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  New-AzMySqlReplica -Name mydemoreplicaserver -ResourceGroupName myresourcegroup
```

`New-AzMySqlReplica` コマンドには、次のパラメーターが必要です。

| 設定 | 値の例 | 説明  |
| --- | --- | --- |
| ResourceGroupName |  myresourcegroup |  レプリカ サーバーが作成されるリソース グループ。  |
| 名前 | mydemoreplicaserver | 作成する新しいレプリカ サーバーの名前。 |

リージョンをまたがる読み取りレプリカを作成するには、**Location** パラメーターを使用します。 次の例では、**米国西部** リージョンにレプリカを作成します。

```azurepowershell-interactive
Get-AzMySqlServer -Name mrdemoserver -ResourceGroupName myresourcegroup |
  New-AzMySqlReplica -Name mydemoreplicaserver -ResourceGroupName myresourcegroup -Location westus
```

> [!NOTE]
> レプリカを作成できるリージョンの詳細については、[読み取りレプリカの概念に関する記事](concepts-read-replicas.md)を参照してください。 

既定では、**Sku** パラメーターが指定されていない限り、読み取りレプリカはソースと同じサーバー構成で作成されます。

> [!NOTE]
> レプリカをマスターと維持できるようにするために、レプリカ サーバーの構成をソースと同じかそれ以上の値にしておくことをお勧めします。

### <a name="list-replicas-for-a-source-server"></a>ソース サーバーのレプリカを一覧表示する

特定のソース サーバーのレプリカをすべて表示するには、次のコマンドを実行します。

```azurepowershell-interactive
Get-AzMySqlReplica -ResourceGroupName myresourcegroup -ServerName mydemoserver
```

`Get-AzMySqlReplica` コマンドには、次のパラメーターが必要です。

| 設定 | 値の例 | 説明  |
| --- | --- | --- |
| ResourceGroupName |  myresourcegroup |  レプリカ サーバーを作成するリソース グループ。  |
| ServerName | mydemoserver | ソース サーバーの名前または ID。 |

### <a name="delete-a-replica-server"></a>レプリカ サーバーを削除します

読み取りレプリカ サーバーを削除するには、`Remove-AzMySqlServer` コマンドレットを実行します。

```azurepowershell-interactive
Remove-AzMySqlServer -Name mydemoreplicaserver -ResourceGroupName myresourcegroup
```

### <a name="delete-a-source-server"></a>ソース サーバーを削除する

> [!IMPORTANT]
> ソース サーバーを削除すると、すべてのレプリカ サーバーへのレプリケーションを停止し、ソース サーバー自体を削除します。 これでレプリカ サーバーは、読み取りと書き込みの両方をサポートするスタンドアロン サーバーになります。

ソース サーバーを削除するには、`Remove-AzMySqlServer` コマンドレットを実行します。

```azurepowershell-interactive
Remove-AzMySqlServer -Name mydemoserver -ResourceGroupName myresourcegroup
```

### <a name="known-issue"></a>既知の問題

General Purpose および Memory Optimized レベルのサーバーで使用されるストレージには、General Purpose ストレージ v1 (最大 4 TB をサポート) と General Purpose ストレージ v2 (最大 16 TB のストレージ をサポート) の 2 つの世代があります。
ソース サーバーとレプリカ サーバーでストレージの種類が同じである必要があります。 [General Purpose ストレージ v2](./concepts-pricing-tiers.md#general-purpose-storage-v2-supports-up-to-16-tb-storage) は一部のリージョンでは使用できないため、読み取りレプリカ作成のために PowerShell で場所を使用するときは必ず、正しいレプリカ リージョンを選択してください。 ソース サーバーのストレージの種類を特定する方法については、[サーバーが実行されているストレージの種類の判別方法](./concepts-pricing-tiers.md#how-can-i-determine-which-storage-type-my-server-is-running-on)に関するリンクを参照してください。 

ソース サーバーの読み取りレプリカを作成できないリージョンを選択した場合、次の図に示すようにデプロイが実行を継続し、 *"リソース プロビジョニング操作が、許可されているタイムアウト期間内に完了しませんでした。"* というエラーでタイムアウトになる問題が発生します。

[ :::image type="content" source="media/howto-read-replicas-powershell/replcia-ps-known-issue.png" alt-text="読み取りレプリカに関する CLI のエラー":::](media/howto-read-replicas-powershell/replcia-ps-known-issue.png#lightbox)


## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [PowerShell を使用して Azure Database for MySQL サーバーを再起動する](howto-restart-server-powershell.md)
