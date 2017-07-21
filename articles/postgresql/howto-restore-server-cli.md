---
title: "Azure Database for PostgreSQL サーバーを復元する方法 | Microsoft Docs"
description: "この記事では、Azure CLI コマンド ラインを使って Azure Database for PostgreSQL のサーバーをバックアップおよび復元する方法について説明します。"
services: postgresql
author: jasonwhowell
ms.author: jasonh
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql-database
ms.devlang: azure-cli
ms.topic: article
ms.date: 06/13/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 4f68f90c3aea337d7b61b43e637bcfda3c98f3ea
ms.openlocfilehash: fa63ccedb455e4972bfdd0e15069ad5e932b0fa3
ms.contentlocale: ja-jp
ms.lasthandoff: 06/20/2017

---

# <a name="how-to-back-up-and-restore-a-server-in-azure-database-for-postgresql-using-the-azure-cli"></a>Azure CLI を使用した Azure Database for PostgreSQL サーバーのバックアップと復元方法

## <a name="prerequisites"></a>前提条件
このハウツー ガイドの手順を実行するには、以下が必要です。
- [Azure Database for PostgreSQL サーバーとデータベース](quickstart-create-server-database-azure-cli.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

CLI をローカルにインストールして使用する場合、このトピックでは、Azure CLI バージョン 2.0 以降を実行していることが要件です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、「[Azure CLI 2.0 のインストール]( /cli/azure/install-azure-cli)」を参照してください。 

## <a name="backup-happens-automatically"></a>自動バックアップ
Azure Database for PostgreSQL を使用するとき、このデータベース サービスは 5 分ごとに自動でサービスのバックアップを行います。 

このバックアップは、Basic レベルでは 7 日間、Standard レベルでは 35 日間使用できます。 詳しくは、「[Azure Database for PostgreSQL のオプションとパフォーマンス: 各価格レベルで使用できる内容](concepts-service-tiers.md)」をご覧ください。

自動バックアップ機能を使用して、過去の特定の時点までサーバーとそのサーバーのすべてのデータベースを新しいサーバーに復元できます。

## <a name="restore-a-database-to-a-previous-point-in-time-using-the-azure-cli"></a>Azure CLI を使って以前の時点にデータベースを復元する
Azure Database for PostgreSQL では、過去の特定の時点までサーバーを復元できます。 復元されたデータは新しいサーバーにコピーされ、既存のサーバーはそのまま残されます。 たとえば、今日の正午にテーブルが誤って削除された場合、正午の直前に復元できます。 その後、不足しているテーブルとデータを、サーバーの復元されたコピーから取得します。 

復元を行うには、Azure CLI コマンドの [az postgres server restore](/cli/azure/postgres/server#restore) を使います。

### <a name="run-the-restore-command"></a>復元コマンドを実行する
```azurecli-interactive
az postgres server restore --resource-group myResourceGroup --name mypgserver-restored --restore-point-in-time 2017-04-13T13:59:00Z --source-server mypgserver-20170401
```

`az postgres server restore` コマンドには、次のパラメーターが必要です。
| Setting | 推奨値 | Description  |
| --- | --- | --- |
| resource-group |  myResourceGroup |  ソース サーバーが存在するリソース グループ。  |
| name | mypgserver-restored | 復元コマンドで作成される新しいサーバーの名前。 |
| restore-point-in-time | 2017-04-13T13:59:00Z | 復元する特定の時点を選びます。 この日付と時刻は、ソース サーバーのバックアップ保有期間内でなければなりません。 ISO8601 の日時形式を使います。 たとえば、ローカルなタイムゾーン (例: `2017-04-13T05:59:00-08:00`) または UTC Zulu 形式 (例: `2017-04-13T13:59:00Z`) を使うことができます。 |
| source-server | mypgserver-20170401 | 復元元のソース サーバーの名前または ID。 |

特定の時点にサーバーを復元すると、新しいサーバーが作成され、指定した時点の元のサーバーがコピーされます。 復元されたサーバーの場所と価格レベルの値は、元のサーバーと同じです。 コマンドは同期であり、サーバーが復元されるまで待ってから戻ります。 

復元が終了した後、作成された新しいサーバーを調べます。 データが期待どおりに復元されたことを確認します。

## <a name="next-steps"></a>次のステップ
[Azure Database for PostgreSQL の接続ライブラリ](concepts-connection-libraries.md)

