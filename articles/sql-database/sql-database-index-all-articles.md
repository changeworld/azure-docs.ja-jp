<properties
	pageTitle="SQL Database サービスに関するすべてのトピック | Microsoft Azure"
	description="http://azure.microsoft.com/documentation/articles/ に存在する SQL Database という名前の Azure サービスに関するすべてのトピックの一覧 (タイトルと説明)。"
	services="sql-database"
	documentationCenter=""
	authors="MightyPen"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.workload="sql-database"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/21/2016"
	ms.author="genemi"/>


# Azure SQL Database サービスに関するすべてのトピック

このトピックでは、Azure **SQL Database** サービスに直接適用されるすべてのトピックを示します。この Web ページでは、**Ctrl + F** を使用してキーワード検索することで、現在関心があるトピックを見つけることができます。




## 新規

| &nbsp; | タイトル | 説明 |
| --: | :-- | :-- |
| 1 | [Azure SQL Database の JSON 機能の概要](sql-database-json-features.md) | Azure SQL Database では、JavaScript Object Notation (JSON) 表記法でデータを解析、照会および書式設定できます。 |
| 2 | [SQL Database と SQL Data Warehouse での Azure AD MFA のための SSMS のサポート](sql-database-ssms-mfa-authentication.md) | SQL Database と SQL Data Warehouse の SSMS で Multi-Factored Authentication を使用します。 |


## 更新された記事

このセクションでは、最近、大幅な更新または重要な更新が行われた記事を示します。更新された各記事について、追加されたマークダウン テキストが大まかに抜粋されています。これらの記事は、**2016 年 7 月 26 日**～ **2016 年 8 月 21 日**に更新されたものです。

| &nbsp; | 記事 | 更新されたテキスト (抜粋) |
| --: | :-- | :-- |
| 3 | [PowerShell を使用して BACPAC ファイルに Azure SQL Database をアーカイブする](sql-database-export-powershell.md) | $subscriptionId = "YOUR AZURE SUBSCRIPTION ID" Login-AzureRmAccount Set-AzureRmContext -SubscriptionId $subscriptionId Database to export $DatabaseName = "DATABASE-NAME" $ResourceGroupName = "RESOURCE-GROUP-NAME" $ServerName = "SERVER-NAME" $serverAdmin = "ADMIN-NAME" $serverPassword = "ADMIN-PASSWORD" $securePassword = ConvertTo-SecureString ΓÇôString $serverPassword ΓÇôAsPlainText -Force $creds = New-Object ΓÇôTypeName System.Management.Automation.PSCredential ΓÇôArgumentList $serverAdmin, $securePassword Generate a unique filename for the BACPAC $bacpacFilename = $DatabaseName + (Get-Date).ToString("yyyyMMddHHmm") + ".bacpac" Storage account info for the BACPAC $BaseStorageUri = "https://STORAGE-NAME.blob.core.windows.net/BLOB-CONTAINER-NAME/" $BacpacUri = $BaseStorageUri + $bacpacFilename $StorageKeytype = "StorageAccessKey" $StorageKey = "YOUR STORAGE KEY" $exportRequest = New-AzureRmSqlDatabaseExpo |
| 4 | [SQL Database のオプションとパフォーマンス: 各サービス階層で使用できる内容について理解します](sql-database-service-tiers.md) | **サービス階層の選択** サービス階層を決めるにはまず、データベースをスタンドアロン データベースにするか、エラスティック プールに追加するかを決定します。**スタンドアロン データベースのサービス階層の選択** スタンドアロン データベースのサービス階層を決めるにあたって最初に行うことは、SQL Database のエディションを選ぶために、必要なデータベース機能を特定することです。/ データベース サイズ (Basic では最大 5 GB、Standard では最大 250 GB、Premium では最大 500 GB ～ 1 TB、パフォーマンス レベルによって異なる) / データベース バックアップの保有期間 (Basic では 7 日間、Standard では 35 日間、Premium では 35 日間) SQL Database のエディションを選んだら、データベースのパフォーマンス レベル (DTU 数) を決めます。見当をつけたうえで実際の使用感に基づいて動的にスケールアップしたりスケールダウンしたり (sql-database-scale-up.md) することができます。DTU Calculator (http://dtucalculator.azurewebsites.net/) を使用して、必要な DTU 数を見積もることもできます。** C |





## 作業開始

| &nbsp; | タイトル | Description |
| --: | :-- | :-- |
| 5 | [Azure SQL Database による分離性と効率性を備えたマルチテナント アプリの構築](sql-database-build-multi-tenant-apps.md) | SQL Database でマルチテナント アプリを構築する方法を説明します |
| 6 | [SQL Server Management Studio を使用して SQL Database に接続し、T-SQL サンプル クエリを実行する](sql-database-connect-query-ssms.md) | SQL Server Management Studio (SSMS) を使用して Azure で SQL Database に接続する方法について説明します。次に、TRANSACT-SQL (T-SQL) を使用して、サンプル クエリを実行します。 |
| 7 | [.NET (C#) を使用して SQL Database に接続する](sql-database-develop-dotnet-simple.md) | このクイック スタートのコード サンプルを使用して、C# で最新のアプリケーションを構築し、Azure SQL Database を使用してクラウドの強力なリレーショナル データベースでバックアップします。 |
| 8 | [Azure ポータルによる新しい Elastic Database プールの作成](sql-database-elastic-pool-create-portal.md) | SQL Database 構成にスケーラブルなエラスティック データベース プールを追加して、多数のデータベースの管理とリソース共有を簡単にする方法について説明します。 |
| 9 | [Azure SQL Database チュートリアルの確認](sql-database-explore-tutorials.md) | SQL Database の機能について説明します。 |
| 10 | [SQL Database チュートリアル: Azure ポータルを使用して数分で SQL データベースを作成する](sql-database-get-started.md) | SQL Database 論理サーバー、サーバー ファイアウォール規則、SQL Database、サンプル データを設定し、クライアント ツールに接続し、ユーザーとデータベースとファイアウォール規則を構成する方法について説明します。 |
| 11 | [Azure SQL Database のセキュリティと保護](sql-database-helps-secures-and-protects.md) | SQL Database でセキュリティと保護を支援する方法 |
| 12 | [Azure SQL Database の学習と適応](sql-database-learn-and-adapt.md) | Azure SQL Database の学習と適応について説明します |
| 13 | [クラウド SQL Server オプションの選択: Azure SQL (PaaS) Database または Azure VM (IaaS) の SQL Server](sql-database-paas-vs-sql-server-iaas.md) | Azure SQL Database (PaaS) とクラウドの Azure Virtual Machines 上の SQL Server のどちらのクラウド SQL Server オプションがお客様のアプリケーションに適合するかを説明します。 |
| 14 | [Azure SQL Database の即時スケール](sql-database-scale-on-the-fly.md) | Azure SQL Database の即時スケールについて説明します |
| 15 | [Azure SQL Database ソリューション クイック スタート ガイド](sql-database-solution-quick-starts.md) | Azure SQL Database ソリューションの詳細 |
| 16 | [ご利用の環境での Azure SQL Database の使用](sql-database-works-in-your-environment.md) | SQL Database でセキュリティと保護を支援する方法 |



## アプリの構築

| &nbsp; | タイトル | Description |
| --: | :-- | :-- |
| 17 | [SQL Database の SQL 接続エラーと一時エラーのトラブルシューティング、診断、防止](sql-database-connectivity-issues.md) | Azure SQL Database での SQL 接続エラーまたは一時エラーのトラブルシューティング、診断、防止の方法について説明します。 |
| 18 | [Visual Studio で SQL Database に接続する](sql-database-connect-query.md) | SQL データベースへのクエリおよび接続を実行するプログラムを C# で作成します。IP アドレス、接続文字列、セキュリティで保護されたログイン、および無料版の Visual Studio に関する情報。 |
| 19 | [Ports beyond 1433 for ADO.NET 4.5, and SQL Database V12 (ADO.NET 4.5、SQL Database V12 における 1433 以外のポート)](sql-database-develop-direct-route-ports-adonet-v12.md) | ADO.NET から Azure SQL Database V12 へのクライアント接続では、プロキシを使用せずに、データベースと直接やり取りする場合があります。1433 以外のポートが重要になります。 |
| 20 | [SQL Database クライアント アプリケーションの SQL エラー コード: データベース接続エラーとその他の問題](sql-database-develop-error-messages.md) | よくあるデータベース接続エラー、データベース コピーの問題、一般エラーなど、SQL Database クライアント アプリケーションの SQL エラー コードについて説明します。 |
| 21 | [Windows 上で PHP を使用して SQL Database に接続する](sql-database-develop-php-simple.md) | Windows クライアントから、Azure SQL Database に接続して、クライアントが必要とするソフトウェア コンポーネントへのリンクを提供するサンプル PHP プログラムを示します。 |
| 22 | [SQL Database を試す: C# を使用して SQL Database Library for .NET で SQL Database を作成する](sql-database-get-started-csharp.md) | SQL Database で SQL および C# アプリケーションを開発し、C# と SQL Database Library for .NET を使用して、Azure SQL Database を作成します。 |
| 23 | [Azure SQL Database との接続に関する一般的な問題のトラブルシューティング](sql-database-troubleshoot-common-connection-issues.md) | Azure SQL Database の一般的な接続エラーを特定して解決する手順。 |
| 24 | [SQL データベースに接続するときの "サーバーのデータベースは現在使用できません" というエラー](sql-database-troubleshoot-connection.md) | アプリケーションが SQL データベースに接続するときの "サーバーのデータベースは現在使用できません" というエラーをトラブルシューティングします。 |



## 開発

| &nbsp; | タイトル | Description |
| --: | :-- | :-- |
| 25 | [コードから SQL Database に接続するためのクライアント ID とキーを取得する](sql-database-client-id-keys.md) | コードから SQL Database にアクセスするためのクライアント ID とキーを取得します。 |
| 26 | [Windows 上で JDBC を含む Java を使用して、SQL Database に接続する](sql-database-develop-java-simple.md) | Azure SQL Database への接続に使用できる Java コード サンプルについて説明します。サンプルは JDBC を使用し、Windows クライアント コンピューター上で実行されます。 |
| 27 | [Node.js を使用して SQL Database に接続する](sql-database-develop-nodejs-simple.md) | Azure SQL Database への接続に使用できる Node.js コード サンプルについて説明します。 |
| 28 | [SQL Database の開発: 概要](sql-database-develop-overview.md) | SQL Database に接続するアプリケーションで使用できる接続ライブラリとベスト プラクティスについて説明します。 |
| 29 | [Python を使用して SQL Database に接続する](sql-database-develop-python-simple.md) | Azure SQL Database への接続に使用できる Python コード サンプルについて説明します。 |
| 30 | [Ruby を使用して SQL Database に接続する](sql-database-develop-ruby-simple.md) | Azure SQL Database に接続するための Ruby コード サンプルを提供します。 |
| 31 | [Azure SQL Database のテンポラル テーブルの概要](sql-database-temporal-tables.md) | Azure SQL Database のテンポラル テーブルの使い方について基本的な事柄を説明します。 |



## パフォーマンスとスケール

| &nbsp; | タイトル | Description |
| --: | :-- | :-- |
| 32 | [SQL Database Advisor](sql-database-advisor.md) | Azure SQL Database Advisor は、現在のクエリのパフォーマンスを向上できる、既存の SQL Database 向けの推奨事項を提供します。 |
| 33 | [SQL Database Advisor](sql-database-advisor-portal.md) | Azure SQL Database Advisor を Azure ポータルで使用して、現在のクエリのパフォーマンスを向上できる、既存の SQL Database 向けの推奨事項の確認および実装ができます。 |
| 34 | [Azure SQL Database ベンチマークの概要](sql-database-benchmark-overview.md) | このトピックでは、Azure SQL Database のパフォーマンス測定で使用される Azure SQL Database ベンチマークについて説明します。 |
| 35 | [エラスティック データベース プールの使用に適した状況](sql-database-elastic-pool-guidance.md) | 弾力性データベース プールは、弾力性データベースのグループで共有される使用可能なリソースのコレクションです。このドキュメントは、データベースのグループに対して、エラスティック データベース プールを使用することが適切であるか評価するガイダンスです。 |
| 36 | [Elastic Database ツールの概要](sql-database-elastic-scale-get-started.md) | 実行が容易なサンプル アプリケーションを含む、Azure SQL Database の Elastic Database ツール機能の基本説明です。 |
| 37 | [SQL Database に関する FAQ](sql-database-faq.md) | クラウド データベースと Azure SQL Database、Microsoft のリレーショナル データベース管理システム (RDBMS)、およびクラウド内のサービスとしてのデータベースのよくある質問に対する回答です。 |
| 38 | [SQL Database でのインメモリ (プレビュー) の使用](sql-database-in-memory.md) | SQL インメモリは、トランザクション ワークロードと分析ワークロードのパフォーマンスを大幅に向上するテクノロジです。これらのテクノロジを活用する方法について説明します。 |
| 39 | [インメモリ OLTP (プレビュー) を使用して SQL Database のアプリケーション パフォーマンスを向上させる](sql-database-in-memory-oltp-migration.md) | インメモリ OLTP を導入して、既存の SQL Database のトランザクション パフォーマンスを向上させます。 |
| 40 | [インメモリ OLTP ストレージの監視](sql-database-in-memory-oltp-monitoring.md) | XTP インメモリ ストレージの使用量と容量を推定し、監視します。また、容量不足エラー 41823 を解決します。 |
| 41 | [Azure SQL Database のクエリ ストアの動作](sql-database-operate-query-store.md) | Azure SQL Database でクエリ ストアがどのように動作するかについて説明します |
| 42 | [SQL Database Performance Insight](sql-database-performance.md) | Azure SQL Database では、現在のクエリのパフォーマンスを向上させる余地がある領域を識別するのに役立つパフォーマンス ツールを提供します。 |
| 43 | [データベースが 1 台の場合の Azure SQL Database のパフォーマンス ガイダンス](sql-database-performance-guidance.md) | このトピックでは、お使いのアプリケーションに最適なサービス層を決める際の指針と Azure SQL Database を最大限活用するためにアプリケーションを調整する際の推奨事項について説明します。 |
| 44 | [Azure SQL Database Query Performance Insight](sql-database-query-performance.md) | クエリのパフォーマンスを監視して、Azure SQL Database の CPU 消費量の多いクエリを明らかにします。 |
| 45 | [SQL Database のサービス階層とパフォーマンス レベル (価格レベル) を変更する](sql-database-scale-up.md) | 「Azure SQL Database のサービス階層とパフォーマンス レベルを変更する」では、SQL Databaseのスケール アップとスケール ダウンの方法について説明しています。Azure SQL Database の価格レベルを変更します。 |
| 46 | [Azure SQL Database におけるデータベース パフォーマンスの監視](sql-database-single-database-monitor.md) | Azure ツールと動的管理ビューを使用してデータベースを監視するためのオプションについて説明します。 |
| 47 | [SQL Database のパフォーマンスのチューニングのヒント](sql-database-troubleshoot-performance.md) | 評価と改善を通じて Azure SQL Database のパフォーマンスをチューニングするためのヒントを紹介します。 |
| 48 | [バッチ処理を使用して SQL Database アプリケーションのパフォーマンスを強化する方法](sql-database-use-batching-to-improve-performance.md) | このトピックでは、データベースの操作をバッチ処理で行うことによって、Azure SQL Database アプリケーションの速度とスケーラビリティが大幅に向上することを実証しています。紹介しているバッチ処理手法は SQL Server データベースにも有効ですが、この記事では Azure に焦点を絞って取り上げています。 |



## スケールアウトのツール

| &nbsp; | タイトル | 説明 |
| --: | :-- | :-- |
| 49 | [マルチテナント SaaS アプリケーションと Azure SQL Database の設計パターン](sql-database-design-patterns-multi-tenancy-saas-applications.md) | この記事では、クラウド環境で実行されるマルチテナント データベース アプリケーションで考慮する必要がある要件と一般的なデータ アーキテクチャ パターン、およびこれらのパターンに関連するさまざまなトレードオフについて説明します。また、エラスティック データベース プールとエラスティック ツールを備えた Azure SQL Database を使用して、妥協のない方法でこれらの要件に対応する方法についても説明します。 |
| 50 | [既存のデータベースを移行してスケールアウト](sql-database-elastic-convert-to-use-elastic-tools.md) | シャード マップ マネージャーを作成することで、エラスティック データベース ツールを使用するようにシャード化されたデータベースを変換します |
| 51 | [スケーラブルなクラウド データベースの構築](sql-database-elastic-database-client-library.md) | エラスティック データベース クライアント ライブラリでスケーラブルな .NET データベース アプリを構築します。 |
| 52 | [シャード マップ マネージャーのパフォーマンス カウンター](sql-database-elastic-database-perf-counters.md) | ShardMapManager クラスとデータ依存ルーティング パフォーマンス カウンター |
| 53 | [Elastic Database ツールを使用してシャードを追加する](sql-database-elastic-scale-add-a-shard.md) | Elastic Scale API を使用して新しいシャードをシャード セットに追加する方法。 |
| 54 | [split-merge サービスのデプロイ](sql-database-elastic-scale-configure-deploy-split-and-merge.md) | エラスティック データベース ツールによる分割とマージ |
| 55 | [データ依存ルーティング](sql-database-elastic-scale-data-dependent-routing.md) | データ依存ルーティング (Azure SQL Database のElastic Database の機能) のために .NET アプリで ShardMapManager クラスを使用する方法について説明します。 |
| 56 | [エラスティック データベース ツールに関する FAQ](sql-database-elastic-scale-faq.md) | Azure SQL Database の Elastic Scale に関してよく寄せられる質問。 |
| 57 | [Elastic Database ツールの用語集](sql-database-elastic-scale-glossary.md) | エラスティック データベース ツールで使用される用語の説明 |
| 58 | [Azure SQL Database によるスケールアウト](sql-database-elastic-scale-introduction.md) | これらのツールを使用すると、Software as a Service (SaaS) の開発者は柔軟で拡張性の高いデータベースを簡単に作成できます。 |
| 59 | [Elastic Database クライアント ライブラリへのアクセスに使用する資格情報](sql-database-elastic-scale-manage-credentials.md) | エラスティック データベース アプリの適切な資格情報のレベルを設定する方法 (管理者から読み取り専用) |
| 60 | [マルチシャード クエリ実行](sql-database-elastic-scale-multishard-querying.md) | エラスティック データベース クライアント ライブラリを使用して複数のシャードを対象にクエリを実行します。 |
| 61 | [スケールアウトされたクラウド データベース間のデータ移動](sql-database-elastic-scale-overview-split-and-merge.md) | エラスティック データベース API を使用して、シャードを操作し、自己ホスト サービス経由でデータを移動する方法について説明します。 |
| 62 | [シャード マップ マネージャーでデータベースをスケールアウトする](sql-database-elastic-scale-shard-map-management.md) | Elastic Database クライアント ライブラリの ShardMapManager の使用方法 |
| 63 | [Split-Merge セキュリティの構成](sql-database-elastic-scale-split-merge-security-configuration.md) | 暗号化のための x409 証明書の設定 |
| 64 | [最新のエラスティック データベース クライアント ライブラリを使用するためのアプリのアップグレード](sql-database-elastic-scale-upgrade-client-library.md) | Nuget を使用してアプリとライブラリをアップグレードする |
| 65 | [Entity Framework による Elastic Database クライアント ライブラリ](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md) | Elastic Database クライアント ライブラリと Entity Framework をデータベースのコーディングに使用する |
| 66 | [Dapper でのエラスティック データベース クライアント ライブラリの使用](sql-database-elastic-scale-working-with-dapper.md) | Dapper でのエラスティック データベース クライアント ライブラリの使用。 |
| 67 | [弾力性データベース ツールと行レベルのセキュリティを使用したマルチテナント アプリケーション](sql-database-elastic-tools-multi-tenant-row-level-security.md) | 弾力性データベース ツールと行レベルのセキュリティを使用して、Azure SQL Database にマルチテナントのシャードをサポートする拡張性の高いデータ層を持つアプリケーションを作成する方法について説明します。 |



## エラスティック ジョブ

| &nbsp; | タイトル | 説明 |
| --: | :-- | :-- |
| 68 | [エラスティック ジョブを使用したスケールアウト済み Azure SQL Database の作成と管理 (プレビュー)](sql-database-elastic-jobs-create-and-manage.md) | エラスティック データベースのジョブの作成と管理について説明します。 |
| 69 | [Elastic Database ジョブの概要](sql-database-elastic-jobs-getting-started.md) | エラスティック データベース ジョブの使用方法 |
| 70 | [スケールアウトされたクラウド データベースの管理](sql-database-elastic-jobs-overview.md) | エラスティック データベース ジョブ サービスの説明 |
| 71 | [PowerShell を使用した SQL Database のエラスティック データベース ジョブの作成と管理 (プレビュー)](sql-database-elastic-jobs-powershell.md) | Azure SQL Database プールを管理するために使用するPowerShell |
| 72 | [Elastic Database ジョブのインストールの概要](sql-database-elastic-jobs-service-installation.md) | 弾力性ジョブの機能のインストールについて説明します。 |
| 73 | [エラスティック データベース ジョブ コンポーネントのアンインストール](sql-database-elastic-jobs-uninstall.md) | エラスティック データベース ジョブ ツールのアンインストール方法 |



## エラスティック プール

| &nbsp; | タイトル | 説明 |
| --: | :-- | :-- |
| 74 | [Azure エラスティック データベース プールの概要](sql-database-elastic-pool.md) | プールを使用して数百または数千のデータベースを管理します。一連のパフォーマンス ユニットの単一価格をプール上で配布できます。データベースは自由に内外に移動できます。 |
| 75 | [C# による新しいエラスティック データベース プールの作成](sql-database-elastic-pool-create-csharp.md) | 多数のデータベースでリソースを共有できるように、C# データベース開発手法を使用して、Azure SQL Database にスケーラブルなエラスティック データベース プールを作成します。 |
| 76 | [PowerShell による新しい Elastic Database プールの作成](sql-database-elastic-pool-create-powershell.md) | PowerShell を使用し、複数のデータベースを管理するスケーラブルなエラスティック データベース プールを作成して、Azure SQL Database リソースをスケールアウトする方法について説明します。 |
| 77 | [C# データベース開発: SQL データベースのエラスティック データベース プールの作成と構成](sql-database-elastic-pool-csharp.md) | 多数のデータベース全体でリソースを共有できるように、C# データベース開発手法を使用して Azure SQL Database エラスティック データベース プールを作成します。 |
| 78 | [エラスティック データベース プールに適したデータベースを識別する Powershell スクリプト](sql-database-elastic-pool-database-assessment-powershell.md) | 弾力性データベース プールは、弾力性データベースのグループで共有される使用可能なリソースのコレクションです。このドキュメントでは、データベースのグループに対して、エラスティック データベース プールを使用することが適切であるか評価する PowerShell スクリプトについて説明します。 |
| 79 | [C# でのエラスティック データベース プールの監視と管理](sql-database-elastic-pool-manage-csharp.md) | C# データベース開発技術を使用して Azure SQL Database エラスティック データベース プールを管理します。 |
| 80 | [Azure ポータルを使用したエラスティック データベース プールの監視と管理](sql-database-elastic-pool-manage-portal.md) | Azure ポータルと SQL Database の組み込みのインテリジェンスを使用して、スケーラブルなエラスティック データベース プールの管理、監視を行い、適切なサイズを設定して、データベース パフォーマンスの最適化とコストの管理を行う方法について説明します。 |
| 81 | [PowerShell でのエラスティック データベース プールの監視と管理](sql-database-elastic-pool-manage-powershell.md) | PowerShell を使用してエラスティック データベース プールを管理する方法について説明します。 |
| 82 | [Transact-SQL を使用したエラスティック データベース プールの監視と管理](sql-database-elastic-pool-manage-tsql.md) | T-SQL を使用してエラスティック プール内に Azure SQL データベースを作成します。または、T-SQL を使用してエラスティック プールにデータベースを移動したり、エラスティック プールからデータベースを移動したりします。 |
| 83 | [エラスティック データベース プールの課金と価格情報](sql-database-elastic-pool-price.md) | エラスティック データベース プール固有の価格情報。 |



## エラスティック クエリ

| &nbsp; | タイトル | 説明 |
| --: | :-- | :-- |
| 84 | [スケールアウトされたクラウド データベース全体のレポート (プレビュー)](sql-database-elastic-query-getting-started.md) | データベース間データベース クエリを使用する方法 |
| 85 | [クロスデータベース クエリの概要 (列方向のパーティション分割) (プレビュー)](sql-database-elastic-query-getting-started-vertical.md) | 垂直にパーティション分割されたデータベースでエラスティック データベース クエリを使用する方法 |
| 86 | [スケールアウトされたクラウド データベース全体をレポートする (プレビュー)](sql-database-elastic-query-horizontal-partitioning.md) | 行方向のパーティション分割でエラスティック クエリを設定する方法 |
| 87 | [Azure SQL Database エラスティック データベース クエリの概要 (プレビュー)](sql-database-elastic-query-overview.md) | エラスティック クエリ機能の概要 |
| 88 | [スキーマが異なるクラウド データベース間のクエリ (プレビュー)](sql-database-elastic-query-vertical-partitioning.md) | 列方向のパーティションでデータベース間クエリを設定する方法 |



## エラスティック トランザクション

| &nbsp; | タイトル | 説明 |
| --: | :-- | :-- |
| 89 | [クラウド データベースにまたがる分散トランザクション](sql-database-elastic-transactions-overview.md) | Azure SQL Database を使用した Elastic Database トランザクションの概要 |



## バックアップと回復

| &nbsp; | タイトル | 説明 |
| --: | :-- | :-- |
| 90 | [SQL Database 自動バックアップ](sql-database-automated-backups.md) | SQL Database 組み込みバックアップについて説明します。この機能を使用すると、Azure SQL Database を以前の時点にロールバックしたり、データベースを地理的リージョン内の新しいデータベースにコピーしたりできます (最大 35 日)。 |
| 91 | [Azure SQL Database によるビジネス継続性の概要](sql-database-business-continuity.md) | Azure SQL Database がどのようにクラウド ビジネス継続性とデータベース復旧をサポートし、ミッション クリティカルなクラウド アプリケーションの実行を維持できるようにするかについて説明します。 |
| 92 | [Azure SQL Database のバックアップから 1 つのテーブルを復元する方法](sql-database-cloud-migrate-restore-single-table-azure-backup.md) | Azure SQL Database のバックアップから 1 つのテーブルを復元する方法を説明します。 |
| 93 | [SQL Database のアクティブ geo レプリケーションを使用したクラウド障害復旧用アプリケーションの設計](sql-database-designing-cloud-solutions-for-disaster-recovery.md) | アプリ データのバックアップに Azure SQL Database と geo レプリケーションを使用して、ビジネス継続性計画を目的としたクラウド障害復旧ソリューションを設計する方法について説明します。 |
| 94 | [Azure SQL Database を復元する、またはセカンダリにフェールオーバーする](sql-database-disaster-recovery.md) | Azure SQL Database のアクティブ geo レプリケーションと geo リストア機能を使用して、地域のデータ センターの停止や障害からデータベースを復旧する方法について説明します。 |
| 95 | [障害復旧訓練の実行](sql-database-disaster-recovery-drills.md) | Azure SQL Database を使用して、ミッション クリティカルなビジネス アプリケーションがエラーと障害に対して回復力を保持するための障害復旧訓練を実行するためのガイダンスとベスト プラクティスについて説明します。 |
| 96 | [SQL Database エラスティック プールを使用した、アプリケーションの障害復旧戦略](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md) | フェールオーバー パターンを適切に選んで、障害復旧を実現するクラウド ソリューションを設計する方法について説明します。 |
| 97 | [RecoveryManager クラスを使用したシャード マップに関する問題の解決](sql-database-elastic-database-recovery-manager.md) | RecoveryManager クラスを使用したシャード マップに関する問題の解決 |
| 98 | [BACPAC ファイルをインポートして新しい Azure SQL Database を作成する](sql-database-import.md) | 既存の BACPAC ファイルをインポートして新しい Azure SQL Database を作成する |
| 99 | [Azure ポータルで以前の時点に Azure SQL Database を復元する](sql-database-point-in-time-restore-portal.md) | 以前の時点に Azure SQL Database を復元します。 |
| 100 | [PowerShell で以前の時点に Azure SQL Database を復元する](sql-database-point-in-time-restore-powershell.md) | 以前の時点に Azure SQL Database を復元する |
| 101 | [復旧された Azure SQL データベースの最終処理を行う](sql-database-recovered-finalize.md) | ポイント イン タイム リストア、Microsoft Azure SQL Database、データベースの復元、データベースの復旧、Azure クラシック ポータル、Azure クラシック ポータル |
| 102 | [データベースの自動バックアップを使用した Azure SQL Database の復旧](sql-database-recovery-using-backups.md) | Azure SQL Database を特定の時点 (最長 35 日間) にロール バックすることができる、ポイントインタイム リストアについて説明します。 |
| 103 | [Azure ポータルを使用した、削除済み Azure SQL データベースの復元](sql-database-restore-deleted-database-portal.md) | 削除された Azure SQL データベースの復元 (Azure ポータル)。 |
| 104 | [PowerShell を使用した、削除済み Azure SQL Database の復元](sql-database-restore-deleted-database-powershell.md) | 削除された Azure SQL Database の復元 (PowerShell) |
| 105 | [過去のある時点へのデータベースの復元、削除したデータベースの復元、またはデータ センターの障害からの回復](sql-database-troubleshoot-backup-and-restore.md) | Azure SQL Database のバックアップおよびレプリカを使用した、クラウド データベースのエラーおよび障害からの回復方法について説明します。 |



## 移行

| &nbsp; | タイトル | Description |
| --: | :-- | :-- |
| 106 | [SqlPackage を使用して SQL Server データベースを BACPAC ファイルにエクスポートする](sql-database-cloud-migrate-compatible-export-bacpac-sqlpackage.md) | Microsoft Azure SQL Database、データベースの移行、データベースのエクスポート、BACPAC ファイルのエクスポート、sqlpackage |
| 107 | [SQL Server Management Studio を使用して SQL Server データベースを BACPAC ファイルにエクスポートする](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md) | Microsoft Azure SQL Database、データベースの移行、データベースのエクスポート、BACPAC ファイルのエクスポート、データ層アプリケーションのエクスポート ウィザード |
| 108 | [SqlPackage を使用して BACPAC ファイルから SQL Database にインポートする](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md) | Microsoft Azure SQL Database、データベースの移行、データベースのインポート、BACPAC ファイルのインポート、sqlpackage |
| 109 | [SSMS を使用して BACPAC から SQL Database にインポートする](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md) | Microsoft Azure SQL Database、データベースのデプロイ、データベースの移行、データベースのインポート、データベースのエクスポート、移行ウィザード |
| 110 | [[データベースの Microsoft Azure Database へのデプロイ] ウィザードを使用して SQL Server データベースを SQL Database に移行する](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md) | Microsoft Azure SQL Database、データベースの移行、Microsoft Azure Database ウィザード |
| 111 | [トランザクション レプリケーションを使用して SQL Server データベースを Azure SQL Database に移行する](sql-database-cloud-migrate-compatible-using-transactional-replication.md) | Microsoft Azure SQL Database、データベースの移行、データベースのインポート、トランザクション レプリケーション |
| 112 | [SqlPackage.exe を使用して SQL Database の互換性を判定する](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md) | Microsoft Azure SQL Database、データベースの移行、SQL Database の互換性、SqlPackage |
| 113 | [Azure SQL Database へ移行する前にSQL Server Management Studio を使用して SQL Database の互換性を判定する](sql-database-cloud-migrate-determine-compatibility-ssms.md) | Microsoft Azure SQL Database、データベースの移行、SQL Database の互換性、[データ層アプリケーションのエクスポート] ウィザード |
| 114 | [Azure SQL Database へ移行する前に SQL Azure 移行ウィザードを使用して SQL Server データベースの互換性に関する問題を修正する](sql-database-cloud-migrate-fix-compatibility-issues.md) | Microsoft Azure SQL Database、データベースの移行、互換性、SQL Azure の移行ウィザード |
| 115 | [Visual Studio 用の SQL Server Data Tools を使用して Azure SQL Database に SQL Server データベースを移行する](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md) | Microsoft Azure SQL Database、データベースの移行、互換性、SQL Azure の移行ウィザード、SSDT |
| 116 | [SQL Database へ移行する前に SQL Server Management Studio を使用して SQL Server データベースの互換性に関する問題を修正する](sql-database-cloud-migrate-fix-compatibility-issues-ssms.md) | Microsoft Azure SQL Database、データベースの移行、互換性、SQL Azure の移行ウィザード |
| 117 | [PowerShell を使用して BACPAC ファイルに Azure SQL Database をアーカイブする](sql-database-export-powershell.md) | PowerShell を使用して BACPAC ファイルに Azure SQL Database をアーカイブする |
| 118 | [PowerShell で BACPAC ファイルをインポートして新しい Azure SQL Database を作成する](sql-database-import-powershell.md) | PowerShell で BACPAC ファイルをインポートして新しい Azure SQL Database を作成する |
| 119 | [CSV から Azure SQL Data Warehouse へのデータの読み込み (フラット ファイル)](sql-database-load-from-csv-with-bcp.md) | データ サイズが小さい場合は、bcp を使用して Azure SQL Database にデータをインポートできます。 |
| 120 | [サーバー間、サブスクリプション間、および Azure の内外にデータベースを移動する](sql-database-troubleshoot-moving-data.md) | Azure SQL Database でデータとデータベースをコピー、移動、および移行するための簡単な手順。 |



## データの移動

| &nbsp; | タイトル | Description |
| --: | :-- | :-- |
| 121 | [SQL Server データベースのクラウド内の SQL Database への移行](sql-database-cloud-migrate.md) | オンプレミスの SQL Server データベースをクラウド内の Azure SQL Database に移行する方法について説明します。データベース移行ツールを使用すると、データベースの移行前に互換性をテストできます。 |
| 122 | [Azure SQL Database のコピー](sql-database-copy.md) | Azure SQL Database のコピーの作成 |
| 123 | [Azure ポータルを使用して BACPAC ファイルに Azure SQL Database をアーカイブする](sql-database-export.md) | Azure ポータルを使用して BACPAC ファイルに Azure SQL Database をアーカイブする |



## セキュリティ

| &nbsp; | タイトル | Description |
| --: | :-- | :-- |
| 124 | [Azure Active Directory 認証を使用して SQL Database または SQL Data Warehouse に接続する](sql-database-aad-authentication.md) | Azure Active Directory 認証を使用して SQL Database に接続する方法について説明します。 |
| 125 | [Always Encrypted: データベース暗号化を使用して SQL Database で機密データを保護し、Windows 証明書ストアで暗号化キーを格納する](sql-database-always-encrypted.md) | SQL Database の機密データをわずか数分で保護します。 |
| 126 | [Always Encrypted: データ暗号化を使用して SQL Database で機密データを保護し、Azure Key Vault で暗号化キーを格納する](sql-database-always-encrypted-azure-key-vault.md) | SQL Database の機密データをわずか数分で保護します。 |
| 127 | [SQL Database - 監査のためのダウンレベル クライアントのサポートと IP エンドポイントの変更](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md) | SQL Database での監査のためのダウンレベル クライアントのサポートと IP エンドポイントの変更について説明します。 |
| 128 | [PowerShell を使用して Azure SQL Database のサーバーレベルのファイアウォール規則を構成する](sql-database-configure-firewall-settings-powershell.md) | Azure SQL データベースにアクセスする IP アドレス用のファイアウォールの構成方法を説明します。 |
| 129 | [REST API を使用して Azure SQL Database ファイアウォールを構成する](sql-database-configure-firewall-settings-rest.md) | Azure SQL データベースにアクセスする IP アドレス用のファイアウォールの構成方法を説明します。 |
| 130 | [TSQL を使用して Azure SQL Database ファイアウォールを構成する](sql-database-configure-firewall-settings-tsql.md) | Azure SQL データベースにアクセスする IP アドレス用のファイアウォールの構成方法を説明します。 |
| 131 | [SQL Database 動的データ マスクの使用 (Azure ポータル)](sql-database-dynamic-data-masking-get-started.md) | Azure ポータルでの SQL Database 動的データ マスクの使用方法 |
| 132 | [SQL Database 動的データ マスクの使用 (Azure クラシック ポータル)](sql-database-dynamic-data-masking-get-started-portal.md) | Azure クラシック ポータルでの SQL Database 動的データ マスクの使用方法 |
| 133 | [Azure SQL Database ファイアウォール規則の構成 - 概要](sql-database-firewall-configure.md) | アクセスを管理するために、サーバー レベルおよびデータベース レベルのファイアウォール規則を使用して SQL データベース ファイアウォールを構成する方法について説明します。 |
| 134 | [SQL Database チュートリアル: データベースにアクセスして管理するための SQL Database ユーザー アカウントを作成する](sql-database-get-started-security.md) | ユーザー アカウントを作成してアクセスし、データベースを管理する方法について説明します。 |
| 135 | [SQL Database の認証と承認: アクセス権の付与](sql-database-manage-logins.md) | SQL Database のセキュリティ管理 (具体的にはサーバーレベル プリンシパル アカウントを使用してデータベースのアクセスとログインのセキュリティを管理する方法) について説明します。 |
| 136 | [Azure SQL Database のセキュリティのガイドラインと制限事項](sql-database-security-guidelines.md) | Microsoft Azure SQL Database のガイドラインとセキュリティに関連する制限事項について説明します。 |
| 137 | [SQL Database 脅威の検出の概要](sql-database-threat-detection-get-started.md) | Azure ポータルでの SQL Database 脅威の検出の使用方法 |
| 138 | [Azure SQL Database で管理者パスワードのリセットなどの一般的な管理タスクを実行する方法](sql-database-troubleshoot-permissions.md) | SQL Database で一般的な管理者タスクを実行する方法について説明します。たとえば、管理者パスワードのリセット、アクセスの許可と削除などです。 |



## データベースの管理

| &nbsp; | タイトル | Description |
| --: | :-- | :-- |
| 139 | [SQL Database 監査の使用](sql-database-auditing-get-started.md) | SQL Database 監査の使用 |
| 140 | [Azure ポータルを使用して Azure SQL Database のサーバー レベルのファイアウォール規則を作成する](sql-database-configure-firewall-settings.md) | Azure SQL サーバーにアクセスする IP アドレス用のファイアウォールの構成方法を説明します。 |
| 141 | [Azure ポータルを使用した Azure SQL Database のコピー](sql-database-copy-portal.md) | Azure SQL Database のコピーの作成 |
| 142 | [Azure Automation を使用した Azure SQL データベースの管理](sql-database-manage-automation.md) | Azure Automation サービスを使用して、規模に応じて Azure SQL データベースを管理する方法について説明します。 |
| 143 | [概要: SQL Database の管理ツール](sql-database-manage-overview.md) | Azure SQL Database を管理するためのツールとオプションを比較します。 |
| 144 | [動的管理ビューを使用した Azure SQL Database の監視](sql-database-monitoring-with-dmvs.md) | 動的管理ビューを使用して Microsoft Azure SQL Database を監視することで、一般的なパフォーマンスの問題を検出および診断する方法について説明します。 |
| 145 | [SQL Database の保護](sql-database-security.md) | 認証、承認、接続のセキュリティ、暗号化、コンプライアンスなどに関するクラウドと SQL Server オンプレミスの違いなど、Azure SQL Database と SQL Server のセキュリティの詳細について説明します。 |



## 拡張イベント

| &nbsp; | タイトル | Description |
| --: | :-- | :-- |
| 146 | [SQL Database の拡張イベントのためのイベント ファイル ターゲット コード](sql-database-xevent-code-event-file.md) | Azure SQL Database の拡張イベントのイベント ファイル ターゲットを示す 2 段階コード サンプルの PowerShell と Transact-SQL を提供します。Azure Storage はこのシナリオの必須の部分です。 |
| 147 | [SQL Database の拡張イベントのためのリング バッファー ターゲット コード](sql-database-xevent-code-ring-buffer.md) | Azure SQL Database で、リング バッファー ターゲットの使用によって簡素化された TRANSACT-SQL のコード サンプルを提供します。 |
| 148 | [SQL Database の拡張イベント](sql-database-xevent-db-diff-from-svr.md) | Azure SQL Database での拡張イベント (XEvents) について、またイベント セッションが Microsoft SQL Server におけるイベント セッションと若干異なる点について説明します。 |



## geo レプリケーション

| &nbsp; | タイトル | Description |
| --: | :-- | :-- |
| 149 | [Azure ポータルを使用した Azure SQL Database の計画されたフェールオーバーまたは計画されていないフェールオーバーの開始](sql-database-geo-replication-failover-portal.md) | Azure ポータルを使用した Azure SQL Database の計画されたフェールオーバーまたは計画されていないフェールオーバーの開始 |
| 150 | [PowerShell を使用した Azure SQL Database の計画されたフェールオーバーまたは計画されていないフェールオーバーの開始](sql-database-geo-replication-failover-powershell.md) | PowerShell を使用した Azure SQL Database の計画されたフェールオーバーまたは計画されていないフェールオーバーの開始 |
| 151 | [Transact-SQL を使用した Azure SQL Database の計画されたフェールオーバーまたは計画されていないフェールオーバーの開始](sql-database-geo-replication-failover-transact-sql.md) | Transact-SQL を使用した Azure SQL Database の計画されたフェールオーバーまたは計画されていないフェールオーバーの開始 |
| 152 | [概要: Azure SQL Database のアクティブ geo レプリケーション](sql-database-geo-replication-overview.md) | アクティブ Geo レプリケーションにより、任意の Azure データ センターでデータベースのレプリカを 4 つ設定できます。 |
| 153 | [Azure ポータルを使用して Azure SQL Database の geo レプリケーションを構成する](sql-database-geo-replication-portal.md) | Azure ポータルを使用して Azure SQL Database の geo レプリケーションを構成する |
| 154 | [PowerShell を使用して Azure SQL Database の geo レプリケーションを構成する](sql-database-geo-replication-powershell.md) | PowerShell を使用して Azure SQL Database のアクティブ geo レプリケーションを構成する |
| 155 | [障害復旧後にセキュリティを管理する方法](sql-database-geo-replication-security-config.md) | このトピックでは、データベースの復元またはフェールオーバー後にセキュリティを管理する際のセキュリティに関する考慮事項について説明します。 |
| 156 | [Transact-SQL を使用して Azure SQL Database の geo レプリケーションを構成する](sql-database-geo-replication-transact-sql.md) | Transact-SQL を使用した Azure SQL Database の geo レプリケーションの構成 |
| 157 | [Azure ポータルを使用した geo 冗長バックアップからの Azure SQL Database の geo リストア](sql-database-geo-restore-portal.md) | geo 冗長バックアップから Azure SQL Database を geo リストアします (Azure ポータル)。 |
| 158 | [PowerShell を使用した geo 冗長バックアップからの Azure SQL Database の復元](sql-database-geo-restore-powershell.md) | geo 冗長バックアップから新しいサーバーに Azure SQL Database を復元します |



## アップグレード

| &nbsp; | タイトル | Description |
| --: | :-- | :-- |
| 159 | [Azure SQL Database の互換性レベル 130 によるクエリ パフォーマンスの向上](sql-database-compatibility-level-query-performance-130.md) | Azure SQL Database または Microsoft SQL Server のデータベースに最も適した互換性レベルを判断するための手順とツール |
| 160 | [SQL Database アクティブ geo レプリケーションを使用したクラウド アプリケーションのローリング アップグレードの管理](sql-database-manage-application-rolling-upgrade.md) | Azure SQL Database で geo レプリケーションを使用してクラウド アプリケーションのオンライン アップグレードをサポートする方法について説明します。 |
| 161 | [Azure ポータルを使用した Azure SQL Database V12 へのアップグレード](sql-database-upgrade-server-portal.md) | Web および Business データベースのアップグレード方法を含む Azure SQL Database V12 へのアップグレード方法を説明します。また、Azure ポータルを使用してエラスティック データベース プールにデータベースを直接移行することで V11 サーバーをアップグレードする方法も説明します。 |
| 162 | [PowerShell を使用した Azure SQL Database V12 へのアップグレード](sql-database-upgrade-server-powershell.md) | Web および Business データベースのアップグレード方法を含む Azure SQL Database V12 へのアップグレード方法を説明します。また、PowerShell を使用してエラスティック データベース プールにデータベースを直接移行することで V11 サーバーをアップグレードする方法も説明します。 |
| 163 | [SQL Database V12 へのアップグレードの計画と準備](sql-database-v12-plan-prepare-upgrade.md) | Azure SQL Database V12 へのアップグレードに関連する準備作業と制限について説明します。 |
| 164 | [SQL Database V12 の新機能](sql-database-v12-whats-new.md) | クラウドで Azure SQL Database を使用しているビジネス システムが今すぐ V12 にアップグレードでメリットを得られる理由を説明します。 |
| 165 | [Web および Business Edition の終了に関する FAQ](sql-database-web-business-sunset-faq.md) | Azure SQL Web および Business データベースの提供終了日と新しいサービス階層の機能について説明しています。 |



## ツール

| &nbsp; | タイトル | Description |
| --: | :-- | :-- |
| 166 | [PowerShell を使用した Azure SQL Database の管理](sql-database-command-line-tools.md) | PowerShell による Azure SQL Database 管理。 |
| 167 | [SQL Database チュートリアル: Excel を Azure SQL データベースに接続してレポートを作成する](sql-database-connect-excel.md) | Microsoft Excel をクラウド内の Azure SQL データベースに接続する方法について説明します。レポートの作成およびデータの探索を目的として、データを Excel にインポートします。 |
| 168 | [PowerShell コマンドレットを使用して SQL データベースを作成し、一般的なデータベース設定タスクを実行する](sql-database-get-started-powershell.md) | PowerShell で SQL データベースを作成する方法について説明します。PowerShell コマンドレットを使用して、一般的なデータベース設定タスクを管理できます。 |
| 169 | [Azure SQL データ同期の概要 (プレビュー)](sql-database-get-started-sql-data-sync.md) | このチュートリアルでは、Azure SQL データ同期 (プレビュー) の概要について説明します。 |
| 170 | [SQL Server Management Studio を使用した Azure SQL Database の管理](sql-database-manage-azure-ssms.md) | SQL Server Management Studio を使用して SQL データベース サーバーとデータベースを管理する方法について説明します。 |
| 171 | [Azure ポータルを使用した Azure SQL データベースの管理](sql-database-manage-portal.md) | Azure ポータルを使用して、クラウド内のリレーショナル データベースを管理する方法について説明します。 |
| 172 | [PowerShell で SQL Database のサービス階層とパフォーマンス レベル (価格レベル) を変更する](sql-database-scale-up-powershell.md) | 「Azure SQL Database のサービス階層とパフォーマンス レベルを変更する」では、PowerShell を使用した SQL Database のスケール アップとスケール ダウンの方法について説明しています。PowerShell を使用して Azure SQL Database の価格レベルを変更します。 |
| 173 | [Azure RemoteApp で SQL Server Management Studio を使用して SQL Database に接続する](sql-database-ssms-remoteapp.md) | このチュートリアルでは、Azure RemoteApp で SQL Server Management Studio を使用して、SQL Database に接続するときにセキュリティとパフォーマンスを確保する方法について説明します。 |



## リファレンス

| &nbsp; | タイトル | Description |
| --: | :-- | :-- |
| 174 | [SQL Database と SQL Server の接続ライブラリ](sql-database-libraries.md) | クライアント プログラムが Azure SQL Database または Microsoft SQL Server に接続する際に使用できる各ドライバーの最小バージョン番号を示します。Microsoft ではなくコミュニティからリリースされているドライバーに関するバージョン情報へのリンクが用意されています。 |
| 175 | [Azure SQL Database のリソース制限](sql-database-resource-limits.md) | このページでは、Azure SQL Database に対するいくつかの一般的なリソース制限について説明します。 |
| 176 | [Azure SQL Database の Transact-SQL の相違点](sql-database-transact-sql-information.md) | Azure SQL Database では完全にサポートされない Transact-SQL ステートメント |



## その他

| &nbsp; | タイトル | Description |
| --: | :-- | :-- |
| 177 | [PowerShell を使用した Azure SQL Database のコピー](sql-database-copy-powershell.md) | PowerShell を使用した Azure SQL Database のコピーの作成 |
| 178 | [Transact-SQL を使用した Azure SQL Database のコピー](sql-database-copy-transact-sql.md) | Transact-SQL を使用した Azure SQL Database のコピーの作成 |
| 179 | [Azure SQL Database の一般的な制限事項とガイドライン](sql-database-general-limitations.md) | このページでは、Azure SQL Database の一般的な制限事項および相互運用性とサポートの領域について説明します。 |
| 180 | [SQL Database の価格レベルの提案](sql-database-service-tier-advisor.md) | Azure ポータルで価格レベルを変更するとき、価格レベルの提案により、既存の Azure SQL Database のワークロードを実行するのに最適なレベルが提案されます。価格レベルは、SQL Database のサービス レベルとパフォーマンス レベルを説明します。 |


&nbsp;

<hr/>

<a name="extras_append"></a>

## 追加

<a name="extra_related_articles"></a>

### 他の Azure サービスの関連記事

- [Azure での Azure App Service アプリのバックアップ](../app-service-web/web-sites-backup.md)

<a name="extra_documentation_tools"></a>

### ドキュメント ツール

- [Azure SQL Database の更新情報](http://azure.microsoft.com/updates/?service=sql-database)

- [Microsoft Azure のすべてのドキュメントの種類をフィルターで検索](http://azure.microsoft.com/search/documentation/)

<a name="extra_learning_path_graphics"></a>

### ラーニング パスの図

- [すべての Microsoft Azure サービスのラーニング パス グラフィック](http://azure.microsoft.com/documentation/learning-paths/)

- [ラーニング パス: Azure SQL Database について](http://azure.microsoft.com/documentation/learning-paths/sql-database-training-learn-sql-database/)

- [ラーニング パス: SQL Database の柔軟な拡張](http://azure.microsoft.com/documentation/learning-paths/sql-database-elastic-scale/)


<!--
AzIxAA.ExtraAppend.sql-database.txt
C:\_MainW\VS15\AzureIndexAllArticles\AzureIndexAllArticles\In-Out\In\

This bullet link is improperly disallowed by publishing automation due to presence of string 'docuXXmentation/arXXticles':
- [Search SQL Database documentation, with filters](http://azure.microsoft.com/docuXXmentation/arXXticles/?service=sql-database)
-->

<!---HONumber=AcomDC_0824_2016-->