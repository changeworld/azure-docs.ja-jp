<properties
   pageTitle="Azure SQL Data Warehouse の概要 | Microsoft Azure"
   description="エンタープライズ クラスの分散データベースであり、ペタバイト単位の量までリレーショナル データと非リレーショナル データを処理できます。業界初のクラウド データ ウェアハウスであり、数秒で拡大、縮小、および一時停止できます。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/27/2016"
   ms.author="lodipalm;barbkess;mausher;jrj;sonyama;kevin"/>


# Azure SQL Data Warehouse の概要

Azure SQL Data Warehouse は、クラウドベースのスケールアウト データベースであり、リレーショナルか非リレーショナルかを問わず、大規模なデータを処理できます。超並列処理 (MPP) アーキテクチャを基盤とする SQL Data Warehouse は、企業のワークロードに対応できます。

SQL Data Warehouse では、次の処理が行われます。

- SQL Server リレーショナル データベースを、Azure クラウド スケールアウト機能と組み合わせます。コンピューティングの増加、減少、一時停止、再開を数秒で行うことができます。必要なときに CPU をスケールアウトし、非ピーク時には使用量を低減させることで、コストを削減できます。
- Azure プラットフォームを活用します。デプロイが簡単で、シームレスに管理できます。自動バックアップのおかげで、完全なフォールト トレランスを実現できます。
- SQL Server エコシステムを補完します。使い慣れた SQL Server Transact-SQL (T-SQL) と各種ツールを使って開発を行うことができます。

この記事では、SQL Data Warehouse の主な機能について説明します。

## 超並列処理アーキテクチャ

SQL Data Warehouse は、超並列処理 (MPP) 分散データベース システムです。SQL Data Warehouse は、データと処理機能を複数のノードに分割することで、単一のシステムをはるかに超えた大規模なスケーラビリティを実現します。SQL Data Warehouse は、バックグラウンドで多数のシェアード ナッシング ストレージと処理装置にデータを分散します。データは Premium ローカル冗長ストレージに格納され、クエリ実行のためにコンピューティング ノードにリンクされます。このアーキテクチャを使用すると、SQL Data Warehouse は読み込みと複雑なクエリの実行に分割統治手法を利用できます。要求は制御ノードが受け取り、最適化して計算ノードに渡します。それらのノードで、作業が並列に行われます。

MPP アーキテクチャと Azure ストレージ機能を組み合わせることで、SQL Data Warehouse では以下のことが可能になります。

- コンピューティングとは無関係に、ストレージを拡大または縮小する。
- データを移動せずに、コンピューティングを拡大または縮小する。
- データをそのままの状態で保持しながら、コンピューティング能力を一時停止する。
- コンピューティング能力を瞬時に再開する。

次の図に、このアーキテクチャの詳細を示します。

![SQL Data Warehouse のアーキテクチャ][1]


**制御ノード:** 制御ノードは、クエリを管理および最適化します。すべてのアプリケーションおよび接続と対話するフロントエンドです。SQL Data Warehouse の制御ノードは SQL Database をベースにしており、接続時の外観や操作性は SQL Database と同じです。制御ノードは、分散したデータに対する並列クエリを実行するために必要なすべてのデータ移動と計算を内部的に調整します。SQL Data Warehouse に T-SQL クエリを送信すると、制御ノードはそれを各コンピューティング ノードで並列で実行される個々のクエリに変換します。

**コンピューティング ノード:** コンピューティング ノードは、SQL Data Warehouse を陰から動かす原動力として機能します。これは、データを格納し、クエリを処理する SQL Database です。データを追加すると、SQL Data Warehouse は行を計算ノードに分散します。計算ノードは、データに対する並列クエリを実行するワーカーです。処理後は、制御ノードに結果を返します。クエリを終了するために、制御ノードは結果を集計し、最終的な結果を返します。

**ストレージ:** データは Azure BLOB ストレージに格納されます。計算ノードは、データと対話する際に、直接 Blob Storage に対して書き込みと読み取りを行います。Azure ストレージは透過的に大幅に拡大されるため、SQL Data Warehouse も同様に拡大できます。コンピューティングとストレージは独立しているため、SQL Data Warehouse は、コンピューティングのスケーリングとは別に、ストレージを自動的にスケーリングできます。Azure Blob Storage は完全にフォールト トレラントでもあり、バックアップと復元のプロセスが合理化されています。

**データ移動サービス:** データ移動サービス (DMS) は、ノード間でデータを移動します。DMS は、コンピューティング ノードに、結合および集計に必要なデータへのアクセスを提供します。DMS は Azure サービスではありません。これは、すべてのノードで SQL Database と共に実行される Windows サービスです。DMS はバックグラウンドで実行されるため、ユーザーが直接操作することはありません。ただし、クエリ プランを見ると、いくつかの DMS 操作が含まれていることがわかります。これは、各クエリを並列で実行するにはデータ移動が必要であるためです。


## データ ウェアハウスのワークロード用の最適化

MPP 手法は、データ ウェアハウスに固有のさまざまなパフォーマンスの最適化によって支援されています。次に例を示します。

- 分散クエリ オプティマイザーと、すべてのデータにわたる複雑な統計情報のセット。データ サイズと分散に関する情報を使用するサービスは、特定の分散クエリ操作のコストの評価結果に基づいてクエリを最適化することができます。

- クエリ実行の必要に応じてコンピューティング リソース間でデータを効率的に移動するためのデータ移動処理に統合されている、高度なアルゴリズムや手法。これらのデータ移動操作は組み込みであり、データ移動サービスのすべての最適化は自動的に行われます。

- クラスター化**列ストア** インデックス (既定)。列ベースのストレージを使用することで、SQL Data Warehouse では従来の行指向型ストレージと比較して平均 5 倍の圧縮率を実現し、最大で 10 倍以上のクエリ パフォーマンスを得ることができます。列ストア インデックスでは、多数の行をスキャンする必要がある分析クエリがうまく機能します。


## 予測可能でスケーラブルなパフォーマンス

SQL Data Warehouse はストレージとコンピューティングを分離して、それぞれを個別にスケーリングできます。SQL Data Warehouse では、すばやく簡単にスケーリングを行い、コンピューティング リソースを即座に追加できます。これを補完するには、Azure Blob Storage を使用します。BLOB は、レプリケートされたストレージを安定して提供するだけでなく、低コストで簡単に展開を実現するためのインフラストラクチャも提供します。クラウド規模のストレージと Azure のコンピューティングを組み合わせて使用する SQL Data Warehouse では、必要なときにクエリのパフォーマンスとストレージに対して支払いを行うことができます。コンピューティング量の変更は、Azure ポータルでスライダーを左または右に移動することで簡単に行うことができます。また、T-SQL および PowerShell を使用してスケジュールすることもできます。

SQL Data Warehouse では、ストレージとは無関係にコンピューティングの量を完全に制御できるほか、データ ウェアハウスを完全に停止することができます。そのため、必要がないときにコンピューティングに対する支払いをする必要がありません。ストレージを適切に維持したまま、すべてのコンピューティングを Azure の主プールに解放することで、コストを節約できます。必要になったときに、コンピューティングを再開するだけで、ワークロードでデータとコンピューティングを利用できます。

## Data Warehouse ユニット

SQL Data Warehouse へのリソースの割り当ては、Data Warehouse ユニット (DWU) で計測されます。DWU は、SQL Data Warehouse に割り当てられる CPU、メモリ、IOPS など、基礎となるリソースの測定単位です。DWU の数が増えると、リソースの数が増え、パフォーマンスが高くなります。具体的には、DWU によって次のことが実現されます。

- 基になるハードウェアまたはソフトウェアを気にせず、データ ウェアハウスを簡単にスケーリングすることができます。

- データ ウェアハウスのサイズを変更する前に、DWU レベルでのパフォーマンスの向上を把握することができます。

- インスタンスの基になるハードウェアとソフトウェアは、ワークロードのパフォーマンスに影響を与えることなく変更または移動することができます。

- Microsoft は、ワークロードのパフォーマンスに影響を与えることなく、サービスの基になるアーキテクチャの調整を行うことができます。

- Microsoft は、スケーラブルかつシステムに均等に影響が及ぶような方法で、SQL Data Warehouse のパフォーマンスを迅速に向上させることができます。

Data Warehouse ユニットによって、データ ウェアハウスのワークロードのパフォーマンスとの関連性が高い 3 つの正確なメトリックの尺度が提供されます。目標は、次のような重要なワークロード メトリックが、データ ウェアハウス用に選択した DWU に比例して変化することです。

**スキャン/集計:** このワークロード メトリックでは、大量の行をスキャンして複雑な集計を実行する標準的なデータ ウェアハウス クエリを使用します。これは I/O と CPU を集中的に使用する操作です。

**読み込み:** このメトリックは、サービスにデータを取り込む機能を測定します。読み込みは、Azure Blob Storage から代表的なデータセットを読み込む PolyBase を使用して実行されます。このメトリックは、サービスのネットワークおよび CPU 要素にストレスをかけるように設計されています。

**CREATE TABLE AS SELECT (CTAS):** CTAS はテーブルをコピーする機能を測定します。この測定には、ストレージからのデータの読み取り、アプライアンスのノード間でのデータ分散、ストレージへのデータの再書き込みが必要です。CPU、IO、およびネットワークを集中的に使用する操作です。

## オンデマンドでの一時停止とスケーリング

より短時間で結果を得る必要がある場合は DWU を増やし、増加された DWU に対して支払いを行います。それほど高いコンピューティング能力が必要ではない場合は、DWU を減らし、小さな DWU に対して支払いを行います。次のシナリオでは、DWU の変更の検討が必要になる場合があります。

- クエリの実行が必要ないときに (おそらく夜間や週末)、クエリを休止します。次に、DWU に対する支払いを抑えるために、不要なときはコンピューティング リソースを一時停止します。

- システムでの需要が小さい場合は、DWU の規模縮小を検討します。引き続きデータにアクセスできますが、コストは大幅に削減されます。

- 大量のデータの読み込みまたは変換操作を実行する場合は、データが短時間で使用可能になるようにスケールアップすることが必要になる可能性があります。

理想的な DWU 値を把握するには、データ読み込みの後で、拡大/縮小を行い、いくつかのクエリを実行してください。スケーリングは簡単に行えるので、1 時間以内でさまざまなレベルのパフォーマンスを多数試すことができます。SQL Data Warehouse は、大量のデータを処理するように設計されています。特に、ここで述べているような大規模なスケーリングの場合、スケーリングの正確な処理能力を確認するには、1 TB に近いか、それ以上のデータを使用してください。


## SQL Server ベースに構築

SQL Data Warehouse は SQL Server リレーショナル データベース エンジンに基づいており、エンタープライズ データ ウェアハウスに期待される機能の多くを備えています。T-SQL を既に知っている場合、その知識を SQL Data Warehouse に当てはめて理解するのは簡単です。上級者か初心者かに関係なく、ドキュメントの中で紹介している例は導入の際に役立ちます。全体的に、SQL Data Warehouse の言語要素の構成方法について、次のように考えることができます。

- SQL Data Warehouse では、多くの操作で T-SQL 構文を使用します。また、従来の広範な SQL コンストラクト セット (ストアド プロシージャ、ユーザー定義関数、テーブルのパーティション分割、インデックス、照合順序など) もサポートされています。

- SQL Data Warehouse には、クラスター化**列ストア** インデックス、PolyBase 統合、データ監査 (脅威の評価による) など、最新の SQL Server 機能も複数用意されています。

- データ ウェアハウス ワークロードであまり一般的でないか、SQL Server にとって新しい特定の T-SQL 言語要素は、現時点では利用できない場合があります。詳細については、[移行に関するドキュメント][]を参照してください。

Transact-SQL と、SQL Server、SQL Data Warehouse、SQL Database、および Analytics Platform System 間の共通する機能を使用して、データのニーズに合ったソリューションを作成できます。パフォーマンス、セキュリティ、およびスケール要件に基づいて、データを保持する場所を決定し、必要に応じてデータをさまざまなシステム間で転送することができます。

## データ保護

SQL Data Warehouse は、Azure Premium のローカル冗長ストレージにすべてのデータを格納します。複数の同期されたデータ コピーがローカル データ センターに保持され、ローカルで障害が発生した場合には透過的なデータ保護が保証されます。さらに、SQL Data Warehouse では、Azure Storage Snapshots を使用して、アクティブな (一時停止されていない) データベースが定期的に自動でバックアップされます。バックアップと復元のしくみについては、[バックアップと復元の概要][]に関するページを参照してください。

## Microsoft のツールとの統合

また、SQL Data Warehouse には、SQL Server ユーザーが使い慣れている多くのツールが統合されています。学習した内容は次のとおりです。

**従来の SQL Server ツール:** SQL Data Warehouse は、SQL Server Analysis Services、Integration Services、Reporting Services と完全に統合されています。

**クラウドベースのツール:** SQL Data Warehouse は、Data Factory、Stream Analytics、Machine Learning、Power BI など、Azure の新しい各種ツールと共に使用できます。詳細な一覧については、[統合されているツールの概要][]に関するページを参照してください。

**サード パーティ製のツール:** 多数のサード パーティ ツール プロバイダーが自社のツールと SQL Data Warehouse との統合を認定しています。完全な一覧については、[SQL Data Warehouse ソリューション パートナー][]に関するページを参照してください。

## ハイブリッド データ ソースのシナリオ

SQL Data Warehouse と PolyBase を併用することで、ユーザーは従来とは異なる方法でエコシステム全体でデータを移動できるようになります。非リレーショナル データ ソースとオンプレミス データ ソースを使用して高度なハイブリッド シナリオをセットアップできます。

Polybase では、使い慣れている T-SQL コマンドを使用してさまざまなソースのデータを活用できます。Polybase を使用すると、通常のテーブルと同じように、Azure Blob Storage に保持されている非リレーショナル データを照会できます。Polybase を使用して非リレーショナル データを照会したり、非リレーショナル データを SQL Data Warehouse にインポートしたりします。

- PolyBase は外部テーブルを使用して非リレーショナル データにアクセスします。テーブルの定義は SQL Data Warehouse に格納され、通常のリレーショナル データにアクセスする場合と同様に SQL とツールを使用してアクセスできます。

- Polybase は統合時に依存性がありません。Polybase がサポートしているすべてのソースに同じ機能を公開します。Polybase によって読み取られるデータは、区切り記号で区切られたファイルや ORC ファイルなど、さまざまな形式を使用できます。

- PolyBase は、HD Insight クラスターのストレージとしても使われている Blob Storage にアクセスするのに使用できます。これにより、リレーショナル ツールと非リレーショナル ツールを使用して同じデータにアクセスできます。

## 次のステップ

SQL Data Warehouse の概要については学習したので、次はすばやく [SQL Data Warehouse を作成][]し、[サンプル データを読み込む][]方法について学習してください。Azure に慣れていない場合に新しい用語を調べるには、[Azure 用語集][]が役立ちます。または、次の SQL Data Warehouse リソースも確認できます。

- [顧客の成功事例]
- [ブログ]
- [機能に関する要求]
- [ビデオ]
- [Customer Advisory Team のブログ]
- [サポート チケットを作成する]
- [MSDN フォーラム]
- [Stack Overflow フォーラム]
- [Twitter]


<!--Image references-->
[1]: ./media/sql-data-warehouse-overview-what-is/dwarchitecture.png

<!--Article references-->
[サポート チケットを作成する]: ./sql-data-warehouse-get-started-create-support-ticket.md
[サンプル データを読み込む]: ./sql-data-warehouse-load-sample-databases.md
[SQL Data Warehouse を作成]: ./sql-data-warehouse-get-started-provision.md
[移行に関するドキュメント]: ./sql-data-warehouse-overview-migrate.md
[SQL Data Warehouse ソリューション パートナー]: ./sql-data-warehouse-partner-business-intelligence.md
[統合されているツールの概要]: ./sql-data-warehouse-overview-integrate.md
[バックアップと復元の概要]: ./sql-data-warehouse-restore-database-overview.md
[Azure 用語集]: ../azure-glossary-cloud-terminology.md

<!--MSDN references-->

<!--Other Web references-->
[顧客の成功事例]: https://customers.microsoft.com/search?sq=&ff=story_products_services%26%3EAzure%2FAzure%2FAzure%20SQL%20Data%20Warehouse%26%26story_product_families%26%3EAzure%2FAzure%26%26story_product_categories%26%3EAzure&p=0
[ブログ]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[Customer Advisory Team のブログ]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[機能に関する要求]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[MSDN フォーラム]: https://social.msdn.microsoft.com/Forums/azure/ja-JP/home?forum=AzureSQLDataWarehouse
[Stack Overflow フォーラム]: http://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[ビデオ]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse

<!---HONumber=AcomDC_0928_2016-->