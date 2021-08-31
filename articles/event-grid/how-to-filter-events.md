---
title: Azure Event Grid のイベントをフィルター処理する方法
description: この記事では、Event Grid サブスクリプションを作成するときに、イベントをフィルター処理 (イベントの種類、件名、演算子、データなど) する方法について説明します。
ms.topic: conceptual
ms.date: 08/11/2021
ms.openlocfilehash: 666690fc7c0d21c7ae257a20b49949961d89cb60
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121744626"
---
# <a name="filter-events-for-event-grid"></a>Event Grid のイベントのフィルター処理

この記事では、Event Grid サブスクリプションを作成するときにイベントをフィルター処理する方法について説明します。 イベントのフィルター処理のオプションの詳細については、「[Understand event filtering for Event Grid subscriptions (Event Grid サブスクリプションのイベントのフィルター処理について)](event-filtering.md)」を参照してください。

## <a name="filter-by-event-type"></a>イベントの種類でフィルター処理する

Event Grid サブスクリプションを作成するときに、エンドポイントに送信する[イベントの種類](event-schema.md)を指定できます。 このセクションの例では、リソース グループのイベント サブスクリプションを作成しますが、送信されるイベントを `Microsoft.Resources.ResourceWriteFailure` と `Microsoft.Resources.ResourceWriteSuccess` に限定しています。 イベントの種類でイベントをフィルター処理する際にさらに柔軟性が必要な場合は、高度な演算子とデータ フィールドを使用したフィルター処理に関するセクションを参照してください。

### <a name="azure-powershell"></a>Azure PowerShell
PowerShell の場合は、サブスクリプションの作成時に `-IncludedEventType` パラメーターを使用します。

```powershell
$includedEventTypes = "Microsoft.Resources.ResourceWriteFailure", "Microsoft.Resources.ResourceWriteSuccess"

New-AzEventGridSubscription `
  -EventSubscriptionName demoSubToResourceGroup `
  -ResourceGroupName myResourceGroup `
  -Endpoint <endpoint-URL> `
  -IncludedEventType $includedEventTypes
```

### <a name="azure-cli"></a>Azure CLI
Azure CLI の場合は、`--included-event-types` パラメーターを使用します。 次の例では、Bash シェルで Azure CLI を使用しています。

```azurecli
includedEventTypes="Microsoft.Resources.ResourceWriteFailure Microsoft.Resources.ResourceWriteSuccess"

az eventgrid event-subscription create \
  --name demoSubToResourceGroup \
  --resource-group myResourceGroup \
  --endpoint <endpoint-URL> \
  --included-event-types $includedEventTypes
```

### <a name="azure-portal"></a>Azure portal

1. **[イベント サブスクリプション]** ページで **[フィルター]** タブに切り替えます。 
1. **[イベントの種類のフィルター]** の横にある **[イベントの種類の追加]** を選択します。 

    :::image type="content" source="./media/how-to-filter-events/add-event-type-button.png" alt-text="[イベントの種類の追加] ボタンが選択されている [イベント サブスクリプション] ページのスクリーンショット。":::    
1. イベントの種類を入力し、Enter キーを押します。 次の例では、イベントの種類は `Microsoft.Resources.ResourceWriteSuccess` です。 

    :::image type="content" source="./media/how-to-filter-events/sample-event-type.png" alt-text="サンプルのイベントの種類が指定された [イベント サブスクリプション] ページのスクリーンショット。":::    
      
### <a name="azure-resource-manager-template"></a>Azure Resource Manager テンプレート
Resource Manager テンプレートの場合は、`includedEventTypes` プロパティを使用します。

```json
"resources": [
  {
    "type": "Microsoft.EventGrid/eventSubscriptions",
    "name": "[parameters('eventSubName')]",
    "apiVersion": "2018-09-15-preview",
    "properties": {
      "destination": {
        "endpointType": "WebHook",
        "properties": {
          "endpointUrl": "[parameters('endpoint')]"
        }
      },
      "filter": {
        "subjectBeginsWith": "",
        "subjectEndsWith": "",
        "isSubjectCaseSensitive": false,
        "includedEventTypes": [
          "Microsoft.Resources.ResourceWriteFailure",
          "Microsoft.Resources.ResourceWriteSuccess"
        ]
      }
    }
  }
]
```

> [!NOTE]
> これらのフィルター (イベントの種類、件名、および高度) の詳細については、「[Event Grid サブスクリプションでのイベントのフィルター処理を理解する](event-filtering.md)」を参照してください。 

## <a name="filter-by-subject"></a>件名でフィルター処理する

イベント データの件名でイベントをフィルター処理できます。 件名の先頭または末尾で照合する値を指定できます。 件名でイベントをフィルター処理する際にさらに柔軟性が必要な場合は、高度な演算子とデータ フィールドを使用したフィルター処理に関するセクションを参照してください。

次の PowerShell の例では、件名の先頭でフィルター処理するイベント サブスクリプションを作成しています。 `-SubjectBeginsWith` パラメーターを使用して、イベントを特定のリソースのイベントに限定しています。 ネットワーク セキュリティ グループのリソース ID を渡します。

### <a name="azure-powershell"></a>Azure PowerShell
```powershell
$resourceId = (Get-AzResource -ResourceName demoSecurityGroup -ResourceGroupName myResourceGroup).ResourceId

New-AzEventGridSubscription `
  -Endpoint <endpoint-URL> `
  -EventSubscriptionName demoSubscriptionToResourceGroup `
  -ResourceGroupName myResourceGroup `
  -SubjectBeginsWith $resourceId
```

次の PowerShell の例では、BLOB ストレージのサブスクリプションを作成しています。 ここでは、イベントは、件名の末尾が `.jpg` のイベントに限定されています。

```powershell
$storageId = (Get-AzStorageAccount -ResourceGroupName myResourceGroup -AccountName $storageName).Id

New-AzEventGridSubscription `
  -EventSubscriptionName demoSubToStorage `
  -Endpoint <endpoint-URL> `
  -ResourceId $storageId `
  -SubjectEndsWith ".jpg"
```

### <a name="azure-cli"></a>Azure CLI
次の Azure CLI の例では、件名の先頭でフィルター処理するイベント サブスクリプションを作成しています。 `--subject-begins-with` パラメーターを使用して、イベントを特定のリソースのイベントに限定しています。 ネットワーク セキュリティ グループのリソース ID を渡します。

```azurecli
resourceId=$(az resource show --name demoSecurityGroup --resource-group myResourceGroup --resource-type Microsoft.Network/networkSecurityGroups --query id --output tsv)

az eventgrid event-subscription create \
  --name demoSubscriptionToResourceGroup \
  --resource-group myResourceGroup \
  --endpoint <endpoint-URL> \
  --subject-begins-with $resourceId
```

次の Azure CLI の例では、BLOB ストレージのサブスクリプションを作成しています。 ここでは、イベントは、件名の末尾が `.jpg` のイベントに限定されています。

```azurecli
storageid=$(az storage account show --name $storageName --resource-group myResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --resource-id $storageid \
  --name demoSubToStorage \
  --endpoint <endpoint-URL> \
  --subject-ends-with ".jpg"
```

### <a name="azure-portal"></a>Azure portal

1. **[イベント サブスクリプション]** ページで、 **[サブジェクト フィルタリングを有効にする]** を選択します。 
1. **[次で始まるサブジェクト]** と **[次で終わるサブジェクト]** のフィールドの 1 つ以上に値を入力します。 次のオプションでは、両方のオプションが選択されています。 

    :::image type="content" source="./media/how-to-filter-events/subject-filter-example.png" alt-text="件名のフィルタリングの例を含む [イベント サブスクリプション] ページのスクリーンショット。":::
1. 指定したフィルターの大文字と小文字とイベントの件名が一致する必要がある場合は、 **[大文字小文字を区別した件名の一致]** オプションを選択します。 

### <a name="azure-resource-manager-template"></a>Azure Resource Manager テンプレート
次の Resource Manager テンプレートの例では、件名の先頭でフィルター処理するイベント サブスクリプションを作成しています。 `subjectBeginsWith` プロパティを使用して、イベントを特定のリソースのイベントに限定しています。 ネットワーク セキュリティ グループのリソース ID を渡します。

```json
"resources": [
  {
    "type": "Microsoft.EventGrid/eventSubscriptions",
    "name": "[parameters('eventSubName')]",
    "apiVersion": "2018-09-15-preview",
    "properties": {
      "destination": {
        "endpointType": "WebHook",
        "properties": {
          "endpointUrl": "[parameters('endpoint')]"
        }
      },
      "filter": {
        "subjectBeginsWith": "[resourceId('Microsoft.Network/networkSecurityGroups','demoSecurityGroup')]",
        "subjectEndsWith": "",
        "isSubjectCaseSensitive": false,
        "includedEventTypes": [ "All" ]
      }
    }
  }
]
```

次の Resource Manager テンプレートの例では、BLOB ストレージのサブスクリプションを作成しています。 ここでは、イベントは、件名の末尾が `.jpg` のイベントに限定されています。

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts/providers/eventSubscriptions",
    "name": "[concat(parameters('storageName'), '/Microsoft.EventGrid/', parameters('eventSubName'))]",
    "apiVersion": "2018-09-15-preview",
    "properties": {
      "destination": {
        "endpointType": "WebHook",
        "properties": {
          "endpointUrl": "[parameters('endpoint')]"
        }
      },
      "filter": {
        "subjectEndsWith": ".jpg",
        "subjectBeginsWith": "",
        "isSubjectCaseSensitive": false,
        "includedEventTypes": [ "All" ]
      }
    }
  }
]
```

> [!NOTE]
> これらのフィルター (イベントの種類、件名、および高度) の詳細については、「[Event Grid サブスクリプションでのイベントのフィルター処理を理解する](event-filtering.md)」を参照してください。 

## <a name="filter-by-operators-and-data"></a>演算子とデータでフィルター処理する

フィルター処理の柔軟性を高めるために、演算子とデータ プロパティを使用してイベントをフィルター処理することもできます。

### <a name="subscribe-with-advanced-filters"></a>高度なフィルターを使用してサブスクライブする

高度なフィルター処理に使用できる演算子とキーについては、「[Advanced filtering (高度なフィルター処理)](event-filtering.md#advanced-filtering)」を参照してください。

以下の例では、カスタム トピックを作成します。 これらの例では、カスタム トピックをサブスクライブし、データ オブジェクト内の値でフィルター処理を行います。 color プロパティが blue、red、または green に設定されているイベントがサブスクリプションに送信されます。

### <a name="azure-powershell"></a>Azure PowerShell

PowerShell では、次を使用します。

```powershell
$topicName = <your-topic-name>
$endpointURL = <endpoint-URL>

New-AzResourceGroup -Name gridResourceGroup -Location eastus2
New-AzEventGridTopic -ResourceGroupName gridResourceGroup -Location eastus2 -Name $topicName

$topicid = (Get-AzEventGridTopic -ResourceGroupName gridResourceGroup -Name $topicName).Id

$expDate = '<mm/dd/yyyy hh:mm:ss>' | Get-Date
$AdvFilter1=@{operator="StringIn"; key="Data.color"; Values=@('blue', 'red', 'green')}

New-AzEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint $endpointURL `
  -ExpirationDate $expDate `
  -AdvancedFilter @($AdvFilter1)
```

### <a name="azure-cli"></a>Azure CLI

Azure CLI では、次を使用します。

```azurecli
topicName=<your-topic-name>
endpointURL=<endpoint-URL>

az group create -n gridResourceGroup -l eastus2
az eventgrid topic create --name $topicName -l eastus2 -g gridResourceGroup

topicid=$(az eventgrid topic show --name $topicName -g gridResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --source-resource-id $topicid \
  -n demoAdvancedSub \
  --advanced-filter data.color stringin blue red green \
  --endpoint $endpointURL \
  --expiration-date "<yyyy-mm-dd>"
```

サブスクリプションに[有効期限の日付](concepts.md#event-subscription-expiration)が設定されていることに注意してください。


### <a name="azure-portal"></a>Azure portal

1. **[イベント サブスクリプション]** ページで、 **[高度なフィルター]** セクションの **[新しいフィルターの追加]** を選択します。 

    :::image type="content" source="./media/how-to-filter-events/add-new-filter-button.png" alt-text="[新しいフィルターの追加] リンクが強調表示されている [イベント サブスクリプション] ページを示すスクリーンショット。":::    
2. キー、演算子、および比較する 1 つ以上の値を指定します。 次の例では、**data.color** はキーとして、**String is in** (次に含まれる文字列) は演算子として使用され、値に対して **blue**、**red**、**green** の値が指定されています。 

    :::image type="content" source="./media/how-to-filter-events/advanced-filter-example.png" alt-text="高度なフィルターの例を示すスクリーンショット。"::: 

    > [!NOTE]
    > 高度なフィルターの詳細については、「[Event Grid サブスクリプションでのイベントのフィルター処理を理解する](event-filtering.md)」を参照してください。 


### <a name="test-the-filter"></a>フィルターをテストする
フィルターをテストするには、color フィールドが green に設定されているイベントを送信します。 green はフィルターに含まれている値の 1 つなので、このイベントはエンドポイントに配信されます。

### <a name="azure-powershell"></a>Azure PowerShell
PowerShell では、次を使用します。

```powershell
$endpoint = (Get-AzEventGridTopic -ResourceGroupName gridResourceGroup -Name $topicName).Endpoint
$keys = Get-AzEventGridTopicKey -ResourceGroupName gridResourceGroup -Name $topicName

$eventID = Get-Random 99999
$eventDate = Get-Date -Format s

$htbody = @{
    id= $eventID
    eventType="recordInserted"
    subject="myapp/vehicles/cars"
    eventTime= $eventDate
    data= @{
        model="SUV"
        color="green"
    }
    dataVersion="1.0"
}

$body = "["+(ConvertTo-Json $htbody)+"]"

Invoke-WebRequest -Uri $endpoint -Method POST -Body $body -Headers @{"aeg-sas-key" = $keys.Key1}
```

イベントが送信されないシナリオをテストするには、color フィールドが yellow に設定されているイベントを送信します。 yellow はサブスクリプションで指定された値の 1 つではないため、イベントはサブスクリプションに配信されません。

```powershell
$htbody = @{
    id= $eventID
    eventType="recordInserted"
    subject="myapp/vehicles/cars"
    eventTime= $eventDate
    data= @{
        model="SUV"
        color="yellow"
    }
    dataVersion="1.0"
}

$body = "["+(ConvertTo-Json $htbody)+"]"

Invoke-WebRequest -Uri $endpoint -Method POST -Body $body -Headers @{"aeg-sas-key" = $keys.Key1}
```


### <a name="azure-cli"></a>Azure CLI
Azure CLI では、次を使用します。

```azurecli
topicEndpoint=$(az eventgrid topic show --name $topicName -g gridResourceGroup --query "endpoint" --output tsv)
key=$(az eventgrid topic key list --name $topicName -g gridResourceGroup --query "key1" --output tsv)

event='[ {"id": "'"$RANDOM"'", "eventType": "recordInserted", "subject": "myapp/vehicles/cars", "eventTime": "'`date +%Y-%m-%dT%H:%M:%S%z`'", "data":{ "model": "SUV", "color": "green"},"dataVersion": "1.0"} ]'

curl -X POST -H "aeg-sas-key: $key" -d "$event" $topicEndpoint
```

イベントが送信されないシナリオをテストするには、color フィールドが yellow に設定されているイベントを送信します。 yellow はサブスクリプションで指定された値の 1 つではないため、イベントはサブスクリプションに配信されません。

Azure CLI では、次を使用します。

```azurecli
event='[ {"id": "'"$RANDOM"'", "eventType": "recordInserted", "subject": "myapp/vehicles/cars", "eventTime": "'`date +%Y-%m-%dT%H:%M:%S%z`'", "data":{ "model": "SUV", "color": "yellow"},"dataVersion": "1.0"} ]'

curl -X POST -H "aeg-sas-key: $key" -d "$event" $topicEndpoint
```

## <a name="next-steps"></a>次のステップ
フィルター (イベントの種類、件名、および高度) の詳細を理解するには、「[Event Grid サブスクリプションでのイベントのフィルター処理を理解する](event-filtering.md)」を参照します。 

