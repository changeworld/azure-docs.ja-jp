---
title: Azure PowerShell を使用して Stream Analytics ジョブを作成する
description: このクイック スタートでは、Azure PowerShell モジュールを使用して Azure Stream Analytics ジョブをデプロイおよび実行する方法について詳しく説明します。
services: stream-analytics
author: sidramadoss
ms.author: sidram
ms.date: 05/14/2018
ms.topic: quickstart
ms.service: stream-analytics
ms.custom: mvc
manager: kfile
ms.openlocfilehash: 18903dfbe187de73a6edb14196fa29e02d35dbca
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/20/2018
ms.locfileid: "39185382"
---
# <a name="quickstart-create-a-stream-analytics-job-by-using-azure-powershell"></a>クイック スタート: Azure PowerShell を使用して Stream Analytics ジョブを作成する

Azure PowerShell モジュールは、PowerShell コマンドレットやスクリプトで Azure リソースを作成および管理するために使用します。 このクイック スタートでは、Azure PowerShell モジュールを使用して Azure Stream Analytics ジョブをデプロイおよび実行する方法について詳しく説明します。 
 
サンプル ジョブによって、Azure Blob Storage の BLOB からストリーミング データが読み取られます。 このクイック スタートで使用される入力データ ファイルには、わかりやすくするために静的なデータが含まれています。 実際のシナリオでは、Stream Analytics ジョブの入力データをストリーミングします。 次に、Stream Analytics クエリ言語を使用したジョブによってデータが変換され、100 度を越えた場合の平均温度が計算されます。 最後に、結果の出力イベントが別のファイルに書き込まれます。 

## <a name="before-you-begin"></a>開始する前に

* Azure サブスクリプションをお持ちでない場合は、[無料アカウント](https://azure.microsoft.com/free/)を作成してください。  

* このクイック スタートには、Azure PowerShell モジュール バージョン 3.6 以降が必要です。 ローカル マシンにインストールされているバージョンを調べるには、`Get-Module -ListAvailable AzureRM` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](https://docs.microsoft.com/powershell/azure/install-azurerm-ps)に関する記事を参照してください。 


## <a name="sign-in-to-azure"></a>Azure へのサインイン

`Connect-AzureRmAccount` コマンドを使用して Azure サブスクリプションにログインし、ポップアップ ブラウザーに Azure 資格情報を入力します。

```powershell
# Log in to your Azure account
Connect-AzureRmAccount
```

サインインした後、複数のサブスクリプションがある場合は、次のコマンドレットを実行して、このクイック スタートに使用するサブスクリプションを選択します。 <your subscription name> は、実際のサブスクリプションの名前に置き換えてください。  

```powershell
# List all available subscriptions.
Get-AzureRmSubscription

# Select the Azure subscription you want to use to create the resource group and resources.
Get-AzureRmSubscription -SubscriptionName "<your subscription name>" | Select-AzureRmSubscription
```

## <a name="create-a-resource-group"></a>リソース グループの作成

[New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroup) を使用して Azure リソース グループを作成します。 リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。

```powershell
$resourceGroup = "StreamAnalyticsRG"
$location = "WestUS2"
New-AzureRmResourceGroup `
   -Name $resourceGroup `
   -Location $location 
```

## <a name="prepare-the-input-data"></a>入力データを準備する

Stream Analytics ジョブを定義する前に、ジョブへの入力として構成されるデータを準備します。

1. GitHub から[センサー サンプル データ](https://raw.githubusercontent.com/Azure/azure-stream-analytics/master/Samples/GettingStarted/HelloWorldASA-InputStream.json)をダウンロードします。 リンクを右クリックして、**[名前を付けてリンク先を保存]** または **[対象をファイルに保存]** を選択します。

2. 次の PowerShell コード ブロックでは、ジョブに必要な入力データを準備するための複数のコマンドが実行されます。 コードを理解するためにセクションを確認してください。 

   1. [New-AzureRmStorageAccount](https://docs.microsoft.com/powershell/module/azurerm.storage/New-AzureRmStorageAccount) コマンドレットを使用して、標準の汎用ストレージ アカウントを作成します。  この例では、ローカル冗長ストレージ (LRS) と BLOB 暗号化機能 (既定で有効になります) を持つ mystorageaccount というストレージ アカウントを作成します。  

   2. 使用されるストレージ アカウントを定義するストレージ アカウント コンテキスト `$storageAccount.Context` を取得します。 ストレージ アカウントを操作するとき、資格情報を繰り返し入力する代わりに、このコンテキストを参照します。 

   3. [New-AzureStorageContainer](https://docs.microsoft.com/powershell/module/azure.storage/new-azurestoragecontainer) を使用してストレージ コンテナーを作成します。また、先ほどダウンロードした[センサー サンプル データ](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/GettingStarted/HelloWorldASA-InputStream.json)をアップロードします。 

   4. コードによって出力されたストレージ キーをコピーし、そのキーを JSON ファイルに貼り付けます。これは、後でストリーミング ジョブの入力と出力を作成するために使用します。

   ```powershell
   $storageAccountName = "mystorageaccount"
   $storageAccount = New-AzureRmStorageAccount `
     -ResourceGroupName $resourceGroup `
     -Name $storageAccountName `
     -Location $location `
     -SkuName Standard_LRS `
     -Kind Storage
   
   $ctx = $storageAccount.Context
   $containerName = "streamanalytics"
   
   New-AzureStorageContainer `
     -Name $containerName `
     -Context $ctx
   
   Set-AzureStorageBlobContent `
     -File "c:\HelloWorldASA-InputStream.json" `
     -Blob "input/HelloWorldASA-InputStream.json" `
     -Container $containerName `
     -Context $ctx  
   
   $storageAccountKey = (Get-AzureRmStorageAccountKey `
     -ResourceGroupName $resourceGroup `
     -Name $storageAccountName).Value[0]
   
   Write-Host "The <storage account key> placeholder needs to be replaced in your input and output json files with this key value:" 
   Write-Host $storageAccountKey -ForegroundColor Cyan
   ```

## <a name="create-a-stream-analytics-job"></a>Stream Analytics のジョブの作成

[New-AzureRmStreamAnalyticsJob](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/new-azurermstreamanalyticsjob?view=azurermps-5.4.0) コマンドレットを使用して、Stream Analytics ジョブを作成します。 このコマンドレットは、パラメーターとして、ジョブ名、リソース グループ名、およびジョブ定義を受け取ります。 ジョブ名は、ジョブを識別する任意のフレンドリ名にすることができます。 これには英数字、ハイフン、アンダースコアのみを使用できます。長さは 3 文字以上 63 文字以下でなければなりません。 ジョブ定義は、ジョブの作成に必要なプロパティを含む JSON ファイルです。 ローカル マシンに `JobDefinition.json` という名前のファイルを作成し、次の JSON データを追加します。

```json
{    
   "location":"WestUS2",  
   "properties":{    
      "sku":{    
         "name":"standard"  
      },  
      "eventsOutOfOrderPolicy":"adjust",  
      "eventsOutOfOrderMaxDelayInSeconds":10,  
      "compatibilityLevel": 1.1
   }
}
```

次に、`New-AzureRmStreamAnalyticsJob` コマンドレットを実行します。 `jobDefinitionFile` 変数の値は、ジョブ定義 JSON ファイルを保存したパスに置き換えてください。 

```powershell
$jobName = "MyStreamingJob"
$jobDefinitionFile = "C:\JobDefinition.json"
New-AzureRmStreamAnalyticsJob `
  -ResourceGroupName $resourceGroup `
  -File $jobDefinitionFile `
  -Name $jobName `
  -Force 
```

## <a name="configure-input-to-the-job"></a>ジョブへの入力を構成する

[New-AzureRmStreamAnalyticsInput](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/new-azurermstreamanalyticsinput?view=azurermps-5.4.0) コマンドレットを使用して、ジョブへの入力を追加します。 このコマンドレットは、パラメーターとして、ジョブ名、ジョブ入力名、リソース グループ名、およびジョブ入力定義を受け取ります。 ジョブ入力定義は、ジョブの入力の構成に必要なプロパティを含む JSON ファイルです。 この例では、BLOB ストレージを入力として作成します。 

ローカル マシンに `JobInputDefinition.json` という名前のファイルを作成し、次の JSON データを追加します。 `accountKey` の値は、ストレージ アカウントのアクセス キー ($storageAccountKey 値に格納されている値) に置き換えてください。 

```json
{
    "properties": {
        "type": "Stream",
        "datasource": {
            "type": "Microsoft.Storage/Blob",
            "properties": {
                "storageAccounts": [
                {
                   "accountName": "mystorageaccount",
                   "accountKey":"<storage account key>"
                }],
                "container": "streamanalytics",
                "pathPattern": "input/",
                "dateFormat": "yyyy/MM/dd",
                "timeFormat": "HH"
            }
        },
        "serialization": {
            "type": "Json",
            "properties": {
                "encoding": "UTF8"
            }
        }
    },
    "name": "MyBlobInput",
    "type": "Microsoft.StreamAnalytics/streamingjobs/inputs"
}
```

次に、`New-AzureRmStreamAnalyticsInput` コマンドレットを実行します。`jobDefinitionFile` 変数の値は、ジョブ入力定義 JSON ファイルを保存したパスに置き換えてください。 

```powershell
$jobInputName = "MyBlobInput"
$jobInputDefinitionFile = "C:\JobInputDefinition.json"
New-AzureRmStreamAnalyticsInput `
  -ResourceGroupName $resourceGroup `
  -JobName $jobName `
  -File $jobInputDefinitionFile `
  -Name $jobInputName 
```

## <a name="configure-output-to-the-job"></a>ジョブへの出力を構成する

[New-AzureRmStreamAnalyticsOutput](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/new-azurermstreamanalyticsoutput?view=azurermps-5.4.0) コマンドレットを使用して、ジョブへの出力を追加します。 このコマンドレットは、パラメーターとして、ジョブ名、ジョブ出力名、リソース グループ名、およびジョブ出力定義を受け取ります。 ジョブ出力定義は、ジョブの出力の構成に必要なプロパティを含む JSON ファイルです。 この例では、BLOB ストレージを出力として使用します。 

ローカル マシンに `JobOutputDefinition.json` という名前のファイルを作成し、次の JSON データを追加します。 `accountKey` の値は、ストレージ アカウントのアクセス キー ($storageAccountKey 値に格納されている値) に置き換えてください。 

```json
{
    "properties": {
        "datasource": {
            "type": "Microsoft.Storage/Blob",
            "properties": {
                "storageAccounts": [
                    {
                      "accountName": "mystorageaccount",
                      "accountKey": "<storage account key>"
                    }],
                "container": "streamanalytics",
                "pathPattern": "output/",
                "dateFormat": "yyyy/MM/dd",
                "timeFormat": "HH"
            }
        },
        "serialization": {
            "type": "Json",
            "properties": {
                "encoding": "UTF8",
                "format": "LineSeparated"
            }
        }
    },
    "name": "MyBlobOutput",
    "type": "Microsoft.StreamAnalytics/streamingjobs/outputs"
}
```

次に、`New-AzureRmStreamAnalyticsOutput` コマンドレットを実行します。 `jobOutputDefinitionFile` 変数の値は、ジョブ出力定義 JSON ファイルを保存したパスに置き換えてください。 

```powershell
$jobOutputName = "MyBlobOutput"
$jobOutputDefinitionFile = "C:\JobOutputDefinition.json"
New-AzureRmStreamAnalyticsOutput `
  -ResourceGroupName $resourceGroup `
  -JobName $jobName `
  -File $jobOutputDefinitionFile `
  -Name $jobOutputName -Force 
```

## <a name="define-the-transformation-query"></a>変換クエリを定義する

[New-AzureRmStreamAnalyticsTransformation](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/new-azurermstreamanalyticstransformation?view=azurermps-5.4.0) コマンドレットを使用して、ジョブの変換を追加します。 このコマンドレットは、パラメーターとして、ジョブ名、ジョブ変換名、リソース グループ名、およびジョブ変換定義を受け取ります。 ローカル マシンに `JobTransformationDefinition.json` という名前のファイルを作成し、次の JSON データを追加します。 JSON ファイルには、変換クエリを定義するクエリ パラメーターが含まれています。

```json
{     
   "name":"MyTransformation",  
   "type":"Microsoft.StreamAnalytics/streamingjobs/transformations",  
   "properties":{    
      "streamingUnits":1,  
      "script":null,  
      "query":" SELECT System.Timestamp AS OutputTime, dspl AS SensorName, Avg(temp) AS AvgTemperature INTO MyBlobOutput FROM MyBlobInput TIMESTAMP BY time GROUP BY TumblingWindow(second,30),dspl HAVING Avg(temp)>100"  
   }  
}
```

次に、`New-AzureRmStreamAnalyticsTransformation` コマンドレットを実行します。 `jobTransformationDefinitionFile` 変数の値は、ジョブ変換定義 JSON ファイルを保存したパスに置き換えてください。 

```powershell
$jobTransformationName = "MyJobTransformation"
$jobTransformationDefinitionFile = "C:\JobTransformationDefinition.json"
New-AzureRmStreamAnalyticsTransformation `
  -ResourceGroupName $resourceGroup `
  -JobName $jobName `
  -File $jobTransformationDefinitionFile `
  -Name $jobTransformationName -Force
```

## <a name="start-the-stream-analytics-job-and-check-the-output"></a>Stream Analytics ジョブを開始して出力をチェックする

[Start-AzureRmStreamAnalyticsJob](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/start-azurermstreamanalyticsjob?view=azurermps-5.4.0) コマンドレットを使用して、ジョブを開始します。 このコマンドレットは、パラメーターとして、ジョブ名、リソース グループ名、出力開始モード、および開始時刻を受け取ります。 `OutputStartMode` には、`JobStartTime`、`CustomTime`、または `LastOutputEventTime` の値が使用できます。 これらの値がそれぞれ何を意味しているかについては、PowerShell ドキュメントの[パラメーター](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/start-azurermstreamanalyticsjob?view=azurermps-5.4.0)に関するセクションを参照してください。 この例では、モードに `CustomTime` を指定し、`OutputStartTime` に値を指定します。 

時間値には `2018-01-01` を選択します。 サンプル データからのイベント タイムスタンプ前に指定するために、この開始日が選択されます。 次のコマンドレットを実行すると、ジョブが開始されたときに出力として `True` が返されます。 変換されたデータが含まれた出力フォルダーがストレージ コンテナーに作成されます。 

```powershell
Start-AzureRmStreamAnalyticsJob `
  -ResourceGroupName $resourceGroup `
  -Name $jobName `
  -OutputStartMode CustomTime `
  -OutputStartTime 2018-01-01T00:00:00Z 
```

## <a name="clean-up-resources"></a>リソースのクリーンアップ

リソース グループ、ストリーミング ジョブ、および関連するすべてのリソースは、不要になったら削除します。 ジョブを削除すると、ジョブによって消費されるストリーミング ユニットに対する課金を回避することができます。 ジョブを後で使用する計画がある場合、削除をスキップしてこのジョブを一時的に停止できます。 このジョブの使用を続けない場合は、次のコマンドレットを実行して、このクイック スタートで作成したすべてのリソースを削除してください。

```powershell
Remove-AzureRmResourceGroup `
  -Name $resourceGroup 
```

## <a name="next-steps"></a>次の手順

このクイック スタートでは、単純な Stream Analytics ジョブをデプロイしました。 その他の入力ソースの構成やリアルタイム検出の実行について学習するには、次の記事に進んでください。

> [!div class="nextstepaction"]
> [Azure Stream Analytics を使用したリアルタイムの不正行為の検出](stream-analytics-real-time-fraud-detection.md)
