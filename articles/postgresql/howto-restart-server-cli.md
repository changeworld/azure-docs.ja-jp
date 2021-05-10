---
title: サーバーを再起動する - Azure CLI - Azure Database for PostgreSQL - Single Server
description: この記事では、Azure CLI を使用して Azure Database for PostgreSQL - 単一サーバーを再起動する方法について説明します
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: how-to
ms.date: 5/6/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: af870c495ae707dd8319645eca979f0906234b4f
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "105608393"
---
# <a name="restart-azure-database-for-postgresql---single-server-using-the-azure-cli"></a>Azure CLI を使用して Azure Database for PostgreSQL - 単一サーバーを再起動する
このトピックでは、Azure Database for PostgreSQL サーバーを再起動する方法について説明します。 メンテナンス上の理由でサーバーの再起動が必要な場合があります。これを行うと、サーバーが操作を実行しているときに短時間の停止が発生します。

サービスがビジー状態の場合、サーバーの再起動はブロックされます。 たとえば、仮想コアのスケーリングなどの前に要求した操作がサービスで処理中である場合があります。
 
> [!NOTE] 
> 再起動を完了するために必要な時間は、PostgreSQL の回復プロセスに依存しています。 再起動の時間を短縮するために、再起動の実行前にサーバー上で発生しているアクティビティの量を最小限に抑えることをお勧めします。 チェックポイントの頻度を増やすこともできます。 `max_wal_size` など、チェックポイント関連のパラメーター値を調整することもできます。 サーバーを再起動する前に `CHECKPOINT` コマンドを実行することも推奨されます。

## <a name="prerequisites"></a>前提条件
この手順を実行するには、以下が必要です。
- [Azure Database for PostgreSQL サーバー](quickstart-create-server-up-azure-cli.md)を作成します。

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- この記事では、Azure CLI のバージョン 2.0 以降が必要です。 Azure Cloud Shell を使用している場合は、最新バージョンが既にインストールされています。

## <a name="restart-the-server"></a>サーバーを再起動します

次のコマンドを使用してサーバーを再起動します。

```azurecli-interactive
az postgres server restart --name mydemoserver --resource-group myresourcegroup
```

## <a name="next-steps"></a>次のステップ

[Azure Database for PostgreSQL でパラメーターを設定する方法](howto-configure-server-parameters-using-cli.md)について確認する
