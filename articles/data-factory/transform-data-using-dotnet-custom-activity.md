---
title: Azure Data Factory パイプラインでカスタム アクティビティを使用する
description: カスタム アクティビティを作成して Azure Data Factory パイプラインで使用する方法について説明します。
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/16/2018
ms.author: douglasl
ms.openlocfilehash: 2dab0adb0728a1fb5e8ac9bebe01f861ed8c7c3a
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/27/2018
ms.locfileid: "37055509"
---
# <a name="use-custom-activities-in-an-azure-data-factory-pipeline"></a>Azure Data Factory パイプラインでカスタム アクティビティを使用する
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1](v1/data-factory-use-custom-activities.md)
> * [現在のバージョン](transform-data-using-dotnet-custom-activity.md)

Azure Data Factory パイプラインでは、2 種類のアクティビティを使用できます。

- [サポートされるソース データ ストアとシンク データ ストア](copy-activity-overview.md#supported-data-stores-and-formats)の間でデータを移動する[データ移動アクティビティ](copy-activity-overview.md)。
- Azure HDInsight、Azure Batch、Azure Machine Learning などのコンピューティング サービスを使用してデータを変換する[データ変換アクティビティ](transform-data.md)。 

Data Factory でサポートされていないデータ ストアとの間でデータを移動する場合や、Data Factory でサポートされていない方法でデータを変換/処理する場合は、独自のデータ移動ロジックまたはデータ変換ロジックで**カスタム アクティビティ**を作成し、パイプラインでそのアクティビティを使用します。 カスタム アクティビティでは、仮想マシンの **Azure Batch** プールでカスタマイズされたコード ロジックを実行します。

Azure Batch サービスを初めて利用する場合は、次の記事をご覧ください。

* [Azure Batch の基本](../batch/batch-technical-overview.md) 」をご覧ください。
* Azure Batch アカウントは、[New-AzureRmBatchAccount](/powershell/module/azurerm.batch/New-AzureRmBatchAccount?view=azurermps-4.3.1) コマンドレットまたは [Azure Portal](../batch/batch-account-create-portal.md) を使用して作成します。 このコマンドレットの使用方法の詳細については、[PowerShell を使用した Azure Batch アカウントの管理](http://blogs.technet.com/b/windowshpc/archive/2014/10/28/using-azure-powershell-to-manage-azure-batch-account.aspx)に関する記事をご覧ください。
* [New-AzureBatchPool](/powershell/module/azurerm.batch/New-AzureBatchPool?view=azurermps-4.3.1) コマンドレット。

## <a name="azure-batch-linked-service"></a>Azure Batch のリンクされたサービス 
次の JSON では、サンプルの Azure Batch のリンクされたサービスを定義しています。 詳細については、「[Azure Data Factory でサポートされるコンピューティング環境](compute-linked-services.md)」をご覧ください。

```json
{
    "name": "AzureBatchLinkedService",
    "properties": {
        "type": "AzureBatch",
        "typeProperties": {
            "accountName": "batchaccount",
            "accessKey": {
                "type": "SecureString",
                "value": "access key"
            },
            "batchUri": "https://batchaccount.region.batch.azure.com",
            "poolName": "poolname",
            "linkedServiceName": {
                "referenceName": "StorageLinkedService",
                "type": "LinkedServiceReference"
            }
        }
    }
}
```

 Azure Batch のリンクされたサービスの詳細については、[計算のリンクされたサービス](compute-linked-services.md)に関する記事をご覧ください。 

## <a name="custom-activity"></a>カスタム アクティビティ

次の JSON スニペットでは、単純なカスタム アクティビティを使用するパイプラインを定義しています。 このアクティビティ定義には、Azure Batch のリンクされたサービスへの参照が含まれています。 

```json
{
    "name": "MyCustomActivityPipeline",
    "properties": {
      "description": "Custom activity sample",
      "activities": [{
        "type": "Custom",
        "name": "MyCustomActivity",
        "linkedServiceName": {
          "referenceName": "AzureBatchLinkedService",
          "type": "LinkedServiceReference"
        },
        "typeProperties": {
          "command": "helloworld.exe",
          "folderPath": "customactv2/helloworld",
          "resourceLinkedService": {
            "referenceName": "StorageLinkedService",
            "type": "LinkedServiceReference"
          }
        }
      }]
    }
  }
```

このサンプルでは、helloworld.exe は、resourceLinkedService で使用される Azure ストレージ アカウントの customactv2/helloworld フォルダーに格納されているカスタム アプリケーションです。 カスタム アクティビティでは、Azure Batch で実行されるこのカスタム アプリケーションを送信します。 コマンドは、Azure Batch プール ノードのターゲット オペレーティング システムで実行できる任意の優先アプリケーションに置き換えることができます。 

次の表は、このアクティビティに固有のプロパティの名前と説明です。 

| プロパティ              | 説明                              | 必須 |
| :-------------------- | :--------------------------------------- | :------- |
| name                  | パイプラインのアクティビティの名前。     | [はい]      |
| description            | アクティビティの動作を説明するテキスト。  | いいえ        |
| type                  | カスタム アクティビティの場合、アクティビティの種類は **Custom** です。 | [はい]      |
| linkedServiceName     | Azure Batch にリンクされたサービス。 このリンクされたサービスの詳細については、[計算のリンクされたサービス](compute-linked-services.md)に関する記事をご覧ください。  | [はい]      |
| コマンド               | 実行されるカスタム アプリケーションのコマンド。 アプリケーションが Azure Batch プール ノードで既に使用可能な場合は、resourceLinkedService と folderPath を省略できます。 たとえば、Windows バッチ プール ノードでネイティブでサポートされている `cmd /c dir` をコマンドとして指定できます。 | [はい]      |
| resourceLinkedService | カスタム アプリケーションが格納されているストレージ アカウントへの Azure Storage のリンクされたサービス。 | いいえ        |
| folderPath            | カスタム アプリケーションとそのすべての依存関係のフォルダーのパス。 | いいえ        |
| referenceObjects      | 既存のリンクされたサービスとデータセットの配列。 カスタム コードが Data Factory のリソースを参照できるように、参照されているリンクされたサービスとデータセットが JSON 形式でカスタム アプリケーションに渡されます。 | いいえ        |
| extendedProperties    | カスタム コードが追加のプロパティを参照できるように、JSON 形式でカスタム アプリケーションに渡すことができるユーザー定義プロパティ。 | いいえ        |

## <a name="executing-commands"></a>コマンドの実行

カスタム アクティビティを使用してコマンドを直接実行できます。 次の例は、Azure Batch プールのターゲット ノードで "echo hello world" コマンドを実行し、出力を stdout に出力します。 

  ```json
  {
    "name": "MyCustomActivity",
    "properties": {
      "description": "Custom activity sample",
      "activities": [{
        "type": "Custom",
        "name": "MyCustomActivity",
        "linkedServiceName": {
          "referenceName": "AzureBatchLinkedService",
          "type": "LinkedServiceReference"
        },
        "typeProperties": {
          "command": "cmd /c echo hello world"
        }
      }]
    }
  } 
  ```

## <a name="passing-objects-and-properties"></a>オブジェクトとプロパティを渡す

次のサンプルは、referenceObjects と extendedProperties を使用して、Data Factory オブジェクトとユーザー定義プロパティをカスタム アプリケーションに渡す方法を示しています。 


```json
{
  "name": "MyCustomActivityPipeline",
  "properties": {
    "description": "Custom activity sample",
    "activities": [{
      "type": "Custom",
      "name": "MyCustomActivity",
      "linkedServiceName": {
        "referenceName": "AzureBatchLinkedService",
        "type": "LinkedServiceReference"
      },
      "typeProperties": {
        "command": "SampleApp.exe",
        "folderPath": "customactv2/SampleApp",
        "resourceLinkedService": {
          "referenceName": "StorageLinkedService",
          "type": "LinkedServiceReference"
        },
        "referenceObjects": {
          "linkedServices": [{
            "referenceName": "AzureBatchLinkedService",
            "type": "LinkedServiceReference"
          }]
        },
        "extendedProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "aSampleSecureString"
            },
            "PropertyBagPropertyName1": "PropertyBagValue1",
            "propertyBagPropertyName2": "PropertyBagValue2",
            "dateTime1": "2015-04-12T12:13:14Z"              
        }
      }
    }]
  }
}
```

アクティビティを実行すると、referenceObjects と extendedProperties が、SampleApp.exe の同じ実行フォルダーに配置されている次のファイルに格納されます。 

- activity.json

  extendedProperties とカスタム アクティビティのプロパティが格納されます。 

- linkedServices.json

  referenceObjects プロパティで定義されているリンクされたサービスの配列が格納されます。 

- datasets.json

  referenceObjects プロパティで定義されているデータセットの配列が格納されます。 

次のサンプル コードは、SampleApp.exe が JSON ファイルの必要な情報にアクセスする方法を示しています。 

```csharp
using Newtonsoft.Json;
using System;
using System.IO;

namespace SampleApp
{
    class Program
    {
        static void Main(string[] args)
        {
            //From Extend Properties
            dynamic activity = JsonConvert.DeserializeObject(File.ReadAllText("activity.json"));
            Console.WriteLine(activity.typeProperties.extendedProperties.connectionString.value);

            // From LinkedServices
            dynamic linkedServices = JsonConvert.DeserializeObject(File.ReadAllText("linkedServices.json"));
            Console.WriteLine(linkedServices[0].properties.typeProperties.accountName);
        }
    }
}
```

## <a name="retrieve-execution-outputs"></a>実行の出力を取得する

  次の PowerShell コマンドを使用して、パイプラインの実行を開始できます。 

  ```.powershell
  $runId = Invoke-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineName $pipelineName
  ```
  パイプラインが実行されているときは、次のコマンドを使用して実行出力を確認できます。 

  ```.powershell
  while ($True) {
      $result = Get-AzureRmDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $runId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)

      if(!$result) {
          Write-Host "Waiting for pipeline to start..." -foregroundcolor "Yellow"
      }
      elseif (($result | Where-Object { $_.Status -eq "InProgress" } | Measure-Object).count -ne 0) {
          Write-Host "Pipeline run status: In Progress" -foregroundcolor "Yellow"
      }
      else {
          Write-Host "Pipeline '"$pipelineName"' run finished. Result:" -foregroundcolor "Yellow"
          $result
          break
      }
      ($result | Format-List | Out-String)
      Start-Sleep -Seconds 15
  }

  Write-Host "Activity `Output` section:" -foregroundcolor "Yellow"
  $result.Output -join "`r`n"

  Write-Host "Activity `Error` section:" -foregroundcolor "Yellow"
  $result.Error -join "`r`n"
  ```

  カスタム アプリケーションの **stdout** と **stderr** は、タスクの GUID を使用して Azure Batch のリンクされたサービスを作成したときに定義した、Azure Storage のリンクされたサービスの **adfjobs** コンテナーに保存されます。 次のスニペットに示すように、アクティビティの実行の出力から詳細なパスを取得できます。 

  ```shell
  Pipeline ' MyCustomActivity' run finished. Result:

  ResourceGroupName : resourcegroupname
  DataFactoryName   : datafactoryname
  ActivityName      : MyCustomActivity
  PipelineRunId     : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
  PipelineName      : MyCustomActivity
  Input             : {command}
  Output            : {exitcode, outputs, effectiveIntegrationRuntime}
  LinkedServiceName : 
  ActivityRunStart  : 10/5/2017 3:33:06 PM
  ActivityRunEnd    : 10/5/2017 3:33:28 PM
  DurationInMs      : 21203
  Status            : Succeeded
  Error             : {errorCode, message, failureType, target}

  Activity Output section:
  "exitcode": 0
  "outputs": [
    "https://shengcstorbatch.blob.core.windows.net/adfjobs/<GUID>/output/stdout.txt",
    "https://shengcstorbatch.blob.core.windows.net/adfjobs/<GUID>/output/stderr.txt"
  ]
  "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (East US)"
  Activity Error section:
  "errorCode": ""
  "message": ""
  "failureType": ""
  "target": "MyCustomActivity"
  ```
ダウンストリームのアクティビティで stdout.txt の内容を使用する場合は、式 "\@activity('MyCustomActivity').output.outputs[0]" で stdout.txt ファイルへのパスを取得できます。 

  > [!IMPORTANT]
  > - activity.json、linkedServices.json、datasets.json は、Batch タスクのランタイム フォルダーに格納されます。 この例では、activity.json、linkedServices.json、datasets.json は、"https://adfv2storage.blob.core.windows.net/adfjobs/<GUID>/runtime/" パスに格納されています。 必要に応じて、パスを個別にクリーンアップする必要があります。 
  > - リンクされたサービスでセルフホステッド統合ランタイムを使用している場合、顧客が定義したプライベート ネットワーク環境内に資格情報を保持できるように、セルフホステッド統合ランタイムによってキーやパスワードなどの機密情報が暗号化されます。 この場合、カスタム アプリケーション コードから一部の機密フィールドを参照したときにフィールドが見つからない可能性があります。 必要に応じて、リンクされたサービスの参照を使用するのではなく、extendedProperties で SecureString を使用してください。 

## <a name="compare-v2-v1"></a> v2 カスタム アクティビティとバージョン 1 (カスタム) DotNet アクティビティを比較する

  Azure Data Factory バージョン 1 では、`IDotNetActivity` インターフェイスの `Execute` メソッドを実装するクラスを含む .Net クラス ライブラリ プロジェクトを作成して (カスタム) DotNet アクティビティを実装します。 (カスタム) DotNet アクティビティの JSON ペイロードのリンクされたサービス、データセット、および拡張プロパティは、厳密に型指定されたオブジェクトとして Execution メソッドに渡されます。 バージョン 1 のビヘイビアーの詳細については、[バージョン 1 の (カスタム) DotNet](v1/data-factory-use-custom-activities.md) に関するページを参照してください。 この実装のため、バージョン 1 DotNet アクティビティ コードは、.Net Framework 4.5.2 をターゲットにする必要があります。 バージョン 1 の DotNet アクティビティは、Windows ベースの Azure Batch プールのノードで実行する必要があります。 

  Azure Data Factory V2 のカスタム アクティビティでは、.Net インターフェイスを実装する必要はありません。 コマンドとスクリプトを直接実行できるようになり、実行可能ファイルとしてコンパイルされた独自のカスタム コードを実行できるようになりました。 この実装を構成するには、`Command` プロパティと `folderPath` プロパティを一緒に指定する必要があります。 カスタム アクティビティにより、実行可能ファイルとその依存関係が `folderpath` にアップロードされ、コマンドが実行されます。 

  Data Factory v2 カスタム アクティビティの JSON ペイロードに定義されたリンクされたサービス、(referenceObjects に定義されている) データセット、および拡張プロパティには、実行可能ファイルから JSON ファイルとしてアクセスできます。 前述の SampleApp.exe コード サンプルに示すように、JSON シリアライザーを使用して、必要なプロパティにアクセスできます。 

  Data Factory V2 カスタム アクティビティに導入された変更により、カスタム コード ロジックを任意の言語で記述し、Azure Batch でサポートされている Windows および Linux オペレーション システムで実行することができます。 

  次の表では、Data Factory V2 カスタム アクティビティと Data Factory V1 (カスタム) DotNet アクティビティの違いを示します。 


|相違点      | カスタム アクティビティ      | バージョン 1 (カスタム) DotNet アクティビティ      |
| ---- | ---- | ---- |
|カスタム ロジックの定義方法      |実行可能ファイルを提供する      |.Net DLL を実装する      |
|カスタム ロジックの実行環境      |Windows または Linux      |Windows (.Net Framework 4.5.2)      |
|スクリプトの実行      |スクリプトの直接実行をサポート (例: Windows VM 上で "cmd /c echo hello world")      |.Net DLL に実装することが必要      |
|データセットは必要か      |省略可能      |アクティビティを連鎖して情報を渡すために必要      |
|アクティビティからカスタム ロジックへの情報の受け渡し      |ReferenceObjects (LinkedServices と Datasets) および ExtendedProperties (カスタム プロパティ) を使用      |ExtendedProperties (カスタム プロパティ)、入力および出力データセットを使用      |
|カスタム ロジックでの情報の取得      |実行可能ファイルと同じフォルダーに格納されている activity.json、linkedServices.json、および datasets.json を解析      |.Net SDK (.Net Frame 4.5.2) を使用      |
|ログの記録      |STDOUT に直接書き込む      |.Net DLL でロガーを実装する      |


  バージョン 1 (カスタム) DotNet アクティビティ用に書かれた .Net コードが既にあり、これをカスタム アクティビティの現在のバージョンで使用するには、コードを変更する必要があります。 以下のガイドラインの概要に従って、コードを更新します。  

   - プロジェクトを .Net クラス ライブラリからコンソール アプリに変更します。 
   - `Main` メソッドでアプリケーションを起動します。 `IDotNetActivity` インターフェイスの `Execute` メソッドは、必要なくなりました。 
   - JSON シリアライザーを使用して、リンクされたサービス、データセット、およびアクティビティを、厳密に型指定されたオブジェクトとしてではなく読み取り、解析します。 必要なプロパティの値をメインのカスタム コード ロジックに渡します。 前述の SampleApp.exe コードをサンプルとして参照してください。 
   - ロガー オブジェクトは、現在サポートされていません。 実行可能ファイルからの出力をコンソールに出力できます。この出力は、stdout.txt に保存されます。 
   - Microsoft.Azure.Management.DataFactories NuGet パッケージは、もはや必要ありません。 
   - コードをコンパイルし、実行可能ファイルとその依存関係を Azure Storage にアップロードし、`folderPath` プロパティにパスを定義します。 

Data Factory バージョン 1 の記事「[Azure Data Factory パイプラインでカスタム アクティビティを使用する](https://docs.microsoft.com/azure/data-factory/v1/data-factory-use-custom-activities)」で説明されているエンド ツー エンドの DLL とパイプラインを Data Factory カスタム アクティビティとして書き直す詳細な例については、[Data Factory カスタム アクティビティのサンプル](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/ADFv2CustomActivitySample)に関するページを参照してください。 

## <a name="auto-scaling-of-azure-batch"></a>Azure Batch の自動スケール
**自動スケール** 機能で、Azure Batch プールを作成することもできます。 たとえば、専用 VM 数が 0 の Azure Batch プールと、保留中のタスクの数に基づく自動スケールの数式を作成できます。 

ここでのサンプル数式では次の動作が行われます。プールが最初に作成されるとき、1 つの VM から始まります。 $PendingTasks メトリックにより、実行中 + アクティブ (キューに登録済み) 状態のタスク数が定義されます。  この数式により、最後の 180 秒間の保留タスク平均数が判明し、TargetDedicated が適宜設定されます。 それにより、TargetDedicated が 25 台の仮想マシンを超えることはありません。 そのため、新しいタスクが送信されると、プールが自動的に増加します。タスクが完了すると、VM は 1 台ずつ解放され、自動スケールにより VM が減らされます。 startingNumberOfVMs と maxNumberofVMs はニーズに合わせて調整できます。

自動スケールの数式:

``` 
startingNumberOfVMs = 1;
maxNumberofVMs = 25;
pendingTaskSamplePercent = $PendingTasks.GetSamplePercent(180 * TimeInterval_Second);
pendingTaskSamples = pendingTaskSamplePercent < 70 ? startingNumberOfVMs : avg($PendingTasks.GetSample(180 * TimeInterval_Second));
$TargetDedicated=min(maxNumberofVMs,pendingTaskSamples);
```

詳細については、「 [Azure Batch プール内のコンピューティング ノードの自動スケール](../batch/batch-automatic-scaling.md) 」をご覧ください。

プールで既定の [autoScaleEvaluationInterval](https://msdn.microsoft.com/library/azure/dn820173.aspx)を使用する場合、Batch サービスがカスタム アクティビティを実行する前に VM を準備するのに 15 ～ 30 分かかることがあります。  プールが異なる 　autoScaleEvaluationInterval を使用する場合、Batch サービスは autoScaleEvaluationInterval + 10 分を要することがあります。


## <a name="next-steps"></a>次の手順
別の手段でデータを変換する方法を説明している次の記事を参照してください。 

* [U-SQL アクティビティ](transform-data-using-data-lake-analytics.md)
* [Hive アクティビティ](transform-data-using-hadoop-hive.md)
* [Pig アクティビティ](transform-data-using-hadoop-pig.md)
* [MapReduce アクティビティ](transform-data-using-hadoop-map-reduce.md)
* [Hadoop Streaming アクティビティ](transform-data-using-hadoop-streaming.md)
* [Spark アクティビティ](transform-data-using-spark.md)
* [Machine Learning バッチ実行アクティビティ](transform-data-using-machine-learning.md)
* [ストアド プロシージャ アクティビティ](transform-data-using-stored-procedure.md)
