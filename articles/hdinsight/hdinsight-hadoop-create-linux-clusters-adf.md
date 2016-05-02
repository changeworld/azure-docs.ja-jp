<properties
   pageTitle="Azure Data Factory を使用した HDInsight でのオンデマンドの Linux ベースの Hadoop クラスターの作成 | Microsoft Azure"
   	description="Azure Data Factory を使用してオンデマンドの HDInsight クラスターを作成する方法について説明します。"
   services="hdinsight"
   documentationCenter=""
   tags="azure-portal"
   authors="mumian"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="03/16/2016"
   ms.author="jgao"/>

# Azure Data Factory を使用した HDInsight でのオンデマンドの Linux ベースの Hadoop クラスターの作成

[AZURE.INCLUDE [セレクター](../../includes/hdinsight-selector-create-clusters.md)]

[Azure Data Factory](../data-factory/data-factory-introduction.md) は、データの移動や変換を調整および自動化する、クラウドベースのデータ統合サービスです。この記事では、Azure Data Factory を使用して [Azure HDInsight のオンデマンドのリンクされたサービス](../data-factory/data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service)を作成し、クラスターを使用して Hive ジョブを実行する方法について説明します。大まかな流れを次に示します。

1. オンデマンドで HDInsight クラスターを作成します。
2. 変換元の BLOB ストレージ アカウントから未加工の Web ログ データを読み取り、データを変換して、その出力を変換先の BLOB ストレージ アカウントに書き込むための Hive ジョブを実行します。 
3. Time to Live の設定に基づいてクラスターを削除します。

データ ファクトリ パイプラインに定義された Hive アクティビティは、定義済みの HiveQL スクリプトを呼び出します。このスクリプトは、Azure Blob Storage に格納されている未加工の Web ログ データを参照する外部テーブルを作成し、その後、年月別に未加工データを分割します。

入力ファイル内の各月のサンプル行を次に示します。

    2014-01-01,02:01:09,SAMPLEWEBSITE,GET,/blogposts/mvc4/step2.png,X-ARR-LOG-ID=2ec4b8ad-3cf0-4442-93ab-837317ece6a1,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,53175,871 
    2014-02-01,02:01:10,SAMPLEWEBSITE,GET,/blogposts/mvc4/step7.png,X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,30184,871
    2014-03-01,02:01:10,SAMPLEWEBSITE,GET,/blogposts/mvc4/step7.png,X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,30184,871

このスクリプトは、前の入力に基づいて 3 つの出力フォルダーを作成します。各フォルダーには、各月のエントリを含むファイルが格納されます。

    adfgetstarted/partitioneddata/year=2014/month=1/000000_0
    adfgetstarted/partitioneddata/year=2014/month=2/000000_0
    adfgetstarted/partitioneddata/year=2014/month=3/000000_0

Hive アクティビティと Data Factory データ変換アクティビティの一覧については、「[Azure Data Factory を使用した変換と分析](../data-factory/data-factory-data-transformation-activities.md)」を参照してください。

Data Factory と共に HDInsight を使用すると、次のような多くの利点があります。

- HDInsight クラスターは、使用されているかどうかにかかわらず、時間単位で課金されます。Data Factory を使用した場合、クラスターは必要に応じて作成されます。さらに、クラスターは、ジョブの完了時に自動的に削除されます。そのため、課金の対象となるのは、ジョブの実行時間と短いアイドル時間 (Time to Live) のみです。
- Data Factory パイプラインを使用してワークフローを作成できます。
- 再帰的なジョブをスケジュールできます。  

##前提条件:

この記事の手順を開始する前に、次の項目を用意する必要があります。

- [Azure サブスクリプション](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。
- [Azure CLI](../xplat-cli-install.md) または [Azure PowerShell](hdinsight-administer-use-powershell.md#install-azure-powershell-10-and-greater)。 

    [AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-powershell-and-cli.md)]

##ストレージ アカウントの準備

このシナリオでは、最大 3 つのストレージ アカウントを使用できます。

- HDInsight クラスターの既定のストレージ アカウント
- 入力データ用のストレージ アカウント
- 出力データ用のストレージ アカウント

ここでは、このチュートリアルを簡単にするために、この 3 つの目的に 1 つのストレージ アカウントを使用します。このセクションで紹介する Azure CLI と Azure PowerShell のサンプル スクリプトでは、次の処理を実行します。

1. Azure にログインします。
2. Azure リソース グループを作成します。
3. Azure ストレージ アカウントを作成します。
4. ストレージ アカウントに BLOB コンテナーを作成します。
5. BLOB コンテナーに次の 2 つのファイルをコピーします。

    - 入力データ ファイル: [https://hditutorialdata.blob.core.windows.net/adfhiveactivity/inputdata/input.log](https://hditutorialdata.blob.core.windows.net/adfhiveactivity/inputdata/input.log)
    - HiveQL スクリプト: [https://hditutorialdata.blob.core.windows.net/adfhiveactivity/script/partitionweblogs.hql](https://hditutorialdata.blob.core.windows.net/adfhiveactivity/script/partitionweblogs.hql)

    どちらのファイルも、パブリック BLOB コンテナーに格納されます。

>[AZURE.IMPORTANT] リソース グループ名、ストレージ アカウント名、スクリプトで使用されているストレージ アカウント キーをメモしておきます。これらは、次のセクションで必要になります。

**ストレージを準備してファイルをコピーするには (Azure CLI を使用)**

    azure login
    
    azure config mode arm

    azure group create --name "<Azure Resource Group Name>" --location "East US 2"

    azure storage account create --resource-group "<Azure Resource Group Name>" --location "East US 2" --type "LRS" <Azure Storage Account Name>

    azure storage account keys list --resource-group "<Azure Resource Group Name>" "<Azure Storage Account Name>"
    azure storage container create "adfgetstarted" --account-name "<Azure Storage AccountName>" --account-key "<Azure Storage Account Key>"

    azure storage blob copy start "https://hditutorialdata.blob.core.windows.net/adfhiveactivity/inputdata/input.log" --dest-account-name "<Azure Storage Account Name>" --dest-account-key "<Azure Storage Account Key>" --dest-container "adfgetstarted" 
    azure storage blob copy start "https://hditutorialdata.blob.core.windows.net/adfhiveactivity/script/partitionweblogs.hql" --dest-account-name "<Azure Storage Account Name>" --dest-account-key "<Azure Storage Account Key>" --dest-container "adfgetstarted" 

コンテナー名は *adfgetstarted* です。この名前はそのままにしておいてください。そうしないと、ARM テンプレートの更新が必要になります。

この CLI スクリプトに関してサポートが必要な場合は、「[Azure Storage での Azure CLI の使用](../storage/storage-azure-cli.md)」を参照してください。

**ストレージを準備してファイルをコピーするには (Azure PowerShell を使用)**

    $resourceGroupName = "<Azure Resource Group Name>"
    $storageAccountName = "<Azure Storage Account Name>"
    $location = "East US 2"

    $sourceStorageAccountName = "hditutorialdata"  
    $sourceContainerName = "adfhiveactivity"

    $destStorageAccountName = $storageAccountName
    $destContainerName = "adfgetstarted" # don't change this value.

    ####################################
    # Connect to Azure
    ####################################
    #region - Connect to Azure subscription
    Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
    try{Get-AzureRmContext}
    catch{Login-AzureRmAccount}
    #endregion

    ####################################
    # Create a resource group, storage, and container
    ####################################

    #region - create Azure resources
    Write-Host "`nCreating resource group, storage account and blob container ..." -ForegroundColor Green

    New-AzureRmResourceGroup -Name $resourceGroupName -Location $location 
    New-AzureRmStorageAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $destStorageAccountName `
        -type Standard_LRS `
        -Location $location 

    $destStorageAccountKey = Get-AzureRmStorageAccountKey `
        -ResourceGroupName $resourceGroupName `
        -Name $destStorageAccountName |  %{ $_.Key1 }

    $sourceContext = New-AzureStorageContext `
        -StorageAccountName $sourceStorageAccountName `
        -Anonymous
    $destContext = New-AzureStorageContext `
        -StorageAccountName $destStorageAccountName `
        -StorageAccountKey $destStorageAccountKey

    New-AzureStorageContainer -Name $destContainerName -Context $destContext
    #endregion

    ####################################
    # Copy files
    ####################################
    #region - copy files
    Write-Host "`nCopying files ..." -ForegroundColor Green

    $blobs = Get-AzureStorageBlob `
        -Context $sourceContext `
        -Container $sourceContainerName 

    $blobs|Start-AzureStorageBlobCopy `
        -DestContext $destContext `
        -DestContainer $destContainerName

    Write-Host "`nCopied files ..." -ForegroundColor Green
    Get-AzureStorageBlob -Context $destContext -Container $destContainerName 
    #endregion

    Write-host "`nYou will use the following values:" -ForegroundColor Green
    write-host "`nResource group name: $resourceGroupName"
    Write-host "Storage Account Name: $destStorageAccountName"
    write-host "Storage Account Key: $destStorageAccountKey"

    Write-host "`nScript completed" -ForegroundColor Green

この PowerShell スクリプトに関してサポートが必要な場合は、「[Azure Storage での Azure PowerShell の使用](../storage/storage-powershell-guide-full.md)」を参照してください。

**ストレージ アカウントとその内容を確認するには**

1. [Azure ポータル](https://portal.azure.com)にサインオンします。
2. 左側のウィンドウの **[リソース グループ]** をクリックします。
3. CLI または PowerShell スクリプトで作成したリソース グループの名前をダブルクリックします。一覧表示されるリソース グループが多すぎる場合は、フィルターを使用します。 
4. **[リソース]** タイルには、リソース グループを他のプロジェクトと共有している場合を除き、リソースが 1 つだけ表示されます。これが、前の手順で指定した名前のストレージ アカウントです。このストレージ アカウント名をクリックします。
5. **[BLOB]** タイルをクリックします。
6. **adfgetstarted** コンテナーをクリックします。"**入力データ**" と "**スクリプト**" の 2 つのフォルダーが表示されます。
7. フォルダーを開き、フォルダー内のファイルを確認します。
 
## データ ファクトリの作成

ストレージ アカウント、入力データ、および HiveQL スクリプトの準備ができたら、いつでも Azure データ ファクトリを作成できます。データ ファクトリを作成する方法はいくつかあります。このチュートリアルでは、Azure ポータルを使用してカスタム ARM テンプレートを呼び出します。ARM テンプレートは、[Azure CLI](../resource-group-template-deploy.md#deploy-with-azure-cli-for-mac-linux-and-windows) や [Azure PowerShell](../resource-group-template-deploy.md#deploy-with-powershell) から呼び出すこともできます。他のデータ ファクトリの作成方法については、「[チュートリアル: 初めての Data Factory の作成](../data-factory/data-factory-build-your-first-pipeline.md)」を参照してください。

最上位レベルの ARM テンプレートの内容を次に示します。

    {
        "contentVersion": "1.0.0.0",
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "parameters": { ...
        },
        "variables": { ...
        },
        "resources": [
            {
                "name": "[parameters('dataFactoryName')]",
                "apiVersion": "[variables('apiVersion')]",
                "type": "Microsoft.DataFactory/datafactories",
                "location": "westus",
                "resources": [
                    { ... },
                    { ... },
                    { ... },
                    { ... }
                ]
            }
        ]
    }

ここには、*hdinsight-hive-on-demand* という名前の Data Factory リソースが 1 つ含まれています (この名前はスクリーンショットに表示されていません)。データ ファクトリは、現在、米国西部リージョンと北ヨーロッパ リージョンでのみサポートされています。

*hdinsight-hive-on-demand* リソースには、次の 4 つのリソースが含まれています。

- 既定の HDInsight ストレージ アカウント、入力データ ストレージ、および出力データ ストレージとして使用されるストレージ アカウントに対する linkedservice。
- 作成する HDInsight クラスターに対する linkedservice。

        {
            "dependsOn": [ ... ],
            "type": "linkedservices",
            "name": "[variables('hdInsightOnDemandLinkedServiceName')]",
            "apiVersion": "[variables('apiVersion')]",
            "properties": {
                "type": "HDInsightOnDemand",
                "typeProperties": {
                    "osType": "linux",
                    "version": "3.2",
                    "clusterSize": 1,
                    "sshUserName": "myuser",                            
                    "sshPassword": "MyPassword!",
                    "timeToLive": "00:30:00",
                    "linkedServiceName": "[variables('storageLinkedServiceName')]"
                }
            }
        },

    指定されていない場合でも、クラスターはストレージ アカウントと同じリージョンに作成されます。
    
    *timeToLive* 設定に注目してください。データ ファクトリは、クラスターがアイドル状態になってから 30 分経過するとそのクラスターを自動的に削除します。
- 入力データのデータセット。ファイル名とフォルダー名をここで定義します。

        "fileName": "input.log",
        "folderPath": "adfgetstarted/inputdata",
        
- 出力データのデータセットと、データ処理用のパイプライン。出力パスをここで定義します。
        
        "folderPath": "adfgetstarted/partitioneddata",

    [データセットの可用性](../data-factory/data-factory-create-datasets.md#Availability)の設定は次のとおりです。
    
        "availability": {
            "frequency": "Month",
            "interval": 1,
            "style": "EndOfInterval"
        },

    Azure Data Factory では、出力データセットの可用性によってパイプラインが動作するようになります。これは、毎月の最終日にスライスが生成されることを意味します。詳細については、「[Data Factory を使用したスケジュール設定と実行](../data-factory/data-factory-scheduling-and-execution.md)」を参照してください。

    パイプラインの定義は次のとおりです。
    
        {
            "dependsOn": [ ... ],
            "type": "datapipelines",
            "name": "[parameters('dataFactoryName')]",
            "apiVersion": "[variables('apiVersion')]",
            "properties": {
                "description": "Azure Data Factory pipeline with an Hadoop Hive activity",
                "activities": [
                    { ...}
                ],
                "start": "2016-01-01T00:00:00Z",
                "end": "2016-01-31T00:00:00Z",
                "isPaused": false
            }
        }
                
    これには 1 つのアクティビティが含まれています。アクティビティの *start* と *end* の両方に過去の日付が設定されています。これは、スライスが 1 つにしかならないことを意味します。end に将来の日付が設定されている場合は、そのときが来ると、データ ファクトリが新たなスライスを作成します。詳細については、「[Data Factory を使用したスケジュール設定と実行](../data-factory/data-factory-scheduling-and-execution.md)」を参照してください。

    次に、アクティビティの定義を示します。
    
        "activities": [
            {
                "type": "HDInsightHive",
                "typeProperties": {
                    "scriptPath": "adfgetstarted/script/partitionweblogs.hql",
                    "scriptLinkedService": "[variables('storageLinkedServiceName')]",
                    "defines": {
                        "inputtable": "[concat('wasb://adfgetstarted@', parameters('storageAccountName'), '.blob.core.windows.net/inputdata')]",
                        "partitionedtable": "[concat('wasb://adfgetstarted@', parameters('storageAccountName'), '.blob.core.windows.net/partitioneddata')]"
                    }
                },
                "inputs": [
                    {
                        "name": "AzureBlobInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutput"
                    }
                ],
                "policy": {
                    "concurrency": 1,
                    "retry": 3
                },
                "name": "RunSampleHiveActivity",
                "linkedServiceName": "HDInsightOnDemandLinkedService"
            }
        ],
    
    入力、出力、およびスクリプトのパスが定義されています。
    
**データ ファクトリを作成するには**

1. 次の画像をクリックして Azure にサインインし、Azure ポータルで ARM テンプレートを開きます。テンプレートは、https://hditutorialdata.blob.core.windows.net/adfhiveactivity/data-factory-hdinsight-on-demand.json にあります。 

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Fadfhiveactivity%2Fdata-factory-hdinsight-on-demand.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. 前のセクションで作成したアカウントの **DATAFACTORYNAME**、**STORAGEACCOUNTNAME**、**STORAGEACCOUNTKEY** を入力し、**[OK]** をクリックします。データ ファクトリ名は、グローバルに一意である必要があります。
3. **[リソース グループ]** で、前のセクションで使用したのと同じリソース グループを選択します。
4. **[法律条項]** をクリックし、**[作成]** をクリックします。
5. **[作成]** をクリックします。ダッシュボードに "**テンプレートのデプロイをデプロイしています**" というタイルが表示されます。タイルのテキストがリソース グループの名前に変わるまで待ちます。通常、HDInsight クラスターの作成には約 20 分かかります。
6. そのタイルをクリックしてリソース グループを開きます。これで、ストレージ アカウント リソースのほかに、もう 1 つデータ ファクトリ リソースが表示されます。
7. **[hdinsight-hive-on-demand]** をクリックします。
8. **[ダイアグラム]** タイルをクリックします。このダイアグラムには、1 つの入力データセットと 1 つの出力データセットを持つ 1 つのアクティビティが示されます。

    ![Azure Data Factory HDInsight on demand hive activity pipeline diagram](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-adf-pipeline-diagram.png)
    
    名前は、ARM テンプレートで定義されています。
9. **[AzureBlobOutput]** をダブルクリックします。
10. **[最近更新されたスライス]** に、1 つのスライスが表示されます。状態が **[処理中]** の場合は、**[準備完了]** に変わるまで待ちます。

**データ ファクトリの出力を確認するには**

1. 前のセクションと同じ手順を使用して、adfgetstarted コンテナーの内容を確認します。**adfgetsarted** に加えて、次の 2 つの新しいコンテナーがあります。

    - adfhdinsight-hive-on-demand-hdinsightondemandlinked-xxxxxxxxxxxxx: これは、HDInsight クラスターの既定のコンテナーです。既定のコンテナー名は、"adf<データ ファクトリ名>-<リンクされたサービス名>-<日時スタンプ>" というパターンになります。 
    - adfjobs: これは、ADF ジョブ ログのコンテナーです。
    
    データ ファクトリの出力は、ARM テンプレートに構成されている afgetstarted に格納されます。 
2. **[adfgetstarted]** をクリックします。
3. **[partitioneddata]** をダブルクリックします。**year=2014** フォルダーが表示されます。これは、すべての Web ログの日付が 2014 年であるためです。 

    ![Azure Data Factory HDInsight on demand hive activity pipeline output](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-adf-output-year.png)

    一覧を展開すると、1 月、2 月、および 3 月に対応する 3 つのフォルダーが表示されます。さらに、月ごとのログがあります。

    ![Azure Data Factory HDInsight on demand hive activity pipeline output](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-adf-output-month.png)

##このチュートリアルのまとめ

オンデマンド HDInsight のリンクされたサービスでは、既存のライブ クラスター (timeToLive) がある場合を除き、スライスを処理する必要があるたびに HDInsight クラスターが作成され、処理が終了するとそのクラスターが削除されます。Azure Data Factory は、クラスターごとに、クラスターの既定のファイル システムとして使用される Azure Blob Storage を作成します。HDInsight クラスターが削除されても、既定の BLOB ストレージ コンテナーとそれに関連付けられたストレージ アカウントは削除されません。これは設計によるものです。処理されるスライスが多いほど、Azure Blob Storage 内のコンテナーも増えます。ジョブのトラブルシューティングのためにコンテナーが必要ない場合、コンテナーを削除してストレージ コストを削減できます。これらのコンテナーの名前は、"adf<データ ファクトリ名>-<リンクされたサービス名>-<日時スタンプ>" というパターンになります。

[Azure Resource Manager](../resource-group-overview.md) は、ソリューションをグループとしてデプロイ、管理、および監視するために使用します。リソース グループを削除すると、そのグループ内のコンポーネントがすべて削除されます。

**リソース グループを削除するには**

1. [Azure ポータル](https://portal.azure.com)にサインオンします。
2. 左側のウィンドウの **[リソース グループ]** をクリックします。
3. CLI または PowerShell スクリプトで作成したリソース グループの名前をダブルクリックします。一覧表示されるリソース グループが多すぎる場合は、フィルターを使用します。新しいブレードでリソース グループが開かれます。
4. **[リソース]** タイルには、リソース グループを他のプロジェクトと共有している場合を除き、既定のストレージ アカウントとデータ ファクトリが表示されます。
5. ブレードの上部にある **[削除]** をクリックします。この操作を実行すると、ストレージ アカウントと、そのストレージ アカウントに格納されているデータも削除されます。
6. リソース グループ名を入力し、**[削除]** をクリックします。

リソース グループを削除する際にストレージ アカウントを削除したくない場合は、ビジネス データを既定のストレージ アカウントから切り離した、次のアーキテクチャの設計を検討してください。この場合、ビジネス データを含むストレージ アカウント用のリソース グループのほかに、既定のストレージ アカウントとデータ ファクトリ用のリソース グループを用意することになります。2 番目のリソース グループを削除しても、ビジネス データのストレージ アカウントには影響しません。そのためには、次の手順を実行します。

- ARM テンプレートの最上位レベルのリソース グループに Microsoft.DataFactory/datafactories リソースと共に次の内容を追加します。これにより、新しいストレージ アカウントが作成されます。

        {
            "name": "[parameters('defaultStorageAccountName')]",
            "type": "Microsoft.Storage/storageAccounts",
            "location": "[parameters('location')]",
            "apiVersion": "[variables('defaultApiVersion')]",
            "dependsOn": [ ],
            "tags": {

            },
            "properties": {
                "accountType": "Standard_LRS"
            }
        },

- 新しいストレージ アカウントに新しいリンクされたサービス ポイントを追加します。

        {
            "dependsOn": [ "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'))]" ],
            "type": "linkedservices",
            "name": "[variables('defaultStorageLinkedServiceName')]",
            "apiVersion": "[variables('apiVersion')]",
            "properties": {
                "type": "AzureStorage",
                "typeProperties": {
                    "connectionString": "[concat('DefaultEndpointsProtocol=https;AccountName=',parameters('defaultStorageAccountName'),';AccountKey=',listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('defaultStorageAccountName')), variables('defaultApiVersion')).key1)]"
                }
            }
        },
    
- 追加の dependsOn と additionalLinkedServiceNames を使用して、HDInsight のオンデマンドのリンクされたサービスを構成します。

        {
            "dependsOn": [
                "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'))]",
                "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'), '/linkedservices/', variables('defaultStorageLinkedServiceName'))]",
                "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'), '/linkedservices/', variables('storageLinkedServiceName'))]"
                
            ],
            "type": "linkedservices",
            "name": "[variables('hdInsightOnDemandLinkedServiceName')]",
            "apiVersion": "[variables('apiVersion')]",
            "properties": {
                "type": "HDInsightOnDemand",
                "typeProperties": {
                    "osType": "linux",
                    "version": "3.2",
                    "clusterSize": 1,
                    "sshUserName": "myuser",                            
                    "sshPassword": "MyPassword!",
                    "timeToLive": "00:30:00",
                    "linkedServiceName": "[variables('storageLinkedServiceName')]",
                    "additionalLinkedServiceNames": "[variables('defaultStorageLinkedServiceName')]"
                }
            }
        },            

##次のステップ
この記事では、Azure Data Factory を使用して Hive ジョブを処理するオンデマンドの HDInsight クラスターを作成する方法について説明しました。詳細については、以下のリンク先を参照してください。

- [Hadoop チュートリアル: HDInsight で Linux ベースの Hadoop を使用する](hdinsight-hadoop-linux-tutorial-get-started.md)
- [HDInsight での Linux ベースの Hadoop クラスターの作成](hdinsight-hadoop-provision-linux-clusters.md)
- [HDInsight のドキュメント](https://azure.microsoft.com/documentation/services/hdinsight/)
- [データ ファクトリのドキュメント](https://azure.microsoft.com/documentation/services/data-factory/)

<!---HONumber=AcomDC_0420_2016-->