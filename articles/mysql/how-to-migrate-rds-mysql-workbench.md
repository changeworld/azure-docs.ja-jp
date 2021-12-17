---
title: MySQL Workbench を使用して、Amazon RDS for MySQL を Azure Database for MySQL に移行する
description: この記事では、MySQL Workbench Migration Wizard を使用して、Amazon RDS for MySQL を Azure Database for MySQL に移行する方法を説明します。
author: HJToland3
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 05/21/2021
ms.openlocfilehash: c744517dfab088fa4edb104b2c5aebda2818ab8f
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/30/2021
ms.locfileid: "122652544"
---
# <a name="migrate-amazon-rds-for-mysql-to-azure-database-for-mysql-using-mysql-workbench"></a>MySQL Workbench を使用して、Amazon RDS for MySQL を Azure Database for MySQL に移行する

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]

Amazon RDS for MySQL を Azure Database for MySQL に移行するには、MySQL Workbench Export/Import、Azure Database Migration Service (DMS)、MySQL のダンプと復元など、さまざまなユーティリティを使用できます。 ただし、Amazon RDS for MySQL を Azure Database for MySQL に移行するには、MySQL Workbench Migration Wizard を使用するのが簡単で便利です。

Migration Wizard を使用すれば、移行するスキームとオブジェクトを簡単に指定できます。 さらに、サーバーのログを見て、エラーとボトルネックをリアルタイムで把握できます。 そうしてエラーを発見したら、移行作業中にテーブル、データベース構造体またはオブジェクトを編集、変更することができ、最初からやり直すことなく移行を再開できます。

> [!NOTE]
> Migration Wizard を使用して、Microsoft SQL Server、Oracle、PostgreSQL、MariaDB など、それ以外のものを移動することもできますが、そのことについてはこの記事では説明しません。

## <a name="prerequisites"></a>前提条件

移行作業を始める前に、下で説明するいくつかのパラメーターと機能を適切に構成、設定できているか確かめることをお勧めします。

- ソースとターゲットのデータベースの文字セットが同じであることを確認します。
- インポートまたは移行するデータまたはワークロードの量に合わせて、タイムアウト時間を適切な長さに設定します。
- インポートまたは移行するデータベースのサイズに合わせて、`max_allowed_packet parameter` を適切な量に設定します。
- Azure Database for MySQL Server では InnoDB Storage エンジンだけをサポートしているため、すべてのテーブルで InnoDB を使用していることを確認してください。
- ルート ユーザーまたはスーパーユーザー定義子 を含むすべてのトリガー、ストアド プロシージャ、その他の関数を削除、置換または変更してください (Azure Database for MySQL では Super ユーザーの権限をサポートしていません)。 重要なプロセスを実行している管理者ユーザーの名前で定義子 を置き換えるには、次のコマンドを実行します。

  ```
  DELIMITER; ;/*!50003 CREATE*/ /*!50017 DEFINER=`root`@`127.0.0.1`*/ /*!50003
  DELIMITER;
  /* Modified to */
  DELIMITER;
  /*!50003 CREATE*//*!50017 DEFINER=`AdminUserName`@`ServerName`*/ /*!50003
  DELIMITER;

  ```

- ユーザー定義関数 (UDF) をデータベース サーバーで実行している場合は、その mysql データベースに対する権限を削除する必要があります。 サーバーで UDF を実行しているかどうかを判断するには、次のクエリを使用します。

  ```
  SELECT * FROM mysql.func;
  ```

  UDF を実行していることが分かった場合は、次のクエリでその UDF を停止できます。

  ```
  DROP FUNCTION your_UDFunction;
  ```

- 特に非常に大規模なデータベースをエクスポートするときは、そのツールを実行しているサーバーとエクスポート先に、エクスポート作業を実行するのに十分なディスク領域と処理性能 (vCores と CPU とメモリ) があることを確認してください。
- ワークロードがファイアウォールなどのネットワーク セキュリティ層で隔てられている場合は、オンプレミスまたは AWS のインスタンスと Azure Database for MySQL の間に経路を作ります。

## <a name="begin-the-migration-process"></a>移行作業を開始する

1. 移行作業を開始するには、MySQL Workbench にサインインしてホームアイコンをクリックします。
2. 下のスクリーンショットに示すとおり、左側のナビゲーション バーにある Migration Wizard アイコンをクリックします。

    :::image type="content" source="./media/how-to-migrate-rds-mysql-workbench/begin-the-migration.png" alt-text="MySQL Workbench のスタート画面":::

   下に示すとおり、Migration Wizard の **[概要]** ページが表示されます。

    :::image type="content" source="./media/how-to-migrate-rds-mysql-workbench/migration-wizard-welcome.png" alt-text="MySQL Workbench Migration Wizard のウェルカム ページ":::

3. MySQL Server の ODBC ドライバーをインストールしてあるかどうか確認するため、 **[ODBC Administrator を開く]** をクリックします。

   この例では、 **[Drivers]** タブで、既に 2 つの MySQL Server ODBC ドライバーがインストールされていることが分かります。

    :::image type="content" source="./media/how-to-migrate-rds-mysql-workbench/obdc-administrator-page.png" alt-text="[ODBC Data Source Administrator] ページ":::

   MySQL ODBC ドライバーがインストールされていない場合は、MySQL Workbench のインストールに使用した MySQL Installer を用いてドライバーをインストールします。 MySQL ODBC ドライバーのインストールの詳細は、次の参考ページをご覧ください。

   - [MySQL :: MySQL Connector/ODBC Developer Guide :: 4.1 Installing Connector/ODBC on Windows](https://dev.mysql.com/doc/connector-odbc/en/connector-odbc-installation-binary-windows.html) (MySQL :: MySQL Connector/ODBC 開発者ガイド :: 4.1 Connecter/ODBC を Windows にインストールする)
   - [ODBC Driver for MySQL: How to Install and Set up Connection (Step-by-step) – {coding}Sight (codingsight.com)](https://codingsight.com/install-and-configure-odbc-drivers-for-mysql/) (MySQL のODBC ドライバー: Connection をインストール、設定する方法 (ステップ バイ ステップ) – {coding}Sight (codingsight.com))

4. **[ODBC Data Source Administrator]** ダイアログ ボックスを閉じ、移行作業を続けます。

## <a name="configure-source-database-server-connection-parameters"></a>ソース データベース サーバーの接続パラメーターを構成する

1. **[概要]** ページで **[移行開始]** をクリックします。

   **[ソース選択]** ページが表示されます。 このページで、ソースの RDBMS と接続のパラメーターを確認します。

2. **[データベース システム]** 欄で **MySQL** を選択します。
3. **[保存された接続]** 欄で、その RDBMS の保存された構成設定の 1 つを選択します。

   ページ下部のチェックボックスに印を入れて好きな名前を入力することで、接続を保存できます。

4. **[接続方法]** 欄で **[標準 TCP/IP]** を選択します。
5. **[ホスト名]** 欄で、ソース データベース サーバーの名前を指定します。
6. **[ポート]** 欄で、**3306** を指定し、サーバーに接続するためのユーザー名とパスワードを入力します。
7. **[データベース]** 欄では、移行するデータベースの名前が分かる場合はそれを入力します。分からなければ、この欄は空白にします。
8. **[接続のテスト]** をクリックして、MySQL Server インスタンスへの接続を確認します。

   正確なパラメーターが入力できていれば、接続の成功を示すメッセージが表示されます。

    :::image type="content" source="./media/how-to-migrate-rds-mysql-workbench/source-connection-parameters.png" alt-text="ソース データベース接続パラメーター ページ":::

9. **[次へ]** を選択します。

## <a name="configure-target-database-server-connection-parameters"></a>ターゲット データベース サーバーの接続パラメーターを構成する

1. **[ターゲット選択]** ページで、ソース サーバーへの接続設定と同様の手順により、移行先の MySQL Server インスタンスに接続するためのパラメーターを設定します。
2. 接続の成功を確認するために、 **[接続のテスト]** をクリックします。

    :::image type="content" source="./media/how-to-migrate-rds-mysql-workbench/target-connection-parameters.png" alt-text="[ターゲット データベース接続パラメーター] ページ":::

3. **[次へ]** を選択します。

## <a name="select-the-schemas-to-migrate"></a>移行するスキーマを選択する

Migration Wizard では、MySQL Server インスタンスと通信し、スキームのリストをソース サーバーから取得します。

1. **[ログの表示]** をクリックしてこの操作の内容を表示ます。

   下のスクリーンショットに、ソース データベース サーバーからスキーマを取得する様子が表示されています。

    :::image type="content" source="./media/how-to-migrate-rds-mysql-workbench/retrieve-schemas.png" alt-text="[スキーマ リスト取得] ページ":::

2. **[次へ]** をクリックして、すべてのスキーマが取得できたことを確認します。

   下のスクリーンショットに、取得したスキーマのリストが表示されています。

    :::image type="content" source="./media/how-to-migrate-rds-mysql-workbench/schemas-selection.png" alt-text="[スキーマ選択] ページ":::

   このリストに表示されるスキーマだけを移行できます。

3. 移行するスキーマを選んで **[次へ]** をクリックします。

## <a name="object-migration"></a>オブジェクトの移行

次に、移行するオブジェクトを指定します。

1. **[選択したものを表示する]** をクリックしてから、 **[選択可能オブジェクト]** で、移行するオブジェクトを選んで追加します。

   オブジェクトを追加すると、下のスクリーンショットに示すとおり、 **[移行するオブジェクト]** にそれが表示されます。

    :::image type="content" source="./media/how-to-migrate-rds-mysql-workbench/source-objects.png" alt-text="[ソース オブジェクト選択] ページ":::

   ここでは、すべてのテーブル オブジェクトを選択しています。

2. **[次へ]** を選択します。

## <a name="edit-data"></a>データを編集する

このセクションでは、移行するオブジェクトを編集できます。

1. **[手動編集]** ページでは、右上に **[表示]** ドロップダウン メニューがあることに注意してください。

    :::image type="content" source="./media/how-to-migrate-rds-mysql-workbench/manual-editing.png" alt-text="[手動編集選択] ページ":::

   **[表示]** ドロップダウン ボックスには 3 つの項目があります。

   - **すべてのオブジェクト** – すべてのオブジェクトを表示します。 このオプションを使用すれば、生成された SQL を、ターゲット データベース サーバーに使用する前に編集できます。 そのためには、オブジェクトを選んで [コードとメッセージを表示] をクリックします。 選択したオブジェクトに対して生成された MySQL コードを見る (そして編集する) ことができます。
   - **移行の問題** – 移行中に発生したすべての問題を表示します。問題は内容を確認できます。
   - **列マッピング** – 列マッピング情報を表示します。 この表示方法では、名前を編集し、ターゲット オブジェクトの列を変更できます。

2. **[次へ]** を選択します。

## <a name="create-the-target-database"></a>ターゲット データベースを作成する

1. **[ターゲット RDBMS にスキーマを作成する]** チェックボックスに印を入れます。

   既存のスキーマを変更、更新せずに維持することもできます。

    :::image type="content" source="./media/how-to-migrate-rds-mysql-workbench/create-target-database.png" alt-text="[ターゲット作成オプション] ページ":::

   この記事ではターゲット RDBMS にスキーマを作成しますが、実際の移行では **[SQL スクリプト ファイルを作成する]** チェックボックスに印を入れることで、ローカル コンピューターにファイルを保存するなどのことができます。

2. **[次へ]** を選択します。

## <a name="run-the-mysql-script-to-create-the-database-objects"></a>MySQL スクリプトを実行してデータベース オブジェクトを作成する

この例ではターゲット RDBMS にスキーマを作成するので、移行した SQL スクリプトは、ターゲット MySQL サーバーで実行されます。 下のスクリーンショットに示すように、操作が進行する様子を見ることができます。

:::image type="content" source="./media/how-to-migrate-rds-mysql-workbench/create-schemas.png" alt-text="[スキーマの作成] ページ":::

1. スキーマを作成してオブジェクトを完成させたら、 **[次へ]** をクリックします。

   **[ターゲット作成結果]** ページには、次のスクリーンショットに示すとおり、作成したオブジェクトのリストと、それらの作成中に発生したすべてのエラーの通知が表示されます。

    :::image type="content" source="./media/how-to-migrate-rds-mysql-workbench/create-target-results.png" alt-text="[ターゲット作成結果] ページ":::

2. このページで詳細を確認し、すべてが意図したとおりに完了したことを確かめます。

   この記事の例では、エラーはありません。 対処するべきエラー メッセージがなければ、移行用スクリプトを編集できます。

3. **オブジェクト** 欄で、編集するオブジェクトを選択します。
4. **[選択したオブジェクトの SQL CREATE スクリプト]** で、SQL スクリプトを変更し、 **[適用]** をクリックして変更を保存します。
5. **[オブジェクトの再作成]** をクリックし、変更点を含むスクリプトを実行します。

   このスクリプトが失敗するときは、生成されたスクリプトを編集しなければならない場合があります。 それから手動で SQL スクリプトを修正し、すべてを再び実行できます。 この記事では、何も変更せずにスクリプトをそのままにします。

6. **[次へ]** を選択します。

## <a name="transfer-data"></a>データの転送

この作業では、ソースの MySQL Server データベース インスタンスから、新たに作成したターゲットの MySQL データベース インスタンスに、データを移動します。 **[データ転送設定]** ページでこの作業を構成します。

:::image type="content" source="./media/how-to-migrate-rds-mysql-workbench/data-transfer-setup.png" alt-text="[データ転送設定]"::: ページ

このページでは、データ転送を設定できます。 この記事では既定値を使用します。

1. データ転送作業そのものを始めるには、 **[次へ]** をクリックします。

   次のスクリーンショットに示すとおり、データ転送作業の進み具合が表示されます。

    :::image type="content" source="./media/how-to-migrate-rds-mysql-workbench/bulk-data-transfer.png" alt-text="[一括データ転送] ページ":::

   > [!NOTE]
   > データ転送作業の所要時間は、移行するデータベースのサイズに直接関係します。 ソース データベースが大きくなるほど作業に時間がかかります。大規模なデータベースでは数時間かかることもあり得ます。

2. 転送が完了したら **[次へ]** をクリックします。

   **[移行レポート]** ページが表示され、下のスクリーンショットに示すとおり、作業全体の要約レポートを見ることができます。

    :::image type="content" source="./media/how-to-migrate-rds-mysql-workbench/migration-report.png" alt-text="[移行の進行状況レポート] ページ":::

3. **[終了]** をクリックして Migration Wizard を閉じます。

   これで移行は完了です。

## <a name="verify-consistency-of-the-migrated-schemas-and-tables"></a>移行したスキーマとテーブルの整合性を確認する

1. 次に、MySQL ターゲット データベース インスタンスにログインして、移行したスキーマとテーブルが、MySQL ソース データベースに一致することを確認します。

   この例では、Amazon RDS for MySQL の **MyjolieDB** のスキーマ (sakila、moda、items、customer、clothes、world、world_x) をすべて、Azure Database for MySQL の **azmysql** インスタンスに移行できたことが分かります。

2. テーブルと行の数を確認するには、次のクエリを両方のインスタンスで実行します。

      `SELECT COUNT (*) FROM sakila.actor;`

   下のスクリーンショットでは、Amazon RDS MySQL の行数が 200 であり、Azure Database for MySQL インスタンスと一致することが分かります。

    :::image type="content" source="./media/how-to-migrate-rds-mysql-workbench/table-row-size-source.png" alt-text="ソース データベースのテーブルと行の数":::

    :::image type="content" source="./media/how-to-migrate-rds-mysql-workbench/table-row-size-target.png" alt-text="ターゲット データベースのテーブルと行の数":::

   上のクエリをすべてのスキーマとテーブルで 1 回 1 回実行することもできますが、何百、何千あるいは何百万というテーブルを扱う場合はかなり手間がかかってしまいます。 下のクエリを使えば、その代わりに、スキーマ (データベース) とテーブルのサイズを確認できます。

3. データベースのサイズを確認するには、次のクエリを実行します。

    ```
   SELECT table_schema AS "Database", 
   ROUND(SUM(data_length + index_length) / 1024 / 1024, 2) AS "Size (MB)" 
   FROM information_schema.TABLES 
   GROUP BY table_schema;
   ```

4. テーブルのサイズを確認するには、次のクエリを実行します。

   ```
   SELECT table_name AS "Table",
   ROUND(((data_length + index_length) / 1024 / 1024), 2) AS "Size (MB)"
   FROM information_schema.TABLES
   WHERE table_schema = "database_name"
   ORDER BY (data_length + index_length) DESC;
   ```

   下のスクリーンショットでは、ソースである Amazon RDS MySQL インスタンスのスキーマ (データベース) のサイズが、ターゲットの Azure Database for MySQL インスタンスと一致していることが分かります。

    :::image type="content" source="./media/how-to-migrate-rds-mysql-workbench/database-size-source.png" alt-text="ソース データベースのデータベース サイズ":::

    :::image type="content" source="./media/how-to-migrate-rds-mysql-workbench/database-size-target.png" alt-text="ターゲット データベースのデータベース サイズ":::

   スキーマ (データベース) のサイズが両方のインスタンスで同じなので、個別のテーブルのサイズを確認しなくても問題はないでしょう。 いずれの場合も、必要に応じて、上のクエリでテーブルのサイズを確認できす。

   これで、問題なく移行が完了したことを確認できました。

## <a name="next-steps"></a>次のステップ

- Azure Database for MySQL へのデータベースの移行については、「[データベース移行ガイド](https://github.com/Azure/azure-mysql/tree/master/MigrationGuide)」をご覧ください。
- [MySQL/PostgreSQL アプリを Azure マネージド サービスに簡単に移行する方法](https://medius.studios.ms/Embed/Video/THR2201?sid=THR2201)のビデオをご覧ください。MySQL アプリを Azure Database for MySQL に移行する方法を示すデモが含まれています。
