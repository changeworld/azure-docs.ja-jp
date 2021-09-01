---
title: サーバーを再起動する - Azure CLI - Azure Database for MySQL
description: この記事では、Azure CLI を使用して Azure Database for MySQL サーバーを再起動する方法について説明します。
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 3/18/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 76ee5e413d325672ac1e87f62bbfa2996ab44831
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/30/2021
ms.locfileid: "122653226"
---
# <a name="restart-azure-database-for-mysql-server-using-the-azure-cli"></a>Azure CLI を使用して Azure Database for MySQL サーバーを再起動する

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]
このトピックでは、Azure Database for MySQL サーバーを再起動する方法について説明します。 メンテナンス上の理由でサーバーの再起動が必要な場合があります。これを行うと、サーバーが操作を実行しているときに短時間の停止が発生します。

サービスがビジー状態の場合、サーバーの再起動はブロックされます。 たとえば、仮想コアのスケーリングなどの前に要求した操作がサービスで処理中である場合があります。

再起動を完了するために必要な時間は、MySQL の回復プロセスに依存しています。 再起動の時間を短縮するために、再起動の実行前にサーバー上で発生しているアクティビティの量を最小限に抑えることをお勧めします。

## <a name="prerequisites"></a>前提条件

この手順を実行するには、以下が必要です。

- [Azure Database for MySQL サーバー](quickstart-create-server-up-azure-cli.md)が必要です。
 
[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- この記事では、Azure CLI のバージョン 2.0 以降が必要です。 Azure Cloud Shell を使用している場合は、最新バージョンが既にインストールされています。

## <a name="restart-the-server"></a>サーバーを再起動します

次のコマンドを使用してサーバーを再起動します。

```azurecli-interactive
az mysql server restart --name mydemoserver --resource-group myresourcegroup
```

## <a name="next-steps"></a>次のステップ

[Azure Database for MySQL でパラメーターを設定する方法](howto-configure-server-parameters-using-cli.md)について確認する
