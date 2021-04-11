---
title: Oracle からの移行
titleSuffix: Azure Database for PostgreSQL
description: このガイドでは、Oracle スキーマを Azure Database for PostgreSQL に移行する方法について説明します。
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.subservice: migration-guide
ms.topic: how-to
ms.date: 03/18/2021
ms.openlocfilehash: ec6cf87b3fd326c905b4843dc30ae6ce15379305
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104608669"
---
# <a name="migrate-oracle-to-azure-database-for-postgresql"></a>Oracle を Azure Database for PostgreSQL に移行する

このガイドでは、Oracle スキーマを Azure Database for PostgreSQL に移行する方法について説明します。 

移行についての詳細かつ包括的なガイダンスについては、[移行ガイド リソース](https://github.com/microsoft/OrcasNinjaTeam/blob/master/Oracle%20to%20PostgreSQL%20Migration%20Guide/Oracle%20to%20Azure%20Database%20for%20PostgreSQL%20Migration%20Guide.pdf)を参照してください。 

## <a name="prerequisites"></a>前提条件

Oracle スキーマを Azure Database for PostgreSQL に移行するには、次の手順に従います。 

- お使いのソース環境のサポートを確認します。 
- 最新バージョンの [ora2pg](https://ora2pg.darold.net/) をダウンロードします。 
- 最新バージョンの [DBD モジュール](https://www.cpan.org/modules/by-module/DBD/)。 


## <a name="overview"></a>概要

PostgreSQL は、世界で最も高度なオープンソースのデータベースの 1 つです。 この記事では、無料の ora2pg ユーティリティを使用して Oracle データベースを PostgreSQL に移行する方法について説明します。 無料のツールである ora2pg を使用すると、Oracle または MySQL データベースを PostgreSQL と互換性のあるスキーマに移行できます。 ユーティリティは、Oracle データベースに接続して自動的にスキャンし、その構造またはデータを抽出します。 その後、ora2pg は、PostgreSQL データベースに読み込むことができる SQL スクリプトを生成します。 ora2pg を使用すると、Oracle データベースのリバース エンジニアリング、大規模なエンタープライズ データベースの移行、または単に一部の Oracle データを PostgreSQL データベースにレプリケートするタスクを実行できます。 これは簡単に使用でき、Oracle データベースに接続するために必要なパラメーターを指定すること以外に、Oracle データベースの知識は必要ありません。

> [!NOTE]
> ora2pg の最新バージョンの使用方法の詳細については、「[ora2pg のドキュメント](https://ora2pg.darold.net/documentation.html)」を参照してください。

### <a name="typical-ora2pg-migration-architecture"></a>一般的な ora2pg 移行アーキテクチャ

![ora2pg 移行アーキテクチャのスクリーンショット。](media/howto-migrate-from-oracle/ora2pg-migration-architecture.png)

VM と Azure Database for PostgreSQL をプロビジョニングした後、これらの接続を有効にするには、 **[Azure サービスへのアクセスを許可する]** と **[SSL 接続を強制する]** の 2 つで次のような構成が必要になります。

- **[接続のセキュリティ]** ブレード -> **[Azure サービスへのアクセスを許可する]** -> [オン]

- **[接続のセキュリティ]** ブレード -> **[SSL 設定]**  ->  **[SSL 接続を強制する]** -> [無効]

### <a name="recommendations"></a>推奨事項

- Oracle サーバーでの評価またはエクスポート操作のパフォーマンスを向上させるには、次のように統計情報を収集します。

   ```
   BEGIN
   
     DBMS_STATS.GATHER_SCHEMA_STATS
     DBMS_STATS.GATHER_DATABASE_STATS
     DBMS_STATS.GATHER_DICTIONARY_STATS
     END;
   ```

- INSERT ではなく COPY コマンドを使用して、データをエクスポートします。

- FK、制約、およびインデックスを使用してテーブルをエクスポートしないでください。これにより、データを PostgreSQL にインポートする速度が低下します。

- **no data 句** を使用して具体化されたビューを作成し、後で更新します。

- 可能であれば、具体化されたビューに一意のインデックスを実装します。これにより、`REFRESH MATERIALIZED VIEW CONCURRENTLY` 構文を使用した更新が高速になります。



## <a name="pre-migration"></a>移行前 

ソース環境がサポートされていることを確認し、前提条件が満たされていることを確認したら、移行前段階を開始することができます。 プロセスのこの部分では、移行する必要があるデータベースのインベントリを実行し、移行に関する潜在的な問題や障害の有無についてそれらのデータベースを評価し、発見していなかったおそれがあるすべての項目を解決します。 Oracle から Azure Database for PostgreSQL などの異種間移行の場合、この段階では、ターゲット環境と互換性があるようにソース データベースのスキーマを変換する必要もあります。

### <a name="discover"></a>発見

発見段階の目標は、既存のデータ ソースを特定し、使用されている機能の詳細を確認して、移行について理解を深め、移行を計画することです。 このプロセスには、ネットワークをスキャンして、組織内のすべての Oracle インスタンスと共に、使用しているバージョンと機能を特定することが含まれます。

Microsoft Oracle の事前評価スクリプトは、Oracle データベースに対して実行されます。 事前評価スクリプトは、Oracle メタデータにヒットし、次のものを提供する一連のクエリです。

- スキーマ、種類、状態別のオブジェクトの数を含むデータベース インベントリ。

- 各スキーマの生データの大まかな推定値 (統計に基づく)。

- 各スキーマ内のテーブルのサイズ変更。

- パッケージ、関数、プロシージャなどのコード行の数。

[ora2pg Web サイト](http://ora2pg.darold.net/)から関連するスクリプトをダウンロードします。

### <a name="assess"></a>アクセス

Oracle データベースのインベントリを完了して、データベースのサイズと課題を把握したら、次のステップは評価を実行することです。

Oracle から PostgreSQL への移行プロセスのコストを見積もるのは簡単ではありません。 この移行コストの適切な評価を取得するために、ora2pg はすべてのデータベース オブジェクト、すべての関数、およびストアド プロシージャを調べて、ora2pg によって自動的に変換できないオブジェクトや PL/SQL コードがまだ存在するかどうかを検出します。

ora2pg には、Oracle データベースを調べて Oracle データベースに含まれているデータとエクスポートできないものに関するテキスト レポートを生成するコンテンツ分析モードがあります。

**分析とレポート** モードをアクティブ化するには、次のコマンドに示すように、エクスポートされた型 `SHOW_REPORT` を使用します。

```
ora2pg -t SHOW_REPORT
```

データベースが分析され、SQL および PL/SQL コードを Oracle 構文から PostgreSQL に変換できるようになったら、ora2pg はコードの問題と、データベースの完全な移行を実行するために必要な時間を推定することで、さらに作業を進めることができます。

人日で移行コストを見積もるために、ora2pg では ESTIMATE_COST という構成ディレクティブを使用できます。これは、コマンドラインでも有効にできます。

```
ora2pg -t SHOW_REPORT --estimate_cost
```

既定の移行単位は、PostgreSQL エキスパートの約 5 分を表します。 これが初めての移行の場合は、構成ディレクティブ COST_UNIT_VALUE または--cost_unit_value のコマンドライン オプションを使用して、より高いレベルにすることができます。

レポートの最後の行には、各オブジェクトに対して推定された移行ユニットの数に続き、人日で移行コードの推定合計が表示されます。

この移行単位は、PostgreSQL エキスパートの約 5 分を表します。 これが初めての移行の場合は、構成ディレクティブ COST_UNIT_VALUE または--cost_unit_value のコマンドライン オプションを使用して、既定を増加させることができます。 次は評価のいくつかのバリエーションです。a) テーブルの評価、b) 列の評価、c) 既定の cost_unit (5 分) を使用するスキーマの評価、d) 10分のコスト単位を使用するスキーマの評価。

```
ora2pg -t SHOW_TABLE -c c:\ora2pg\ora2pg_hr.conf > c:\ts303\hr_migration\reports\tables.txt ora2pg -t SHOW_COLUMN -c c:\ora2pg\ora2pg_hr.conf > c:\ts303\hr_migration\reports\columns.txt
ora2pg -t SHOW_REPORT -c c:\ora2pg\ora2pg_hr.conf --dump_as_html --estimate_cost > c:\ts303\hr
_migration\reports\report.html
ora2pg -t SHOW_REPORT -c c:\ora2pg\ora2pg_hr.conf –-cost_unit_value 10 --dump_as_html --estimate_cost > c:\ts303\hr_migration\reports\report2.html
```

スキーマ評価の出力を次に示します。

**移行レベル: B-5**

移行レベル:

A - 自動的に実行される移行

B - コード書き換えによる移行と最大5日間の人日コスト

C - コード書き換えによる移行と5日間以上の人日コスト

技術レベル:

1 = 些細: 格納されている関数とトリガーがない。

2 = 簡単: 格納されている関数はないが、トリガーがある。手動での書き換えは不要。

3 = 単純: 格納されている関数および/またはトリガーがある。手動での書き換えは不要。

4 = 手動: 格納されている関数はないが、トリガーまたはビューでコードの書き換えが必要。

5 = 困難: コードの書き換えを必要とする格納されている関数および/またはトリガーがある。

評価は、移行で手動書き換えが必要かどうかを指定する文字 (A または B) と、技術的な難易度レベルを示す 1 ～ 5 の数字で構成されます。 追加のオプション -human_days_limit を使用すると、移行レベルを C に設定して、作業量が多く、完全なプロジェクト管理で移行がサポートされていることを示す、人日数を指定できます。 既定値は 10 人日です。 この既定値を完全に変更するには、構成ディレクティブ HUMAN_DAYS_LIMIT を使用します。

この機能は、最初に移行できるデータベースと、稼働が必要なチームを決定するのに役立つように開発されました。

### <a name="convert"></a>Convert

最小限のダウンタイムでの移行では、1 回の移行が行われた後に、移行するソースが引き続き変更され、データとスキーマの観点からターゲットから離れていきます。 **データ同期** 段階では、ソース内のすべての変更がキャプチャされ、ほぼリアルタイムでターゲットに適用されることを確認する必要があります。 ソース内のすべての変更がターゲットに適用されていることを確認した後、ソースからターゲット環境に切り替えすることができます。

移行のこのステップでは、Oracle Code + DDLS から PostgreSQL への変換または翻訳が行われます。 ora2pg ツールは、Oracle オブジェクトを PostgreSQL 形式で自動的にエクスポートします。 生成されたオブジェクトについては、手動で変更しないと PostgreSQL データベースにコンパイルされないものがあります。  
手動による介入が必要な要素を理解するプロセスは、PostgreSQL データベースに対して ora2pg によって生成されたファイルをコンパイルし、ログを確認し、すべてのスキーマ構造が PostgreSQL 構文と互換性を持つようにするために必要な変更を行うことによって成り立っています。


#### <a name="create-migration-template"></a>移行テンプレートを作成する 

最初に、ora2pg で用意されている移行テンプレートを作成することをお勧めします。 2 つのオプション、--project_base と --init_project を使用した場合、作業ツリーでプロジェクト テンプレートを作成する必要があること、また構成ファイル、および Oracle データベースからすべてのオブジェクトをエクスポートするためのスクリプトを作成する必要があることが ora2pg に示されます。 詳細については、[ora2pg のドキュメント](https://ora2pg.darold.net/documentation.html)を参照してください。

   次のコマンドを使用します。 

   ```
   ora2pg --project_base /app/migration/ --init_project test_project
   
   ora2pg --project_base /app/migration/ --init_project test_project
   ```

出力例: 
   
   ```
   Creating project test_project. /app/migration/test_project/ schema/ dblinks/ directories/ functions/ grants/ mviews/ packages/ partitions/ procedures/ sequences/ synonyms/    tables/ tablespaces/ triggers/ types/ views/ sources/ functions/ mviews/ packages/ partitions/ procedures/ triggers/ types/ views/ data/ config/ reports/
   
   Generating generic configuration file
   
   Creating script export_schema.sh to automate all exports.
   
   Creating script import_all.sh to automate all imports.
   ```

sources/ ディレクトリに Oracle コードが含まれています。schema/ ディレクトリには、PostgreSQL に移植されたコードが含まれています。 reports/ ディレクトリには、移行コスト評価を含む html レポートが含まれています。


プロジェクト構造が作成されると、汎用構成ファイルが作成されます。 Oracle データベース接続と構成ファイル内の関連する構成パラメータ―を定義します。構成ファイル内で何をどのように設定できるかを理解するには、ora2pg のドキュメントを参照してください。


#### <a name="export-oracle-objects"></a>Oracle オブジェクトをエクスポートする

次に、ファイル export_schema.sh を実行して、Oracle オブジェクトを PostgreSQL オブジェクトとしてエクスポートします。

   ```
   cd /app/migration/mig_project
   ./export_schema.sh
   
   Run the following command manually:
   
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

   次のコマンドを使用してデータを抽出します。

   ```
   ora2pg -t COPY -o data.sql -b %namespace/data -c %namespace/config/ora2pg.conf
   ```

#### <a name="compile-files"></a>ファイルのコンパイル

最後に、Azure Database for PostgreSQL サーバーに対してすべてのファイルをコンパイルします。 これで、手動で生成された DDL ファイルを読み込むか、2 つ目のスクリプト import_all.sh を使用してこれらのファイルを対話的にインポートするかを選択できるようになります。

   ```
   psql -f %namespace%\schema\sequences\sequence.sql -h server1-
   
   server.postgres.database.azure.com -p 5432 -U username@server1-server -d database -l
   
   %namespace%\ schema\sequences\create_sequences.log
   
   psql -f %namespace%\schema\tables\table.sql -h server1-server.postgres.database.azure.com p 5432 -U username@server1-server -d database -l    %namespace%\schema\tables\create_table.log
   ```

   データ インポートのコマンド:

   ```
   psql -f %namespace%\data\table1.sql -h server1-server.postgres.database.azure.com -p 5432 -U username@server1-server -d database -l %namespace%\data\table1.log
   
   psql -f %namespace%\data\table2.sql -h server1-server.postgres.database.azure.com -p 5432 -U username@server1-server -d database -l %namespace%\data\table2.log
   ```

ファイルのコンパイル中にログを確認し、ora2pg が変換できなかった、必要な構文を修正します。

問題に対処するためのサポートについては、ホワイトペーパー「[Oracle から Azure Database for PostgreSQL への移行に関する回避策](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20Database%20for%20PostgreSQL%20Migration%20Workarounds.pdf)」を参照してください。

## <a name="migrate"></a>Migrate 

必要な前提条件を満たし、**移行前** 段階に関連するタスクを完了すると、スキーマとデータの移行を実行する準備が整います。

### <a name="migrate-schema-and-data"></a>スキーマとデータの移行

修正が適用されたら、データベースの安定したビルドを配置できるようになります。

この時点では、PostgreSQL データベースに対してデータベース オブジェクトをコンパイルしてデータをインポートするために、*psql* インポート コマンドを実行し、変更されたコードが含まれているファイルを示す必要があります。

この手順では、データのインポート時に一部の並列処理レベルを実装できます。

### <a name="data-sync-and-cutover"></a>データの同期と切り替え

オンラインの最小限のダウンタイムでの移行では、1 回の移行が行われた後に、移行するソースが引き続き変更され、データとスキーマの観点からターゲットから離れていきます。 **データ同期** 段階では、ソース内のすべての変更がキャプチャされ、ほぼリアルタイムでターゲットに適用されることを確認する必要があります。 ソース内のすべての変更がターゲットに適用されていることを確認した後、ソースからターゲット環境に切り替えすることができます。

2019年3月の時点で、オンライン移行を実行する場合は、Attunity Replicate for Microsoft Migrations または Striim を使用することを検討してください。

ora2pg を使用した *差分/増分* 移行の場合、この手法は、日付または時刻などによってフィルター (切り取り) を適用するクエリを各テーブルに適用し、その後、データの残りの部分 (残り) を移行する 2 番目のクエリを適用することで移行を完了します。

ソース データ テーブルで、すべての履歴データを最初に移行します。 その例を次に示します。

```
select * from table1 where filter_data < 01/01/2019
```

次のようなコマンドを実行して、最初の移行以降に行われた変更をクエリできます。

```
select * from table1 where filter_data >= 01/01/2019
```

この場合、ソースとターゲットの両方でデータ パリティをチェックすることで、検証を強化することをお勧めします。

## <a name="post-migration"></a>移行後 

**移行** 段階が正常に完了したら、移行後の一連のタスクを実行し、すべてが可能な限り円滑かつ効率的に機能していることを保証する必要があります。

### <a name="remediate-applications"></a>アプリケーションを修復する

データがターゲット環境に移行された後、以前にソースを使用していたすべてのアプリケーションは、ターゲットの使用を開始する必要があります。 これを実現するには、場合によってはアプリケーションの変更が必要になります。

### <a name="perform-tests"></a>テストを実行する

データをターゲットに移行した後、データベースに対してテストを実行し、移行後にアプリケーションがターゲットに対して適切に動作することを確認します。

ソースとターゲットが正しく移行されたことを保証するには、Oracle ソース データベースと PostgreSQL ターゲット データベースに対して手動のデータ検証スクリプトを実行します。

ソース データベースとターゲット データベースにネットワーク パスがある場合は、理想的にはデータの検証には ora2pg を使用する必要があります。 TEST アクションを使用すると、Oracle データベースのすべてのオブジェクトが PostgreSQL の下で作成されていることを確認できます。 次のようにコマンドを実行します。

```
ora2pg -t TEST -c config/ora2pg.conf > migration_diff.txt
```

### <a name="optimize"></a>最適化

移行後フェーズは、データの精度の問題を調整するため、完全性を確認するため、およびワークロードのパフォーマンスの問題に対処するために非常に重要です。

## <a name="migration-assets"></a>移行資産 

この移行シナリオを完了するための追加のサポートについては、次のリソースを参照してください。これらは、実際の移行プロジェクトの取り組みをサポートするために開発されました。

| **タイトル リンク** | **説明**    |
| -------------- | ------------------ |
| [Oracle から Azure PostgreSQL への移行に関するクックブック](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20PostgreSQL%20Migration%20Cookbook.pdf) | このドキュメントの目的は、ora2pg ツールを使用して Oracle から Azure Database for PostgreSQL にワークロードをすばやく移行するためのガイドを、アーキテクト、コンサルタント、DBA、および関連するロールに提供することです。 |
| [Oracle から Azure PostgreSQL への移行に関する回避策](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20Database%20for%20PostgreSQL%20Migration%20Workarounds.pdf) | このドキュメントの目的は、Oracle から Azure Database for PostgreSQL へのワークロード移行中に起きる問題の修正や回避策に関するガイドを、アーキテクト、コンサルタント、DBA、および関連するロールに提供することです。 |
| [ora2pg を Windows または Linux にインストールする手順](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Steps%20to%20Install%20ora2pg%20on%20Windows%20and%20Linux.pdf)                       | このドキュメントは、Windows または Linux で ora2pg ツールを使用して、Oracle から Azure Database for PostgreSQL へのスキーマとデータの移行を可能にするためのクイック インストール ガイドとして使用することを目的としています。 ツールの詳細については、「 http://ora2pg.darold.net/documentation.html 」を参照してください。 |

これらのリソースは、Azure Data Group エンジニアリング チームがスポンサーである Data SQL Ninja プログラムの一部として開発されました。 Data SQL Ninja プログラムの中核となるのは、複雑なモダン化のブロックを解除して加速し、データ プラットフォームを Microsoft の Azure Data プラットフォームに移行する機会を獲得することです。 組織が Data SQL Ninja プログラムへの参加に関心があると思われる場合は、アカウント チームに連絡し、申請を提出するよう依頼してください。


### <a name="contact-support"></a>サポートにお問い合せください

ora2pg ツールを超える移行について支援が必要な場合、他の移行オプションに関する情報は、[@AskAzure DB for PostgreSQL](mailto:AskAzureDBforPostgreSQL@service.microsoft.com) エイリアスにお問い合わせください。

## <a name="next-steps"></a>次のステップ

- さまざまなデータベースとデータの移行シナリオ (および特殊なタスク) を支援する Microsoft とサードパーティ製のサービスとツールの表については、[データ移行のためのサービスとツール](https://docs.microsoft.com/azure/dms/dms-tools-matrix)に関する記事を参照してください。

詳細については、次を参照してください。 
- [Azure Database for PostgreSQL のドキュメント](https://docs.microsoft.com/azure/postgresql/)
- [ora2pg のドキュメント](https://ora2pg.darold.net/documentation.html)
- [PostgreSQL の Web サイト](https://www.postgresql.org/)
- [PostgreSQL での自律トランザクションのサポート](http://blog.dalibo.com/2016/08/19/Autonoumous_transactions_support_in_PostgreSQL.html) 

ビデオ コンテンツ: 
- [移行の行程の概要、および評価と移行を実行するために推奨されるツールとサービス](https://azure.microsoft.com/resources/videos/overview-of-migration-and-recommended-tools-services/)。