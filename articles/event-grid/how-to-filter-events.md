---
title: Azure Event Grid のイベントをフィルター処理する方法
description: イベントをフィルター処理する Azure Event Grid サブスクリプションを作成する方法を示します。
services: event-grid
author: tfitzmac
ms.service: event-grid
ms.topic: conceptual
ms.date: 10/29/2018
ms.author: tomfitz
ms.openlocfilehash: 8bf7ac9daf928c35a3d6efcac528d3372fa87c8a
ms.sourcegitcommit: 1d3353b95e0de04d4aec2d0d6f84ec45deaaf6ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2018
ms.locfileid: "50252044"
---
# <a name="filter-events-for-event-grid"></a>Event Grid のイベントのフィルター処理

この記事では、Event Grid サブスクリプションを作成するときにイベントをフィルター処理する方法について説明します。 イベントのフィルター処理のオプションの詳細については、「[Understand event filtering for Event Grid subscriptions (Event Grid サブスクリプションのイベントのフィルター処理について)](event-filtering.md)」を参照してください。

## <a name="filter-by-event-type"></a>イベントの種類でフィルター処理する

Event Grid サブスクリプションを作成するときに、エンドポイントに送信する[イベントの種類](event-schema.md)を指定できます。 このセクションの例では、リソース グループのイベント サブスクリプションを作成しますが、送信されるイベントを `Microsoft.Resources.ResourceWriteFailure` と `Microsoft.Resources.ResourceWriteSuccess` に限定しています。 イベントの種類でイベントをフィルター処理する際にさらに柔軟性が必要な場合は、[高度な演算子とデータ フィールドを使用したフィルター処理](#filter-by-advanced-operators-and-data-fields)に関するセクションを参照してください。

PowerShell の場合は、サブスクリプションの作成時に `-IncludedEventType` パラメーターを使用します。

```powershell
$includedEventTypes = "Microsoft.Resources.ResourceWriteFailure", "Microsoft.Resources.ResourceWriteSuccess"

New-AzureRmEventGridSubscription `
  -EventSubscriptionName demoSubToResourceGroup `
  -ResourceGroupName myResourceGroup `
  -Endpoint <endpoint-URL> `
  -IncludedEventType $includedEventTypes
```

Azure CLI の場合は、`--included-event-types` パラメーターを使用します。 次の例では、Bash シェルで Azure CLI を使用しています。

```azurecli
includedEventTypes="Microsoft.Resources.ResourceWriteFailure Microsoft.Resources.ResourceWriteSuccess"

az eventgrid event-subscription create \
  --name demoSubToResourceGroup \
  --resource-group myResourceGroup \
  --endpoint <endpoint-URL> \
  --included-event-types $includedEventTypes
```

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

## <a name="filter-by-subject"></a>件名でフィルター処理する

イベント データの件名でイベントをフィルター処理できます。 件名の先頭または末尾で照合する値を指定できます。 件名でイベントをフィルター処理する際にさらに柔軟性が必要な場合は、[高度な演算子とデータ フィールドを使用したフィルター処理](#filter-by-advanced-operators-and-data-fields)に関するセクションを参照してください。

次の PowerShell の例では、件名の先頭でフィルター処理するイベント サブスクリプションを作成しています。 `-SubjectBeginsWith` パラメーターを使用して、イベントを特定のリソースのイベントに限定しています。 ネットワーク セキュリティ グループのリソース ID を渡します。

```powershell
$resourceId = (Get-AzureRmResource -ResourceName demoSecurityGroup -ResourceGroupName myResourceGroup).ResourceId

New-AzureRmEventGridSubscription `
  -Endpoint <endpoint-URL> `
  -EventSubscriptionName demoSubscriptionToResourceGroup `
  -ResourceGroupName myResourceGroup `
  -SubjectBeginsWith $resourceId
```

次の PowerShell の例では、BLOB ストレージのサブスクリプションを作成しています。 ここでは、イベントは、件名の末尾が `.jpg` のイベントに限定されています。

```powershell
$storageId = (Get-AzureRmStorageAccount -ResourceGroupName myResourceGroup -AccountName $storageName).Id

New-AzureRmEventGridSubscription `
  -EventSubscriptionName demoSubToStorage `
  -Endpoint <endpoint-URL> `
  -ResourceId $storageId `
  -SubjectEndsWith ".jpg"
```

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

## <a name="filter-by-operators-and-data"></a>演算子とデータでフィルター処理する

高度なフィルター処理を使用するには、Azure CLI のプレビュー拡張機能をインストールする必要があります。 [CloudShell](/azure/cloud-shell/quickstart) を使用するか、Azure CLI をローカルにインストールできます。

### <a name="install-extension"></a>拡張機能のインストール

CloudShell の場合:

* 拡張機能を以前にインストールしている場合は、`az extension update -n eventgrid` で拡張機能を更新します。
* 拡張機能を以前にインストールしていない場合は、`az extension add -n eventgrid` で拡張機能をインストールします。

ローカル インストールの場合:

1. ローカルの Azure CLI をアンインストールします。
1. [最新バージョン](/cli/azure/install-azure-cli)の Azure CLI をインストールします。
1. コマンド ウィンドウを起動します。
1. `az extension remove -n eventgrid` で以前のバージョンの拡張機能をアンインストールします。
1. `az extension add -n eventgrid` で拡張機能をインストールします。

これで、高度なフィルター処理を使用する準備が整いました。

### <a name="subscribe-with-advanced-filters"></a>高度なフィルターを使用してサブスクライブする

高度なフィルター処理に使用できる演算子とキーについては、「[Advanced filtering (高度なフィルター処理)](event-filtering.md#advanced-filtering)」を参照してください。

次の例では、カスタム トピックを作成しています。 カスタム トピックをサブスクライブし、データ オブジェクト内の値でフィルター処理します。 color プロパティが blue、red、または green に設定されているイベントがサブスクリプションに送信されます。

```azurecli-interactive
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
  --expiration-date "2018-11-30"
```

サブスクリプションに有効期限の日付が設定されていることに注意してください。 イベント サブスクリプションは、その日付後、自動的に期限切れになります。 限定された期間にだけ必要なイベント サブスクリプションに有効期限を設定します。

### <a name="test-filter"></a>フィルターをテストする

フィルターをテストするには、color フィールドが green に設定されているイベントを送信します。

```azurecli-interactive
topicEndpoint=$(az eventgrid topic show --name $topicName -g gridResourceGroup --query "endpoint" --output tsv)
key=$(az eventgrid topic key list --name $topicName -g gridResourceGroup --query "key1" --output tsv)

event='[ {"id": "'"$RANDOM"'", "eventType": "recordInserted", "subject": "myapp/vehicles/cars", "eventTime": "'`date +%Y-%m-%dT%H:%M:%S%z`'", "data":{ "model": "SUV", "color": "green"},"dataVersion": "1.0"} ]'

curl -X POST -H "aeg-sas-key: $key" -d "$event" $topicEndpoint
```

イベントは、エンドポイントに送信されます。

イベントが送信されないシナリオをテストするには、color フィールドが yellow に設定されているイベントを送信します。

```azurecli-interactive
event='[ {"id": "'"$RANDOM"'", "eventType": "recordInserted", "subject": "myapp/vehicles/cars", "eventTime": "'`date +%Y-%m-%dT%H:%M:%S%z`'", "data":{ "model": "SUV", "color": "yellow"},"dataVersion": "1.0"} ]'

curl -X POST -H "aeg-sas-key: $key" -d "$event" $topicEndpoint
```

yellow はサブスクリプションで指定された値の 1 つではないため、イベントはサブスクリプションに配信されません。

## <a name="next-steps"></a>次の手順

* イベント配信の監視について詳しくは、「[Event Grid メッセージ配信の監視](monitor-event-delivery.md)」をご覧ください。
* 認証キーについて詳しくは、「[Event Grid のセキュリティと認証](security-authentication.md)」をご覧ください。
* Azure Event Grid サブスクリプションの作成の詳細については、[Event Grid サブスクリプション スキーマ](subscription-creation-schema.md)に関する記事を参照してください。
