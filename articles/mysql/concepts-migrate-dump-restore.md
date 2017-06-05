---
title: "Azure Database for MySQL でのダンプと復元を使用した MySQL データベースの移行 | Microsoft Docs"
description: "Azure Database for MySQL の移行について紹介します。"
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonh
ms.assetid: 
ms.service: mysql-database
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: portal
ms.date: 05/17/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 95b8c100246815f72570d898b4a5555e6196a1a0
ms.openlocfilehash: c0029e025cf6d0af478d1f21dc6acc7860905a81
ms.contentlocale: ja-jp
ms.lasthandoff: 05/18/2017

---

# <a name="migrate-your-mysql-database-to-azure-database-for-mysql-using-dump-and-restore"></a>ダンプと復元を使用した Azure Database for MySQL への MySQL データベースの移行
この記事では、Azure Database for MySQL でデータベースをバックアップして復元する一般的な 2 つの方法について説明します
- コマンド ラインからバックアップと復元を行う (mysqldump を使用) 
- PowerShell を使用してバックアップと復元を行う 

## <a name="before-you-begin"></a>開始する前に
このハウツー ガイドの手順を実行するには、以下が必要です。
- [Azure Database for MySQL サーバー - Azure Portal を使用して作成](quickstart-create-mysql-server-database-using-azure-portal.md)
- コンピューターにインストールされている [mysqldump](https://dev.mysql.com/doc/refman/5.7/en/mysqldump.html) コマンド ライン ユーティリティ
- MySQL Workbench ([MySQL Workbench のダウンロード](https://dev.mysql.com/downloads/workbench/))、Toad、Navicat、またはサード パーティ製の MySQL ツール

## <a name="use-common-tools"></a>一般的なツールの使用
MySQL Workbench、mysqldump、Toad、Navicat などの一般的なツールを使用して、Azure Database for MySQL にリモートで接続し、データを復元します。 こうしたツールは、インターネットに接続されたクライアント コンピューターで、Azure Database for MySQL に接続するときに使用します。 SSL で暗号化された接続を使ってセキュリティを強化するために、[Azure Database for MySQL での SSL 接続の構成](concepts-ssl-connection-security.md)に関するページもご覧ください。 Azure Database for MySQL に移行するとき、ダンプ ファイルをクラウドの特別な場所に移動する必要はありません。 

## <a name="create-a-backup-file-from-the-command-line-using-mysqldump"></a>mysqldump を使用したコマンド ラインからのバックアップ ファイルの作成
オンプレミスまたは VM の既存の MySQL データベースをバックアップするには、次のコマンドを実行します。 
```bash
$ mysqldump --opt -u [uname] -p[pass] [dbname] > [backupfile.sql]
```

指定するパラメーターは次のとおりです。
- [uname]: データベースのユーザー名 
- [pass]: データベースのパスワード (-p とパスワードの間にスペースがありません) 
- [dbname]: データベースの名前 
- [backupfile.sql]: データベース バックアップのファイル名 
- [--opt]: mysqldump オプション 

たとえば、ユーザー名が "testuser" で、パスワードが設定されていない "testdb" という名前のデータベースを、testdb_backup.sql ファイルにバックアップするには、次のコマンドを使用します。 このコマンドにより、"testdb" データベースが、testdb_backup.sql というファイルにバックアップされます。このファイルには、データベースの再作成に必要なすべての SQL ステートメントが追加されます。 

```bash
$ mysqldump -u root -p testdb > testdb_backup.sql
```
バックアップするデータベースのテーブルを選択するには、テーブル名をスペースで区切って指定します。 たとえば、"testdb" の table1 テーブルと table2 テーブルだけをバックアップするには、次のように指定します。 
```bash
$ mysqldump -u root -p testdb table1 table2 > testdb_tables_backup.sql
```

複数のデータベースを一度にバックアップするには、--database スイッチを使用して、データベース名をスペースで区切って指定します。 
```bash
$ mysqldump -u root -p --databases testdb1 testdb3 testdb5 > testdb135_backup.sql 
```
サーバーのすべてのデータベースを一度にバックアップするには、--all databases オプションを使用します。
```
$ mysqldump -u root -p --all-databases > alldb_backup.sql 
```

## <a name="create-a-database-on-the-target-azure-mysql-server"></a>対象 Azure MySQL サーバーでのデータベースの作成
MySQL Workbench、Toad、Navicat、または MySQL 用のサード パーティ製ツールを使用して、データの移行先 Azure Database for MySQL サーバー上に空のデータベースを作成する必要があります。 データベースの名前は、ダンプされたデータが含まれるデータベースと同じにすることも、別の名前でデータベースを作成することもできます。

![Azure Database for MySQL の接続文字列](./media/concepts-migrate-import-export/p5.png)

![MySQL Workbench の接続文字列](./media/concepts-migrate-import-export/p4.png)


## <a name="restore-your-mysql-database-using-command-line-or-mysql-workbench"></a>コマンド ラインまたは MySQL Workbench を使用した MySQL データベースの復元
ターゲット データベースを作成したら、mysql コマンドまたは MySQL Workbench を使用して、ダンプ ファイルから新しく作成された特定のデータベースにデータを復元できます。
```bash
mysql -u [uname] -p[pass] [db_to_restore] < [backupfile.sql]
```
次の例では、対象サーバーで新しく作成された testdb3 データベースにデータを復元します。
```bash
$ mysql -u root -p testdb3 < testdb_backup.sql
```

## <a name="export-using-phpmyadmin"></a>PHPMyAdmin を使用したエクスポート
エクスポートには一般的なツールである phpMyAdmin を使用できます。このツールは、既にローカル環境にインストールされている可能性があります。 PHPMyAdmin を使用して MySQL データベースをエクスポートするには、次の操作を行います。
- phpMyAdmin を開きます。
- 画面の左側にある一覧でデータベース名をクリックして、データベースを選択します。 
- [エクスポート] リンクをクリックします。 これにより、新しい [View dump of database (データベースのダンプの表示)] 画面が表示されます。 
- [エクスポート] 領域で [Select All link (すべてのリンクを選択)] をクリックして、データベース内のすべてのテーブルを選択します。 
- [SQL options (SQL オプション)] 領域で、適切なオプションをクリックします。 
- [名前を付けて保存] ファイル オプションと、適切な圧縮オプションをクリックして、[移動] をクリックします。 ファイルをローカルに保存するよう求めるダイアログ ボックスが表示されます。

## <a name="import-using-phpmyadmin"></a>PHPMyAdmin を使用したインポート
データベースのインポート操作は、エクスポートと似ています。 次の操作を実行してください。
- phpMyAdmin を開きます。 
- データベースを作成して適切な名前を付けたら、画面の左側にある一覧でそのデータベース名をクリックして選択します。 既存のデータベースをインポートによって上書きする場合は、データベース名をクリックし、テーブル名の横にあるすべてのチェック ボックスをオンにして [ドロップ] を選択します。これにより、データベース内のすべての既存のテーブルが削除されます。 
- SQL リンクをクリックします。 新しい画面が表示され、ここで SQL コマンドを入力するか、SQL ファイルをアップロードできます。 
- 参照ボタンを使用して、データベース ファイルを検索します。 
- [移動] ボタンをクリックします。 これによりバックアップがエクスポートされ、SQL コマンドが実行された後、データベースが再作成されます。

## <a name="next-steps"></a>次のステップ

[Azure Portal を使用した Azure Database for MySQL サーバーの作成](quickstart-create-mysql-server-database-using-azure-portal.md) 
[Azure CLI を使用した Azure Database for MySQL サーバーの作成](quickstart-create-mysql-server-database-using-azure-cli.md)

