---
title: Azure Database for PostgreSQL でダンプおよび復元する方法
description: ダンプ ファイルに PostgreSQL データベースを抽出し、Azure Database for PostgreSQL で pg_dump によって作成されたファイルから復元する方法について説明します。
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/22/2018
ms.openlocfilehash: 2aa59bcf0d56358601b81730abe330a56ca35d02
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2018
ms.locfileid: "52966910"
---
# <a name="migrate-your-postgresql-database-using-dump-and-restore"></a>ダンプと復元を使用した PostgreSQL データベースの移行
[pg_dump](https://www.postgresql.org/docs/9.3/static/app-pgdump.html) を使用して PostgreSQL データベースをダンプ ファイルに抽出し、[pg_restore](https://www.postgresql.org/docs/9.3/static/app-pgrestore.html) を使用して、pg_dump によって作成されたアーカイブ ファイルから PostgreSQL データベースを復元することができます。

## <a name="prerequisites"></a>前提条件
このハウツー ガイドの手順を実行するには、以下が必要です。
- アクセスとその下のデータベースを許可するファイアウォール規則のある [Azure Database for PostgreSQL サーバー](quickstart-create-server-database-portal.md)
- [pg_dump](https://www.postgresql.org/docs/9.6/static/app-pgdump.html) および [pg_restore](https://www.postgresql.org/docs/9.6/static/app-pgrestore.html) コマンド ライン ユーティリティのインストール

以下の手順に従って、PostgreSQL データベースをダンプおよび復元します。

## <a name="create-a-dump-file-using-pgdump-that-contains-the-data-to-be-loaded"></a>読み込まれるデータを含む pg_dump を使用してダンプ ファイルを作成する
オンプレミスまたは VM の既存の PostgreSQL データベースをバックアップするには、次のコマンドを実行します。
```bash
pg_dump -Fc -v --host=<host> --username=<name> --dbname=<database name> > <database>.dump
```
たとえば、ローカル サーバーとその中に **testdb** というデータベースがあるとします
```bash
pg_dump -Fc -v --host=localhost --username=masterlogin --dbname=testdb > testdb.dump
```


## <a name="restore-the-data-into-the-target-azure-database-for-postrgesql-using-pgrestore"></a>pg_restore を使用して対象の Azure Database for PostrgeSQL にデータを復元する
ターゲット データベースを作成すると、pg_restore コマンドおよび -d、--dbname パラメータを使用して、ダンプ ファイルからターゲット データベースにデータを復元できます。
```bash
pg_restore -v --no-owner –-host=<server name> --port=<port> --username=<user@servername> --dbname=<target database name> <database>.dump
```
--no-owner パラメーターを指定すると、復元中に作成されるすべてのオブジェクトは、--username に指定されたユーザーによって所有されます。 詳細については、[pg_restore](https://www.postgresql.org/docs/9.6/static/app-pgrestore.html) の公式 PostgreSQL ドキュメントを参照してください。

> [!NOTE]
> お使いの PostgreSQL サーバーで SSL 接続を必要とする場合 (Azure Database for PostgreSQL サーバーでは既定でオン) は、pg_restore ツールが SSL で接続するように、環境変数 `PGSSLMODE=require` を設定します。 SSL を使用しない場合、エラー `FATAL:  SSL connection is required. Please specify SSL options and retry.` が表示される場合があります。
>
> Windows コマンド ラインで、コマンド `SET PGSSLMODE=require` を実行してから、pg_restore コマンドを実行します。 Linux または Bash では、コマンド `export PGSSLMODE=require` を実行してから、pg_restore コマンドを実行します。
>

この例では、対象サーバー **mydemoserver.postgres.database.azure.com** 上のデータベース **mypgsqldb** に、ダンプ ファイル **testdb.dump** からデータを復元します。 
```bash
pg_restore -v --no-owner --host=mydemoserver.postgres.database.azure.com --port=5432 --username=mylogin@mydemoserver --dbname=mypgsqldb testdb.dump
```

## <a name="optimizing-the-migration-process"></a>移行プロセスの最適化

既存の PostgreSQL データベースを Azure Database for PostgreSQL サービスに移行する方法の 1 つは、ソース側のデータベースをバックアップし、そのバックアップを Azure に復元することです｡ 移行に要する時間を最小限に抑えるには、バックアップと復元のコマンドに次のパラメーターを使用することを検討してください｡

> [!NOTE]
> 構文についての詳細は、[pg_dump](https://www.postgresql.org/docs/9.6/static/app-pgdump.html) と [pg_restore](https://www.postgresql.org/docs/9.6/static/app-pgrestore.html) の記事を参照してください｡
>

### <a name="for-the-backup"></a>バックアップ
- バックアップと平行して復元を行うことで処理をスピードアップできるよう､バックアップには -FC スイッチを使用します｡ 例: 

    ```
    pg_dump -h MySourceServerName -U MySourceUserName -Fc -d MySourceDatabaseName > Z:\Data\Backups\MyDatabaseBackup.dump
    ```

### <a name="for-the-restore"></a>復元
- バックアップ ファイルは､移行先の Azure Database for PostgreSQL サーバーと同じリージョンにある Azure VM に移動し、その VM から pg_restore を実行することで､ネットワーク待機時間を短縮することをお勧めします。 また､VM は [accelerated networking](../virtual-network/create-vm-accelerated-networking-powershell.md) を有効にした状態で作成することもお勧めします。
- 既定では、すでにその設定になっているはずですが､ダンプ ファイルを開いて､データの insert 後に create index ステートメントがあることを確認してください｡ そのようになっていない場合は、データの insert の後に create index ステートメントを移動します。
- -fc および -j *#* スイッチを付けて復元することで､復元を並列処理します。 *#* ターゲット サーバーのコアの数です。 *#* をターゲット サーバーのコア数の 2 倍に設定することで､その影響を確認することもできます｡ 例: 

    ```
    pg_restore -h MyTargetServer.postgres.database.azure.com -U MyAzurePostgreSQLUserName -Fc -j 4 -d MyTargetDatabase Z:\Data\Backups\MyDatabaseBackup.dump
    ```

- ダンプ ファイルを編集することもできます｡先頭に *set synchronous_commit = off;* コマンド､最後に *set synchronous_commit = on;* コマンドを追加してください｡ アプリによってデータが変更される前の､最後にオンにするようにしないと､後続データが失われることがあります。

これらのコマンドは､運用環境で使用する前にテスト環境でテスト､検証してください。

## <a name="next-steps"></a>次の手順
- エクスポートとインポートを使用して PostgreSQL データベースを移行するには、「[エクスポートとインポートを使用した PostgreSQL の移行](howto-migrate-using-export-and-import.md)」を参照してください。
- Azure Database for PostgreSQL へのデータベースの移行については、「[Database Migration Guide](https://aka.ms/datamigration)」 (データベースの移行ガイド) を参照してください。
