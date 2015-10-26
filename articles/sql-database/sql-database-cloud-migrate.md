<properties
   pageTitle="SQL Server データベースの Azure SQL Database への移行"
   description="Microsoft Azure SQL Database、データベースのデプロイ、データベースの移行、データベースのインポート、データベースのエクスポート、移行ウィザード"
   services="sql-database"
   documentationCenter=""
   authors="carlrabeler"
   manager="jeffreyg"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management"
   ms.date="10/12/2015"
   ms.author="carlrab"/>

# SQL Server データベースの Azure SQL Database への移行

Azure SQL Database V12 は、SQL Server 2014 および SQL Server 2016 とのほぼ完全なエンジン互換性を提供します。互換性のあるデータベースの場合、Azure SQL Database データベースの移行は、スキーマとデータの簡単な移動操作であり、スキーマの変更がほとんど不要で、アプリケーションの再構築もほとんど、もしくはまったく必要としません。データベースの変更が必要な場合も、Azure SQL Database V11 の場合より変更の範囲は限定的です。

仕様では、Azure SQL Database V12 は、SQL Server のサーバー スコープの機能をサポートしていません。これらの機能に依存するデータベースとアプリケーションは、移行前に再構築する必要があります。

>[AZURE.NOTE]Microsoft Access、Sybase、MySQL Oracle、DB2 などの他の種類のデータベースを Azure SQL Database に移行する場合は、「[SQL Server Migration Assistant](http://blogs.msdn.com/b/ssma/)」を参照してください。

SQL Server データベースを Azure SQL Database に移行するワークフローは次のとおりです。

 1. [データベースに互換性があるかどうかを調べます](#determine-if-your-database-is-compatible)
 2. [互換性がない場合は、データベースの互換性の問題を修正します](#fix-database-compatibility-issues)
 3. [互換性のあるデータベースを移行します](#options-to-migrate-a-compatible-database-to-azure-sql-database)

## データベースに互換性があるかどうかを調べる
移行元データベースに互換性があるかどうかを調べるには、主に 2 つの方法があります。- データ層アプリケーションのエクスポート: この方法は Management Studio のウィザードを使用し、コンソールにエラー メッセージが表示されます。- [SqlPackage.exe](https://msdn.microsoft.com/library/hh550080.aspx): Visual Studio および SQL Server に付属するコマンド ライン ユーティリティです。この方法では、レポートが生成されます。

> [AZURE.NOTE]3 番目の方法として、トレース ファイルを使用して互換性をテストすることもできます。これは、Codeplex の無償のツール [SQL Azure 移行ウィザード](http://sqlazuremw.codeplex.com/)です。ただし、このツールは現在、 Azure SQL Database V12 では問題にならないが Azure SQL Database V11 では問題であった互換性エラーを検出できます。

データベースの非互換性が検出された場合は、Azure SQL Database にデータベースを移行する前に、これらの非互換性を修正する必要があります。データベース互換性問題の修正方法については、「[データベースの互換性の問題を修正する](#fix-database-compatibility-issues)」を参照してください。

> [AZURE.IMPORTANT]これらのオプションでは、SQL Server データベースの異なるレベル (つまり、レベル 90、100、110) の間における互換性の問題をすべて検出することはできません。古いデータベース (レベル 80、90、100、110) から移行する場合は、最初に (少なくとも開発環境で) アップグレードを行い、SQL Server 2014 以降にした後で、Azure SQL Database に移行する必要があります。

## sqlpackage.exe を使用してデータベースの互換性を調べる

1. コマンド プロンプトを開き、sqlpackage.exe の最新バージョンを含むディレクトリに変更します。このユーティリティは、Visual Studio と SQL Server の両方に含まれます。
2. 次のコマンドを実行します。引数 < server_name >、< database_name >、< target_file >、< schema_name.table_name >、< output_file > は適切な値に置き換えます。/p:TableName 引数を指定するのは、すべてのテーブルからデータをエクスポートするのではなく、Azure SQL DB V12 にエクスポートするためのデータベースの互換性をテストするだけであるためです。残念ながら、sqlpackage.exe の export 引数ではテーブルを抽出しないように指定することはできないので、小さいテーブルを 1 つ指定する必要があります。< output_file > には、すべてのエラーのレポートが含まれます。

	'sqlpackage.exe /Action:Export /ssn:< server_name > /sdn:< database_name > /tf:< target_file > /p:TableData=< schema_name.table_name > > < output_file > 2>&1'

	![[タスク] メニューの [データ層アプリケーションのエクスポート]](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSQLPackage01.png)

3. 出力ファイルを開き、互換性エラーがある場合は確認します。データベース互換性問題の修正方法については、「[データベースの互換性の問題を修正する](#fix-database-compatibility-issues)」を参照してください。

	![[タスク] メニューの [データ層アプリケーションのエクスポート]](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSQLPackage02.png)

## データ層アプリケーションのエクスポートを使用してデータベースの互換性を調べる

1. SQL Server Management Studio がバージョン 13.0.600.65 以降であることを確認します。Management Studio は毎月新しいバージョンに更新されて、Azure ポータルの更新との同期が維持されます。

 	 >[AZURE.IMPORTANT][最新](https://msdn.microsoft.com/library/mt238290.aspx)バージョンの SQL Server Management Studio をダウンロードします。最新バージョンの Management Studio を使用することを常にお勧めします。

2. Management Studio を開き、オブジェクト エクスプローラーで移行元データベースに接続します。
3. オブジェクト エクスプローラーで移行元データベースを右クリックし、**[タスク]** をポイントして **[データ層アプリケーションのエクスポート...]** をクリックします。

	![[タスク] メニューの [データ層アプリケーションのエクスポート]](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS01.png)

4. エクスポート ウィザードの **[設定]** タブで、BACPAC ファイルがローカル ディスクまたは Azure BLOB に保存されるようにエクスポートを構成します。BACPAC ファイルは、データベースの互換性に問題がない場合にのみ保存されます。互換性の問題がある場合は、コンソールに表示されます。

	![設定のエクスポート](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS02.png)

5. **[詳細設定]** タブをクリックし、**[すべて選択]** チェック ボックスをオフにしてデータのエクスポートをスキップします。ここでの目的は、互換性をテストすることだけです。

	![設定のエクスポート](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS03.png)

6. **[次へ]** をクリックし、**[完了]** をクリックします。データベースの互換性に問題がある場合は、ウィザードによるスキーマの検証後に表示されます。

	![設定のエクスポート](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS04.png)

7. エラーが表示されない場合は、データベースは互換性があり、移行できる状態です。エラーがある場合は、修正する必要があります。エラーを表示するには、**[スキーマの検証]** の **[エラー]** をクリックします。エラーを修正する方法については、「[データベースの互換性の問題を修正する](#fix-database-compatibility-issues)」を参照してください。

	![設定のエクスポート](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS05.png)

## 互換性のあるデータベースを Azure SQL Database に移行するためのオプション

- 小規模から中規模のデータベースの場合、[互換性のある](#determine-if-your-database-is-compatible) SQL Server 2005 以降のデータベースの移行は、SQL Server Management Studio の [[データベースの Microsoft Azure Database へのデプロイ]](#use-the-deploy-database-to-microsoft-azure-database-wizard) ウィザードを実行するのと同じくらい簡単です。接続に問題 (接続なし、低帯域幅、タイムアウト) がある場合は、Azure SQL Database への SQL Server データベースの[移行を BACPAC を使用して行う](#use-a-bacpac-to-migrate-a-database-to-azure-sql-database)ことができます。
- 中規模から大規模のデータベースの場合、または接続に問題がある場合は、[BACPAC を使用](#use-a-bacpac-to-migrate-a-database-to-azure-sql-database)して SQL Server データベースを Azure SQL Database に移行します。この方法では、SQL Server Management Studio を使用して、データとスキーマを [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) ファイル (ローカルまたは Azure BLOB に保存される) にエクスポートしてから、Azure SQL インスタンスに BACPAC ファイルをインポートします。Azure BLOB に BACPAC を保存する場合は、[Azure ポータル](sql-database-import.md)から、または [PowerShell を使用](sql-database-import-powershell.md)して、BACPAC ファイルをインポートすることもできます。
- 大規模なデータベースの場合は、スキーマとデータを個別に移行することでパフォーマンスを最適化できます。この方法では、SQL Server Management Studio を使用してスキーマのスクリプトを作成するか、または Visual Studio でデータベース プロジェクトを作成した後、Azure SQL Database にスキーマをデプロイします。Azure SQL Database にスキーマをインポートした後、[BCP](https://msdn.microsoft.com/library/ms162802.aspx) を使用してフラット ファイルにデータを抽出し、Azure SQL Database にこれらのファイルをインポートします。

 ![SSMS の移行ダイアグラム](./media/sql-database-migrate-ssms/01SSMSDiagram.png)

## Microsoft Azure SQL Database へのデータベースのデプロイ ウィザードを使用する

SQL Server Management Studio の Microsoft Azure SQL Database へのデータベースのデプロイ ウィザードは、[互換性のある](#determine-if-your-database-is-compatible) SQL Server 2005 以降のデータベースを Azure SQL 論理サーバー インスタンスに直接移行します。

> [AZURE.NOTE]以下の手順では、Azure SQL 論理インスタンスを既にプロビジョニングしてあり、接続情報がわかっているものとします。

1. SQL Server Management Studio がバージョン 13.0.600.65 以降であることを確認します。Management Studio は毎月新しいバージョンに更新されて、Azure ポータルの更新との同期が維持されます。

	 >[AZURE.IMPORTANT][最新](https://msdn.microsoft.com/library/mt238290.aspx)バージョンの SQL Server Management Studio をダウンロードします。最新バージョンの Management Studio を使用することを常にお勧めします。

2. Management Studio を開き、オブジェクト エクスプローラーで移行元データベースに接続します。
3. オブジェクト エクスプローラーで移行元データベースを右クリックし、**[タスク]** をポイントして **[Microsoft Azure SQL Database へのデータベースのデプロイ...]** をクリックします。

	![[タスク] メニューの "Azure へのデプロイ"](./media/sql-database-cloud-migrate/MigrateUsingDeploymentWizard01.png)

4.	デプロイメント ウィザードで、Azure SQL Database サーバーへの接続を構成します。
5.	Azure SQL DB でのデータベースの **[新しいデータベース名]** を指定し、**[Microsoft Azure SQL Database のエディション]** (サービス階層)、**[データベースの最大サイズ]**、**[サービスの目的]** (パフォーマンス レベル)、および移行プロセスの間にこのウィザードが作成する BACPAC ファイルの **[一時ファイル名]** を設定します。サービス階層とパフォーマンス レベルの詳細については、「[SQL Database のサービス階層](sql-database-service-tiers.md)」を参照してください。

	![設定のエクスポート](./media/sql-database-cloud-migrate/MigrateUsingDeploymentWizard02.png)

6.	データベースを移行するウィザードを完了します。データベースのサイズと複雑さに応じて、デプロイメントには数分から数時間かかる場合があります。
7.	オブジェクト エクスプローラーを使用して、Azure SQL Database サーバーの移行されたデータベースに接続します。
8.	Azure ポータルを使用して、データベースとそのプロパティを表示します。

## BACPAC を使用して SQL Server データベースを Azure SQL Database に移行する

中規模から大規模のデータベースの場合、または接続に問題がある場合は、移行プロセスを 2 つの個別の手順に分けることができます。1 つまたは 2 つの方法を使用して、スキーマとそのデータを [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) ファイルにエクスポートします。

- [SQL Server Management Studio を使用して BACPAC ファイルにエクスポートする](#export-a-compatible-sql-server-database-to-a-bacpac-file-using-sql-server-management-studio)
- [SqlPackage を使用して BACPAC にエクスポートする](#export-a-compatible-sql-server-database-to-a-bacpac-file-using-sqlpackage)

この BACPAC は、ローカルまたは Azure BLOB に保存できます。その後、次のいずれかの方法を使用してこの BACPAC ファイルを Azure SQL Database にインポートします。

- [SQL Server Management Studio を使用して BACPAC ファイルを Azure SQL Database にインポートする](#import-from-a-bacpac-file-into-azure-sql-database-using-sql-server-management-studio)
- [SqlPackage を使用して BACPAC ファイルを Azure SQL Database にインポートする](#import-from-a-bacpac-file-into-azure-sql-database-using-sqlpackage)
- [Azure ポータルを使用して BACPAC ファイルを Azure SQL Database にインポートする](sql-database-import.md)
- [PowerShell を使用して BACPAC ファイルを Azure SQL Database にインポートする](sql-database-import-powershell.md)

## SQL Server Management Studio を使用して互換性のある SQL Server データベースを BACPAC ファイルにエクスポートする

以下の手順では、Management Studio を使用して[互換性のある](#determine-if-your-database-is-compatible) SQL Server データベースを BACPAC ファイルにエクスポートします。

1. SQL Server Management Studio がバージョン 13.0.600.65 以降であることを確認します。Management Studio は毎月新しいバージョンに更新されて、Azure ポータルの更新との同期が維持されます。

	 >[AZURE.IMPORTANT][最新](https://msdn.microsoft.com/library/mt238290.aspx)バージョンの SQL Server Management Studio をダウンロードします。最新バージョンの Management Studio を使用することを常にお勧めします。

2. Management Studio を開き、オブジェクト エクスプローラーで移行元データベースに接続します。

	![[タスク] メニューの [データ層アプリケーションのエクスポート]](./media/sql-database-cloud-migrate/MigrateUsingBACPAC01.png)

3. オブジェクト エクスプローラーで移行元データベースを右クリックし、**[タスク]** をポイントして **[データ層アプリケーションのエクスポート...]** をクリックします。

	![[タスク] メニューの [データ層アプリケーションのエクスポート]](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS01.png)

4. エクスポート ウィザードで、BACPAC ファイルがローカル ディスクまたは Azure BLOB に保存されるようにエクスポートを構成します。エクスポートされた BACPAC には、常にデータベース スキーマ全体が含まれ、既定ではすべてのテーブルのデータが含まれます。一部またはすべてのテーブルからデータを除外する場合は、[詳細設定] タブを使用してください。たとえば、すべてのテーブルではなく参照テーブルのデータのみをエクスポートするように選択できます。

	![設定のエクスポート](./media/sql-database-cloud-migrate/MigrateUsingBACPAC02.png)

## SqlPackage を使用して互換性のある SQL Server データベースを BACPAC ファイルにエクスポートする

以下の手順では、[SqlPackage.exe](https://msdn.microsoft.com/library/hh550080.aspx) コマンド ライン ユーティリティを使用して[互換性のある](#determine-if-your-database-is-compatible)データベースを BACPAC ファイルにエクスポートします。

> [AZURE.NOTE]Azure SQL Database サーバーを既にプロビジョニングしてあり、接続情報がわかっていて、移行元データベースに互換性があることを検証済みであるものとします。

1. コマンド プロンプトを開き、sqlpackage.exe コマンド ライン ユーティリティのあるディレクトリに移動します。このユーティリティは、Visual Studio および SQL Server に付属します。
2. 次のコマンドを実行します。引数 < server_name >、< database_name >、< target_file > は適切な値に置き換えます。

	'sqlpackage.exe /Action:Export /ssn:< server_name > /sdn:< database_name > /tf:< target_file >

	![[タスク] メニューの [データ層アプリケーションのエクスポート]](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSQLPackage01b.png)

## SQL Server Management Studio を使用して BACPAC ファイルを Azure SQL Database にインポートする

以下の手順を使用して、BACPAC ファイルを Azure SQL Database にインポートします。

> [AZURE.NOTE]以下の手順では、Azure SQL 論理インスタンスを既にプロビジョニングしてあり、接続情報がわかっているものとします。

1. SQL Server Management Studio がバージョン 13.0.600.65 以降であることを確認します。Management Studio は毎月新しいバージョンに更新されて、Azure ポータルの更新との同期が維持されます。

	 >[AZURE.IMPORTANT][最新](https://msdn.microsoft.com/library/mt238290.aspx)バージョンの SQL Server Management Studio をダウンロードします。最新バージョンの Management Studio を使用することを常にお勧めします。

2. Management Studio を開き、オブジェクト エクスプローラーで移行元データベースに接続します。

	![[タスク] メニューの [データ層アプリケーションのエクスポート]](./media/sql-database-cloud-migrate/MigrateUsingBACPAC01.png)

    BACPAC が作成されたら、Azure SQL Database サーバーに接続し、**[データベース]** フォルダーを右クリックして、**[データ層アプリケーションのインポート...]** をクリックします。

    ![[データ層アプリケーションのインポート] メニュー項目](./media/sql-database-cloud-migrate/MigrateUsingBACPAC03.png)

3.	インポート ウィザードで、エクスポートした BACPAC ファイルを選択して、Azure SQL Database に新しいデータベースを作成します。

    ![設定のインポート](./media/sql-database-cloud-migrate/MigrateUsingBACPAC04.png)

4.	Azure SQL DB でのデータベースの **[新しいデータベース名]** を指定し、**[Microsoft Azure SQL Database のエディション]** (サービス階層)、**[データベースの最大サイズ]**、および **[サービスの目的]** (パフォーマンス レベル) を設定します。

    ![データベース設定](./media/sql-database-cloud-migrate/MigrateUsingBACPAC05.png)

5.	**[次へ]**、**[完了]** の順にクリックし、Azure SQL Database サーバーの新しいデータベースに BACPAC ファイルをインポートします。

6. オブジェクト エクスプローラーを使用して、Azure SQL Database サーバーの移行されたデータベースに接続します。

7.	Azure ポータルを使用して、データベースとそのプロパティを表示します。

## SqlPackage を使用して BACPAC ファイルを Azure SQL Database にインポートする

以下の手順では、[SqlPackage.exe](https://msdn.microsoft.com/library/hh550080.aspx) コマンド ライン ユーティリティを使用して、互換性のある SQL Server データベース (または Azure SQL Database) を BACPAC ファイルからインポートします。

> [AZURE.NOTE]Azure SQL Database サーバーを既にプロビジョニングしてあり、接続情報がわかっているものとします。

1. コマンド プロンプトを開き、sqlpackage.exe コマンド ライン ユーティリティのあるディレクトリに移動します。このユーティリティは、Visual Studio および SQL Server に付属します。
2. 次のコマンドを実行します。引数 < server_name >、< database_name >、< user_name >、< password >、< source_file > は適切な値に置き換えます。

	'sqlpackage.exe /Action:Import /tsn:< server_name > /tdn:< database_name > /tu:< user_name > /tp:< password > /sf:< target_file >

	![[タスク] メニューの [データ層アプリケーションのエクスポート]](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSQLPackage01c.png)


## データベースの互換性の問題を修正する

移行元の SQL Server データベースに互換性がないことがわかった場合は、いくつかのオプションを使用して、[明らかになった](#determine-if-your-database-is-compatible)データベース互換性の問題を修正します。

- [SQL Azure 移行ウィザード](http://sqlazuremw.codeplex.com/)を使用します。この Codeplex ツールを使うと、互換性のない移行元データベースから T-SQL スクリプトを生成した後、SQL Database に対応するようにこのスクリプトを変換し、Azure SQL Database に接続してこのスクリプトを実行できます。このツールは、トレース ファイルを分析して互換性の問題の特定も行います。スキーマのみを含むスクリプトを生成することも、BCP 形式のデータを含むスクリプトを生成することもできます。詳細な手順などのその他のドキュメントは、Codeplex の [SQL Azure 移行ウィザード](http://sqlazuremw.codeplex.com/)に関するページで入手できます。  

 ![SAMW の移行ダイアグラム](./media/sql-database-cloud-migrate/02SAMWDiagram.png)

 >[AZURE.NOTE]ウィザードで検出できるすべての非互換スキーマを組み込み変換で処理できるとは限りません。処理できない非互換スクリプトは、生成されたスクリプトにコメントが挿入され、エラーとして報告されます。多くのエラーが検出された場合は、Visual Studio または SQL Server Management Studio を使用して、SQL Server 移行ウィザードでは修正できない各エラーを修正します。

- Visual Studio を使用します。Visual Studio を使用すると、データベース スキーマを Visual Studio データベース プロジェクトにインポートして分析できます。分析するには、SQL Database V12 にプロジェクトのターゲット プラットフォームを指定し、プロジェクトを構築します。構築に成功した場合は、データベースには互換性があります。構築に失敗した場合は、Visual Studio 用の SQL Server Data Tools ("SSDT") でエラーを解決できます。プロジェクトが正常に構築されたら、これをソース データベースのコピーとして発行し、SSDT のデータ比較機能を使用して、ソース データベースから Azure SQL V12 互換のデータベースにデータをコピーできます。この更新されたデータベースを、[前に説明した](#options-to-migrate-a-compatible-database-to-azure-sql-database)オプションを使用して Azure SQL Database にデプロイします。

 ![VSSSDT の移行ダイアグラム](./media/sql-database-cloud-migrate/03VSSSDTDiagram.png)

 >[AZURE.NOTE]スキーマのみの移行が必要な場合は、Visual Studio から Azure SQL Database に直接、発行することができます。この方法は移行ウィザードのみで処理できる変更よりも多数の変更を、データベース スキーマで行う必要がある場合に使用します。

- SQL Server Management Studio。Management Studio では、**ALTER DATABASE** などのさまざまな Transact-SQL コマンドを使用して問題を修正できます。

<!---HONumber=Oct15_HO3-->