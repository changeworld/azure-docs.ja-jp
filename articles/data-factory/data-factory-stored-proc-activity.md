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
	ms.date="08/18/2016" 
	ms.author="spelluru"/>

# SQL Server ストアド プロシージャ アクティビティ

SQL Server ストアド プロシージャ アクティビティを Data Factory の[パイプライン](data-factory-create-pipelines.md)で使用して、次のいずれかのデータ ストアでストアド プロシージャを呼び出すことができます。


- Azure SQL データベース
- Azure SQL Data Warehouse
- エンタープライズ内または Azure VM 内の SQL Server データベース。データベースをホストするコンピューターと同じコンピューター、またはデータベースとのリソースの競合を避けるために別のコンピューター上に Data Management Gateway をインストールする必要があります。Data Management Gateway は、安全かつ管理された方法でオンプレミスのデータ ソースまたは Azure VM でホストされているデータ ソースをクラウド サービスに接続するソフトウェアです。Data Management Gateway の詳細については、[オンプレミスとクラウド間でのデータ移動](data-factory-move-data-between-onprem-and-cloud.md)に関する記事を参照してください。

この記事は、データ変換とサポートされる変換アクティビティの概要を説明する、[データ変換アクティビティ](data-factory-data-transformation-activities.md)に関する記事に基づいています。

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
name | アクティビティの名前 | はい
description | アクティビティの用途を説明するテキストです。 | なし
type | SqlServerStoredProcedure | はい
inputs | 省略可能。入力データセットを指定した場合、ストアド プロシージャ アクティビティの実行に使用できる ("準備完了" 状態である) 必要があります。ストアド プロシージャで入力データセットをパラメーターとして使用することはできません。入力データセットは、ストアド プロシージャ アクティビティを開始する前に、依存関係の確認にのみ使用されます。 | なし
outputs | ストアド プロシージャ アクティビティの出力データセットを指定する必要があります。出力データセットでは、ストアド プロシージャ アクティビティの**スケジュール** (毎時、毎週、毎月など) を指定します。<br/><br/>出力データセットでは、ストアド プロシージャを実行する、Azure SQL Database、Azure SQL Data Warehouse、または SQL Server Database を表す**リンクされたサービス**を使用する必要があります。<br/><br/>出力データセットは、パイプラインの別のアクティビティ ([連鎖するアクティビティ](data-factory-scheduling-and-execution.md#chaining-activities)) による後続処理のために、ストアド プロシージャの結果を渡す 1 つの方法として使用できます。ただし、Data Factory では、ストアド プロシージャの出力をこのデータセットに自動的に書き込むわけではありません。出力データセットが参照する SQL テーブルへの書き込みは、ストアド プロシージャが実行します。<br/><br/>出力データセットに**ダミー データセット**を指定できる場合もあります。ダミー データセットは、ストアド プロシージャ アクティビティを実行するスケジュールの指定にのみ使用されます。 | はい
storedProcedureName | 出力テーブルに使用するリンク サービスで示される Azure SQL データベースまたは Azure SQL Data Warehouse のストアド プロシージャ名を指定します。 | はい
storedProcedureParameters | ストアド プロシージャのパラメーター値を指定します。パラメーターで null を渡す必要がある場合は、構文として "param1": null (すべて小文字) を使用します。このプロパティの使用方法については、次のサンプルをご覧ください。| なし

## サンプルのチュートリアル

### サンプル テーブルとストアド プロシージャ
> [AZURE.NOTE] このサンプルでは、Azure SQL Database を使用しますが、Azure SQL Data Warehouse や SQL Server Database でも同様に機能します。

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

2. **sampletable** にデータを挿入する、次の**ストアド プロシージャ**を作成します。

		CREATE PROCEDURE sp_sample @DateTime nvarchar(127)
		AS
		
		BEGIN
		    INSERT INTO [sampletable]
		    VALUES (newid(), @DateTime)
		END

	> [AZURE.IMPORTANT] パラメーター (この例では DateTime) の**名前**と**大文字小文字の区別**は、パイプライン/アクティビティ JSON に指定されたパラメーターのものと一致する必要があります。ストアド プロシージャ定義で、**@** がパラメーターのプレフィックスとして使用されていることを確認します。
	
### Data Factory を作成する。  
4. [Azure ポータル](https://portal.azure.com/)にログインした後、次の操作を行います。
	1.	左側のメニューの **[新規]** をクリックします。
	2.	**[作成]** ブレードの **[データ分析]** をクリックします。
	3.	**[データ分析]** ブレードの **[Data Factory]** をクリックします。
4.	**[新しいデータ ファクトリ]** ブレードで、[名前] フィールドに「**SProcDF**」と入力します。Azure Data Factory の名前はグローバルで一意となります。ファクトリを正常に作成できるようにするには、データ ファクトリの名前の先頭に自分の名前を付ける必要があります。
3.	リソース グループを作成していない場合は、リソース グループを作成する必要があります。
	1.	**[リソース グループ名]** をクリックします。
	2.	**[リソース グループ]** ブレードで、**[新規リソース グループの作成]** を選択します。
	3.	**[リソース グループの作成]** ブレードで、**[名前]** に「**ADFTutorialResourceGroup**」と入力します。
	4.	**[OK]** をクリックします。
4.	リソース グループを選択したら、データ ファクトリを作成する適切なサブスクリプションを使用していることを確認します。
5.	**[新しい Data Factory]** ブレードで **[作成]** をクリックします。
6.	Azure ポータルの**スタート画面**に、データ ファクトリを作成中であることが示されます。データ ファクトリが正常に作成されると、データ ファクトリの内容を表示するデータ ファクトリ ページが表示されます。

### Azure SQL のリンク サービスを作成する  
データ ファクトリの作成後、Azure SQL Database をデータ ファクトリに関連付ける Azure SQL リンク サービスを作成します。このデータベースには、sampletable テーブルと sp\_sample ストアド プロシージャが含まれます。

7.	**SProcDF** の **[Data Factory]** ブレードで、**[作成とデプロイ]** をクリックして Data Factory エディターを起動します。
2.	コマンド バーの **[新しいデータ ストア]** をクリックし、**[Azure SQL]** を選択します。Azure SQL のリンク サービスを作成するための JSON スクリプトがエディターに表示されます。
4. **servername** を実際の Azure SQL Database サーバーの名前に置き換え、**databasename** をテーブルとストアド プロシージャの作成先となったデータベースに置き換えます。また、**username@servername** をデータベースへのアクセス権を持つユーザー アカウントに置き換え、**password** をユーザー アカウントのパスワードに置き換えます。
5. コマンド バーの **[デプロイ]** をクリックして、リンク サービスをデプロイします。

### 出力データセットの作成
6. コマンド バーの **[新しいデータセット]** をクリックし、**[Azure SQL]** を選択します。
7. 次の JSON スクリプトをコピーして、JSON エディターに貼り付けます。

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
 
9. コマンド バーの **[...] \(省略記号)** をクリックし、**[新しいパイプライン]** をクリックします。
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
         		"start": "2016-08-02T00:00:00Z",
         		"end": "2016-08-02T05:00:00Z",
		        "isPaused": false
		    }
		}

	パラメーターで null を渡す必要がある場合は、構文として "param1": null (すべて小文字) を使用します。
9. ツール バーの **[デプロイ]** をクリックしてパイプラインをデプロイします。

### パイプラインの監視

6. **[X]** をクリックして Data Factory エディターのブレードを閉じ、[Data Factory] ブレードに戻って **[ダイアグラム]** をクリックします。
7. ダイアグラム ビューに、パイプラインの概要と、このチュートリアルで使用するデータセットが表示されます。
8. ダイアグラム ビューで、**sprocsampleout** データセットをダブルクリックします。スライスが準備完了状態で表示されます。スライスは、JSON の開始時刻から終了時刻までの間に 1 時間ごとに生成されるため、5 つのスライスがあります。
10. スライスが**準備完了**状態のときに、Azure SQL Database に対して **select * from sampletable** クエリを実行して、ストアド プロシージャによってデータがテーブルに挿入されていることを確認します。

	![出力データ](./media/data-factory-stored-proc-activity/output.png)

	Azure Data Factory パイプラインの監視の詳細については、[パイプラインの監視](data-factory-monitor-manage-pipelines.md)に関するページを参照してください。

> [AZURE.NOTE] 上の例では、SprocActivitySample に入力がありません。このアクティビティをアップストリームのアクティビティ (つまり、前の処理) と連鎖させる場合は、アップストリーム アクティビティの出力をこのアクティビティの入力として使用できます。この場合、アップストリーム アクティビティが完了し、アップストリーム アクティビティの出力が使用可能 (準備完了状態) になるまで、このアクティビティは実行されません。入力は、ストアド プロシージャ アクティビティのパラメーターとして直接使用できません。

## 静的な値を渡す 
次に、‘Document sample’ という静的値を含む ‘Scenario’ という別の列をテーブルに追加する例を考えてましょう。

![サンプル データ 2](./media/data-factory-stored-proc-activity/sample-data-2.png)

	CREATE PROCEDURE sp_sample @DateTime nvarchar(127) , @Scenario nvarchar(127)
	
	AS
	
	BEGIN
	    INSERT INTO [sampletable]
	    VALUES (newid(), @DateTime, @Scenario)
	END

ストアド プロシージャ アクティビティから Scenario パラメーターとその値を渡します。上記のサンプルの typeProperties セクションは、次のスニペットのようになります。

	"typeProperties":
	{
		"storedProcedureName": "sp_sample",
	    "storedProcedureParameters": 
	    {
	    	"DateTime": "$$Text.Format('{0:yyyy-MM-dd HH:mm:ss}', SliceStart)",
			"Scenario": "Document sample"
		}
	}

<!---HONumber=AcomDC_0824_2016-->