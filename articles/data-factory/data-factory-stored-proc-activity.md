<properties 
	pageTitle="SQL Server ストアド プロシージャ アクティビティ" 
	description="SQL Server ストアド プロシージャ アクティビティを使用して、Data Factory パイプラインから Azure SQL データベースでストアド プロシージャを呼び出す方法について説明します。" 
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
	ms.date="08/04/2015" 
	ms.author="spelluru"/>

# SQL Server ストアド プロシージャ アクティビティ

SQL Server ストアド プロシージャ アクティビティを Data Factory の[パイプライン](data-factory-create-pipelines.md)で使用して、**Azure SQL** データベースでストアド プロシージャを呼び出すことができます。この記事は、データ変換とサポートされる変換アクティビティの概要について説明する記事「[データ変換のアクティビティ](data-factory-data-transformation-activities.md)」を基に作成されています。

## 構文
	{
    	"name": "SQLSPROCActivity",
    	"description": "description", 
    	"type": "SqlServerStoredProcedure",
    	"inputs":  [ { "name": "inputtable"  } ],
    	"outputs":  [ { "name": "outputtable" } ],
    	"typeProperties":
    	{
        	"storedProcedureName": “”,
        	"storedProcedureParameters": “” 
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
description | アクティビティの用途を説明するテキストです。 | いいえ
type | SqlServerStoredProcedure | あり
inputs | 続行するストアド プロシージャ アクティビティに使用できる状態 (’Ready’ 状態) である必要がある入力。 | いいえ
outputs | ストアド プロシージャ アクティビティで生成された出力。出力テーブルでは、必ず Azure SQL データベースを Data Factory にリンクするリンク サービスを使用します。 | あり
storedProcedureName | 出力テーブルに使用するリンク サービスで示される Azure SQL データベースのストアド プロシージャ名を指定します。 | あり
storedProcedureParameters | ストアド プロシージャのパラメーター値を指定します | いいえ

> [AZURE.NOTE]ストアド プロシージャ アクティビティへの入力は、依存関係管理と、このアクティビティと他のアクティビティの関連付けにのみ使用されます。入力は、ストアド プロシージャでパラメーターとして使用できません。
 

## 例

Azure SQL データベースで 2 つの列があるテーブルを作成する例について考えてみましょう。

分割 | 型
------ | ----
ID | uniqueidentifier
Datetime | 対応する ID が生成された日付と時刻

![サンプル データ](./media/data-factory-stored-proc-activity/sample-data.png)

	CREATE PROCEDURE sp_sample @DateTime nvarchar(127)
	AS
	
	BEGIN
	    INSERT INTO [sampletable]
	    VALUES (newid(), @DateTime)
	END

Data Factory パイプラインでこのストアド プロシージャを実行するには、以下の手順を実行する必要があります。

1.	[リンク サービス](data-factory-azure-sql-connector.md/#azure-sql-linked-service-properties)を作成して、ストアド プロシージャを実行する Azure SQL データベースの接続文字列を登録します。
2.	Azure SQL データベースの出力テーブルを示す[データセット](data-factory-azure-sql-connector.md/#azure-sql-dataset-type-properties)を作成します。このデータセット sprocsampleout を呼び出してみましょう。このデータセットは、手順 1 のリンク サービスを参照する必要があります。 
3.	Azure SQL データベースにストアド プロシージャを作成します。
4.	SqlServerStoredProcedure アクティビティを使用して次の[パイプライン](data-factory-azure-sql-connector.md/#azure-sql-copy-activity-type-properties)を作成し、Azure SQL データベースのストアド プロシージャを呼び出します。

		{
		    "name": "SprocActivitySamplePipeline",
		    "properties":
		    {
		        "activities":
		        [
		            {
		             "name": "SprocActivitySample",
		             "type": " SqlServerStoredProcedure ",
		             "outputs": [ {"name": "sprocsampleout"} ],
		             "typeproperties":
		              {
		                "storedProcedureName": "sp_sample",
		        		"storedProcedureParameters": 
		        		{
		            	"DateTime": "$$Text.Format('{0:yyyy-MM-dd HH:mm:ss}', SliceStart)"
		        		}
				}
		            }
		          ]
		     }
		}
5.	[パイプライン](data-factory-create-pipelines.md)をデプロイします。
6.	データ ファクトリの監視と管理のビューを使用して、[パイプラインを監視](data-factory-monitor-manage-pipelines.md)します。

> [AZURE.NOTE]上の例では、SprocActivitySample に入力がありません。アップストリームでこれとアクティビティを関連付ける場合、アップストリーム アクティビティの出力をこのアクティビティの入力として使用できます。この場合、このアクティビティは、アップストリーム アクティビティが完了し、出力を使用できる状態 (Ready 状態) になるまで実行されません。入力は、ストアド プロシージャ アクティビティのパラメーターとして直接使用できません。
> 
> JSON ファイルのストアド プロシージャ パラメーターの名前と大文字と小文字は、対象データベースのストアド プロシージャ パラメーターの名前と一致する必要があります。

次に、‘Document sample’ という静的値を含む ‘Scenario’ という別の列をテーブルに追加する例を考えてましょう。

![サンプル データ 2](./media/data-factory-stored-proc-activity/sample-data-2.png)

	CREATE PROCEDURE sp_sample @DateTime nvarchar(127) , @Scenario nvarchar(127)
	
	AS
	
	BEGIN
	    INSERT INTO [sampletable]
	    VALUES (newid(), @DateTime, @Scenario)
	END

この処理を実行するには、ストアド プロシージャ アクティビティから Scenario パラメーターとその値を渡します。上のサンプルの typeproperties セクションは次のようになります。

	"typeproperties":
	{
		"storedProcedureName": "sp_sample",
	    "storedProcedureParameters": 
	    {
	    	"DateTime": "$$Text.Format('{0:yyyy-MM-dd HH:mm:ss}', SliceStart)",
			"Scenario": "Document sample"
		}
	}

<!---HONumber=August15_HO6-->