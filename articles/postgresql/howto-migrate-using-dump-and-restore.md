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
ms.date: 07/19/2018
ms.openlocfilehash: 94d196ceecc0b63b9f0b0fe94f71363dc2086c30
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2018
ms.locfileid: "39213652"
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

> [!IMPORTANT]
> バックアップ ファイルを Azure blob/ストアにコピーし、そこから復元します。これは、インターネット経由で復元するよりもかなり高速であるはずです。
> 

## <a name="restore-the-data-into-the-target-azure-database-for-postrgesql-using-pgrestore"></a>pg_restore を使用して対象の Azure Database for PostrgeSQL にデータを復元する
ターゲット データベースを作成したら、pg_restore コマンドおよび -d、--dbname パラメータを使用して、ダンプ ファイルからターゲット データベースにデータを復元できます。
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

## <a name="next-steps"></a>次の手順
- エクスポートとインポートを使用して PostgreSQL データベースを移行するには、「[エクスポートとインポートを使用した PostgreSQL の移行](howto-migrate-using-export-and-import.md)」を参照してください。
- Azure Database for PostgreSQL へのデータベースの移行については、「[Database Migration Guide](http://aka.ms/datamigration)」 (データベースの移行ガイド) を参照してください。
