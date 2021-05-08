---
title: ダンプと復元 - Azure Database for PostgreSQL - Single Server
description: PostgreSQL データベースをダンプ ファイルに抽出することができます。 次に、Azure Database for PostgreSQL 単一サーバーの pg_dump によって作成されたファイルから復元できます。
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.subservice: migration-guide
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 809ff06fe460a06a689d7bbc11cdbd5ee247f585
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2021
ms.locfileid: "106450057"
---
# <a name="migrate-your-postgresql-database-by-using-dump-and-restore"></a>ダンプと復元を使用した PostgreSQL データベースの移行
[!INCLUDE[applies-to-postgres-single-flexible-server](includes/applies-to-postgres-single-flexible-server.md)]

[pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) を使用して、PostgreSQL データベースをダンプ ファイルに抽出することができます。 次に、[pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html) を使用して、`pg_dump` によって作成されたアーカイブ ファイルから PostgreSQL データベースを復元することができます。

## <a name="prerequisites"></a>前提条件

このハウツー ガイドの手順を実行するには、以下が必要です。
- アクセスを許可するファイアウォール規則のある [Azure Database for PostgreSQL サーバー](quickstart-create-server-database-portal.md)。
- [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) および [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html) コマンド ライン ユーティリティのインストール。

## <a name="create-a-dump-file-that-contains-the-data-to-be-loaded"></a>読み込まれるデータを格納するダンプ ファイルを作成する

オンプレミスまたは VM の既存の PostgreSQL データベースをバックアップするには、次のコマンドを実行します。

```bash
pg_dump -Fc -v --host=<host> --username=<name> --dbname=<database name> -f <database>.dump
```
たとえば、ローカル サーバーとその中に **testdb** というデータベースがある場合、以下を実行します。

```bash
pg_dump -Fc -v --host=localhost --username=masterlogin --dbname=testdb -f testdb.dump
```

## <a name="restore-the-data-into-the-target-database"></a>ターゲット データベースにデータを復元する

ターゲット データベースを作成した後、`pg_restore` コマンドおよび `--dbname` パラメータを使用して、ダンプ ファイルからターゲット データベースにデータを復元できます。

```bash
pg_restore -v --no-owner --host=<server name> --port=<port> --username=<user-name> --dbname=<target database name> <database>.dump
```

`--no-owner` パラメーターを指定すると、復元中に作成されるすべてのオブジェクトは、`--username` で指定されたユーザーによって所有されます。 詳細については、[PostgreSQL のドキュメント](https://www.postgresql.org/docs/9.6/static/app-pgrestore.html)を参照してください。

> [!NOTE]
> Azure Database for PostgreSQL サーバーでは、TLS/SSL 接続が既定でオンになっています。 お使いの PostgreSQL サーバーで TLS または SSL 接続を必要としているが、それがない場合、pg_restore ツールが TLS で接続するように環境変数 `PGSSLMODE=require` を設定します。 TLS を使用しない場合、"致命的: SSL 接続が必要です。 SSL のオプションを指定して再試行してください。" のようなエラーが表示されます。 Windows コマンド ラインで、コマンド `SET PGSSLMODE=require` を実行してから、`pg_restore` コマンドを実行します。 Linux または Bash では、コマンド `export PGSSLMODE=require` を実行してから、`pg_restore` コマンドを実行します。 
>

この例では、対象サーバー **mydemoserver.postgres.database.azure.com** 上のデータベース **mypgsqldb** に、ダンプ ファイル **testdb.dump** からデータを復元します。

この `pg_restore` を単一サーバーで使用する方法の例を、こちらに示します。

```bash
pg_restore -v --no-owner --host=mydemoserver.postgres.database.azure.com --port=5432 --username=mylogin@mydemoserver --dbname=mypgsqldb testdb.dump
```

この `pg_restore` をフレキシブル サーバーで使用する方法の例を、こちらに示します。

```bash
pg_restore -v --no-owner --host=mydemoserver.postgres.database.azure.com --port=5432 --username=mylogin --dbname=mypgsqldb testdb.dump
```

## <a name="optimize-the-migration-process"></a>移行プロセスを最適化する

既存の PostgreSQL データベースを Azure Database for PostgreSQL に移行する方法の 1 つは、ソース側のデータベースをバックアップし、それを Azure で復元することです｡ 移行に要する時間を最小限に抑えるには、バックアップと復元のコマンドに次のパラメーターを使用することを検討してください｡

> [!NOTE]
> 詳細な構文情報については、[pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) と [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html) を参照してください｡
>

### <a name="for-the-backup"></a>バックアップ

`-Fc` スイッチを使用してバックアップを実行することで、平行して復元を行い、処理をスピードアップできます。 次に例を示します。

```bash
pg_dump -h my-source-server-name -U source-server-username -Fc -d source-databasename -f Z:\Data\Backups\my-database-backup.dump
```

### <a name="for-the-restore"></a>復元

- バックアップ ファイルを、移行先の Azure Database for PostgreSQL サーバーと同じリージョンにある Azure VM に移動します。 ネットワーク待機時間を減らすために、その VM から `pg_restore` を実行します。 [高速ネットワーク](../virtual-network/create-vm-accelerated-networking-powershell.md)を有効にして VM を作成します。

- ダンプ ファイルを開き、create index ステートメントがデータの insert の後にあることを確認します。 そのようになっていない場合は、データの insert の後に create index ステートメントを移動します。 これは既定で既に行われているはずですが、確認することをお勧めします。

- `-Fc` および `-j` のスイッチを (数値とともに) 付けて復元することで､復元を並列処理します。 指定する数値は、ターゲット サーバーのコアの数です。 ターゲット サーバーのコア数の 2 倍に設定することで､その影響を確認することもできます｡

    この `pg_restore` を単一サーバーで使用する方法の例を、こちらに示します。

    ```bash
     pg_restore -h my-target-server.postgres.database.azure.com -U azure-postgres-username@my-target-server -Fc -j 4 -d my-target-databasename Z:\Data\Backups\my-database-backup.dump
    ```

    この `pg_restore` をフレキシブル サーバーで使用する方法の例を、こちらに示します。

    ```bash
     pg_restore -h my-target-server.postgres.database.azure.com -U azure-postgres-username@my-target-server -Fc -j 4 -d my-target-databasename Z:\Data\Backups\my-database-backup.dump
    ```

- ダンプ ファイルを編集することもできます｡先頭に `set synchronous_commit = off;` コマンド､最後に `set synchronous_commit = on;` コマンドを追加してください｡ アプリによってデータが変更される前に､最後にオンにするようにしないと､後続データが失われることがあります。

- ターゲットの Azure Database for PostgreSQL サーバーで、復元の前に以下を行うことを検討してください。
    
  - クエリ パフォーマンス追跡をオフにする。 これらの統計は、移行中は必要ありません。 これを行うには、`pg_stat_statements.track`、`pg_qs.query_capture_mode`、および `pgms_wait_sampling.query_capture_mode` を `NONE` に設定します。

  - 移行の速度を上げるために、32 vCore メモリ最適化のようなハイ コンピューティングとハイ メモリの SKU を使用する。 復元が完了したら、希望する SKU に簡単に戻すことができます。 SKU が大きいほど、`pg_restore` コマンドの対応する `-j` パラメーターを増やすことで、より多くの並行処理を実現できます。

  - ターゲット サーバーの IOPS を上げると、復元のパフォーマンスが向上する可能性がある。 サーバーのストレージ サイズを増やすことで、より高い IOPS をプロビジョニングできます。 この設定は元に戻せませんが、IOPS を高くすることで、将来実際のワークロードにメリットがあるかどうか検討してください。

これらのコマンドは､運用環境で使用する前にテスト環境でテスト､検証してください。

## <a name="next-steps"></a>次の手順

- エクスポートとインポートを使用して PostgreSQL データベースを移行するには、「[エクスポートとインポートを使用した PostgreSQL の移行](howto-migrate-using-export-and-import.md)」を参照してください。
- Azure Database for PostgreSQL へのデータベースの移行については、「[Database Migration Guide](https://aka.ms/datamigration)」 (データベースの移行ガイド) を参照してください。


