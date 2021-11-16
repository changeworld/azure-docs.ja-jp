---
title: Microsoft コマーシャル マーケットプレースの SaaS Fulfillment サブスクリプション API v2
description: SaaS Fulfillment サブスクリプション API バージョン 2 の一部である Operations API を使用して、Microsoft AppSource、Azure Marketplace、および Azure portal で SaaS オファーを管理する方法を学習します。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 10/27/2021
author: saasguide
ms.author: souchak
ms.openlocfilehash: ace7aac5a308621ca8032677e7ca7eec3858bdb7
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/09/2021
ms.locfileid: "132063918"
---
# <a name="saas-fulfillment-subscription-apis-v2-in-microsoft-commercial-marketplace"></a>Microsoft コマーシャル マーケットプレースの SaaS Fulfillment サブスクリプション API v2

この記事では、SaaS Fulfillment サブスクリプション API のバージョン 2 について説明します。

## <a name="resolve-a-purchased-subscription"></a>購入済みサブスクリプションを解決する

resolve エンドポイントを使用すると、公開元は、コマーシャル マーケットプレースからの購入識別トークン (「[購入済みであるが、まだアクティブ化されていない](pc-saas-fulfillment-life-cycle.md#purchased-but-not-yet-activated-pendingfulfillmentstart)」で "*トークン*" として言及) を、永続的な購入済み SaaS サブスクリプション ID とその詳細に交換できます。

顧客がパートナーのランディング ページの URL にリダイレクトされると、この URL 呼び出しで、顧客識別トークンが *token* パラメーターとして渡されます。 パートナーは、このトークンを使用すること、およびそれを解決するための要求を行うことを期待されています。 Resolve API 応答には、購入を一意に識別するための SaaS サブスクリプション ID とその他の詳細が含まれています。 ランディング ページの URL 呼び出しで提供される "*トークン*" は、通常 24 時間有効です。 受け取った "*トークン*" が既に期限切れになっている場合は、エンド ユーザーに次のガイダンスを提供することをお勧めします。

"この購入を特定できませんでした。 Azure portal または Microsoft 365 管理センターでこの SaaS サブスクリプションを再度開いて、[アカウントの構成] または [アカウントの管理] をもう一度選択してください。"

Resolve API を呼び出すと、サポートされているすべての状態の SaaS サブスクリプションの詳細と状態が返されます。

### <a name="post-httpsmarketplaceapimicrosoftcomapisaassubscriptionsresolveapi-versionapiversion"></a>Post`https://marketplaceapi.microsoft.com/api/saas/subscriptions/resolve?api-version=<ApiVersion>`

*クエリ パラメーター:*

|  パラメーター         | 値            |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  2018-08-31 を使用します。   |

*要求ヘッダー:*

|  パラメーター         | 値             |
|  ---------------   |  ---------------  |
|  `content-type`      | `application/json` |
|  `x-ms-requestid`    |  クライアントからの要求を追跡するための一意の文字列値 (GUID を推奨)。 この値を指定しないと、値が生成され、応答ヘッダーに指定されます。 |
|  `x-ms-correlationid` |  クライアントでの操作に対する一意の文字列値。 このパラメーターによって、クライアント操作からのすべてのイベントがサーバー側のイベントに関連付けられます。 この値を指定しないと、値が生成され、応答ヘッダーに指定されます。  |
|  `authorization`     |  この API 呼び出しを行っている発行者を識別する一意のアクセス トークン。 「[Azure AD アプリに基づいたトークンの取得](./pc-saas-registration.md#get-the-token-with-an-http-post)」の説明に従って、発行者によってトークン値が取得される場合、形式は `"Bearer <accessaccess_token>"` です。 |
|  `x-ms-marketplace-token`  | 解決する購入 ID *token* パラメーター。  このトークンは、顧客が SaaS パートナーの Web サイトにリダイレクトされるときに、ランディング ページの URL 呼び出しで渡されます (例: `https://contoso.com/signup?token=<token><authorization_token>`)。 <br> <br>  エンコードされている *token* 値は、ランディング ページの URL の一部であるため、この API 呼び出しでパラメーターとして使用する前にデコードする必要があることに注意してください。  <br> <br> URL 内のエンコードされた文字列の例は次のとおりです: `contoso.com/signup?token=ab%2Bcd%2Fef`。ここで、*token* は `ab%2Bcd%2Fef` です。  同じトークンをデコードすると、このようになります: `Ab+cd/ef` |
| | |

*応答コード:*

コード:200 - 指定された `x-ms-marketplace-token` に基づいて一意の SaaS サブスクリプション識別子を返します。

応答本文の例:

```json
{
  "id": "<guid>", // purchased SaaS subscription ID
  "subscriptionName": "Contoso Cloud Solution", // SaaS subscription name
  "offerId": "offer1", // purchased offer ID
  "planId": "silver", // purchased offer's plan ID
  "quantity": "20", // number of purchased seats, might be empty if the plan is not per seat
  "subscription": { // full SaaS subscription details, see Get Subscription APIs response body for full description
    "id": "<guid>",
    "publisherId": "contoso",
    "offerId": "offer1",
    "name": "Contoso Cloud Solution",
    "saasSubscriptionStatus": " PendingFulfillmentStart ",
    "beneficiary": {
      "emailId": "test@test.com",
      "objectId": "<guid>",
      "tenantId": "<guid>",
      "pid": "<ID of the user>"
    },
    "purchaser": {
      "emailId": "test@test.com",
      "objectId": "<guid>",
      "tenantId": "<guid>",
      "pid": "<ID of the user>"
    },
    "planId": "silver",
    "term": {
      "termUnit": "P1M",
      "startDate": "2019-05-31",
      "endDate": "2019-06-29"
    },
    "isTest": true,
    "isFreeTrial": false,
    "allowedCustomerOperations": ["Delete", "Update", "Read"],
    "sandboxType": "None",
    "sessionMode": "None"
  }
}

```

コード:400 - 無効な要求です。 `x-ms-marketplace-token` が見つからないか、形式が正しくありません。または、有効期限が切れています。

コード:403 - 許可されていません。 認証トークンが無効であるか、期限切れであるか、指定されませんでした。  要求が、認証トークンの作成に使用されたものとは異なる Azure AD アプリ ID で公開されたオファーの SaaS サブスクリプションにアクセスしようとしています。

多くの場合、このエラーは、[SaaS 登録](pc-saas-registration.md)を正しく実行していないことを表す症状です。

コード:500 - 内部サーバー エラーです。  API 呼び出しをやり直してください。  エラーが解決しない場合は、[Microsoft サポート](https://go.microsoft.com/fwlink/?linkid=2165533)にお問い合わせください。

## <a name="activate-a-subscription"></a>サブスクリプションをアクティブ化する

SaaS アカウントがエンド ユーザー向けに構成されたら、公開元は Microsoft 側で Activate Subscription API を呼び出す必要があります。  この API 呼び出しが成功しない限り、顧客に対する請求は行われません。

### <a name="post-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidactivateapi-versionapiversion"></a>Post`https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/activate?api-version=<ApiVersion>`

*クエリ パラメーター:*

|  パラメーター         | 値             |
|  --------   |  ---------------  |
| `ApiVersion`  |  2018-08-31 を使用します。   |
| `subscriptionId` | 購入済み SaaS サブスクリプションの一意識別子。  この ID は、[Resolve API](#resolve-a-purchased-subscription) を使用してコマーシャル マーケットプレース認証トークンを解決した後に取得されます。
 |

*要求ヘッダー:*

|  パラメーター         | 値             |
|  ---------------   |  ---------------  |
| `content-type`       |  `application/json`  |
| `x-ms-requestid`     |  クライアントからの要求を追跡するための一意の文字列値 (GUID を推奨)。  この値を指定しないと、値が生成され、応答ヘッダーに指定されます。 |
| `x-ms-correlationid` |  クライアントでの操作に対する一意の文字列値。  この文字列によって、クライアント操作からのすべてのイベントがサーバー側のイベントに関連付けられます。  この値を指定しないと、値が生成され、応答ヘッダーに指定されます。 |
| `authorization`      |  この API 呼び出しを行っている発行者を識別する一意のアクセス トークン。 「[Azure AD アプリに基づいたトークンの取得](./pc-saas-registration.md#get-the-token-with-an-http-post)」の説明に従って、発行者によってトークン値が取得される場合、形式は `"Bearer <access_token>"` です。 |

*要求ペイロードの例:*

```json
{  // needed for validation of the activation request
  "planId": "gold", // purchased plan, cannot be empty
  "quantity": "" // purchased number of seats, can be empty if plan is not per seat
}
```

*応答コード:*

コード:200 - サブスクリプションは、Microsoft 側でサブスクライブ済みとしてマークされました。

この呼び出しには応答本文がありません。

コード:400 - 無効な要求: 検証に失敗しました。

* `planId` が要求ペイロードに存在しません。
* 要求ペイロード内の `planId` が、購入されたものと一致しません。
* 要求ペイロード内の `quantity` が、購入されたものと一致しません。
* SaaS サブスクリプションは、*Subscribed* または *Suspended* の状態です。

コード:403 - 許可されていません。 認証トークンが無効であるか、期限切れであるか、指定されませんでした。 要求が、認証トークンの作成に使用されたものとは異なる Azure AD アプリ ID で公開されたオファーの SaaS サブスクリプションにアクセスしようとしています。

多くの場合、このエラーは、[SaaS 登録](pc-saas-registration.md)を正しく実行していないことを表す症状です。

コード:404 見つかりません。 SaaS サブスクリプションは *Unsubscribed* 状態です。

コード:500 - 内部サーバー エラーです。  API 呼び出しをやり直してください。  エラーが解決しない場合は、[Microsoft サポート](https://go.microsoft.com/fwlink/?linkid=2165533)にお問い合わせください。

## <a name="get-list-of-all-subscriptions"></a>すべてのサブスクリプションの一覧を取得する

この API は、公開元がコマーシャル マーケットプレースに公開しているすべてのオファーのすべての購入済み SaaS サブスクリプションの一覧を取得します。  可能なすべての状態の SaaS サブスクリプションが返されます。 登録解除された SaaS サブスクリプションも返されます (この情報は Microsoft 側では削除されないため)。

この API では、1 ページあたり 100 件のページ分割された結果が返されます。

### <a name="get-httpsmarketplaceapimicrosoftcomapisaassubscriptionsapi-versionapiversion"></a>Get `https://marketplaceapi.microsoft.com/api/saas/subscriptions?api-version=<ApiVersion>`

*クエリ パラメーター:*

|  パラメーター         | 値             |
|  --------   |  ---------------  |
| `ApiVersion`  |  2018-08-31 を使用します。  |
| `continuationToken`  | 省略可能なパラメーター。 結果の最初のページを取得するには、空のままにします。  次のページを取得するには、`@nextLink` パラメーターで返された値を使用します。 |

*要求ヘッダー:*

|  パラメーター         | 値             |
|  ---------------   |  ---------------  |
| `content-type`       |  `application/json`  |
| `x-ms-requestid`     |  クライアントからの要求を追跡するための一意の文字列値 (GUID を推奨)。 この値を指定しないと、値が生成され、応答ヘッダーに指定されます。 |
| `x-ms-correlationid` |  クライアントでの操作に対する一意の文字列値。  このパラメーターによって、クライアント操作からのすべてのイベントがサーバー側のイベントに関連付けられます。  この値を指定しないと、値が生成され、応答ヘッダーに指定されます。 |
| `authorization`      |  この API 呼び出しを行っている公開元を識別する一意のアクセス トークン。  「[Azure AD アプリに基づいたトークンの取得](./pc-saas-registration.md#get-the-token-with-an-http-post)」の説明に従って、発行者によってトークン値が取得される場合、形式は `"Bearer <access_token>"` です。 |

*応答コード:*

コード:200 - この公開元が行ったオファーについて、公開元の認証トークンに基づいて既存のすべてのサブスクリプションの一覧を返します。

*応答本文の例:*

```json
{
  "subscriptions": [
    {
      "id": "<guid>", // purchased SaaS subscription ID
      "name": "Contoso Cloud Solution", // SaaS subscription name
      "publisherId": "contoso", // publisher ID
      "offerId": "offer1", // purchased offer ID
      "planId": "silver", // purchased plan ID
      "quantity": "10", // purchased amount of seats, will be empty if plan is not per seat
      "beneficiary": { // email address, user ID and tenant ID for which SaaS subscription was purchased.
        "emailId": " test@contoso.com",
        "objectId": "<guid>",
        "tenantId": "<guid>",
        "pid": "<ID of the user>"
      },
      "purchaser": { // email address, user ID and tenant ID that purchased the SaaS subscription. These could be different from beneficiary information for reseller (CSP) purchase
        "emailId": " test@contoso.com",
        "objectId": "<guid>",
        "tenantId": "<guid>",
        "pid": "<ID of the user>"
      },
      "term": { // The period for which the subscription was purchased.
        "startDate": "2019-05-31", //format: YYYY-MM-DD. This is the date when the subscription was activated by the ISV and the billing started. This field is relevant only for Active and Suspended subscriptions.
        "endDate": "2019-06-30", // This is the last day the subscription is valid. Unless stated otherwise, the automatic renew will happen the next day. This field is relevant only for Active and Suspended subscriptions.
        "termUnit": "P1M" // where P1M is monthly and P1Y is yearly. Also reflected in the startDate and endDate values
      },
      "allowedCustomerOperations": ["Read", "Update", "Delete"], // Indicates operations allowed on the SaaS subscription for beneficiary. For CSP-initiated purchases, this will always be "Read" because the customer cannot update or delete subscription in this flow.  Purchaser can perform all operations on the subscription.
      "sessionMode": "None", // not relevant
      "isFreeTrial": true, // true - the customer subscription is currently in free trial, false - the customer subscription is not currently in free trial. (Optional field -– if not returned, the value is false.)
      "isTest": false, // not relevant
      "sandboxType": "None", // not relevant
      "saasSubscriptionStatus": "Subscribed" // Indicates the status of the operation. Can be one of the following: PendingFulfillmentStart, Subscribed, Suspended or Unsubscribed.
    },
    // next SaaS subscription details, might be a different offer
    {
      "id": "<guid1>",
      "name": "Contoso Cloud Solution1",
      "publisherId": "contoso",
      "offerId": "offer2",
      "planId": "gold",
      "quantity": "",
      "beneficiary": {
        "emailId": " test@contoso.com",
        "objectId": "<guid>",
        "tenantId": "<guid>",
        "pid": "<ID of the user>"
      },
      "purchaser": {
        "emailId": "purchase@csp.com ",
        "objectId": "<guid>",
        "tenantId": "<guid>",
        "pid": "<ID of the user>"
      },
      "term": {
        "startDate": "2019-05-31",
        "endDate": "2020-04-30",
        "termUnit": "P1Y"
      },
      "allowedCustomerOperations": ["Read"],
      "sessionMode": "None",
      "isFreeTrial": false,
      "isTest": false,
      "sandboxType": "None",
      "saasSubscriptionStatus": "Suspended"
    }
  ],
  "@nextLink": "https:// https://marketplaceapi.microsoft.com/api/saas/subscriptions/?continuationToken=%5b%7b%22token%22%3a%22%2bRID%3a%7eYeUDAIahsn22AAAAAAAAAA%3d%3d%23RT%3a1%23TRC%3a2%23ISV%3a1%23FPC%3aAgEAAAAQALEAwP8zQP9%2fFwD%2b%2f2FC%2fwc%3d%22%2c%22range%22%3a%7b%22min%22%3a%22%22%2c%22max%22%3a%2205C1C9CD673398%22%7d%7d%5d&api-version=2018-08-31" // url that contains continuation token to retrieve next page of the SaaS subscriptions list, if empty or absent, this is the last page. ISV can use this url as is to retrieve the next page or extract the value of continuation token from this url.
}
```

この発行者について購入済み SaaS サブスクリプションが見つからない場合は、空の応答本文が返されます。

コード:403 - 許可されていません。 認証トークンが使用できないか、無効であるか、有効期限が切れています。

多くの場合、このエラーは、[SaaS 登録](pc-saas-registration.md)を正しく実行していないことを表す症状です。 

コード:500 - 内部サーバー エラーです。 API 呼び出しをやり直してください。  エラーが解決しない場合は、[Microsoft サポート](https://go.microsoft.com/fwlink/?linkid=2165533)にお問い合わせください。

## <a name="get-subscription"></a>サブスクリプションを取得する

この API は、公開元がコマーシャル マーケットプレースに公開している SaaS オファーの、指定された購入済み SaaS サブスクリプションを取得します。 この呼び出しを使用して (すべてのサブスクリプションの一覧を取得するために API を呼び出すのではなく)、特定の SaaS サブスクリプションに関して入手できるすべての情報をその ID で取得します。

### <a name="get-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Get `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*クエリ パラメーター:*

|  パラメーター         | 値             |
|  ---------------   |  ---------------  |
| `ApiVersion`        |   2018-08-31 を使用します。 |
| `subscriptionId`     |  購入済み SaaS サブスクリプションの一意識別子。  この ID は、Resolve API を使用してコマーシャル マーケットプレース認証トークンを解決した後に取得されます。 |

*要求ヘッダー:*

|  パラメーター         | 値             |
|  ---------------   |  ---------------  |
|  `content-type`      |  `application/json`  |
|  `x-ms-requestid`    |  クライアントからの要求を追跡するための一意の文字列値 (GUID を推奨)。 この値を指定しないと、値が生成され、応答ヘッダーに指定されます。 |
|  `x-ms-correlationid` |  クライアントでの操作に対する一意の文字列値。  このパラメーターによって、クライアント操作からのすべてのイベントがサーバー側のイベントに関連付けられます。  この値を指定しないと、値が生成され、応答ヘッダーに指定されます。 |
|  `authorization`     | この API 呼び出しを行っている公開元を識別する一意のアクセス トークン。 「[Azure AD アプリに基づいたトークンの取得](./pc-saas-registration.md#get-the-token-with-an-http-post)」の説明に従って、発行者によってトークン値が取得される場合、形式は `"Bearer <access_token>"` です。  |

*応答コード:*

コード:200 - 提供されている `subscriptionId` に基づいて、SaaS サブスクリプションの詳細を返します。

*応答本文の例:*

```json
{
  "id": "<guid>", // purchased SaaS subscription ID
  "name": "Contoso Cloud Solution", // SaaS subscription name
  "publisherId": "contoso", // publisher ID
  "offerId": "offer1", // purchased offer ID
  "planId": "silver", // purchased plan ID
  "quantity": "10", // purchased amount of seats, will be empty if plan is not per seat
  "beneficiary": { // email address, user ID and tenant ID for which SaaS subscription is purchased.
    "emailId": "test@contoso.com",
    "objectId": "<guid>",
    "tenantId": "<guid>",
    "pid": "<ID of the user>"
  },
  "purchaser": { // email address ,user ID and tenant ID that purchased the SaaS subscription.  These could be different from beneficiary information for reseller (CSP) scenario
    "emailId": "test@test.com",
    "objectId": "<guid>",
    "tenantId": "<guid>",
    "pid": "<ID of the user>"
  },
  "allowedCustomerOperations": ["Read", "Update", "Delete"], // Indicates operations allowed on the SaaS subscription for beneficiary.  For CSP-initiated purchases, this will always be "Read" because the customer cannot update or delete subscription in this flow.  Purchaser can perform all operations on the subscription.
  "sessionMode": "None", // not relevant
  "isFreeTrial": false, // true - the customer subscription is currently in free trial, false - the customer subscription is not currently in free trial. Optional field – if not returned the value is false.
  "isTest": false, // not relevant
  "sandboxType": "None", // not relevant
  "saasSubscriptionStatus": " Subscribed ", // Indicates the status of the operation: PendingFulfillmentStart, Subscribed, Suspended or Unsubscribed.
  "term": { // the period for which the subscription was purchased
    "startDate": "2019-05-31", //format: YYYY-MM-DD. This is the date when the subscription was activated by the ISV and the billing started. This field is relevant only for Active and Suspended subscriptions.
    "endDate": "2019-06-29", // This is the last day the subscription is valid. Unless stated otherwise, the automatic renew will happen the next day. This field is relevant only for Active and Suspended subscriptions.
    "termUnit": "P1M" //where P1M is monthly and P1Y is yearly. Also reflected in the startDate and endDate values.
  }
}
```

コード:403 - 許可されていません。 認証トークンが無効であるか、期限切れであるか、指定されませんでした。 要求が、認証トークンの作成に使用されたものとは異なる Azure AD アプリ ID で公開されているオファーの SaaS サブスクリプションにアクセスしようとしています。

多くの場合、このエラーは、[SaaS 登録](pc-saas-registration.md)を正しく実行していないことを表す症状です。 

コード:404 見つかりません。  指定された `subscriptionId` の SaaS サブスクリプションが見つかりません。

コード:500 - 内部サーバー エラーです。  API 呼び出しをやり直してください。  エラーが解決しない場合は、[Microsoft サポート](https://go.microsoft.com/fwlink/?linkid=2165533)にお問い合わせください。

## <a name="list-available-plans"></a>利用可能なプランを一覧表示する

この API は、このオファーの特定の購入の `subscriptionId` によって識別される SaaS オファーのすべてのプランを取得します。  この呼び出しを使用して、SaaS サブスクリプションの利用者がサブスクリプションで更新できるすべてのプライベートおよびパブリック プランの一覧を取得します。  返されたプランは、既に購入済みのプランと同じ地域で使用可能になります。

この呼び出しからは、既に購入済みのものに加えて、その顧客が使用可能なプランの一覧が返されます。  その一覧は、公開元のサイト上でエンド ユーザーに表示できます。  エンド ユーザーはサブスクリプション プランを、返された一覧にあるいずれかのプランに変更できます。  一覧にないプランに変更すると、失敗します。

### <a name="get-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidlistavailableplansapi-versionapiversion"></a>Get `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/listAvailablePlans?api-version=<ApiVersion>`

*クエリ パラメーター:*

|  パラメーター         | 値             |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  2018-08-31 を使用します。  |
|  `subscriptionId`    |  購入済み SaaS サブスクリプションの一意識別子。  この ID は、Resolve API を使用してコマーシャル マーケットプレース認証トークンを解決した後に取得されます。 |

*要求ヘッダー:*

|  パラメーター         | 値             |
|  ---------------   |  ---------------  |
|   `content-type`     |  `application/json` |
|   `x-ms-requestid`   |  クライアントからの要求を追跡するための一意の文字列値 (GUID を推奨)。  この値を指定しないと、値が生成され、応答ヘッダーに指定されます。 |
|  `x-ms-correlationid`  |  クライアントでの操作に対する一意の文字列値。  このパラメーターによって、クライアント操作からのすべてのイベントがサーバー側のイベントに関連付けられます。  この値を指定しないと、値が生成され、応答ヘッダーに指定されます。 |
|  `authorization`     |  この API 呼び出しを行っている公開元を識別する一意のアクセス トークン。  「[Azure AD アプリに基づいたトークンの取得](./pc-saas-registration.md#get-the-token-with-an-http-post)」の説明に従って、発行者によってトークン値が取得される場合、形式は `"Bearer <access_token>"` です。  |

*応答コード:*

コード:200 - 既に購入済みのものを含め、既存の SaaS サブスクリプションで使用可能なすべてのプランの一覧を返します。

応答本文の例:

```json
{
  "plans": [
    {
      "planId": "Platinum001",
      "displayName": "Private platinum plan for Contoso", // display name of the plan as it appears in the marketplace
      "isPrivate": true //true or false
    },
    {
      "planId": "gold",
      "displayName": "Gold plan for Contoso",
      "isPrivate": false //true or false
    }
  ]
}
```

`subscriptionId` が見つからない場合は、空の応答本文が返されます。

コード:403 - 許可されていません。 認証トークンが無効であるか、期限切れであるか、指定されませんでした。  要求が、認証トークンの作成に使用されたものとは異なる Azure AD アプリ ID で公開されているオファーの SaaS サブスクリプションにアクセスしようとしている可能性があります。

多くの場合、このエラーは、[SaaS 登録](pc-saas-registration.md)を正しく実行していないことを表す症状です。 

コード:500 - 内部サーバー エラーです。  API 呼び出しをやり直してください。  エラーが解決しない場合は、[Microsoft サポート](https://go.microsoft.com/fwlink/?linkid=2165533)にお問い合わせください。

## <a name="change-the-plan-on-the-subscription"></a>サブスクリプションのプランを変更する

この API を使用して、SaaS サブスクリプションで購入済みの既存のプランを新しいプラン (パブリックまたはプライベート) に更新します。  公開元は、コマーシャル マーケットプレースで購入された SaaS サブスクリプションについて公開元側でプランが変更されるときに、この API を呼び出す必要があります。

この API は、"*アクティブ*" なサブスクリプションに対してのみ呼び出すことができます。  どのプランも他の既存のプラン (パブリックまたはプライベート) に変更できますが、それ自体には変更できません。  プライベート プランの場合、顧客のテナントは、パートナー センターでプランの対象ユーザーの一部として定義されている必要があります。

### <a name="patch-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Patch `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*クエリ パラメーター:*

|  パラメーター         | 値             |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  2018-08-31 を使用します。  |
| `subscriptionId`     | 購入済み SaaS サブスクリプションの一意識別子。  この ID は、Resolve API を使用してコマーシャル マーケットプレース認証トークンを解決した後に取得されます。 |

*要求ヘッダー:*
 
|  パラメーター         | 値             |
|  ---------------   |  ---------------  |
|  `content-type`      | `application/json`  |
|  `x-ms-requestid`    | クライアントからの要求を追跡するための一意の文字列値 (GUID を推奨)。 この値を指定しないと、値が生成され、応答ヘッダーに指定されます。  |
|  `x-ms-correlationid`  | クライアントでの操作に対する一意の文字列値。  このパラメーターによって、クライアント操作からのすべてのイベントがサーバー側のイベントに関連付けられます。  この値を指定しないと、値が生成され、応答ヘッダーに指定されます。  |
|  `authorization`     |  この API 呼び出しを行っている公開元を識別する一意のアクセス トークン。  「[Azure AD アプリに基づいたトークンの取得](./pc-saas-registration.md#get-the-token-with-an-http-post)」の説明に従って、発行者によってトークン値が取得される場合、形式は `"Bearer <access_token>"` です。 |

*要求ペイロードの例:*

```json
{
  "planId": "gold" // the ID of the new plan to be purchased
}
```

*応答コード:*

コード:202 - プランを変更する要求が受理され、非同期に処理されました。  パートナーは、プラン変更要求の成功または失敗を判断するために **Operation-Location の URL** をポーリングする必要があります。  操作の最終状態として "*失敗*"、"*成功*"、または "*競合*" を受け取るまで数秒ごとにポーリングを行います。  最終的な操作の状態は迅速に返されますが、場合によっては数分かかることがあります。

また、コマーシャル マーケットプレース側でアクションを正常に完了させる準備が整うと、パートナーは Webhook 通知を受け取ります。  その段階でようやく、公開元側でプランの変更を行います。

*応答ヘッダー:*

|  パラメーター         | 値             |
|  ---------------   |  ---------------  |
|  `Operation-Location`        |  操作の状態を取得するための URL。  たとえば、「 `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=2018-08-31` 」のように入力します。 |

コード:400 - 無効な要求: 検証エラー。

* 新しいプランが存在しないか、この特定の SaaS サブスクリプションでは使用できません。
* 新しいプランは、現在のプランと同じです。
* SaaS サブスクリプションの状態が *Subscribed* ではありません。
* SaaS サブスクリプションの更新操作が `allowedCustomerOperations` に含まれていません。

コード:403 - 許可されていません。 認証トークンが無効であるか、期限切れであるか、指定されませんでした。  要求が、認証トークンの作成に使用されたものとは異なる Azure AD アプリ ID で公開されているオファーの SaaS サブスクリプションにアクセスしようとしています。

多くの場合、このエラーは、[SaaS 登録](pc-saas-registration.md)を正しく実行していないことを表す症状です。

コード:404 見つかりません。  `subscriptionId` を持つ SaaS サブスクリプションが見つかりません。

コード:500 - 内部サーバー エラーです。  API 呼び出しをやり直してください。  エラーが解決しない場合は、[Microsoft サポート](https://go.microsoft.com/fwlink/?linkid=2165533)にお問い合わせください。

>[!NOTE]
>一度に変更できるのは、プランまたはシート数のいずれかであり、両方ではありません。

>[!Note]
>この API は、エンド ユーザーから変更の明示的な承認を得た後にのみ呼び出すことができます。

## <a name="change-the-quantity-of-seats-on-the-saas-subscription"></a>SaaS サブスクリプションのシート数を変更する

この API を使用して、SaaS サブスクリプションのために購入されたシートの数を更新 (増減) します。  公開元は、コマーシャル マーケットプレースで作成された SaaS サブスクリプションのシート数が公開元側から変更されるときに、この API を呼び出す必要があります。

シートの数は、現在のプランで許可されている数よりも多くすることはできません。  この場合、公開元は、シート数を変更する前にプランを変更する必要があります。

### <a name="patch-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Patch `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*クエリ パラメーター:*

|  パラメーター         | 値             |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  2018-08-31 を使用します。  |
|  `subscriptionId`     | 購入済み SaaS サブスクリプションの一意識別子。  この ID は、Resolve API を使用してコマーシャル マーケットプレース認証トークンを解決した後に取得されます。  |

*要求ヘッダー:*
 
|  パラメーター         | 値             |
|  ---------------   |  ---------------  |
|  `content-type`      | `application/json`  |
|  `x-ms-requestid`    | クライアントからの要求を追跡するための一意の文字列値 (GUID を推奨)。  この値を指定しないと、値が生成され、応答ヘッダーに指定されます。  |
|  `x-ms-correlationid`  | クライアントでの操作に対する一意の文字列値。  このパラメーターによって、クライアント操作からのすべてのイベントがサーバー側のイベントに関連付けられます。  この値を指定しないと、値が生成され、応答ヘッダーに指定されます。  |
|  `authorization`     | この API 呼び出しを行っている公開元を識別する一意のアクセス トークン。  「[Azure AD アプリに基づいたトークンの取得](./pc-saas-registration.md#get-the-token-with-an-http-post)」の説明に従って、発行者によってトークン値が取得される場合、形式は `"Bearer <access_token>"` です。  |

*要求ペイロードの例:*

```json
{
  "quantity": 5 // the new amount of seats to be purchased
}
```

*応答コード:*

コード:202 - 数量を変更する要求は受理され、非同期に処理されました。 パートナーは、数量変更要求の成功または失敗を判断するために **Operation-Location の URL** をポーリングする必要があります。  操作の最終状態として "*失敗*"、"*成功*"、または "*競合*" を受け取るまで数秒ごとにポーリングを行います。  最終的な操作の状態は迅速に返されますが、場合によっては数分かかることがあります。

また、コマーシャル マーケットプレース側でアクションを正常に完了させる準備が整うと、パートナーは Webhook 通知を受け取ります。  その段階でようやく、公開元側で数量の変更を行います。

*応答ヘッダー:*

|  パラメーター         | 値             |
|  ---------------   |  ---------------  |
|  `Operation-Location`        |  操作状態を取得するリソースへのリンク。  たとえば、「 `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=2018-08-31` 」のように入力します。  |

コード:400 - 無効な要求: 検証エラー。

* 新しい数量が現在のプランの制限を上回っているか下回っています。
* 新しい数量がありません。
* 新しい数量は、現在の数量と同じです。
* SaaS サブスクリプションの状態がサブスクライブ済みではありません。
* SaaS サブスクリプションの更新操作が `allowedCustomerOperations` に含まれていません。

コード:403 - 許可されていません。  認証トークンが無効であるか、期限切れであるか、指定されませんでした。  要求で、現在の発行者に属していないサブスクリプションにアクセスしようとしています。

多くの場合、このエラーは、[SaaS 登録](pc-saas-registration.md)を正しく実行していないことを表す症状です。 

コード:404 見つかりません。  `subscriptionId` を持つ SaaS サブスクリプションが見つかりません。

コード:500 - 内部サーバー エラーです。  API 呼び出しをやり直してください。  エラーが解決しない場合は、[Microsoft サポート](https://go.microsoft.com/fwlink/?linkid=2165533)にお問い合わせください。

>[!Note]
>一度に変更できるのはプランまたは数量だけであり、両方ではありません。

>[!Note]
>この API は、変更についてエンド ユーザーから明示的な承認を得た後にのみ呼び出すことができます。

## <a name="cancel-a-subscription"></a>サブスクリプションを取り消す

この API を使用して、指定した SaaS サブスクリプションの登録を解除します。  公開元はこの API を使用する必要はありません。SaaS サブスクリプションを取り消すには、顧客をコマーシャル マーケットプレースに誘導することをお勧めします。

コマーシャル マーケットプレースで購入された SaaS サブスクリプションの取り消しを公開元側で実装する場合、公開元はこの API を呼び出す必要があります。  この呼び出しが完了すると、サブスクリプションの状態が Microsoft 側で *Unsubscribed* になります。

次に示す猶予期間中にサブスクリプションが取り消された場合、顧客への請求は行われません。

* 月単位のサブスクリプションでは、アクティブ化の後 24 時間。
* 年単位のサブスクリプションでは、アクティブ化の後 14 日間。

前述の猶予期間が経過した後にサブスクリプションが取り消された場合は、顧客への請求が行われます。  顧客は、取り消しの直後に Microsoft 側の SaaS サブスクリプションにアクセスできなくなります。 

### <a name="delete-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Delete `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*クエリ パラメーター:*

|  パラメーター         | 値             |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  2018-08-31 を使用します。  |
|  `subscriptionId`     | 購入済み SaaS サブスクリプションの一意識別子。  この ID は、Resolve API を使用してコマーシャル マーケットプレース認証トークンを解決した後に取得されます。  |

*要求ヘッダー:*
 
|  パラメーター         | 値             |
|  ---------------   |  ---------------  |
|  `content-type`      | `application/json`  |
|  `x-ms-requestid`    | クライアントからの要求を追跡するための一意の文字列値 (GUID を推奨)。  この値を指定しないと、値が生成され、応答ヘッダーに指定されます。  |
|  `x-ms-correlationid`  | クライアントでの操作に対する一意の文字列値。  このパラメーターによって、クライアント操作からのすべてのイベントがサーバー側のイベントに関連付けられます。  この値を指定しないと、値が生成され、応答ヘッダーに指定されます。  |
|  `authorization`     |  この API 呼び出しを行っている発行者を識別する一意のアクセス トークン。  「[Azure AD アプリに基づいたトークンの取得](./pc-saas-registration.md#get-the-token-with-an-http-post)」の説明に従って、発行者によってトークン値が取得される場合、形式は `"Bearer <access_token>"` です。 |

*応答コード:*

コード:202 - 登録を解除する要求が受理され、非同期に処理されました。  パートナーは、この要求の成功または失敗を判断するために **Operation-Location の URL** をポーリングする必要があります。  操作の最終状態として "*失敗*"、"*成功*"、または "*競合*" を受け取るまで数秒ごとにポーリングを行います。  最終的な操作の状態は迅速に返されますが、場合によっては数分かかることがあります。

また、コマーシャル マーケットプレース側でアクションが正常に完了すると、パートナーは Webhook 通知を受け取ります。  その段階でようやく、公開元側でサブスクリプションを取り消します。

*応答ヘッダー:*

|  パラメーター         | 値             |
|  ---------------   |  ---------------  |
|  `Operation-Location`        |  操作状態を取得するリソースへのリンク。  たとえば、「 `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=2018-08-31` 」のように入力します。 |

コード:400 - 無効な要求です。  削除は、この SaaS サブスクリプションの `allowedCustomerOperations` 一覧に含まれていません。

コード:403 - 許可されていません。  認証トークンが無効であるか、期限切れであるか、使用できません。 要求が、認証トークンの作成に使用されたものとは異なる Azure AD アプリ ID で公開されているオファーの SaaS サブスクリプションにアクセスしようとしています。

多くの場合、このエラーは、[SaaS 登録](pc-saas-registration.md)を正しく実行していないことを表す症状です。

コード:404 見つかりません。  `subscriptionId` を持つ SaaS サブスクリプションが見つかりません。

コード:500 - 内部サーバー エラーです。 API 呼び出しをやり直してください。  エラーが解決しない場合は、[Microsoft サポート](https://go.microsoft.com/fwlink/?linkid=2165533)にお問い合わせください。

## <a name="next-steps"></a>次のステップ

コマーシャル マーケットプレースでの SaaS オファーのその他のオプションについては、[コマーシャル マーケットプレースの測定サービス API](../marketplace-metering-service-apis.md) を参照してください。

[さまざまなプログラミング言語のクライアントを確認して、サンプルを使用します](https://github.com/microsoft/commercial-marketplace-samples)。
