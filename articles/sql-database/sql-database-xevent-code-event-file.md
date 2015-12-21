<properties 
	pageTitle="SQL Database の XEvent イベント ファイル コード | Microsoft Azure" 
	description="Azure SQL Database の拡張イベントのイベント ファイル ターゲットを示す 2 段階コード サンプルの PowerShell と Transact-SQL を提供します。Azure Storage はこのシナリオの必須の部分です。" 
	services="sql-database" 
	documentationCenter="" 
	authors="MightyPen" 
	manager="jeffreyg" 
	editor="" 
	tags=""/>


<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/09/2015" 
	ms.author="genemi"/>


# SQL Database の拡張イベントのためのイベント ファイル ターゲット コード


拡張イベントに関する情報を確かな方法で取得し、レポートするための完全なコード サンプルが必要です。


Microsoft SQL Server では、イベント出力をローカル ハード ドライブ ファイルに保存するために[イベント ファイル ターゲット](http://msdn.microsoft.com/library/ff878115.aspx)が使用します。ただし、このファイルは Azure SQL Database で利用できません。代わりに、イベント ファイル ターゲットをサポートする Azure ストレージ サービスを使用します。


このトピックでは、2 段階のコード サンプルを使用します。


- PowerShell。クラウドで Azure Storage コンテナーを作成します。

- Transact-SQL:
 - イベント ファイル ターゲットに Azure Storage コンテナーを割り当てます。
 - イベント セッションを作成し、開始するなどを行います。


## 前提条件


- Azure アカウントとサブスクリプション。[無料試用版](http://azure.microsoft.com/pricing/free-trial/)にサインアップできます。


- テーブルを作成できるデータベース。
 - 必要に応じて、数分で [**AdventureWorksLT** デモ データベースを作成できる](sql-database-get-started.md)。


- SQL Server Management Studio (ssms.exe)、2015 年 8 月のプレビューまたはそれ以降のバージョン。最新の ssms.exe をダウンロードすることができる。
 - 「[SQL Server Management Studio のダウンロード](http://msdn.microsoft.com/library/mt238290.aspx)」というタイトルのトピック。
 - [ダウンロードへの直接リンク。](http://go.microsoft.com/fwlink/?linkid=616025)
 - マイクロソフトでは、ssms.exe を定期的に更新することをお勧めします。ssms.exe は毎月更新されることもあります。


- [Azure PowerShell モジュール](http://go.microsoft.com/?linkid=9811175)をインストールしておく必要があります。
 - このモジュールから **New-AzureStorageAccount** などのコマンドが提供されます。


## フェーズ 1: Azure Storage コンテナーの PowerShell コード


この PowerShell は 2 段階のコード サンプルの第 1 段階です。

このスクリプトは、前の実行があれば、その後でクリーンアップするコマンドで始まり、再実行可能として設計されています。



1. PowerShell スクリプトを Notepad.exe のような単純なテキスト エディターに貼り付け、**.ps1** 拡張子が付いたファイルとしてスクリプトを保存します。

2. PowerShell ISE を管理者として起動します。

3. プロンプトで、「<br/>`Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser`<br/>」と入力し、Enter キーを押します。

4. PowerShell ISE で、**.ps1** ファイルを開きます。スクリプトを実行します。

5. 最初に新しいウィンドウが開きます。そこから Azure にログインします。
 - セッションを妨げずにスクリプトを再実行するために、**Add-AzureAccount** コマンドをコメントアウトする便利なオプションがあります。


![Azure モジュールがインストールされ、スクリプトの実行準備が整っている PowerShell ISE。][30_powershell_ise]


&nbsp;


```
## TODO: Before running, find all 'TODO' and make each edit!

#--------------- 1 -----------------------


# You can comment out or skip this Add-AzureAccount command after the first run.
# Current PowerShell environment retains the successful outcome.

'Expect a pop-up window in which you log in to Azure.'


Add-AzureAccount

#-------------- 2 ------------------------


'
TODO: Edit the values assigned to these variables, especially the first few!
'

$subscriptionName       = 'YOUR_SUBSCRIPTION_NAME'
$policySasExpiryTime = '2016-01-28T23:44:56Z'
$policySasStartTime  = '2015-08-01'


$storageAccountName     = 'gmstorageaccountxevent'
$storageAccountLocation = 'West US'
$contextName            = 'gmcontext'
$containerName          = 'gmcontainerxevent'
$policySasToken      = 'gmpolicysastoken'


# Leave this value alone, as 'rwl'.
$policySasPermission = 'rwl'

#--------------- 3 -----------------------


# The ending display lists your Azure subscriptions.
# One should match the $subscriptionName value you assigned
#   earlier in this PowerShell script. 

'Choose an existing subscription for the current PowerShell environment.'


Select-AzureSubscription -SubscriptionName $subscriptionName


#-------------- 4 ------------------------


'
Clean-up the old Azure Storage Account after any previous run, 
before continuing this new run.'


If ($storageAccountName)
{
    Remove-AzureStorageAccount -StorageAccountName $storageAccountName
}

#--------------- 5 -----------------------

[System.DateTime]::Now.ToString()

'
Create a storage account. 
This might take several minutes, will beep when ready.
  ...PLEASE WAIT...'

New-AzureStorageAccount `
    -StorageAccountName $storageAccountName `
    -Location           $storageAccountLocation

[System.DateTime]::Now.ToString()

[System.Media.SystemSounds]::Beep.Play()


'
Get the primary access key for your storage account.
'


$primaryAccessKey_ForStorageAccount = `
    (Get-AzureStorageKey `
        -StorageAccountName $storageAccountName).Primary

"`$primaryAccessKey_ForStorageAccount = $primaryAccessKey_ForStorageAccount"

'Azure Storage Account cmdlet completed.
Remainder of PowerShell .ps1 script continues.
'

#--------------- 6 -----------------------


# The context will be needed to create a container within the storage account.

'Create a context object from the storage account and its primary access key.
'

$context = New-AzureStorageContext `
    -StorageAccountName $storageAccountName `
    -StorageAccountKey  $primaryAccessKey_ForStorageAccount


'Create a container within the storage account.
'


$containerObjectInStorageAccount = New-AzureStorageContainer `
    -Name    $containerName `
    -Context $context


'Create a security policy to be applied to the SAS token.
'

New-AzureStorageContainerStoredAccessPolicy `
    -Container  $containerName `
    -Context    $context `
    -Policy     $policySasToken `
    -Permission $policySasPermission `
    -ExpiryTime $policySasExpiryTime `
    -StartTime  $policySasStartTime 

'
Generate a SAS token for the container.
'
Try
{
    $sasTokenWithPolicy = New-AzureStorageContainerSASToken `
        -Name    $containerName `
        -Context $context `
        -Policy  $policySasToken
}
Catch 
{
    $Error[0].Exception.ToString()
}

#-------------- 7 ------------------------


'Display the values that YOU must edit into the Transact-SQL script next!:
'

"storageAccountName: $storageAccountName"
"containerName:      $containerName"
"sasTokenWithPolicy: $sasTokenWithPolicy"

'
REMINDER: sasTokenWithPolicy here might start with "?" character, which you must exclude from Transact-SQL.
'

'
(Later, return here to delete your Azure Storage account. See the preceding - Remove-AzureStorageAccount -StorageAccountName $storageAccountName)'

'
Now shift to the Transact-SQL portion of the two-part code sample!'

# EOFile
```


&nbsp;


PowerShell スクリプトが終了したら、出力された名前付きの値を書き留めます。後続の第 2 段階で、これらの値を使用するように Transact-SQL スクリプトを編集する必要があります。


## フェーズ 2: Azure Storage コンテナーを使用する Trasact-SQL コード


- このコード サンプルの第 1 段階で、PowerShell スクリプトを実行し、Azure ストレージ コンテナーを作成しました。
- 次の第 2 段階では、次の Transact-SQL スクリプトでそのコンテナーを使用する必要があります。


このスクリプトは、前の実行があれば、その後でクリーンアップするコマンドで始まり、再実行可能として設計されています。


PowerShell スクリプトの終了時に、名前付きの値がいくつか出力されました。それらの値を使用するように Transact-SQL スクリプトを編集する必要があります。Transact-SQL スクリプトの **TODO** を探し、編集する箇所を見つけます。


1. SQL Server Management Studio (ssms.exe) を開きます。

2. Azure SQL Database データベースに接続します。

3. クリックし、新しいクエリ ウィンドウを開きます。

4. 次の Transact-SQL スクリプトをクエリ ウィンドウに貼り付けます。

5. スクリプトにある **TODO** をすべて探し、適宜編集します。

6. 保存し、スクリプトを実行します。


&nbsp;


```
---- TODO: First, run the PowerShell portion of this two-part code sample.
---- TODO: Second, find every 'TODO' in this Transact-SQL file, and edit each.

---- Transact-SQL code for Event File target on Azure SQL Database.


SET NOCOUNT ON;

GO


----  Step 1.  Establish one little table, and  ---------
----  insert one row of data.


IF EXISTS
	(SELECT * FROM sys.objects
		WHERE type = 'U' and name = 'gmTabEmployee')
BEGIN
	DROP TABLE gmTabEmployee;
END
GO


CREATE TABLE gmTabEmployee
(
	EmployeeGuid         uniqueIdentifier   not null  default newid()  primary key,
	EmployeeId           int                not null  identity(1,1),
	EmployeeKudosCount   int                not null  default 0,
	EmployeeDescr        nvarchar(256)          null
);
GO


INSERT INTO gmTabEmployee ( EmployeeDescr )
	VALUES ( 'Jane Doe' );
GO


------  Step 2.  Create key, and  ------------
------  Create credential (your Azure Storage container must already exist).


IF NOT EXISTS
	(SELECT * FROM sys.symmetric_keys
		WHERE symmetric_key_id = 101)
BEGIN
	CREATE MASTER KEY ENCRYPTION BY PASSWORD = '0C34C960-6621-4682-A123-C7EA08E3FC46' -- Or any newid().
END
GO


IF EXISTS
	(SELECT * FROM sys.database_scoped_credentials
		-- TODO: Assign AzureStorageAccount name, and the associated Container name.
		WHERE name = 'https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent')
BEGIN
	DROP DATABASE SCOPED CREDENTIAL
		-- TODO: Assign AzureStorageAccount name, and the associated Container name.
		[https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent] ;
END
GO


CREATE
	DATABASE SCOPED
	CREDENTIAL
		-- use '.blob.',   and not '.queue.' or '.table.' etc.
		-- TODO: Assign AzureStorageAccount name, and the associated Container name.
		[https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent]
	WITH
		IDENTITY = 'SHARED ACCESS SIGNATURE',  -- "SAS" token.
		-- TODO: Paste in the long SasToken string here for Secret, but exclude any leading '?'.
		SECRET = 'sv=2014-02-14&sr=c&si=gmpolicysastoken&sig=EjAqjo6Nu5xMLEZEkMkLbeF7TD9v1J8DNB2t8gOKTts%3D'
	;
GO


------  Step 3.  Create (define) an event session.  --------
------  The event session has an event with an action,
------  and a has a target.

IF EXISTS
	(SELECT * from sys.database_event_sessions
		WHERE name = 'gmeventsessionname240b')
BEGIN
	DROP
		EVENT SESSION
			gmeventsessionname240b
	    ON DATABASE;
END
GO


CREATE
	EVENT SESSION
		gmeventsessionname240b
	ON DATABASE

	ADD EVENT
		sqlserver.sql_statement_starting
			(
			ACTION (sqlserver.sql_text)
			WHERE statement LIKE 'UPDATE gmTabEmployee%'
			)
	ADD TARGET
		package0.event_file
			(
			-- TODO: Assign AzureStorageAccount name, and the associated Container name.
			-- Also, tweak the .xel file name at end, if you like.
			SET filename =
				'https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent/anyfilenamexel242b.xel'
			)
	WITH
		(MAX_MEMORY = 10 MB,
		MAX_DISPATCH_LATENCY = 3 SECONDS)
	;
GO


------  Step 4.  Start the event session.  ----------------
------  Issue the SQL Update statements that will be traced.
------  Then stop the session.

------  Note: If the target fails to attach,
------  the session must be stopped and restarted.

ALTER
	EVENT SESSION
		gmeventsessionname240b
	ON DATABASE
	STATE = START;
GO


SELECT 'BEFORE_Updates', EmployeeKudosCount, * FROM gmTabEmployee;

UPDATE gmTabEmployee
	SET EmployeeKudosCount = EmployeeKudosCount + 2
	WHERE EmployeeDescr = 'Jane Doe';

UPDATE gmTabEmployee
	SET EmployeeKudosCount = EmployeeKudosCount + 13
	WHERE EmployeeDescr = 'Jane Doe';

SELECT 'AFTER__Updates', EmployeeKudosCount, * FROM gmTabEmployee;
GO


ALTER
	EVENT SESSION
		gmeventsessionname240b
	ON DATABASE
	STATE = STOP;
GO


-------------- Step 5.  Select the results. ----------

SELECT
		*, 'CLICK_NEXT_CELL_TO_BROWSE_ITS_RESULTS!' as [CLICK_NEXT_CELL_TO_BROWSE_ITS_RESULTS],
		CAST(event_data AS XML) AS [event_data_XML]  -- TODO: In ssms.exe results grid, double-click this cell!
	FROM
		sys.fn_xe_file_target_read_file
			(
				-- TODO: Fill in Storage Account name, and the associated Container name.
				'https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent/anyfilenamexel242b',
				null, null, null
			);
GO


-------------- Step 6.  Clean up. ----------

DROP
	EVENT SESSION
		gmeventsessionname240b
	ON DATABASE;
GO

DROP DATABASE SCOPED CREDENTIAL
	-- TODO: Assign AzureStorageAccount name, and the associated Container name.
	[https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent]
	;
GO

DROP TABLE gmTabEmployee;
GO

PRINT 'Use PowerShell Remove-AzureStorageAccount to delete your Azure Storage account!';
GO
```


&nbsp;


実行時にターゲットがアタッチできなかった場合、イベント セッションを停止し、再起動する必要があります。


```
ALTER EVENT SESSION ... STATE = STOP;
GO
ALTER EVENT SESSION ... STATE = START;
GO
```


&nbsp;


## 出力


Transact-SQL スクリプトが完了したら、**event\_data\_XML** 列ヘッダーの下にあるセルをクリックします。**<event>** 要素が 1 つ表示されます。これに UPDATE ステートメントが 1 つ表示されます。

ここに、テスト中に生成された **<event>** 要素が 1 つあります。


&nbsp;


```
<event name="sql_statement_starting" package="sqlserver" timestamp="2015-09-22T19:18:45.420Z">
  <data name="state">
    <value>0</value>
    <text>Normal</text>
  </data>
  <data name="line_number">
    <value>5</value>
  </data>
  <data name="offset">
    <value>148</value>
  </data>
  <data name="offset_end">
    <value>368</value>
  </data>
  <data name="statement">
    <value>UPDATE gmTabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 2
    WHERE EmployeeDescr = 'Jane Doe'</value>
  </data>
  <action name="sql_text" package="sqlserver">
    <value>

SELECT 'BEFORE_Updates', EmployeeKudosCount, * FROM gmTabEmployee;

UPDATE gmTabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 2
    WHERE EmployeeDescr = 'Jane Doe';

UPDATE gmTabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 13
    WHERE EmployeeDescr = 'Jane Doe';

SELECT 'AFTER__Updates', EmployeeKudosCount, * FROM gmTabEmployee;
</value>
  </action>
</event>
```





&nbsp;


## SQL Server で実行できるようにコード サンプルを変換する


先の Transact-SQL サンプルを Microsoft SQL Server で実行するとします。


- わかりやすくするために、Azure ストレージ コンテナーの使用を「**C:\\myeventdata.xel**」のような単純なファイルに完全に置換します。ファイルは SQL Server をホストするコンピューターのローカル ハード ドライブに書き込まれます。


- **CREATE MASTER KEY** と **CREATE CREDENTIAL** には Transact-SQL ステートメントを必要としません。


- **CREATE EVENT SESSION** ステートメントの **ADD TARGET** 句で、**filename=** に割り当てられている HTTP 値を「**C:\\myfile.xel**」のような完全パス文字列に置換します。
 - Azure ストレージ アカウントは必要ありません。


## 詳細情報


Azure SQL Database での拡張イベントに関する主なトピックは次のとおりです。

- [SQL Database の拡張イベント](sql-database-xevent-db-diff-from-svr.md) - Azure SQL データベースの拡張イベントの主要トピックです。
 - Azure SQL Database と Microsoft SQL Server の間で異なる拡張イベントの機能を比較します。


- [SQL Database の拡張イベントのためのリング バッファーのターゲット コード](sql-database-xevent-code-ring-buffer.md) - 簡素版であり、簡単なテストに向き、大規模な活動では堅牢性に劣る姉妹コード サンプルを提供します。


Azure ストレージ サービスのアカウントとコンテナーに関する詳細については、次を参照してください。

- [.NET から BLOB ストレージを使用する方法](storage-dotnet-how-to-use-blobs.md/)
- [コンテナー、BLOB、メタデータの名前付けと参照](http://msdn.microsoft.com/library/azure/dd135715.aspx)
- [ルート コンテナーの使用](http://msdn.microsoft.com/library/azure/ee395424.aspx)


<!--
Image references.
-->

[30_powershell_ise]: ./media/sql-database-xevent-code-event-file/event-file-powershell-ise-b30.png

<!---HONumber=AcomDC_1210_2015-->