<properties
	pageTitle="Blob Storage から SQL Database へのデータのコピー | Microsoft Azure"
	description="このチュートリアルでは、Azure Data Factory パイプラインでコピー アクティビティを使用して、Blob Storage から SQL Database にデータをコピーする方法を示します。"
	Keywords="BLOB SQL, Blob Storage, データのコピー"
	services="data-factory"
	documentationCenter=""
	authors="spelluru"
	manager="jhubbard"
	editor="monicar"/>

<tags
	ms.service="data-factory"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article" 
	ms.date="08/01/2016"
	ms.author="spelluru"/>

# Data Factory を使用した Blob Storage から SQL Database へのデータのコピー 
> [AZURE.SELECTOR]
- [チュートリアルの概要](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
- [Data Factory エディターの使用](data-factory-copy-activity-tutorial-using-azure-portal.md)
- [PowerShell の使用](data-factory-copy-activity-tutorial-using-powershell.md)
- [Visual Studio の使用](data-factory-copy-activity-tutorial-using-visual-studio.md)
- [コピー ウィザードの使用](data-factory-copy-data-wizard-tutorial.md)

このチュートリアルでは、Blob Storage から SQL Database にデータをコピーするパイプラインを備えたデータ ファクトリを作成します。

コピー アクティビティにより、Azure Data Factory ではデータ移動が実行されます。また、このアクティビティは、安全で信頼性が高いスケーラブルな方法によってさまざまなデータ ストア間でデータをコピーできる、グローバルに利用可能なサービスによって動作します。コピー アクティビティの詳細については、「[データ移動アクティビティ](data-factory-data-movement-activities.md)」をご覧ください。

> [AZURE.NOTE] Data Factory サービスの詳細については、記事「[Azure Data Factory サービスの概要][data-factory-introduction]」を参照してください。

##このチュートリアルの前提条件
このチュートリアルを読み始める前に、次の項目を用意する必要があります。

- **Azure サブスクリプション**。サブスクリプションがない場合は、無料試用版のアカウントを数分で作成することができます。詳細については、[無料試用版][azure-free-trial]のページを参照してください。
- **Azure ストレージ アカウント**。このチュートリアルでは、BLOB ストレージを**ソース** データ ストアとして使用します。Azure ストレージ アカウントがない場合の作成手順については、[ストレージ アカウントの作成][data-factory-create-storage]に関する記事を参照してください。
- **Azure SQL データベース**。このチュートリアルでは、Azure SQL Database を**ターゲット** データ ストアとして使用します。このチュートリアルで使用できる Azure SQL Database がない場合の作成方法については、「[Azure SQL Database を作成して構成する方法][data-factory-create-sql-database]」を参照してください。
- **SQL Server 2012/2014 または Visual Studio 2013**。サンプル データベースを作成して、結果データをデータベースに表示するには、SQL Server Management Studio または Visual Studio を使用します。

## BLOB ストレージ アカウントの名前とキーを収集する 
このチュートリアルの内容を実行するには、Azure ストレージ アカウントのアカウント名とアカウント キーが必要です。以下の手順に従って、Azure ストレージ アカウントの**アカウント名**と**アカウント キー**をメモしておきます。

1. [Azure ポータル][azure-portal]にログインします。
2. 左側にある **[参照]** ハブをクリックし、**[ストレージ アカウント]** を選択します。
3. **[ストレージ アカウント]** ブレードで、このチュートリアルで使用する **Azure ストレージ アカウント**を選択します。
4. **[設定]** にある **[アクセス キー]** リンクを選択します。
5.  **[ストレージ アカウント名]** テキスト ボックスの隣にある (イメージの) **[コピー]** ボタンをクリックし、任意の場所 (たとえばテキスト ファイル) に貼り付けて保存します。
6. **key1** についても、前のコピー手順を繰り返すか、メモしておきます。
7. **[X]** をクリックしてすべてのブレードを閉じます。

## SQL サーバー、データベース、ユーザーの名前を収集する
このチュートリアルの内容を実行するには、Azure SQL のサーバー名、データベース名、ユーザー名が必要です。次の手順に従って、Azure SQL Database の**サーバー**、**データベース**、**ユーザー**の名前をメモしておきます。

1. **Azure ポータル**の左側にある **[参照]** をクリックし、**[SQL データベース]** を選択します。
2. **[SQL データベース]** ブレードで、このチュートリアルで使用する**データベース**を選択します。**データベース名**をメモしておきます。
3. **[SQL Database]** ブレードで **[プロパティ]** タイルをクリックします。
4. **[サーバー名]** と **[サーバー管理ログイン]** の値をメモしておきます。
5. **[X]** をクリックしてすべてのブレードを閉じます。

## Azure サービスに SQL サーバーへのアクセスを許可する 
Data Factory サービスから Azure SQL サーバーにアクセスできるように、Azure SQL サーバーで **[Azure サービスへのアクセスを許可する]** の設定が**オン**になっていることを確認します。この設定を確認してオンにするには、次の手順を実行します。

1. 左側にある **[参照]** ハブをクリックし、**[SQL サーバー]** をクリックします。
2. **お使いのサーバー**を選択し、**[SQL サーバー]** ブレードで **[設定]** をクリックします。
3. **[設定]** ブレードで **[ファイアウォール]** をクリックします。
4. **[ファイアウォールの設定]** ブレードの **[Azure サービスへのアクセスを許可する]** で **[オン]** をクリックします。
5. **[X]** をクリックしてすべてのブレードを閉じます。

## Blob Storage と SQL Database を準備する 
ここからは、次の手順を実行して、チュートリアルで使用する Azure Blob Storage と Azure SQL Database を準備します。

1. メモ帳を起動し、次のテキストを貼り付け、**emp.txt** という名前でハード ドライブの **C:\\ADFGetStarted** フォルダーに保存します。

        John, Doe
		Jane, Doe

2. [Azure Storage エクスプローラー](https://azurestorageexplorer.codeplex.com/)などのツールを使用して **adftutorial** コンテナーを作成し、このコンテナーに **emp.txt** ファイルをアップロードします。

    ![Azure Storage Explorer.Copy data from Blob storage to SQL database](./media/data-factory-copy-data-from-azure-blob-storage-to-sql-database/getstarted-storage-explorer.png)
3. 次の SQL スクリプトを使用して、**emp** テーブルを Azure SQL Database に作成します。


        CREATE TABLE dbo.emp
		(
			ID int IDENTITY(1,1) NOT NULL,
			FirstName varchar(50),
			LastName varchar(50),
		)
		GO

		CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID);

	**SQL Server 2012/2014 がコンピューターにインストールされている場合**: 記事「[SQL Server Management Studio を使用した Azure SQL Database の管理」の「手順 2. SQL Database への接続][sql-management-studio]」に従い、Azure SQL サーバーに接続して SQL スクリプトを実行します。この記事では、[この Azure ポータル](https://portal.azure.com)ではなく、[この Azure ポータル](http://manage.windowsazure.com)を使用して、Azure SQL サーバーのファイアウォールを構成することに注意してください。

	**Visual Studio 2013 がコンピューターにインストールされている場合:** [Azure ポータル](https://portal.azure.com)の左側にある **[参照]** ハブ、**[SQL サーバー]** の順にクリックしてデータベースを選択し、ツール バーの **[Visual Studio で開く]** をクリックして Azure SQL サーバーに接続し、スクリプトを実行します。クライアントから Azure SQL サーバーへのアクセスが許可されていない場合は、コンピューター (IP アドレス) からのアクセスを許可するように、Azure SQL Server のファイアウォールを構成する必要があります。Azure SQL サーバーのファイアウォールを構成する手順については、上の記事を参照してください。


以下の手順を実行します。

- Azure ポータルの一部である Data Factory エディターを使用してチュートリアルを実行するには、上部にある「[Data Factory エディターの使用](data-factory-copy-activity-tutorial-using-azure-portal.md)」というリンクをクリックします。
- Azure PowerShell を使用してチュートリアルを実行するには、上部にある「[PowerShell の使用](data-factory-copy-activity-tutorial-using-powershell.md)」というリンクをクリックします。
- Visual Studio 2013 を使用してチュートリアルを実行するには、上部にある「[Visual Studio の使用](data-factory-copy-activity-tutorial-using-visual-studio.md)」というリンクをクリックします。

## アクティビティ
Azure Data Factory のコピー アクティビティの詳細については、「[データ移動アクティビティ](data-factory-data-movement-activities.md)」を参照してください。


<!--Link references-->
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-portal]: https://portal.azure.com/
[sql-management-studio]: http://azure.microsoft.com/documentation/articles/sql-database-manage-azure-ssms/#Step2

[data-factory-introduction]: data-factory-introduction.md
[data-factory-create-storage]: http://azure.microsoft.com/documentation/articles/storage-create-storage-account/#create-a-storage-account
[data-factory-create-sql-database]: ../sql-database/sql-database-get-started.md

<!---HONumber=AcomDC_0810_2016-->