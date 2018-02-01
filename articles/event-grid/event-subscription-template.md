---
title: "テンプレートを使用した Azure Event Grid サブスクリプション"
description: "Resource Manager テンプレートを使用して Event Grid サブスクリプションを作成します。"
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 01/30/2018
ms.author: tomfitz
ms.openlocfilehash: ee0b2c228ae4ea53c0ee9794529aa190334ceed9
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/01/2018
---
# <a name="use-resource-manager-template-for-event-grid-subscription"></a>Event Grid サブスクリプションに Resource Manager テンプレートを使用する

この記事では、Azure Resource Manager テンプレートを使用して Event Grid サブスクリプションを作成する方法について説明します。 使用する形式は、リソース グループ イベントにサブスクライブするか、特定のリソースの種類のイベントにサブスクライブするかによって異なります。 この記事では、両方の形式について紹介します。

## <a name="subscribe-to-resource-group-events"></a>リソース グループ イベントにサブスクライブする

リソース グループ イベントにサブスクライブするときは、リソースの種類に `Microsoft.EventGrid/eventSubscriptions` を使用します。 イベント ポイントの種類の場合は、`WebHook` または `EventHub` を使用します。

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "resources": [
        {
            "type": "Microsoft.EventGrid/eventSubscriptions",
            "name": "mySubscription",
            "apiVersion": "2018-01-01",
            "properties": {
                "destination": {
                    "endpointType": "WebHook",
                    "properties": {
                        "endpointUrl": "https://requestb.in/ynboxiyn"
                    }
                },
                "filter": {
                    "subjectBeginsWith": "",
                    "subjectEndsWith": "",
                    "isSubjectCaseSensitive": false,
                    "includedEventTypes": ["All"]
                }
            }
        }
    ]
}
```

このテンプレートをリソース グループに展開すると、そのリソース グループのイベントにサブスクライブされます。

## <a name="subscribe-to-resource-events"></a>リソース イベントにサブスクライブする

リソース イベントにサブスクライブするときに、サブスクリプション定義にリソースの種類と名前を含めることで、サブスクリプションを正しいリソースに関連付けます。 リソースの種類の場合は、`<provider-namespace>/<resource-type>/providers/eventSubscriptions` を使用します。 名前の場合は、`<resource-name>/Microsoft.EventGrid/<subscription-name>` を使用します。 イベント ポイントの種類の場合は、`WebHook` または `EventHub` を使用します。

次の例は、BLOB ストレージ イベントをサブスクライブする方法を示しています。

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageName": {
            "type": "string"
        }
    },
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts/providers/eventSubscriptions",
            "name": "[concat(parameters('storageName'), '/Microsoft.EventGrid/myStorageSubscription')]",
            "apiVersion": "2018-01-01",
            "properties": {
                "destination": {
                    "endpointType": "WebHook",
                    "properties": {
                        "endpointUrl": "https://requestb.in/ynboxiyn"
                    }
                },
                "filter": {
                    "subjectBeginsWith": "",
                    "subjectEndsWith": "",
                    "isSubjectCaseSensitive": false
                }
            }
        }
    ]
}
```

## <a name="next-steps"></a>次の手順

* Event Grid の概要については、[Event Grid の紹介](overview.md)に関する記事を参照してください。
* Resource Manager の概要については、「[Azure Resource Manager の概要](../azure-resource-manager/resource-group-overview.md)」を参照してください。
* Event Grid の概要については、[Event Grid を使用したカスタム イベントの作成とルーティング](custom-event-quickstart.md)に関するページを参照してください。