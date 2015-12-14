<properties 
	pageTitle="SQL Server ストアド プロシージャ アクティビティ" 
	description="SQL Server ストアド プロシージャ アクティビティを使用して、Data Factory パイプラインから Azure SQL Database または Azure SQL Data Warehouse でストアド プロシージャを呼び出す方法について説明します。" 
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
	ms.date="10/12/2015" 
	ms.author="spelluru"/>

# SQL Server ストアド プロシージャ アクティビティ

SQL Server ストアド プロシージャ アクティビティを Data Factory の[パイプライン](data-factory-create-pipelines.md)で使用して、**Azure SQL Database** または **Azure SQL Data Warehouse** でストアド プロシージャを呼び出すことができます。この記事は、データ変換とサポートされる変換アクティビティの概要について説明する記事「[データ変換のアクティビティ](data-factory-data-transformation-activities.md)」を基に作成されています。

## 構文
	{
    	"name": "SQLSPROCActivity",
    	"description": "description", 
    	"type": "SqlServerStoredProcedure",
    	"inputs":  [ { "name": "inputtable"  } ],
    	"outputs":  [ { "name": "outputtable" } ],
    	"typeProperties":
    	{
        	"storedProcedureName": "<name of the stored procedure>",
        	"storedProcedureParameters":  
        	{
				"param1": "param1Value"
				…
        	}
    	}
	}

## 構文の詳細

プロパティ | 説明 | 必須
-------- | ----------- | --------
name | アクティビティの名前 | あり
description | アクティビティの用途を説明するテキストです。 | いいえ
type | SqlServerStoredProcedure | あり
inputs | 続行するストアド プロシージャ アクティビティに使用できる状態 (’Ready’ 状態) である必要がある入力データセット。ストアド プロシージャ アクティビティへの入力は、このアクティビティと他のアクティビティを関連付けるときの依存関係管理にのみ使用されます。入力データセットは、ストアド プロシージャでパラメーターとして使用できません。 | いいえ
outputs | ストアド プロシージャ アクティビティで生成された出力データセット。出力テーブルでは、必ず Azure SQL Database または Azure SQL Data Warehouse を Data Factory にリンクするリンク サービスを使用します。ストアド プロシージャ アクティビティの出力は、後続の処理にストアド プロシージャ アクティビティの結果を渡すための手段として、およびこのアクティビティと他のアクティビティを関連付けるときの依存関係管理に使用できます。 | あり
storedProcedureName | 出力テーブルに使用するリンク サービスで示される Azure SQL データベースまたは Azure SQL Data Warehouse のストアド プロシージャ名を指定します。 | あり
storedProcedureParameters | ストアド プロシージャのパラメーター値を指定します | いいえ

## サンプルのチュートリアル

### サンプル テーブルとストアド プロシージャ
1. SQL Server Management Studio などのツールを使って Azure SQL Database に以下の**テーブル**を作成します。datetimestamp 列は、対応する ID が生成された日付と時刻です。 

		CREATE TABLE dbo.sampletable
		(
			Id uniqueidentifier,
			datetimestamp nvarchar(127)
		)
		GO

		CREATE CLUSTERED INDEX ClusteredID ON dbo.sampletable(Id);
		GO

	Id は一意の識別子で、datetimestamp 列は、対応する ID が生成された日時です。 ![サンプル データ](./media/data-factory-stored-proc-activity/sample-data.png)

2. **sampletable** にデータを挿入する**ストアド プロシージャ**を作成します。

		CREATE PROCEDURE sp_sample @DateTime nvarchar(127)
		AS
		
		BEGIN
		    INSERT INTO [sampletable]
		    VALUES (newid(), @DateTime)
		END

	> [AZURE.IMPORTANT]パラメーターの**名前**と**大文字と小文字**は (この例では DateTime) は、パイプライン/アクティビティ JSON に指定されたパラメーターのものと一致する必要があります。ストアド プロシージャ定義で、**@** がパラメーターのプレフィックスとして使用されていることを確認します。
	
### Data Factory を作成する。  
4. [Azure ポータル](http://portal.azure.com/)にログインした後、次の操作を行います。
	1.	左側のメニューの **[新規]** をクリックします。 
	2.	**[作成]** ブレードの **[データ分析]** をクリックします。
	3.	**[データ分析]** ブレードの **[Data Factory]** をクリックします。
4.	**[新しいデータ ファクトリ]** ブレードで、[名前] フィールドに「**SProcDF**」と入力します。Azure Data Factory の名前はグローバルで一意となります。ファクトリを作成するには、データ ファクトリの名前の先頭にあなたの名前を付ける必要があります。 
3.	リソース グループを作成していない場合は、リソース グループを作成する必要があります。これを行うには、次の手順を実行します。
	1.	**[リソース グループ名]** をクリックします。
	2.	**[リソース グループ]** ブレードで、**[新規リソース グループの作成]** を選択します。
	3.	**[リソース グループの作成]** ブレードで、**[名前]** に「**ADFTutorialResourceGroup**」と入力します。
	4.	**[OK]** をクリックします。
4.	リソース グループを選択した後、データ ファクトリを作成する正しいサブスクリプションを使用していることを確認します。
5.	**[新しいデータ ファクトリ]** ブレードで **[作成]** をクリックします。
6.	Azure ポータルの**スタート画面**にデータ ファクトリを作成中であることが示されます。データ ファクトリが正常に作成されると、データ ファクトリの内容を表示するデータ ファクトリ ページが表示されます。

### Azure SQL のリンク サービスを作成する  
データ ファクトリの作成後、Azure SQL Database をデータ ファクトリに関連付ける Azure SQL リンク サービスを作成します。これは、sampletable テーブルと sp\_sample ストアド プロシージャを含んだデータベースです。

7.	**[SProcDF]** の **[DATA FACTORY]** ブレードの **[作成とデプロイ]** をクリックします。Data Factory エディタが起動します。 
2.	コマンド バーの **[新しいデータ ストア]** をクリックし、**[Azure SQL]** を選択します。Azure SQL のリンク サービスを作成するための JSON スクリプトがエディターに表示されます。 
4. **servername** に、ご使用の Azure SQL Database サーバーの名前を指定します。**databasename** には、テーブルとストアド プロシージャの作成先となったデータベースを指定します。****username@servername** には、そのデータベースへのアクセス権を持ったユーザー アカウントを、**password** には、そのユーザー アカウントのパスワードを指定してください。
5. コマンド バーの **[デプロイ]** をクリックして、リンク サービスをデプロイします。

### 出力データセットの作成
6. コマンド バーの **[新しいデータセット]** をクリックし、**[Azure SQL]** を選択します。
7. 次の JSON スクリプトをコピーして JSON エディターに貼り付けます。

		{			    
			"name": "sprocsampleout",
			"properties": {
				"type": "AzureSqlTable",
				"linkedServiceName": "AzureSqlLinkedService",
				"typeProperties": {
					"tableName": "sampletable"
				},
				"availability": {
					"frequency": "Hour",
					"interval": 1
				}
			}
		}
7. コマンド バーの **[デプロイ]** をクリックしてデータセットをデプロイします。 

### SqlServerStoredProcedure アクティビティでパイプラインを作成する
今度は、SqlServerStoredProcedure アクティビティでパイプラインを作成しましょう。
 
9. コマンド バーの **[...] (省略記号)** をクリックし、**[新しいパイプライン]** をクリックします。 
9. 次の JSON スニペットをコピーして貼り付けます。**storedProcedureName** は **sp\_sample** に設定します。パラメーター **DateTime** の名前は、大文字と小文字の区別も含め、ストアド プロシージャの定義と一致させる必要があります。  

		{
		    "name": "SprocActivitySamplePipeline",
		    "properties": {
		        "activities": [
		            {
		                "type": "SqlServerStoredProcedure",
		                "typeProperties": {
		                    "storedProcedureName": "sp_sample",
		                    "storedProcedureParameters": {
		                        "DateTime": "$$Text.Format('{0:yyyy-MM-dd HH:mm:ss}', SliceStart)"
		                    }
		                },
		                "outputs": [
		                    {
		                        "name": "sprocsampleout"
		                    }
		                ],
		                "scheduler": {
		                    "frequency": "Hour",
		                    "interval": 1
		                },
		                "name": "SprocActivitySample"
		            }
		        ],
		        "start": "2015-01-02T00:00:00Z",
		        "end": "2015-01-03T00:00:00Z",
		        "isPaused": false
		    }
		}
9. ツール バーの **[デプロイ]** をクリックしてパイプラインをデプロイします。  

### パイプラインの監視

6. **[X]** をクリックして Data Factory エディターのブレードを閉じ、[Data Factory] ブレードに戻って **[ダイアグラム]** をクリックします。
7. ダイアグラム ビューに、パイプラインの概要と、このチュートリアルで使用されるデータセットが表示されます。 
8. ダイアグラム ビューで、**sprocsampleout** データセットをダブルクリックします。スライスが準備完了状態として表示されます。スライスは 2015/01/02 から 2015/01/03 まで毎時生成されるため、スライス数は 24 となります。 
10. スライスが **[準備完了]** 状態のときに、Azure SQL Database に対して **select * from sampledata** クエリを実行し、ストアド プロシージャによってデータがテーブルに挿入されたことを確認します。

	![出力データ](./media/data-factory-stored-proc-activity/output.png)

	Azure Data Factory パイプラインの監視の詳細については、[パイプラインの監視](data-factory-monitor-manage-pipelines.md)に関するページを参照してください。

> [AZURE.NOTE]上の例では、SprocActivitySample に入力がありません。アップストリームのアクティビティ (つまり前の処理) とこれを関連付ける場合、アップストリーム アクティビティの出力をこのアクティビティの入力として使用できます。この場合、このアクティビティは、アップストリーム アクティビティが完了し、アップストリーム アクティビティの出力を使用できる状態 (Ready 状態) になるまで実行されません。入力は、ストアド プロシージャ アクティビティのパラメーターとして直接使用できません。

## 静的な値を渡す 
次に、‘Document sample’ という静的値を含む ‘Scenario’ という別の列をテーブルに追加する例を考えてましょう。

![サンプル データ 2](./media/data-factory-stored-proc-activity/sample-data-2.png)

	CREATE PROCEDURE sp_sample @DateTime nvarchar(127) , @Scenario nvarchar(127)
	
	AS
	
	BEGIN
	    INSERT INTO [sampletable]
	    VALUES (newid(), @DateTime, @Scenario)
	END

この処理を実行するには、ストアド プロシージャ アクティビティから Scenario パラメーターとその値を渡します。上のサンプルの typeProperties セクションは次のようになります。

	"typeProperties":
	{
		"storedProcedureName": "sp_sample",
	    "storedProcedureParameters": 
	    {
	    	"DateTime": "$$Text.Format('{0:yyyy-MM-dd HH:mm:ss}', SliceStart)",
			"Scenario": "Document sample"
		}
	}

<!---HONumber=AcomDC_1203_2015-->