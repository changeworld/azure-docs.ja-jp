---
title: "PowerShell を使用して Azure データ ファクトリを作成する | Microsoft Docs"
description: "Azure データ ファクトリを作成して、データを Azure BLOB ストレージ内のある場所から同じ BLOB ストレージ内の別の場所にコピーします。"
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: 
ms.devlang: powershell
ms.topic: hero-article
ms.date: 09/19/2017
ms.author: jingwang
ms.translationtype: HT
ms.sourcegitcommit: 44e9d992de3126bf989e69e39c343de50d592792
ms.openlocfilehash: 92f798244db1f69d01f46d0c0bcce9fe139bef05
ms.contentlocale: ja-jp
ms.lasthandoff: 09/25/2017

---
# <a name="create-a-data-factory-and-pipeline-using-powershell"></a>PowerShell を使用してデータ ファクトリとパイプラインを作成する
Azure Data Factory は、データドリブン型のワークフローをクラウドに作成することでデータの移動と変換を制御し、自動化することができるクラウドベースのデータ統合サービスです。 Azure Data Factory を使用すると、データドリブン型のワークフロー (パイプライン) を作成し、スケジューリングできます。具体的には、各種データ ストアからデータを取り込む、そのデータを各種コンピューティング サービス (Azure HDInsight Hadoop、Spark、Azure Data Lake Analytics、Azure Machine Learning など) で処理/変換する、データ ストア (Azure SQL Data Warehouse など) に出力データを公開して、それを利用するビジネス インテリジェンス (BI) アプリケーションに提供するという一連の処理を行えるワークフローです。 

このクイックスタートでは、PowerShell を使用して Azure データ ファクトリを作成する方法について説明します。 このデータ ファクトリのパイプラインは、Azure BLOB ストレージ内のある場所から別の場所にデータをコピーするものです。

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。

## <a name="prerequisites"></a>前提条件

* **Azure Storage アカウント**。 BLOB ストレージを、**ソース**と**シンク**の両方のデータ ストアとして使用します。 Azure ストレージ アカウントを持っていない場合は、作成について、「ストレージ アカウントの作成」を参照してください。 作成の手順については、記事 (../storage/common/storage-create-storage-account.md#create-a-storage-account) を参照してください。
* Blob Storage に **BLOB コンテナー**を作成し、コンテナーに入力**フォルダー**を作成して、フォルダーにいくつかのファイルをアップロードします。 
* **Azure PowerShell**。 [Azure PowerShell のインストールと構成の方法](/powershell/azure/install-azurerm-ps)に関するページに記載されている手順に従います。
* [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)。 このツールを使用して、Azure Blob Storage への接続、BLOB コンテナーの作成、入力ファイルのアップロード、出力ファイルの検証を行うことができます。 

## <a name="create-a-data-factory"></a>Data Factory を作成する。

1. **PowerShell**を起動します。 Azure PowerShell は、このクイックスタートが終わるまで開いたままにしておいてください。 Azure PowerShell を閉じて再度開いた場合は、これらのコマンドをもう一度実行する必要があります。

    次のコマンドを実行して、Azure Portal へのサインインに使用するユーザー名とパスワードを入力します。
        
    ```powershell
    Login-AzureRmAccount
    ```        
    次のコマンドを実行して、このアカウントのすべてのサブスクリプションを表示します。

    ```powershell
    Get-AzureRmSubscription
    ```
    次のコマンドを実行して、使用するサブスクリプションを選択します。 **SubscriptionId** は、実際の Azure サブスクリプションの ID に置き換えてください。

    ```powershell
    Select-AzureRmSubscription -SubscriptionId "<SubscriptionId>"       
    ```
2. **Set-AzureRmDataFactoryV2** コマンドレットを実行してデータ ファクトリを作成します。 各プレースホルダーを実際の値に置き換えてからコマンドを実行してください。 独自の値に置き換えられた**プレースホルダー**。 

    ```powershell
    $resourceGroupName = "<your resource group to create the factory>";
    $dataFactoryName = "<specify the name of data factory to create. It must be globally unique.>";
    Set-AzureRmDataFactoryV2 -ResourceGroupName "<your resource group to create the factory>" -Location "East US" -Name "<specify the name of data factory to create. It must be globally unique.>" 
    ```

    以下の点に注意してください。

    * Azure Data Factory の名前はグローバルに一意にする必要があります。 次のエラーが発生した場合は、名前を変更してからもう一度実行してください。

        ```
        The specified Data Factory name 'ADFv2QuickStartDataFactory' is already in use. Data Factory names must be globally unique.
        ```

    * Data Factory インスタンスを作成するには、Azure サブスクリプションの共同作成者または管理者である必要があります。
    * 現在、Data Factory V2 でデータ ファクトリを作成できるリージョンは米国東部だけです。 データ ファクトリで使用するデータ ストア (Azure Storage、Azure SQL Database など) やコンピューティング (HDInsight など) は他のリージョンに配置できます。

## <a name="create-a-linked-service"></a>リンクされたサービスを作成する

データ ストアおよびコンピューティング サービスをデータ ファクトリにリンクするには、リンクされたサービスをデータ ファクトリに作成します。 このクイックスタートでは、ソース ストアとシンク ストアの両方として、Azure Storage のリンクされたサービスを 1 つ作成するだけで済みます。このサービスは、このサンプルでは "AzureStorageLinkedService" という名前です。

1. 以下の内容を記述した **AzureStorageLinkedService.json** という名前の JSON ファイルを **C:\ADFv2QuickStartPSH** フォルダー内に作成します (C:\ADFv2QuickStartPSH フォルダーがない場合は作成します)。 &lt;accountName&gt; と &lt;accountKey&gt; を実際の Azure ストレージ アカウントの名前とキーに置き換えてからファイルを保存してください。

    ```json
    {
        "name": "AzureStorageLinkedService",
        "properties": {
            "type": "AzureStorage",
            "typeProperties": {
                "connectionString": {
                    "value": "DefaultEndpointsProtocol=https;AccountName=<accountName>;AccountKey=<accountKey>",
                    "type": "SecureString"
                }
            }
        }
    }
    ```

2. **Azure PowerShell** で **ADFv2QuickStartPSH** フォルダーに切り替えます。

3. **Set-AzureRmDataFactoryV2LinkedService** コマンドレットを実行して、リンクされたサービス **AzureStorageLinkedService** を作成します。 

    ```powershell
    Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureStorageLinkedService" -DefinitionFile ".\AzureStorageLinkedService.json"
    ```

    出力例を次に示します。

    ```
    LinkedServiceName : AzureStorageLinkedService
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureStorageLinkedService
    ```

## <a name="create-a-dataset"></a>データセットを作成する

ソースからシンクにコピーするデータを表すデータセットを定義します。 この例のこの BLOB データセットは、前の手順で作成した Azure Storage のリンクされたサービスを参照します。 データセットは、データセットを使用するアクティビティで設定された値を持つパラメーターを受け取ります。 パラメーターは、データの存在/格納場所を指す "folderPath" を構築するために使用されます。

1. 以下の内容を記述した **BlobDataset.json** という名前の JSON ファイルを **C:\ADFv2QuickStartPSH** フォルダー内に作成します。

    ```json
    {
        "name": "BlobDataset",
        "properties": {
            "type": "AzureBlob",
            "typeProperties": {
                "folderPath": {
                    "value": "@{dataset().path}",
                    "type": "Expression"
                }
            },
            "linkedServiceName": {
                "referenceName": "AzureStorageLinkedService",
                "type": "LinkedServiceReference"
            },
            "parameters": {
                "path": {
                    "type": "String"
                }
            }
        }
    }
    ```

2. データセット **BlobDataset** を作成するには、**Set-AzureRmDataFactoryV2Dataset** コマンドレットを実行します。

    ```powershell
    Set-AzureRmDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "BlobDataset" -DefinitionFile ".\BlobDataset.json"
    ```

    出力例を次に示します。

    ```
    DatasetName       : BlobDataset
    ResourceGroupName : <resourceGroupname>
    DataFactoryName   : <dataFactoryName>
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureBlobDataset
    ```

## <a name="create-a-pipeline"></a>パイプラインを作成する。

この例では、このパイプラインには 1 つのアクティビティが含まれており、2 つのパラメーター (入力 BLOB パスと出力 BLOB パス) を受け取ります。 これらのパラメーターの値は、パイプラインがトリガー/実行されたときに設定されます。 コピー アクティビティは、入力と出力として、前の手順で作成された同じ BLOB データセットを参照します。 データセットが入力データセットとして使用される場合は、入力パスが指定されます。 また、データセットが出力データセットとして使用される場合は、出力パスが指定されます。 

1. 以下の内容を記述した **Adfv2QuickStartPipeline.json** という名前の JSON ファイルを **C:\ADFv2QuickStartPSH** フォルダー内に作成します。

    ```json
    {
        "name": "Adfv2QuickStartPipeline",
        "properties": {
            "activities": [
                {
                    "name": "CopyFromBlobToBlob",
                    "type": "Copy",
                    "inputs": [
                        {
                            "referenceName": "BlobDataset",
                            "parameters": {
                                "path": "@pipeline().parameters.inputPath"
                            },
                        "type": "DatasetReference"
                        }
                    ],
                    "outputs": [
                        {
                            "referenceName": "BlobDataset",
                            "parameters": {
                                "path": "@pipeline().parameters.outputPath"
                            },
                            "type": "DatasetReference"
                        }
                    ],
                    "typeProperties": {
                        "source": {
                            "type": "BlobSource"
                        },
                        "sink": {
                            "type": "BlobSink"
                        }
                    }
                }
            ],
            "parameters": {
                "inputPath": {
                    "type": "String"
                },
                "outputPath": {
                    "type": "String"
                }
            }
        }
    }
    ```

2. パイプライン **Adfv2QuickStartPipeline** を作成するには、**Set-AzureRmDataFactoryV2Pipeline** コマンドレットを実行します。

    ```powershell
    Set-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "Adfv2QuickStartPipeline" -DefinitionFile ".\Adfv2QuickStartPipeline.json"
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

このステップでは、パイプライン パラメーターの **inputPath** と **outputPath** に、ソースおよびシンク BLOB パスの実際の値を設定します。 次に、これらの引数を使用して、パイプラインの実行を作成します。 

1. 以下の内容を記述した **PipelineParameters.json** という名前の JSON ファイルを **C:\ADFv2QuickStartPSH** フォルダー内に作成します。

    "inputPath" と "outputPath" の値を、ソースおよびシンクの BLOB パスに置き換え、ファイルを保存する前にデータをコピーします。

    ```json
    {
        "inputPath": "<the path to existing blob(s) to copy data from, e.g. containername/foldername>",
        "outputPath": "<the blob path to copy data to, e.g. containername/foldername>"
    }
    ```

2. **Invoke-AzureRmDataFactoryV2Pipeline** コマンドレットを実行し、パイプラインの実行を作成し、パラメーターの値を渡します。 将来の監視のために、パイプラインの実行の ID もキャプチャされます。

    ```powershell
    $runId = Invoke-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineName "Adfv2QuickStartPipeline" -ParameterFile .\PipelineParameters.json
    ```

## <a name="monitor-a-pipeline-run"></a>パイプラインの実行を監視する

1. 次のスクリプトを実行し、データのコピーが完了するまで、パイプラインの実行の状態を継続的にチェックします。

    ```powershell
    while ($True) {
        $run = Get-AzureRmDataFactoryV2PipelineRun -ResourceGroupName $resourceGroupName -DataFactoryName $DataFactoryName -PipelineRunId $runId

        if ($run) {
            if ($run.Status -ne 'InProgress') {
                Write-Host "Pipeline run finished. The status is: " $run.Status -foregroundcolor "Yellow"
                $run
                break
            }
        }

        Write-Host  "Pipeline is running...status: " $run.Status -foregroundcolor "Yellow"
        Start-Sleep -Seconds 30
    }
    ```

    パイプラインの実行の出力例を次に示します。

    ```
    Key                  : 00000000-0000-0000-0000-000000000000
    Timestamp            : 9/7/2017 8:31:26 AM
    RunId                : 000000000-0000-0000-0000-000000000000
    DataFactoryName      : <dataFactoryname>
    PipelineName         : Adfv2QuickStartPipeline
    Parameters           : {inputPath: <inputBlobPath>, outputPath: <outputBlobPath>}
    ParametersCount      : 2
    ParameterNames       : {inputPath, outputPath}
    ParameterNamesCount  : 2
    ParameterValues      : {<inputBlobPath>, <outputBlobPath>}
    ParameterValuesCount : 2
    RunStart             : 9/7/2017 8:30:45 AM
    RunEnd               : 9/7/2017 8:31:26 AM
    DurationInMs         : 41291
    Status               : Succeeded
    Message              :
    ```

2. 次のスクリプトを実行し、コピー アクティビティの実行の詳細 (たとえば、読み書きされたデータのサイズ) を取得します。

    ```powershell
    $result = Get-AzureRmDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $runId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)
    Write-Host "Activity run details:" -foregroundcolor "Yellow"
    $result
    
    Write-Host "Activity 'Output' section:" -foregroundcolor "Yellow"
    $result.Output -join "`r`n"
    
    Write-Host "\nActivity 'Error' section:" -foregroundcolor "Yellow"
    $result.Error -join "`r`n"
    ```
3. アクティビティの実行の結果である次のサンプル出力のような出力が表示されることを確認します。

    ```json
    Activity run details:
    ResourceGroupName : adf
    DataFactoryName   : <dataFactoryname>
    ActivityName      : CopyFromBlobToBlob
    Timestamp         : 9/7/2017 8:24:06 AM
    PipelineRunId     : 9b362a1d-37b5-449f-918c-53a8d819d83f
    PipelineName      : Adfv2QuickStartPipeline
    Input             : {source, sink}
    Output            : {dataRead, dataWritten, copyDuration, throughput...}
    LinkedServiceName :
    ActivityStart     : 9/7/2017 8:23:30 AM
    ActivityEnd       : 9/7/2017 8:24:06 AM
    Duration          : 36331
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}
    
    Activity 'Output' section:
    "dataRead": 331452208
    "dataWritten": 331452208
    "copyDuration": 23
    "throughput": 14073.209
    "errors": []
    "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (West US)"
    ```

## <a name="verify-the-output"></a>出力を検証する
[Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) のようなツールを使用して、inputBlobPath の BLOB が outputBlobPath にコピーされることを確認します。

## <a name="clean-up-resources"></a>リソースのクリーンアップ
このクイックスタートで作成したリソースは、2 とおりの方法でクリーンアップすることができます。 [Azure リソース グループ](../azure-resource-manager/resource-group-overview.md)を削除した場合、そのリソース グループに含まれたすべてのリソースが対象となります。 他のリソースをそのまま維持する場合は、このチュートリアルで作成したデータ ファクトリだけを削除してください。

次のコマンドを実行して、リソース グループ全体を削除します。 
```powershell
Remove-AzureRmResourceGroup -ResourceGroupName $resourcegroupname
```

次のコマンドを実行して、データ ファクトリだけを削除します。 

```powershell
Remove-AzureRmDataFactoryV2 -Name "<NameOfYourDataFactory>" -ResourceGroupName "<NameOfResourceGroup>"
```

## <a name="next-steps"></a>次のステップ
このサンプルのパイプラインは、Azure BLOB ストレージ内のある場所から別の場所にデータをコピーするものです。 より多くのシナリオで Data Factory を使用する方法については、[チュートリアル](tutorial-copy-data-dot-net.md)を参照してください。 
