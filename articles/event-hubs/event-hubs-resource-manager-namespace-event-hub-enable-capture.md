---
title: "テンプレートを使用した Azure Event Hubs 名前空間の作成と Capture の有効化 | Microsoft Docs"
description: "Azure Resource Manager テンプレートを使用して、1 つのイベント ハブを含んだ Azure Event Hubs 名前空間を作成して Capture を有効にします"
services: event-hubs
documentationcenter: .net
author: ShubhaVijayasarathy
manager: timlt
editor: 
ms.assetid: 8bdda6a2-5ff1-45e3-b696-c553768f1090
ms.service: event-hubs
ms.devlang: tbd
ms.topic: get-started-article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 06/28/2017
ms.author: shvija;sethm
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: f19a3d9b323d75ae23480d0699d55b79bb7d2e84
ms.contentlocale: ja-jp
ms.lasthandoff: 06/28/2017


---
<a id="create-an-event-hubs-namespace-with-an-event-hub-and-enable-capture-using-an-azure-resource-manager-template" class="xliff"></a>

# Azure Resource Manager テンプレートを使用して、イベント ハブを含んだ Event Hubs 名前空間を作成して Capture を有効にする
この記事では、Azure Resource Manager テンプレートを使用し、1 つのイベント ハブ インスタンスを含んだ Event Hubs 名前空間を作成して、イベント ハブの Capture 機能を有効にする方法について説明します。 記事では、デプロイ対象のリソースを定義する方法と、デプロイの実行時に指定されるパラメーターを定義する方法を説明します。 このテンプレートは、独自のデプロイに使用することも、要件に合わせてカスタマイズすることもできます。

テンプレートの作成の詳細については、「[Azure Resource Manager のテンプレートの作成][Authoring Azure Resource Manager templates]」をご覧ください。

Azure リソースの名前付け規則のプラクティスとパターンの詳細については、[Azure リソースの名前付け規則][Azure Resources Naming Conventions]に関する記事をご覧ください。

完全なテンプレートについては、GitHub の[イベント ハブと、Capture テンプレートの有効化][Event Hub and enable Capture template]に関する記事を参照してください。

> [!NOTE]
> 最新のテンプレートを確認する場合は、「[Azure クイックスタート テンプレート][Azure Quickstart Templates]」ギャラリーで "Event Hubs" を検索してください。
> 
> 

<a id="what-will-you-deploy" class="xliff"></a>

## デプロイの対象
このテンプレートを使用して、イベント ハブを含んだ Event Hubs 名前空間をデプロイし、[Event Hubs Capture](event-hubs-capture-overview.md) も有効にします。

[Event Hubs](event-hubs-what-is-event-hubs.md) は、Azure への大規模なイベントとテレメトリ受信をわずかな待機時間と高い信頼性で提供するために使用される、イベント処理サービスです。 Event Hubs Capture を使用すると、指定した時間またはサイズの間隔で、Event Hubs のストリーミング データを任意の Azure Blob Storage に自動的に配信できます。

デプロイメントを自動的に実行するには、次のボタンをクリックします。

[![Azure へのデプロイ](./media/event-hubs-resource-manager-namespace-event-hub/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-eventhubs-create-namespace-and-enable-capture%2Fazuredeploy.json)

<a id="parameters" class="xliff"></a>

## パラメーター
Azure リソース マネージャーを使用して、テンプレートのデプロイ時に値を指定するパラメーターを定義します。 テンプレートには、すべてのパラメーター値を含む `Parameters` という名前のセクションがあります。 これらの値のパラメーターを定義する必要があります。これらの値は、デプロイするプロジェクトやデプロイ先の環境に応じて異なります。 常に同じ値に対してはパラメーターを定義しないでください。 テンプレート内のそれぞれのパラメーターの値は、デプロイされるリソースを定義するために使用されます。

このテンプレートでは、次のパラメーターを定義します。

<a id="eventhubnamespacename" class="xliff"></a>

### eventHubNamespaceName
作成する Event Hubs 名前空間の名前。

```json
"eventHubNamespaceName":{  
     "type":"string",
     "metadata":{  
         "description":"Name of the EventHub namespace"
      }
}
```

<a id="eventhubname" class="xliff"></a>

### eventHubName
Event Hubs 名前空間に作成するイベント ハブの名前。

```json
"eventHubName":{  
    "type":"string",
    "metadata":{  
        "description":"Name of the event hub"
    }
}
```

<a id="messageretentionindays" class="xliff"></a>

### messageRetentionInDays
イベント ハブにメッセージを保持する日数。 

```json
"messageRetentionInDays":{
    "type":"int",
    "defaultValue": 1,
    "minValue":"1",
    "maxValue":"7",
    "metadata":{
       "description":"How long to retain the data in event hub"
     }
 }
```

<a id="partitioncount" class="xliff"></a>

### partitionCount
イベント ハブに作成するパーティションの数。

```json
"partitionCount":{
    "type":"int",
    "defaultValue":2,
    "minValue":2,
    "maxValue":32,
    "metadata":{
        "description":"Number of partitions chosen"
    }
 }
```

<a id="captureenabled" class="xliff"></a>

### captureEnabled
イベント ハブの Capture を有効にします。

```json
"captureEnabled":{
    "type":"string",
    "defaultValue":"true",
    "allowedValues": [
    "false",
    "true"],
    "metadata":{
        "description":"Enable or disable the Capture for your event hub"
    }
 }
```
<a id="captureencodingformat" class="xliff"></a>

### captureEncodingFormat
イベント データのシリアル化に使用するエンコード形式。

```json
"captureEncodingFormat":{
    "type":"string",
    "defaultValue":"Avro",
    "allowedValues":[
    "Avro"],
    "metadata":{
        "description":"The encoding format in which Capture serializes the EventData"
    }
}
```

<a id="capturetime" class="xliff"></a>

### captureTime
Azure Blob Storage へのデータのキャプチャが Event Hubs Capture で開始される時間間隔。

```json
"captureTime":{
    "type":"int",
    "defaultValue":300,
    "minValue":60,
    "maxValue":900,
    "metadata":{
         "description":"the time window in seconds for the capture"
    }
}
```

<a id="capturesize" class="xliff"></a>

### captureSize
Azure Blob Storage へのデータのキャプチャが Capture で開始されるサイズ間隔。

```json
"captureSize":{
    "type":"int",
    "defaultValue":314572800,
    "minValue":10485760,
    "maxValue":524288000,
    "metadata":{
        "description":"The size window in bytes for capture"
    }
}
```

<a id="destinationstorageaccountresourceid" class="xliff"></a>

### destinationStorageAccountResourceId
目的のストレージ アカウントへのキャプチャを有効にするには、Azure ストレージ アカウントのリソース ID が必要になります。

```json
 "destinationStorageAccountResourceId":{
    "type":"string",
    "metadata":{
        "description":"Your existing Storage account resource id where you want the blobs be captured"
    }
 }
```

<a id="blobcontainername" class="xliff"></a>

### blobContainerName
イベント データをキャプチャする BLOB コンテナー。

```json
 "blobContainerName":{
    "type":"string",
    "metadata":{
        "description":"Your existing storage container in which you want the blobs captured"
    }
}
```


<a id="apiversion" class="xliff"></a>

### apiVersion
テンプレートの API バージョン。

```json
 "apiVersion":{  
    "type":"string",
    "defaultValue":"2015-08-01",
    "metadata":{  
        "description":"ApiVersion used by the template"
    }
 }
```

<a id="resources-to-deploy" class="xliff"></a>

## デプロイ対象のリソース
1 つのイベント ハブを含んだ **EventHubs** 型の名前空間を作成し、Capture も有効にします。

```json
"resources":[  
      {  
         "apiVersion":"[variables('ehVersion')]",
         "name":"[parameters('eventHubNamespaceName')]",
         "type":"Microsoft.EventHub/Namespaces",
         "location":"[variables('location')]",
         "sku":{  
            "name":"Standard",
            "tier":"Standard"
         },
         "resources":[  
            {  
               "apiVersion":"[variables('ehVersion')]",
               "name":"[parameters('eventHubName')]",
               "type":"EventHubs",
               "dependsOn":[  
                  "[concat('Microsoft.EventHub/namespaces/', parameters('eventHubNamespaceName'))]"
               ],
               "properties":{  
                  "path":"[parameters('eventHubName')]",
                  "MessageRetentionInDays":"[parameters('messageRetentionInDays')]",
                  "PartitionCount":"[parameters('partitionCount')]",
                  "CaptureDescription":{
                        "enabled":"[parameters('captureEnabled')]",
                        "encoding":"[parameters('captureEncodingFormat')]",
                        "intervalInSeconds":"[parameters('captureTime')]",
                        "sizeLimitInBytes":"[parameters('captureSize')]",
                        "destination":{
                            "name":"EventHubCapture.AzureBlockBlob",
                            "properties":{
                                "StorageAccountResourceId":"[parameters('destinationStorageAccountResourceId')]",
                                "BlobContainer":"[parameters('blobContainerName')]"
                            }
                        } 
                  }

               }

            }
         ]
      }
   ]
```

<a id="commands-to-run-deployment" class="xliff"></a>

## デプロイを実行するコマンド
[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

<a id="powershell" class="xliff"></a>

## PowerShell
```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName \<resource-group-name\> -TemplateFile https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-eventhubs-create-namespace-and-enable-capture/azuredeploy.json
```

<a id="azure-cli" class="xliff"></a>

## Azure CLI
```cli
azure config mode arm

azure group deployment create \<my-resource-group\> \<my-deployment-name\> --template-uri [https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-eventhubs-create-namespace-and-enable-capture/azuredeploy.json][]
```
<a id="next-steps" class="xliff"></a>

## 次のステップ

[Azure Portal](https://portal.azure.com) を使用して Event Hubs Capture を構成することもできます。 詳細については、「[Azure Portal を使用して Event Hubs Capture を有効にする](event-hubs-capture-enable-through-portal.md)」を参照してください。

Event Hubs の詳細については、次のリンク先を参照してください:

* [Event Hubs の概要](event-hubs-what-is-event-hubs.md)
* [イベント ハブの作成](event-hubs-create.md)
* [Event Hubs の FAQ](event-hubs-faq.md)

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/resource-group-authoring-templates.md
[Azure Quickstart Templates]:  https://azure.microsoft.com/documentation/templates/?term=event+hubs
[Using Azure PowerShell with Azure Resource Manager]: ../powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../xplat-cli-azure-resource-manager.md
[Azure Resources Naming Conventions]: https://azure.microsoft.com/documentation/articles/guidance-naming-conventions/
[Event hub and enable Capture template]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-eventhubs-create-namespace-and-enable-capture

