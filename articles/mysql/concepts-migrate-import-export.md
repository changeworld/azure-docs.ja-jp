---
title: インポートとエクスポート - Azure Database for MySQL
description: この記事では、MySQL Workbench などのツールを使用して、Azure Database for MySQL でデータベースをインポートおよびエクスポートする一般的な方法について説明します。
author: savjani
ms.author: pariks
ms.service: mysql
ms.subservice: migration-guide
ms.topic: how-to
ms.date: 10/30/2020
ms.openlocfilehash: 641dfa2439513138b5dd8f56843e81c31eb38609
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/14/2021
ms.locfileid: "107389776"
---
# <a name="migrate-your-mysql-database-by-using-import-and-export"></a>インポートとエクスポートを使用した MySQL データベースの移行

[!INCLUDE[applies-to-single-flexible-server](includes/applies-to-single-flexible-server.md)]

この記事では、MySQL Workbench を使用して、Azure Database for MySQL サーバーにデータをインポートおよびエクスポートする 2 つの一般的な方法について説明します。

移行についての詳細かつ包括的なガイダンスについては、[移行ガイド リソース](https://github.com/Azure/azure-mysql/tree/master/MigrationGuide)を参照してください。 

その他の移行シナリオについては、[データベース移行ガイド](https://datamigration.microsoft.com/)を参照してください。 

## <a name="prerequisites"></a>前提条件

MySQL データベースの移行を開始する前に、次のことを行う必要があります。
- [Azure portal を使用して、Azure Database for MySQL サーバー](quickstart-create-mysql-server-database-using-azure-portal.md)を作成する。
- インポートおよびエクスポートを行うために [MySQL Workbench](https://dev.mysql.com/downloads/workbench/) または他のサードパーティの MySQL ツールをダウンロードしてインストールする。

## <a name="create-a-database-on-the-azure-database-for-mysql-server"></a>Azure Database for MySQL サーバーでのデータベースの作成
MySQL Workbench、Toad、または Navicat を使用して、Azure Database for MySQL サーバー上に空のデータベースを作成します。 データベースは、ダンプされたデータが含まれるデータベースと同じ名前にすることも、別の名前でデータベースを作成することもできます。

接続するには、次のようにします。

1. Azure portal で、Azure Database for MySQL の **[概要]** ペインで接続情報を探します。

   :::image type="content" source="./media/concepts-migrate-import-export/1_server-overview-name-login.png" alt-text="Azure portal の Azure Database for MySQL サーバーの接続情報のスクリーンショット。":::

1. 接続情報を MySQL Workbench に追加します。

   :::image type="content" source="./media/concepts-migrate-import-export/2_setup-new-connection.png" alt-text="MySQL Workbench の接続文字列のスクリーンショット。":::

## <a name="determine-when-to-use-import-and-export-techniques"></a>インポート/エクスポート手法を使用する状況の判断

> [!TIP]
> データベース全体をダンプして復元するシナリオでは、代わりに[ダンプと復元の](concepts-migrate-dump-restore.md)の手法を使用します。

次のシナリオでは、MySQL ツールを使用して、データベースをご利用の MySQL データベースにインポートおよびエクスポートします。 その他のツールについては、[MySQL から Azure Database への移行ガイド](https://github.com/Azure/azure-mysql/blob/master/MigrationGuide/MySQL%20Migration%20Guide_v1.1.pdf)の「移行方法」セクション (22 ページ) に移動してください。 

- 既存の MySQL データベースから少数のテーブルを選択して、Azure MySQL Database にインポートする必要がある場合は、インポートとエクスポートの手法を使用することをお勧めします。 これにより、不要なテーブルを移行から除外して、時間とリソースを節約できます。 たとえば、[mysqlpump](https://dev.mysql.com/doc/refman/5.7/en/mysqlpump.html#option_mysqlpump_include-tables) では `--include-tables` または `--exclude-tables` スイッチを、[mysqldump](https://dev.mysql.com/doc/refman/5.7/en/mysqldump.html#option_mysqldump_tables) では `--tables` スイッチを使用します。
- テーブル以外のデータベース オブジェクトを移行する場合は、それらのオブジェクトを明示的に作成します。 制約 (主キー、外部キー、インデックス)、ビュー、関数、プロシージャ、トリガーなど、移行するデータベース オブジェクトを含めます。
- MySQL データベース以外の外部データ ソースからデータを移行する場合は、フラット ファイルを作成し、[mysqlimport](https://dev.mysql.com/doc/refman/5.7/en/mysqlimport.html) を使用してそれらのファイルをインポートします。

> [!Important]
> 単一サーバーとフレキシブル サーバーの両方でサポートされるのは、InnoDB ストレージ エンジンのみです。 Azure Database for MySQL にデータを読み込むときは、データベース内のすべてのテーブルで InnoDB ストレージ エンジンが使用されていることを確認します。
>
> ソース データベースで別のストレージ エンジンが使用されている場合は、データベースを移行する前に InnoDB エンジンに変換します。 たとえば、MyISAM エンジンを使用する WordPress または Web アプリがある場合は、まず、データを InnoDB テーブルに移行してテーブルを変換します。 テーブルを作成するときに、`ENGINE=INNODB` 句を使用してエンジンを設定し、移行前にデータを互換性のあるテーブルに転送します。

   ```sql
   INSERT INTO innodb_table SELECT * FROM myisam_table ORDER BY primary_key_columns
   ```

## <a name="performance-recommendations-for-import-and-export"></a>インポートおよびエクスポートのパフォーマンスに関する推奨事項

データのインポートとエクスポートで最適なパフォーマンスを得るために、次のようにすることをお勧めします。
-   データを読み込む前に、クラスター化インデックスと主キーを作成します。 主キーの順序でデータを読み込みます。
-   データの読み込みが完了するまでセカンダリ インデックスの作成を遅らせます。
-   データを読み込む前に外部キー制約を無効にします。 外部キーのチェックを無効にすると、パフォーマンスが大幅に向上します。 読み込み後に制約を有効にし、データを検証して参照整合性を確認します。
-   データを並列で読み込みます。 リソースの上限に達するような過剰な並列処理を避け、Azure Portal で使用可能なメトリックを使用してリソースを監視します。
-   パーティション テーブルを適宜使用します。

## <a name="import-and-export-data-by-using-mysql-workbench"></a>MySQL Workbench を使用してデータをインポートおよびエクスポートする
MySQL Workbench でデータをエクスポートおよびインポートする方法には、オブジェクト ブラウザーのコンテキスト メニューからと [ナビゲーター] ペインからの 2 つがあります。 各方法の目的は異なります。

> [!NOTE]
> MySQL Workbench で MySQL 単一サーバーまたはフレキシブル サーバー (プレビュー) への接続を追加する場合は、次のようにします。
> - MySQL 単一サーバーの場合は、ユーザー名が *\<username@servername>* の形式であることをご確認ください。
> - MySQL フレキシブル サーバーの場合は、 *\<username>* のみを使用します。 *\<username@servername>* を使用して接続すると、その接続は失敗します。

### <a name="run-the-table-data-export-and-import-wizards-from-the-object-browser-context-menu"></a>オブジェクト ブラウザーのコンテキスト メニューからテーブル データのエクスポートおよびインポート ウィザードを実行する

:::image type="content" source="./media/concepts-migrate-import-export/p1.png" alt-text="オブジェクト ブラウザーのコンテキスト メニューにある MySQL Workbench のエクスポートおよびインポート ウィザード コマンドのスクリーンショット。":::

テーブル データのウィザードでは、CSV および JSON ファイルを使用するインポートとエクスポートの操作がサポートされます。 そのウィザードには、区切り記号、列の選択、エンコードの選択など、複数の構成オプションが含まれています。 各ウィザードは、ローカルまたはリモート接続された MySQL サーバーに対して実行できます。 インポート アクションには、テーブル、列、型マッピングが含まれます。

これらのウィザードにオブジェクト ブラウザーのコンテキスト メニューからアクセスするには、テーブルを右クリックし、 **[テーブル データのエクスポート ウィザード]** または **[テーブル データのインポート ウィザード]** を選択します。

#### <a name="the-table-data-export-wizard"></a>テーブル データのエクスポート ウィザード

テーブルを CSV ファイルにエクスポートするには、次のようにします。

1. エクスポートするデータベースのテーブルを右クリックします。
1. **[Table Data Export Wizard (テーブル データのエクスポート ウィザード)]** を選択します。 エクスポートする列、行のオフセット (該当する場合)、数 (該当する場合) を選択します。
1. **[エクスポートするデータの選択]** ペインで、 **[次へ]** を選択します。 ファイル パスとファイルの種類 (CSV または JSON) を選択します。 また、行区切り記号、文字列を囲む方法、フィールド区切り記号も選択します。
1. **[Select output file location]\(出力ファイルの場所の選択\)** ペインで、 **[次へ]** を選択します。
1. **[データのエクスポート]** ペインで、 **[次へ]** を選択します。

#### <a name="the-table-data-import-wizard"></a>テーブル データのインポート ウィザード

CSV からテーブルをインポートするには、次のようにします。

1. インポートするデータベースのテーブルを右クリックします。
1. インポートする CSV ファイルを見つけてそれを選び、 **[次へ]** を選択します。
1. インポート先テーブル (新規または既存) を選び、 **[Truncate table before import]\(インポート前にテーブルの切り詰め処理を行う\)** チェック ボックスをオンまたはオフにしてから、 **[次へ]** を選択します。
1. エンコードと、インポートを行う列を選んでから、 **[次へ]** を選択します。
1. **[データのインポート]** ペインで、 **[次へ]** を選択します。 ウィザードでデータがインポートされます。

### <a name="run-the-sql-data-export-and-import-wizards-from-the-navigator-pane"></a>[ナビゲーター] ペインから SQL データのエクスポートおよびインポート ウィザードを実行する
ウィザードを使用して、MySQL Workbench または mysqldump コマンドから生成された SQL データをエクスポートまたはインポートします。 そのウィザードには、 **[ナビゲーター]** ペインからアクセスできます。あるいは、メイン メニューから **[サーバー]** を選択することもできます。

#### <a name="export-data"></a>データのエクスポート

:::image type="content" source="./media/concepts-migrate-import-export/p2.png" alt-text="[ナビゲーター] ペインを使用して、MySQL Workbench の [データのエクスポート] ペインを表示している状態のスクリーンショット。":::

**[データのエクスポート]** ペインを使用して、MySQL データをエクスポートできます。

1. MySQL Workbench の **[ナビゲーター]** ペインで、 **[データのエクスポート]** を選択します。

1. **[データのエクスポート]** ペインで、エクスポートする各スキーマを選択します。
 
   各スキーマについて、エクスポートする特定のスキーマ オブジェクトまたはテーブルを選択できます。 構成オプションには、プロジェクト フォルダーまたは自己完結型 SQL ファイルへのエクスポート、格納されているルーチンとイベントのダンプ、テーブル データのスキップなどがあります。

   また、 **[Export a Result Set]\(結果セットのエクスポート\)** を使用して、SQL エディターの特定の結果セットを、CSV、JSON、HTML、XML などの別の形式にエクスポートすることもできます。

1. エクスポートするデータベース オブジェクトを選択し、関連するオプションを構成します。
1. **[更新]** を選択して、現在のオブジェクトを読み込みます。
1. 必要に応じて、右上の **[詳細オプション]** を選択してエクスポート操作を調整します。 たとえば、テーブル ロックの追加、`insert` ではなく `replace` ステートメントの使用、バッククォート文字を含む識別子の引用などを行います。
1. **[エクスポートの開始]** を選択して、エクスポート プロセスを開始します。


#### <a name="import-data"></a>データのインポート

:::image type="content" source="./media/concepts-migrate-import-export/p3.png" alt-text="[ナビゲーター] ペインを使用して、MySQL Workbench の [データのインポート] ペインを表示している状態のスクリーンショット。":::

**[データのインポート]** ペインを使用して、データ エクスポート操作または mysqldump コマンドからエクスポートされたデータをインポートまたは復元できます。

1. MySQL Workbench の **[ナビゲーター]** ペインで、 **[Data Export/Restore]\(データのエクスポート/復元\)** を選択します。
1. プロジェクト フォルダーまたは自己完結型 SQL ファイルを選択するか、インポート先スキーマを選ぶか、 **[新規]** ボタンを選択して新しいスキーマを定義します。
1. **[インポートの開始]** を選択してインポート プロセスを開始します。

## <a name="next-steps"></a>次の手順
- 別の移行方法については、「[ダンプと復元を使用した Azure Database for MySQL への MySQL データベースの移行](concepts-migrate-dump-restore.md)」を参照してください。
- Azure Database for MySQL へのデータベースの移行について詳しくは、「[データベース移行ガイド](https://github.com/Azure/azure-mysql/tree/master/MigrationGuide)」をご覧ください。
