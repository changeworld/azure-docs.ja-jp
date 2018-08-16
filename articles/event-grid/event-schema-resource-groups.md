---
title: Azure Event Grid のリソース グループ用のイベント スキーマ
description: Azure Event Grid のリソース グループ イベントに対して用意されているプロパティについて説明します
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: reference
ms.date: 08/02/2018
ms.author: tomfitz
ms.openlocfilehash: 407d9fd5b6f4d554af37b60edf12422f8816ac00
ms.sourcegitcommit: eaad191ede3510f07505b11e2d1bbfbaa7585dbd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/03/2018
ms.locfileid: "39495324"
---
# <a name="azure-event-grid-event-schema-for-resource-groups"></a>Azure Event Grid のリソース グループ用のイベント スキーマ

この記事では、リソース グループ イベントのプロパティとスキーマについて説明します。 イベント スキーマの概要については、「[Azure Event Grid イベント スキーマ](event-schema.md)」を参照してください。

Azure サブスクリプションとリソース グループは、同じ種類のイベントを出力します。 イベントの種類は、リソースの変更に関連しています。 主な違いは、リソース グループはリソース グループ内のリソースのイベントを出力し、Azure サブスクリプションはサブスクリプションのリソースのイベントを出力することです。

リソース イベントは、`management.azure.com` に送信される PUT、PATCH、および DELETE 操作に対して作成されます。 POST 操作と GET 操作は、イベントを作成しません。 データ プレーンに送信される操作 (`myaccount.blob.core.windows.net` など) は、イベントを作成しません。

リソース グループのイベントをサブスクライブすると、エンドポイントは、そのリソース グループのすべてのイベントを受信します。 イベントには、表示するイベント (仮想マシンの更新など) を含めることができますが、重要とは思えないイベント (デプロイ履歴への新しいエントリの書き込みなど) を含めることもできます。 エンドポイントですべてのイベントを受信し、対象となるイベントを処理するコードを記述することも、イベント サブスクリプションを作成するときにフィルターを設定することもできます。

プログラムでイベントを処理するには、`operationName` 値を調べることでイベントを並べ替えることができます。 たとえば、イベント エンドポイントで、`Microsoft.Compute/virtualMachines/write` または `Microsoft.Storage/storageAccounts/write` と等しい操作のイベントのみを処理できます。

イベントの対象は、操作の対象となっているリソースのリソース ID です。 リソースのイベントをフィルター処理するには、イベント サブスクリプションを作成するときに、そのリソース ID を指定します。 サンプル スクリプトについては、[リソース グループのサブスクライブとフィルター処理 - PowerShell](scripts/event-grid-powershell-resource-group-filter.md) または[リソース グループのサブスクライブとフィルター処理 - Azure CLI](scripts/event-grid-cli-resource-group-filter.md) に関する記事を参照してください。 リソースの種類でフィルター処理するには、次の形式で値を使用します。`/subscriptions/<subscription-id>/resourcegroups/<resource-group>/providers/Microsoft.Compute/virtualMachines`

## <a name="available-event-types"></a>使用可能なイベントの種類

リソース グループは、VM が作成されたりストレージ アカウントが削除されたりしたときに、Azure Resource Manager から管理イベントを出力します。

| イベントの種類 | 説明 |
| ---------- | ----------- |
| Microsoft.Resources.ResourceWriteSuccess | リソースの作成または更新操作が成功したときに発生します。 |
| Microsoft.Resources.ResourceWriteFailure | リソースの作成または更新操作が失敗したときに発生します。 |
| Microsoft.Resources.ResourceWriteCancel | リソースの作成または更新操作が取り消されたときに発生します。 |
| Microsoft.Resources.ResourceDeleteSuccess | リソースの削除操作が成功したときに発生します。 |
| Microsoft.Resources.ResourceDeleteFailure | リソースの削除操作が失敗したときに発生します。 |
| Microsoft.Resources.ResourceDeleteCancel | リソースの削除操作が取り消されたときに発生します。 このイベントは、テンプレートのデプロイが取り消された場合に発生します。 |

## <a name="example-event"></a>イベントの例

次の例は、**ResourceWriteSuccess** イベント用のスキーマを示しています。 **ResourceWriteFailure** イベントと **ResourceWriteCancel** イベントでも、`eventType` の値を変更して、同じスキーマが使用されます。

```json
[{
  "subject": "/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}",
  "eventType": "Microsoft.Resources.ResourceWriteSuccess",
  "eventTime": "2018-07-19T18:38:04.6117357Z",
  "id": "4db48cba-50a2-455a-93b4-de41a3b5b7f6",
  "data": {
    "authorization": {
      "scope": "/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}",
      "action": "Microsoft.Storage/storageAccounts/write",
      "evidence": {
        "role": "Subscription Admin"
      }
    },
    "claims": {
      "aud": "{audience-claim}",
      "iss": "{issuer-claim}",
      "iat": "{issued-at-claim}",
      "nbf": "{not-before-claim}",
      "exp": "{expiration-claim}",
      "_claim_names": "{\"groups\":\"src1\"}",
      "_claim_sources": "{\"src1\":{\"endpoint\":\"{URI}\"}}",
      "http://schemas.microsoft.com/claims/authnclassreference": "1",
      "aio": "{token}",
      "http://schemas.microsoft.com/claims/authnmethodsreferences": "rsa,mfa",
      "appid": "{ID}",
      "appidacr": "2",
      "http://schemas.microsoft.com/2012/01/devicecontext/claims/identifier": "{ID}",
      "e_exp": "{expiration}",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "{last-name}",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "{first-name}",
      "ipaddr": "{IP-address}",
      "name": "{full-name}",
      "http://schemas.microsoft.com/identity/claims/objectidentifier": "{ID}",
      "onprem_sid": "{ID}",
      "puid": "{ID}",
      "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "{ID}",
      "http://schemas.microsoft.com/identity/claims/tenantid": "{ID}",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": "{user-name}",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "{user-name}",
      "uti": "{ID}",
      "ver": "1.0"
    },
    "correlationId": "{ID}",
    "resourceProvider": "Microsoft.Storage",
    "resourceUri": "/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}",
    "operationName": "Microsoft.Storage/storageAccounts/write",
    "status": "Succeeded",
    "subscriptionId": "{subscription-id}",
    "tenantId": "{tenant-id}"
  },
  "dataVersion": "2",
  "metadataVersion": "1",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}"
}]
```

次の例は、**ResourceDeleteSuccess** イベントのスキーマを示しています。 **ResourceDeleteFailure** イベントと **ResourceDeleteCancel** イベントでも、`eventType` の値を変更して、同じスキーマが使用されます。

```json
[{
  "subject": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}",
  "eventType": "Microsoft.Resources.ResourceDeleteSuccess",
  "eventTime": "2018-07-19T19:24:12.763881Z",
  "id": "19a69642-1aad-4a96-a5ab-8d05494513ce",
  "data": {
    "authorization": {
      "scope": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}",
      "action": "Microsoft.Storage/storageAccounts/delete",
      "evidence": {
        "role": "Subscription Admin"
      }
    },
    "claims": {
      "aud": "{audience-claim}",
      "iss": "{issuer-claim}",
      "iat": "{issued-at-claim}",
      "nbf": "{not-before-claim}",
      "exp": "{expiration-claim}",
      "_claim_names": "{\"groups\":\"src1\"}",
      "_claim_sources": "{\"src1\":{\"endpoint\":\"{URI}\"}}",
      "http://schemas.microsoft.com/claims/authnclassreference": "1",
      "aio": "{token}",
      "http://schemas.microsoft.com/claims/authnmethodsreferences": "rsa,mfa",
      "appid": "{ID}",
      "appidacr": "2",
      "http://schemas.microsoft.com/2012/01/devicecontext/claims/identifier": "{ID}",
      "e_exp": "262800",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "{last-name}",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "{first-name}",
      "ipaddr": "{IP-address}",
      "name": "{full-name}",
      "http://schemas.microsoft.com/identity/claims/objectidentifier": "{ID}",
      "onprem_sid": "{ID}",
      "puid": "{ID}",
      "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "{ID}",
      "http://schemas.microsoft.com/identity/claims/tenantid": "{ID}",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": "{user-name}",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "{user-name}",
      "uti": "{ID}",
      "ver": "1.0"
    },
    "correlationId": "{ID}",
    "httpRequest": {
      "clientRequestId": "{ID}",
      "clientIpAddress": "{IP-address}",
      "method": "DELETE",
      "url": "https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}?api-version=2018-02-01"
    },
    "resourceProvider": "Microsoft.Storage",
    "resourceUri": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}",
    "operationName": "Microsoft.Storage/storageAccounts/delete",
    "status": "Succeeded",
    "subscriptionId": "{subscription-id}",
    "tenantId": "{tenant-id}"
  },
  "dataVersion": "2",
  "metadataVersion": "1",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}"
}]
```

## <a name="event-properties"></a>イベントのプロパティ

イベントのトップレベルのデータを次に示します。

| プロパティ | 型 | 説明 |
| -------- | ---- | ----------- |
| topic | 文字列 | イベント ソースの完全なリソース パス。 このフィールドは書き込み可能ではありません。 この値は Event Grid によって指定されます。 |
| subject | 文字列 | 発行元が定義したイベントの対象のパス。 |
| eventType | 文字列 | このイベント ソース用に登録されたイベントの種類のいずれか。 |
| eventTime | 文字列 | プロバイダーの UTC 時刻に基づくイベントの生成時刻。 |
| id | 文字列 | イベントの一意識別子。 |
| data | オブジェクト | リソース グループ イベントのデータ。 |
| dataVersion | 文字列 | データ オブジェクトのスキーマ バージョン。 スキーマ バージョンは発行元によって定義されます。 |
| metadataVersion | 文字列 | イベント メタデータのスキーマ バージョン。 最上位プロパティのスキーマは Event Grid によって定義されます。 この値は Event Grid によって指定されます。 |

データ オブジェクトには、次のプロパティがあります。

| プロパティ | 型 | 説明 |
| -------- | ---- | ----------- |
| authorization | オブジェクト | 操作の要求された承認。 |
| claims | オブジェクト | 要求のプロパティ。 詳細については、[JWT 認証](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html)に関する記事を参照してください。 |
| correlationId | 文字列 | トラブルシューティング用の操作 ID。 |
| httpRequest | オブジェクト | 操作の詳細。 このオブジェクトは、既存のリソースを更新する場合、またはリソースを削除する場合にのみ含まれます。 |
| resourceProvider | 文字列 | 操作を実行しているリソース プロバイダー。 |
| resourceUri | 文字列 | 操作内のリソースの URI。 |
| operationName | 文字列 | 実行された操作。 |
| status | 文字列 | 操作の状態。 |
| subscriptionId | 文字列 | リソースのサブスクリプション ID。 |
| tenantId | 文字列 | リソースのテナント ID。 |

## <a name="next-steps"></a>次の手順

* Azure Event Grid の概要については、[Event Grid の紹介](overview.md)に関する記事を参照してください。
* Azure Event Grid サブスクリプションの作成の詳細については、[Event Grid サブスクリプション スキーマ](subscription-creation-schema.md)に関する記事を参照してください。
