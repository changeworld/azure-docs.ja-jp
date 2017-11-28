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
ms.date: 11/16/2017
ms.author: jingwang
ms.openlocfilehash: 254dcb6642afc19f434df837c9073d2dd7314313
ms.sourcegitcommit: 1d8612a3c08dc633664ed4fb7c65807608a9ee20
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/20/2017
---
# <a name="create-an-azure-data-factory-using-powershell"></a>PowerShell を使用した Azure データ ファクトリの作成 
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [バージョン 1 - 一般公開](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [バージョン 2 - プレビュー](quickstart-create-data-factory-powershell.md)

このクイックスタートでは、PowerShell を使用して Azure データ ファクトリを作成する方法について説明します。 このデータ ファクトリに作成されたパイプラインは、データを Azure BLOB ストレージ内のあるフォルダーから別のフォルダーに**コピー**します。 Azure Data Factory を使用してデータを**変換**する方法のチュートリアルについては、[Spark を使用したデータ変換のチュートリアル](transform-data-using-spark.md)を参照してください。 

> [!NOTE]
> この記事は、現在プレビュー段階にある Data Factory のバージョン 2 に適用されます。 一般公開 (GA) されている Data Factory サービスのバージョン 1 を使用している場合は、[Data Factory バージョン 1 の使用](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)に関する記事をご覧ください。
>
> この記事では、Data Factory サービスの概要については詳しく取り上げません。 Azure Data Factory サービスの概要については、「[Azure Data Factory の概要](introduction.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

### <a name="azure-subscription"></a>Azure サブスクリプション
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。

### <a name="azure-roles"></a>Azure ロール
Data Factory インスタンスを作成するには、Azure へのログインに使用するユーザー アカウントが、**共同作成者**または**所有者**ロールのメンバーであるか、Azure サブスクリプションの**管理者**である必要があります。 サブスクリプションで自分が持っているアクセス許可を表示するには、Azure Portal で右上隅にある**ユーザー名**をクリックし、**[アクセス許可]** を選択します。 複数のサブスクリプションへのアクセス権がある場合は、適切なサブスクリプションを選択します。 ロールにユーザーを追加するサンプル手順については、[ロールの追加](../billing/billing-add-change-azure-subscription-administrator.md)に関する記事を参照してください。

### <a name="azure-storage-account"></a>Azure Storage アカウント
このクイックスタートでは、**ソース** データ ストアと**コピー先**データ ストアの両方に汎用の Azure Storage アカウント (具体的には Blob Storage) を使用します。 汎用の Azure Storage アカウントがない場合、作成方法については、「[ストレージ アカウントの作成](../storage/common/storage-create-storage-account.md#create-a-storage-account)」をご覧ください。 

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
このセクションでは、**adftutorial** という名前の BLOB コンテナーを Azure Blob ストレージに作成します。 次に、そのコンテナーに **input** という名前のフォルダーを作成し、input フォルダーにサンプル ファイルをアップロードします。 

1. **[ストレージ アカウント]** ページで **[概要]** に切り替え、**[BLOB]** をクリックします。 

    ![BLOB オプションを選択する](media/quickstart-create-data-factory-powershell/select-blobs.png)
2. **[Blob service]** ページのツール バーで、**[+ コンテナー]** をクリックします。 

    ![コンテナーの追加ボタン](media/quickstart-create-data-factory-powershell/add-container-button.png)    
3. **[新しいコンテナー]** ダイアログ ボックスで、名前に「**adftutorial**」と入力し、**[OK]** をクリックします。 

    ![コンテナー名を入力する](media/quickstart-create-data-factory-powershell/new-container-dialog.png)
4. コンテナーの一覧で、**[adftutorial]** をクリックします。 

    ![コンテナーを選択する](media/quickstart-create-data-factory-powershell/seelct-adftutorial-container.png)
1. **[コンテナー]** ページのツール バーで、**[アップロード]** をクリックします。  

    ![[アップロード] ボタン](media/quickstart-create-data-factory-powershell/upload-toolbar-button.png)
6. **[BLOB のアップロード]** ページで、**[詳細設定]** をクリックします。

    ![詳細設定リンクをクリックする](media/quickstart-create-data-factory-powershell/upload-blob-advanced.png)
7. **メモ帳**を開き、以下の内容を含む **emp.txt** という名前のファイルを作成します。それを **c:\ADFv2QuickStartPSH** フォルダーに保存します。**ADFv2QuickStartPSH** フォルダーがない場合は作成します。
    
    ```
    John, Doe
    Jane, Doe
    ```    
8. Azure Portal の **[BLOB のアップロード]** ページの **[ファイル]** フィールドで、**emp.txt** ファイルを探して選択します。 
9. **[アップロード先のフォルダー]** フィールドの値として、「**input**」と入力します。 

    ![BLOB のアップロードの設定](media/quickstart-create-data-factory-powershell/upload-blob-settings.png)    
10. フォルダーが **input** で、ファイルが **emp.txt** であることを確認し、**[アップロード]** をクリックします。
11. 一覧に **emp.txt** ファイルとアップロードの状態が表示されます。 
12. 隅の **[X]** をクリックして、**[BLOB のアップロード]** ページを閉じます。 

    ![BLOB のアップロード ページを閉じる](media/quickstart-create-data-factory-powershell/close-upload-blob.png)
1. **[コンテナー]** ページを開いたままにしておきます。 このクイックスタートの最後で、このページを使用して出力を確認します。 

### <a name="azure-powershell"></a>Azure PowerShell

#### <a name="install-azure-powershell"></a>Azure PowerShell をインストールする
ご利用のマシンに最新の Azure PowerShell がまだインストールされていない場合はインストールします。 

1. Web ブラウザーで [Azure SDK のダウンロードと SDK](https://azure.microsoft.com/downloads/) に関するページに移動します。 
2. **[コマンドライン ツール]** -> **[PowerShell]** セクションの **[Windows のインストール]** をクリックします。 
3. Azure PowerShell をインストールするには、**MSI** ファイルを実行します。 

詳しい手順については、「 [Azure PowerShell のインストールおよび構成方法](/powershell/azure/install-azurerm-ps)」をご覧ください。 

#### <a name="log-in-to-azure-powershell"></a>Azure PowerShell へのログイン

1. お使いのマシンで **PowerShell** を起動します。 Azure PowerShell は、このクイックスタートが終わるまで開いたままにしておいてください。 Azure PowerShell を閉じて再度開いた場合は、これらのコマンドをもう一度実行する必要があります。

    ![PowerShell を起動する](media/quickstart-create-data-factory-powershell/search-powershell.png)
1. 次のコマンドを実行して、Azure Portal へのサインインに使用するのと同じ Azure ユーザー名とパスワードを入力します。
       
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
1. 後で PowerShell コマンドで使用できるように、リソース グループ名の変数を定義します。 次のコマンド テキストを PowerShell にコピーし、[Azure リソース グループ](../azure-resource-manager/resource-group-overview.md)の名前を二重引用符で囲んで指定し、コマンドを実行します。 (例: `"adfrg"`)。
   
     ```powershell
    $resourceGroupName = "<Specify a name for the Azure resource group>";
    ```
2. データ ファクトリ名の変数を定義します。 

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
    リソース グループが既に存在する場合、上書きしないようお勧めします。 `$resourceGroupName` 変数に別の値を割り当てて、コマンドをもう一度実行します。 
5. データ ファクトリを作成するには、次の **Set-AzureRmDataFactoryV2** コマンドレットを実行します。 
    
    ```powershell       
    Set-AzureRmDataFactoryV2 -ResourceGroupName $resourceGroupName -Location "East US" -Name $dataFactoryName 
    ```

以下の点に注意してください。

* Azure Data Factory の名前はグローバルに一意にする必要があります。 次のエラーが発生した場合は、名前を変更してからもう一度実行してください。

    ```
    The specified Data Factory name 'ADFv2QuickStartDataFactory' is already in use. Data Factory names must be globally unique.
    ```
* Data Factory インスタンスを作成するには、Azure へのログインに使用するユーザー アカウントが、**共同作成者**または**所有者**ロールのメンバーであるか、Azure サブスクリプションの**管理者**である必要があります。
* 現在、Data Factory バージョン 2 でデータ ファクトリを作成できるリージョンは、米国東部、米国東部 2、および西ヨーロッパだけです。 データ ファクトリで使用するデータ ストア (Azure Storage、Azure SQL Database など) やコンピューティング (HDInsight など) は他のリージョンに配置できます。

## <a name="create-a-linked-service"></a>リンクされたサービスを作成する

データ ストアおよびコンピューティング サービスをデータ ファクトリにリンクするには、リンクされたサービスをデータ ファクトリに作成します。 このクイックスタートでは、ソース ストアとシンク ストアの両方として使用される Azure Storage のリンクされたサービスを作成します。 リンクされたサービスは、Data Factory サービスが実行時に接続するために使用する接続情報を持っています。

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
                    "value": "DefaultEndpointsProtocol=https;AccountName=<accountName>;AccountKey=<accountKey>;EndpointSuffix=core.windows.net",
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
この手順では、ソースからシンクにコピーするデータを表すデータセットを定義します。 データセットの型は、**AzureBlob** です。 前の手順で作成した **Azure Storage のリンクされたサービス**を参照します。 **folderPath** プロパティを構築するために、パラメーターを受け取ります。 入力データセットでは、パイプライン内のコピー アクティビティが、このパラメーターの値として入力パスを渡します。 同様に、出力データセットでは、コピー アクティビティがこのパラメーターの値として出力パスを渡します。 

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
  
このクイックスタートでは、2 つのパラメーター (入力 BLOB パスと出力 BLOB パス) を受け取る 1 つのアクティビティを持つパイプラインを作成します。 これらのパラメーターの値は、パイプラインがトリガー/実行されたときに設定されます。 コピー アクティビティは、入力と出力として、前の手順で作成された同じ BLOB データセットを使用します。 データセットが入力データセットとして使用される場合は、入力パスが指定されます。 また、データセットが出力データセットとして使用される場合は、出力パスが指定されます。 

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

    ```json
    {
        "inputPath": "adftutorial/input",
        "outputPath": "adftutorial/output"
    }
    ```
2. **Invoke-AzureRmDataFactoryV2Pipeline** コマンドレットを実行し、パイプラインの実行を作成し、パラメーターの値を渡します。 コマンドレットは、将来の監視のために、パイプラインの実行 ID を返します。

    ```powershell
    $runId = Invoke-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineName "Adfv2QuickStartPipeline" -ParameterFile .\PipelineParameters.json
    ```

## <a name="monitor-the-pipeline-run"></a>パイプラインの実行を監視します

1. 次の PowerShell スクリプトを実行し、データのコピーが完了するまで、パイプラインの実行の状態を継続的にチェックします。 次のスクリプトをコピーして PowerShell ウィンドウに貼り付け、Enter キーを押します。 

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

        Start-Sleep -Seconds 10
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

    次のようなエラーが表示された場合:
    ```
    Activity CopyFromBlobToBlob failed: Failed to detect region of linked service 'AzureStorage' : 'AzureStorageLinkedService' with error '[Region Resolver] Azure Storage failed to get address for DNS. Warning: System.Net.Sockets.SocketException (0x80004005): No such host is known
    ```
    次の手順を実行します。 
    1. AzureStorageLinkedService.json で、Azure Storage アカウントの名前とキーが正しいことを確認します。 
    2. 接続文字列の形式が正しいことを確認します。 たとえば、AccountName プロパティと AccountKey プロパティは、セミコロン (`;`) 文字で区切ります。 
    3. アカウント名とアカウント キーが山かっこで囲まれていたら、それらを削除します。 
    4. 接続文字列の例を次に示します。 

        ```json
        "connectionString": {
            "value": "DefaultEndpointsProtocol=https;AccountName=mystorageaccountname;AccountKey=mystorageacountkey;EndpointSuffix=core.windows.net",
            "type": "SecureString"
        }
        ```
    5. 「[リンクされたサービスを作成する](#create-a-linked-service)」セクションの手順に従って、リンクされたサービスを再作成します。 
    6. 「[パイプラインの実行を作成する](#create-a-pipeline-run)」セクションの手順に従って、パイプラインを再実行します。 
    7. 現在の監視コマンドを再実行して、新しいパイプライン実行を監視します。 
1. 次のスクリプトを実行し、コピー アクティビティの実行の詳細 (たとえば、読み書きされたデータのサイズ) を取得します。

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
このパイプラインは、adftutorial BLOB コンテナーに対して output フォルダーを自動的に作成します。 そのうえで、input フォルダーから output フォルダーに emp.txt ファイルをコピーします。 

1. Azure Portal の **adftutorial** コンテナー ページで **[最新の情報に更新]** をクリックして output フォルダーを表示します。 
    
    ![更新](media/quickstart-create-data-factory-powershell/output-refresh.png)
2. フォルダー一覧の **[output]** をクリックします。 
2. **emp.txt** が output フォルダーにコピーされていることを確認します。 

    ![更新](media/quickstart-create-data-factory-powershell/output-file.png)

## <a name="clean-up-resources"></a>リソースのクリーンアップ
このクイックスタートで作成したリソースは、2 とおりの方法でクリーンアップすることができます。 [Azure リソース グループ](../azure-resource-manager/resource-group-overview.md)を削除した場合、そのリソース グループに含まれたすべてのリソースが対象となります。 他のリソースをそのまま維持する場合は、このチュートリアルで作成したデータ ファクトリだけを削除してください。

リソース グループを削除すると、その中のデータ ファクトリも含めて、すべてのリソースが削除されます。 次のコマンドを実行して、リソース グループ全体を削除します。 
```powershell
Remove-AzureRmResourceGroup -ResourceGroupName $resourcegroupname
```

リソース グループ全体ではなく、データ ファクトリだけを削除する場合は、次のコマンドを実行します。 

```powershell
Remove-AzureRmDataFactoryV2 -Name $dataFactoryName -ResourceGroupName $resourceGroupName
```

## <a name="next-steps"></a>次のステップ
このサンプルのパイプラインは、Azure BLOB ストレージ内のある場所から別の場所にデータをコピーするものです。 より多くのシナリオで Data Factory を使用する方法については、[チュートリアル](tutorial-copy-data-dot-net.md)を参照してください。 
