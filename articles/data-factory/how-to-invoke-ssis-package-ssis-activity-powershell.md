---
title: PowerShell で SSIS パッケージの実行アクティビティを使用する
description: この記事では、SSIS パッケージの実行アクティビティを使用して、PowerShell を用いて SQL Server Integration Services (SSIS) パッケージを Azure Data Factory パイプラインで実行する方法を説明します。
ms.service: data-factory
ms.subservice: integration-services
ms.devlang: powershell
ms.topic: conceptual
ms.author: sawinark
author: swinarko
ms.custom: seo-lt-2019, devx-track-azurepowershell
ms.date: 10/22/2021
ms.openlocfilehash: ab67509d2b9c9eda90268c19bce081d1bb11e942
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2021
ms.locfileid: "131843463"
---
# <a name="run-an-ssis-package-with-the-execute-ssis-package-activity-in-azure-data-factory-with-powershell"></a>PowerShell を使用して Azure Data Factory の SSIS パッケージの実行アクティビティで SSIS パッケージを実行する

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

この記事では、PowerShell と SSIS パッケージの実行アクティビティを使用して、SQL Server Integration Services (SSIS) パッケージを Azure Data Factory パイプラインで実行する方法を説明します。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Azure-SSIS 統合ランタイム (IR) がない場合は、[「チュートリアル: Azure-SSIS 統合ランタイムのプロビジョニング](./tutorial-deploy-ssis-packages-azure.md)」の手順に従って作成します。

## <a name="create-a-data-factory-with-azure-ssis-ir"></a>Azure-SSIS IR でデータ ファクトリを作成する
既に Azure-SSIS IR がプロビジョニングされている既存のデータ ファクトリを使用することも、Azure-SSIS IR を使用して新しいデータ ファクトリを作成することもできます。 「[チュートリアル: PowerShell を使用した Azure への SSIS パッケージのデプロイに関するチュートリアル](./tutorial-deploy-ssis-packages-azure-powershell.md)の手順に従って、Azure-SSIS IR を使用して新しい ADF を作成します。

## <a name="create-a-pipeline-with-an-execute-ssis-package-activity"></a>SSIS パッケージの実行アクティビティでパイプラインを作成する 
この手順では、SSIS パッケージの実行アクティビティでパイプラインを作成します。 このアクティビティは SSIS パッケージを実行します。 

1. 次の例のような内容で、`C:\ADF\RunSSISPackage` フォルダーに `RunSSISPackagePipeline.json` という名前の JSON ファイルを作成します。

   > [!IMPORTANT]
   > ファイルを保存する前に、オブジェクトの名前、説明、パス、プロパティ値、パラメーター値、パスワード、およびその他の変数値を置き換えます。 
    
   ```json
   {
       "name": "RunSSISPackagePipeline",
       "properties": {
           "activities": [{
               "name": "MySSISActivity",
               "description": "My SSIS package/activity description",
               "type": "ExecuteSSISPackage",
               "typeProperties": {
                   "connectVia": {
                       "referenceName": "MyAzureSSISIR",
                       "type": "IntegrationRuntimeReference"
                   },
                   "executionCredential": {
                       "domain": "MyExecutionDomain",
                       "username": "MyExecutionUsername",
                       "password": {
                           "type": "SecureString",
                           "value": "MyExecutionPassword"
                       }
                   },
                   "runtime": "x64",
                   "loggingLevel": "Basic",
                   "packageLocation": {
                       "type": "SSISDB",
                       "packagePath": "MyFolder/MyProject/MyPackage.dtsx"
                   },
                   "environmentPath": "MyFolder/MyEnvironment",
                   "projectParameters": {
                       "project_param_1": {
                           "value": "123"
                       },
                       "project_param_2": {
                           "value": {
                               "value": "@pipeline().parameters.MyProjectParameter",
                               "type": "Expression"
                           }
                       }
                   },
                   "packageParameters": {
                       "package_param_1": {
                           "value": "345"
                       },
                       "package_param_2": {
                           "value": {
                               "type": "AzureKeyVaultSecret",
                               "store": {
                                   "referenceName": "myAKV",
                                   "type": "LinkedServiceReference"
                               },
                               "secretName": "MyPackageParameter"
                           }
                       }
                   },
                   "projectConnectionManagers": {
                       "MyAdonetCM": {
                           "username": {
                               "value": "MyConnectionUsername"
                           },
                           "password": {
                               "value": {
                                   "type": "SecureString",
                                   "value": "MyConnectionPassword"
                               }
                           }
                       }
                   },
                   "packageConnectionManagers": {
                       "MyOledbCM": {
                           "username": {
                               "value": {
                                   "value": "@pipeline().parameters.MyConnectionUsername",
                                   "type": "Expression"
                               }
                           },
                           "password": {
                               "value": {
                                   "type": "AzureKeyVaultSecret",
                                   "store": {
                                       "referenceName": "myAKV",
                                       "type": "LinkedServiceReference"
                                   },
                                   "secretName": "MyConnectionPassword",
                                   "secretVersion": "MyConnectionPasswordVersion"
                               }
                           }
                       }
                   },
                   "propertyOverrides": {
                       "\\Package.MaxConcurrentExecutables": {
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

   ファイル システムまたは Azure Files に格納されているパッケージを実行するには、次のようにパッケージおよびログの場所のプロパティの値を入力します。

   ```json
   {
       {
           {
               {
                   "packageLocation": {
                       "type": "File",
                       "packagePath": "//MyStorageAccount.file.core.windows.net/MyFileShare/MyPackage.dtsx",
                       "typeProperties": {
                           "packagePassword": {
                               "type": "SecureString",
                               "value": "MyEncryptionPassword"
                           },
                           "accessCredential": {
                               "domain": "Azure",
                               "username": "MyStorageAccount",
                               "password": {
                                   "type": "SecureString",
                                   "value": "MyAccountKey"
                               }
                           }
                       }
                   },
                   "logLocation": {
                       "type": "File",
                       "logPath": "//MyStorageAccount.file.core.windows.net/MyFileShare/MyLogFolder",
                       "typeProperties": {
                           "accessCredential": {
                               "domain": "Azure",
                               "username": "MyStorageAccount",
                               "password": {
                                   "type": "AzureKeyVaultSecret",
                                   "store": {
                                       "referenceName": "myAKV",
                                       "type": "LinkedServiceReference"
                                   },
                                   "secretName": "MyAccountKey"
                               }
                           }
                       }
                   }
               }
           }
       }
   }
   ```

   ファイル システムまたは Azure Files に格納されているプロジェクト内のパッケージを実行するには、次のようにパッケージの場所のプロパティの値を入力します。

   ```json
   {
       {
           {
               {
                   "packageLocation": {
                       "type": "File",
                       "packagePath": "//MyStorageAccount.file.core.windows.net/MyFileShare/MyProject.ispac:MyPackage.dtsx",
                       "typeProperties": {
                           "packagePassword": {
                               "type": "SecureString",
                               "value": "MyEncryptionPassword"
                           },
                           "accessCredential": {
                               "domain": "Azure",
                               "userName": "MyStorageAccount",
                               "password": {
                                   "type": "SecureString",
                                   "value": "MyAccountKey"
                               }
                           }
                       }
                   }
               }
           }
       }
   }
   ```

   埋め込みパッケージを実行するには、パッケージの場所プロパティの値を次のように入力します。

   ```json
   {
       {
           {
               {
                   "packageLocation": {
                       "type": "InlinePackage",
                       "typeProperties": {
                           "packagePassword": {
                               "type": "SecureString",
                               "value": "MyEncryptionPassword"
                           },
                           "packageName": "MyPackage.dtsx",
                           "packageContent":"My compressed/uncompressed package content",
                           "packageLastModifiedDate": "YYYY-MM-DDTHH:MM:SSZ UTC-/+HH:MM"
                       }
                   }
               }
           }
       }
   }
   ```

   パッケージ ストアに格納されているパッケージを実行するには、パッケージの値と構成の場所のプロパティを次のように入力します。

   ```json
   {
       {
           {
               {
                   "packageLocation": {
                       "type": "PackageStore",
                       "packagePath": "myPackageStore/MyFolder/MyPackage",
                       "typeProperties": {
                           "packagePassword": {
                               "type": "SecureString",
                               "value": "MyEncryptionPassword"
                           },
                           "accessCredential": {
                               "domain": "Azure",
                               "username": "MyStorageAccount",
                               "password": {
                                   "type": "SecureString",
                                   "value": "MyAccountKey"
                               }
                           },
                           "configurationPath": "//MyStorageAccount.file.core.windows.net/MyFileShare/MyConfiguration.dtsConfig",
                           "configurationAccessCredential": {
                               "domain": "Azure",
                               "userName": "MyStorageAccount",
                               "password": {
                                   "type": "AzureKeyVaultSecret",
                                   "store": {
                                       "referenceName": "myAKV",
                                       "type": "LinkedServiceReference"
                                   },
                                   "secretName": "MyAccountKey"
                               }
                           }
                       }
                   }
               }
           }
       }
   }
   ```

2. Azure PowerShell で `C:\ADF\RunSSISPackage` フォルダーに切り替えます。

3. パイプライン **RunSSISPackagePipeline** を作成するには、**Set-AzDataFactoryV2Pipeline** コマンドレットを実行します。

   ```powershell
   $DFPipeLine = Set-AzDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName `
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

## <a name="run-the-pipeline"></a>パイプラインを実行する
**Invoke-AzDataFactoryV2Pipeline** コマンドレットを使ってパイプラインを実行します。 コマンドレットは、将来の監視のために、パイプラインの実行 ID を返します。

```powershell
$RunId = Invoke-AzDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName `
                                             -ResourceGroupName $ResGrp.ResourceGroupName `
                                             -PipelineName $DFPipeLine.Name
```

## <a name="monitor-the-pipeline"></a>パイプラインの監視

次の PowerShell スクリプトを実行し、データのコピーが完了するまで、パイプラインの実行の状態を継続的にチェックします。 次のスクリプトを PowerShell ウィンドウにコピーするか貼り付けて、Enter キーを押します。 

```powershell
while ($True) {
    $Run = Get-AzDataFactoryV2PipelineRun -ResourceGroupName $ResGrp.ResourceGroupName `
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

Azure portal を使用してパイプラインを監視することもできます。 具体的な手順については、「[パイプラインの監視](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline)」を参照してください。

## <a name="schedule-the-pipeline-with-a-trigger"></a>トリガーを使用してパイプラインのスケジュールを設定する
前の手順ではオンデマンドでパイプラインを実行しました。 スケジュール トリガーを作成して、スケジュール (毎時、毎日など) によってパイプラインをトリガーすることもできます。

1. 以下の内容を記述した `MyTrigger.json` という名前の JSON ファイルを `C:\ADF\RunSSISPackage` フォルダーに作成します。 
        
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
           }]
       }
   }    
   ```
    
1. Azure PowerShell で `C:\ADF\RunSSISPackage` フォルダーに切り替えます。
1. **Set-AzDataFactoryV2Trigger** コマンドレットを実行してトリガーを作成します。 

   ```powershell
   Set-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName `
                                   -DataFactoryName $DataFactory.DataFactoryName `
                                   -Name "MyTrigger" -DefinitionFile ".\MyTrigger.json"
   ```
1. 既定ではトリガーは停止状態になっています。 **Start-AzDataFactoryV2Trigger** コマンドレットを使用してトリガーを起動します。 

   ```powershell
   Start-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName `
                                     -DataFactoryName $DataFactory.DataFactoryName `
                                     -Name "MyTrigger" 
   ```
1. **Start-AzDataFactoryV2Trigger** コマンドレットを実行して、トリガーが起動されていることを確認します。 

   ```powershell
   Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName `
                                   -DataFactoryName $DataFactoryName `
                                   -Name "MyTrigger"     
   ```    
1. 時刻の時間部分が次の時間になってから、次のコマンドを実行します。 たとえば、現在時刻が午後 3 時 25 分 (UTC) であれば、午後 4 時 (UTC) にコマンドを実行します。 
    
   ```powershell
   Get-AzDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName `
                                      -DataFactoryName $DataFactoryName `
                                      -TriggerName "MyTrigger" `
                                      -TriggerRunStartedAfter "2017-12-06" `
                                      -TriggerRunStartedBefore "2017-12-09"
   ```

   パッケージが実行されたことを確認するには、次のクエリを SQL サーバーの SSISDB データベースに対して実行します。 

   ```sql
   select * from catalog.executions
   ```

## <a name="next-steps"></a>次のステップ

- [Azure Data Factory Studio ポータルで SSIS パッケージの実行アクティビティを使用し、SSIS パッケージを実行する](how-to-invoke-ssis-package-ssis-activity.md)
- [Azure Data Factory パイプラインでの SSIS アクティビティを含む ETL/ELT ワークフローの最新化と拡張](https://techcommunity.microsoft.com/t5/SQL-Server-Integration-Services/Modernize-and-Extend-Your-ETL-ELT-Workflows-with-SSIS-Activities/ba-p/388370)