---
title: "Azure Data Factory を使用して Blob Storage でデータをコピーする | Microsoft Docs"
description: "Azure データ ファクトリを作成して、Azure BLOB ストレージ内のあるフォルダーから別のフォルダーにデータをコピーし、同じ BLOB ストレージ内の別の場所にコピーします。"
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
ms.date: 11/14/2017
ms.author: jingwang
ms.openlocfilehash: 8ee2f48db009da4660a03f91194c4e99f6ecac4a
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2017
---
# <a name="create-an-azure-data-factory-using-powershell"></a>PowerShell を使用した Azure データ ファクトリの作成 
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [バージョン 1 - 一般公開](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [バージョン 2 - プレビュー](quickstart-create-data-factory-powershell.md)

このクイックスタートでは、PowerShell を使用して Azure データ ファクトリを作成する方法について説明します。 このデータ ファクトリに作成されたパイプラインは、データを Azure BLOB ストレージ内のあるフォルダーから別のフォルダーにコピーします。 Azure Data Factory を使用してデータを変換する方法のチュートリアルについては、[Spark を使用したデータ変換のチュートリアル](transform-data-using-spark.md)を参照してください。 

この記事では、Data Factory サービスの概要については詳しく取り上げません。 Azure Data Factory サービスの概要については、「[Azure Data Factory の概要](introduction.md)」をご覧ください。

> [!NOTE]
> この記事は、現在プレビュー段階にある Data Factory のバージョン 2 に適用されます。 一般公開 (GA) されている Data Factory サービスのバージョン 1 を使用している場合は、[Data Factory バージョン 1 の使用](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)に関する記事をご覧ください。


## <a name="prerequisites"></a>前提条件

### <a name="azure-subscription"></a>Azure サブスクリプション
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。

### <a name="azure-storage-account"></a>Azure Storage アカウント
このクイックスタートでは、**ソース** データ ストアと**シンク/コピー先**データ ストアの両方に汎用の Azure Storage アカウント (具体的には Blob Storage) を使用します。 汎用の Azure Storage アカウントがない場合、作成方法については、「[ストレージ アカウントの作成](../storage/common/storage-create-storage-account.md#create-a-storage-account)」をご覧ください。 

#### <a name="get-storage-account-name-and-account-key"></a>ストレージ アカウント名とアカウント キーの取得
このクイックスタートでは、Azure Storage アカウントの名前とキーを使用します。 以下の手順に従って、ご利用のストレージ アカウントの名前とキーを取得してください。 

1. Web ブラウザーを起動して [Azure Portal](https://portal.azure.com) にアクセスします。 Azure のユーザー名とパスワードを使用してログインします。 
2. 左側のメニューの **[その他のサービス >]** をクリックし、"**ストレージ**" というキーワードでフィルター処理して、**[ストレージ アカウント]** を選択します。

    ![ストレージ アカウントを検索](media/quickstart-create-data-factory-powershell/search-storage-account.png)
3. ストレージ アカウントの一覧で、ご利用のストレージ アカウントを (必要に応じて) フィルターで抽出し、**該当するストレージ アカウント**を選択します。 
4. **[ストレージ アカウント]** ページのメニューから **[アクセス キー]** を選択します。

    ![ストレージ アカウントの名前とキーを取得](media/quickstart-create-data-factory-powershell/storage-account-name-key.png)
5. **[ストレージ アカウント名]** フィールドと **[Key1]** フィールドの値をクリップボードにコピーします。 それらをメモ帳または他のエディターに貼り付けて保存します。  

#### <a name="create-input-folder-and-files"></a>入力フォルダーとファイルの作成
このセクションでは、adftutorial という名前の BLOB コンテナーを Azure Blob Storage に作成します。 次に、そのコンテナーに input という名前のフォルダーを作成し、input フォルダーにサンプル ファイルをアップロードします。 

1. [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) がまだマシンにインストールされていない場合はインストールします。 
2. ご利用のマシンで **Microsoft Azure Storage Explorer** を起動します。   
3. **[Azure Storage へ接続]** ウィンドウで **[Use a storage account name and key]\(ストレージ アカウント名とキーを使用\)** を選択し、**[次へ]** をクリックします。 **[Azure Storage へ接続]** ウィンドウが表示されない場合は、ツリー ビューで **[ストレージ アカウント]** を右クリックし、**[Azure Storage へ接続]** をクリックします。 

    ![Azure Storage へ接続](media/quickstart-create-data-factory-powershell/storage-explorer-connect-azure-storage.png)
4. **[Attach using Name and Key]\(名前とキーを使用してアタッチ\)** ウィンドウに、前の手順で保存した**アカウント名**と**アカウント キー**を貼り付けます。 次に、 **[次へ]**をクリックします。 
5. **[接続の概要]** ウィンドウで **[接続]** をクリックします。
6. **[(Local and Attached)]\((ローカルおよびアタッチ)\)** -> **[ストレージ アカウント]** のツリー ビューにストレージ アカウントが表示されていることを確認します。 
7. **[BLOB コンテナー]** を展開し、**adftutorial** BLOB コンテナーが存在しないことを確認します。 既に存在する場合は、次のコンテナー作成手順は省略してください。 
8. **[BLOB コンテナー]** を右クリックし、**[BLOB コンテナーの作成]** を選択します。

    ![BLOB コンテナーを作成する](media/quickstart-create-data-factory-powershell/stroage-explorer-create-blob-container-menu.png)
9. 名前に「**adftutorial**」と入力し、**Enter** キーを押します。 
10. ツリー ビューで **adftutorial** コンテナーが選択されていることを確認します。 
11. ツール バーの **[新しいフォルダー]** をクリックします。 

    ![フォルダーの作成ボタン](media/quickstart-create-data-factory-powershell/stroage-explorer-new-folder-button.png)
12. **[新しい仮想ディレクトリの作成]** ウィンドウで、**[名前]** に「**input**」と入力し、**[OK]** をクリックします。 

    ![[ディレクトリの作成] ダイアログ](media/quickstart-create-data-factory-powershell/storage-explorer-create-new-directory-dialog.png)
13. **メモ帳**を起動し、**emp.txt** という名前のファイルを作成して次の内容を入力します。 
    
    ```
    John, Doe
    Jane, Doe
    ```    
    これを **c:\ADFv2QuickStartPSH** フォルダーに保存します。**ADFv2QuickStartPSH** フォルダーがまだ存在しない場合は作成してください。 
14. ツール バーの **[アップロード]** ボタンをクリックし、**[ファイルのアップロード]** を選択します。 

    ![[アップロード] ボタン](media/quickstart-create-data-factory-powershell/storage-explorer-upload-button.png)
15. **[ファイルのアップロード]** ウィンドウで、**[ファイル]** の [`...`] を選択します。 
16. **[Select folder to upload]\(アップロードするフォルダーの選択\)** ウィンドウで、**emp.txt** が格納されているフォルダーに移動し、ファイルを選択します。 

    ![ファイルのアップロード ダイアログ](media/quickstart-create-data-factory-powershell/storage-explorer-upload-files-dialog.png)
17. **[ファイルのアップロード]** ウィンドウで **[アップロード]** をクリックします。 

### <a name="azure-powershell"></a>Azure PowerShell

#### <a name="install-azure-powershell"></a>Azure PowerShell をインストールする
ご利用のマシンに最新の Azure PowerShell がまだインストールされていない場合はインストールします。 

1. Web ブラウザーで [Azure SDK のダウンロードと SDK](https://azure.microsoft.com/downloads/) に関するページに移動します。 
2. **[コマンドライン ツール]** -> **[PowerShell]** セクションの **[Windows のインストール]** をクリックします。 
3. Azure PowerShell をインストールするには、**MSI** ファイルを実行します。 

詳しい手順については、「 [Azure PowerShell のインストールおよび構成方法](/powershell/azure/install-azurerm-ps)」をご覧ください。 

#### <a name="log-in-to-azure-powershell"></a>Azure PowerShell へのログイン
お使いのマシンで **PowerShell** を起動します。 Azure PowerShell は、このクイックスタートが終わるまで開いたままにしておいてください。 Azure PowerShell を閉じて再度開いた場合は、これらのコマンドをもう一度実行する必要があります。

1. 次のコマンドを実行して、Azure Portal へのサインインに使用する Azure ユーザー名とパスワードを入力します。
       
    ```powershell
    Login-AzureRmAccount
    ```        
2. 複数の Azure サブスクリプションがある場合は、次のコマンドを実行して、このアカウントのすべてのサブスクリプションを表示します。

    ```powershell
    Get-AzureRmSubscription
    ```
3. 次のコマンドを実行して、使用するサブスクリプションを選択します。 **SubscriptionId** は、実際の Azure サブスクリプションの ID に置き換えてください。

    ```powershell
    Select-AzureRmSubscription -SubscriptionId "<SubscriptionId>"       
    ```

## <a name="create-a-data-factory"></a>Data Factory を作成する。
1. 後で PowerShell コマンドで使用できるように、リソース グループ名の変数を定義します。 次のコマンド テキストを PowerShell にコピーし、[Azure リソース グループ](../azure-resource-manager/resource-group-overview.md)の名前を二重引用符で囲んで指定し、コマンドを実行します。 
   
     ```powershell
    $resourceGroupName = "<Specify a name for the Azure resource group>";
    ```
2. 後で PowerShell コマンドで使用できるように、データ ファクトリ名の変数を定義します。 

    ```powershell
    $dataFactoryName = "<Specify a name for the data factory. It must be globally unique.>";
    ```
1. データ ファクトリの場所の変数を定義します。 

    ```powershell
    $location = "East US"
    ```
4. Azure リソース グループを作成するには、次のコマンドを実行します。 

    ```powershell
    New-AzureRmResourceGroup $resourceGroupName $location
    ``` 
    リソース グループが既に存在する場合、上書きしないようお勧めします。 異なる値を `$resourceGroupName` 変数に割り当てて、もう一度実行してください。 リソース グループを他のユーザーと共有する場合は、次の手順に進みます。 
5. データ ファクトリを作成するには、次の **Set-AzureRmDataFactoryV2** コマンドレットを実行します。 
    
    ```powershell       
    Set-AzureRmDataFactoryV2 -ResourceGroupName $resourceGroupName -Location "East US" -Name $dataFactoryName 
    ```

以下の点に注意してください。

* Azure Data Factory の名前はグローバルに一意にする必要があります。 次のエラーが発生した場合は、名前を変更してからもう一度実行してください。

    ```
    The specified Data Factory name 'ADFv2QuickStartDataFactory' is already in use. Data Factory names must be globally unique.
    ```

* Data Factory インスタンスを作成するには、Azure サブスクリプションの**共同作成者**または**管理者**である必要があります。
* 現在、Data Factory バージョン 2 でデータ ファクトリを作成できるリージョンは、米国東部、米国東部 2、および西ヨーロッパだけです。 データ ファクトリで使用するデータ ストア (Azure Storage、Azure SQL Database など) やコンピューティング (HDInsight など) は他のリージョンに配置できます。

## <a name="create-a-linked-service"></a>リンクされたサービスを作成する

データ ストアおよびコンピューティング サービスをデータ ファクトリにリンクするには、リンクされたサービスをデータ ファクトリに作成します。 このクイックスタートでは、ソース ストアとシンク ストアの両方として、Azure Storage のリンクされたサービスを 1 つ作成するだけで済みます。このサービスは、このサンプルでは "AzureStorageLinkedService" という名前です。

1. 以下の内容を記述した **AzureStorageLinkedService.json** という名前の JSON ファイルを **C:\ADFv2QuickStartPSH** フォルダー内に作成します (C:\ADFv2QuickStartPSH フォルダーがない場合は作成します)。 

    > [!IMPORTANT]
    > &lt;accountName&gt; と &lt;accountKey&gt; を実際の Azure ストレージ アカウントの名前とキーに置き換えてからファイルを保存してください。

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

ソースからシンクにコピーするデータを表すデータセットを定義します。 この例のこの BLOB データセットは、前の手順で作成した Azure Storage のリンクされたサービスを参照します。 データセットは、データセットを使用するアクティビティで設定された値を持つパラメーターを受け取ります。 パラメーターは、データの存在/格納場所を指す **folderPath** を構築するために使用されます。

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
  
この例では、このパイプラインには 1 つのアクティビティが含まれており、2 つのパラメーター (入力 BLOB パスと出力 BLOB パス) を受け取ります。 これらのパラメーターの値は、パイプラインがトリガー/実行されたときに設定されます。 コピー アクティビティは、入力と出力として、前の手順で作成された同じ BLOB データセットを使用します。 データセットが入力データセットとして使用される場合は、入力パスが指定されます。 また、データセットが出力データセットとして使用される場合は、出力パスが指定されます。 

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

    異なるコンテナーとフォルダーを使用している場合は、**inputPath** と **outputPath** の値を、ソースおよびシンクの BLOB パスに置き換えます。

    ```json
    {
        "inputPath": "adftutorial/input",
        "outputPath": "adftutorial/output"
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
            Write-Host  "Pipeline is running...status: InProgress" -foregroundcolor "Yellow"
        }

        Start-Sleep -Seconds 30
    }
    ```

    パイプラインの実行の出力例を次に示します。

    ```
    Pipeline is running...status: InProgress
    Pipeline run finished. The status is:  Succeeded
    
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : SPTestFactory0928
    RunId             : 0000000000-0000-0000-0000-0000000000000
    PipelineName      : Adfv2QuickStartPipeline
    LastUpdated       : 9/28/2017 8:28:38 PM
    Parameters        : {[inputPath, adftutorial/input], [outputPath, adftutorial/output]}
    RunStart          : 9/28/2017 8:28:14 PM
    RunEnd            : 9/28/2017 8:28:38 PM
    DurationInMs      : 24151
    Status            : Succeeded
    Message           :
    ```

2. 次のスクリプトを実行し、コピー アクティビティの実行の詳細 (たとえば、読み書きされたデータのサイズ) を取得します。

    ```powershell
    Write-Host "Activity run details:" -foregroundcolor "Yellow"
    $result = Get-AzureRmDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $runId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)
    $result
    
    Write-Host "Activity 'Output' section:" -foregroundcolor "Yellow"
    $result.Output -join "`r`n"
    
    Write-Host "\nActivity 'Error' section:" -foregroundcolor "Yellow"
    $result.Error -join "`r`n"
    ```
3. アクティビティの実行の結果である次のサンプル出力のような出力が表示されることを確認します。

    ```json
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : SPTestFactory0928
    ActivityName      : CopyFromBlobToBlob
    PipelineRunId     : 00000000000-0000-0000-0000-000000000000
    PipelineName      : Adfv2QuickStartPipeline
    Input             : {source, sink}
    Output            : {dataRead, dataWritten, copyDuration, throughput...}
    LinkedServiceName :
    ActivityRunStart  : 9/28/2017 8:28:18 PM
    ActivityRunEnd    : 9/28/2017 8:28:36 PM
    DurationInMs      : 18095
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}
    
    Activity 'Output' section:
    "dataRead": 38
    "dataWritten": 38
    "copyDuration": 7
    "throughput": 0.01
    "errors": []
    "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (West US)"
    "usedCloudDataMovementUnits": 2
    "billedDuration": 14
    ```

## <a name="verify-the-output"></a>出力を検証する
このパイプラインは、adftutorial BLOB コンテナーに対して output フォルダーを自動的に作成します。 そのうえで、input フォルダーから output フォルダーに emp.txt ファイルをコピーします。 [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) を使用して、inputBlobPath の BLOB が outputBlobPath にコピーされることを確認します。 

## <a name="clean-up-resources"></a>リソースのクリーンアップ
このクイックスタートで作成したリソースは、2 とおりの方法でクリーンアップすることができます。 [Azure リソース グループ](../azure-resource-manager/resource-group-overview.md)を削除した場合、そのリソース グループに含まれたすべてのリソースが対象となります。 他のリソースをそのまま維持する場合は、このチュートリアルで作成したデータ ファクトリだけを削除してください。

次のコマンドを実行して、リソース グループ全体を削除します。 
```powershell
Remove-AzureRmResourceGroup -ResourceGroupName $resourcegroupname
```

次のコマンドを実行して、データ ファクトリだけを削除します。 

```powershell
Remove-AzureRmDataFactoryV2 -Name $dataFactoryName -ResourceGroupName $resourceGroupName
```

## <a name="next-steps"></a>次のステップ
このサンプルのパイプラインは、Azure BLOB ストレージ内のある場所から別の場所にデータをコピーするものです。 より多くのシナリオで Data Factory を使用する方法については、[チュートリアル](tutorial-copy-data-dot-net.md)を参照してください。 
