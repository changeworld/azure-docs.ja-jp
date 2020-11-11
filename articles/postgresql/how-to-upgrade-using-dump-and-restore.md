---
title: ダンプと復元を使用したアップグレード - Azure Database for PostgreSQL - 単一サーバー
description: データベースをダンプおよび復元して、上位バージョンの Azure Database for PostgreSQL - 単一サーバーに移行するいくつかの方法について説明します。
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 11/03/2020
ms.openlocfilehash: 26154f4501daba373f1f8b108f1ee7105b1b194f
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2020
ms.locfileid: "93294213"
---
# <a name="upgrade-your-postgresql-database-using-dump-and-restore"></a>ダンプと復元を使用した PostgreSQL データベースのアップグレード

Azure Database for PostgreSQL - 単一サーバーでは、次のいずれかの方法を使用して PostgreSQL データベース エンジンを上位のメジャー バージョンにアップグレードすることをお勧めします。
* PostgreSQL [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) および [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html) を使用したオフライン方法。 この方法では、まず、ソース サーバーからダンプを実行した後、そのダンプをターゲット サーバーに復元します。
* [**Database Migration Service**](https://docs.microsoft.com/azure/dms/tutorial-azure-postgresql-to-azure-postgresql-online-portal) (DMS) を使用したオンライン方法。 この方法では、ターゲット データベースとソースの同期が維持され、カットオーバーのタイミングを選択できます。 ただし、対処する必要があるいくつかの前提条件と制限があります。 

オンラインとオフラインのどちらの方法でメジャー バージョンのアップグレードを実行するかを決定する際には、次の推奨事項を使用できます。

| **データベース** | **ダンプ/復元 (オフライン)** | **DMS (オンライン)** |
| ------ | :------: | :-----: |
| データベースが小さく、アップグレードするためのダウンタイムを許容できる  | X | |
| 小規模なデータベース (10 GB 未満)  | X | X | 
| 中小規模の DB (10 GB から 100 GB) | X | X |
| 大規模なデータベース (100 GB より大きい) |  | X |
| アップグレードするためのダウンタイムを許容できる (データベースのサイズに関係なく) | X |  |
| 再起動を含む DMS の[前提条件](https://docs.microsoft.com/azure/dms/tutorial-azure-postgresql-to-azure-postgresql-online-portal#prerequisites)に対処できるか? |  | X |
| アップグレード プロセス中に DDL とログに記録されていないテーブルを回避できるか? | |  X |

この攻略ガイドでは、PostgreSQL の pg_dump および pg_restore コマンドを使用してデータベースをアップグレードする方法について 2 つの方法の例を示します。 データベースはソース サーバーからターゲット サーバーに **移行** されますが、このドキュメントでは、このプロセスを **アップグレード** と呼びます。 

> [!NOTE]
> PostgreSQL のダンプと復元は、さまざまな方法で実行できます。 このドキュメントで参照される方法とは異なる方法の使用を選択することもできます。 たとえば、ダンプを実行した後、PostgreSQL クライアントから復元を行うには、詳細な手順とベスト プラクティスに関する[ドキュメント](./howto-migrate-using-dump-and-restore.md)を参照してください。 ダンプと復元の詳細な構文と追加のパラメーターについては、[pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) および [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html) の記事を参照してください。 


## <a name="prerequisites-for-using-dump-and-restore-with-azure-postgresql"></a>Azure PostgreSQL でダンプと復元を使用するための前提条件
 
この攻略ガイドの手順を実行するには、以下が必要です。
- 9\.5、9.6、または 10 (Azure Database for PostgreSQL - 単一サーバー) を実行するソース データベース
- ターゲット データベース サーバーと目的の PostgreSQL メジャー バージョンの [Azure Database for PostgreSQL サーバー](quickstart-create-server-database-portal.md)。 
- PostgreSQL がインストールされており、[pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) および [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html) コマンドライン ユーティリティがインストールされているクライアント システム (Linux)。 
- または、[Azure Cloud Shell](https://shell.azure.com) を使用するか、[Azure portal](https://portal.azure.com) の右上にあるメニュー バーで Azure Cloud Shell をクリックすることもできます。 ダンプおよび復元コマンドを実行する前に、ご利用のアカウントにログインする (`az login`) 必要があります。
- VM などの PostgreSQL クライアントの場所 (ソース サーバーおよびターゲット サーバーと同じリージョンで実行されることが望ましい)。 

## <a name="additional-details-and-considerations"></a>追加情報と考慮事項
- portal で [接続文字列] をクリックして、ソースおよびターゲットのデータベースへの接続文字列を見つけることができます。 
- サーバー内で複数のデータベースを実行している可能性があります。 データベースの一覧を確認するには、ソース サーバーに接続し、`\l`を実行します。
- ターゲットのデータベース サーバーで、対応するデータベースを作成します。
- `azure_maintenance` またはテンプレート データベースのアップグレードをスキップできます。
- 上記の表を参照して、データベースがこの移行モードに適しているかどうかを判断します。
- Azure Cloud Shell を使用する場合、セッションは 20 分後にタイムアウトします。 データベース サイズが 10 GB 未満の場合、アップグレードはタイムアウトせずに完了する可能性があります。それ以外の場合は、10 から 15 分に 1 回、<Enter> キーを押すなど、他の方法でセッションを開いたままにすることが必要なことがあります。 

> [!TIP] 
> - ソースとターゲットのデータベースに同じパスワードを使用する場合、`PGPASSWORD=yourPassword` 環境変数を設定できます。  この場合、psql、pg_dump、pg_restore などのコマンドを実行するたびにパスワードを入力する必要はありません。  同様に、`PGUSER`、`PGSSLMODE` などの追加変数も設定できます。[PostgreSQL の環境変数](https://www.postgresql.org/docs/11/libpq-envars.html)に関するページを参照してください。
>  
> - お使いの PostgreSQL サーバーで TLS または SSL 接続を必要とする場合 (Azure Database for PostgreSQL サーバーでは既定でオン) は、pg_restore ツールが TLS で接続するように、環境変数 `PGSSLMODE=require` を設定します。 TLS を使用しない場合、エラー `FATAL:  SSL connection is required. Please specify SSL options and retry.` が表示される場合があります。
>
> - Windows コマンド ラインで、コマンド `SET PGSSLMODE=require` を実行してから、pg_restore コマンドを実行します。 Linux または Bash では、コマンド `export PGSSLMODE=require` を実行してから、pg_restore コマンドを実行します。

## <a name="example-database-used-in-this-guide"></a>このガイドで使用されるデータベース例

 | **説明** | **Value** |
 | ------- | ------- |
 | ソース サーバー (v9.5) | pg-95.postgres.database.azure.com |
 | [同期元データベース] | bench5gb |
 | ソース データベースのサイズ | 5 GB |
 | ソース ユーザー名 | pg@pg-95 |
 | ターゲット サーバー (v11) | pg-11.postgres.database.azure.com |
 | 対象になるデータベース | bench5gb |
 | ターゲット ユーザー名 | pg@pg-11 |

## <a name="method-1-upgrade-with-streaming-backups-to-the-target"></a>方法 1:バックアップをターゲットにストリーミングしてアップグレードする 

この方法では、データベース ダンプ全体が、ターゲットのデータベース サーバーに直接ストリーミングされ、ダンプはクライアントに保存されません。 このため、これは、記憶域に制限があるクライアントで使用できます。また、Azure Cloud Shell からも実行できます。 

1. `\l` コマンドを使用してターゲット サーバー内にデータベースが存在していることを確認します。 データベースが存在しない場合は、作成します。
   ```azurecli-interactive
    psql "host=myTargetServer port=5432 dbname=postgres user=myUser password=###### sslmode=mySSLmode"
    ```
    ```bash
    postgres> \l   
    postgres> create database myTargetDB;
   ```

2. パイプを使用して、ダンプと復元を単一のコマンド ラインとして実行します。 
    ```azurecli-interactive
    pg_dump -Fc -v --mySourceServer --port=5432 --username=myUser --dbname=mySourceDB | pg_restore -v --no-owner --host=myTargetServer --port=5432 --username=myUser --dbname=myTargetDB
    ```

    たとえば、次のように入力します。

    ```azurecli-interactive
    pg_dump -Fc -v --host=pg-95.postgres.database.azure.com --port=5432 --username=pg@pg-95 --dbname=bench5gb | pg_restore -v --no-owner --host=pg-11.postgres.database.azure.com --port=5432 --username=pg@pg-11 --dbname=bench5gb
    ```  
3. アップグレード (移行) プロセスが完了したら、ターゲット サーバーでアプリケーションをテストできます。 
4. サーバー内のすべてのデータベースについて、このプロセスを繰り返します。

 次の表は、この方法を使用するアップグレードにかかる時間を示します。 データは、[pgbench](https://www.postgresql.org/docs/10/pgbench.html) を使用して設定されます。 データベースには、pgbench で生成されたテーブルやインデックスとは異なるサイズのオブジェクトが多数存在する可能性があるため、データベースのアップグレードにかかる実際の時間を把握するには、データベースのダンプと復元をテストすることを強くお勧めします。 

| **データベースのサイズ** | **およその所要時間** | 
| ----- | ------ |
| 1 GB  | 1 - 2 分 |
| 5 GB | 8 - 10 分 |
| 10 GB | 15 ～ 20 分 |
| 50 GB | 1 - 1.5 時間 |
| 100 GB | 2.5 - 3 時間|
   
## <a name="method-2-upgrade-with-parallel-dump-and-restore"></a>方法 2:並列のダンプと復元を使用してアップグレードする 

この方法は、データベース内にいくつかの大きなテーブルがあり、そのデータベースのダンプと復元を並列処理する場合に役立ちます。 データベースのバックアップ ダンプを格納するのに十分なローカル ディスク ストレージが必要です。 このダンプと復元の並列プロセスを使用すると、移行またはアップグレード全体を完了するのに要する時間が短縮されます。 たとえば、1 - 1.5 時間かかっていた 50 GB の pgbench データベースの移行は、30 分未満で完了しました。

1. ソース サーバー内のデータベースごとに、ターゲット サーバーで対応するデータベースを作成します。

   ```bash
    psql "host=myTargetServer port=5432 dbname=postgres user=myuser password=###### sslmode=mySSLmode"
    postgresl> create database myDB;
   ```
   たとえば、次のように入力します。
    ```bash
    psql "host=pg-11.postgres.database.azure.com port=5432 dbname=postgres user=pg@pg-11 password=###### sslmode=require"

    postgres> create database bench5gb;
    postgres> \q
    ```

2. ジョブ数 = 4 (データベース内のテーブル数) を指定してディレクトリ形式で pg_dump コマンドを実行します。 より大きなコンピューティング レベルとより多くのテーブルを使用すると、これをより多くの数に増加できます。 その pg_dump によって、各ジョブの圧縮ファイルを格納するディレクトリが作成されます。

    ```bash
    pg_dump -Fd -v --host=sourceServer --port=5432 --username=myUser --dbname=mySourceDB -j 4 -f myDumpDirectory
    ```
    たとえば、次のように入力します。
    ```bash
    pg_dump -Fd -v --host=pg-95.postgres.database.azure.com --port=5432 --username=pg@pg-95 --dbname=bench5gb -j 4 -f dump.dir
    ```

3. 次に、ターゲット サーバーでバックアップを復元します。
    ```bash
    $ pg_restore -v --no-owner --host=myTargetServer --port=5432 --username=myUser --dbname=myTargetDB -j 4 myDumpDir
    ```
    たとえば、次のように入力します。
    ```bash
    $ pg_restore -v --no-owner --host=pg-11.postgres.database.azure.com --port=5432 --username=pg@pg-11 --dbname=bench5gb -j 4 dump.dir
    ```

> [!TIP]
> このドキュメントで説明するプロセスは、Azure Database for PostgreSQL - フレキシブル サーバーのアップグレードにも使用できます。これはプレビュー段階です。 主要な違いは、フレキシブル サーバーのターゲットの接続文字列に `@dbName` がないことです。  たとえば、ユーザー名が `pg` の場合、接続文字列の単一サーバーのユーザー名は `pg@pg-95` ですが、フレキシブル サーバーでは、`pg` だけが使用されます。

## <a name="next-steps"></a>次の手順

- ターゲット データベースの機能に問題がなければ、古いデータベース サーバーを削除できます。 
- ソース サーバーと同じデータベース エンドポイントを使用する場合、古いソース データベース サーバーを削除した後、古いデータベース サーバー名を使用して読み取りレプリカを作成できます。 安定した状態が確立されたら、レプリカを停止することができます。これにより、レプリカ サーバーが独立したサーバーに昇格されます。 詳細については、「[レプリケーション](./concepts-read-replicas.md)」を参照してください。
- これらのコマンドは､運用環境で使用する前にテスト環境でテスト､検証してください。
