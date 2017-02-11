---
title: "SQL Server ストアド プロシージャ アクティビティ"
description: "SQL Server ストアド プロシージャ アクティビティを使用して、Data Factory パイプラインから Azure SQL Database または Azure SQL Data Warehouse でストアド プロシージャを呼び出す方法について説明します。"
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: 1c46ed69-4049-44ec-9b46-e90e964a4a8e
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/30/2016
ms.author: spelluru
translationtype: Human Translation
ms.sourcegitcommit: 1b2514e1e6f39bb3ce9d8a46f4af01835284cdcc
ms.openlocfilehash: 3510b0446cf3c1a7ffb3ff02a4d84d24ead1cae7


---
# <a name="sql-server-stored-procedure-activity"></a>SQL Server ストアド プロシージャ アクティビティ
> [!div class="op_single_selector"]
> [Hive](data-factory-hive-activity.md)  
> [Pig](data-factory-pig-activity.md)  
> [MapReduce](data-factory-map-reduce.md)  
> [Hadoop Streaming](data-factory-hadoop-streaming-activity.md)
> [Machine Learning](data-factory-azure-ml-batch-execution-activity.md)
> [ストアド プロシージャ](data-factory-stored-proc-activity.md)
> [Data Lake Analytics U-SQL](data-factory-usql-activity.md)
> [.NET カスタム](data-factory-use-custom-activities.md)
>
>

SQL Server ストアド プロシージャ アクティビティを Data Factory の[パイプライン](data-factory-create-pipelines.md)で使用して、次のいずれかのデータ ストアでストアド プロシージャを呼び出すことができます。

* Azure SQL Database
* Azure SQL Data Warehouse  
* エンタープライズ内または Azure VM 内の SQL Server データベース。 データベースをホストするコンピューターと同じコンピューター、またはデータベースとのリソースの競合を避けるために別のコンピューター上に Data Management Gateway をインストールする必要があります。 Data Management Gateway は、安全かつ管理された方法でオンプレミスのデータ ソースまたは Azure VM でホストされているデータ ソースをクラウド サービスに接続するソフトウェアです。 Data Management Gateway の詳細については、 [オンプレミスとクラウド間でのデータ移動](data-factory-move-data-between-onprem-and-cloud.md) に関する記事を参照してください。

この記事は、データ変換とサポートされる変換アクティビティの概要を説明する、 [データ変換アクティビティ](data-factory-data-transformation-activities.md) に関する記事に基づいています。

## <a name="walkthrough"></a>チュートリアル
### <a name="sample-table-and-stored-procedure"></a>サンプル テーブルとストアド プロシージャ
1. SQL Server Management Studio などのツールを使って Azure SQL Database に以下の **テーブル** を作成します。 datetimestamp 列は、対応する ID が生成された日付と時刻です。

        CREATE TABLE dbo.sampletable
        (
            Id uniqueidentifier,
            datetimestamp nvarchar(127)
        )
        GO

        CREATE CLUSTERED INDEX ClusteredID ON dbo.sampletable(Id);
        GO

    Id は一意の識別子で、datetimestamp 列は、対応する ID が生成された日時です。
    ![サンプル データ](./media/data-factory-stored-proc-activity/sample-data.png)

   > [!NOTE]
   > このサンプルでは、Azure SQL Database を使用しますが、Azure SQL Data Warehouse や SQL Server Database でも同様に機能します。
   >
   >
2. **sampletable** にデータを挿入する、次の**ストアド プロシージャ**を作成します。

        CREATE PROCEDURE sp_sample @DateTime nvarchar(127)
        AS

        BEGIN
            INSERT INTO [sampletable]
            VALUES (newid(), @DateTime)
        END

   > [!IMPORTANT]
   > パラメーター (この例では DateTime) の**名前**と**大文字小文字の区別**は、パイプライン/アクティビティ JSON に指定されたパラメーターのものと一致する必要があります。 ストアド プロシージャ定義で、 **@** がパラメーターのプレフィックスとして使用されていることを確認します。
   >
   >

### <a name="create-a-data-factory"></a>Data Factory を作成する。
1. [Azure Portal](https://portal.azure.com/) にログインします。
2. 左側のメニューの **[新規]** をクリックし、**[インテリジェンス + 分析]**、**[Data Factory]** の順にクリックします。

    ![新しいデータ ファクトリ](media/data-factory-stored-proc-activity/new-data-factory.png)    
3. **[新しいデータ ファクトリ]** ブレードで、[名前] フィールドに「**SProcDF**」と入力します。 Azure Data Factory の名前は**グローバルで一意**となります。 ファクトリを正常に作成できるようにするには、データ ファクトリの名前の先頭に自分の名前を付ける必要があります。

   ![新しいデータ ファクトリ](media/data-factory-stored-proc-activity/new-data-factory-blade.png)         
4. **Azure サブスクリプション**を選択します。
5. **リソース グループ**について、次の手順のいずれかを行います。
   1. **[新規作成]** をクリックし、リソース グループの名前を入力します。
   2. **[既存のものを使用]** を選択し、既存のリソース グループを選択します。  
6. データ ファクトリの **場所** を選択します。
7. 次回ログオンしたときにデータ ファクトリがダッシュボードに表示されるようにするために、**[ダッシュボードにピン留めする]** を選択します。
8. **[新しい Data Factory]** ブレードで **[作成]** をクリックします。
9. 作成したデータ ファクトリは、Azure Portal の**ダッシュボード**に表示されます。 データ ファクトリが正常に作成されると、データ ファクトリの内容を表示するデータ ファクトリ ページが表示されます。
   ![データ ファクトリのホーム ページ](media/data-factory-stored-proc-activity/data-factory-home-page.png)

### <a name="create-an-azure-sql-linked-service"></a>Azure SQL のリンク サービスを作成する
データ ファクトリの作成後、Azure SQL Database をデータ ファクトリに関連付ける Azure SQL リンク サービスを作成します。 このデータベースには、sampletable テーブルと sp_sample ストアド プロシージャが含まれます。

1. **SProcDF** の **[Data Factory]** ブレードで、**[作成およびデプロイ]** をクリックして Data Factory エディターを起動します。
2. コマンド バーの **[新しいデータ ストア]** をクリックし、**[Azure SQL Database]** を選択します。 Azure SQL のリンク サービスを作成するための JSON スクリプトがエディターに表示されます。

   ![新しいデータ ストア](media/data-factory-stored-proc-activity/new-data-store.png)
3. JSON スクリプトを次のように変更します。

   1. **&lt;servername&gt;** を、SQL Database サーバーの名前に置き換えます。
   2. **&lt;databasename&gt;** を、テーブルとストアド プロシージャを作成したデータベースの名前に置き換えます。
   3. **&lt;username@servername&gt;** を、データベースへのアクセス権を持つユーザー アカウントに置き換えます。
   4. **&lt;password&gt;** を、ユーザー アカウントのパスワードに置き換えます。

      ![新しいデータ ストア](media/data-factory-stored-proc-activity/azure-sql-linked-service.png)
4. コマンド バーの **[デプロイ]** をクリックして、リンク サービスをデプロイします。 AzureSqlLinkedService が左側のツリー ビューに表示されることを確認します。

    ![リンクされたサービスを表示しているツリー ビュー](media/data-factory-stored-proc-activity/tree-view.png)

### <a name="create-an-output-dataset"></a>出力データセットの作成
1. **[...More (その他)]** (ツール バー上) をクリックし、**[新しいデータセット]**、**[Azure SQL]** の順にクリックします。 コマンド バーの **[新しいデータセット]** をクリックし、**[Azure SQL]** を選択します。

    ![リンクされたサービスを表示しているツリー ビュー](media/data-factory-stored-proc-activity/new-dataset.png)
2. 次の JSON スクリプトをコピーして、JSON エディターに貼り付けます。

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
3. コマンド バーの **[デプロイ]** をクリックしてデータセットをデプロイします。 ツリー ビューにデータセットが表示されることを確認します。

    ![リンクされたサービスを表示しているツリー ビュー](media/data-factory-stored-proc-activity/tree-view-2.png)

### <a name="create-a-pipeline-with-sqlserverstoredprocedure-activity"></a>SqlServerStoredProcedure アクティビティでパイプラインを作成する
今度は、SqlServerStoredProcedure アクティビティでパイプラインを作成しましょう。

1. **[...More (その他)]** (コマンド バー上) をクリックし、**[新しいパイプライン]** をクリックします。
2. 次の JSON スニペットをコピーして貼り付けます。 **storedProcedureName** は **sp_sample** に設定します。 パラメーター **DateTime** の名前は、大文字と小文字の区別も含め、ストアド プロシージャの定義と一致させる必要があります。  

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
3. ツール バーの **[デプロイ]** をクリックしてパイプラインをデプロイします。  

### <a name="monitor-the-pipeline"></a>パイプラインの監視
1. **[X]** をクリックして Data Factory エディターのブレードを閉じ、[Data Factory] ブレードに戻って **[ダイアグラム]** をクリックします。

    ![[ダイアグラム] タイル](media/data-factory-stored-proc-activity/data-factory-diagram-tile.png)
2. **ダイアグラム ビュー**に、パイプラインの概要と、このチュートリアルで使用するデータセットが表示されます。

    ![[ダイアグラム] タイル](media/data-factory-stored-proc-activity/data-factory-diagram-view.png)
3. ダイアグラム ビューで、 **sprocsampleout**データセットをダブルクリックします。 スライスが準備完了状態で表示されます。 スライスは、JSON の開始時刻から終了時刻までの間に 1 時間ごとに生成されるため、5 つのスライスがあります。

    ![[ダイアグラム] タイル](media/data-factory-stored-proc-activity/data-factory-slices.png)
4. スライスが**準備完了**状態のときに、Azure SQL Database に対して **select * from sampletable** クエリを実行して、ストアド プロシージャによってデータがテーブルに挿入されていることを確認します。

   ![出力データ](./media/data-factory-stored-proc-activity/output.png)

   Azure Data Factory パイプラインの監視の詳細については、 [パイプラインの監視](data-factory-monitor-manage-pipelines.md) に関するページを参照してください。  

> [!NOTE]
> この例では、SprocActivitySample に入力がありません。 このアクティビティをアップストリームのアクティビティ (つまり、前の処理) と連鎖させる場合は、アップストリーム アクティビティの出力をこのアクティビティの入力として使用できます。 この場合、アップストリーム アクティビティが完了し、アップストリーム アクティビティの出力が使用可能 (準備完了状態) になるまで、このアクティビティは実行されません。 入力は、ストアド プロシージャ アクティビティのパラメーターとして直接使用できません。
>
>

## <a name="json-format"></a>JSON 形式
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

## <a name="json-properties"></a>JSON のプロパティ
| プロパティ | 説明 | 必須 |
| --- | --- | --- |
| name |アクティビティの名前 |はい |
| 説明 |アクティビティの用途を説明するテキストです。 |なし |
| type |SqlServerStoredProcedure |はい |
| inputs |省略可能。 入力データセットを指定した場合、ストアド プロシージャ アクティビティの実行に使用できる ("準備完了" 状態である) 必要があります。 ストアド プロシージャで入力データセットをパラメーターとして使用することはできません。 入力データセットは、ストアド プロシージャ アクティビティを開始する前に、依存関係の確認にのみ使用されます。 |なし |
| outputs |ストアド プロシージャ アクティビティの出力データセットを指定する必要があります。 出力データセットでは、ストアド プロシージャ アクティビティの**スケジュール** (毎時、毎週、毎月など) を指定します。 <br/><br/>出力データセットでは、ストアド プロシージャを実行する、Azure SQL Database、Azure SQL Data Warehouse、または SQL Server Database を表す**リンクされたサービス**を使用する必要があります。 <br/><br/>出力データセットは、パイプラインの別のアクティビティ ([連鎖するアクティビティ](data-factory-scheduling-and-execution.md#run-activities-in-a-sequence)) による後続処理のために、ストアド プロシージャの結果を渡す 1 つの方法として使用できます。 ただし、Data Factory では、ストアド プロシージャの出力をこのデータセットに自動的に書き込むわけではありません。 出力データセットが参照する SQL テーブルへの書き込みは、ストアド プロシージャが実行します。 <br/><br/>出力データセットに**ダミー データセット**を指定できる場合もあります。ダミー データセットは、ストアド プロシージャ アクティビティを実行するスケジュールの指定にのみ使用されます。 |あり |
| storedProcedureName |出力テーブルに使用するリンク サービスで示される Azure SQL データベースまたは Azure SQL Data Warehouse のストアド プロシージャ名を指定します。 |はい |
| storedProcedureParameters |ストアド プロシージャのパラメーター値を指定します。 パラメーターで null を渡す必要がある場合は、構文として "param1": null (すべて小文字) を使用します。 このプロパティの使用方法については、次のサンプルをご覧ください。 |なし |

## <a name="passing-a-static-value"></a>静的な値を渡す
次に、‘Document sample’ という静的値を含む ‘Scenario’ という別の列をテーブルに追加する例を考えてましょう。

![サンプル データ 2](./media/data-factory-stored-proc-activity/sample-data-2.png)

    CREATE PROCEDURE sp_sample @DateTime nvarchar(127) , @Scenario nvarchar(127)

    AS

    BEGIN
        INSERT INTO [sampletable]
        VALUES (newid(), @DateTime, @Scenario)
    END

ストアド プロシージャ アクティビティから Scenario パラメーターとその値を渡します。 前のサンプルの typeProperties セクションは、次のスニペットのようになります。

    "typeProperties":
    {
        "storedProcedureName": "sp_sample",
        "storedProcedureParameters":
        {
            "DateTime": "$$Text.Format('{0:yyyy-MM-dd HH:mm:ss}', SliceStart)",
            "Scenario": "Document sample"
        }
    }



<!--HONumber=Nov16_HO3-->


