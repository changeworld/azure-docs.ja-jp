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
	ms.topic="get-started-article" 
	ms.date="11/02/2015" 
	ms.author="spelluru"/>

# チュートリアル: Visual Studio を使用したデータ ファクトリの作成と監視
> [AZURE.SELECTOR]
- [Tutorial Overview](data-factory-get-started.md)
- [Using Data Factory Editor](data-factory-get-started-using-editor.md)
- [Using PowerShell](data-factory-monitor-manage-using-powershell.md)
- [Using Visual Studio](data-factory-get-started-using-vs.md)


##このチュートリアルの内容
このチュートリアルでは、Visual Studio 2013 で次の作業を行います。

1. リンクされた次の 2 つのサービス、**AzureStorageLinkedService1** と **AzureSqlinkedService1** を作成します。AzureStorageLinkedService1 は、Azure ストレージにリンクし、AzureSqlLinkedService1 は、Azure SQL database を **ADFTutorialDataFactoryVS** データ ファクトリにリンクします。パイプラインの入力データは、Azure BLOB ストレージの BLOB コンテナーにあります。また出力データは、Azure SQL Database のテーブルに格納されます。そのため、これら 2 つのデータ ストアをリンクされたサービスとしてデータ ファクトリに追加します。
2. **EmpTableFromBlob** と **EmpSQLTable** の 2 つのデータ ファクトリ テーブルを定義します。これらはデータ ストアに格納されている入力/出力データを表します。EmpTableFromBlob の場合、ソース データを持つ BLOB を含む BLOB コンテナーを指定します。EmpSQLTable の場合は、出力データを格納する SQL テーブルを指定します。また、データの構造や可用性など、他のプロパティも指定します。
3. ADFTutorialDataFactoryVS に **ADFTutorialPipeline** という名前のパイプラインを作成します。このパイプラインには、Azure BLOB から Azure SQL 出力テーブルに入力データをコピーする**コピー アクティビティ**があります。
4. Data Factory を作成し、リンクされたサービス、テーブル、パイプラインをデプロイする。    

## Visual Studio を使用して、Data Factory のエンティティを作成し、デプロイする 

### 前提条件
コンピューターに以下がインストールされている必要があります: 
- Visual Studio 2013
- Azure SDK for Visual Studio 2013 をダウンロードします。[Azure ダウンロード ページ](http://azure.microsoft.com/downloads/) に移動して、**.NET** セクションの**[VS 2013 install]** をクリックします。

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

##### Azure Storage のリンクされたサービスを作成します。

4. ソリューション エクスプローラーの **[Linked Services]** を右クリックして、**[追加]** をポイントし、**[新しい項目]** をクリックします。      
5. **[新しい項目の追加]** ダイアログ ボックスで、一覧から **[Azure Storage Linked Service]** を選択し、**[追加]** をクリックします。 

	![新規のリンクされたサービス](./media/data-factory-get-started-using-vs/new-linked-service-dialog.png)
 
3. **accountname** と **accountkey** を Azure ストレージ アカウントとそのキーで置き換えます。

	![Azure Storage のリンクされたサービス](./media/data-factory-get-started-using-vs/azure-storage-linked-service.png)

4. **AzureStorageLinkedService1.json** ファイルを保存します。

#### Azure SQL のリンクされたサービスを作成します。

5. **[ソリューション エクスプローラー]** で **[Linked Services]** ノードをもう一度右クリックし、**[追加]** をポイントし、**[新しい項目]** をクリックします。 
6. このとき、**[Azure SQL Linked Service]** をクリックし、**[追加]** をクリックします。 
7. **AzureSqlLinkedService1.json ファイル**で、**servername**、**databasename**、**username@servername**、**password** を Azure SQL のサーバー名、データベース、ユーザー アカウント、パスワードで置き換えます。
8.  **AzureSqlLinkedService1.json** ファイルを保存します。 


### 入力テーブルと出力テーブルを作成する
前の手順では、**AzureStorageLinkedService1** と **AzureSqlLinkedService1** というリンクされたサービスを作成し、Azure ストレージ アカウントと Azure SQL Database を **ADFTutorialDataFactory** というデータ ファクトリにリンクしました。この手順では、**EmpTableFromBlob** と **EmpSQLTable** の 2 つのデータ ファクトリ テーブルを定義します。これらは AzureStorageLinkedService1 と AzureSqlLinkedService1 がそれぞれ参照するデータ ストアに格納されている入力/出力データを表します。EmpTableFromBlob の場合、ソース データを持つ BLOB を含む BLOB コンテナーを指定します。EmpSQLTable の場合は、出力データを格納する SQL テーブルを指定します。

#### 入力テーブルの作成

9. **[ソリューション エクスプローラー]** の **[テーブル]** を右クリックし、**[追加]** をポイントし、**[新しい項目]** をクリックします。
10. **[新しい項目の追加]** ダイアログ ボックスで、**[Azure BLOB]** をクリックし、**[追加]** をクリックします。   
10. JSON テキストを次のテキストで置き換え、**AzureBlobLocation1.json** ファイルを保存します。 

		{
		  "name": "EmpTableFromBlob",
		  "properties": {
		    "structure": [
		      {
		        "name": "FirstName",
		        "type": "String"
		      },
		      {
		        "name": "LastName",
		        "type": "String"
		      }
		    ],
		    "type": "AzureBlob",
		    "linkedServiceName": "AzureStorageLinkedService1",
		    "typeProperties": {
		      "folderPath": "adftutorial/",
		      "format": {
		        "type": "TextFormat",
		        "columnDelimiter": ","
		      }
		    },
		    "external": true,
		    "availability": {
		      "frequency": "Hour",
		      "interval": 1
		    }
		  }
		}

#### 出力テーブルの作成

11. **[ソリューション エクスプローラー]** の **[テーブル]** をもう一度右クリックし、**[追加]** をポイントし、**[新しい項目]** をクリックします。
12. **[新しい項目の追加]** ダイアログ ボックスで、**[Azure SQL]** を選択し、**[追加]** をクリックします。 
13. JSON テキストを次の JSON テキストで置き換え、**AzureSqlTableLocation1.json** ファイルを保存します。

		{
		  "name": "EmpSQLTable",
		  "properties": {
		    "structure": [
		      {
		        "name": "FirstName",
		        "type": "String"
		      },
		      {
		        "name": "LastName",
		        "type": "String"
		      }
		    ],
		    "type": "AzureSqlTable",
		    "linkedServiceName": "AzureSqlLinkedService1",
		    "typeProperties": {
		      "tableName": "emp"
		    },
		    "availability": {
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
		  "properties": {
		    "description": "Copy data from a blob to Azure SQL table",
		    "activities": [
		      {
		        "name": "CopyFromBlobToSQL",
		        "description": "Push Regional Effectiveness Campaign data to Azure SQL database",
		        "type": "Copy",
		        "inputs": [
		          {
		            "name": "EmpTableFromBlob"
		          }
		        ],
		        "outputs": [
		          {
		            "name": "EmpSQLTable"
		          }
		        ],
		        "typeProperties": {
		          "source": {
		            "type": "BlobSource"
		          },
		          "sink": {
		            "type": "SqlSink",
		            "writeBatchSize": 10000,
		            "writeBatchTimeout": "60:00:00"
		          }
		        },
		        "Policy": {
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
  
18. ソリューション エクスプローラーでプロジェクトを右クリックし、**[発行]** をクリックします。 
19. **[Microsoft アカウントの新規登録]** ダイアログ ボックスが表示されたら、Azure サブスクリプションを所有するアカウントの資格情報を入力し、**[サインイン]** をクリックします。
20. 次のダイアログ ボックスが表示されます。

	![[発行] ダイアログ ボックス](./media/data-factory-get-started-using-vs/publish.png)

21. Data Factory の構成ページで、次の操作を行います。
	1. **[Data Factory の新規作成]** オプションを選択します。
	2. **[名前]** に「**VSTutorialFactory**」と入力します。  
	
		> [AZURE.NOTE]  
		> Azure Data Factor の名前はグローバルに一意にする必要があります。発行時にデータ ファクトリの名前に関するエラーが発生した場合は、そのデータ ファクトリの名前を変更して (yournameVSTutorialFactory など)、発行し直してください。Data Factory アーティファクトの名前付け規則については、[Data Factory - 名前付け規則](data-factory-naming-rules.md)に関するトピックを参照してください。
		> 
		> データ ファクトリの名前は今後、DNS 名として登録される可能性があるため、一般ユーザーに表示される場合があります。
	3. **[サブスクリプション]** フィールドで適切なサブスクリプションを選択します。 
	4. 作成するデータ ファクトリの**ソース グループ**を選択します。 
	5. データ ファクトリの**リージョン**を選択します。 
	6. **[次へ]** をクリックし、**[項目の発行]** ページに切り替えます。 
23. **[項目の発行]** ページで、すべての Data Factory エンティティが選択されていることを確認し、**[次へ]** をクリックして **[概要]** ページに切り替えます。     
24. 概要を確認し、**[次へ]** をクリックし、デプロイメント プロセスを開始し、**[デプロイ ステータス]** を表示します。
25. **[デプロイ ステータス]** ページに、デプロイメント プロセスのステータスが表示されます。デプロイメントが完了したら、[完了] をクリックします。 


## サーバー エクスプローラーを使用して Data Factory のエンティティを確認する

1. **Visual Studio** のメニューで **[ビュー]** をクリックし、**[サーバー エクスプローラー]** をクリックします。
2. [サーバー エクスプローラー] ウィンドウで、**[Azure]** を展開し、**[Data Factory]** を展開します。**[Visual Studio にサインイン]** が表示されたら、Azure サブスクリプションに関連付けられている**アカウント**を入力して **[続行]** をクリックします。**パスワード**を入力し、**[サインイン]** をクリックします。Visual Studio は、サブスクリプション内のすべての Azure データ ファクトリに関する情報を取得しようとします。**[Data Factory タスク リスト]** ウィンドウで、この操作のステータスを確認します。  
	![Server Explorer](./media/data-factory-get-started-using-vs/server-explorer.png)
3. データ ファクトリを右クリックし、[Data Factory を新しいプロジェクトにエクスポートする] を選択して、既存のデータ ファクトリに基づいて Visual Studio プロジェクトを作成します。  
	![Data Factory の Visual Studio プロジェクトへのエクスポート](./media/data-factory-get-started-using-vs/export-data-factory-menu.png)  

## Visual Studio の Data Factory ツールを更新する
Visual Studio の Azure Data Factory ツールを更新するには、次のように行います。

1. メニューで **[ツール]** をクリックし、**[拡張機能と更新プログラム]** を選択します。 
2. 左ウィンドウで **[更新]** を選択し、**[Visual Studio ギャラリー]** を選択します。
4. **[Visual Studio の Azure Data Factory ツール]** を選択して、**[更新]** をクリックします。このエントリが表示されない場合は、ツールは既に最新バージョンです。 

Azure プレビュー ポータルを使用して、このチュートリアルで作成したパイプラインとデータセットを監視する方法については、「[データセットとパイプラインを監視する](data-factory-get-started-using-editor.md#MonitorDataSetsAndPipeline)」を参照してください。

<!----HONumber=Nov15_HO3-->