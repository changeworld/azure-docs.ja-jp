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

この記事では、チュートリアルでは、すぐに、Azure のデータのファクトリのサービスを使用して使用できます。このチュートリアルでは、Azure のデータのファクトリを作成し、データを Azure blob ストレージから Azure SQL データベースをコピーするデータのファクトリでのパイプラインを作成します。

> [AZURE.NOTE]データのファクトリのサービスの詳細については、次を参照してください。、 [Azure のデータのファクトリの概要][data-factory-introduction] 記事です。

##このチュートリアルの前提条件
このチュートリアルを読み始める前に、次の項目を用意する必要があります。

- **Azure サブスクリプション**。サブスクリプションを持っていない場合は、いくつかの分だけで無料の試用アカウントを作成できます。参照してください、 [無料評価版][azure-free-trial] の詳細については、資料です。
- **Azure のストレージ アカウント**です。Blob ストレージとして使用する、 **ソース** を参照して、Azure のストレージ アカウントがあるない場合、このチュートリアルでのデータ ストア、 [ストレージ アカウントの作成][data-factory-create-storage] を 1 つを作成する手順については、資料です。
- **Azure SQL データベース**。としての Azure SQL データベースを使用するが、 **送信先** このチュートリアルではデータを格納します。チュートリアルでは、「」を参照している、Azure SQL データベースがない場合は、 [を作成して、Azure SQL データベースを構成する方法][data-factory-create-sql-database] を 1 つ作成します。
- **2014/SQL Server 2012 または Visual Studio 2013**です。サンプル データベースを作成して、データベース内の結果データを表示するには、SQL Server Management Studio または Visual Studio を使用するが。  

### アカウント名と、Azure ストレージ アカウントのアカウント キーを収集します。
アカウント名とアカウントのキーをこのチュートリアルを行うには、Azure ストレージ アカウントの必要があります。下に注意してください、 **アカウント名** と **アカウント キー** の以下の手順では、Azure ストレージ アカウント。

1. ログイン、 [Azure プレビュー ポータル][azure-preview-portal]です。
2. クリックして **参照** ハブをクリックし、左 **ストレージ アカウント**です。
3.  **ストレージ アカウント** ブレードでは、select、 **Azure のストレージ アカウント** このチュートリアルで使用します。
4.  **ストレージ** ブレードで] をクリックして **キー** 並べて表示します。
5.  **キーの管理** ブレードで] をクリックして **コピー** 横に (イメージ) のボタン **ストレージ アカウント名** テキスト ボックスと [保存と貼り付けがどこかに (たとえば: テキスト ファイルに)。  
6. コピーまたは応答では、前の手順を繰り返して、 **プライマリ アクセス キー**です。
7. クリックして、すべてのブレードを閉じる **X**です。

### サーバー名、データベース名、および Azure SQL データベース用のユーザー アカウントを収集します。
Azure の SQL サーバー、データベース、およびをこのチュートリアルを行うにはユーザーの名前の必要があります。名前をメモ **サーバー**, 、**データベース**, 、および **ユーザー** 以下の手順を次には、Azure SQL データベースの。

1.  **Azure プレビュー ポータル**, 、] をクリックして **参照** クリックし、左上 **SQL データベース**です。
2.  **SQL データベースのブレード**, を選択して、 **データベース** このチュートリアルで使用します。下に注意してください、 **データベース名**です。  
3.  **SQL データベース** ブレードで] をクリックして **プロパティ** 並べて表示します。
4. 下の値への注 **サーバー名** と **SERVER 管理者ログイン**です。
5. クリックして、すべてのブレードを閉じる **X**です。

### Azure のサービスに、Azure の SQL server のアクセスを許可します。
いることを確認 **Azure サービスにアクセスできるように** 設定になっている **ON** 、Azure の SQL server 用、データのファクトリのサービスは、Azure の SQL server にアクセスできるようにします。確認してこの設定をオンにするには、次のように行います。

1. クリックして **参照** ハブをクリックして、左に **SQL server**です。
2. 選択 **サーバー**, 、] をクリック **設定** 上、 **SQL SERVER** ブレードします。
3.  **設定** ブレードでクリックして **ファイアウォール**です。
4.  **ファイアウォールの設定** ブレードで] をクリックして **ON** の **Azure サービスにアクセスできるように**です。
5. クリックして、すべてのブレードを閉じる **X**です。

### Azure BLOB ストレージと Azure SQL データベースをチュートリアル用に準備する
これで、Azure blob ストレージと Azure SQL データベース チュートリアルの準備、次の手順を実行することによって。

1. メモ帳を起動する、次のテキストを貼り付け、そのとして保存 **emp.txt** に **C:\ADFGetStarted** 、ハード ドライブ上のフォルダーです。

        John, Doe
		Jane, Doe

2. などのツールを使用して [Azure ストレージ エクスプ ローラー](https://azurestorageexplorer.codeplex.com/) を作成する、 **adftutorial** コンテナーにアップロードして、 **emp.txt** コンテナーにファイルです。

    ![Azure ストレージ エクスプローラー][image-data-factory-get-started-storage-explorer]
3. 作成するには、次の SQL スクリプトを使用して、 **emp** Azure SQL データベース内のテーブルです。  


        CREATE TABLE dbo.emp
		(
			ID int IDENTITY(1,1) NOT NULL,
			FirstName varchar(50),
			LastName varchar(50),
		)
		GO

		CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID);

	**SQL Server 2012/2014 をコンピューターにインストールされている必要があるかどうか:** からの指示に従います [手順 2: 管理する Azure SQL データベースの SQL Server Management Studio を使用して SQL データベースへの接続][sql-management-studio] 記事では、Azure の SQL server に接続し、SQL スクリプトを実行します。メモこの記事が、リリースの管理ポータルを使用する (http://manage.windowsazure.com), 、プレビュー ポータルではありません (http://portal.azure.com), 、Azure の SQL server のファイアウォールを構成します。

	**Visual Studio 2013 がコンピューターにインストールされている必要があるかどうか:** で、 [Azure プレビュー ポータル](http://portal.azure.com), 、] をクリックして **参照** ハブで、左クリック **SQL サーバー**, 、データベースを選択し、 **Visual Studio で開いて** ツールバーは、Azure の SQL server に接続し、スクリプトを実行するのです。Azure の SQL server へのアクセスに、クライアントが許可されない場合は、コンピューター (IP アドレス) からのアクセスを許可するには、Azure の SQL server 用のファイアウォールを構成する必要があります。Azure の SQL server のファイアウォールを構成する手順については、上の記事を参照してください。


以下の手順を実行します。

- クリックして [データ工場出荷時のエディターを使用した](data-factory-get-started-using-editor.md) 最上部を Azure ポータルの一部であるデータ工場出荷時のエディターを使用して、チュートリアルを実行する]。
- クリックして [PowerShell を使用した](data-factory-monitor-manage-using-powershell.md) Azure PowerShell を使用して、チュートリアルを実行する上部にあるリンクです。


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

<!---HONumber=GIT-SubDir--> 