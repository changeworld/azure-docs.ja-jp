---
title: Microsoft コマーシャル マーケットプレースの SaaS Fulfillment 操作 API v2
description: SaaS Fulfillment API バージョン 2 の一部である操作 API を使用して、Microsoft AppSource、Azure Marketplace、および Azure portal で SaaS オファーを管理する方法を学習します。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 11/10/2021
author: saasguide
ms.author: souchak
ms.openlocfilehash: ef0e735206df6556bd7a7a7b2cf584bc79566a0f
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/09/2021
ms.locfileid: "132063881"
---
# <a name="saas-fulfillment-operations-apis-v2-in-the-microsoft-commercial-marketplace"></a>Microsoft コマーシャル マーケットプレースの SaaS Fulfillment 操作 API v2

この記事では、SaaS Fulfillment 操作 API のバージョン 2 について説明します。

## <a name="list-outstanding-operations"></a>未処理の操作を一覧表示する

指定した SaaS サブスクリプションの保留中の操作の一覧を取得します。 公開元は、[Operation patch API](#update-the-status-of-an-operation) を呼び出すことによって、返された操作を確認する必要があります。

### <a name="get-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsapi-versionapiversion"></a>Get `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations?api-version=<ApiVersion>`

*クエリ パラメーター:*

|  パラメーター         | 値             |
|  ---------------   |  ---------------  |
|    `ApiVersion`    |  2018-08-31 を使用します。         |
|    `subscriptionId` | 購入済み SaaS サブスクリプションの一意識別子。  この ID は、Resolve API を使用してコマーシャル マーケットプレース認証トークンを解決した後に取得されます。  |

*要求ヘッダー:*
 
|  パラメーター         | 値             |
|  ---------------   |  ---------------  |
|  `content-type`     |  `application/json` |
|  `x-ms-requestid`    |  クライアントからの要求を追跡するための一意の文字列値 (GUID を推奨)。  この値を指定しないと、値が生成され、応答ヘッダーに指定されます。  |
|  `x-ms-correlationid` |  クライアントでの操作に対する一意の文字列値。  このパラメーターによって、クライアント操作からのすべてのイベントがサーバー側のイベントに関連付けられます。  この値を指定しないと、値が生成され、応答ヘッダーに指定されます。  |
|  `authorization`     |  「[Azure AD アプリに基づいたトークンの取得](./pc-saas-registration.md#get-the-token-with-an-http-post)」の説明に従って、発行者によってトークン値が取得される場合、形式は `"Bearer <access_token>"` です。  |

*応答コード:*

コード: 200 指定された SaaS サブスクリプションで保留中の操作を返します。

*応答ペイロードの例:*

```json
{
  "operations": [
    {
      "id": "<guid>", //Operation ID, should be provided in the operations patch API call
      "activityId": "<guid>", //not relevant
      "subscriptionId": "<guid>", // subscriptionId of the SaaS subscription that is being reinstated
      "offerId": "offer1", // purchased offer ID
      "publisherId": "contoso",
      "planId": "silver", // purchased plan ID
      "quantity": "20", // purchased amount of seats, will be empty is not relevant
      "action": "Reinstate",
      "timeStamp": "2018-12-01T00:00:00", // UTC
      "status": "InProgress" // the only status that can be returned in this case
    }
  ]
}
```

保留中の操作がない場合は、空の json を返します。

コード:400 - 無効な要求: 検証エラー。

コード:403 - 許可されていません。 認証トークンが無効であるか、期限切れであるか、指定されませんでした。 要求が、認証トークンの作成に使用されたものとは異なる Azure AD アプリ ID で公開されているオファーの SaaS サブスクリプションにアクセスしようとしています。

多くの場合、このエラーは、[SaaS 登録](pc-saas-registration.md)を正しく実行していないことを表す症状です。

コード:404 見つかりません。  `subscriptionId` を持つ SaaS サブスクリプションが見つかりません。

コード:500 - 内部サーバー エラーです。 API 呼び出しをやり直してください。  エラーが解決しない場合は、[Microsoft サポート](https://go.microsoft.com/fwlink/?linkid=2165533)にお問い合わせください。

## <a name="get-operation-status"></a>操作状態を取得する

この API により、公開元が、指定した非同期操作の状態を追跡できるようになります:**Unsubscribe**、**ChangePlan**、または **ChangeQuantity**。

この API 呼び出しの `operationId` は、**Operation-Location** によって返される値、保留中の Operations API の取得の呼び出し、または Webhook 呼び出しで受け取った `<id>` パラメーター値から取得できます。

### <a name="get-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsoperationidapi-versionapiversion"></a>Get `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=<ApiVersion>`

*クエリ パラメーター:*

|  パラメーター         | 値             |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  2018-08-31 を使用します。  |
|  `subscriptionId`    |  購入済み SaaS サブスクリプションの一意識別子。  この ID は、Resolve API を使用してコマーシャル マーケットプレース認証トークンを解決した後に取得されます。 |
|  `operationId`       |  取得する対象である操作を表す一意識別子。 |

*要求ヘッダー:*

|  パラメーター         | 値             |
|  ---------------   |  ---------------  |
|  `content-type`      |  `application/json`   |
|  `x-ms-requestid`    |  クライアントからの要求を追跡するための一意の文字列値 (GUID を推奨)。  この値を指定しないと、値が生成され、応答ヘッダーに指定されます。 |
|  `x-ms-correlationid` |  クライアントでの操作に対する一意の文字列値。  このパラメーターによって、クライアント操作からのすべてのイベントがサーバー側のイベントに関連付けられます。  この値を指定しないと、値が生成され、応答ヘッダーに指定されます。  |
|  `authorization`     |  この API 呼び出しを行っている発行者を識別する一意のアクセス トークン。  「[Azure AD アプリに基づいたトークンの取得](./pc-saas-registration.md#get-the-token-with-an-http-post)」の説明に従って、発行者によってトークン値が取得される場合、形式は `"Bearer <access_token>"` です。  |

*応答コード:*

コード:200 - 指定した SaaS 操作の詳細を取得します。 

*応答ペイロードの例:*

```json
Response body:
{
  "id  ": "<guid>", //Operation ID, should be provided in the patch operation API call
  "activityId": "<guid>", //not relevant
  "subscriptionId": "<guid>", // subscriptionId of the SaaS subscription for which this operation is relevant
  "offerId": "offer1", // purchased offer ID
  "publisherId": "contoso",
  "planId": "silver", // purchased plan ID
  "quantity": "20", // purchased amount of seats
  "action": "ChangePlan", // Can be ChangePlan, ChangeQuantity or Reinstate
  "timeStamp": "2018-12-01T00:00:00", // UTC
  "status": "InProgress", // Possible values: NotStarted, InProgress, Failed, Succeeded, Conflict (new quantity / plan is the same as existing)
  "errorStatusCode": "",
  "errorMessage": ""
}
```

コード:403 - 許可されていません。 認証トークンが無効であるか、期限切れであるか、指定されませんでした。 要求が、認証トークンの作成に使用されたものとは異なる Azure AD アプリ ID で公開されているオファーの SaaS サブスクリプションにアクセスしようとしています。

多くの場合、このエラーは、[SaaS 登録](pc-saas-registration.md)を正しく実行していないことを表す症状です。

コード:404 見つかりません。  

* `subscriptionId` を持つサブスクリプションが見つかりません。
* `operationId` を持つ操作が見つかりません。

コード:500 - 内部サーバー エラーです。  API 呼び出しをやり直してください。  エラーが解決しない場合は、[Microsoft サポート](https://go.microsoft.com/fwlink/?linkid=2165533)にお問い合わせください。

## <a name="update-the-status-of-an-operation"></a>操作の状態を更新する

この API を使用して、保留中の操作の状態を更新し、公開元側で操作が成功したか失敗したかを示します。

この API 呼び出しの `operationId` は、**Operation-Location** によって返される値、保留中の Operations API の取得の呼び出し、または Webhook 呼び出しで受け取った `<id>` パラメーター値から取得できます。

### <a name="patch-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsoperationidapi-versionapiversion"></a>Patch `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=<ApiVersion>`

*クエリ パラメーター:*

|  パラメーター         | 値             |
|  ---------------   |  ---------------  |
|   `ApiVersion`       |  2018-08-31 を使用します。  |
|   `subscriptionId`   |  購入済み SaaS サブスクリプションの一意識別子。  この ID は、Resolve API を使用してコマーシャル マーケットプレース認証トークンを解決した後に取得されます。  |
|   `operationId`      |  完了する対象である操作の一意識別子。 |

*要求ヘッダー:*

|  パラメーター         | 値             |
|  ---------------   |  ---------------  |
|   `content-type`   | `application/json`   |
|   `x-ms-requestid`   |  クライアントからの要求を追跡するための一意の文字列値 (GUID を推奨)。  この値を指定しないと、値が生成され、応答ヘッダーに指定されます。 |
|   `x-ms-correlationid` |  クライアントでの操作に対する一意の文字列値。  このパラメーターによって、クライアント操作からのすべてのイベントがサーバー側のイベントに関連付けられます。  この値を指定しないと、値が生成され、応答ヘッダーに指定されます。 |
|  `authorization`     |  この API 呼び出しを行っている公開元を識別する一意のアクセス トークン。  「[Azure AD アプリに基づいたトークンの取得](./pc-saas-registration.md#get-the-token-with-an-http-post)」の説明に従って、発行者によってトークン値が取得される場合、形式は `"Bearer <access_token>"` です。 |

*要求ペイロードの例:*

```json
{
  "status": "Success" // Allowed Values: Success/Failure. Indicates the status of the operation on ISV side.
}
```

*応答コード:*

コード:200 - パートナー側での操作の完了を通知する呼び出し。  たとえば、この応答によって発行者側でのシートまたはプランの変更を通知できます。

コード:403
- Forbidden.  認証トークンが使用できないか、無効であるか、有効期限が切れています。 要求で、現在の発行者に属していないサブスクリプションにアクセスしようとしている可能性があります。
- Forbidden.  認証トークンが無効であるか、期限切れであるか、指定されませんでした。 要求が、認証トークンの作成に使用されたものとは異なる Azure AD アプリ ID で公開されているオファーの SaaS サブスクリプションにアクセスしようとしています。

多くの場合、このエラーは、[SaaS 登録](pc-saas-registration.md)を正しく実行していないことを表す症状です。

コード:404 見つかりません。

* `subscriptionId` を持つサブスクリプションが見つかりません。
* `operationId` を持つ操作が見つかりません。

コード:409 - 競合。  たとえば、より新しい更新が既に履行されています。

コード:500 - 内部サーバー エラーです。  API 呼び出しをやり直してください。  エラーが解決しない場合は、[Microsoft サポート](https://go.microsoft.com/fwlink/?linkid=2165533)にお問い合わせください。

## <a name="next-steps"></a>次のステップ

コマーシャル マーケットプレースでの SaaS オファーのその他のオプションについては、[コマーシャル マーケットプレースの測定サービス API](../marketplace-metering-service-apis.md) を参照してください。

[さまざまなプログラミング言語のクライアントを確認して、サンプルを使用します](https://github.com/microsoft/commercial-marketplace-samples)。
