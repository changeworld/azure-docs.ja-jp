---
title: dbForge Studio for MySQL を使用して MySQL データベースを Azure Database for MySQL に移行する
description: この記事では、dbForge Studio for MySQL を使用して Azure Database for MySQL に移行する方法について説明します。
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 03/03/2021
ms.openlocfilehash: d7ed106fb1922315ba181e2ca2c27dcc7d2b9cd3
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/30/2021
ms.locfileid: "122652249"
---
# <a name="migrate-data-to-azure-database-for-mysql-with-dbforge-studio-for-mysql"></a>dbForge Studio for MySQL を使用して Azure Database for MySQL にデータを移行する

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]

MySQL データベースを Azure Database for MySQL に移動しようとしていますか? dbForge Studio for MySQL の移行ツールを使用することを検討してください。 これを使用すると、データベースの転送を構成、保存、編集、自動化、スケジュール設定できます。

この記事の例を実行するには、[dbForge Studio for MySQL](https://www.devart.com/dbforge/mysql/studio/) をダウンロードしてインストールする必要があります。

## <a name="connect-to-azure-database-for-mysql"></a>Azure Database for MySQL に接続する

1. dbForge Studio for MySQL で、 **[Database]\(データベース\)** メニューから **[New Connection]\(新しい接続\)** を選択します。

1. ホスト名とサインイン資格情報を指定します。

1. **[Test Connection]\(テスト接続\)** を選択して構成を確認します。

:::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/azure-connection-1.png" alt-text="Azure Database for MySQL への接続テストが成功したことを示すスクリーンショット。" lightbox="media/concepts-migrate-dbforge-studio-for-mysql/azure-connection-1.png":::

## <a name="migrate-with-the-backup-and-restore-functionality"></a>バックアップと復元の機能を使用して移行する

dbForge Studio for MySQL を使用してデータベースを Azure に移行する場合、多くのオプションから選択できます。 データベース全体を移動する必要がある場合は、**バックアップと復元** の機能を使用することをお勧めします。

この例では、*sakila* データベースを MySQL サーバーから Azure Database for MySQL に移行します。 **バックアップと復元** 機能の使用の背後にあるロジックは、MySQL データベースのバックアップを作成し、その後、それを Azure Database for MySQL で復元することです。

### <a name="back-up-the-database"></a>データベースをバックアップする

1. dbForge Studio for MySQL で、 **[Backup and Restore]\(バックアップと復元\)** メニューから **[Backup Database]\(データベースのバックアップ\)** を選択します。 **[Database Backup Wizard]\(データベース バックアップ ウィザード\)** が表示されます。

1. **[Database Backup Wizard]\(データベース バックアップ ウィザード\)** の **[Backup content]\(バックアップ コンテンツ\)** タブで、バックアップするデータベース オブジェクトを選択します。

1. **[Options]\(オプション\)** タブで、バックアップ プロセスを要件に合わせて構成します。

    :::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/back-up-wizard-options.png" alt-text="バックアップ ウィザードの [options]\(オプション\) ペインを示すスクリーンショット。" lightbox="media/concepts-migrate-dbforge-studio-for-mysql/back-up-wizard-options.png":::

1. **[Next]\(次へ\)** を選択し、エラー処理の動作とログ記録のオプションを指定します。

1. **[Backup]\(バックアップ\)** を選択します。

### <a name="restore-the-database"></a>データベースを復元する

1.  dbForge Studio for MySQL で、Azure Database for MySQL に接続します。 [こちらの手順](#connect-to-azure-database-for-mysql)を参照してください。

1. **[Backup and Restore]\(バックアップと復元\)** メニューから **[Restore Database]\(データベースの復元\)** を選択します。 **[Database Restore Wizard]\(データベースの復元ウィザード\)** が表示されます。

1. **[Database Restore Wizard]\(データベース復元ウィザード\)** で、データベースのバックアップが含まれるファイルを選択します。

    :::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/restore-step-1.png" alt-text="[Database Restore wizard]\(データベースの復元ウィザード\) の復元手順を示すスクリーンショット。" lightbox="media/concepts-migrate-dbforge-studio-for-mysql/restore-step-1.png":::

1. **[復元]** を選択します。

1. その結果を確認します。

## <a name="migrate-with-the-copy-databases-functionality"></a>データベースのコピー機能を使用して移行する

dbForge Studio for MySQL の **[Copy Databases]\(データベースのコピー\)** 機能は、 **[Backup and Restore]\(バックアップと復元\)** に似ていますが、データベースを移行するための 2 つの手順は必要ありません。 また、一度に 2 つ以上のデータベースを転送できます。

>[!NOTE]
> **[Copy Databases]\(データベースのコピー\)** 機能は、Enterprise Edition の dbForge Studio for MySQL でのみ使用できます。

この例では、*world_x* データベースを MySQL サーバーから Azure Database for MySQL に移行します。

データベースのコピー機能を使用してデータベースを移行するには、次のようにします。

1. dbForge Studio for MySQL で、 **[Database]\(データベース\)** メニューから **[Copy Databases]\(データベースのコピー\)** を選択します。 

1. **[Copy Databases]\(データベースのコピー\)** タブで、ソースおよびターゲット接続を指定します。 また、移行するデータベースを選択します。 

   ここでは Azure MySQL 接続を入力し、*world_x* データベースを選択します。 緑色の矢印を選択してプロセスを開始します。

1. その結果を確認します。

*world_x* データベースが Azure MySQL に正常に表示されます。

:::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/copy-databases-result.png" alt-text="[Copy Databases]\(データベースのコピー\) 機能の結果を示すスクリーンショット。" lightbox="media/concepts-migrate-dbforge-studio-for-mysql/copy-databases-result.png":::

## <a name="migrate-a-database-with-schema-and-data-comparison"></a>スキーマとデータの比較を使用してデータベースを移行する

dbForge Studio for MySQL を使用してデータベース、スキーマ、データを Azure に移行する場合、多くのオプションから選択できます。 MySQL データベースから Azure に選択したテーブルを移動する必要がある場合、 **[Schema Comparison]\(スキーマ比較\)** および **[Data Comparison]\(データ比較\)** 機能を使用することをお勧めします。

この例では、*world* データベースを MySQL サーバーから Azure Database for MySQL に移行します。 

**バックアップと復元** 機能の使用の背後にあるロジックは、MySQL データベースのバックアップを作成し、その後、それを Azure Database for MySQL で復元することです。

この方法の背後にあるロジックは、Azure Database for MySQL で空のデータベースを作成し、そのデータベースをソース MySQL データベースと同期することです。 最初に **[Schema Comparison]\(スキーマ比較\)** ツールを使用し、次に **[Data Comparison]\(データ比較\)** 機能を使用します。 これらの手順によって、MySQL スキーマとデータが Azure に正確に移動されます。

この演習を実行するには、最初に [Azure Database for MySQL に接続](#connect-to-azure-database-for-mysql)し、空のデータベースを作成する必要があります。

### <a name="schema-synchronization"></a>スキーマの同期

1. **[Comparison]\(比較\)** メニューで、 **[New Schema Comparison]\(新しいスキーマ比較\)** を選択します。 **[New Schema Comparison Wizard]\(新しいスキーマ比較ウィザード\)** が表示されます。

1. ソースとターゲットを選択し、その後、スキーマ比較オプションを指定します。 **[Compare]\(比較\)** を選択します。

1. 表示される比較結果グリッドで、同期するオブジェクトを選択します。 緑色の矢印ボタンを選択して、 **[Schema Synchronization Wizard]\(スキーマ同期ウィザード\)** を開きます。

1. ウィザードの手順に従い同期を構成します。 **[Synchronize]\(同期\)** を選択して、変更をデプロイします。

    :::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/schema-sync-wizard.png" alt-text="[schema synchronization wizard]\(スキーマ同期ウィザード\) を示すスクリーンショット。" lightbox="media/concepts-migrate-dbforge-studio-for-mysql/schema-sync-wizard.png":::

### <a name="data-comparison"></a>データ比較

1. **[Comparison]\(比較\)** メニューで、 **[New Data Comparison]\(新しいデータ比較\)** を選択します。 **[New Data Comparison Wizard]\(新しいデータ比較ウィザード\)** が表示されます。

1. ソースとターゲットを選択し、その後、データ比較オプションを指定します。 必要に応じてマッピングを変更し、その後、 **[Compare]\(比較\)** を選択します。

1. 表示される比較結果グリッドで、同期するオブジェクトを選択します。 緑色の矢印ボタンを選択して、 **[Data Synchronization Wizard]\(データ同期ウィザード\)** を開きます。

    :::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/data-comp-result.png" alt-text="データ同期の結果を示すスクリーンショット。" lightbox="media/concepts-migrate-dbforge-studio-for-mysql/data-comp-result.png":::

1. 同期を構成するウィザードの手順を確認します。 **[Synchronize]\(同期\)** を選択して、変更をデプロイします。

1. その結果を確認します。

    :::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/data-sync-result.png" alt-text="[Data Synchronization wizard]\(データ同期ウィザード\) の結果を示すスクリーンショット。" lightbox="media/concepts-migrate-dbforge-studio-for-mysql/data-sync-result.png":::

## <a name="next-steps"></a>次のステップ
- [MySQL の概要](overview.md)
