---
title: "Azure Data Factory を使用した SSIS パッケージの呼び出し - ストアド プロシージャ アクティビティ | Microsoft Docs"
description: "この記事では、ストアド プロシージャ アクティビティを使用して SQL Server Integration Services (SSIS) パッケージを Azure Data Factory パイプラインから呼び出す方法を説明します。"
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: 
ms.devlang: powershell
ms.topic: article
ms.date: 12/07/2017
ms.author: jingwang
ms.openlocfilehash: 713e9ad7a76c15cbde912954e00991a80b995683
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/02/2018
---
# <a name="invoke-an-ssis-package-using-stored-procedure-activity-in-azure-data-factory"></a>Azure Data Factory のストアド プロシージャ アクティビティを使用して SSIS パッケージを呼び出す
この記事では、ストアド プロシージャ アクティビティを使用して SSIS パッケージを Azure Data Factory パイプラインから呼び出す方法を説明します。 

> [!NOTE]
> この記事は、現在プレビュー段階にある Data Factory のバージョン 2 に適用されます。 一般公開 (GA) されている Data Factory サービスのバージョン 1 を使用している場合は、[バージョン 1 でストアド プロシージャ アクティビティを使用して SSIS パッケージを呼び出す方法](v1/how-to-invoke-ssis-package-stored-procedure-activity.md)に関するページを参照してください。

## <a name="prerequisites"></a>前提条件

### <a name="azure-sql-database"></a>の接続文字列 
この記事のチュートリアルでは、SSIS カタログをホストする Azure SQL データベースを使用します。 Azure SQL マネージ インスタンス (プライベート プレビュー) を使用することもできます。

## <a name="create-an-azure-ssis-integration-runtime"></a>Azure-SSIS 統合ランタイムを作成します
Azure-SSIS 統合ランタイムがない場合は、[チュートリアル: SSIS パッケージのデプロイ](tutorial-deploy-ssis-packages-azure.md)の手順に従って作成します。

### <a name="azure-powershell"></a>Azure PowerShell
[Azure PowerShell のインストールと構成の方法](/powershell/azure/install-azurerm-ps)に関するページの手順に従って、最新の Azure PowerShell モジュールをインストールしてください。 

## <a name="create-a-data-factory"></a>Data Factory を作成する。
Azure-SSIS IR と同じデータ ファクトリを使用することも、別のデータ ファクトリを作成することもできます。 次の手順では、データ ファクトリを作成する方法を説明します。 このデータ ファクトリにストアド プロシージャ アクティビティを含むパイプラインを作成します。 ストアド プロシージャ アクティビティが SSISDB データベース内でストアド プロシージャを実行して、SSIS パッケージを実行します。 

1. 後で PowerShell コマンドで使用できるように、リソース グループ名の変数を定義します。 次のコマンド テキストを PowerShell にコピーし、[Azure リソース グループ](../azure-resource-manager/resource-group-overview.md)の名前を二重引用符で囲んで指定し、コマンドを実行します。 たとえば、「 `"adfrg"`」のように入力します。 
   
     ```powershell
    $resourceGroupName = "ADFTutorialResourceGroup";
    ```

    リソース グループが既に存在する場合、上書きしないようお勧めします。 `$ResourceGroupName` 変数に別の値を割り当てて、コマンドをもう一度実行します。
2. Azure リソース グループを作成するには、次のコマンドを実行します。 

    ```powershell
    $ResGrp = New-AzureRmResourceGroup $resourceGroupName -location 'eastus'
    ``` 
    リソース グループが既に存在する場合、上書きしないようお勧めします。 `$ResourceGroupName` 変数に別の値を割り当てて、コマンドをもう一度実行します。 
3. データ ファクトリ名の変数を定義します。 

    > [!IMPORTANT]
    >  データ ファクトリ名は、グローバルに一意となるように更新してください。 

    ```powershell
    $DataFactoryName = "ADFTutorialFactory";
    ```

5. データ ファクトリを作成するには、$ResGrp 変数の Location および ResourceGroupName プロパティを使用して、次の **Set-AzureRmDataFactoryV2** コマンドレットを実行します。 
    
    ```powershell       
    $DataFactory = Set-AzureRmDataFactoryV2 -ResourceGroupName $ResGrp.ResourceGroupName -Location $ResGrp.Location -Name $dataFactoryName 
    ```

以下の点に注意してください。

* Azure Data Factory の名前はグローバルに一意にする必要があります。 次のエラーが発生した場合は、名前を変更してからもう一度実行してください。

    ```
    The specified Data Factory name 'ADFv2QuickStartDataFactory' is already in use. Data Factory names must be globally unique.
    ```
* Data Factory インスタンスを作成するには、Azure へのログインに使用するユーザー アカウントが、**共同作成者**または**所有者**ロールのメンバーであるか、Azure サブスクリプションの**管理者**である必要があります。
* 現在、Data Factory バージョン 2 でデータ ファクトリを作成できるリージョンは、米国東部、米国東部 2、および西ヨーロッパだけです。 データ ファクトリで使用するデータ ストア (Azure Storage、Azure SQL Database など) やコンピューティング (HDInsight など) は他のリージョンに配置できます。

### <a name="create-an-azure-sql-database-linked-service"></a>Azure SQL Database のリンクされたサービスを作成する
リンクされたサービスを作成して、SSIS カタログをホストする Azure SQL データベースをデータ ファクトリにリンクします。 このリンクされたサービスの情報をデータ ファクトリが使用して、SSISDB データベースに接続し、ストアド プロシージャを実行して SSIS パッケージを実行します。 

1. 次の内容を記述した **AzureSqlDatabaseLinkedService.json** という名前の JSON ファイルを **C:\ADF\RunSSISPackage** フォルダーに作成します。 

    > [!IMPORTANT]
    > &lt;servername&gt;、&lt;username&gt;、&lt;password&gt; を実際の Azure SQL データベースの値に置き換えてから、ファイルを保存してください。

    ```json
    {
        "name": "AzureSqlDbLinkedService",
        "properties": {
            "type": "AzureSqlDatabase",
            "typeProperties": {
                "connectionString": {
                    "type": "SecureString",
                    "value": "Server=tcp:<servername>.database.windows.net,1433;Database=SSISDB;User ID=<username>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
                }
            }
        }
    }
    ```

2. **Azure PowerShell** で **C:\ADF\RunSSISPackage** フォルダーに切り替えます。

3. **Set-AzureRmDataFactoryV2LinkedService** コマンドレットを実行して、リンクされたサービス **AzureSqlDatabaseLinkedService** を作成します。 

    ```powershell
    Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -Name "AzureSqlDatabaseLinkedService" -File ".\AzureSqlDatabaseLinkedService.json"
    ```

## <a name="create-a-pipeline-with-stored-procedure-activity"></a>ストアド プロシージャ アクティビティを含むパイプラインを作成する 
この手順では、ストアド プロシージャ アクティビティを含むパイプラインを作成します。 このアクティビティは、SSIS パッケージを実行する sp_executesql ストアド プロシージャを呼び出します。 

1. 次の内容を記述した **RunSSISPackagePipeline.json** という名前の JSON ファイルを **C:\ADF\RunSSISPackage** フォルダーに作成します。

    > [!IMPORTANT]
    > &lt;FOLDER NAME&gt;、&lt;PROJECT NAME&gt;、&lt;PACKAGE NAME&gt; を、SSIS カタログのフォルダー、プロジェクト、パッケージの名前で置き換えてから、ファイルを保存してください。 

    ```json
    {
        "name": "RunSSISPackagePipeline",
        "properties": {
            "activities": [
                {
                    "name": "My SProc Activity",
                    "description":"Runs an SSIS package",
                    "type": "SqlServerStoredProcedure",
                    "linkedServiceName": {
                        "referenceName": "AzureSqlDbLinkedService",
                        "type": "LinkedServiceReference"
                    },
                    "typeProperties": {
                        "storedProcedureName": "sp_executesql",
                        "storedProcedureParameters": {
                            "stmt": {
                                "value": "DECLARE @return_value INT, @exe_id BIGINT, @err_msg NVARCHAR(150)    EXEC @return_value=[SSISDB].[catalog].[create_execution] @folder_name=N'<FOLDER NAME>', @project_name=N'<PROJECT NAME>', @package_name=N'<PACKAGE NAME>', @use32bitruntime=0, @runinscaleout=1, @useanyworker=1, @execution_id=@exe_id OUTPUT    EXEC [SSISDB].[catalog].[set_execution_parameter_value] @exe_id, @object_type=50, @parameter_name=N'SYNCHRONIZED', @parameter_value=1    EXEC [SSISDB].[catalog].[start_execution] @execution_id=@exe_id, @retry_count=0    IF(SELECT [status] FROM [SSISDB].[catalog].[executions] WHERE execution_id=@exe_id)<>7 BEGIN SET @err_msg=N'Your package execution did not succeed for execution ID: ' + CAST(@exe_id AS NVARCHAR(20)) RAISERROR(@err_msg,15,1) END"
                            }
                        }
                    }
                }
            ]
        }
    }
    ```

2. パイプライン **RunSSISPackagePipeline** を作成するには、**Set-AzureRmDataFactoryV2Pipeline** コマンドレットを実行します。

    ```powershell
    $DFPipeLine = Set-AzureRmDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -Name "RunSSISPackagePipeline" -DefinitionFile ".\RunSSISPackagePipeline.json"
    ```

    出力例を次に示します。

    ```
    PipelineName      : Adfv2QuickStartPipeline
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Activities        : {CopyFromBlobToBlob}
    Parameters        : {[inputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification], [outputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification]}
    ```

## <a name="create-a-pipeline-run"></a>パイプラインの実行を作成する
**Invoke-AzureRmDataFactoryV2Pipeline** コマンドレットを使ってパイプラインを実行します。 コマンドレットは、将来の監視のために、パイプラインの実行 ID を返します。

```powershell
$RunId = Invoke-AzureRmDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -PipelineName $DFPipeLine.Name
```

## <a name="monitor-the-pipeline-run"></a>パイプラインの実行を監視します

次の PowerShell スクリプトを実行し、データのコピーが完了するまで、パイプラインの実行の状態を継続的にチェックします。 次のスクリプトをコピーして PowerShell ウィンドウに貼り付け、Enter キーを押します。 

```powershell
while ($True) {
    $Run = Get-AzureRmDataFactoryV2PipelineRun -ResourceGroupName $ResGrp.ResourceGroupName -DataFactoryName $DataFactory.DataFactoryName -PipelineRunId $RunId

    if ($Run) {
        if ($run.Status -ne 'InProgress') {
            Write-Output ("Pipeline run finished. The status is: " +  $Run.Status)
            $Run
            break
        }
        Write-Output  "Pipeline is running...status: InProgress"
    }

    Start-Sleep -Seconds 10
}   
```

## <a name="create-a-trigger"></a>トリガーを作成する
前の手順ではオンデマンドでパイプラインを呼び出しました。 スケジュール トリガーを作成して、スケジュール (毎時、毎日など) によってパイプラインをトリガーすることもできます。

1. 以下の内容を記述した **MyTrigger.json** という名前の JSON ファイルを **C:\ADF\RunSSISPackage** フォルダー内に作成します。 

    ```json
    {
        "properties": {
            "name": "MyTrigger",
            "type": "ScheduleTrigger",
            "typeProperties": {
                "recurrence": {
                    "frequency": "Hour",
                    "interval": 1,
                    "startTime": "2017-12-07T00:00:00-08:00",
                    "endTime": "2017-12-08T00:00:00-08:00"
                }
            },
            "pipelines": [{
                    "pipelineReference": {
                        "type": "PipelineReference",
                        "referenceName": "RunSSISPackagePipeline"
                    },
                    "parameters": {}
                }
            ]
        }
    }    
    ```
2. **Azure PowerShell** で **C:\ADF\RunSSISPackage** フォルダーに切り替えます。
3. **Set-AzureRmDataFactoryV2Trigger** コマンドレットを実行してトリガーを作成します。 

    ```powershell
    Set-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName -DataFactoryName $DataFactory.DataFactoryName -Name "MyTrigger" -DefinitionFile ".\MyTrigger.json"
    ```
4. 既定ではトリガーは停止状態になっています。 **Start-AzureRmDataFactoryV2Trigger** コマンドレットを実行してトリガーを起動します。 

    ```powershell
    Start-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName -DataFactoryName $DataFactory.DataFactoryName -Name "MyTrigger" 
    ```
5. **Get-AzureRmDataFactoryV2Trigger** コマンドレットを実行して、トリガーの状態が "Started" であることを確認します。 

    ```powershell
    Get-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"     
    ```    
6. 時刻の時間部分が次の時間になってから、次のコマンドを実行します。 たとえば、現在時刻が午後 3 時 25 分 (UTC) であれば、午後 4 時 (UTC) にコマンドを実行します。 
    
    ```powershell
    Get-AzureRmDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -TriggerName "MyTrigger" -TriggerRunStartedAfter "2017-12-06" -TriggerRunStartedBefore "2017-12-09"
    ```

    次のクエリを Azure SQL サーバーの SSISDB データベースに対して実行すると、パッケージが実行されたことを確認できます。 

    ```sql
    select * from catalog.executions
    ```

## <a name="next-steps"></a>次の手順
Azure Portal を使用してパイプラインを監視することもできます。 具体的な手順については、「[パイプラインの監視](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline)」を参照してください。
