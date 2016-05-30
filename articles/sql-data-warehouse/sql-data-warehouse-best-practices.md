<properties
   pageTitle="Azure SQL Data Warehouse のベスト プラクティス | Microsoft Azure"
   description="Azure SQL Data Warehouse のソリューションを開発する際に知っておく必要がある推奨事項とベスト プラクティス。これらは、成功に役立ちます。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyam"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="05/02/2016"
   ms.author="sonyama;barbkess"/>

# SQL Data Warehouse のベスト プラクティス

この記事には、Azure SQL Data Warehouse で最適なコスト パフォーマンスを実現するためのさまざまなベスト プラクティスがまとめられています。この記事で取り上げている概念には、非常に基本的なため、簡単に説明できるものから、高度なため、この記事では軽く紹介するだけのものまであります。この記事の目的は、基本的なガイダンスを提供し、データ ウェアハウスを構築する際に考慮する必要がある重要な事項に対する認識を高めることです。各セクションでは、概念と、その概念について詳しく説明している詳細な記事を紹介します。

Azure SQL Data Warehouse を使ってみるだけの場合でも、この記事は手に負えないと思わないでください。一連のトピックは、だいたい重要な順に並んでいます。初めは最初の 3 つに注力するだけでも、効果があります。SQL Data Warehouse を使い慣れてきたら、戻ってきて、その他のトピックもいくつか確認してください。すべてを理解するまでに時間はかかりません。最初は手に負えないと思うかもしれませんが、時間と共に経験を積むと、これらのトピックは基本となります。

## 一時停止とスケールでコストを削減する
SQL Data Warehouse の重要な機能として、使用していないときは一時停止できます。そうすると、コンピューティング リソースの課金が停止されます。もう 1 つの重要な機能として、リソースをスケールできます。一時停止とスケールは、Azure ポータルまたは PowerShell コマンドを使用して行うことができます。これらの機能を使用すると、データ ウェアハウスを使用していないときにコストを大幅に削減できるため、これらの機能についてよく理解してください。常にデータ ウェアハウスにアクセスできることが必要な場合は、一時停止ではなく、最小サイズ (DW100) へのスケールダウンを検討することをお勧めします。

関連項目: [コンピューティング リソースの一時停止][]に関するセクション、[コンピューティング リソースの再開][]に関するセクション、[コンピューティング リソースのスケール][]に関するセクション


## 一時停止またはスケールの前にトランザクションを排出する 
SQL Data Warehouse を一時停止またはスケールすると、バックグラウンドでデータベース インスタンスが停止します。これは、実行中のクエリがすべて取り消されることを意味します。単純な SELECT クエリの取り消しは、短時間で終わる処理であるため、インスタンスを一時停止またはスケールするのにかかる時間にほとんど影響しません。ただし、データやデータ構造を変更するトランザクション クエリは、すぐに停止できない場合があります。**トランザクション クエリについては、すべてが完了するか、変更をロールバックする必要があります。** トランザクション クエリが完了した作業をロールバックするには、クエリが元の変更の適用にかかった時間と同じか、それよりも長くかかる場合があります。たとえば、行の削除を既に 1 時間実行しているクエリを取り消した場合、削除された行を挿入し直すのに 1 時間かかる可能性があります。トランザクションの実行中に一時停止またはスケールを実行した場合、一時停止またはスケールするには、ロールバックが完了するのを待機する必要があるため、時間がかかることがあります。

関連項目: 「[SQL Data Warehouse のトランザクション][]」、「[SQL Data Warehouse のトランザクションの最適化][]」

## 統計を管理する
有用な列を自動的に検出し、その列に関する統計を作成または更新する SQL Server とは異なり、SQL Data Warehouse では、統計を手動で管理する必要があります。この点は今後変更する予定ですが、現時点では、SQL Data Warehouse プランを確実に最適化するには、統計を管理する必要があります。オプティマイザーによって作成されたプランの有効性は、使用可能な統計によって決まります。**統計を開始する簡単な方法は、すべての列のサンプリングされた統計を作成することです。** データに大幅な変更が発生したときに統計を更新することも同様に重要です。控えめなアプローチとしては、毎日または各読み込みの後に統計を更新することが挙げられます。パフォーマンスと統計を作成および更新するコストの間には常にトレードオフの関係が存在します。すべての統計を管理するのは時間がかかりすぎる場合は、統計を作成する列や頻繁に更新する列を限定することをお勧めします。たとえば、新しい値が毎日追加される日付列を更新します。**結合に使用されている列、WHERE 句で使用されている列、および GROUP BY に含まれている列に関する統計を作成すると、最も大きなメリットが得られます。**

関連項目: 「[SQL Data Warehouse での統計の管理][]」、「[CREATE STATISTICS (Transact-SQL)][]」、「[UPDATE STATISTICS (Transact-SQL)][]」

## INSERT ステートメントをバッチにグループ化する
INSERT ステートメントで小さなテーブルに 1 回だけ読み込む場合や、検索を定期的に再読み込みする場合は、`INSERT INTO MyLookup VALUES (1, 'Type 1')` などのステートメントで、ニーズに十分応えることができます。ただし、1 日を通して数千や数百万行を読み込む必要がある場合、シングルトンの INSERT だけでは対応できない可能性があります。代わりに、ファイルに書き込み、定期的に別のプロセスを利用してそのファイルを読み込むプロセスを作成します。

関連項目: 「[Insert (Transact-SQL)][]」
 
## PolyBase を使用して、データの読み込みとエクスポートをすばやく実行する
SQL Data Warehouse では、Azure Data Factory、PolyBase、BCP など、さまざまなツールを使用したデータの読み込みとエクスポートをサポートしています。パフォーマンスが重要でない少量のデータについては、どのツールでもニーズに十分応えることができます。ただし、大量のデータを読み込んだり、エクスポートしたりする場合や、高速なパフォーマンスが必要な場合は、PolyBase が最適な選択肢です。PolyBase は、SQL Data Warehouse の MPP (超並列処理) アーキテクチャを活用するように設計されています。そのため、他のツールよりも速く大量のデータを読み込むことや、エクスポートすることができます。PolyBase の読み込みは、CTAS または INSERT INTO を使用して実行できます。**CTAS を使用すると、トランザクション ログが最小限に抑えられ、データを一番速く読み込むことができます。** Azure Data Factory では、PolyBase の読み込みもサポートしています。PolyBase では、Gzip ファイルなど、さまざまなファイル形式をサポートしています。**gzip テキスト ファイルを使用する場合にスループットを最大限引き上げるには、ファイルを 60 個以上に分割して、読み込みの並列処理を最大化します。** 全体のスループットを引き上げるには、データを同時に読み込むことを検討してください。

関連項目: 「[SQL Data Warehouse へのデータのロード][]」、「[SQL Data Warehouse で PolyBase を使用するためのガイド][]」、「[Azure SQL Data Warehouse loading patterns and strategies (Azure SQL Data Warehouse の読み込みパターンと戦略)][]」、「[Azure Data Factory を使用してデータを読み込む][]」、「[Azure Data Factory を使用した Azure SQL Data Warehouse との間でのデータの移動][]」、「[CREATE EXTERNAL FILE FORMAT (Transact-SQL)][]」、「[SQL Data Warehouse での CREATE TABLE AS SELECT (CTAS)][]」

## ハッシュで大規模なテーブルを分散させる
既定では、テーブルはラウンド ロビン分散です。そのため、ユーザーはテーブルの分散方法を決定することなくテーブルの作成を簡単に開始できます。ラウンド ロビン テーブルは一部のワークロードでは良好なパフォーマンスを示しますが、多くの場合、分散列を選択すると、パフォーマンスが大幅に向上します。列で分散したテーブルのパフォーマンスがラウンド ロビン テーブルをはるかに上回る最も一般的な例としては、2 つの大規模なファクト テーブルが結合されている場合が挙げられます。たとえば、orders テーブルが order\_id で分散されており、transactions テーブルも order\_id で分散されている場合に、orders テーブルを transactions テーブルに order\_id で結合すると、このクエリはパススルー クエリになり、データの移動処理が行われなくなります。手順が減るため、クエリは高速になります。また、データの移動の減少もクエリの高速化に貢献します。ここでは、大まかにしか説明しません。分散テーブルを読み込む場合は、受信データを分散キーで並べ替えないでください。読み込みが遅くなります。分散列を選択するとパフォーマンスがどのように向上するのかや、CREATE TABLES ステートメントの WITH 句で分散テーブルを定義する方法の詳細については、次のリンクを参照してください。

関連項目: 「[SQL Data Warehouse でのハッシュ分散と、クエリ パフォーマンスに与える影響][]」、「[Choosing hash distributed table vs. round-robin distributed table in Azure SQL DW Service (Azure SQL DW サービスでハッシュ分散テーブルとラウンド ロビン分散テーブルのどちらを選択するのか)][]」、「[CREATE TABLE (Azure SQL Data Warehouse, Parallel Data Warehouse) (CREATE TABLE (Azure SQL Data Warehouse、Parallel Data Warehouse))][]」、「[CREATE TABLE AS SELECT (Azure SQL Data Warehouse)][]」

## パーティション分割しすぎないようにする
データをパーティション分割すると、パーティション切り替えを利用してデータを管理したり、パーティションを除外してスキャンを最適化したりできるため、非常に有用ですが、パーティションが多すぎると、クエリの速度が低下する場合があります。多くの場合、高い粒度でパーティション分割する戦略は、SQL Server では効果的ですが、SQL Data Warehouse では効果的ではありません。パーティションが多すぎると、各パーティションの行数が 100 万を下回る場合に、クラスター化列ストア インデックスの効果が減少する可能性もあります。SQL Data Warehouse では、バックグラウンドでデータを 60 個のデータベースにパーティション分割します。そのため、パーティションが 100 個あるテーブルを作成すると、実質的にはパーティションが 6000 個になることに留意してください。ワークロードはそれぞれに異なるため、パーティション分割を試して、自分のワークロードに最適な数を判断することをお勧めします。SQL Server の場合よりも粒度を下げることを検討してください。たとえば、日単位ではなく、週単位や月単位のパーティションを使用します。

関連項目: 「[SQL Data Warehouse のテーブル パーティション][]」

## トランザクション サイズを最小限に抑える
トランザクションで INSERT、UPDATE、DELETE ステートメントを実行して、失敗した場合は、ロールバックする必要があります。ロールバック時間が長くならないようにするには、できる限りトランザクション サイズを最小限に抑えます。そのためには、INSERT、UPDATE、DELETE ステートメントを複数に分割します。たとえば、INSERT に 1 時間かかると予測される場合は、可能であれば、INSERT を 4 つに分割すると、それぞれの実行時間は 15 分になります。CTAS、TRUNCATE、DROP TABLE、空のテーブルへの INSERT など、特殊な最小ログ記録のケースを活用すると、ロールバックのリスクが軽減されます。ロールバックを回避するもう 1 つの方法としては、データ管理のためのパーティション切り替えなど、メタデータのみの操作を使用します。たとえば、DELETE ステートメントを実行して、テーブル内の order\_date が 2001 年 10 月のすべての行を削除する代わりに、月単位でデータをパーティション分割し、該当するデータを含むパーティションを別のテーブルの空のパーティションに切り替えします (ALTER TABLE の例を参照してください)。パーティション分割されていないテーブルについては、DELETE を使用する代わりに、CTAS を使用して、テーブルに保持するデータを書き込むことを検討してください。CTAS にかかる時間が同じ場合でも、トランザクション ログが最小限に抑えられ、必要なときにすばやく取り消すことができるため、CTAS は非常に安全に実行できる操作です。

関連項目: 「[SQL Data Warehouse のトランザクション][]」、「[SQL Data Warehouse のトランザクションの最適化][]」、「[SQL Data Warehouse のテーブル パーティション][]」、「[TRUNCATE TABLE (Transact-SQL)][]」、「[ALTER TABLE (Transact-SQL)][]」、「[SQL Data Warehouse での CREATE TABLE AS SELECT (CTAS)][]」

## できる限り最小の列サイズを使用する
DDL を定義するときに、データをサポートする最小のデータ型を使用すると、クエリのパフォーマンスが向上します。これは、CHAR および VARCHAR 列の場合に特に重要です。列の最長の値が 25 文字の場合は、列を VARCHAR(25) として定義します。すべての文字列を既定の長さで定義しないようにします。さらに、VARCHAR で済む場合は、NVARCHAR を使用せずに、列を VARCHAR として定義します。

関連項目: 「[CREATE TABLE (Azure SQL Data Warehouse, Parallel Data Warehouse) (CREATE TABLE (Azure SQL Data Warehouse、Parallel Data Warehouse))][]」

## 一時的なデータには一時的なヒープ テーブルを使用する
データを一時的に SQL Data Warehouse に読み込む際は、ヒープ テーブルを使用すると、プロセス全体が高速になる場合があります。さまざまな変換を実行する前にデータをステージングするためにのみ読み込む場合は、ヒープ テーブルにテーブルを読み込むと、データをクラスター化列ストア テーブルに読み込む場合よりもはるかに高速に読み込まれます。さらに、テーブルを永続記憶域に読み込むよりも、データを一時テーブルに読み込んだ方が読み込み速度が大幅に向上します。一時テーブルは、"#" で始まり、作成元のセッションからしかアクセスできません。そのため、一部のシナリオには適していません。ヒープ テーブルは、CREATE TABLE の WITH 句で定義します。一時テーブルを使用する場合は、その一時テーブルの統計も必ず作成してください。

関連項目: 「[SQL Data Warehouse の一時テーブル][]」、「[CREATE TABLE (Azure SQL Data Warehouse, Parallel Data Warehouse) (CREATE TABLE (Azure SQL Data Warehouse、Parallel Data Warehouse))][]」、「[CREATE TABLE AS SELECT (Azure SQL Data Warehouse)][]」

## クラスター化列ストア テーブルを最適化する
クラスター化列ストア インデックスは、Azure SQL Data Warehouse にデータを格納する最も効率的な方法の 1 つです。既定では、SQL Data Warehouse のテーブルは、クラスター化列ストアとして作成されます。列ストア テーブルに対するクエリのパフォーマンスを最大限に引き出すには、セグメントの質が高いことが重要です。行を列ストア テーブルに書き込む際にメモリ負荷が発生すると、列ストア セグメントの質が低下する可能性があります。セグメントの質は、圧縮後の行グループに含まれる行の数を使って判断できます。クラスター化列ストア テーブルのセグメントの質を検出して向上させる詳細な手順については、「[トラブルシューティング][]」の「**クラスター化列ストア セグメントの質**」を参照してください。列ストア セグメントの質を高めることが非常に重要であるため、一般に、中規模または大規模リソース クラスを利用する読み込み専用の特別なユーザー ID を作成することをお勧めします。使用する DWU が少なくなると、読み込みユーザーに割り当てるリソース クラスを大きくすることができます。

通常、テーブルあたりの行数が 100 万を超え、各 SQL Data Warehouse テーブルが 60 個にパーティション分割されるまで、列ストア テーブルは圧縮された列ストア セグメントにデータをプッシュしません。そのため、経験上、テーブルの行数が 6,000 万を超えない限り、列ストア テーブルはクエリにとってメリットがありません。6,000 万行未満のテーブルについては、列ストア インデックスを作成してもメリットがありません。また、デメリットもありません。さらに、データをパーティション分割する場合は、クラスター化列ストア インデックスの恩恵を受けるには、各パーティションに 100 万行が必要なことを考慮に入れる必要があります。テーブルに 100 個のパーティションがある場合に、クラスター化列ストアの恩恵を受けるには、少なくとも 60 億行必要です (60 個のディストリビューション x 100 個のパーティション x 100 万行)。この例でテーブルに 60 億行もない場合は、パーティションの数を減らすか、代わりにヒープ テーブルを使用することを検討してください。列ストア テーブルの代わりに、ヒープ テーブルとセカンダリ インデックスを使用してパフォーマンスが向上するかどうかを試してみる価値もあります。列ストア テーブルでは、セカンダリ インデックスはまだサポートされていません。

列ストア テーブルに対してクエリを実行する場合は、必要な列のみを選択すると、クエリの実行速度が向上します。

関連項目: 「[トラブルシューティング][]」、「[Azure SQL Data Warehouse で列ストア インデックスを管理する][]」、「[列ストア インデックスの説明][]」

## 大きなリソース クラスを使用して、クエリのパフォーマンスを向上させる
SQL Data Warehouse では、クエリにメモリを割り当てる方法としてリソース グループを使用します。既定では、ディストリビューションごとに 100 MB のメモリが与えられる小規模リソース クラスにすべてのユーザーが割り当てられます。常に 60 個のディストリビューションが存在し、各ディストリビューションに最低 100 MB が割り当てられるため、システム全体のメモリの割り当ての合計は 6,000 MB (6 GB 弱) です。大規模な結合やクラスター化列ストア テーブルへの読み込みなど、特定のクエリについては、割り当てるメモリを増やすと効果的です。純粋なスキャンなどのクエリでは、効果はありません。一方で、より大きなリソース クラスを利用すると、同時実行に影響します。そのため、すべてのユーザーをより大きなリソース クラスに移行する前に、その点を考慮する必要があります。
 
関連項目: 「[SQL Data Warehouse での同時実行とワークロード管理][]」

## 小さいリソース クラスを使用して、同時実行を増やす
ユーザー クエリの遅延が長いと感じている場合は、ユーザーが大きなリソース クラスで実行しており、同時実行スロットを大量に使用していることが原因で、他のクエリがキューに配置されている可能性があります。ユーザー クエリがキューに配置されているかどうかを確認するには、`SELECT * FROM sys.dm_pdw_waits` を実行して、行が返されるかどうかを確認します。

関連項目: 「[SQL Data Warehouse での同時実行とワークロード管理][]」、「[sys.dm\_pdw\_waits (Transact-SQL)][]」

## DMV を使用して、クエリを監視および最適化する
SQL Data Warehouse には、クエリの実行を監視するために使用できる DMV がいくつか用意されています。以下の監視に関する記事では、実行中のクエリの詳細を確認する方法についての詳細な手順を説明しています。これらの DMV でクエリをすばやく見つけるには、クエリで LABEL オプションを使用すると便利です。

関連項目: 「[DMV を利用してワークロードを監視する][]」、「[SQL Data Warehouse で、ラベルを使用してクエリをインストルメント化][]」、「[OPTION Clause (Transact-SQL)][]」、「[sys.dm\_exec\_sessions (Transact-SQL)][]」、「[sys.dm\_pdw\_exec\_requests (Transact-SQL)][]」、「[sys.dm\_pdw\_request\_steps (Transact-SQL)][]」、「[sys.dm\_pdw\_sql\_requests (Transact-SQL)][]」、「[sys.dm\_pdw\_dms\_workers (Transact-SQL)]」、「[DBCC PDW\_SHOWEXECUTIONPLAN (Transact-SQL)][]」、「[sys.dm\_pdw\_waits (Transact-SQL)][]」

## その他のリソース
Azure SQL Data Warehouse の使用方法に関する情報はさまざまな場所で見つかります。この記事は Azure ドキュメントの一部であり、その他の Azure 記事や MSDN の記事へのリンクが多数含まれています。Microsoft では、これらすべての記事のフィードバックを監視し、頻繁に更新しています。記事が役に立った場合は、"このページは役に立ちましたか?" という質問に回答して、その旨をお知らせください。回答に "はい" と "いいえ" のどちらを選んだ場合でも、コメントを入力できます。記事は役に立ったが、フィードバックがある場合は、[はい] をクリックし、記事の改善に向けてできることがあればコメントを追加してください。この質問は、画面に表示されない場合でも、すべての Azure 記事の末尾に必ずあります。MSDN の記事の場合は、MSDN の各ページの右上隅にある "解決策はありますか" リンクを使用してください。フィードバックをお寄せください。ほとんどのフィードバックに対応します。

SQL Data Warehouse の**機能の提案**がある場合は、[Azure SQL Data Warehouse のフィードバック][] ページをご利用ください。要望の追加や他の要求への投票は、機能の優先順位を決める際に役立ちます。

[Azure SQL Data Warehouse の MSDN フォーラム][]は、他のユーザーや SQL Data Warehouse 製品グループに質問できる場所として設けられました。Microsoft では、このフォーラムを積極的に監視し、お客様からの質問に他のユーザーや Microsoft のスタッフが回答しているかどうかを確認しています。Stack Overflow で質問したい方のために、[Azure SQL Data Warehouse Stack Overflow フォーラム][]も用意しています。

<!--Image references-->

<!--Article references-->
[create a support ticket]: sql-data-warehouse-get-started-create-support-ticket.md
[SQL Data Warehouse での同時実行とワークロード管理]: sql-data-warehouse-develop-concurrency.md
[SQL Data Warehouse での CREATE TABLE AS SELECT (CTAS)]: sql-data-warehouse-develop-ctas.md
[SQL Data Warehouse で PolyBase を使用するためのガイド]: sql-data-warehouse-load-polybase-guide.md
[SQL Data Warehouse でのハッシュ分散と、クエリ パフォーマンスに与える影響]: sql-data-warehouse-develop-hash-distribution-key.md
[SQL Data Warehouse へのデータのロード]: sql-data-warehouse-overview-load.md
[Azure Data Factory を使用してデータを読み込む]: sql-data-warehouse-get-started-load-with-azure-data-factory.md
[Load data with bcp]: sql-data-warehouse-load-with-bcp.md
[Load data with PolyBase in SQL Data Warehouse]: sql-data-warehouse-get-started-load-with-polybase.md
[Azure SQL Data Warehouse で列ストア インデックスを管理する]: sql-data-warehouse-manage-columnstore-indexes.md
[SQL Data Warehouse での統計の管理]: sql-data-warehouse-develop-statistics.md
[DMV を利用してワークロードを監視する]: sql-data-warehouse-manage-monitor.md
[Azure Data Factory を使用した Azure SQL Data Warehouse との間でのデータの移動]: ../data-factory/data-factory-azure-sql-data-warehouse-connector.md
[SQL Data Warehouse のトランザクションの最適化]: sql-data-warehouse-develop-best-practices-transactions.md
[コンピューティング リソースの一時停止]: sql-data-warehouse-overview-scalability.md#pause-compute-bk
[コンピューティング リソースの再開]: sql-data-warehouse-overview-scalability.md#resume-compute-bk
[コンピューティング リソースのスケール]: sql-data-warehouse-overview-scalability.md#scale-performance-bk
[Table design in SQL Data Warehouse]: sql-data-warehouse-develop-table-design.md
[SQL Data Warehouse のテーブル パーティション]: sql-data-warehouse-develop-table-partitions.md
[SQL Data Warehouse の一時テーブル]: sql-data-warehouse-develop-temporary-tables.md
[SQL Data Warehouse のトランザクション]: sql-data-warehouse-develop-transactions.md
[トラブルシューティング]: sql-data-warehouse-troubleshoot.md
[SQL Data Warehouse で、ラベルを使用してクエリをインストルメント化]: sql-data-warehouse-develop-label.md

<!--MSDN references-->
[ALTER TABLE (Transact-SQL)]: https://msdn.microsoft.com/library/ms190273.aspx
[列ストア インデックスの説明]: https://msdn.microsoft.com/library/gg492088.aspx
[CREATE TABLE (Azure SQL Data Warehouse, Parallel Data Warehouse) (CREATE TABLE (Azure SQL Data Warehouse、Parallel Data Warehouse))]: https://msdn.microsoft.com/library/mt203953.aspx
[CREATE EXTERNAL FILE FORMAT (Transact-SQL)]: https://msdn.microsoft.com/library/dn935026.aspx
[CREATE TABLE AS SELECT (Azure SQL Data Warehouse)]: https://msdn.microsoft.com/library/mt204041.aspx
[CREATE STATISTICS (Transact-SQL)]: https://msdn.microsoft.com/library/ms188038.aspx
[DBCC PDW\_SHOWEXECUTIONPLAN (Transact-SQL)]: https://msdn.microsoft.com/library/mt204017.aspx
[Insert (Transact-SQL)]: https://msdn.microsoft.com/library/ms174335.aspx
[OPTION Clause (Transact-SQL)]: https://msdn.microsoft.com/library/ms190322.aspx
[sys.dm\_exec\_sessions (Transact-SQL)]: https://msdn.microsoft.com/library/ms176013.aspx
[sys.dm\_pdw\_exec\_requests (Transact-SQL)]: https://msdn.microsoft.com/library/mt203887.aspx
[sys.dm\_pdw\_request\_steps (Transact-SQL)]: https://msdn.microsoft.com/library/mt203913.aspx
[sys.dm\_pdw\_sql\_requests (Transact-SQL)]: https://msdn.microsoft.com/library/mt203889.aspx
[sys.dm\_pdw\_dms\_workers (Transact-SQL)]: https://msdn.microsoft.com/library/mt203878.aspx
[sys.dm\_pdw\_waits (Transact-SQL)]: https://msdn.microsoft.com/library/mt203893.aspx
[TRUNCATE TABLE (Transact-SQL)]: https://msdn.microsoft.com/library/ms177570.aspx
[UPDATE STATISTICS (Transact-SQL)]: https://msdn.microsoft.com/library/ms187348.aspx

<!--Other Web references-->
[Choosing hash distributed table vs. round-robin distributed table in Azure SQL DW Service (Azure SQL DW サービスでハッシュ分散テーブルとラウンド ロビン分散テーブルのどちらを選択するのか)]: https://blogs.msdn.microsoft.com/sqlcat/2015/08/11/choosing-hash-distributed-table-vs-round-robin-distributed-table-in-azure-sql-dw-service/
[Azure SQL Data Warehouse のフィードバック]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Azure SQL Data Warehouse の MSDN フォーラム]: https://social.msdn.microsoft.com/Forums/sqlserver/home?forum=AzureSQLDataWarehouse
[Azure SQL Data Warehouse Stack Overflow フォーラム]: http://stackoverflow.com/questions/tagged/azure-sqldw
[Azure SQL Data Warehouse loading patterns and strategies (Azure SQL Data Warehouse の読み込みパターンと戦略)]: https://blogs.msdn.microsoft.com/sqlcat/2016/02/06/azure-sql-data-warehouse-loading-patterns-and-strategies

<!---HONumber=AcomDC_0518_2016-->