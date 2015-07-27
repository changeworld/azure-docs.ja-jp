<properties
	pageTitle="Azure Data Factory を使い始める"
	description="このチュートリアルでは、BLOB から Azure SQL Database インスタンスにデータをコピーするサンプル データ パイプラインを作成する方法を示します。"
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
	ms.topic="hero-article" 
	ms.date="05/04/2015"
	ms.author="spelluru"/>

# Azure Data Factory を使ってみる
> [AZURE.SELECTOR]
- [Tutorial Overview](data-factory-get-started.md)
- [Using Data Factory Editor](data-factory-get-started-using-editor.md)
- [Using PowerShell](data-factory-monitor-manage-using-powershell.md)

この記事のチュートリアルは、Azure Data Factory サービスの使用をスムーズに開始するのに役立ちます。このチュートリアルでは、Azure Data Factory を作成し、その Data Factory 内にパイプラインを作成して、Azure BLOB ストレージから Azure SQL Database にデータをコピーする方法について説明します。

> [AZURE.NOTE]Data Factory サービスの詳細については、記事「[Azure Data Factory サービスの概要][data-factory-introduction]」を参照してください。

##このチュートリアルの前提条件
このチュートリアルを読み始める前に、次の項目を用意する必要があります。

- **Azure サブスクリプション**。サブスクリプションがない場合は、無料試用版のアカウントを数分で作成することができます。詳細については、[無料試用版][azure-free-trial]のページを参照してください。
- **Azure ストレージ アカウント**。このチュートリアルでは、BLOB ストレージを**ソース** データ ストアとして使用します。Azure ストレージ アカウントがない場合の作成手順については、[ストレージ アカウントの作成][data-factory-create-storage]に関する記事を参照してください。
- **Azure SQL データベース**。このチュートリアルでは、Azure SQL Database を**ターゲット** データ ストアとして使用します。このチュートリアルで使用できる Azure SQL Database がない場合の作成方法については、「[Azure SQL Database を作成して構成する方法][data-factory-create-sql-database]」を参照してください。
- **SQL Server 2012/2014 または Visual Studio 2013**。サンプル データベースを作成して、結果データをデータベースに表示するには、SQL Server Management Studio または Visual Studio を使用します。  

### Azure ストレージ アカウントのアカウント名とアカウント キーを収集する
このチュートリアルの内容を実行するには、Azure ストレージ アカウントのアカウント名とアカウント キーが必要です。以下の手順に従って、Azure ストレージ アカウントの**アカウント名**と**アカウント キー**をメモしておきます。

1. [Azure プレビュー ポータル][azure-preview-portal]にログインします。
2. 左側にある **[参照]** ハブをクリックし、**[ストレージ アカウント]** を選択します。
3. **[ストレージ アカウント]** ブレードで、このチュートリアルで使用する **Azure ストレージ アカウント**を選択します。
4. **[ストレージ]** ブレードで **[キー]** タイルをクリックします。
5. **[キーの管理]** ブレードで、**[ストレージ アカウント名]** テキスト ボックスの隣にある (イメージの) **[コピー]** ボタンをクリックし、任意の場所 (たとえばテキスト ファイル) に貼り付けて保存します。  
6. **プライマリ アクセス キー**についても、前のコピー手順を繰り返すか、またはメモしておきます。
7. **[X]** をクリックしてすべてのブレードを閉じます。

### Azure SQL Database のサーバー名、データベース名、ユーザー アカウントを収集する
このチュートリアルの内容を実行するには、Azure SQL のサーバー名、データベース名、ユーザー名が必要です。次の手順に従って、Azure SQL Database の**サーバー**、**データベース**、**ユーザー**の名前をメモしておきます。

1. **Azure プレビュー ポータル**の左側にある **[参照]** をクリックし、**[SQL Database]** を選択します。
2. **[SQL Database]** ブレードで、このチュートリアルで使用する**データベース**を選択します。**データベース名**をメモしておきます。  
3. **[SQL Database]** ブレードで **[プロパティ]** タイルをクリックします。
4. **[サーバー名]** と **[サーバー管理ログイン]** の値をメモしておきます。
5. **[X]** をクリックしてすべてのブレードを閉じます。

### Azure のサービスから Azure SQL サーバーへのアクセスを許可します。
Data Factory サービスから Azure SQL サーバーにアクセスできるように、Azure SQL サーバーで **[Azure サービスへのアクセスを許可する]** の設定が**オン**になっていることを確認します。この設定を確認してオンにするには、次の手順を実行します。

1. 左側にある **[参照]** ハブをクリックし、**[SQL サーバー]** をクリックします。
2. **お使いのサーバー**を選択し、**[SQL サーバー]** ブレードで **[設定]** をクリックします。
3. **[設定]** ブレードで **[ファイアウォール]** をクリックします。
4. **[ファイアウォールの設定]** ブレードの **[Azure サービスへのアクセスを許可する]** で **[オン]** をクリックします。
5. **[X]** をクリックしてすべてのブレードを閉じます。

### Azure BLOB ストレージと Azure SQL データベースをチュートリアル用に準備する
ここからは、次の手順を実行して、チュートリアルで使用する Azure BLOB ストレージと Azure SQL Database を準備します。

1. メモ帳を起動し、次のテキストを貼り付け、**emp.txt** という名前でハード ドライブの **C:\\ADFGetStarted** フォルダーに保存します。

        John, Doe
		Jane, Doe

2. [Azure Storage エクスプローラー](https://azurestorageexplorer.codeplex.com/)などのツールを使用して **adftutorial** コンテナーを作成し、このコンテナーに **emp.txt** ファイルをアップロードします。

    ![Azure Storage Explorer][image-data-factory-get-started-storage-explorer]
3. 次の SQL スクリプトを使用して、**emp** テーブルを Azure SQL Database に作成します。  


        CREATE TABLE dbo.emp
		(
			ID int IDENTITY(1,1) NOT NULL,
			FirstName varchar(50),
			LastName varchar(50),
		)
		GO

		CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID);

	**SQL Server 2012/2014 がコンピューターにインストールされている場合**: 記事「[SQL Server Management Studio を使用した Azure SQL Database の管理」の「手順 2. SQL Database への接続][sql-management-studio]」に従い、Azure SQL サーバーに接続して SQL スクリプトを実行します。この記事では、プレビュー ポータル (http://portal.azure.com) ではなく、リリースの管理ポータル (http://manage.windowsazure.com) を使用して、Azure SQL サーバーのファイアウォールを構成します。

	**Visual Studio 2013 がコンピューターにインストールされている場合:** [Azure プレビュー ポータル](http://portal.azure.com)の左側にある **[参照]** ハブ、**[SQL サーバー]** の順にクリックしてデータベースを選択し、ツール バーの **[Visual Studio で開く]** をクリックして Azure SQL サーバーに接続し、スクリプトを実行します。クライアントから Azure SQL サーバーへのアクセスが許可されていない場合は、コンピューター (IP アドレス) からのアクセスを許可するように、Azure SQL Server のファイアウォールを構成する必要があります。Azure SQL サーバーのファイアウォールを構成する手順については、上の記事を参照してください。


以下の手順を実行します。

- Azure ポータルの一部である Data Factory エディターを使用してチュートリアルを実行するには、上部にある「[Data Factory エディターの使用](data-factory-get-started-using-editor.md)」というリンクをクリックします。
- Azure PowerShell を使用してチュートリアルを実行するには、上部にある「[PowerShell の使用](data-factory-monitor-manage-using-powershell.md)」というリンクをクリックします。


<!--Link references-->
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[msdn-activities]: https://msdn.microsoft.com/library/dn834988.aspx
[msdn-linkedservices]: https://msdn.microsoft.com/library/dn834986.aspx
[data-factory-naming-rules]: https://msdn.microsoft.com/library/azure/dn835027.aspx

[azure-preview-portal]: https://portal.azure.com/
[download-azure-powershell]: http://azure.microsoft.com/documentation/articles/install-configure-powershell
[sql-management-studio]: http://azure.microsoft.com/documentation/articles/sql-database-manage-azure-ssms/#Step2
[sql-cmd-exe]: https://msdn.microsoft.com/library/azure/ee336280.aspx

[data-factory-editor]: data-factory-editor.md
[monitor-manage-using-powershell]: data-factory-monitor-manage-using-powershell.md
[use-onpremises-datasources]: data-factory-use-onpremises-datasources.md
[adf-tutorial]: data-factory-tutorial.md
[use-custom-activities]: data-factory-use-custom-activities.md
[use-pig-and-hive-with-data-factory]: data-factory-pig-hive-activities.md
[copy-activity]: data-factory-copy-activity.md
[troubleshoot]: data-factory-troubleshoot.md
[data-factory-introduction]: data-factory-introduction.md
[data-factory-create-storage]: http://azure.microsoft.com/documentation/articles/storage-create-storage-account/#create-a-storage-account
[data-factory-create-sql-database]: ../sql-database-create-configure.md


[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456

<!--Image references-->

[DataSlicesBySliceTime]: ./media/data-factory-get-started/DataSlicesBySliceTime.png

[image-data-factory-getstarted-new-everything]: ./media/data-factory-get-started/GetStarted-New-Everything.png

[image-data-factory-gallery-storagecachebackup]: ./media/data-factory-get-started/getstarted-gallery-datastoragecachebackup.png

[image-data-factory-gallery-storagecachebackup-seeall]: ./media/data-factory-get-started/getstarted-gallery-datastoragecachebackup-seeall.png

[image-data-factory-getstarted-data-services-data-factory-selected]: ./media/data-factory-get-started/getstarted-data-services-data-factory-selected.png

[image-data-factory-getstarted-data-factory-create-button]: ./media/data-factory-get-started/getstarted-data-factory-create-button.png

[image-data-factory-getstarted-new-data-factory-blade]: ./media/data-factory-get-started/getstarted-new-data-factory.png

[image-data-factory-get-stated-factory-home-page]: ./media/data-factory-get-started/getstarted-data-factory-home-page.png

[image-author-deploy-tile]: ./media/data-factory-get-started/getstarted-author-deploy-tile.png

[image-editor-newdatastore-button]: ./media/data-factory-get-started/getstarted-editor-newdatastore-button.png

[image-editor-blob-storage-json]: ./media/data-factory-get-started/getstarted-editor-blob-storage-json.png

[image-editor-blob-storage-deploy]: ./media/data-factory-get-started/getstarted-editor-blob-storage-deploy.png

[image-editor-azure-sql-settings]: ./media/data-factory-get-started/getstarted-editor-azure-sql-settings.png

[image-editor-newpipeline-button]: ./media/data-factory-get-started/getstarted-editor-newpipeline-button.png

[image-datafactoryblade-diagramtile]: ./media/data-factory-get-started/getstarted-datafactoryblade-diagramtile.png


[image-data-factory-get-started-startboard]: ./media/data-factory-get-started/getstarted-data-factory-startboard.png

[image-data-factory-get-started-linked-services-link]: ./media/data-factory-get-started/getstarted-data-factory-linked-services-link.png

[image-data-factory-get-started-linked-services-add-store-button]: ./media/data-factory-get-started/getstarted-linked-services-add-store-button.png

[image-data-factory-linked-services-get-started-new-data-store]: ./media/data-factory-get-started/getstarted-linked-services-new-data-store.png

[image-data-factory-get-started-new-data-store-with-storage]: ./media/data-factory-get-started/getstarted-linked-services-new-data-store-with-storage.png

[image-data-factory-get-started-storage-account-name-key]: ./media/data-factory-get-started/getstarted-storage-account-name-key.png

[image-data-factory-get-started-linked-services-list-with-myblobstore]: ./media/data-factory-get-started/getstarted-linked-services-list-with-myblobstore.png

[image-data-factory-get-started-linked-azure-sql-properties]: ./media/data-factory-get-started/getstarted-linked-azure-sql-properties.png

[image-data-factory-get-started-azure-sql-connection-string]: ./media/data-factory-get-started/getstarted-azure-sql-connection-string.png

[image-data-factory-get-started-linked-services-list-two-stores]: ./media/data-factory-get-started/getstarted-linked-services-list-two-stores.png

[image-data-factory-get-started-storage-explorer]: ./media/data-factory-get-started/getstarted-storage-explorer.png

[image-data-factory-get-started-diagram-link]: ./media/data-factory-get-started/getstarted-diagram-link.png

[image-data-factory-get-started-diagram-blade]: ./media/data-factory-get-started/getstarted-diagram-blade.png

[image-data-factory-get-started-home-page-pipeline-tables]: ./media/data-factory-get-started/getstarted-datafactory-home-page-pipeline-tables.png

[image-data-factory-get-started-datasets-blade]: ./media/data-factory-get-started/getstarted-datasets-blade.png

[image-data-factory-get-started-table-blade]: ./media/data-factory-get-started/getstarted-table-blade.png

[image-data-factory-get-started-dataslices-blade]: ./media/data-factory-get-started/getstarted-dataslices-blade.png

[image-data-factory-get-started-dataslice-blade]: ./media/data-factory-get-started/getstarted-dataslice-blade.png

[image-data-factory-get-started-sql-query-results]: ./media/data-factory-get-started/getstarted-sql-query-results.png

[image-data-factory-get-started-datasets-emptable-selected]: ./media/data-factory-get-started/DataSetsWithEmpTableFromBlobSelected.png

[image-data-factory-get-started-activity-run-details]: ./media/data-factory-get-started/ActivityRunDetails.png

[image-data-factory-create-resource-group]: ./media/data-factory-get-started/CreateNewResourceGroup.png

[image-data-factory-preview-storage-key]: ./media/data-factory-get-started/PreviewPortalStorageKey.png

[image-data-factory-database-connection-string]: ./media/data-factory-get-started/DatabaseConnectionString.png

[image-data-factory-new-datafactory-menu]: ./media/data-factory-get-started/NewDataFactoryMenu.png

[image-data-factory-sql-management-console]: ./media/data-factory-get-started/getstarted-azure-sql-management-console.png

[image-data-factory-sql-management-console-2]: ./media/data-factory-get-started/getstarted-azure-sql-management-console-2.png

[image-data-factory-name-not-available]: ./media/data-factory-get-started/getstarted-data-factory-not-available.png
 

<!---HONumber=July15_HO3-->