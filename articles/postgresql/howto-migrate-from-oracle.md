---
title: 'Oracle から Azure Database for PostgreSQL へ: 移行ガイド'
titleSuffix: Azure Database for PostgreSQL
description: このガイドでは、Oracle スキーマを Azure Database for PostgreSQL に移行する方法について説明します。
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.subservice: migration-guide
ms.topic: how-to
ms.date: 03/18/2021
ms.openlocfilehash: 931528ec415cabde8e862db17b9f8f26502f6788
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "105968936"
---
# <a name="migrate-oracle-to-azure-database-for-postgresql"></a>Oracle を Azure Database for PostgreSQL に移行する

このガイドでは、Oracle スキーマを Azure Database for PostgreSQL に移行する方法について説明します。 

移行についての詳細かつ包括的なガイダンスについては、[移行ガイド リソース](https://github.com/microsoft/OrcasNinjaTeam/blob/master/Oracle%20to%20PostgreSQL%20Migration%20Guide/Oracle%20to%20Azure%20Database%20for%20PostgreSQL%20Migration%20Guide.pdf)を参照してください。 

## <a name="prerequisites"></a>前提条件

Oracle スキーマを Azure Database for PostgreSQL に移行するには、次の手順に従います。 

- お使いのソース環境のサポートを確認します。 
- 最新バージョンの [ora2pg](https://ora2pg.darold.net/) をダウンロードします。 
- 最新バージョンの [DBD モジュール](https://www.cpan.org/modules/by-module/DBD/)を用意します。 


## <a name="overview"></a>概要

PostgreSQL は、世界で最も高度なオープンソースのデータベースの 1 つです。 この記事では、無料の ora2pg ツールを使用して Oracle データベースを PostgreSQL に移行する方法について説明します。 ora2pg を使用すると、Oracle データベースまたは MySQL データベースを PostgreSQL と互換性のあるスキーマに移行できます。 

ora2pg ツールにより、Oracle データベースに接続されて自動的にそのスキャンが行われ、構造またはデータが抽出されます。 その後、ora2pg により、PostgreSQL データベースに読み込むことができる SQL スクリプトが生成されます。 Oracle データベースのリバース エンジニアリング、大規模なエンタープライズ データベースの移行、または一部の Oracle データの PostgreSQL データベースへの単純なレプリケートといったタスクに、ora2pg を使用できます。 このツールは簡単に使用でき、Oracle データベースに接続するために必要なパラメーターを指定できること以外に、Oracle データベースについての知識は必要ありません。

> [!NOTE]
> ora2pg の最新バージョンの使用方法の詳細については、「[ora2pg のドキュメント](https://ora2pg.darold.net/documentation.html)」を参照してください。

### <a name="typical-ora2pg-migration-architecture"></a>一般的な ora2pg 移行アーキテクチャ

![ora2pg 移行アーキテクチャのスクリーンショット。](media/howto-migrate-from-oracle/ora2pg-migration-architecture.png)

VM と Azure Database for PostgreSQL をプロビジョニングした後は、それらの間の接続を有効にするために、 **[Azure サービスへのアクセスを許可する]** と **[SSL 接続を強制する]** の 2 つを構成する必要があります。 

- **[接続のセキュリティ]** ブレード > **[Azure サービスへのアクセスを許可する]**  >  **[オン]**

- **[接続のセキュリティ]** ブレード -> **[SSL 設定]**  >  **[SSL 接続を強制する]**  >  **[無効]**

### <a name="recommendations"></a>推奨事項

- Oracle サーバーでの評価またはエクスポート操作のパフォーマンスを向上させるには、統計情報を収集します。

   ```
   BEGIN
   
     DBMS_STATS.GATHER_SCHEMA_STATS
     DBMS_STATS.GATHER_DATABASE_STATS
     DBMS_STATS.GATHER_DICTIONARY_STATS
     END;
   ```

- `INSERT` ではなく `COPY` コマンドを使用して、データをエクスポートします。

- 外部キー (FK)、制約、インデックスを含むテーブルはエクスポートしないでください。 これらの要素があると、PostgreSQL にデータをインポートするプロセスが遅くなります。

- "*no data 句*" を使用して、具体化されたビューを作成します。 その後で、ビューを更新します。

- 可能であれば、具体化されたビューで一意なインデックスを使用します。 これらのインデックスを使用すると、`REFRESH MATERIALIZED VIEW CONCURRENTLY` 構文を使用するときの更新の速度が向上します。


## <a name="premigration"></a>移行前 

ソース環境がサポートされていて、前提条件が満たされていることを確認したら、移行前ステージを始めることができます。 開始するには 

1. 移行する必要があるデータベースのインベントリを作成します。 
1. それらのデータベースで、移行の問題や阻害要因の可能性を評価します。
1. 検出されたすべての項目を解決します。 
 
Oracle から Azure Database for PostgreSQL へのような異種間移行の場合は、このステージで、ソース データベースのスキーマをターゲット環境と互換性のあるものにする必要もあります。

### <a name="discover"></a>発見

発見フェーズの目的は、既存のデータ ソースと、使用されている機能についての詳細を、明らかにすることです。 このフェーズは、移行についての理解を深め、よりよい計画を立てるのに役立ちます。 このプロセスには、ネットワークをスキャンして、組織内のすべての Oracle インスタンスと、使用されているバージョンおよび機能を確認することが含まれます。

Microsoft の Oracle 用事前評価スクリプトは、Oracle データベースに対して実行されます。 事前評価スクリプトにより、Oracle メタデータのクエリが実行されます。 スクリプトによって次のものが提供されます。

- スキーマ、種類、状態別のオブジェクトの数を含むデータベース インベントリ。
- 統計に基づく、各スキーマの生データの大まかな推定。
- 各スキーマ内のテーブルのサイズ。
- パッケージ、関数、プロシージャなどのコード行の数。

[ora2pg Web サイト](https://ora2pg.darold.net/)から関連するスクリプトをダウンロードします。

### <a name="assess"></a>アクセス

Oracle データベースのインベントリを作成した後は、データベースのサイズと潜在的な問題を把握できます。 次のステップは、評価を実行することです。

Oracle から PostgreSQL への移行のコストを見積もるのは簡単ではありません。 移行コストを評価するため、ora2pg により、自動的に変換できないオブジェクトおよび PL/SQL コードに関するすべてのデータベース オブジェクト、関数、ストアド プロシージャがチェックされます。

ora2pg ツールのコンテンツ分析モードを使用すると、Oracle データベースが調査されて、テキスト レポートが生成されます。 そのレポートでは、Oracle データベースに含まれるものとエクスポートできないものが示されています。

*分析とレポート* モードをアクティブ化するには、次のコマンドに示すように、エクスポートされた型 `SHOW_REPORT` を使用します。

```
ora2pg -t SHOW_REPORT
```

ora2pg ツールを使用すると、SQL および PL/SQL のコードを、Oracle 構文から PostgreSQL に変換できます。 そのため、データベースを分析した後、ora2pg でコードの難しさと、完全なデータベースの移行に必要な時間を見積もることができます。

人日数で移行コストを見積もるには、`ESTIMATE_COST` という構成ディレクティブを ora2pg で使用できます。 このディレクティブは、コマンド プロンプトで有効にすることもできます。

```
ora2pg -t SHOW_REPORT --estimate_cost
```

既定の移行単位は、PostgreSQL エキスパートによる約 5 分の作業として表されます。 これが初めての移行の場合は、構成ディレクティブ `COST_UNIT_VALUE` または `--cost_unit_value` コマンドライン オプションを使用して、既定の移行単位を大きくすることができます。

レポートの最後の行には、推定される移行コードの総量が人日数で示されています。 この推定は、各オブジェクトに対して推定された移行単位の数に従っています。

この移行単位は、PostgreSQL エキスパートの約 5 分を表します。 これが初めての移行の場合は、構成ディレクティブ `COST_UNIT_VALUE` または `--cost_unit_value` コマンドライン オプションを使用して、既定値を大きくすることができます。 

次のコード例では、評価のいくつかのバリエーションが示されています。 
* テーブルの評価
* 列の評価
* 既定の 5 分のコスト単位を用いたスキーマ評価
* 10 分のコスト単位を用いたスキーマ評価

```
ora2pg -t SHOW_TABLE -c c:\ora2pg\ora2pg_hr.conf > c:\ts303\hr_migration\reports\tables.txt ora2pg -t SHOW_COLUMN -c c:\ora2pg\ora2pg_hr.conf > c:\ts303\hr_migration\reports\columns.txt
ora2pg -t SHOW_REPORT -c c:\ora2pg\ora2pg_hr.conf --dump_as_html --estimate_cost > c:\ts303\hr
_migration\reports\report.html
ora2pg -t SHOW_REPORT -c c:\ora2pg\ora2pg_hr.conf –-cost_unit_value 10 --dump_as_html --estimate_cost > c:\ts303\hr_migration\reports\report2.html
```

次に、スキーマ評価移行レベル B-5 の出力を示します。

* 移行レベル:

  * A - 自動的に実行できる移行
    
  * B - コード書き換えによる移行と最大5日間の人日コスト
    
  * C - コードの書き換えと 5 日以上の人日数コストを要する移行
    
* 技術レベル:

   * 1 = 些細: ストアド関数なし、トリガーなし

   * 2 = 簡単: ストアド関数なし、トリガーあり、手作業による書き換えなし

   * 3 = 単純: ストアド関数とトリガーの一方または両方あり、手作業による書き換えなし

   * 4 = 手作業: ストアド関数なし、トリガーまたはビューのコードの書き換えが必要

   * 5 = 困難: ストアド関数とトリガーの一方または両方のコードの書き換えが必要

評価は次のもので構成されます。 
* 手作業による書き換えが移行に必要かどうかを指定する文字 (A または B)。

* 技術的な難しさを示す 1 から 5 の数値。 

もう 1 つのオプション `-human_days_limit` では、人日数の制限を指定します。 ここで、移行に大量の作業、完全なプロジェクト管理、移行のサポートが必要であることを示すには、移行レベルを C に設定します。 既定値は 10 人日です。 構成ディレクティブ `HUMAN_DAYS_LIMIT` を使用すると、この既定値を完全に変更できます。

このスキーマ評価は、最初に移行するデータベースと、投入するチームをユーザーが決定しやすいように開発されました。

### <a name="convert"></a>Convert

最小限のダウンタイムでの移行では、移行ソースが変更されます。 これにより、1 回限りの移行の後で、データとスキーマに関してターゲットからのずれが生じます。 "*データ同期*" フェーズの間に、ソースでのすべての変更がキャプチャされ、ほぼリアルタイムでターゲットに適用されたことを確認します。 すべての変更がターゲットに適用されたことを確認した後、ソースからターゲット環境に "*切り替える*" ことができます。

移行のこのステップでは、Oracle のコードと DDL のスクリプトが PostgreSQL に変換または翻訳されます。 ora2pg ツールは、Oracle オブジェクトを PostgreSQL 形式で自動的にエクスポートします。 生成されるオブジェクトの中には、手作業で変更しないと PostgreSQL データベースでコンパイルできないものがあります。  

手動による介入が必要な要素を把握するには、まず、ora2pg によって生成されたファイルを PostgreSQL データベースに対してコンパイルします。 ログを調べて、スキーマの構造が PostgreSQL の構文と互換性があるようになるまで、必要な変更を行います。


#### <a name="create-a-migration-template"></a>移行テンプレートを作成する 

ora2pg によって提供される移行テンプレートを使用することをお勧めします。 オプション `--project_base` と `--init_project` を使用すると、作業ツリー、構成ファイル、Oracle データベースからすべてのオブジェクトをエクスポートするためのスクリプトが含まれるプロジェクト テンプレートが、ora2pg によって作成されます。 詳細については、[ora2pg のドキュメント](https://ora2pg.darold.net/documentation.html)を参照してください。

次のコマンドを使用します。 

```
ora2pg --project_base /app/migration/ --init_project test_project

ora2pg --project_base /app/migration/ --init_project test_project
```

出力の例を次に示します。 
   
```
Creating project test_project. /app/migration/test_project/ schema/ dblinks/ directories/ functions/ grants/ mviews/ packages/ partitions/ procedures/ sequences/ synonyms/    tables/ tablespaces/ triggers/ types/ views/ sources/ functions/ mviews/ packages/ partitions/ procedures/ triggers/ types/ views/ data/ config/ reports/

Generating generic configuration file

Creating script export_schema.sh to automate all exports.

Creating script import_all.sh to automate all imports.
```

`sources/` ディレクトリには、Oracle のコードが含まれています。 `schema/` ディレクトリには、PostgreSQL に移植されたコードが含まれています。 `reports/` ディレクトリには、HTML レポートと移行コストの評価が含まれています。


プロジェクト構造が作成されると、汎用構成ファイルが作成されます。 構成ファイルで、Oracle データベースの接続と関連する構成パラメータ―を定義します。 構成ファイルの詳細については、[ora2pg のドキュメント](https://ora2pg.darold.net/documentation.html)を参照してください。


#### <a name="export-oracle-objects"></a>Oracle オブジェクトをエクスポートする

次に、ファイル *export_schema.sh* を実行して、Oracle オブジェクトを PostgreSQL オブジェクトとしてエクスポートします。

```
cd /app/migration/mig_project
./export_schema.sh
```

次のコマンドを手動で実行します。

```
SET namespace="/app/migration/mig_project"

ora2pg -t DBLINK -p -o dblink.sql -b %namespace%/schema/dblinks -c
%namespace%/config/ora2pg.conf
ora2pg -t DIRECTORY -p -o directory.sql -b %namespace%/schema/directories -c
%namespace%/config/ora2pg.conf
ora2pg -p -t FUNCTION -o functions2.sql -b %namespace%/schema/functions -c
%namespace%/config/ora2pg.conf ora2pg -t GRANT -o grants.sql -b %namespace%/schema/grants -c %namespace%/config/ora2pg.conf ora2pg -t MVIEW -o mview.sql -b %namespace%/schema/   mviews -c %namespace%/config/ora2pg.conf
ora2pg -p -t PACKAGE -o packages.sql
%namespace%/config/ora2pg.conf -b %namespace%/schema/packages -c
ora2pg -p -t PARTITION -o partitions.sql %namespace%/config/ora2pg.conf -b %namespace%/schema/partitions -c
ora2pg -p -t PROCEDURE -o procs.sql
%namespace%/config/ora2pg.conf -b %namespace%/schema/procedures -c
ora2pg -t SEQUENCE -o sequences.sql
%namespace%/config/ora2pg.conf -b %namespace%/schema/sequences -c
ora2pg -p -t SYNONYM -o synonym.sql -b %namespace%/schema/synonyms -c
%namespace%/config/ora2pg.conf
ora2pg -t TABLE -o table.sql -b %namespace%/schema/tables -c %namespace%/config/ora2pg.conf ora2pg -t TABLESPACE -o tablespaces.sql -b %namespace%/schema/tablespaces -c
%namespace%/config/ora2pg.conf
ora2pg -p -t TRIGGER -o triggers.sql -b %namespace%/schema/triggers -c
%namespace%/config/ora2pg.conf ora2pg -p -t TYPE -o types.sql -b %namespace%/schema/types -c %namespace%/config/ora2pg.conf ora2pg -p -t VIEW -o views.sql -b %namespace%/   schema/views -c %namespace%/config/ora2pg.conf
```

データを抽出するには、次のコマンドを使用します。

```
ora2pg -t COPY -o data.sql -b %namespace/data -c %namespace/config/ora2pg.conf
```

#### <a name="compile-files"></a>ファイルのコンパイル

最後に、Azure Database for PostgreSQL サーバー用にすべてのファイルをコンパイルします。 手動で生成された DDL ファイルを読み込むか、2 つ目のスクリプト *import_all.sh* を使用してそれらのファイルを対話的にインポートすることができます。

```
psql -f %namespace%\schema\sequences\sequence.sql -h server1-

server.postgres.database.azure.com -p 5432 -U username@server1-server -d database -l

%namespace%\ schema\sequences\create_sequences.log

psql -f %namespace%\schema\tables\table.sql -h server1-server.postgres.database.azure.com p 5432 -U username@server1-server -d database -l    %namespace%\schema\tables\create_table.log
```

データ インポート コマンドを次に示します。

```
psql -f %namespace%\data\table1.sql -h server1-server.postgres.database.azure.com -p 5432 -U username@server1-server -d database -l %namespace%\data\table1.log

psql -f %namespace%\data\table2.sql -h server1-server.postgres.database.azure.com -p 5432 -U username@server1-server -d database -l %namespace%\data\table2.log
```

ファイルがコンパイルされている間に、ログを調べて、ora2pg で自動的に変換できなかった構文を修正します。

詳細については、「[Oracle から Azure Database for PostgreSQL への移行に関する回避策](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20Database%20for%20PostgreSQL%20Migration%20Workarounds.pdf)」を参照してください。

## <a name="migrate"></a>移行 

必要な前提条件を用意し、移行前の手順を完了したら、スキーマとデータの移行を開始できます。

### <a name="migrate-schema-and-data"></a>スキーマとデータの移行

必要な修正を行ったら、データベースの安定したビルドをデプロイできる状態になります。 `psql` インポート コマンドを実行し、変更されたコードが含まれるファイルを指定します。 このタスクにより、PostgreSQL データベースに対してデータベース オブジェクトがコンパイルされ、データがインポートされます。

このステップでは、データのインポートに並列処理のレベルを実装できます。

### <a name="sync-data-and-cut-over"></a>データを同期してカットオーバーする

オンライン (最小限のダウンタイム) 移行では、移行ソースの変更が続けられています。 これにより、1 回限りの移行の後で、データとスキーマに関してターゲットからのずれが生じます。 

"*データ同期*" フェーズの間に、ソースでのすべての変更がキャプチャされ、ほぼリアルタイムでターゲットに適用されたことを確認します。 すべての変更が適用されたことを確認した後、ソースからターゲット環境に切り替えることができます。

オンライン移行を行うには、サポートについて AskAzureDBforPostgreSQL@service.microsoft.com にお問い合わせください。

ora2pg を使用する "*デルタおよび増分*" 移行では、テーブルごとに、日付、時刻、または別のパラメーターによってフィルター処理 ("*切り取り*") を行うクエリを使用します。 その後、残りのデータを移行する 2 つ目のクエリを使用して、移行を完了します。

ソース データ テーブルで、すべての履歴データを最初に移行します。 次に例を示します。

```
select * from table1 where filter_data < 01/01/2019
```

次のようなコマンドを実行することで、最初の移行の後に行われた変更のクエリを行うことができます。

```
select * from table1 where filter_data >= 01/01/2019
```

この場合、ソースとターゲットの両方でデータ パリティをチェックすることで、検証を強化することをお勧めします。

## <a name="postmigration"></a>移行後 

"*移行*" ステージの後は、移行後のタスクを完了して、すべてが可能な限り円滑かつ効率的に機能していることを確認する必要があります。

### <a name="remediate-applications"></a>アプリケーションを修復する

データがターゲット環境に移行された後、以前にソースを使用していたすべてのアプリケーションは、ターゲットの使用を開始する必要があります。 セットアップでは、アプリケーションの変更が必要になる場合があります。

### <a name="test"></a>テスト

データがターゲットに移行された後、データベースのテストを実行して、アプリケーションがターゲットで正常に動作することを確認します。 Oracle ソースと PostgreSQL ターゲットのデータベースで手動のデータ検証スクリプトを実行することにより、ソースとターゲットが正しく移行されたことを確認します。

ソース データベースとターゲット データベースにネットワーク パスがある場合は、理想的にはデータの検証には ora2pg を使用する必要があります。 `TEST` アクションを使用して、Oracle データベースのすべてのオブジェクトが PostgreSQL に作成されていることを確認できます。 

次のコマンドを実行します。

```
ora2pg -t TEST -c config/ora2pg.conf > migration_diff.txt
```

### <a name="optimize"></a>最適化

移行後フェーズは、データの精度の問題を調整したり、完全性を検証したりするために非常に重要です。 このフェーズでは、ワークロードのパフォーマンスの問題にも対処します。

## <a name="migration-assets"></a>移行資産 

この移行シナリオの詳細については、次のリソースを参照してください。 それらでは、実際の移行プロジェクトへの取り組みがサポートされています。

| リソース | 説明    |
| -------------- | ------------------ |
| [Oracle から Azure PostgreSQL への移行に関するクックブック](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20PostgreSQL%20Migration%20Cookbook.pdf) | このドキュメントは、アーキテクト、コンサルタント、データベース管理者、関連するロールが、ora2pg を使用して Oracle から Azure Database for PostgreSQL にワークロードをすばやく移行するのに役立ちます。 |
| [Oracle から Azure PostgreSQL への移行に関する回避策](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20Database%20for%20PostgreSQL%20Migration%20Workarounds.pdf) | このドキュメントは、アーキテクト、コンサルタント、データベース管理者、関連するロールが、Oracle から Azure Database for PostgreSQL へのワークロードの移行に関する問題をすばやく解決または回避するのに役立ちます。 |
| [ora2pg を Windows または Linux にインストールする手順](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Steps%20to%20Install%20ora2pg%20on%20Windows%20and%20Linux.pdf)                       | このドキュメントでは、Windows または Linux で ora2pg を使用して、スキーマとデータを Oracle から Azure Database for PostgreSQL に移行するためのクイック インストール ガイドが提供されています。 詳細については、[ora2pg のドキュメント](http://ora2pg.darold.net/documentation.html)を参照してください。 |

データ SQL エンジニアリング チームが、これらのリソースを開発しました。 このチームの主要な作業は、Microsoft Azure データ プラットフォームへのデータ プラットフォーム移行プロジェクトのための複雑な近代化の障害を取り除き、迅速に進めることです。

## <a name="more-support"></a>その他のサポート

ora2pg ツール以外の移行に関するヘルプについては、[@AskAzure DB for PostgreSQL](mailto:AskAzureDBforPostgreSQL@service.microsoft.com) にお問い合わせください。

## <a name="next-steps"></a>次の手順

データベースとデータの移行および特殊なタスクに関するサービスとツールの一覧については、[データ移行のためのサービスとツール](https://docs.microsoft.com/azure/dms/dms-tools-matrix)に関する記事を参照してください。

ドキュメント 
- [Azure Database for PostgreSQL のドキュメント](https://docs.microsoft.com/azure/postgresql/)
- [ora2pg のドキュメント](https://ora2pg.darold.net/documentation.html)
- [PostgreSQL の Web サイト](https://www.postgresql.org/)
- [PostgreSQL での自律トランザクションのサポート](http://blog.dalibo.com/2016/08/19/Autonoumous_transactions_support_in_PostgreSQL.html) 
