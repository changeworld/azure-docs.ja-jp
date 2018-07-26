---
title: SSIS パッケージの実行アクティビティを使用した SSIS パッケージの実行 - Azure | Microsoft Docs
description: この記事では、SSIS パッケージの実行アクティビティを使用して、SQL Server Integration Services (SSIS) パッケージを Azure Data Factory パイプラインで実行する方法を説明します。
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.date: 07/16/2018
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 3a43c0cd13300918979ae03c7f6c703796b65dc9
ms.sourcegitcommit: 7827d434ae8e904af9b573fb7c4f4799137f9d9b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/18/2018
ms.locfileid: "39114227"
---
# <a name="run-an-ssis-package-with-the-execute-ssis-package-activity-in-azure-data-factory"></a>Azure Data Factory の SSIS パッケージの実行アクティビティを使用して SSIS パッケージを実行する
この記事では、SSIS パッケージの実行アクティビティを使用して、SSIS パッケージを Azure Data Factory パイプラインで実行する方法を説明します。 

## <a name="prerequisites"></a>前提条件

**Azure SQL データベース**。 この記事のチュートリアルでは、SSIS カタログをホストする Azure SQL データベースを使用します。 Azure SQL マネージド インスタンス (プレビュー) を使うこともできます。

## <a name="create-an-azure-ssis-integration-runtime"></a>Azure-SSIS 統合ランタイムを作成します
Azure-SSIS 統合ランタイムがない場合は、[SSIS パッケージのデプロイに関するチュートリアル](tutorial-create-azure-ssis-runtime-portal.md)の手順に従って作成します。

## <a name="run-a-package-in-the-azure-portal"></a>Azure Portal でパッケージを実行する
このセクションでは、データ ファクトリ UI を使用して、SSIS パッケージを実行する SSIS パッケージの実行アクティビティを含む Data Factory パイプラインを作成します。

### <a name="create-a-data-factory"></a>Data Factory を作成する。
最初の手順として、Azure Portal を使用してデータ ファクトリを作成します。 

1. Web ブラウザー (**Microsoft Edge** または **Google Chrome**) を起動します。 現在、Data Factory の UI がサポートされる Web ブラウザーは Microsoft Edge と Google Chrome だけです。
2. [Azure Portal](https://portal.azure.com) に移動します。 
3. 左側のメニューで **[新規]** をクリックし、**[データ + 分析]**、**[Data Factory]** の順にクリックします。 
   
   ![New->DataFactory](./media/how-to-invoke-ssis-package-stored-procedure-activity/new-azure-data-factory-menu.png)
2. **[新しいデータ ファクトリ]** ページで、**[名前]** に「**ADFTutorialDataFactory**」と入力します。 
      
     ![[新しいデータ ファクトリ] ページ](./media/how-to-invoke-ssis-package-stored-procedure-activity/new-azure-data-factory.png)
 
   Azure データ ファクトリの名前は **グローバルに一意**にする必要があります。 名前フィールドで次のエラーが発生した場合は、データ ファクトリの名前を変更してください (yournameADFTutorialDataFactory など)。 Data Factory アーティファクトの名前付け規則については、[Data Factory の名前付け規則](naming-rules.md)に関する記事を参照してください。
  
     ![名前は使用できません - エラー](./media/how-to-invoke-ssis-package-stored-procedure-activity/name-not-available-error.png)
3. データ ファクトリを作成する Azure **サブスクリプション**を選択します。 
4. **[リソース グループ]** について、次の手順のいずれかを行います。
     
      - **[Use existing (既存のものを使用)]** を選択し、ドロップダウン リストから既存のリソース グループを選択します。 
      - **[新規作成]** を選択し、リソース グループの名前を入力します。   
         
    リソース グループの詳細については、 [リソース グループを使用した Azure のリソースの管理](../azure-resource-manager/resource-group-overview.md)に関するページを参照してください。  
4. **バージョン**として **[V2]** を選択します。
5. データ ファクトリの **場所** を選択します。 Data Factory でサポートされている場所のみがドロップダウン リストに表示されます。 データ ファクトリで使用するデータ ストア (Azure Storage、Azure SQL Database など) やコンピューティング (HDInsight など) は他の場所に配置できます。
6. **[ダッシュボードにピン留めする]** をオンにします。     
7. **Create** をクリックしてください。
8. ダッシュボードに、**[Deploying data factory]\(データ ファクトリをデプロイしています\)** というステータスを示したタイルが表示されます。 

    ![[Deploying data factory]\(データ ファクトリをデプロイしています\) タイル](media//how-to-invoke-ssis-package-stored-procedure-activity/deploying-data-factory.png)
9. 作成が完了すると、図に示されているような **[Data Factory]** ページが表示されます。
   
    ![データ ファクトリのホーム ページ](./media/how-to-invoke-ssis-package-stored-procedure-activity/data-factory-home-page.png)
10. **[Author & Monitor]\(作成と監視\)** タイルをクリックして、別のタブで Azure Data Factory ユーザー インターフェイス (UI) アプリケーションを起動します。 

### <a name="create-a-pipeline-with-an-execute-ssis-package-activity"></a>SSIS パッケージの実行アクティビティでパイプラインを作成する
この手順では、データ ファクトリ UI を使用してパイプラインを作成します。 パイプラインに SSIS パッケージの実行アクティビティを追加し、SSIS パッケージを実行するように構成します。 

1. 開始ページで **[Create Pipeline]\(パイプラインの作成\)** をクリックします。 

    ![開始ページ](./media/how-to-invoke-ssis-package-stored-procedure-activity/get-started-page.png)
2. **[アクティビティ]** ツールボックスで **[一般]** を展開し、**[SSIS パッケージ]** アクティビティをドラッグしてパイプライン デザイナー画面にドロップします。 

   ![SSIS パッケージの実行アクティビティをデザイナー画面にドラッグする](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-designer.png) 

3. SSIS パッケージの実行アクティビティのプロパティの **[一般]** タブで、アクティビティの名前と説明を入力します。 オプションのタイムアウト値と再試行値を設定します。

    ![[一般] タブでプロパティを設定する](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)

4. SSIS パッケージの実行アクティビティのプロパティの **[設定]** タブで、パッケージが展開される `SSISDB` データベースに関連付けられている Azure-SSIS Integration Runtime を選択します。 `<folder name>/<project name>/<package name>.dtsx` という形式で `SSISDB` データベースのパッケージ パスを指定します。 必要に応じて、32 ビットの実行と、事前定義されたログ レベルまたはカスタム ログ レベルを指定し、`<folder name>/<environment name>` という形式で環境パスを指定します。

    ![[設定] タブでプロパティを設定する](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings.png)

5. パイプラインの構成を検証するために、ツール バーの **[検証]** をクリックします。 **[>>]** をクリックして、**[Pipeline Validation Report]\(パイプライン検証レポート\)** を閉じます。

6. **[Publish All]\(すべて発行\)** ボタンをクリックして、データ ファクトリにパイプラインを発行します。 

### <a name="optionally-parameterize-the-activity"></a>必要に応じてアクティビティをパラメーター化する

オプションで、SSIS パッケージの実行アクティビティ ボックスの一番下にある **[View Source Code] (ソース コードの表示)** ボタン、またはパイプライン領域の右上隅にある **[コード]** ボタンを使用して、Data Factory システム変数を参照できる値、式、または関数をプロジェクトまたはパッケージ パラメーターに JSON 形式で割り当てます。 たとえば、次のスクリーンショットに示すように、Data Factory パイプライン パラメーターを SSIS プロジェクトまたはパッケージ パラメーターに割り当てることができます。

![SSIS パッケージの実行アクティビティの JSON スクリプトを編集する](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-parameters.png)

![SSIS パッケージの実行アクティビティにパラメーターを追加する](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-parameters2.png)

### <a name="run-the-pipeline"></a>パイプラインを実行する
このセクションでは、パイプラインの実行をトリガーして監視します。 

1. パイプラインの実行をトリガーするために、ツール バーの **[トリガー]** をクリックし、**[Trigger Now]\(今すぐトリガー\)** をクリックします。 

    ![[Trigger Now]\(今すぐトリガー\)](./media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-trigger.png)

2. **[Pipeline Run]\(パイプラインの実行\)** ウィンドウで **[完了]** を選択します。 

### <a name="monitor-the-pipeline"></a>パイプラインの監視

1. 左側で **[監視]** タブに切り替えます。 パイプラインの実行とその状態が、その他の情報 (実行開始時刻など) と共に表示されます。 ビューを更新するには、**[Refresh]\(最新の情報に更新\)** をクリックします。

    ![パイプライン実行](./media/how-to-invoke-ssis-package-stored-procedure-activity/pipeline-runs.png)

2. **[アクション]** 列の **[View Activity Runs]\(アクティビティの実行の表示\)** リンクをクリックします。 パイプラインに 1 つしかアクティビティ (SSIS パッケージの実行アクティビティ) がないので、アクティビティの実行が 1 つだけ表示されます。

    ![アクティビティの実行](./media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-runs.png)

3. 次の**クエリ**を Azure SQL サーバーの SSISDB データベースに対して実行すると、パッケージが実行されたことを確認できます。 

    ```sql
    select * from catalog.executions
    ```

    ![パッケージの実行を確認する](./media/how-to-invoke-ssis-package-stored-procedure-activity/verify-package-executions.png)

4. パイプラインのアクティビティ実行の出力から SSISDB 実行 ID を取得し、その ID を使用して SSMS 内でより包括的な実行ログとエラー メッセージを確認することもできます。

    ![実行 ID を取得します。](media/how-to-invoke-ssis-package-ssis-activity/get-execution-id.png)

### <a name="schedule-the-pipeline-with-a-trigger"></a>トリガーを使用してパイプラインのスケジュールを設定する

また、パイプラインがスケジュール上で実行されるように (時間単位、日次など)、お使いのパイプラインのスケジュールされたトリガーを作成します。 例については、「[Create a data factory - Data Factory UI](quickstart-create-data-factory-portal.md#trigger-the-pipeline-on-a-schedule)」 (データ ファクトリの作成 - データ ファクトリ UI ) リンクをご確認ください。

## <a name="run-a-package-with-powershell"></a>PowerShell を使用してパッケージを実行する
このセクションでは、Azure PowerShell を使用して、SSIS パッケージを実行する SSIS パッケージの実行アクティビティを含む Data Factory パイプラインを作成します。 

[Azure PowerShell のインストールと構成の方法](/powershell/azure/install-azurerm-ps)に関するページの手順に従って、最新の Azure PowerShell モジュールをインストールしてください。 

### <a name="create-a-data-factory"></a>Data Factory を作成する。
Azure-SSIS IR と同じデータ ファクトリを使用することも、別のデータ ファクトリを作成することもできます。 次の手順では、データ ファクトリを作成する方法を説明します。 SSIS パッケージの実行アクティビティが含まれたパイプラインをデータ ファクトリに作成します。 SSIS パッケージの実行アクティビティにより SSIS パッケージが実行されます。 

1. 後で PowerShell コマンドで使用できるように、リソース グループ名の変数を定義します。 次のコマンド テキストを PowerShell にコピーし、[Azure リソース グループ](../azure-resource-manager/resource-group-overview.md)の名前を二重引用符で囲んで指定し、コマンドを実行します。 (例: `"adfrg"`)。 
   
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
    $DataFactory = Set-AzureRmDataFactoryV2 -ResourceGroupName $ResGrp.ResourceGroupName `
                                            -Location $ResGrp.Location `
                                            -Name $dataFactoryName 
    ```

以下の点に注意してください。

* Azure Data Factory の名前はグローバルに一意にする必要があります。 次のエラーが発生した場合は、名前を変更してからもう一度実行してください。

    ```
    The specified Data Factory name 'ADFv2QuickStartDataFactory' is already in use. Data Factory names must be globally unique.
    ```
* Data Factory インスタンスを作成するには、Azure へのログインに使用するユーザー アカウントが、**共同作成者**ロールまたは**所有者**ロールのメンバーであるか、Azure サブスクリプションの**管理者**である必要があります。
* 現在 Data Factory が利用できる Azure リージョンの一覧については、「[リージョン別の利用可能な製品](https://azure.microsoft.com/global-infrastructure/services/)」ページで目的のリージョンを選択し、**[分析]** を展開して **[Data Factory]** を探してください。 データ ファクトリで使用するデータ ストア (Azure Storage、Azure SQL Database など) やコンピューティング (HDInsight など) は他のリージョンに配置できます。

### <a name="create-a-pipeline-with-an-execute-ssis-package-activity"></a>SSIS パッケージの実行アクティビティでパイプラインを作成する 
この手順では、SSIS パッケージの実行アクティビティでパイプラインを作成します。 このアクティビティは SSIS パッケージを実行します。 

1. 次の例のような内容を記述した **RunSSISPackagePipeline.json** という名前の JSON ファイルを **C:\ADF\RunSSISPackage** フォルダーに作成します。

    > [!IMPORTANT]
    > ファイルを保存する前に、オブジェクトの名前、説明、パス、プロパティ値、パラメーター値、パスワード、およびその他の変数値を置き換えます。 

    ```json
    {
        "name": "RunSSISPackagePipeline",
        "properties": {
            "activities": [{
                "name": "mySSISActivity",
                "description": "My SSIS package/activity description",
                "type": "ExecuteSSISPackage",
                "typeProperties": {
                    "connectVia": {
                        "referenceName": "myAzureSSISIR",
                        "type": "IntegrationRuntimeReference"
                    },
                    "runtime": "x64",
                    "loggingLevel": "Basic",
                    "packageLocation": {
                        "packagePath": "FolderName/ProjectName/PackageName.dtsx"            
                    },
                    "environmentPath":   "FolderName/EnvironmentName",
                    "projectParameters": {
                        "project_param_1": {
                            "value": "123"
                        }
                    },
                    "packageParameters": {
                        "package_param_1": {
                            "value": "345"
                        }
                    },
                    "projectConnectionManagers": {
                        "MyAdonetCM": {
                            "userName": {
                                "value": "sa"
                            },
                            "passWord": {
                                "value": {
                                    "type": "SecureString",
                                    "value": "abc"
                                }
                            }
                        }
                    },
                    "packageConnectionManagers": {
                        "MyOledbCM": {
                            "userName": {
                                "value": "sa"
                            },
                            "passWord": {
                                "value": {
                                    "type": "SecureString",
                                    "value": "def"
                                }
                            }
                        }
                    },
                    "propertyOverrides": {
                        "\\PackageName.dtsx\\MaxConcurrentExecutables ": {
                            "value": 8,
                            "isSensitive": false
                        }
                    }
                },
                "policy": {
                    "timeout": "0.01:00:00",
                    "retry": 0,
                    "retryIntervalInSeconds": 30
                }
            }]
        }
    }
    ```

2.  Azure PowerShell で `C:\ADF\RunSSISPackage` フォルダーに切り替えます。

3. パイプライン **RunSSISPackagePipeline** を作成するには、**Set-AzureRmDataFactoryV2Pipeline** コマンドレットを実行します。

    ```powershell
    $DFPipeLine = Set-AzureRmDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName `
                                                   -ResourceGroupName $ResGrp.ResourceGroupName `
                                                   -Name "RunSSISPackagePipeline"
                                                   -DefinitionFile ".\RunSSISPackagePipeline.json"
    ```

    出力例を次に示します。

    ```
    PipelineName      : Adfv2QuickStartPipeline
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Activities        : {CopyFromBlobToBlob}
    Parameters        : {[inputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification], [outputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification]}
    ```

### <a name="run-the-pipeline"></a>パイプラインを実行する
**Invoke-AzureRmDataFactoryV2Pipeline** コマンドレットを使ってパイプラインを実行します。 コマンドレットは、将来の監視のために、パイプラインの実行 ID を返します。

```powershell
$RunId = Invoke-AzureRmDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName `
                                             -ResourceGroupName $ResGrp.ResourceGroupName `
                                             -PipelineName $DFPipeLine.Name
```

### <a name="monitor-the-pipeline"></a>パイプラインの監視

次の PowerShell スクリプトを実行し、データのコピーが完了するまで、パイプラインの実行の状態を継続的にチェックします。 次のスクリプトをコピーして PowerShell ウィンドウに貼り付け、Enter キーを押します。 

```powershell
while ($True) {
    $Run = Get-AzureRmDataFactoryV2PipelineRun -ResourceGroupName $ResGrp.ResourceGroupName `
                                               -DataFactoryName $DataFactory.DataFactoryName `
                                               -PipelineRunId $RunId

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

Azure Portal を使用してパイプラインを監視することもできます。 具体的な手順については、「[パイプラインの監視](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline)」を参照してください。

### <a name="schedule-the-pipeline-with-a-trigger"></a>トリガーを使用してパイプラインのスケジュールを設定する
前の手順ではオンデマンドでパイプラインを実行しました。 スケジュール トリガーを作成して、スケジュール (毎時、毎日など) によってパイプラインをトリガーすることもできます。

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
    Set-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName `
                                    -DataFactoryName $DataFactory.DataFactoryName `
                                    -Name "MyTrigger" -DefinitionFile ".\MyTrigger.json"
    ```
4. 既定ではトリガーは停止状態になっています。 **Start-AzureRmDataFactoryV2Trigger** コマンドレットを実行してトリガーを起動します。 

    ```powershell
    Start-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName `
                                      -DataFactoryName $DataFactory.DataFactoryName `
                                      -Name "MyTrigger" 
    ```
5. **Get-AzureRmDataFactoryV2Trigger** コマンドレットを実行して、トリガーの状態が "Started" であることを確認します。 

    ```powershell
    Get-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName `
                                    -DataFactoryName $DataFactoryName `
                                    -Name "MyTrigger"     
    ```    
6. 時刻の時間部分が次の時間になってから、次のコマンドを実行します。 たとえば、現在時刻が午後 3 時 25 分 (UTC) であれば、午後 4 時 (UTC) にコマンドを実行します。 
    
    ```powershell
    Get-AzureRmDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName `
                                       -DataFactoryName $DataFactoryName `
                                       -TriggerName "MyTrigger" `
                                       -TriggerRunStartedAfter "2017-12-06" `
                                       -TriggerRunStartedBefore "2017-12-09"
    ```

    次のクエリを Azure SQL サーバーの SSISDB データベースに対して実行すると、パッケージが実行されたことを確認できます。 

    ```sql
    select * from catalog.executions
    ```

## <a name="next-steps"></a>次の手順
次のブログ記事を参照してください。
-   [ADF パイプラインでの SSIS アクティビティを含む ETL/ELT ワークフローの最新化と拡張](https://blogs.msdn.microsoft.com/ssis/2018/05/23/modernize-and-extend-your-etlelt-workflows-with-ssis-activities-in-adf-pipelines/)
