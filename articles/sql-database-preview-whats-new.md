<properties 
	pageTitle="SQL データベース V12 の新機能" 
	description="Azure SQL データベースに 2014 年 12 月以降に追加された最新の拡張機能について説明します。" 
	services="sql-database" 
	documentationCenter="" 
	authors="MightyPen" 
	manager="jeffreyg" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/05/2015" 
	ms.author="genemi"/>


# SQL データベース V12 の新機能


<!--
GeneMi、2015 年 3 月 5 日 木曜日 20 時 06 分
トピック タイトルから  'Azure' を削除し、'V12' (J.G. ごとに) を追加しました。
-->



Azure SQL データベースのバージョン 12 は2015 年 1 月に一般公開 (GA) に昇格しました。この主要な更新プログラムによって、SQL データベースは、Microsoft SQL Server エンジンとほぼ完全な互換性を提供できるようになりました。SQL データベースは、お客様により Premium なパフォーマンスを提供します。これらの拡張機能を使用すると、SQL Server アプリケーションへの移行を簡略化でき、データベースのワークロードの負荷が高いお客様に向いています。


いくつかの地理的リージョンでは、まだプレビュー状態のままで、一般公開 (GA) ではありません。「[GA-per-region テーブル](../sql-database-preview-whats-new/#V12AzureSqlDbPreviewGaTable)」でお住まいの地域をご確認ください。お住まいの地域が GA に昇格するまでは、V12 は実稼働データベースではなくテスト データベースに最適です。


SQL データベースに**[サインアップ](https://portal.azure.com)すると、Microsoft Azure で次世代の SQL Database の機能を活用できるようになります。使用を開始するには、Microsoft Azure のサブスクリプションが必要です。[Azure の無料評価版](http://azure.microsoft.com/pricing/free-trial)にサインアップして[料金](http://azure.microsoft.com/pricing/details/sql-database)の情報をご確認ください。**


V11 と V12 にアップグレードする計画の手順は、「[最新の SQL Database 更新プログラム V12 (プレビュー) へのアップグレードの計画と準備をする](http://azure.microsoft.com/documentation/articles/sql-database-preview-plan-prepare-upgrade/)」から始まります。


### 主なハイライト


Azure SQL Database V12 の主な特長は次のとおりです。


- **セキュリティ**には、[包含データベース内のユーザー](../sql-database-preview-whats-new/#UsersInContainedDatabases)の新機能が追加されました。その他の 2 つの機能に[行レベル セキュリティ](../sql-database-preview-whats-new/#RowLevelSecurity)と[動的データ マスク](../sql-database-preview-whats-new/#DynamicDataMasking)がありますが、これらはまだプレビュー状態で、一般公開状態ではありません。


- クエリの並行処理 (Premium のみ) でより高い負荷のワークロードをサポートすることで、大規模なデータベースの**管理がより簡単に**、[テーブル パーティション](http://msdn.microsoft.com/library/ms187802.aspx)、[オンラインのインデックス作成](http://msdn.microsoft.com/library/ms188388.aspx)、2 GB のサイズ制限をなくすことで安心して使用できる、大規模なインデックスの再構築、[alter database](http://msdn.microsoft.com/library/bb522682.aspx) ステートメントの詳細なオプション。


- **主なプログラム関数のサポート**により、[CLR 統合](http://msdn.microsoft.com/library/ms189524.aspx)によるより強固なアプリケーション設計を推進、T-SQL [ウィンドウ関数](http://msdn.microsoft.com/library/ms189461.aspx)、[XML インデックス](http://msdn.microsoft.com/library/bb934097.aspx)、データの [変更の追跡](http://msdn.microsoft.com/library/bb933875.aspx)。


- **画期的なパフォーマンス**。 データ マートと小規模な解析ワークロードにインメモリ [columnstore](http://msdn.microsoft.com/library/gg492153.aspx) クエリを使用 (Premium 階層のみ)。


- **監視とトラブルシューティング**の改善。一連の拡張したデータベース管理ビュー ([DMV](http://msdn.microsoft.com/library/ms188754.aspx)) は 100 を超える新しいテーブル ビューを表示可能。


- **Standard 階層の新しい S3 パフォーマンス レベル:** Standard と Premium との間により柔軟な料金レベルを提供。S3 では提供される DTU (データベース スループット単位) が増え、Standard 階層で利用できる機能のすべてを使用できます。


## 1.新しい V12 の拡張機能の詳細の説明


このセクションでは、各カテゴリの新しい機能を挙げ、その機能について説明します。


### 1.1 カテゴリ:拡張されたデータベース管理


| 機能 | 説明 |
| :--- | :--- |
| 。 | ***2014 年 12 月:*** |
| <a name="UsersInContainedDatabases" id="UsersInContainedDatabases"></a>包含データベース内のユーザー | master データベースに対応するログインをしなくても、包含データベースでユーザーを作成できるようになりました。これにより、データベースの他のサーバーへの移動が簡単になりました。包含データベース ユーザーを使用しているかどうかに関わらず、クライアント アプリケーションの接続コードは同じものになります。<br/><br/>より高いサービスレベルの契約を保証するためにこの機能を使うことが求められる場合もあります。<br/><br/>通常、この機能の使用を検討することをお勧めします。ただし、一部のお客様では、この時点では従来の  *login+user* ペアがより良い選択である場合もあります。<br/><br/>詳細については、以下をご覧ください。<br/>- [Azure SQL データベースのセキュリティのガイドラインと制限事項](http://msdn.microsoft.com/library/azure/ff394108.aspx)<br/>- [Azure SQL データベースにおけるデータベースとログインの管理](http://msdn.microsoft.com/library/azure/ee336235.aspx)<br/>- [包含データベース](http://msdn.microsoft.com/library/azure/ff929071.aspx) |
| テーブル パーティション | [テーブル パーティション](http://msdn.microsoft.com/library/ms190787.aspx)に関するこれまでの制限事項がなくなりました。 |
| より大きなトランザクション | V12 では、1 つのトランザクションでのデータ変更の最大サイズが 2 GB に制限されなくなりました。 <br/><br/> 大規模なインデックスを再構築しても、2 GB のトランザクション サイズによって制限されることもありません。一般的な情報は、「[Azure SQL データベースのリソース制限](http://msdn.microsoft.com/library/azure/dn338081.aspx)」をご覧ください。 |
| オンライン インデックスの構築と再構築 | V12 よりも前のバージョンでは、Azure SQL データベースでは一般的に ALTER INDEX ステートメントの (ONLINE=ON) 句をサポートしていましたが、これは BLOB タイプの列ではサポートされていませんでした。現在は、V12 でも BLOB 列のインデックスの (ONLINE=ON) がサポートされるようになりました。<br/><br/> ONLINE 機能により、インデックスが再構築中の場合でも、クエリでインデックスを利用できるようになりました。 |
| CHECKPOINT のサポート | V12 では、データベースの T-SQL CHECKPOINT ステートメントを発行できます。 |
| ALTER TABLE の拡張機能 | テーブルを使用可能な状態に維持したまま、多くの列変更アクションを実行できるようになりました。詳細については、「[ALTER TABLE (Transact-SQL)](http://msdn.microsoft.com/library/ms190273.aspx)」をご覧ください。 |
| TRUNCATE TABLE の拡張機能 | 特定のパーティションの切り捨てできます。詳細については、「[TRUNCATE TABLE (Transact-SQL)](http://msdn.microsoft.com/library/ms177570.aspx)」をご覧ください。 |
| ALTER DATABASE の詳細オプション | V12 では、ALTER DATABASE ステートメントで使用できるオプションの数が増えました。 <br/><br/> 詳細については、「[ALTER DATABASE (Transact-SQL)](http://msdn.microsoft.com/library/ms174269.aspx)」や「[Azure SQL データベース Transact-SQL リファレンス](http://msdn.microsoft.com/library/azure/ee336281.aspx)」をご覧ください。 |
| DBCC コマンドに関する詳細 | 他のいくつかの DBCC コマンドが V12 で使用できるようになりました。詳細については、「[Azure SQL Database Transact-SQL リファレンス](http://msdn.microsoft.com/library/azure/ee336281.aspx)」をご覧ください。 |


### 1.2 カテゴリ:プログラミングとアプリケーションのサポート


| 機能 | 説明 |
| :--- | :--- |
| 。 | ***2015 年 2 月:*** |
| <a name="DynamicDataMasking" id="DynamicDataMasking"></a> 動的データ マスク プレビュー | クエリから行セットが生成されるときに、設定済みのデータ マスク ポリシーによって、データの各部分を "X" という文字に置換して秘密情報をオーバーレイし、保護できます。マスク処理が完了した後、変更された行セットがクライアントに送られます。<br/><br/>使用例として、クレジット カード番号の最後の数桁を除くすべての桁をマスクする処理があります。<br/><br/>**注:**この機能はプレビュー状態にあり、本番利用できる一般公開はまだ発表されていません。<br/><br/>詳細については、「[Get started with Azure SQL Database Dynamic Data Masking (Azure SQL Database 動的データ マスクの概要)](http://azure.microsoft.com/documentation/articles/sql-database-dynamic-data-masking-get-started/)」をご覧ください。 |
| 。 | ***2015 年 1 月:*** |
| <a name="RowLevelSecurity" id="RowLevelSecurity"></a> 行レベル セキュリティ (RLS) プレビュー | **注意:**RLS 機能は現在、V12 が一般公開 (GA) 状態になっている地理的リージョンにおいても、 *preview* 状態でのみ提供されています。GA 状態になるまで、RLS はビジネス クリティカルな本番データベースでの使用には不向きです。<br/><br/>T-SQL の新しい CREATE SECURITY POLICY ステートメントを使用すると、RLS を実装できます。RLS を実装すると、データベース サーバーに条件を追加して、行セットを呼び出し元に返す前にデータ行をフィルターで除外できます。<br/><br/>業界では、RLS を細かいアクセス制御と呼ぶこともあります。<br/><br/>コード例やその他の情報については、「[行レベル セキュリティ プレビュー](http://msdn.microsoft.com/library/7221fa4e-ca4a-4d5c-9f93-1b8a4af7b9e8.aspx)」をご覧ください。 |
| 。 | ***2014 年 12 月:*** |
| T-SQL クエリでのウィンドウ関数 | [OVER 句](http://msdn.microsoft.com/library/ms189461.aspx)を使用すると、ANSI ウィンドウ関数にアクセスできます。 <br/><br/> Itzik Ben-Gan 氏が[ブログ記事](http://sqlmag.com/sql-server-2012/how-use-microsoft-sql-server-2012s-window-functions-part-1)で、ウィンドウ関数と OVER 句を詳細に説明しています。 |
| .NET CLR の統合 |  .NET Framework の CLR (共通言語ランタイム) が V12 プレビューに統合されました。 <br/><br/> バイナリ コードに完全にコンパイルされた SAFE アセンブリのみがサポートされます。詳細については、「[CLR 統合プログラミング モデルの制限事項](http://msdn.microsoft.com/library/ms403273.aspx)」をご覧ください。 <br/><br/> この機能については、次のトピックにも説明があります。 <br/> * [SQL Server CLR 統合の概要](http://msdn.microsoft.com/library/ms254498.aspx) <br/> * [CREATE ASSEMBLY (Transact-SQL)](http://msdn.microsoft.com/library/ms189524.aspx). |
| データの変更の追跡 | データの変更の追跡をデータベースまたはテーブル レベルで構成できるようになりました。 <br/><br/> 変更の追跡の詳細については、「[変更の追跡について (SQL Server)](http://msdn.microsoft.com/library/bb933875.aspx)」をご覧ください。 |
| XML インデックス | V12 では、T-SQL ステートメントの CREATE XML INDEX と CREATE SELECTIVE XML INDEX を使用できます。 <br/><br/> XML インデックスに関する詳細については、次のトピックをご覧ください。 <br/> * [CREATE XML INDEX (Transact-SQL)](http://msdn.microsoft.com/library/bb934097.aspx) <br/> * [選択的 XML インデックスの作成、変更、ドロップ](http://msdn.microsoft.com/library/jj670109.aspx) |
| Table as a heap | V12 では、クラスター化されたインデックスを持たないテーブルを作成できます。 <br/><br/> この機能は、クエリの結果からテーブルを作成する T-SQL SELECT...INTO ステートメントをサポートするのに特に便利です。 |
| アプリケーション ロール | セキュリティと権限を制御するために、V12 では[アプリケーション ロール](http://msdn.microsoft.com/library/ms190998.aspx)に対して GRANT - DENY - REMOVE ステートメントを発行できます。 |


### 1.3 カテゴリ:より詳細な把握


| 機能 | 説明 |
| :--- | :--- |
| 。 | ***2014 年 12 月:*** |
| DMVs (動的管理ビュー) | V12 では、いくつかの DMV が追加されています。詳細については、「[Azure SQL Database Transact-SQL リファレンス](http://msdn.microsoft.com/library/azure/ee336281.aspx)」をご覧ください。 |
| 変更の追跡 | V12 は変更の追跡を完全にサポートしています。 <br/><br/> この機能の詳細については、「[変更の追跡の有効化と無効化 (SQL Server)](http://msdn.microsoft.com/library/bb964713.aspx)」をご覧ください。 |


### 1.4 Premium サーバー階層のパフォーマンスの向上


これらのパフォーマンスの拡張機能は、Premium サービス階層の P2 と P3 レベルに適用されます。


| 機能 | 説明 |
| :--- | :--- |
| 。 | ***2014 年 12 月:*** |
| クエリの並列処理 | V12 はクエリの高度な並列処理機能を備えています。 |
| I/O 待機時間の短縮 | V12 では、入力/出力操作の待機時間が大幅に短縮されました。 |
| IOPS の増加 | V12 では、1 秒あたりに処理できる入力/出力 (IOPS) の量が増加しました。 |
| ログのレート | V12 では、1 秒あたりにより多くのデータ変更を記録できるようになりました。 |


### 1.5 拡張機能の概要


V12 があれば SQL データベースを昇格させて、SQL Server 製品とほぼ完全な互換性を持たせることができます。V12 は最も頻繁に使用される機能に焦点を置いており、プログラミングのサポートにも重点を置いています。これにより、より効率的で簡単に開発を行うことができます。さらに、SQL データベース アプリケーションのクラウドへの移動もより簡単になりました。


また、Premium 階層では、V12 のパフォーマンスが大幅に向上しています。一部のアプリケーションではクエリの速度が大幅に向上しています。負荷の高いワークロードのアプリケーションでは、信頼性の高い安定したスループットが実現されています。


## <a name="V12AzureSqlDbPreviewGaTable"></a>2.リージョンごとの V12 一般公開 (GA) の状況


Azure SQL Database の V12 バージョンは、プレビューとテストのみを目的として、次の表に示すリージョンで 2014 年 12 月にリリースされました。プレビューから完全一般公開 (GA) 状態への変更は、リージョンごとに異なる日付で進行します。次の表は、リージョンごとに V12 の現在のリリース状況を示しています。


> [AZURE.NOTE]
> プレビュー期間の[料金](http://azure.microsoft.com/pricing/details/sql-database/)には割引が適用されています。料金は、2015 年 3 月 31 日にすべてのリージョンで GA レベルに戻されます。


| Azure のリージョン | 現在のリリース<br/>V12 の状態 | 昇格の日付<br/>GA に |
| :--- | :--- | :--- |
| 米国中南部 | 完全一般公開 (GA)  | 2015 年 2 月 9 日 |
| 米国中央部 | 完全一般公開 (GA) | 2015 年 2 月 9 日 |
| 米国中北部 | 完全一般公開 (GA) | 2015 年 2 月 9 日 |
| 米国西部 | 完全一般公開 (GA) | 2015 年 2 月 9 日 |
| 米国東部 | 完全一般公開 (GA) | 2015 年 2 月 9 日 |
| 米国東部 2 | 完全一般公開 (GA) | 2015 年 2 月 9 日 |
| 東アジア | 完全一般公開 (GA) | 2015 年 2 月 24 日 |
| 東南アジア | 完全一般公開 (GA) | 2015 年 2 月 24 日 |
| 西日本 | 完全一般公開 (GA) | 2015 年 2 月 24 日 |
| 東日本 | 完全一般公開 (GA) | 2015 年 2 月 24 日 |
| 北ヨーロッパ | 完全一般公開 (GA) | 2015 年 1 月 29 日 |
| 西ヨーロッパ | 完全一般公開 (GA) | 2015 年 1 月 29 日 |
| ブラジル南部 | 使用できません。 | 2015 年の第 3 四半期の予定 |
| オーストラリア東部 | プレビュー | 2015 年第 2 四半期の予定 |
| オーストラリア南東部 | プレビュー | 2015 年第 2 四半期の予定 |


GA に移行済みのリージョンでは、すべての新規サブスクリプションとそれ以降のデータベースに V12 サービス アーキテクチャが使用されるため、最新の機能を利用できます。現在の記事では、V12 で実現する新機能を紹介しています。


GA になった時点で、V12 以前のサーバーとデータベースをご利用のお客様は、これらの機能を本番運用で使用するために、データベースを V12 サービス アーキテクチャにアップグレード (または移行) できます。V12 データベースは、Basic、Standard、Premium のいずれかの[料金レベル](http://azure.microsoft.com/documentation/articles/sql-database-upgrade-new-service-tiers/)になります。


## 3.開始するには


データベースを Azure SQL Database V11 から V12 にアップグレードする方法については、「[Plan and Prepare to Upgrade to the Latest Azure SQL Database Update Preview (最新の SQL データベースの更新プログラム (プレビュー) へのアップグレードの計画と準備をする)](http://azure.microsoft.com/documentation/articles/sql-database-preview-plan-prepare-upgrade/)」をご覧ください。


V12 のようなバージョン番号は、次の TRANSACT-SQL ステートメントによって返される値をご覧ください。<br/>
**SELECT @@version;**


- 11.0.9228.18 *(V11)*
- 12.0.2000.8 *(または少し高い、V12)*


*割引:*DV12 の期間中は、割引料金で V12 をご利用いただけます。新しい S3 では 100 DTU (データベース スループット ユニット) と、Standard 階層で利用可能なすべての機能が $0.2016/時で利用できます。V12 プレビューの期間中は、料金は $0.1008/時に割引されます。詳細については、「[SQL データベースの料金](http://azure.microsoft.com/pricing/details/sql-database/)」をご覧ください。


## 4.V12 プレビューに関する注意点


Azure SQL Database 更新プログラム V12 (プレビュー) へのアップグレードに関する注意点とアップグレード後の注意点は次のとおりです。


### 4.1 V12 のプレビュー ポータル


次の 2 つの Azure 管理ポータルの最初のもののみが V12 データベースをサポートします。


- [http://portal.azure.com/](http://portal.azure.com/)
 - この新しいポータルはプレビューの状態で、まだ一般公開 (GA) の状態ではありません。<br/><br/>
- [http://manage.windowsazure.com/](http://manage.windowsazure.com/)
 - この以前のポータルは、V12 をサポートするためには更新されません。


Visual Studio 2013 (VS2013) で Azure SQL データベースに接続することを推奨します。VS2013 は、次のようなタスクに使用できます。


- T-SQL ステートメントの実行。
- スキーマの設計。
- オンラインまたはオフラインでのデータベースの開発。


代わりに、VS2013 の無償ですべての機能を備えたバージョンである [Visual Studio Community 2013](https://www.visualstudio.com/ja-jp/news/vs2013-community-vs.aspx/) で接続することもできます。


以前の Azure 管理ポータルでは、データベース ページで **[Visual Studio で開く]** をクリックしてコンピューター上で VS2013 を起動し、Azure SQL データベースに接続できます。


代わりに、[CU6](http://support.microsoft.com/kb/3031047/) を含む SQL Server Management Studio (SSMS) 2014 を使用して Azure SQL データベースに接続することもできます。詳細についてはこのブログの投稿をご覧ください。<br/>[Azure SQL データベースのクライアント ツーリングの更新](http://azure.microsoft.com/blog/2014/12/22/client-tooling-updates-for-azure-sql-database/).


### 4.2  V12 へのアップグレード *during*の注意点


> [AZURE.NOTE]
> V12 へのアップグレード中、データ アクセスの *remains available*V12 以前のデータベース。


- 50 GB より大きいデータベースの場合、V12 へのアップグレードは 24 時間ほどかかる可能性があります。
- Azure SQL Database V12 サーバーへのデータベースのアップグレード時、サーバーに対する以下の操作はアップグレードが完了するまで実行できません。
 - 新しいデータベースの作成。
 - サーバーへのデータベースのコピー。
 - 削除したデータベースの復元。
 - 特定の時点へのデータベースの復元。
 - geo レプリケーション。
- V12 へのアップグレードが完了した時点から、システムが V12 データベースに対してドメイン ネーム システム (DNS) エントリを更新するまで数分かかります。DNS 更新が行われた後、クライアント アプリケーションからデータベースに接続できます。
- V12 では、Web と Business サービスの料金レベルはサポートされません。また、将来のバージョンでもサポートされません。


### 4.3  V12 へアップグレード *after*の注意


- V12 にアップグレードしたデータベースを、以前のバージョンに戻すことはできません。
- Azure SQL Database V12 の 50% 割引料金は、2015 年 3 月 31 日に無効になるまで、すべての地理的リージョンで実施されます。プレビューと GA 状態の両方で、リージョンに割引が適用されます。


### 4.4  V12 へのアップグレード *after*のエクスポートとインポート


[Azure Web ポータル](http://portal.azure.com/)を使用して V12 データベースをエクスポート、インポートできます。または、次のいずれかのツールを使用してエクスポートまたはインポートすることもできます。


- SQL Server Management Studio (SSMS) 2014
- Visual Studio 2013
- Data-Tier Application Framework (DacFx)


ただし、これらのツールを使用するには、まずそれぞれの最新の更新プログラムをインストールし、新しい V12 の機能が確実にサポートされるようにする必要があります。


- [SQL Server Management Studio 2014 用の累積更新プログラム 6](http://support2.microsoft.com/kb/3031047)
- [Visual Studio 2013 の SQL Server データベース用の 2015年 2 月の更新プログラム](https://msdn.microsoft.com/data/hh297027)
- [Azure SQL データベース V12 用の 2015 年 2月のData-Tier Application Framework (DacFx)](http://www.microsoft.com/download/details.aspx?id=45886)


> [AZURE.NOTE] 上記のツールへのリンクが、2015 年 3 月 2 日以降に更新されました。これらのツールの新しい更新プログラムを使用することをお勧めします。


[2.リージョンごとの V12 一般公開 (GA) の状況]:#V12AzureSqlDbPreviewGaTable


<!-- EndOfFile -->


<!--HONumber=47-->
