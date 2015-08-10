<properties 
	pageTitle="Azure で SQL Server にデータを移動する| Microsoft Azure" 
	description="Azure で SQL Server にデータを移動する" 
	services="machine-learning" 
	documentationCenter="" 
	authors="msolhab" 
	manager="paulettm" 
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/04/2015" 
	ms.author="fashah;garye;mohabib;bradsev" />

#Azure で SQL Server にデータを移動する

このドキュメントでは、フラット ファイル (csv/tsv) またはオンプレミスの SQL Server から、Azure の SQL Server へのデータの移動について説明します。このタスクは、Azure Machine Learning が提供する Advanced Analytics Process and Technology (ADAPT) の一部です。


<table>

<tr>
<td><b>ソース</b></td>
<td colspan="2" align="center"><b>移動先</b></td>
</tr>

<tr>
  <td></td>
  <td><b>Azure 上の SQL Server VM</b></td>
</tr>

<tr>
  <td><b>フラット ファイル</b></td>  
  <td>
    1.<a href="#insert-tables-bcp">コマンド ライン一括コピー ユーティリティ (BCP)</a><br>
    2.<a href="#insert-tables-bulkquery">一括挿入 SQL クエリ</a><br>
    3.<a href="#sql-builtin-utilities">SQL Server のグラフィカル組み込みユーティリティ</a>
  </td>
</tr>
<tr>
  <td><b>オンプレミスの SQL Server</b></td>
  <td>
    1.<a href="#export-flat-file">フラット ファイルへのエクスポート</a><br>
    2.<a href="#sql-migration">SQL Database 移行ウィザード</a> <br>    
    3.<a href="#sql-backup">データベースのバックアップと復元</a> <br>
  </td>
</tr>
</table>

このドキュメントでは、SQL Server Management Studio または Visual Studio のデータベース エクスプローラーから SQL コマンドが実行されることを想定していることに注意してください。

> [AZURE.TIP]別の方法として、[Azure Data Factory](https://azure.microsoft.com/ja-jp/services/data-factory/) を使用して、データを Azure の SQL Server VM に移動するパイプラインの作成とスケジュール設定を実行できます。詳細については、「[Azure Data Factory を使用してデータをコピーする (コピー アクティビティ)](../data-factory/data-factory-copy-activity.md)」を参照してください。


## <a name="sqlonazurevm"></a>Azure の SQL Server VM へのデータの移動

このセクションでは、Azure の SQL Server VM にデータを移動するプロセスについて説明します。SQL Server VM をセットアップしていない場合は、「[Azure SQL Server 仮想マシンを高度な分析用の IPython Notebook サーバーとしてセットアップする](machine-learning-data-science-setup-sql-server-virtual-machine.md)」の説明に従って、高度な分析用の新しい SQL Server 仮想マシンをプロビジョニングします。

このドキュメントでは、次のデータ ソースからデータを移動する方法について説明します。
  
1. [フラット ファイルから](#filesource_to_sqlonazurevm) 
2. [オンプレミスの SQL Server から](#sqlonprem_to_sqlonazurevm)


### <a name="filesource_to_sqlonazurevm"></a>ファイル ソース

データがフラット ファイル (行と列の形式で配置されている) に存在する場合は、次の方法を使用して Azure の SQL Server VM にデータを移動できます。

1. [コマンド ライン一括コピー ユーティリティ (BCP)](#insert-tables-bcp) 
2. [一括挿入 SQL クエリ](#insert-tables-bulkquery)
3. [SQL Server のグラフィカル組み込みユーティリティ (インポート/エクスポート、SSIS)](#sql-builtin-utilities)


### <a name="insert-tables-bcp">コマンド ライン一括コピー ユーティリティ (BCP)</a>

BCP は、SQL Server と一緒にインストールされるコマンド ライン ユーティリティであり、データを移動する最も簡単な方法の 1 つです。これは、3 つの異なる SQL Server (オンプレミスの SQL Server、SQL Azure、および Azure での SQL Server VM) すべて機能します。

> [AZURE.NOTE]**BCP 用のデータの場所** 必須ではありませんが、ターゲットの SQL Server と同じマシン上にソース データを含むファイルがある場合、高速転送 (ネットワークの速度とローカル ディスク IO の速度の差) を使用できます。さまざまなファイル コピー ツール ([AZCopy](../storage-use-azcopy.md)、[Azure ストレージ エクスプローラー](https://azurestorageexplorer.codeplex.com/)、またはリモート デスクトップ プロトコル (RDP) を介した Windows のコピーと貼り付けなど) を使用して、データを含むフラット ファイルを SQL Server がインストールされているマシンに移動できます。

1. データベースとテーブルがターゲットの SQL Server データベースで作成されていることを確認します。以下に、`Create Database` コマンドと `Create Table` コマンドを使用してこれを行う方法の例を示します。

		CREATE DATABASE <database_name>
	
		CREATE TABLE <tablename>
		(
			<columnname1> <datatype> <constraint>,
			<columnname2> <datatype> <constraint>,
			<columnname3> <datatype> <constraint>
		) 
	
2. bcp がインストールされているマシンのコマンド ラインから次のコマンドを発行し、テーブルのスキーマを記述するフォーマット ファイルを生成します。

	`bcp dbname..tablename format nul -c -x -f exportformatfilename.xml -S servername\sqlinstance -T -t \t -r \n`

3. 次のように bcp コマンドを使用して、データをデータベースに挿入します。SQL Server が同じマシン上にインストールされていると想定した場合、これはコマンド ラインから動作するはずです。

	`bcp dbname..tablename in datafilename.tsv -f exportformatfilename.xml -S servername\sqlinstancename -U username -P password -b block_size_to_move_in_single_attemp -t \t -r \n`

> **BCP 挿入の最適化** こうした挿入を最適化するには、「[一括インポートを最適化するためのガイドライン](https://technet.microsoft.com/library/ms177445%28v=sql.105%29.aspx)」の記事を参照してください。

#### <a name="insert-tables-bulkquery-parallel"></a>高速データ移動用の挿入の並列処理

移動するデータのサイズが大きい場合は、PowerShell スクリプトで複数の BCP コマンドを並行して同時に実行することによって、高速化できます。

> [AZURE.NOTE]**ビッグ データの取り込み** 大きなデータセットや非常に大きなデータセットのデータ読み込みを最適化するには、複数のファイル グループとパーティション テーブルを使用して、論理的および物理的なデータベース テーブルをパーティション分割します。データを作成してパーティション テーブルに読み込む方法についての詳細は、「[SQL パーティション テーブルの並列読み込み](machine-learning-data-science-parallel-load-sql-partitioned-tables.md)」を参照してください。


次のサンプル PowerShell スクリプトは、bcp を使用した並列挿入を示しています。
	
	$NO_OF_PARALLEL_JOBS=2

	 Set-ExecutionPolicy RemoteSigned #set execution policy for the script to execute
	 # Define what each job does
	   $ScriptBlock = {
		   param($partitionnumber)

		   #Explictly using SQL username password
		   bcp database..tablename in datafile_path.csv -F 2 -f format_file_path.xml -U username@servername -S tcp:servername -P password -b block_size_to_move_in_single_attempt -t "," -r \n -o path_to_outputfile.$partitionnumber.txt

			#Trusted connection w.o username password (if you are using windows auth and are signed in with that credentials)
			#bcp database..tablename in datafile_path.csv -o path_to_outputfile.$partitionnumber.txt -h "TABLOCK" -F 2 -f format_file_path.xml  -T -b block_size_to_move_in_single_attempt -t "," -r \n 
	  }
	

	# Background processing of all partitions
	for ($i=1; $i -le $NO_OF_PARALLEL_JOBS; $i++)
	{
	  Write-Debug "Submit loading partition # $i"
	  Start-Job $ScriptBlock -Arg $i	  
	}
	

	# Wait for it all to complete
	While (Get-Job -State "Running")
	{
	  Start-Sleep 10
	  Get-Job
	}
	
	# Getting the information back from the jobs
	Get-Job | Receive-Job
	Set-ExecutionPolicy Restricted #reset the execution policy


### <a name="insert-tables-bulkquery">一括挿入 SQL クエリ</a>

[一括挿入 SQL クエリ](https://msdn.microsoft.com/library/ms188365)は、行/列ベースのファイルからデータをデータベースにインポートする場合に使用できます (サポートされるタイプについては[ここ](https://msdn.microsoft.com/library/ms188609)で説明されています)。

一括挿入用のいくつかのサンプル コマンドを以下に示します。

1. インポートする前に、データを分析して任意のカスタム オプションを設定し、SQL Server データベースの日付などのすべての特別なフィールドが同じ形式になるようにします。次に、日付形式を年-月-日 (データに年-月-日形式で日付が含まれている場合) に設定する方法の例を示します。

		SET DATEFORMAT ymd;	
	
2. 一括インポート ステートメントを使用してデータをインポートする:

    	BULK INSERT <tablename>
    	FROM	
    	'<datafilename>'
    	WITH 
    	(
		FirstRow=2,
    	FIELDTERMINATOR =',', --this should be column separator in your data
    	ROWTERMINATOR ='\n'   --this should be the row separator in your data
    	)
 	  

### <a name="sql-builtin-utilities">SQL Server の組み込みユーティリティ</a>

SQL Server 統合サービス (SSIS) を使用して、フラット ファイルから Azure の SQL Server VM にデータをインポートすることができます。SSIS は 2 つの Studio 環境で使用できます。詳細については、「[統合サービス (SSIS) と Studio 環境](https://technet.microsoft.com/library/ms140028.aspx)」を参照してください:。

- SQL Server データ ツールの詳細については、「[Microsoft SQL Server データ ツール](https://msdn.microsoft.com/data/tools.aspx)」を参照してください。  
- インポート/エクスポート ウィザードの詳細については、「[SQL Server のインポートおよびエクスポート ウィザード](https://msdn.microsoft.com/library/ms141209.aspx)」を参照してください。

### <a name="sqlonprem_to_sqlonazurevm"></a>オンプレミスの SQL Server からのデータの移動

データは、オンプレミスの SQL Server から次のように移動できます。

1. [フラット ファイルへのエクスポート](#export-flat-file) 
2. [SQL Database 移行ウィザード](#sql-migration)
3. [データベースのバックアップと復元](#sql-backup)

それぞれの方法について以下で説明します。

#### <a name="export-flat-file"></a>フラット ファイルへのエクスポート

[ここ](https://msdn.microsoft.com/library/ms175937.aspx)で説明されているように、さまざまな方法を使用してオンプレミスの SQL Server からデータを一括エクスポートできます。このドキュメントでは、一例として一括コピー プログラム (BCP) について説明します。データをフラット ファイルにエクスポートした後は、一括インポートを使用して別の SQL Server にそのデータをインポートできます。

1. 次のように bcp ユーティリティを使用して、オンプレミスの SQL Server からファイルにデータをエクスポートします。

	`bcp dbname..tablename out datafile.tsv -S	servername\sqlinstancename -T -t \t -t \n -c`

2. 手順 1 でエクスポートされたテーブル スキーマに対して `create database` と `create table` を使用して、データベースとテーブルを Azure の SQL Server VM に作成します。

3. エクスポート/インポートされているデータのテーブルのスキーマを記述するためのフォーマット ファイルを作成します。フォーマット ファイルの詳細については、[ここ](https://msdn.microsoft.com/library/ms191516.aspx)で説明されています。

	SQL Server マシンから BCP を実行する場合のフォーマット ファイルの生成

		bcp dbname..tablename format nul -c -x -f exportformatfilename.xml -S servername\sqlinstance -T -t \t -r \n 

	SQL Server に対して BCP をリモート実行する場合のフォーマット ファイルの生成

		bcp dbname..tablename format nul -c -x -f  exportformatfilename.xml  -U username@servername.database.windows.net -S tcp:servername -P password  --t \t -r \n
		
	
4. 「[ファイル ソースからのデータの移動](#filesource_to_sqlonazurevm)」セクションで説明されているいずれかの方法を使用して、フラット ファイルのデータを SQL Server に移動します。

#### <a name="sql-migration">SQL Database 移行ウィザード</a>

[SQL Server データベース移行ウィザード](http://sqlazuremw.codeplex.com/)は、2 つの SQL server インスタンス間でデータを移動するための使いやすい方法を提供します。これにより、ユーザーは、ソースと移動先テーブルの間のデータ スキーマをマップし、列のタイプおよびその他のさまざまな機能を選択できます。これは、内部で一括コピー (BCP) を使用します。次に、SQL データベースの移行ウィザードのようこそ画面のスクリーン ショットを示します。

![SQL Server 移行ウィザード][2]

#### <a name="sql-backup"></a>データベースのバックアップと復元

SQL Server は以下のものをサポートします。

1. [データベースのバックアップと復元機能](https://msdn.microsoft.com/library/ms187048.aspx) (ローカル ファイルに対するバックアップと復元、または BLOB への BACPAC のエクスポート) と[データ層アプリケーション](https://msdn.microsoft.com/library/ee210546.aspx) (BACPAC を使用)。 
2. コピーされたデータベースを使用して Azure で SQL Server VM を直接作成する機能、または既存の SQL Azure データベースにコピーする機能。詳細については、「[データベース コピー ウィザードの使用](https://msdn.microsoft.com/library/ms188664.aspx)」を参照してください。 

以下に、SQL Server Management Studio からのデータベースのバックアップ/復元オプションのスクリーン ショットを示します。

![SQL Server インポート ツール][1]


[1]: ./media/machine-learning-data-science-move-sql-server-virtual-machine/sqlserver_builtin_utilities.png
[2]: ./media/machine-learning-data-science-move-sql-server-virtual-machine/database_migration_wizard.png

 

<!---HONumber=July15_HO5-->