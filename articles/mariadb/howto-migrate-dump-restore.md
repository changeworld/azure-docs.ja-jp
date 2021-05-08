---
title: ダンプと復元を使用した移行 - Azure Database for MariaDB
description: この記事では、mysqldump、MySQL Workbench、phpMyAdmin などのツールを使用して、Azure Database for MariaDB でデータベースをバックアップして復元する一般的な 2 つの方法について説明します。
author: savjani
ms.author: pariks
ms.service: mariadb
ms.subservice: migration-guide
ms.topic: how-to
ms.date: 2/27/2020
ms.openlocfilehash: 35b1e84bdf74afd9577c7c98f023179dd769cd66
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "105628222"
---
# <a name="migrate-your-mariadb-database-to-an-azure-database-for-mariadb-by-using-dump-and-restore"></a>ダンプと復元を使用した Azure Database for MariaDB への MariaDB データベースの移行

この記事では、Azure Database for MariaDB でデータベースをバックアップして復元する一般的な 2 つの方法について説明します。
- コマンドライン ツールを使用したダンプと復元 (mysqldump を使用) 
- phpMyAdmin を使用したダンプと復元

## <a name="prerequisites"></a>前提条件
データベースの移行を開始する前に、次の操作を実行します。
- [Azure Database for MariaDB サーバーを作成する - Azure portal](quickstart-create-mariadb-server-database-using-azure-portal.md)。
- [mysqldump](https://mariadb.com/kb/en/library/mysqldump/) コマンドライン ユーティリティをインストールする。
- ダンプと復元のコマンドを実行するための [MySQL Workbench](https://dev.mysql.com/downloads/workbench/) または別のサードパーティ MySQL ツールをダウンロードし、インストールする。

## <a name="use-common-tools"></a>一般的なツールの使用
MySQL Workbench や mysqldump などの一般的なユーティリティとツールを使用して、Azure Database for MariaDB にリモート接続し、データを復元します。 Azure Database for MariaDB に接続するために、インターネットに接続されたクライアント マシンでこれらのツールを使用します。 セキュリティのベスト プラクティスとして、SSL で暗号化された接続を使用します。 詳細については、[Azure Database for MariaDB での SSL 接続の構成](concepts-ssl-connection-security.md)に関する記事を参照してください。 Azure Database for MariaDB にデータを移行するとき、ダンプ ファイルをクラウドの特別な場所に移動する必要はありません。 

## <a name="common-uses-for-dump-and-restore"></a>ダンプと復元の一般的な使用法
いくつかの一般的なシナリオでは、mysqldump や mysqlpump などの MySQL ユーティリティを使用して、データベースをダンプし、Azure Database for MariaDB サーバーに読み込むことができます。 

- データベース全体を移行するときは、データベース ダンプを使用します。 この推奨事項は、大量のデータを移動するときや、ライブのサイトまたはアプリケーションのためにサービスの中断を最小限に抑える必要がある場合に有効です。 
-  Azure Database for MariaDB にデータを読み込むときは、データベース内のすべてのテーブルで InnoDB ストレージ エンジンが使用されていることを確認します。 Azure Database for MariaDB では InnoDB ストレージ エンジンのみがサポートされており、その他のストレージ エンジンはサポートされていません。 テーブルが他のストレージ エンジンで構成されている場合は、それらを InnoDB エンジン形式に変換した後、Azure Database for MariaDB に移行する必要があります。
   
   たとえば、MyISAM テーブルを使用する WordPress アプリまたは Web アプリがある場合、最初にそれらのテーブルを InnoDB 形式に移行することで変換し、その後、それらのテーブルを Azure Database for MariaDB に復元します。 `ENGINE=InnoDB` 句を使用して、新しいテーブルを作成する場合に使用するエンジンを設定し、データを互換性のあるテーブルに転送してから、それを復元します。 

   ```sql
   INSERT INTO innodb_table SELECT * FROM myisam_table ORDER BY primary_key_columns
   ```
- データベースのダンプ時に互換性の問題を回避するために、ダンプ元とダンプ先のシステムで同じバージョンの MariaDB が使用されていることを確認します。 たとえば、既存の MariaDB サーバーがバージョン 10.2 の場合は、バージョン 10.2 を実行するように構成された Azure Database for MariaDB に移行する必要があります。 `mysql_upgrade` コマンドは、Azure Database for MariaDB サーバーでは機能しないため、サポートされていません。 MariaDB のバージョン間でアップグレードする必要がある場合、最初に独自の環境で以前のバージョンのデータベースを新しいバージョンの MariaDB にダンプまたはエクスポートします。 その後、Azure Database for MariaDB に移行する前に、`mysql_upgrade` を実行できます。

## <a name="performance-considerations"></a>パフォーマンスに関する考慮事項
大規模なデータベースをダンプするとき、パフォーマンスを最適化するために、次の考慮事項に留意してください。
-   mysqldump で `exclude-triggers` オプションを使用します。 データの復元中にトリガー コマンドが実行されないように、ダンプ ファイルからトリガーを除外します。 
-   `single-transaction` オプションを使用してトランザクション分離モードを REPEATABLE READ に設定し、データをダンプする前に START TRANSACTION SQL ステートメントをサーバーに送信します。 1 つのトランザクション内で多数のテーブルをダンプすると、復元中に余分なストレージが使用されます。 `single-transaction` オプションと `lock-tables` オプションは同時に使用できません。 これは、LOCK TABLES によって、保留中のトランザクションが暗黙的にコミットされるためです。 大きなテーブルをダンプするには、`single-transaction` オプションと `quick` オプションを組み合わせてください。 
-   複数の値リストを含む複数行の構文 `extended-insert` を使用します。 この方法によって、ダンプ ファイルが小さくなり、ファイルの再読み込み時に挿入が高速化されます。
-  データベースをダンプするときに、mysqldump で `order-by-primary` オプションを使用します。これにより、主キー順にデータがスクリプト化されます。
-   データをダンプするときに、mysqldump で `disable-keys` オプションを使用して、読み込み前に外部キー制約を無効にします。 外部キーのチェックを無効にすると、パフォーマンスが向上します。 読み込み後に制約を有効にし、データを検証して参照整合性を確認します。
-   パーティション テーブルを適宜使用します。
-   データを並列で読み込みます。 リソースの上限に達するような過剰な並列処理を避け、Azure portal で使用可能なメトリックを使用してリソースを監視します。 
-   データベースをダンプするときに、mysqlpump で `defer-table-indexes` オプションを使用します。これにより、テーブル データが読み込まれてからインデックスが作成されます。
-   バックアップ ファイルを Azure BLOB ストアにコピーし、そこから復元を実行します。 この方法は、インターネット経由で復元を実行するよりもはるかに高速です。

## <a name="create-a-backup-file"></a>バックアップ ファイルの作成

ローカル オンプレミス サーバーまたは仮想マシンで既存の MariaDB データベースをバックアップするには、mysqldump を使用して次のコマンドを実行します。 

```bash
$ mysqldump --opt -u <uname> -p<pass> <dbname> > <backupfile.sql>
```

指定するパラメーターは次のとおりです。
- *\<uname>* : データベース ユーザー名 
- *\<pass>* : データベースのパスワード (-p とパスワードの間にスペースはないので注意してください) 
- *\<dbname>* : データベースの名前 
- *\<backupfile.sql>* : データベース バックアップのファイル名 
- *\<--opt>* : mysqldump オプション 

たとえば、MariaDB サーバー上の、ユーザー名が *testuser* で、パスワードが設定されていない *testdb* という名前のデータベースを、testdb_backup.sql ファイルにバックアップするには、次のコマンドを使用します。 このコマンドは、`testdb` データベースを `testdb_backup.sql` というファイルにバックアップします。このファイルには、データベースを再作成するために必要なすべての SQL ステートメントが含まれます。 

```bash
$ mysqldump -u root -p testdb > testdb_backup.sql
```
データベース内のバックアップ対象のテーブルを選択するには、テーブル名をスペースで区切って指定します。 たとえば、*testdb* の table1 と table2 テーブルのみをバックアップするには、次のように指定します。 

```bash
$ mysqldump -u root -p testdb table1 table2 > testdb_tables_backup.sql
```

複数のデータベースを一度にバックアップするには、--database スイッチを使用して、データベース名をスペースで区切って指定します。 

```bash
$ mysqldump -u root -p --databases testdb1 testdb3 testdb5 > testdb135_backup.sql 
```

## <a name="create-a-database-on-the-target-server"></a>対象サーバーでのデータベースの作成
データを移行する Azure Database for MariaDB ターゲット サーバーに空のデータベースを作成します。 MySQL Workbench などのツールを使用して、データベースを作成します。 データベースは、ダンプされたデータが含まれるデータベースと同じ名前にすることも、別の名前でデータベースを作成することもできます。

接続するために、Azure Database for MariaDB の **[概要]** ペインで接続情報を見つけます。

![Azure portal の Azure Database for MariaDB サーバーの [概要] ペインのスクリーンショット。](./media/howto-migrate-dump-restore/1_server-overview-name-login.png)

MySQL Workbench で、接続情報を追加します。

![MySQL Workbench の [MySQL Connections]\(MySQL 接続\) ペインのスクリーンショット。](./media/howto-migrate-dump-restore/2_setup-new-connection.png)

## <a name="restore-your-mariadb-database"></a>MariaDB データベースの復元
ターゲット データベースを作成した後、mysql コマンドまたは MySQL Workbench を使用して、ダンプ ファイルから新しく作成されたデータベースにデータを復元できます。

```bash
mysql -h <hostname> -u <uname> -p<pass> <db_to_restore> < <backupfile.sql>
```

この例では、Azure Database for MariaDB ターゲット サーバー上の新しく作成されたデータベースにデータを復元します。

```bash
$ mysql -h mydemoserver.mariadb.database.azure.com -u myadmin@mydemoserver -p testdb < testdb_backup.sql
```

## <a name="export-your-mariadb-database-by-using-phpmyadmin"></a>phpMyAdmin を使用して MariaDB データベースをエクスポートする

エクスポートには、一般的なツールである phpMyAdmin を使用できます。これは、ローカル環境に既にインストールされている場合があります。 MariaDB データベースをエクスポートするには、次の操作を行います。
1. phpMyAdmin を開きます。
1. 左側のペインでデータベースを選択し、 **[エクスポート]** リンクを選択します。 新しいページが表示され、データベースのダンプが表示されます。
1. **[エクスポート]** 領域で **[すべて選択]** リンクを選択して、データベース内のテーブルを選択します。 
1. **[SQL options]\(SQL オプション\)** 領域で、適切なオプションを選択します。 
1. **[名前を付けて保存]** ファイル オプションと、対応する圧縮オプションを選択し、 **[実行]** を選択します。 プロンプトで、ファイルをローカルに保存します。

## <a name="import-your-database-by-using-phpmyadmin"></a>phpMyAdmin を使用してデータベースをインポートする

インポート プロセスは、エクスポート プロセスに似ています。 次の操作を行います。
1. phpMyAdmin を開きます。 
1. phpMyAdmin セットアップ ページで、 **[追加]** を選択して Azure Database for MariaDB サーバーを追加します。 
1. 接続の詳細とログイン情報を入力します。
1. 適切な名前のデータベースを作成し、左側のペインでそれを選択します。 既存のデータベースを書き換えるには、データベース名を選択して、テーブル名の横のチェック ボックスをすべてオンにし、 **[ドロップ]** を選択して既存のテーブルを削除します。 
1. **[SQL]** リンクを選択してページを表示します。ここで、SQL コマンドを入力したり、SQL ファイルをアップロードしたりできます。 
1. **[参照]** ボタンを選択して、データベース ファイルを見つけます。 
1. **[実行]** ボタンを選択して、バックアップをエクスポートし、SQL コマンドを実行し、データベースを再作成します。

## <a name="next-steps"></a>次のステップ
- [Azure Database for MariaDB にアプリケーションを接続する](./howto-connection-string.md)。
