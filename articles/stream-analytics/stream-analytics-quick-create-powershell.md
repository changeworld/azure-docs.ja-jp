---
title: Azure PowerShell を使用して Stream Analytics ジョブを作成する | Microsoft Docs
description: このクイック スタートでは、Azure PowerShell モジュールを使用して Azure Stream Analytics ジョブをデプロイおよび実行する方法について詳しく説明します。
services: stream-analytics
keywords: Stream Analytics, クラウド ジョブ, Azure PowerShell, ジョブ入力, ジョブ出力, ジョブ変換
author: SnehaGunda
ms.author: sngun
ms.date: 03/16/2018
ms.topic: quickstart
ms.service: stream-analytics
ms.custom: mvc
manager: kfile
ms.openlocfilehash: 8a1036531ea0e7c1426224bc4d42c83e9049cabf
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/19/2018
---
# <a name="quickstart-create-a-stream-analytics-job-by-using-azure-powershell"></a>クイック スタート: Azure PowerShell を使用して Stream Analytics ジョブを作成する

Azure PowerShell モジュールは、PowerShell コマンドレットやスクリプトで Azure リソースを作成および管理するために使用します。 このクイック スタートでは、Azure PowerShell モジュールを使用して Azure Stream Analytics ジョブをデプロイおよび実行する方法について詳しく説明します。

## <a name="before-you-begin"></a>開始する前に

* Azure サブスクリプションをお持ちでない場合は、[無料アカウント](https://azure.microsoft.com/free/)を作成してください。  

* このクイック スタートには、Azure PowerShell モジュール バージョン 3.6 以降が必要です。 ローカル マシンにインストールされているバージョンを調べるには、`Get-Module -ListAvailable AzureRM` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](https://docs.microsoft.com/powershell/azure/install-azurerm-ps)に関する記事を参照してください。 この記事のシナリオでは、BLOB ストレージからデータを読み込み、データを変換して、同じ BLOB ストレージ内の別のコンテナーに書き戻す操作を説明しています。

## <a name="sign-in-to-azure"></a>Azure へのサインイン

`Connect-AzureRmAccount` コマンドを使用して Azure サブスクリプションにログインし、ポップアップ ブラウザーに Azure 資格情報を入力します。 サインインした後、複数のサブスクリプションがある場合は、次のコマンドレットを実行して、このクイック スタートに使用するサブスクリプションを選択します。 <your subscription> は、実際のサブスクリプションの名前に置き換えてください。  

```powershell
# Log in to your Azure account
Connect-AzureRmAccount

# Select the Azure subscription you want to use to create the resource group.
Get-AzureRmSubscription `
  -SubscriptionName “<your subscription>” | Select-AzureRmSubscription
```

## <a name="create-a-resource-group"></a>リソース グループの作成

[New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroup) を使用して Azure リソース グループを作成します。 リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。

```powershell
$resourceGroup = "ASAPSRG"
$location = "WestUS2"
New-AzureRMResourceGroup `
  -Name $resourceGroup `
  -Location $location 
```

## <a name="prepare-the-input-data"></a>入力データを準備する

Stream Analytics ジョブを定義する前に、ジョブへの入力として構成されるデータを準備する必要があります。 ジョブで必要な入力データを準備するには、次の手順を実行します。 

1. GitHub から[センサー サンプル データ](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/GettingStarted/HelloWorldASA-InputStream.json)をダウンロードします。  

2. [New-AzureRmStorageAccount](https://docs.microsoft.com/powershell/module/azurerm.storage/New-AzureRmStorageAccount) コマンドレットを使用して、LRS レプリケーション付きの標準の汎用ストレージ アカウントを作成します。  

3. 使用されるストレージ アカウントを定義するストレージ アカウント コンテキストを取得します。 ストレージ アカウントを操作するとき、資格情報を繰り返し入力する代わりに、このコンテキストを参照します。 この例では、ローカル冗長ストレージ (LRS) と BLOB 暗号化機能 (既定で有効になります) を持つ mystorageaccount というストレージ アカウントを作成します。  

4. 次に、[New-AzureStorageContainer](https://docs.microsoft.com/powershell/module/azure.storage/new-azurestoragecontainer) を使用してコンテナーを作成し、アクセス許可を 'blob' に設定してファイルのパブリック アクセスを許可して、以前ダウンロードした[センサー サンプル データ](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/GettingStarted/HelloWorldASA-InputStream.json)をアップロードします。 

これらの手順は、次の PowerShell スクリプトを実行することによって実現されます。

```powershell
$storageAccountName = "mystorageaccount"
$storageAccount = New-AzureRmStorageAccount `
  -ResourceGroupName $resourceGroup `
  -Name $storageAccountName `
  -Location $location `
  -SkuName Standard_LRS `
  -Kind Storage

$ctx = $storageAccount.Context
$containerName = "myinputcontainer"

New-AzureStorageContainer `
  -Name $containerName `
  -Context $ctx `
  -Permission blob

Set-AzureStorageBlobContent `
  -File "C:\HelloWorldASA-InputStream.json" `
  -Container $containerName `
  -Context $ctx  

$storageAccountKey = (Get-AzureRmStorageAccountKey `
  -ResourceGroupName $resourceGroup `
  -Name $storageAccountName).Value[0]
```

## <a name="create-a-stream-analytics-job"></a>Stream Analytics のジョブの作成

[New-AzureRMStreamAnalyticsJob](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/new-azurermstreamanalyticsjob?view=azurermps-5.4.0) コマンドレットを使用して、Stream Analytics ジョブを作成します。 このコマンドレットは、パラメーターとして、ジョブ名、リソース グループ名、およびジョブ定義を受け取ります。 ジョブ名は、ジョブを識別する任意のフレンドリ名にすることができます。Stream Analytics ジョブ名には、英数字、ハイフン、アンダースコアのみを使用することができます。長さは 3 文字以上 63 文字以下でなければなりません。 ジョブ定義は、ジョブの作成に必要なプロパティを含む JSON ファイルです。 ローカル マシンに "JobDefinition.json" という名前のファイルを作成し、次の JSON データを追加します。

```json
{    
   "location":"Central US",  
   "properties":{    
      "sku":{    
         "name":"standard"  
      },  
      "eventsOutOfOrderPolicy":"drop",  
      "eventsOutOfOrderMaxDelayInSeconds":10,  
      "compatibilityLevel": 1.1
}
}
```

次に、New-AzureRMStreamAnalyticsJob コマンドレットを実行します。jobDefinitionFile 変数の値は、ジョブ定義 JSON ファイルを保存したパスに置き換えてください。 

```powershell
$jobName = "MyStreamingJob"
$jobDefinitionFile = "C:\JobDefinition.json"
New-AzureRMStreamAnalyticsJob `
  -ResourceGroupName $resourceGroup `
  –File $jobDefinitionFile `
  –Name $jobName -Force 
```

## <a name="configure-input-to-the-job"></a>ジョブへの入力を構成する

[New-AzureRMStreamAnalyticsInput](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/new-azurermstreamanalyticsinput?view=azurermps-5.4.0) コマンドレットを使用して、ジョブへの入力を追加します。 このコマンドレットは、パラメーターとして、ジョブ名、ジョブ入力名、リソース グループ名、およびジョブ入力定義を受け取ります。 ジョブ入力定義は、ジョブ入力の構成に必要なプロパティを含む JSON ファイルです。この例では、入力として BLOB ストレージを作成します。 ローカル マシンで "JobInputDefinition.json" という名前のファイルを作成し、以下の JSON データを追加します。**accountKey** の値は、ストレージ アカウントのアクセス キー ($storageAccountKey 値に格納されている値) に置き換えてください。 

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
                   "accountKey":"<Your storage account key>"
                }],
                "container": "myinputcontainer",
                "pathPattern": "",
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

次に、New-AzureRMStreamAnalyticsInput コマンドレットを実行します。jobDefinitionFile 変数の値は、ジョブ入力定義 JSON ファイルを保存したパスに置き換えてください。 

```powershell
$jobInputName = "MyBlobInput"
$jobInputDefinitionFile = "C:\JobInputDefinition.json"
New-AzureRMStreamAnalyticsInput `
  -ResourceGroupName $resourceGroup `
  -JobName $jobName `
  -File $jobInputDefinitionFile `
  -Name $jobInputName 
```

## <a name="configure-output-to-the-job"></a>ジョブへの出力を構成する

[New-AzureRmStreamAnalyticsOutput](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/new-azurermstreamanalyticsoutput?view=azurermps-5.4.0) コマンドレットを使用して、ジョブへの出力を追加します。 このコマンドレットは、パラメーターとして、ジョブ名、ジョブ出力名、リソース グループ名、およびジョブ出力定義を受け取ります。 ジョブ出力定義は、ジョブ出力の構成に必要なプロパティを含む JSON ファイルです。この例では、出力として BLOB ストレージを作成します。 ローカル マシンで "JobOutputDefinition.json" という名前のファイルを作成し、以下の JSON データを追加します。**accountKey** の値は、ストレージ アカウントのアクセス キー ($storageAccountKey 値に格納されている値) に置き換えてください。 

```json
{
    "properties": {
        "datasource": {
            "type": "Microsoft.Storage/Blob",
            "properties": {
                "storageAccounts": [
                    {
                      "accountName": "mystorageaccount",
                      "accountKey": "<Your storage account key>"
                    }],
                "container": "myoutputcontainer",
                "pathPattern": "",
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

次に、New-AzureRMStreamAnalyticsOutput コマンドレットを実行します。jobOutputDefinitionFile 変数の値は、ジョブ出力定義 JSON ファイルを保存したパスに置き換えてください。 

```powershell
$jobOutputName = "MyBlobOutput"
$jobOutputDefinitionFile = "C:\JobOutputDefinition.json"
New-AzureRMStreamAnalyticsOutput `
  -ResourceGroupName $resourceGroup `
  –JobName $jobName `
  –File $jobOutputDefinitionFile `
  –Name $jobOutputName -Force 
```

## <a name="define-the-transformation-query"></a>変換クエリを定義する

[New-AzureRmStreamAnalyticsTransformation](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/new-azurermstreamanalyticstransformation?view=azurermps-5.4.0) コマンドレットを使用して、ジョブの変換を追加します。 このコマンドレットは、パラメーターとして、ジョブ名、ジョブ変換名、リソース グループ名、およびジョブ変換定義を受け取ります。 ローカル マシンに "JobTransformationDefinition.json" という名前のファイルを作成し、次の JSON データを追加します。 JSON ファイルには、変換クエリを定義するクエリ パラメーターが含まれています。

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

次に、New-AzureRMStreamAnalyticsTransformation コマンドレットを実行します。jobTransformationDefinitionFile 変数の値は、ジョブ変換定義 JSON ファイルを保存したパスに置き換えてください。 

```powershell
$jobTransformationName = "MyJobTransformation"
$jobTransformationDefinitionFile = "C:\JobTransformationDefinition.json"
New-AzureRMStreamAnalyticsTransformation `
  -ResourceGroupName $resourceGroup `
  –JobName $jobName `
  –File $jobTransformationDefinitionFile `
  –Name $jobTransformationName -Force
```

## <a name="start-the-stream-analytics-job-and-check-the-output"></a>Stream Analytics ジョブを開始して出力をチェックする

[Start-AzureRMStreamAnalyticsJob](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/start-azurermstreamanalyticsjob?view=azurermps-5.4.0) コマンドレットを使用して、ジョブを開始します。 このコマンドレットは、パラメーターとして、ジョブ名、リソース グループ名、出力開始モード、および開始時刻を受け取ります。 OUtpputStartMode は、2 つの値を受け取ります (JobStartTime、CustomTime、または LastOutputEventTime)。これらの値のそれぞれが何を意味しているかについては、PowerShell ドキュメントの[パラメーター](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/start-azurermstreamanalyticsjob?view=azurermps-5.4.0)のセクションを参照してください。 この例では、モードに CustomTime を指定し、OutputStartTime に値を指定することができます。 

時間の値には、ファイルを BLOB ストレージにアップロードした日より 1 日前の日を選択します。これは、ファイルがアップロードされた時刻が現在の時刻よりも早いためです。 次のコマンドレットを実行すると、ジョブが開始された場合、出力として "True" が返されます。 変換されたデータを持つ、"myoutputcontainer" という名前のコンテナーが、ストレージ アカウントに作成されます。 

```powershell
Start-AzureRMStreamAnalyticsJob `
  -ResourceGroupName $resourceGroup `
  -Name $jobName `
  -OutputStartMode CustomTime `
  -OutputStartTime 2018-03-11T14:45:12Z 
```

## <a name="clean-up-resources"></a>リソースのクリーンアップ

リソース グループ、ストリーミング ジョブ、および関連するすべてのリソースは、不要になったら削除します。 ジョブを削除すると、ジョブによって消費されるストリーミング ユニットに対する課金を回避することができます。 ジョブを後で使用する計画がある場合は、ジョブを停止し、必要なときに再起動することができます。 このジョブの使用を続けない場合は、次のコマンドレットを実行して、このクイック スタートで作成したすべてのリソースを削除してください。

```powershell
Remove-AzureRmResourceGroup `
  -Name $resourceGroup 
```

## <a name="next-steps"></a>次の手順

このクイック スタートでは、単純な Stream Analytics ジョブをデプロイしました。他の入力ソースの構成とリアルタイム検出の実行について学習するには、次の記事に進んでください。

> [!div class="nextstepaction"]
> [Azure Stream Analytics を使用したリアルタイムの不正行為の検出](stream-analytics-real-time-fraud-detection.md)
