---
title: Azure CLI を使用して Azure Database for MySQL - フレキシブル サーバーを復元する
description: この記事では、Azure CLI を使用して Azure Database for MySQL で復元操作を実行する方法について説明します。
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 04/01/2021
ms.openlocfilehash: 46602d13441fd972f3d292d0bfd48b9fd2919231
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131467988"
---
# <a name="point-in-time-restore-of-a-azure-database-for-mysql-flexible-server-with-azure-cli"></a>Azure CLI を使用した Azure Database for MySQL フレキシブル サーバーのポイントインタイム リストア

[[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

この記事では、バックアップを使用して、フレキシブル サーバーでポイントインタイム リストアを実行する手順について説明します。

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 

    [!INCLUDE [flexible-server-free-trial-note](../includes/flexible-server-free-trial-note.md)]
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

- MySQL フレキシブル サーバーをまだ作成していない場合は、```az mysql flexible-server create``` コマンドを使用して作成します。

    ```azurecli
    az mysql flexible-server create --resource-group myresourcegroup --name myservername
    ```

## <a name="restore-a-server-from-backup-to-a-new-server"></a>バックアップから新しいサーバーにサーバーを復元する

次のコマンドを実行して、サーバーを最も古い既存のバックアップに復元できます。

**使用方法**
```azurecli
az mysql flexible-server restore --restore-time
                                 --source-server
                                 [--ids]
                                 [--location]
                                 [--name]
                                 [--no-wait]
                                 [--resource-group]
                                 [--subscription]
```

**例:** この ```2021-03-03T13:10:00Z``` バックアップ スナップショットからサーバーを復元します。

```azurecli
az mysql flexible-server restore \
--name mydemoserver-restored \
--resource-group myresourcegroup \
--restore-time "2021-03-03T13:10:00Z" \
--source-server mydemoserver
```
復元にかかる時間は、サーバーに格納されているデータのサイズによって異なります。

## <a name="perform-post-restore-tasks"></a>復元後のタスクの実行
復元が完了したら、次のタスクを実行してユーザーとアプリケーションを稼働状態に戻す必要があります。

- 元のサーバーを新しいサーバーで置き換える場合は、クライアントとクライアント アプリケーションを新しいサーバーにリダイレクトする
- ユーザーが接続できるように、適切な VNet 規則が適用されていることを確認する。 これらのルールは配信元のサーバーからはコピーされません。
- 適切なログインとデータベース レベルのアクセス許可が適切に指定されていることを確認する
- 新しい復元サーバーに対して、必要に応じてアラートを構成する

## <a name="next-steps"></a>次のステップ
[ビジネス継続性](concepts-business-continuity.md)について説明します。

