<properties 
	pageTitle="SQL Database V12 の新機能" 
	description="Azure SQL Database V12 に追加された最新機能について説明します。ただし、2015 年 5 月までです。" 
	services="sql-database" 
	documentationCenter="" 
	authors="MightyPen" 
	manager="jeffreyg" 
	editor=""/>


<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/15/2015" 
	ms.author="genemi"/>


# SQL Database V12 の新機能


現在、Azure SQL Database V12 ([一部のリージョンではプレビュー](sql-database-v12-whats-new.md#V12AzureSqlDbPreviewGaTable)) は、Microsoft SQL Server エンジンとのほぼ完全な互換性を提供しています。最新の拡張機能を使用すると、SQL Server アプリケーションの SQL Database への移行を簡略化でき、システムでより負荷の大きいデータベースのワークロードを堅牢に処理できます。


SQL Database に[サインアップ](https://portal.azure.com)すると、Microsoft Azure でこのような次世代の機能を[活用できるようになります](sql-database-get-started.md)。使用を開始するには、Microsoft Azure のサブスクリプションが必要です。[Azure の無料試用版](http://azure.microsoft.com/pricing/free-trial)にサインアップして[料金](http://azure.microsoft.com/pricing/details/sql-database)の情報をご確認ください。


データベースを以前のバージョンから V12 にアップグレードする計画とその実施手順の第一歩は、「[最新の SQL Database V12 へのアップグレードの計画と準備をする](sql-database-v12-plan-prepare-upgrade.md)」です。


> [AZURE.TIP]Azure SQL Database の新機能の情報は、主に、[http://azure.microsoft.com/updates/](http://azure.microsoft.com/updates/) にあるサービスの更新情報に関する Web ページで提供しています。このサービスの更新情報に関する Web ページの **[サービス]** コントロールで **[SQL Database]** を選択します。


## ハイライト


- **Azure プレビュー ポータル**は、SQL Database のデータベースとサーバーを、V12 または以前のバージョンで作成するのに[使用できます](http://portal.azure.com/)。Azure プレビュー ポータルで SQL Database のデータベースを指定した後で、そのデータベースをホストする SQL Database のサーバーを指定できます。
 - 2015 年 4 月現在、[以前のバージョンの Azure ポータル](http://manage.windowsazure.com/) も引き続きサポートされます。


- Azure プレビュー ポータルを使用して新しいデータベースを作成する場合は、SQL Database サーバーの**バージョンを選択**します。既定値は V12 ですが、以前のバージョンの SQL Database サーバーを選択することもできます。


- **セキュリティ**には、[包含データベース内のユーザー](sql-database-v12-whats-new.md#UsersInContainedDatabases)の新機能が追加されました。その他の 2 つの機能に[行レベル セキュリティ](sql-database-v12-whats-new.md#RowLevelSecurity)と[動的データ マスク](sql-database-v12-whats-new.md#DynamicDataMasking)がありますが、これらはまだプレビュー状態で、一般公開状態ではありません。


- クエリの並行処理 (Premium のみ)、[テーブル パーティション](http://msdn.microsoft.com/library/ms187802.aspx)、[オンラインのインデックス作成](http://msdn.microsoft.com/library/ms188388.aspx)、2 GB のサイズ制限をなくすことで安心して使用できる、大規模なインデックスの再構築、[ALTER DATABASE](http://msdn.microsoft.com/library/bb522682.aspx) コマンドのオプションの追加により、大規模なデータベースの**管理がさらに簡単に**なり、より高い負荷のワークロードをサポートできるようになりました。


- **主なプログラム関数をサポート**することで、[CLR 統合](http://msdn.microsoft.com/library/ms189524.aspx)、[Transact-SQL ウィンドウ関数](http://msdn.microsoft.com/library/ms189461.aspx)、[XML インデックス](http://msdn.microsoft.com/library/bb934097.aspx)、データの[変更追跡](http://msdn.microsoft.com/library/bb933875.aspx)を使用して、さらに堅牢なアプリケーション設計を促進できます。


- データ マートと小規模な解析ワークロードでインメモリ [ColumnStore インデックス](http://msdn.microsoft.com/library/gg492153.aspx) クエリ (Premium 階層のみ) をサポートすることで、**飛躍的なパフォーマンス**を実現します。


- **監視とトラブルシューティング**の機能が向上し、データベース管理ビュー ([DMV](http://msdn.microsoft.com/library/ms188754.aspx)) の拡張セットで 100 を超える新しいテーブル ビューを表示できるようになりました。


- **Standard 階層の新しい S3 パフォーマンス レベル:** Standard と Premium の間により柔軟な[価格レベル](sql-database-upgrade-new-service-tiers.md)を提供します。S3 では提供される DTU (データベース スループット単位) が増え、Standard 階層で利用できる機能のすべてを使用できます。


## V12 の拡張機能: 拡張されたデータベース管理


| 機能 | 説明 |
| :--- | :--- |
| ()。 | ***2015 年 5 月 1 日:*** |
| 以前のバージョンから V12 へのアップグレードに使う PowerShell | 新しい PowerShell コマンドレットを使用して、Azure SQL Database の V11 またはその他の V12 以前のバージョンから V12 へのアップグレードを開始、取り消し、監視できます。<br/><br/>これらのコマンドレットについては、次のリファレンス ドキュメントを参照してください。<br/>* [Get-AzureSqlServerUpgrade](http://msdn.microsoft.com/library/mt143621.aspx)<br/>* [Start-AzureSqlServerUpgrade](http://msdn.microsoft.com/library/mt143623.aspx)<br/>* [Stop-AzureSqlServerUpgrade](http://msdn.microsoft.com/library/mt143622.aspx) |
| ()。 | ***2015 年 4 月:*** |
| TDE | 透過的なデータ暗号化 (プレビュー) は、データベース、関連付けられているバックアップ、保管されているトランザクション ログ ファイルの暗号化と暗号化解除をリアルタイムで実行することにより、悪意のあるアクティビティからデータを保護できます。TDE では、アプリケーションに対する変更は不要です。<br/><br/>詳細については、以下を参照してください。<br/>- [Azure SQL Database での透過的なデータ暗号化](http://msdn.microsoft.com/library/dn948096.aspx) - 詳細な手順。<br/>- [透過的なデータ暗号化 (TDE)](http://msdn.microsoft.com/library/bb934049.aspx) - 全般的な説明。 |
| Transact-SQL 証明書、キー、および暗号化関数 | 証明書、対称キー、非対称キーは、Azure SQL Database V12 で作成できるようになりました。現在、ほとんどの暗号化関数がサポートされています。<br/><br/>詳細については、以下を参照してください。<br/>- [CREATE CERTIFICATE (TRANSACT-SQL)](http://msdn.microsoft.com/library/ms187798.aspx)<br/>- [CREATE SYMMETRIC KEY (TRANSACT-SQL)](http://msdn.microsoft.com/library/ms188357.aspx)<br/>- [CREATE ASYMMETRIC KEY (TRANSACT-SQL)](http://msdn.microsoft.com/library/ms174430.aspx)<br/>- [暗号化関数 (Transact-SQL)](http://msdn.microsoft.com/library/ms173744.aspx) |
| SQL Database の弾力性プール | 何千ものデータベースに拡張すると、単純な作業は複雑になり、これは急激な増加によるマイナス面となります。SaaS の開発者は、ビジネスの成長に応じて、スキーマの変更やその他の管理操作を処理するための複雑なロジックを作成するのに膨大な時間を費やすことになります。<br/><br/>[弾力性のあるデータベース](sql-database-elastic-pool.md)があれば、作業が楽になります。スクリプトを取得してジョブとして送信すると、残りは Azure SQL Database で自動的に処理されます。 |
| 価格レベルの提案と STA | データベースを Web Edition または Business Edition から新しいサービス プランである Basic、Standard、Premium のいずれかに移行する際、Azure プレビュー ポータルから直接、価格レベルの提案を受けることができるようになりました。<br/><br/>Azure SQL Database サービスでは、既存のデータベースのパフォーマンス要件と機能要件を分析します。[サービス層アドバイザー (STA)](sql-database-service-tier-advisor.md) では、パフォーマンス データを読み取り、データベースに最適なサービス プランを推奨します。 |
| DMV の権限 | これまでは、いくつかの動的管理ビュー (DMV) を実行するのに VIEW SERVER STATE 権限が必要でした。Azure SQL Database V12 では、多くの場合、Basic または Standard サービス プランにあるデータベースの SQL Database 管理者アカウントで DMV を実行できるようになりました。<br/><br/>Premium プランでは、データベースの VIEW DATABASE STATE 権限を使用して、追加ユーザーに DMV へのアクセス権を付与できます。<br/><br/>詳細については、指定された個々の [DMV](http://msdn.microsoft.com/library/ms188754.aspx) の「権限」セクションを参照してください。 |
| DBCC SQLPERF | [DBCC SQLPERF](http://msdn.microsoft.com/library/ms189768.aspx) コマンドは、Azure SQL Database V12 で使用できるようになりました。*reset wait* と *latch statistics* オプションは、SQL Database V12 ではサポートされません。 |
| ()。 | ***2014 年 12 月:*** |
| <a name="UsersInContainedDatabases" id="UsersInContainedDatabases"></a>データベースに含まれるユーザー | master データベースに対応するログインをしなくても、データベースでユーザーを作成できるようになりました。これを*包含ユーザー*といいます。これにより、データベースの他のサーバーへの移動が簡単になりました。包含データベース ユーザーを使用しているかどうかに関係なく、クライアント アプリケーションの接続コードは同じものになります。<br/><br/>より高いサービス レベル アグリーメントを保証するためには、この機能を使用することが最適な方法です。<br/><br/>一般的に、この機能の使用を検討することをお勧めします。ただし、この時点では、従来の*ログインとユーザー*のペアがより良い選択である場合もあります。<br/><br/>。詳細については、以下を参照してください。<br/>- [Azure SQL Database のセキュリティのガイドラインと制限事項](http://msdn.microsoft.com/library/azure/ff394108.aspx)<br/>- [Azure SQL Database におけるデータベースとログインの管理](http://msdn.microsoft.com/library/azure/ee336235.aspx)<br/>- [包含データベース](http://msdn.microsoft.com/library/azure/ff929071.aspx) |
| テーブル パーティション | [テーブル パーティション](http://msdn.microsoft.com/library/ms190787.aspx)に関するこれまでの制限事項がなくなりました。 |
| より大きなトランザクション | V12 では、1 つのトランザクションでのデータ変更の最大サイズが 2 GB に制限されなくなりました。<br/><br/> 大規模なインデックスを再構築しても、2 GB のトランザクション サイズによって制限されることもありません。一般的な情報は、「[Azure SQL Database のリソース制限](http://msdn.microsoft.com/library/azure/dn338081.aspx)」を参照してください。 |
| オンライン インデックスの構築と再構築 | V12 より前のバージョンの Azure SQL Database では、一般的に、[ALTER INDEX](http://msdn.microsoft.com/library/ms188388.aspx) コマンドの (ONLINE=ON) 句をサポートしていましたが、これは BLOB 型の列ではサポートされていませんでした。現在は、V12 では、BLOB 列のインデックスの (ONLINE=ON) もサポートされるようになりました。<br/><br/> ONLINE 機能により、インデックスが再構築中の場合でも、クエリでインデックスを利用できるようになりました。 |
| CHECKPOINT のサポート | V12 では、データベースの Transact-SQL [CHECKPOINT](http://msdn.microsoft.com/library/ms188748.aspx) コマンドを実行できます。 |
| ALTER TABLE の拡張機能 | テーブルを使用可能な状態に維持したまま、多くの列変更アクションを実行できるようになりました。詳細については、「[ALTER TABLE (Transact-SQL)](http://msdn.microsoft.com/library/ms190273.aspx)」を参照してください。 |
| TRUNCATE TABLE の拡張機能 | 特定のパーティションの切り捨てできます。詳細については、「[TRUNCATE TABLE (Transact-SQL)](http://msdn.microsoft.com/library/ms177570.aspx)」を参照してください。 |
| ALTER DATABASE の詳細オプション | V12 では、[ALTER DATABASE](http://msdn.microsoft.com/library/ms174269.aspx) コマンドで使用できるオプションの数が増えました。<br/><br/> 詳細については、「[Azure SQL Database Transact-SQL リファレンス](http://msdn.microsoft.com/library/azure/ee336281.aspx)」を参照してください。 |
| DBCC コマンドに関する詳細 | 他のいくつかの [DBCC](http://msdn.microsoft.com/library/ms188796.aspx) コマンドが V12 で使用できるようになりました。詳細については、「[Azure SQL Database Transact-SQL リファレンス](http://msdn.microsoft.com/library/azure/ee336281.aspx)」を参照してください。 |


## プログラミングとアプリケーションのサポート


| 機能 | 説明 |
| :--- | :--- |
| ()。 | ***2015 年 5 月 1 日:*** |
| インデックス サイズの増加 | SQL Database V12 では、最大 32 の列を 1 つの複合インデックス キーに結合できます。インデックス値の最大許容サイズは、クラスター化インデックスの場合は 900 バイト、非クラスター化インデックスの場合は 1700 バイトです。<br/><br/>SQL Database V12 より前の V11 やその他のバージョンでは、16 列と 900 バイトという制限がありました。 |
| ()。 | ***2015 年 4 月:*** |
| フルテキスト検索のプレビュー | [フルテキスト検索 (FTS)](http://msdn.microsoft.com/library/ms142571.aspx) を使用すると、LIKE 演算子よりも強力な方法で文字ベースの列を照会できます。次に例を示します。<br/><br/>- FREETEXT: 文字の並びが正確に一致しない場合でも、検索語句の*意味*と一致する語句を検索します。<br/>- CONTAINS: 相互に*物理的に近く*にある 2 つの検索語句を含むセルの値を検索します。<br/><br/>**注:** この機能はプレビュー状態にあり、本番利用できる一般公開はまだ発表されていません。FTS のプレビュー機能の範囲は、Microsoft SQL Server の FTS 範囲のサブセットです。 |
| ()。 | ***2015 年 2 月:*** |
| <a name="DynamicDataMasking" id="DynamicDataMasking"></a> 動的データ マスクのプレビュー | クエリから行セットが生成されるときに、設定済みのデータ マスク ポリシーによって、データの各部分を "X" という文字に置換して秘密情報をオーバーレイし、保護できます。マスク処理が完了した後、変更された行セットがクライアントに送られます。<br/><br/>使用例として、クレジット カード番号の最後の数桁を除くすべての桁数をマスクする処理があります。<br/><br/>**注:** この機能はプレビュー状態にあり、本番利用できる一般公開はまだ発表されていません。<br/><br/>詳細については、[Azure SQL Database 動的データ マスクの使用](sql-database-dynamic-data-masking-get-started.md)に関するページを参照してください。 |
| ()。 | ***2015 年 1 月:*** |
| <a name="RowLevelSecurity" id="RowLevelSecurity"></a> Row-Level Security (RLS) プレビュー | Transact-SQL の新しい [CREATESECURITY POLICY](http://msdn.microsoft.com/library/dn765135.aspx) コマンドを使用すると、RLS を実装できます。RLS を実装すると、データベース サーバーに条件を追加して、行セットを呼び出し元に返す前にデータ行をフィルターで除外できます。<br/><br/>業界では、RLS を細かいアクセス制御と呼ぶこともあります。<br/><br/>**注:** この機能はプレビュー状態にあり、本番利用できる一般公開はまだ発表されていません。<br/><br/>コード例と詳細については、[Row-Level Security プレビュー](http://msdn.microsoft.com/library/7221fa4e-ca4a-4d5c-9f93-1b8a4af7b9e8.aspx)に関するページを参照してください。 |
| ()。 | ***2014 年 12 月:*** |
| Transact-SQL クエリでのウィンドウ関数 | [OVER](http://msdn.microsoft.com/library/ms189461.aspx) 句を使用すると、ANSI ウィンドウ関数にアクセスできます。<br/><br/> Itzik Ben-Gan 氏が[ブログ記事](http://sqlmag.com/sql-server-2012/how-use-microsoft-sql-server-2012s-window-functions-part-1)で、ウィンドウ関数と OVER 句を詳細に説明しています。 |
| .NET CLR の統合 | .NET Framework の CLR (共通言語ランタイム) が V12 プレビューに統合されました。<br/><br/> バイナリ コードに完全にコンパイルされた SAFE アセンブリのみがサポートされます。詳細については、「[CLR 統合プログラミング モデルの制限事項](http://msdn.microsoft.com/library/ms403273.aspx)」を参照してください。<br/><br/> この機能については、次のトピックにも説明があります。<br/> * [SQL Server の CLR 統合の概要](http://msdn.microsoft.com/library/ms254498.aspx) <br/> * [CREATE ASSEMBLY (TRANSACT-SQL)](http://msdn.microsoft.com/library/ms189524.aspx) |
| データの変更の追跡 | データの変更の追跡をデータベースまたはテーブル レベルで構成できるようになりました。<br/><br/> 変更の追跡の詳細については、「[変更の追跡について (SQL Server)](http://msdn.microsoft.com/library/bb933875.aspx)」を参照してください。 |
| XML インデックス | V12 では、Transact-SQL コマンドの [CREATE XML INDEX](http://msdn.microsoft.com/library/bb934097.aspx) と [CREATE SELECTIVE XML INDEX](http://msdn.microsoft.com/library/jj670104.aspx) を使用できます。 |
| ヒープとしてのテーブル | V12 では、クラスター化されたインデックスを持たないテーブルを作成できます。<br/><br/> この機能は、クエリの結果からテーブルを作成する Transact-SQL [SELECT...INTO](http://msdn.microsoft.com/library/ms188029.aspx) コマンドをサポートするのに特に便利です。 |
| アプリケーション ロール | セキュリティと権限を制御するために、V12 では[アプリケーション ロール](http://msdn.microsoft.com/library/ms190998.aspx)に対して [GRANT](http://msdn.microsoft.com/library/ms187965.aspx) - [DENY](http://msdn.microsoft.com/library/ms188338.aspx) - [REVOKE](http://msdn.microsoft.com/library/ms187728.aspx) コマンドを発行できます。 |


## より詳細な把握


| 機能 | 説明 |
| :--- | :--- |
| ()。 | ***2014 年 12 月:*** |
| DMVs (動的管理ビュー) | V12 では、いくつかの DMV が追加されています。詳細については、「[Azure SQL Database Transact-SQL リファレンス](http://msdn.microsoft.com/library/azure/ee336281.aspx)」を参照してください。 |
| 変更の追跡 | V12 は変更の追跡を完全にサポートしています。<br/><br/> この機能の詳細については、「[変更の追跡の有効化と無効化 (SQL Server)](http://msdn.microsoft.com/library/bb964713.aspx)」を参照してください。 |
| 列ストア インデックス | インデックス付き列に同じ値の繰り返しが含まれている場合は、列ストア インデックスを使用すると、データ ウェアハウスのシステム パフォーマンスが向上します。[列ストア インデックス](http://msdn.microsoft.com/library/gg492088.aspx)により、重複する値が圧縮され、クエリの実行中にアクセスする必要がある物理的な行の数が削減されます。 |


## Premium サービス階層のパフォーマンスの向上


これらのパフォーマンスの拡張機能は、Premium サービス階層の P2 と P3 レベルに適用されます。


| 機能 | 説明 |
| :--- | :--- |
| ()。 | ***2014 年 12 月:*** |
| クエリの並列処理 | V12 はクエリの高度な並列処理機能を備えています。 |
| I/O 待機時間の短縮 | V12 では、入力/出力操作の待機時間が大幅に短縮されました。 |
| IOPS の増加 | V12 では、1 秒あたりに処理できる入力/出力 (IOPS) の量が増加しました。 |
| ログのレート | V12 では、1 秒あたりにより多くのデータ変更を記録できるようになりました。 |


## 2015 年 8 月 1 日以降 V12 が API の既定値になる  


| 機能 | 説明 |
| :--- | :--- |
| ()。 | ***2015 年 8 月:*** |
| REST または PowerShell を使用してサーバーを作成する | サーバーのバージョンを指定せずにサーバーを作成すると、既定のバージョンが V11 から V12 に変更されます。<br/><br/>これは、[Azure プレビュー ポータル](http://portal.azure.com)でも同様です。 |
| TRANSACT-SQL、[REST](http://msdn.microsoft.com/library/azure/gg715283.aspx)、または [PowerShell](http://msdn.microsoft.com/library/azure/dn806332.aspx) を使用してデータベースを作成する | V11 のサーバー上で、エディションまたはサービス目標を指定せずに新しいデータベースを作成すると、既定のサービス目標が Web や Business ではなく [S0](http://azure.microsoft.com/pricing/details/sql-database/) になります。これは、Azure プレビュー ポータルの V12 サーバーでも同様です。 |


## 拡張機能の概要


V12 があれば SQL Database を昇格させて、SQL Server 製品とほぼ完全な互換性を持たせることができます。V12 は最も頻繁に使用される機能に焦点を置いており、プログラミングのサポートにも重点を置いています。これにより、より効率的で簡単に開発を行うことができます。さらに、SQL データベース アプリケーションのクラウドへの移動もより簡単になりました。


また、Premium 階層では、V12 のパフォーマンスが大幅に向上しています。一部のアプリケーションではクエリの速度が大幅に向上しています。負荷の高いワークロードのアプリケーションでは、信頼性の高い安定したスループットが実現されています。


## <a name="V12AzureSqlDbPreviewGaTable"></a>リージョンごとの V12 一般公開 (GA) の状況


> [AZURE.NOTE]
> [Pricing](http://azure.microsoft.com/pricing/details/sql-database/)は、プレビュー期間中に割引が適用されていました。2015 年 3 月 31 日 (火) にすべてのリージョンで GA レベルに戻されます。


| Azure のリージョン | V12 の現在の<br/>リリース状況 | GA への<br/>昇格日 |
| :--- | :--- | :--- |
| 米国中南部 | 完全一般公開 (GA) | 2015 年 2 月 9 日 |
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
| ブラジル南部 | 完全一般公開 (GA) | 2015 年 4 月 21 日 |
| オーストラリア東部 | 更新 | 2015 年第 2 四半期の予定 |
| オーストラリア南東部 | 更新 | 2015 年第 2 四半期の予定 |


GA に移行済みのリージョンでは、すべての新規サブスクリプションとそれ以降のデータベースに V12 サービス アーキテクチャが使用されるため、最新の機能を利用できます。現在の記事では、V12 で実現する新機能を紹介しています。


プレビュー段階でまだ一般公開 (GA) の状態ではないリージョンでは、[V12 データベースを使用するオプションをアクティブ化する](sql-database-v12-sign-up.md)必要があります。


GA になった時点で、V12 以前のサーバーとデータベースがある場合は、データベースを V12 サービス アーキテクチャにアップグレード (または移行) できます。その後、新しい機能を運用環境に使用できます。V12 データベースは、Basic、Standard、Premium のいずれかの[価格レベル](sql-database-upgrade-new-service-tiers.md)になります。


## 開始するには


データベースを Azure SQL Database V11 から V12 にアップグレードする方法については、「[Azure SQL Database V12 へのアップグレードの計画と準備をする](sql-database-v12-plan-prepare-upgrade.md)」をご覧ください。


V12 のようなバージョン番号は、次の Transact-SQL ステートメントによって返される値を指しています。<br/> **SELECT @@version;**


- 11.0.9228.18 *(V11)*
- 12.0.2000.8 *(または少し高い、V12)*


V12 に関する最新の料金の詳細については、「[SQL Database 料金](http://azure.microsoft.com/pricing/details/sql-database/)」を参照してください。


## V12 へのアップグレードに関する注意事項


V11 データベースの V12 へのアップグレード中またはアップグレード後の制限事項について知っておく必要のある重要事項があります。詳細については、「*最新の SQL Database V12 へのアップグレードの計画と準備をする*」の「[中間部分](sql-database-v12-plan-prepare-upgrade.md#limitations)」をご覧ください。


## その他の最新ニュース


Azure SQL Database V12 に関するこの*新機能*のトピックは最終版となる予定で、2015 年 4 月 30 日以降は更新されなる可能性があります。新機能に関する情報やその他のアナウンスは、次のお知らせリンクに移行します。


- **サービスの更新情報** Web ページで Azure SQL Database について[お知らせする](http://azure.microsoft.com/updates/?service=sql-database)
 - [サービス] を *[SQL Database]* に設定している場合は、この Web ページで **RSS** アイコンをクリックすることをお勧めします。
- Twitter でフォローする: **@SQLTechCenter**


[V12 general availability (GA) status per region]: #V12AzureSqlDbPreviewGaTable

<!---HONumber=58--> 