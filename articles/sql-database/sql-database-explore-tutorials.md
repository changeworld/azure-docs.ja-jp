<properties
   pageTitle="Azure SQL Database チュートリアルの確認 | Microsoft Azure"
   description="SQL Database の機能について説明します。"
   keywords=""
   services="sql-database"
   documentationCenter=""
   authors="CarlRabeler"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management"
   ms.date="08/24/2016"
   ms.author="carlrab"/>
   
# Azure SQL Database チュートリアルの確認

以下のリンクで、記載されている各機能領域の概要とその領域のクイック スタート チュートリアルを提供します。実世界のシナリオに基づく、完全なソリューションで SQL Database の利用方法を実演するソリューション中心のクイック スタートについては、「[Azure SQL Database Solution Quick Starts](sql-database-solution-quick-starts.md)」 (Azure SQL Database ソリューション クイック スタート) を参照してください。

## SQL Server Management Studio を使用する

以下のチュートリアルでは、SQL Server Management Studio を使用して Azure SQL Database を管理し、クエリを実行する方法を学習します。


> [AZURE.IMPORTANT] 常に最新バージョンの Management Studio を使用して、Microsoft Azure と SQL Database の更新プログラムとの同期を維持することをお勧めします。[SQL Server Management Studio を更新します](https://msdn.microsoft.com/library/mt238290.aspx)。


| チュートリアル | Description |
|---|---|---|
| [サーバー レベルのプリンシパル ログインを使用して Azure SQL Database に接続する](sql-database-get-started-security.md#connect-to-azure-sql-database-using-a-server-level-principal-login)| このチュートリアルでは、サーバー レベルのプリンシパル ログインを使用して Azure SQL Database に接続する方法を学習します。|
| [Azure SQL Database にユーザーとして接続する](sql-database-get-started-security.md#connect-to-azure-sql-database-as-a-user) | このチュートリアルでは、データベース レベルのユーザー アカウントを使用して Azure SQL Database に接続する方法を学習します。|
||||

## エラスティック プール

次のチュートリアルでは、[エラスティック プール](sql-database-elastic-pool.md)を使用して、多種多様な予測できない使用パターンを持つ複数のデータベースに対するパフォーマンス目標を管理する方法を学習します。

| チュートリアル | Description |
|---|---|---|
| [エラスティック プールの作成](sql-database-elastic-pool-create-portal.md) | このチュートリアルでは、Azure SQL Database のスケーラブルなプールを作成する方法を学習します。 |
| [エラスティック データベースの監視](sql-database-elastic-pool-manage-portal.md#elastic-database-monitoring) | このチュートリアルでは、個々のエラスティック データベースを監視して潜在的なトラブルを見つける方法を学習します。 |
| [プール リソースへのアラートの追加](sql-database-elastic-pool-manage-portal.md#add-an-alert-to-a-pool-resource) | このチュートリアルでは、リソースにルールを追加して、設定した使用率のしきい値にリソースが達したときに、ユーザーへの電子メールの送信や URL エンドポイントへのアラート文字列の送信を実行する方法を学習します。 |
| [エラスティック プールへのデータベースの移動](sql-database-elastic-pool-manage-portal.md#move-a-database-into-an-elastic-pool) | このチュートリアルでは、エラスティック プールにデータベースを移動する方法を学習します。 |
| [エラスティック プールからのデータベースの移動](sql-database-elastic-pool-manage-portal.md#move-a-database-out-of-an-elastic-pool) | このチュートリアルでは、エラスティック プールからデータベースを移動する方法を学習します。 |
| [プールのパフォーマンス設定を変更する](sql-database-elastic-pool-manage-portal.md#change-performance-settings-of-a-pool) | このチュートリアルでは、プールのパフォーマンスとストレージの制限を調整する方法を学習します。 |
||||

## エラスティック データベース ジョブ

以下のチュートリアルでは、[エラスティック データベース ジョブ](sql-database-elastic-jobs-overview.md)の使用について学習します。

| チュートリアル | Description |
|---|---|---|
| [Elastic Database ツールの概要](sql-database-elastic-scale-get-started.md) | このチュートリアルでは、単純なシャーディング アプリケーションを使用して、エラスティック データベース ツールの機能を使用する方法を学習します。 |
| [Azure SQL Database エラスティック ジョブの概要](sql-database-elastic-jobs-getting-started.md) | このチュートリアルでは、関連データベースのグループを管理するジョブを作成し、管理する方法を学習します。 | 
||||

## エラスティック クエリ

以下のチュートリアルでは、[エラスティック クエリ](sql-database-elastic-query-overview.md)の実行について学習します。

| チュートリアル | Description |
|---|---|---|
| [水平方向にパーティション分割 (シャーディング) されたデータベースにまたがるクエリ](sql-database-elastic-query-getting-started.md) | このチュートリアルでは、[エラスティック クエリ](sql-database-elastic-query-overview.md)を使用して、水平方向にパーティション分割 (シャード) されたすべてのデータベースからレポートを作成する方法を学習します |
| [垂直方向にパーティション分割されたデータベースにまたがるクエリ](sql-database-elastic-query-getting-started-vertical.md#create-database-objects) | このチュートリアルでは、[エラスティック クエリ](sql-database-elastic-query-overview.md)を使用して、垂直方向にパーティション分割されたすべてのデータベースからレポートを作成する方法を学習します |
| [既存のデータベースを移行してスケールアウト](sql-database-elastic-convert-to-use-elastic-tools.md)| このチュートリアルでは、Azure SQL Database を水平方向にスケーリング (シャード) する方法を学習します。 |
||||

## パフォーマンスの最適化

以下のチュートリアルでは、[単一のデータベースのパフォーマンス](sql-database-performance-guidance.md)の最適化について学習します。複数のデータベースのパフォーマンスの最適化については、「[エラスティック プール](#elastic-pools)」を参照してください。

| チュートリアル | Description |
|---|---|---|
| [データベースのサービス階層とパフォーマンス レベルを変更する](sql-database-scale-up.md#change-the-service-tier-and-performance-level-of-your-database) | このチュートリアルでは、サービス階層を使用して Azure SQL Database のパフォーマンスをスケールアップまたはスケールダウンする方法を学習します。 |
| [SQL Database Advisor Query Performance Insight](sql-database-performance.md#performance-overview) | このチュートリアルでは、SQL Database Advisor Query Performance Insight を開き、使用する方法を学習します。|
| [SQL Database Advisor のパフォーマンスに関する推奨事項](sql-database-advisor.md#viewing-recommendations) | このチュートリアルでは、SQL Database Advisor のパフォーマンスに関する推奨事項を表示し、適用する方法を学習します。 |
| [CPU 消費量上位クエリを確認する](sql-database-query-performance.md#review-top-cpu-consuming-queries)| このチュートリアルでは、SQL Database Advisor Query Performance Insight を使用して、CPU 消費量上位クエリを確認する方法を学習します。|
| [個々のクエリの詳細表示](sql-database-query-performance.md#viewing-individual-query-details)| このチュートリアルでは、SQL Database Advisor Query Performance Insight を使用して、個々のクエリのパフォーマンスの詳細を表示する方法を学習します。|
||||

## SQL Database の移行とアーカイブ 

以下のチュートリアルでは、[Azure SQL Database への既存の SQL Server データベースの移行](sql-database-cloud-migrate.md)について学習します。

| チュートリアル | Description |
|---|---|---|
| [Visual Studio 用の SQL Server Data Tools を使用して互換性に関する問題を検出する](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md#detecting-compatibility-issues-using-sql-server-data-tools-for-visual-studio) | このチュートリアルでは、Visual Studio 用の SQL Server Data Tools を使用して、Azure SQL Database の互換性を判定する方法を学習します。 |
| [Visual Studio 用の SQL Server Data Tools を使用して互換性に関する問題を修正する](sql-database-cloud-migrate-fix-compatibility-issues-ssdt#fixing-compatibility-issues-using-sql-server-data-tools-for-visual-studio) | このチュートリアルでは、Visual Studio 用の SQL Server Data Tools を使用して、Azure SQL Database の互換性の問題を修正する方法を学習します。 |
| [SqlPackage.exe を使用して SQL Database の互換性を判定する](ql-database-cloud-migrate-determine-compatibility-sqlpackage.md#using-sqlpackageexe) | このチュートリアルでは、SqlPackage.exe コマンドライン ユーティリティを使用して、Azure SQL Database の互換性を判定する方法を学習します。|
| [SSMS を使用して SQL Database の互換性を判定する](sql-database-cloud-migrate-determine-compatibility-ssms.md#using-sql-server-management-studio) |このチュートリアルでは、SQL Server Management Studio を使用して、Azure SQL Database の互換性を判定する方法を学習します。|
| [[データベースの Microsoft Azure Database へのデプロイ] ウィザードを使用して SQL Server データベースを SQL Database に移行する](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md#use-the-deploy-database-to-microsoft-azure-database-wizard) | このチュートリアルでは、SQL Server Management Studio のデータベースの Microsoft Azure Database へのデプロイ ウィザードを使用して、互換性のある SQL Server データベースを Azure SQL Database に移行する方法を学習します。
| [SSMS を使用して SQL Server データベースを BACPAC ファイルにエクスポートする](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md) | このチュートリアルでは、SQL Server Management Studio のデータ層アプリケーションのエクスポート ウィザードを使用して、互換性のある SQL Server データベースを BACPAC ファイルにエクスポートする方法を学習します。|
| [SqlPackage を使用して SQL Server データベースを BACPAC ファイルにエクスポートする](sql-database-cloud-migrate-compatible-export-bacpac-sqlpackage.md) | このチュートリアルでは、SqlPackage.exe コマンドライン ユーティリティを使用して、互換性のある SQL Server データベースを BACPAC ファイルにエクスポートする方法を学習します。|
| [SSMS を使用して BACPAC ファイルを Azure SQL Database にインポートする](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md) | このチュートリアルでは、SQL Server Management Studio のデータ層アプリケーションのエクスポート ウィザードを使用して、データベースを BACPAC ファイルから Azure SQL Database にインポートする方法を学習します。 |
| [SqlPackage を使用して BACPAC ファイルを Azure SQL Database にインポートする](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md#import-from-a-bacpac-file-into-azure-sql-database-using-sqlpackage) | このチュートリアルでは、SqlPackage コマンドライン ユーティリティを使用して、BACPAC ファイルから Azure SQL Database にデータベースをインポートする方法を学習します。 |
| [Azure ポータルを使用して BACPAC ファイルを Azure SQL Database にインポートする](sql-database-import.md) | このチュートリアルでは、Azure ポータルを使用して、Azure BLOB に格納されている BACPAC ファイルから Azure SQL Database にデータベースをインポートする方法を学習します。|
| [PowerShell を使用して BACPAC ファイルを Azure SQL Database にインポートする](sql-database-import-powershell.md) | このチュートリアルでは、PowerShell を使用して、BACPAC ファイルから Azure SQL Database にデータベースをインポートする方法を学習します。|
| [Azure ポータルを使用して Azure SQL Database をアーカイブする](sql-database-export.md#export-your-database) | このチュートリアルでは、Azure ポータルを使用して、Azure SQL Database を BACPAC ファイルにアーカイブする方法を学習します。 |
| [PowerShell を使用して Azure SQL Database をアーカイブする](sql-database-export-powershell.md) | このチュートリアルでは、PowerShell を使用し、Azure SQL Database を BACPAC ファイルにアーカイブする方法を学習します。 |
| [Azure ポータルを使用した Azure SQL Database のコピー](sql-database-copy.md#copy-your-sql-database) | このチュートリアルでは、Azure ポータルを使用して Azure SQL Database をコピーする方法を学習します。 |
| [PowerShell を使用した Azure SQL Database のコピー](sql-database-copy-powershell#copy-your-sql-database) | このチュートリアルでは、PowerShell を使用して Azure SQL Database をコピーする方法を学習します。 |
| [Transact-SQL を使用した Azure SQL Database のコピー](sql-database-copy-transact-sql.md#copy-your-sql-database) | このチュートリアルでは、Transact-SQL を使用して Azure SQL Database をコピーする方法を学習します。 |
||||

##開発

以下のチュートリアルでは、[SQL Database の開発](sql-database-develop-overview.md)と[接続ライブラリ](sql-database-libraries.md)の使用について学習します。

| チュートリアル | Description |
|---|---|---|
| [.NET (C#) を使用して SQL Database に接続する](sql-database-develop-dotnet-simple.md) | このチュートリアルでは、C# を使用して Azure SQL Database に接続する方法を学習します。 |
| [Java を使用して SQL Database に接続する](sql-database-develop-java-simple.md) | このチュートリアルでは、Java を使用して Azure SQL Database に接続する方法について説明します。 |
| [Node.js を使用して SQL Database に接続する](sql-database-develop-nodejs-simple.md) | このチュートリアルでは、Node.js を使用して Azure SQL Database に接続する方法を学習します。 |
| [PHP を使用して SQL Database に接続する](sql-database-develop-php-simple.md) | このチュートリアルでは、PHP を使用して Azure SQL Database に接続する方法を学習します。 |
| [Python を使用して SQL Database に接続する](sql-database-develop-python-simple.md) | このチュートリアルでは、Python を使用して Azure SQL Database に接続する方法を学習します。 |
| [Ruby を使用して SQL Database に接続する](sql-database-develop-ruby-simple.md) | このチュートリアルでは、Ruby を使用して Azure SQL Database に接続する方法を学習します。 |
||||
 
## データベース アクセス

以下のチュートリアルでは、[ログインおよびユーザーの作成と管理](sql-database-manage-logins.md)について学習します。

| チュートリアル | Description |
|---|---|---|
| [Azure ポータルを使用して Azure SQL Database のサーバー レベルのファイアウォール規則を作成する](sql-database-configure-firewall-settings.md) | このチュートリアルでは、Azure ポータルを使用して SQL Database のサーバー レベルのファイアウォールを構成する方法を学習します。 |
| [Transact-SQL を使用してデータベース レベルのファイアウォール規則を作成する](sql-database-configure-firewall-settings-tsql.md#database-level-firewall-rules) | このチュートリアルでは、Transact-SQL を使用してデータベース レベルのファイアウォール規則を作成する方法を学習します。|
| [Transact-SQL を使用してサーバー レベルのファイアウォール規則を管理する](sql-database-configure-firewall-settings-tsql.md#manage-server-level-firewall-rules-through-transact-sql) | このチュートリアルでは、Transact-SQL を使用して SQL Database のサーバー レベルのファイアウォールを管理する方法を学習します。|
| [PowerShell を使用してサーバー レベルのファイアウォール規則を管理する](sql-database-configure-firewall-settings-powershell.md#manage-firewall-rules-using-powershell) | このチュートリアルでは、PowerShell を使用して Azure SQL Database のサーバー レベルのファイアウォールを管理する方法を学習します。|
| [REST API を使用してサーバー レベルのファイアウォール規則を管理する](sql-database-configure-firewall-settings-rest.md#manage-firewall-rules-using-the-service-management-rest-api) | このチュートリアルでは、REST API を使用して Azure SQL Database のサーバー レベルのファイアウォールを管理する方法を学習します。|
| [サーバー レベルのプリンシパル ログインを使用して Azure SQL Database に接続する](sql-database-get-started-security.md#connect-to-azure-sql-database-using-a-server-level-principal-login)| このチュートリアルでは、サーバー レベルのプリンシパル ログインを使用して Azure SQL Database に接続する方法を学習します。|
| [ログインに対するデータベース アクセスの付与](sql-database-manage-logins.md#granting-database-access-to-a-login() | このチュートリアルでは、サーバー レベルのログインに対してデータベース アクセスを付与する方法を学習します。|
| [SSMS を使用して新しいデータベース ユーザーを作成する](sql-database-get-started-security.md#create-new-database-user-using-ssms) | このチュートリアルでは、SSMS を使用して既存のデータベースに新しいデータベース ユーザーを作成する方法を学習します。|
| [新しいデータベース ユーザーに db\_owner 権限を付与する](sql-database-get-started-security.md#grant-new-database-user-dbowner-permissions) | このチュートリアルでは、既存のデータベース ユーザーに db\_owner 権限を付与する方法を学習します。|
| [Azure SQL Database にユーザーとして接続する](sql-database-get-started-security.md#connect-to-azure-sql-database-as-a-user) | このチュートリアルでは、データベース レベルのユーザー アカウントを使用して Azure SQL Database に接続する方法を学習します。|
||||


## データのセキュリティ

以下のチュートリアルでは、[Azure SQL Database のデータのセキュリティ保護](sql-database-security.md)について学習します。

| チュートリアル | Description |
|---|---|---|
| [Azure ポータルを使用してデータベースの脅威検出を有効にする](sql-database-threat-detection-get-started.md#set-up-threat-detection-for-your-database) | このチュートリアルでは、Azure ポータルでデータベースの脅威検出を設定する方法を学習します。|
| [Always Encrypted を使用して機密データを保護する](sql-database-always-encrypted-azure-key-vault.md) | このチュートリアルでは、Always Encrypted ウィザードを使用して Azure SQL Database の機密データをセキュリティで保護する方法を学習します。|
| [Transparent Data Encryption を使用して機密データを保護する](https://msdn.microsoft.com/library/dn948096.aspx)| このチュートリアルでは、Transparent Data Encryption を使用して、機密データをセキュリティで保護する方法を学習します。|
| [データの列の暗号化](https://msdn.microsoft.com/library/ms179331.aspx)| このチュートリアルでは、Transact-SQL を使用してデータの列を暗号化する方法を学習します。|
| [動的データ マスクを設定する](sql-database-dynamic-data-masking-get-started.md#set-up-dynamic-data-masking-for-your-database-using-the-azure-portal) | このチュートリアルでは、Azure SQL Database に動的データ マスクを設定する方法を学習します。 |
||||

## ビジネス継続性とクエリのスケール アウト

以下のチュートリアルでは、ビジネス継続性とクエリのスケール アウトのために、[geo リストアとアクティブ geo レプリケーション](sql-database-business-continuity.md)を使用してエラーから回復する方法を学習します。

| チュートリアル | Description |
|---|---|---|
| [Azure ポータルで以前の時点に Azure SQL Database を復元する](sql-database-point-in-time-restore-portal.md)| このチュートリアルでは、Azure ポータルを使用して以前の時点にデータベースを復元する方法を学習します。|
| [PowerShell で以前の時点に Azure SQL Database を復元する](sql-database-point-in-time-restore-powershell.md) | このチュートリアルでは、PowerShell を使用して以前の時点にデータベースを復元する方法を学習します。|
| [Azure ポータルを使用した、削除済み Azure SQL データベースの復元](sql-database-restore-deleted-database-portal.md) | このチュートリアルでは、Azure ポータルを使用して、削除済みのデータベースを復元する方法を学習します。|
| [PowerShell を使用して削除済み Azure SQL Database を復元する](sql-database-restore-deleted-database-powershell.md) | このチュートリアルでは、PowerShell を使用して、削除済みのデータベースを復元する方法を学習します。|
| [Azure ポータルを使用して Azure SQL Database の geo レプリケーションを構成する](sql-database-geo-replication-portal.md)| このチュートリアルでは、Azure ポータルを使用してアクティブ geo レプリケーションを構成する方法を学習します。|
| [PowerShell を使用して Azure SQL Database の geo レプリケーションを構成する](sql-database-geo-replication-powershell.md)| このチュートリアルでは、PowerShell を使用してアクティブ geo レプリケーションを構成する方法を学習します。|
| [Transact-SQL を使用した Azure SQL Database の geo レプリケーションの構成](sql-database-geo-replication-transact-sql.md)| このチュートリアルでは、Transact-SQL を使用してアクティブ geo レプリケーションを構成する方法を学習します。|
| [Azure ポータルを使用した Azure SQL Database の計画されたフェールオーバーまたは計画されていないフェールオーバーの開始](sql-database-geo-replication-failover-portal.md) | このチュートリアルでは、Azure ポータルを使用して、geo レプリケーションされたセカンダリ レプリカにフェールオーバーする方法を学習します。|
| [PowerShell を使用した Azure SQL Database の計画されたフェールオーバーまたは計画されていないフェールオーバーの開始](sql-database-geo-replication-failover-powershell.md) | このチュートリアルでは、PowerShell を使用して、geo レプリケーションされたセカンダリ レプリカにフェールオーバーする方法を学習します。|
| [Transact-SQL を使用した Azure SQL Database の計画されたフェールオーバーまたは計画されていないフェールオーバーの開始](sql-database-geo-replication-failover-transact-sql.md) | このチュートリアルでは、Transact-SQL を使用して、geo レプリケーションされたセカンダリ レプリカにフェールオーバーする方法を学習します。|
||||

## データ同期

このチュートリアルでは、[データ同期](http://download.microsoft.com/download/4/E/3/4E394315-A4CB-4C59-9696-B25215A19CEF/SQL_Data_Sync_Preview.pdf)について学習します。

| チュートリアル | Description |
|---|---|---| 
| [Azure SQL データ同期の概要 (プレビュー)](sql-database-get-started-sql-data-sync.md) | このチュートリアルでは、Azure クラシック ポータルを使用して、Azure SQL データ同期の基礎を学習します。 |
||||

## 次のステップ

[Azure SQL Database ソリューション クイック スタート ガイド](sql-database-solution-quick-starts.md)

<!---HONumber=AcomDC_0824_2016-->