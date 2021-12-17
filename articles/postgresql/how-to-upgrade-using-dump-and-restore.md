---
title: ダンプと復元を使用したアップグレード - Azure Database for PostgreSQL
description: データベースのダンプと復元を使用したオフライン アップグレード方法による、上位バージョンの Azure Database for PostgreSQL への移行について説明します。
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 09/21/2021
ms.openlocfilehash: b2216754cbdb6081a82f71392aee6e5f8ce2d3ba
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128648413"
---
# <a name="upgrade-your-postgresql-database-using-dump-and-restore"></a>ダンプと復元を使用した PostgreSQL データベースのアップグレード

>[!NOTE]
> このドキュメントで説明する概念は、Azure Database for PostgreSQL (単一サーバー) と Azure Database for PostgreSQL (フレキシブル サーバー (プレビュー)) の両方に適用されます。 

次の方法を使用してデータベースを上位のメジャー バージョンへ移行し、Azure Database for PostgreSQL にデプロイされている PostgreSQL サーバーをアップグレードできます。
* PostgreSQL [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) と [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html) を使用した **オフライン** の方法。データを移行するためのダウンタイムが発生します。 このドキュメントでは、このアップグレードまたは移行の方法について説明します。
* [Database Migration Service](../dms/tutorial-azure-postgresql-to-azure-postgresql-online-portal.md) (DMS) を使用した **オンライン** の方法。 この方法では、移行におけるダウンタイムが短縮され、ターゲット データベースとソースの同期が維持され、カットオーバーのタイミングを選択できます。 ただし、DMS を使用するために対処する必要があるいくつかの前提条件と制限があります。 詳細については、[DMS のドキュメント](../dms/tutorial-azure-postgresql-to-azure-postgresql-online-portal.md)をご覧ください。 

 次の表に、データベースのサイズとシナリオに基づく推奨事項をいくつか示します。

| **データベース/シナリオ** | **ダンプ/復元 (オフライン)** | **DMS (オンライン)** |
| ------ | :------: | :-----: |
| データベースが小さく、アップグレードするためのダウンタイムを許容できる  | X | |
| 小規模なデータベース (10 GB 未満)  | X | X | 
| 中小規模の DB (10 GB から 100 GB) | X | X |
| 大規模なデータベース (100 GB より大きい) |  | X |
| アップグレードするためのダウンタイムを許容できる (データベースのサイズに関係なく) | X |  |
| 再起動を含む DMS の[前提条件](../dms/tutorial-azure-postgresql-to-azure-postgresql-online-portal.md#prerequisites)に対処できるか? |  | X |
| アップグレード プロセス中に DDL とログに記録されていないテーブルを回避できるか? | |  X |

このガイドでは、オフラインの移行方法と例を示して、ソース サーバーから、上位バージョンの PostgreSQL を実行するターゲット サーバーへの移行方法を説明します。

> [!NOTE]
> PostgreSQL のダンプと復元は、さまざまな方法で実行できます。 このガイドで説明されているいずれかの方法を使用して移行することも、ニーズに適した別の方法を選択することもできます。 ダンプと復元の詳細な構文と追加のパラメーターについては、[pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) および [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html) の記事を参照してください。 


## <a name="prerequisites-for-using-dump-and-restore-with-azure-database-for-postgresql"></a>Azure Database for PostgreSQL でダンプと復元を使用するための前提条件
 
この攻略ガイドの手順を実行するには、以下が必要です。

- アップグレードするエンジンの下位バージョンを実行している **移行元の** PostgreSQL データベース サーバー。
- 目的のメジャー バージョンの [Azure Database for PostgreSQL サーバー (単一サーバー)](quickstart-create-server-database-portal.md) または [Azure Database for PostgreSQL (フレキシブル サーバー)](./flexible-server/quickstart-create-server-portal.md) を実行している **移行先の** PostgreSQL データベース サーバー。 
- ダンプおよび復元コマンドを実行する PostgreSQL クライアント システム。 できるだけ新しいバージョンのデータベースを使用することをお勧めします。 たとえば、PostgreSQL バージョン 9.6 から 11 にアップグレードする場合は、PostgreSQL バージョン 11 クライアントを使用してください。 
  - PostgreSQL がインストールされており、[pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) および [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html) コマンドライン ユーティリティがインストールされている Linux または Windows クライアントを使用できます。 
  - または、[Azure Cloud Shell](https://shell.azure.com) を使用するか、[Azure portal](https://portal.azure.com) の右上にあるメニュー バーで Azure Cloud Shell をクリックすることもできます。 ダンプおよび復元コマンドを実行する前に、ご利用のアカウントにログインする (`az login`) 必要があります。
- PostgreSQL クライアントが、ソースおよびターゲット サーバーと同じリージョンで実行されていることが推奨されます。 


## <a name="additional-details-and-considerations"></a>追加情報と考慮事項
- portal で [接続文字列] をクリックして、ソースおよびターゲットのデータベースへの接続文字列を見つけることができます。 
- サーバー内で複数のデータベースを実行している可能性があります。 データベースの一覧を確認するには、ソース サーバーに接続し、`\l`を実行します。
- 対応するデータベースをターゲット データベース サーバーに作成するか、データベースを作成する `pg_dump` コマンドに `-C` オプションを追加します。
- `azure_maintenance` とテンプレート データベースはアップグレードしないでください。 既にテンプレート データベースに変更を加えた場合は、その変更を移行するか、またはターゲット データベースにその変更を加えるかを選択できます。
- 上記の表を参照して、データベースがこの移行モードに適しているかどうかを判断します。
- Azure Cloud Shell を使用する場合、20 分後にセッションがタイムアウトすることに注意してください。 データベース サイズが 10 GB 未満の場合、セッションがタイムアウトすることなくアップグレーを完了できる可能性があります。それ以外の場合は、10 から 15 分に 1 回、キーを押すなど、他の方法でセッションを開いたままにすることが必要なことがあります。 


## <a name="example-database-used-in-this-guide"></a>このガイドで使用されるデータベース例

このガイドでは、次のソースおよびターゲット サーバーとデータベース名を使用して、例を使って説明します。

 | **説明** | **Value** |
 | ------- | ------- |
 | ソース サーバー (v9.5) | pg-95.postgres.database.azure.com |
 | [同期元データベース] | bench5gb |
 | ソース データベースのサイズ | 5 GB |
 | ソース ユーザー名 | pg@pg-95 |
 | ターゲット サーバー (v11) | pg-11.postgres.database.azure.com |
 | 対象になるデータベース | bench5gb |
 | ターゲット ユーザー名 | pg@pg-11 |

>[!NOTE]
> フレキシブル サーバーでは、PostgreSQL バージョン 11 以降がサポートされます。 また、フレキシブル サーバーのユーザー名に @dbservername は必要ありません。

## <a name="upgrade-your-databases-using-offline-migration-methods"></a>オフラインの移行方法を使用してデータベースをアップグレードする
このセクションで説明するいずれかの方法を選択してアップグレードできます。 タスクを実行する際に次のヒントを使用できます。

- ソースとターゲットのデータベースに同じパスワードを使用する場合、`PGPASSWORD=yourPassword` 環境変数を設定できます。  この場合、psql、pg_dump、pg_restore などのコマンドを実行するたびにパスワードを入力する必要はありません。  同様に、`PGUSER`、`PGSSLMODE` などの追加変数も設定できます。[PostgreSQL の環境変数](https://www.postgresql.org/docs/11/libpq-envars.html)に関するページを参照してください。
  
- お使いの PostgreSQL サーバーで TLS または SSL 接続を必要とする場合 (Azure Database for PostgreSQL サーバーでは既定でオン) は、pg_restore ツールが TLS で接続するように、環境変数 `PGSSLMODE=require` を設定します。 TLS を使用しない場合、エラー `FATAL:  SSL connection is required. Please specify SSL options and retry.` が表示される場合があります。

- Windows コマンド ラインで、コマンド `SET PGSSLMODE=require` を実行してから、pg_restore コマンドを実行します。 Linux または Bash では、コマンド `export PGSSLMODE=require` を実行してから、pg_restore コマンドを実行します。

>[!Important]
> このドキュメントで紹介する手順や方法は、あくまで pg_dump コマンドと pg_restore コマンドの例を示すためのものです。アップグレードを実行する方法は他にもあります。 これらのコマンドは、運用環境で使用する前にテスト環境でテスト、検証することをお勧めします。

### <a name="migrate-the-roles"></a>ロールを移行する

ロール (ユーザー) はグローバル オブジェクトであり、データベースの復元前に、新しいクラスターに個別に移行する必要があります。 それらは、`pg_dumpall` バイナリと -r (--roles-only) オプションを使用してダンプできます。
ソース サーバーからすべてのロールをダンプするには:

```azurecli-interactive
pg_dumpall -r --host=mySourceServer --port=5432 --username=myUser --database=mySourceDB > roles.sql
```

ターゲット サーバーで psql を使用してコンテンツを復元する前に、`roles.sql` を編集し、`NOSUPERUSER` および `NOBYPASSRLS` の参照を削除します。

```azurecli-interactive
psql -f roles.sql --host=myTargetServer --port=5432 --username=myUser
```

ダンプ スクリプトは、エラーなく完全に実行されることを期待してはいけません。 特に、スクリプトはソース クラスタに存在するすべてのロールについて CREATE ROLE を発行するため、azure_pg_admin や azure_superuser のようなブートストラップのスーパーユーザについては "ロールが既に存在します" という内容のエラーが確実に発生します。 このエラーは無害であり、無視してかまいません。 無害なエラー メッセージはそれだけではありません。`--clean` オプションを使用すると、オブジェクトが存在しないことについてのエラー メッセージが高い確率で発生します。ただし、このエラーは、`--if-exists` を追加することで最小限に抑えることができます。


### <a name="method-1-using-pg_dump-and-psql"></a>方法 1: pg_dump と psql を使用する

この方法には、次の 2 つの手順があります。 まず、`pg_dump` を使用してソース サーバーから SQL ファイルをダンプします。 次に、`psql` を使用してターゲット サーバーにファイルをインポートします。 詳細については、[エクスポートとインポートを使用した移行](howto-migrate-using-export-and-import.md)に関するドキュメントを参照してください。

### <a name="method-2-using-pg_dump-and-pg_restore"></a>方法 2: pg_dump と pg_restore を使用する

このアップグレード方法ではまず、`pg_dump` を使用してソース サーバーからダンプを作成します。 次に、`pg_restore` を使用して、そのダンプ ファイルをターゲット サーバーに復元します。 詳細については、[ダンプと復元を使用した移行](howto-migrate-using-dump-and-restore.md)に関するドキュメントを参照してください。 

### <a name="method-3-using-streaming-the-dump-data-to-the-target-database"></a>方法 3: ターゲット データベースへのダンプ データのストリーム配信を使用する

PostgreSQL クライアントがない場合、または Azure Cloud Shell を使用する場合は、この方法を使用できます。 ターゲット データベース サーバーにデータベース ダンプが直接ストリーミングされ、ダンプはクライアントに保存されません。 このため、これは、記憶域に制限があるクライアントで使用できます。また、Azure Cloud Shell からも実行できます。 

1. `\l` コマンドを使用してターゲット サーバー内にデータベースが存在していることを確認します。 データベースが存在しない場合は、作成します。
   ```azurecli-interactive
    psql "host=myTargetServer port=5432 dbname=postgres user=myUser password=###### sslmode=mySSLmode"
    ```
    ```SQL
    postgres> \l   
    postgres> create database myTargetDB;
   ```

2. パイプを使用して、ダンプと復元を単一のコマンド ラインとして実行します。 
    ```azurecli-interactive
    pg_dump -Fc --host=mySourceServer --port=5432 --username=myUser --dbname=mySourceDB | pg_restore  --no-owner --host=myTargetServer --port=5432 --username=myUser --dbname=myTargetDB
    ```

    たとえば、次のように入力します。

    ```azurecli-interactive
    pg_dump -Fc --host=pg-95.postgres.database.azure.com --port=5432 --username=pg@pg-95 --dbname=bench5gb | pg_restore --no-owner --host=pg-11.postgres.database.azure.com --port=5432 --username=pg@pg-11 --dbname=bench5gb
    ```  
3. アップグレード (移行) プロセスが完了したら、ターゲット サーバーでアプリケーションをテストできます。 
4. サーバー内のすべてのデータベースについて、このプロセスを繰り返します。

 例として、ストリーミング ダンプ方法を使用して移行にかかった時間を次の表に示します。 サンプル データは、[pgbench](https://www.postgresql.org/docs/10/pgbench.html) を使用して設定されます。 データベースには、pgbench で生成されたテーブルやインデックスとは異なるサイズのオブジェクトが多数存在する可能性があるため、データベースのアップグレードにかかる実際の時間を把握するには、データベースのダンプと復元をテストすることを強くお勧めします。 

| **データベースのサイズ** | **およその所要時間** | 
| ----- | ------ |
| 1 GB  | 1 - 2 分 |
| 5 GB | 8 - 10 分 |
| 10 GB | 15 ～ 20 分 |
| 50 GB | 1 - 1.5 時間 |
| 100 GB | 2.5 - 3 時間|
   
### <a name="method-4-using-parallel-dump-and-restore"></a>方法 4: ダンプと復元の並列処理を使用する 

この方法は、データベース内にいくつかの大きなテーブルがあり、そのデータベースのダンプと復元を並列処理する場合に検討してください。 また、クライアント システムに、バックアップ ダンプを格納するための十分なストレージが必要です。 このダンプと復元の並列プロセスを使用すると、移行全体を完了するのに要する時間が短縮されます。 たとえば、方法 1 と 2 を使用して 1 から 1.5 時間かかっていた 50 GB の pgbench データベースの移行は、この方法を使用すると 30 分未満で完了しました。

1. ソース サーバー内のデータベースごとに、ターゲット サーバーで対応するデータベースを作成します。

    ```azurecli-interactive
    psql "host=myTargetServer port=5432 dbname=postgres user=myuser password=###### sslmode=mySSLmode"
    ```

    ```SQL
    postgres> create database myDB;
   ```

   たとえば、次のように入力します。
    ```bash
    psql "host=pg-11.postgres.database.azure.com port=5432 dbname=postgres user=pg@pg-11 password=###### sslmode=require"
    psql (12.3 (Ubuntu 12.3-1.pgdg18.04+1), server 13.3)
    SSL connection (protocol: TLSv1.3, cipher: TLS_AES_256_GCM_SHA384, bits: 256, compression: off)
    Type "help" for help.

    postgres> create database bench5gb;
    postgres> \q
    ```

2. ジョブ数 = 4 (データベース内のテーブル数) を指定してディレクトリ形式で pg_dump コマンドを実行します。 より大きなコンピューティング レベルとより多くのテーブルを使用すると、これをより多くの数に増加できます。 その pg_dump によって、各ジョブの圧縮ファイルを格納するディレクトリが作成されます。

    ```azurecli-interactive
    pg_dump -Fd -v --host=sourceServer --port=5432 --username=myUser --dbname=mySourceDB -j 4 -f myDumpDirectory
    ```
    たとえば、次のように入力します。
    ```bash
    pg_dump -Fd -v --host=pg-95.postgres.database.azure.com --port=5432 --username=pg@pg-95 --dbname=bench5gb -j 4 -f dump.dir
    ```

3. 次に、ターゲット サーバーでバックアップを復元します。
    ```azurecli-interactive
    $ pg_restore -v --no-owner --host=myTargetServer --port=5432 --username=myUser --dbname=myTargetDB -j 4 myDumpDir
    ```
    たとえば、次のように入力します。
    ```bash
    $ pg_restore -v --no-owner --host=pg-11.postgres.database.azure.com --port=5432 --username=pg@pg-11 --dbname=bench5gb -j 4 dump.dir
    ```

> [!TIP]
> このドキュメントで説明するプロセスは、Azure Database for PostgreSQL - フレキシブル サーバーのアップグレードにも使用できます。これはプレビュー段階です。 主要な違いは、フレキシブル サーバーのターゲットの接続文字列に `@dbName` がないことです。  たとえば、ユーザー名が `pg` の場合、接続文字列の単一サーバーのユーザー名は `pg@pg-95` ですが、フレキシブル サーバーでは、`pg` だけが使用されます。

## <a name="post-upgrademigrate"></a>アップグレード後/移行
メジャー バージョンのアップグレードが完了したら、各データベースで `ANALYZE` コマンドを実行して、`pg_statistic` テーブルを更新することをお勧めします。 そうしなければ、パフォーマンスの問題が発生する可能性があります。

```SQL
postgres=> analyze;
ANALYZE
```

## <a name="next-steps"></a>次のステップ

- ターゲット データベースの機能に問題がなければ、古いデータベース サーバーを削除できます。 
- Azure Database for PostgreSQL (単一サーバーのみ)。 ソース サーバーと同じデータベース エンドポイントを使用する場合、古いソース データベース サーバーを削除した後、古いデータベース サーバー名を使用して読み取りレプリカを作成できます。 レプリケーションの安定した状態が確立されたら、レプリカを停止できます。これにより、レプリカ サーバーが独立したサーバーに昇格されます。 詳細については、「[レプリケーション](./concepts-read-replicas.md)」を参照してください。

>[!Important] 
> アップグレード後の新しいバージョンの PostgreSQL は、運用環境で直接使用する前にテストすることを強くお勧めします。 たとえば、以前のソースのバージョン ソースと新しいバージョン ターゲットとでサーバーのパラメーターを比較します。 それらが同じであることを確認すると共に、新バージョンに追加された新しいパラメーターがあればチェックします。 バージョン間の違いについては、[こちら](https://www.postgresql.org/docs/release/)を参照してください。
