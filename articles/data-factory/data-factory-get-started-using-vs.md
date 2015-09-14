<properties 
	pageTitle="チュートリアル: コピー アクティビティがあるパイプラインを Visual Studio で作成する"
	description="このチュートリアルでは、Visual Studio を使用して、コピー アクティビティがある Azure Data Factory パイプラインを作成します。"
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
	ms.date="07/27/2015"
	ms.author="spelluru"/>

# チュートリアル: Visual Studio を使用したデータ ファクトリの作成と監視
> [AZURE.SELECTOR]
- [Tutorial Overview](data-factory-get-started.md)
- [Using Data Factory Editor](data-factory-get-started-using-editor.md)
- [Using PowerShell](data-factory-monitor-manage-using-powershell.md)
- [Using Visual Studio](data-factory-get-started-using-vs.md)


##このチュートリアルの内容
このチュートリアルでは、まず Azure プレビュー ポータルを使用して、**ADFTutorialDataFactoryVS** という名前の Azure のデータのファクトリを作成し、次に Visual Studio 2013 を使用して以下のことを実行します。

1. リンクされた次の 2 つのサービス、**AzureStorageLinkedService1** と **AzureSqlinkedService1** を作成します。AzureStorageLinkedService1 は、Azure ストレージにリンクし、AzureSqlLinkedService1 は、Azure SQL database を **ADFTutorialDataFactoryVS** データ ファクトリにリンクします。パイプラインの入力データは、Azure BLOB ストレージの BLOB コンテナーにあります。また出力データは、Azure SQL Database のテーブルに格納されます。そのため、これら 2 つのデータ ストアをリンクされたサービスとしてデータ ファクトリに追加します。
2. **EmpTableFromBlob** と **EmpSQLTable** の 2 つのデータ ファクトリ テーブルを定義します。これらはデータ ストアに格納されている入力/出力データを表します。EmpTableFromBlob の場合、ソース データを持つ BLOB を含む BLOB コンテナーを指定します。EmpSQLTable の場合は、出力データを格納する SQL テーブルを指定します。また、データの構造や可用性など、他のプロパティも指定します。
3. ADFTutorialDataFactoryVS に **ADFTutorialPipeline** という名前のパイプラインを作成します。このパイプラインには、Azure BLOB から Azure SQL 出力テーブルに入力データをコピーする**コピー アクティビティ**があります。 


## <a name="CreateDataFactory"></a>Azure Data Factory を作成します。
この手順では、Azure プレビュー ポータルを使用して、**ADFTutorialDataFactory** という名前の Azure Data Factory を作成します。

1.	[Azure プレビュー ポータル](http://portal.azure.com)にログインしたら、左下隅にある **[新規]** をクリックします。**[作成]** ブレードで **[データ分析]** を選択し、**[データ分析]** ブレードで **[Data Factory]** をクリックします。 

	![New->DataFactory](./media/data-factory-get-started-using-vs/NewDataFactoryMenu.png)

6. **[新しいデータ ファクトリ]** ブレードで以下の手順を実行します。
	1. **[名前]** に「**ADFTutorialDataFactoryVS**」と入力します。 
	
  		![New data factory blade](./media/data-factory-get-started-using-vs/getstarted-new-data-factory.png)
	2. **[リソース グループ名]** をクリックし、以下の手順を実行します。
		1. **[新しいリソース グループを作成]** をクリックします。
		2. **[リソース グループの作成]** ブレードで、リソース グループの **[名前]** に「**ADFTutorialResourceGroup**」と入力し、**[OK]** をクリックします。 

			![Create Resource Group](./media/data-factory-get-started-using-vs/CreateNewResourceGroup.png)

		このチュートリアルの一部の手順は、**ADFTutorialResourceGroup** という名前のリソース グループを使用することを前提としています。リソース グループの詳細については、[リソース グループを使用した Azure のリソースの管理](resource-group-overview.md)に関するページを参照してください。  
7. **[新しいデータ ファクトリ]** ブレードで、**[スタート画面に追加する]** が選択されていることを確認してください。
8. **[新しいデータ ファクトリ]** ブレードで **[作成]** をクリックします。

	Azure Data Factor の名前はグローバルに一意にする必要があります。**“ADFTutorialDataFactoryVS" という名前の Data Factory は使用できません"** というエラーが発生した場合は、データ ファクトリの名前を変更して (yournameADFTutorialDataFactoryVS など) 作成し直してください。このチュートリアルでは以降の手順の実行中に、この名前を ADFTutorialFactory の代わりに使用します。Data Factory アーティファクトの名前付け規則については、[Data Factory - 名前付け規則] [data-factory-naming-rules]に関するトピックをご覧ください。
	 
	![Data Factory name not available](./media/data-factory-get-started-using-vs/getstarted-data-factory-not-available.png)

9. 左側の **[通知]** ハブをクリックし、作成プロセスからの通知を探します。**[通知]** ブレードが開いている場合は、**[X]** をクリックして閉じます。
10. 作成が完了すると、次に示すような **[Data Factory]** ブレードが表示されます。

    ![Data factory home page](./media/data-factory-get-started-using-vs/getstarted-data-factory-home-page.png)

## Visual Studio を使用して、Data Factory のエンティティを作成し、デプロイする 

### 前提条件
コンピューターに Visual Studio 2013 がインストールされており、Azure SDK for Visual Studio 2013 がダウンロードされている必要があります。[Azure ダウンロード ページ](http://azure.microsoft.com/downloads/) に移動して、**.NET** セクションの**[VS 2013 install]** をクリックします。

### チュートリアル

#### Visual Studio プロジェクトの作成 
1. **Visual Studio 2013** を起動します。**[ファイル]** をクリックし、**[新規作成]** をポイントして、**[プロジェクト]** をクリックします。**[新しいプロジェクト]** ダイアログ ボックスが表示されます。  
2. **[新しいプロジェクト]** ダイアログ ボックスで、**[DataFactory]** テンプレートを選択し、**[Empty Data Factory Project]** をクリックします。DataFactory テンプレートが表示されない場合は、Visual Studio を終了し、Azure SDK for Visual Studio 2013 をインストールし、Visual Studio を再度開きます。  

	![New Project dialog box](./media/data-factory-get-started-using-vs/new-project-dialog.png)

3. プロジェクトの**名前**、**場所**、**ソリューション**の名前を入力して、**[OK]** をクリックします。

	![Solution Explorer](./media/data-factory-get-started-using-vs/solution-explorer.png)

#### リンクされたサービスの作成
リンクされたサービスは、データ ストアまたはコンピューティング サービスを Azure Data Factory にリンクします。データ ストアには、Azure Storage、Azure SQL Database、またはオンプレミスの SQL Server データベースを指定できます。

この手順では、**AzureStorageLinkedService1** と **AzureSqlLinkedService1** の 2 つのリンクされたサービスを作成します。リンクされたサービス AzureStorageLinkedService1 は Azure ストレージ アカウントをリンクし、AzureSqlLinkedService は Azure SQL Database を **ADFTutorialDataFactory** という Data Factory にリンクします。

##### Azure ストレージのリンクされたサービスを作成します。

4. ソリューション エクスプローラーの **[Linked Services]** を右クリックして、**[追加]** をポイントし、**[新しい項目]** をクリックします。      
5. **[新しい項目の追加]** ダイアログ ボックスで、一覧から **[Azure Storage Linked Service]** を選択し、**[追加]** をクリックします。 

	![新規のリンクされたサービス](./media/data-factory-get-started-using-vs/new-linked-service-dialog.png)
 
3. **accountname** と **accountkey** を Azure ストレージ アカウントとそのキーで置き換えます。

	![Azure Storage のリンクされたサービス](./media/data-factory-get-started-using-vs/azure-storage-linked-service.png)

4. **AzureStorageLinkedService1.json** ファイルを保存します。

#### Azure SQL のリンクされたサービスを作成します。

5. **[ソリューション エクスプローラー]** で **[Linked Services]** ノードをもう一度右クリックし、**[追加]** をポイントし、**[新しい項目]** をクリックします。 
6. このとき、**[Azure SQL Linked Service]** をクリックし、**[追加]** をクリックします。 
7. **AzureSqlLinkedService1.json ファイル**で、**servername**、**databasename**、****username@servername**、**password** を Azure SQL のサーバー名、データベース、ユーザー アカウント、パスワードで置き換えます。
8.  **AzureSqlLinkedService1.json** ファイルを保存します。 


### 入力テーブルと出力テーブルを作成する
前の手順では、**AzureStorageLinkedService1** と **AzureSqlLinkedService1** というリンクされたサービスを作成し、Azure ストレージ アカウントと Azure SQL Database を **ADFTutorialDataFactory** というデータ ファクトリにリンクしました。この手順では、**EmpTableFromBlob** と **EmpSQLTable** の 2 つのデータ ファクトリ テーブルを定義します。これらは AzureStorageLinkedService1 と AzureSqlLinkedService1 がそれぞれ参照するデータ ストアに格納されている入力/出力データを表します。EmpTableFromBlob の場合、ソース データを持つ BLOB を含む BLOB コンテナーを指定します。EmpSQLTable の場合は、出力データを格納する SQL テーブルを指定します。

#### 入力テーブルの作成

9. **[ソリューション エクスプローラー]** の **[テーブル]** を右クリックし、**[追加]** をポイントし、**[新しい項目]** をクリックします。
10. **[新しい項目の追加]** ダイアログ ボックスで、**[Azure BLOB]** をクリックし、**[追加]** をクリックします。   
10. JSON テキストを次のテキストで置き換え、**AzureBlobLocation1.json** ファイルを保存します。 

		{
    		"name": "EmpTableFromBlob",
        	"properties":
        	{
            	"structure":  
                [ 
	                { "name": "FirstName", "type": "String"},
	                { "name": "LastName", "type": "String"}
	            ],
	            "location": 
	            {
	                "type": "AzureBlobLocation",
	                "folderPath": "adftutorial/",
	                "format":
	                {
	                    "type": "TextFormat",
	                    "columnDelimiter": ","
	                },
	                "linkedServiceName": "AzureStorageLinkedService1"
	            },
	            "availability": 
	            {
	                "frequency": "Hour",
	                "interval": 1,
	                "waitOnExternal": {}
                }
	        }
		}

#### 出力テーブルの作成

11. **[ソリューション エクスプローラー]** の **[テーブル]** をもう一度右クリックし、**[追加]** をポイントし、**[新しい項目]** をクリックします。
12. **[新しい項目の追加]** ダイアログ ボックスで、**[Azure SQL]** を選択し、**[追加]** をクリックします。 
13. JSON テキストを次の JSON テキストで置き換え、**AzureSqlTableLocation1.json** ファイルを保存します。

		{
		    "name": "EmpSQLTable",
		    "properties":
		    {
		        "structure":
		        [
		            { "name": "FirstName", "type": "String"},
		            { "name": "LastName", "type": "String"}
		        ],
		        "location":
		        {
		            "type": "AzureSqlTableLocation",
		            "tableName": "emp",
		            "linkedServiceName": "AzureSqlLinkedService1"
		        },
		        "availability": 
		        {
		            "frequency": "Hour",
		            "interval": 1            
		        }
		    }
		}

#### パイプラインの作成 
入力/出力のリンクされたサービスとテーブルの作成は完了しました。次に、**コピー アクティビティ**を使用してパイプラインを作成し、Azure BLOB から Azure SQL Database にデータをコピーします。


1. **[ソリューション エクスプローラー]** の **[パイプライン]** を右クリックし、**[追加]** をポイントし、**[新しい項目]** をクリックします。  
15. **[新しい項目の追加]** ダイアログ ボックスで **[データ パイプラインのコピー]** を選択し、**[追加]** をクリックします。 
16. JSON テキストを次の JSON テキストで置き換え、**CopyActivity1.json** ファイルを保存します。
			
		 {
		    "name": "ADFTutorialPipeline",
		    "properties":
		    {   
		        "description" : "Copy data from a blob to Azure SQL table",
		        "activities":   
		        [
		            {
		                "name": "CopyFromBlobToSQL",
		                "description": "Push Regional Effectiveness Campaign data to Azure SQL database",
		                "type": "CopyActivity",
		                "inputs": [ {"name": "EmpTableFromBlob"} ],
		                "outputs": [ {"name": "EmpSQLTable"} ],     
		                "transformation":
		                {
		                    "source":
		                    {                               
		                        "type": "BlobSource"
		                    },
		                    "sink":
		                    {
		                        "type": "SqlSink",
		                        "writeBatchSize": 10000,
		                        "writeBatchTimeout": "60:00:00"
		                    }   
		                },
		                "Policy":
		                {
		                    "concurrency": 1,
		                    "executionPriorityOrder": "NewestFirst",
		                    "style": "StartOfInterval",
		                    "retry": 0,
		                    "timeout": "01:00:00"
		                }       
		            }
		        ],
		
		        "start": "2015-07-12T00:00:00Z",
		        "end": "2015-07-13T00:00:00Z",
		        "isPaused": false
		    }
		} 

#### Data Factory エンティティの発行/デプロイ
  
18. Data Factory ツール バーが有効になっていない場合は、ツール バー領域で **[Data Factory]** を右クリックして選択すると有効になります。 
19. **Data Factory ツール バー**で**ドロップダウン ボックス**をクリックし、Azure サブスクリプション内にすべてのデータ ファクトリを表示します。**[Visual Studio にサインイン]** ダイアログ ボックスが表示された場合。 
	20. データ ファクトリを作成する Azure サブスクリプションに関連付けられている**電子メール アカウント**を入力し、**パスワード**を入力し、**[サインイン]** をクリックします。
	21. サイン インが成功すると、Azure サブスクリプション内のすべてのデータ ファクトリが表示されます。このチュートリアルでは、新しいデータ ファクトリを作成します。       
22. ドロップダウン リストで **[ADFTutorialFactoryVS]** を選択し、**[発行]** ボタンをクリックして、リンクされたサービス、データセット、パイプラインをデプロイ/発行します。    

	![[発行] ボタン](./media/data-factory-get-started-using-vs/publish.png)

23. 上の図に示されている [Data Factory Task List] ウィンドウで発行のステータスを確認する必要があります。発行が成功したことを確認します。

## サーバー エクスプローラーを使用して Data Factory のエンティティを確認する

1. **Visual Studio** のメニューで **[ビュー]** をクリックし、**[サーバー エクスプローラー]** をクリックします。
2. [サーバー エクスプローラー] ウィンドウで、**[Azure]** を展開し、**[Data Factory]** を展開します。**[Visual Studio にサインイン]** が表示されたら、Azure サブスクリプションに関連付けられている**アカウント**を入力して **[続行]** をクリックします。**パスワード**を入力し、**[サインイン]** をクリックします。Visual Studio は、サブスクリプション内のすべての Azure データ ファクトリに関する情報を取得しようとします。**[Data Factory タスク リスト]** ウィンドウで、この操作のステータスを確認できます。![Server Explorer](./media/data-factory-get-started-using-vs/server-explorer.png)
3. データ ファクトリを右クリックし、[データ ファクトリを新しいプロジェクトにエクスポートする] を選択して、既存のデータ ファクトリに基づいて Visual Studio プロジェクトを作成します。![Data Factory の Visual Studio プロジェクトへのエクスポート](./media/data-factory-get-started-using-vs/export-data-factory-menu.png)  

## Visual Studio の Data Factory ツールを更新する
Visual Studio の Azure Data Factory ツールを更新するには、次のように行います。

1. メニューで **[ツール]** をクリックし、**[拡張機能と更新プログラム]** を選択します。 
2. 左ウィンドウで **[更新]** を選択し、**[Visual Studio ギャラリー]** を選択します。
4. **[Visual Studio の Azure Data Factory ツール]** を選択して、**[更新]** をクリックします。このエントリが表示されない場合は、ツールは既に最新バージョンです。 

Azure プレビュー ポータルを使用して、このチュートリアルで作成したパイプラインとデータセットを監視する方法については、[データセットとパイプラインの監視](data-factory-get-started-using-editor.md/#MonitorDataSetsAndPipeline)に関する記事を参照してください。


## フィードバックの送信
この記事に関するフィードバックをお待ちしています。少しのお時間をとって、[電子メール](mailto:adfdocfeedback@microsoft.com?subject=data-factory-get-started-using-vs.md)でフィードバックをお寄せください。

<!---HONumber=September15_HO1-->