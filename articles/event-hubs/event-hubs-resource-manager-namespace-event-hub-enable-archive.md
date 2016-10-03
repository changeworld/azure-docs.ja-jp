<properties
    pageTitle="Azure Resource Manager テンプレートでイベント ハブを含んだ Event Hubs 名前空間を作成してアーカイブを有効にする | Microsoft Azure"
    description="Azure Resource Manager テンプレートでイベント ハブを含んだ Event Hubs 名前空間を作成してアーカイブを有効にします。"
    services="event-hubs"
    documentationCenter=".net"
    authors="ShubhaVijayasarathy"
    manager="timlt"
    editor=""/>

<tags
    ms.service="event-hubs"
    ms.devlang="tbd"
    ms.topic="article"
    ms.tgt_pltfrm="dotnet"
    ms.workload="na"
    ms.date="09/14/2016"
    ms.author="ShubhaVijayasarathy"/>

# Azure Resource Manager テンプレートでイベント ハブを含んだ Event Hubs 名前空間を作成してアーカイブを有効にする

この記事では、Azure Resource Manager テンプレートを使用し、イベント ハブを含んだ Event Hubs 名前空間を作成して、イベント ハブのアーカイブを有効にする方法について説明します。デプロイ対象のリソースを定義する方法と、デプロイの実行時に指定されるパラメーターを定義する方法について説明します。このテンプレートは、独自のデプロイに使用することも、要件に合わせてカスタマイズすることもできます。

テンプレートの作成の詳細については、「[Azure Resource Manager のテンプレートの作成][]」を参照してください。

Azure リソースの名前付け規則のプラクティスとパターンの詳細については、[Azure リソースの名前付け規則][]に関するページを参照してください。

完全なテンプレートについては、GitHub にアクセスし、[イベント ハブと、アーカイブ テンプレートの有効化][]に関する記事をご覧ください。

>[AZURE.NOTE]
最新のテンプレートを確認する場合は、「[Azure クイックスタート テンプレート][]」ギャラリーで "Event Hubs" を検索してください。

## デプロイの対象

このテンプレートを使用して、イベント ハブを含んだ Event Hubs 名前空間をデプロイし、アーカイブを有効にします。

[Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md) は、Azure への大規模なイベントとテレメトリ受信をわずかな待機時間と高い信頼性で提供するために使用される、イベント処理サービスです。Event Hubs Archive を使用すると、指定した時間またはサイズの間隔で、Event Hubs のストリーミング データを任意の Azure BLOB ストレージに自動的に配信できます。

デプロイメントを自動的に実行するには、次のボタンをクリックします。

[![Azure へのデプロイ](./media/event-hubs-resource-manager-namespace-event-hub/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-eventhubs-create-namespace-and-enable-archive%2Fazuredeploy.json)

## パラメーター

Azure リソース マネージャーを使用して、テンプレートのデプロイ時に値を指定するパラメーターを定義します。テンプレートには、すべてのパラメーター値を含む `Parameters` という名前のセクションがあります。これらの値のパラメーターを定義する必要があります。これらの値は、デプロイするプロジェクトやデプロイ先の環境に応じて異なります。常に同じ値に対してはパラメーターを定義しないでください。テンプレート内のそれぞれのパラメーターの値は、デプロイされるリソースを定義するために使用されます。

このテンプレートでは、次のパラメーターを定義します。

### eventHubNamespaceName

作成する Event Hubs 名前空間の名前。

```
"eventHubNamespaceName":{  
     "type":"string",
     "metadata":{  
         "description":"Name of the EventHub namespace"
      }
}
```

### eventHubName

Event Hubs 名前空間に作成するイベント ハブの名前。

```
"eventHubName":{  
    "type":"string",
    "metadata":{  
        "description":"Name of the Event Hub"
    }
}
```

### messageRetentionInDays

イベント ハブでメッセージを保持する日数。

```
"messageRetentionInDays":{
	"type":"int",
	"defaultValue": 1,
	"minValue":"1",
	"maxValue":"7",
	"metadata":{
	   "description":"How long to retain the data in Event Hub"
	 }
 }
```

### partitionCount

イベント ハブに作成するパーティションの数。

```
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

### archiveEnabled

イベント ハブ上のアーカイブを有効にします。

```
"archiveEnabled":{
	"type":"string",
	"defaultValue":"true",
	"allowedValues": [
	"false",
	"true"],
	"metadata":{
		"description":"Enable or disable the Archive for your Event Hub"
	}
 }
```
### archiveEncodingFormat

イベント データのシリアル化に使用するエンコード形式。

```
"archiveEncodingFormat":{
	"type":"string",
	"defaultValue":"Avro",
	"allowedValues":[
	"Avro"],
	"metadata":{
		"description":"The encoding format Archive serializes the EventData"
	}
}
```

### archiveTime

Azure BLOB ストレージへのデータのアーカイブが開始される時間間隔。

```
"archiveTime":{
	"type":"int",
	"defaultValue":300,
	"minValue":60,
	"maxValue":900,
	"metadata":{
		 "description":"the time window in seconds for the archival"
	}
}
```

### archiveSize

Azure BLOB ストレージへのデータのアーカイブが開始されるサイズの間隔。

```
"archiveSize":{
	"type":"int",
	"defaultValue":314572800,
	"minValue":10485760,
	"maxValue":524288000,
	"metadata":{
		"description":"the size window in bytes for archival"
	}
}
```

### destinationStorageAccountResourceId

目的の Azure Storage へのアーカイブを有効にするには、ストレージ アカウントのリソース ID が必要になります。

```
 "destinationStorageAccountResourceId":{
	"type":"string",
	"metadata":{
		"description":"Your existing storage Account resource id where you want the blobs be archived"
	}
 }
```

### blobContainerName

イベント データのアーカイブ先の BLOB コンテナー。

```
 "blobContainerName":{
	"type":"string",
	"metadata":{
		"description":"Your existing storage Container that you want the blobs archived in"
	}
}
```


### apiVersion

テンプレートの API バージョン。

```
 "apiVersion":{  
    "type":"string",
    "defaultValue":"2015-08-01",
    "metadata":{  
        "description":"ApiVersion used by the template"
    }
 }
```

## デプロイ対象のリソース

イベント ハブを含んだ、**EventHubs** タイプの名前空間を作成し、アーカイブを有効にします。

```
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
				  "ArchiveDescription":{
						"enabled":"[parameters('archiveEnabled')]",
						"encoding":"[parameters('archiveEncodingFormat')]",
						"intervalInSeconds":"[parameters('archiveTime')]",
						"sizeLimitInBytes":"[parameters('archiveSize')]",
						"destination":{
							"name":"EventHubArchive.AzureBlockBlob",
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

## デプロイを実行するコマンド

[AZURE.INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

## PowerShell

```
New-AzureRmResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateFile https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-eventhubs-create-namespace-and-enable-archive/azuredeploy.json
```

## Azure CLI

```
azure config mode arm

azure group deployment create <my-resource-group> <my-deployment-name> --template-uri [https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-eventhubs-create-namespace-and-enable-archive/azuredeploy.json][]
```

## 次のステップ

Azure Resource Manager を使ってリソースを作成、デプロイしたら、それらのリソースを管理する方法を次の記事で確認しましょう。

- [Service Bus Explorer を使用した Event Hubs リソースの管理](https://code.msdn.microsoft.com/Service-Bus-Explorer-f2abca5a)

  [Azure Resource Manager のテンプレートの作成]: ../resource-group-authoring-templates.md
  [Azure クイックスタート テンプレート]: https://azure.microsoft.com/documentation/templates/?term=event+hubs
  [Using Azure PowerShell with Azure Resource Manager]: ../powershell-azure-resource-manager.md
  [Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../xplat-cli-azure-resource-manager.md
  [Event Hub and consumer group template]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-eventhubs-create-namespace-and-enable-archive/
  [Azure リソースの名前付け規則]: https://azure.microsoft.com/documentation/articles/guidance-naming-conventions/
  [イベント ハブと、アーカイブ テンプレートの有効化]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-eventhubs-create-namespace-and-enable-archive

<!---HONumber=AcomDC_0921_2016-->