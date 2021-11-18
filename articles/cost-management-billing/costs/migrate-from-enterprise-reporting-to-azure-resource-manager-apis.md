---
title: Enterprise Reporting から Azure Resource Manager API への移行
description: この記事は、Reporting API と Azure Resource Manager API の違い、Azure Resource Manager API に移行した場合に予想される状況、新しい Azure Resource Manager API に用意されている新機能について理解するのに役立ちます。
author: bandersmsft
ms.reviewer: adwise
ms.service: cost-management-billing
ms.subservice: common
ms.topic: reference
ms.date: 03/10/2021
ms.author: banders
ms.openlocfilehash: d1a8552c2e102586be5eb96f7dbb3821418440ef
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132719468"
---
# <a name="migrate-from-enterprise-reporting-to-azure-resource-manager-apis"></a>Enterprise Reporting から Azure Resource Manager API への移行

この記事は、[企業ユーザー向けの Azure Reporting API](../manage/enterprise-api.md) を使用してカスタム ソリューションを構築してきた開発者が Azure Resource Manager API for Cost Management に移行するのを支援するものです。 新しい Azure Resource Manager API では、サービス プリンシパルのサポートが一般提供となりました。 Azure Resource Manager API は、開発が続けられています。 従来の企業ユーザー向け Azure Reporting API を使用する代わりに、移行を検討してください。 従来の API は非推奨となります。 この記事は、Reporting API と Azure Resource Manager API の違い、Azure Resource Manager API に移行した場合に予想される状況、新しい Azure Resource Manager API に用意されている新機能について理解するのに役立ちます。

## <a name="api-differences"></a>API の相違点

以下の情報は、従来の企業ユーザー向け Reporting API と新しい Azure Resource Manager API の違いを説明したものです。

| **用途** | **Enterprise Agreement API** | **Azure Resource Manager API** |
| --- | --- | --- |
| 認証 | Enterprise Agreement (EA) ポータルでプロビジョニングされる API キー | ユーザー トークンまたはサービス プリンシパルを使用した Azure Active Directory (Azure AD) 認証。 サービス プリンシパルが API キーの代わりとなります。 |
| スコープとアクセス許可 | すべての要求は加入契約スコープとなります。 加入契約全体、部署、特定のアカウントのうち、どのデータが返されるかは、API キーのアクセス許可の割り当てによって決まります。 ユーザー認証はありません。 | ユーザーまたはサービス プリンシパルには、加入契約、部署、アカウントのいずれかのスコープへのアクセス権が割り当てられます。 |
| URI エンドポイント | https://consumption.azure.com | https://management.azure.com |
| 開発状況 | メンテナンス モードとなります。 非推奨となる予定です。 | 開発が進められています |
| 利用可能な API | 現在提供されているもののみ | 各 EA API の代わりとなる同等の API が提供されています。 <p> 加えて、[Cost Management API](/rest/api/cost-management/) も利用できます。以下はその例です。 <p> <ul><li>予算</li><li>警告<li>Exports</li></ul> |

## <a name="migration-checklist"></a>移行チェックリスト

- [Azure Resource Manager REST API](/rest/api/azure) について詳しく理解します。
- 使用している EA API を特定し、移行先となる Azure Resource Manager API を「[EA API と新しい Azure Resource Manager API のマッピング](#ea-api-mapping-to-new-azure-resource-manager-apis)」で確認します。
- Azure Resource Manager API に対するサービスの承認と認証を構成します
  - まだ Azure Resource Manager API を使用していない場合は、[クライアント アプリを Azure AD に登録](/rest/api/azure/#register-your-client-application-with-azure-ad)します。 API の呼び出しに使用するサービス プリンシパルが、登録によって自動的に作成されます。
  - サービス プリンシパルに、必要なスコープへのアクセス権を割り当てます (後述)。
  - [Azure AD 認証](/rest/api/azure/#create-the-request)とサービス プリンシパルを使用するようにプログラミング コードを更新します。
- API をテストしたうえでプログラミング コードを更新し、EA API 呼び出しを Azure Resource Manager API 呼び出しに置き換える。
- 新しいエラー コードを使用するように、エラー処理を更新します。 いくつかの考慮事項を次に示します。
  - Azure Resource Manager API には、60 秒のタイムアウト期間があります。
  - Azure Resource Manager API には、レート制限が設定されています。 そのため、レートを超えた場合は、429 スロットリング エラーが発生します。 短時間に多数の API 呼び出しを行うことのないようにソリューションを構築してください。
- Azure Resource Manager を通じて提供されている他の Cost Management API を確認し、今後の使用に備えて評価します。 詳細については、「[その他の Cost Management API を使用する](#use-additional-cost-management-apis)」を参照してください。

## <a name="assign-service-principal-access-to-azure-resource-manager-apis"></a>サービス プリンシパルに Azure Resource Manager API へのアクセス権を割り当てる

Azure Resource Manager API をプログラムから呼び出すためのサービス プリンシパルを作成した後、Azure Resource Manager で要求を承認して実行するためには、適切なアクセス許可をサービス プリンシパルに割り当てる必要があります。 アクセス許可には、各種シナリオに対応するために 2 つのフレームワークがあります。

### <a name="azure-billing-hierarchy-access"></a>Azure 課金階層アクセス

エンタープライズ課金アカウント、部署、登録アカウントの各スコープに対するアクセス許可をサービス プリンシパルに割り当てるには、「[Azure Enterprise Agreement サービス プリンシパル名にロールを割り当てる](../manage/assign-roles-azure-service-principals.md)」を参照してください。

### <a name="azure-role-based-access-control"></a>Azure ロールベースのアクセス制御

新たにサポートされるサービス プリンシパルの対象は、管理グループ、サブスクリプション、リソース グループなど Azure 固有のスコープにも及びます。 そうしたスコープへのアクセス許可をサービス プリンシパルに割り当てる際には、[Azure portal](../../active-directory/develop/howto-create-service-principal-portal.md#assign-a-role-to-the-application) で直接行うことも、[Azure PowerShell](../../active-directory/develop/howto-authenticate-service-principal-powershell.md#assign-the-application-to-a-role) を使用して行うこともできます。

## <a name="ea-api-mapping-to-new-azure-resource-manager-apis"></a>EA API と新しい Azure Resource Manager API のマッピング

以下の表を参照して、現在使用している EA API と、それに代わる Azure Resource Manager API を把握してください。

| **シナリオ** | **EA API** | **Azure Resource Manager API** |
| --- | --- | --- |
| 残高の概要 | [/balancesummary](/rest/api/billing/enterprise/billing-enterprise-api-balance-summary) |[Microsoft.Consumption/balances](/rest/api/consumption/balances/getbybillingaccount) |
| Price Sheet | [/pricesheet](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet) | [Microsoft.Consumption/pricesheets/default](/rest/api/consumption/pricesheet) - 交渉済みの価格用 <p> [Retail Prices API](/rest/api/cost-management/retail-prices/azure-retail-prices) - 小売価格用 |
| 予約インスタンスの詳細 | [/reservationdetails](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage) | [Microsoft.CostManagement/generateReservationDetailsReport](../reservations/reservation-utilization.md) |
| 予約インスタンスの概要 | [/reservationsummaries](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage) | [Microsoft.Consumption/reservationSummaries](/rest/api/consumption/reservationssummaries/list#reservationsummariesdailywithbillingaccountid) |
| 予約インスタンスの推奨情報 | [/SharedReservationRecommendations](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation)<p>[/SingleReservationRecommendations](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation) | [Microsoft.Consumption/reservationRecommendations](/rest/api/consumption/reservationrecommendations/list) |
| 予約インスタンスの料金 | [/reservationcharges](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges) | [Microsoft.Consumption/reservationTransactions](/rest/api/consumption/reservationtransactions/list) |

## <a name="migration-details-by-api"></a>API ごとの移行の詳細

以降の各セクションでは、従来の API 要求の例と、それに代わる新しい API の例を紹介します。

### <a name="balance-summary-api"></a>Balance Summary API

新しい Balance Summary API を呼び出す際は、以下の要求 URI を使用します。 billingAccountId には、実際の加入契約番号を使用する必要があります。

#### <a name="supported-requests"></a>サポートされている要求

[加入契約を対象に取得する](/rest/api/consumption/balances/getbybillingaccount)

```json
https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/providers/Microsoft.Consumption/balances?api-version=2019-10-01
```

### <a name="response-body-changes"></a>応答本文の変更

_以前の応答本文_:

```json
{
        "id": "enrollments/100/billingperiods/201507/balancesummaries",
          "billingPeriodId": 201507,
          "currencyCode": "USD",
          "beginningBalance": 0,
          "endingBalance": 1.1,
          "newPurchases": 1,
          "adjustments": 1.1,
          "utilized": 1.1,
          "serviceOverage": 1,
          "chargesBilledSeparately": 1,
          "totalOverage": 1,
          "totalUsage": 1.1,
          "azureMarketplaceServiceCharges": 1,
          "newPurchasesDetails": [
            {
              "name": "",
              "value": 1
            }
          ],
          "adjustmentDetails": [
            {
              "name": "Promo Credit",
              "value": 1.1
            },
            {
              "name": "SIE Credit",
              "value": 1.0
            }
          ]
    }

```

_新しい応答本文_:

同じデータは、新しい API 応答の `properties` フィールドから取得できます。 一部のフィールド名は、スペルが若干変更されている可能性があります。

```json
{
  "id": "/providers/Microsoft.Billing/billingAccounts/123456/providers/Microsoft.Billing/billingPeriods/201702/providers/Microsoft.Consumption/balances/balanceId1",
  "name": "balanceId1",
  "type": "Microsoft.Consumption/balances",
  "properties": {
    "currency": "USD  ",
    "beginningBalance": 3396469.19,
    "endingBalance": 2922371.02,
    "newPurchases": 0,
    "adjustments": 0,
    "utilized": 474098.17,
    "serviceOverage": 0,
    "chargesBilledSeparately": 0,
    "totalOverage": 0,
    "totalUsage": 474098.17,
    "azureMarketplaceServiceCharges": 609.82,
    "billingFrequency": "Month",
    "priceHidden": false,
    "newPurchasesDetails": [
      {
        "name": "Promo Purchase",
        "value": 1
      }
    ],
    "adjustmentDetails": [
      {
        "name": "Promo Credit",
        "value": 1.1
      },
      {
        "name": "SIE Credit",
        "value": 1
      }
    ]
  }
}

```

### <a name="price-sheet"></a>Price Sheet

新しい Price Sheet API を呼び出す際は、以下の要求 URI を使用します。

#### <a name="supported-requests"></a>サポートされている要求

 この API は、次のスコープを使用して呼び出すことができます。

- 加入契約: `providers/Microsoft.Billing/billingAccounts/{billingAccountId}`
- サブスクリプション: `subscriptions/{subscriptionId}`

[_現在の請求期間を対象に取得する_](/rest/api/consumption/pricesheet/get)


```json
https://management.azure.com/{scope}/providers/Microsoft.Consumption/pricesheets/default?api-version=2019-10-01
```

[_特定の請求期間を対象に取得する_](/rest/api/consumption/pricesheet/getbybillingperiod)

```json
https://management.azure.com/{scope}/providers/Microsoft.Billing/billingPeriods/{billingPeriodName}/providers/Microsoft.Consumption/pricesheets/default?api-version=2019-10-01
```

#### <a name="response-body-changes"></a>応答本文の変更

_以前の応答_:

```json
      [
        {
              "id": "enrollments/57354989/billingperiods/201601/products/343/pricesheets",
              "billingPeriodId": "201704",
            "meterId": "dc210ecb-97e8-4522-8134-2385494233c0",
              "meterName": "A1 VM",
              "unitOfMeasure": "100 Hours",
              "includedQuantity": 0,
              "partNumber": "N7H-00015",
              "unitPrice": 0.00,
              "currencyCode": "USD"
        },
        {
              "id": "enrollments/57354989/billingperiods/201601/products/2884/pricesheets",
              "billingPeriodId": "201404",
            "meterId": "dc210ecb-97e8-4522-8134-5385494233c0",
              "meterName": "Locally Redundant Storage Premium Storage - Snapshots - AU East",
              "unitOfMeasure": "100 GB",
              "includedQuantity": 0,
              "partNumber": "N9H-00402",
              "unitPrice": 0.00,
              "currencyCode": "USD"
        },
        ...
    ]

```

_新しい応答_:

以前のデータは、新しい API 応答の `pricesheets` フィールドから取得できます。 測定の詳細情報も提供されます。

```json
{
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Billing/billingPeriods/201702/providers/Microsoft.Consumption/pricesheets/default",
  "name": "default",
  "type": "Microsoft.Consumption/pricesheets",
  "properties": {
    "nextLink": "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/microsoft.consumption/pricesheets/default?api-version=2018-01-31&$skiptoken=AQAAAA%3D%3D&$expand=properties/pricesheets/meterDetails",
    "pricesheets": [
      {
        "billingPeriodId": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Billing/billingPeriods/201702",
        "meterId": "00000000-0000-0000-0000-000000000000",
        "unitOfMeasure": "100 Hours",
        "includedQuantity": 100,
        "partNumber": "XX-11110",
        "unitPrice": 0.00000,
        "currencyCode": "EUR",
        "offerId": "OfferId 1",
        "meterDetails": {
          "meterName": "Data Transfer Out (GB)",
          "meterCategory": "Networking",
          "unit": "GB",
          "meterLocation": "Zone 2",
          "totalIncludedQuantity": 0,
          "pretaxStandardRate": 0.000
        }
      }
    ]
  }
}

```

### <a name="reserved-instance-usage-details"></a>Reserved Instance 使用量の詳細

同期ベースの Reservation Details API には、Microsoft は積極的に取り組んでいません。 SPN に対応した新しい非同期 API 呼び出しパターンへの変更を、移行の一環としてお勧めします。 非同期要求の方が大量のデータを処理しやすく、タイムアウト エラーも少なくなります。

#### <a name="supported-requests"></a>サポートされている要求

新しい Asynchronous Reservation Details API を呼び出す際は、以下の要求 URI を使用します。 `billingAccountId` には、実際の加入契約番号を使用する必要があります。 この API は、次のスコープを使用して呼び出すことができます。

- 加入契約: `providers/Microsoft.Billing/billingAccounts/{billingAccountId}`

#### <a name="sample-request-to-generate-a-reservation-details-report"></a>予約の詳細レポートを生成する要求のサンプル

```json
POST 
https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/providers/Microsoft.CostManagement/generateReservationDetailsReport?startDate={startDate}&endDate={endDate}&api-version=2019-11-01

```

#### <a name="sample-request-to-poll-report-generation-status"></a>レポートの生成状態をポーリングする要求のサンプル

```json
GET
https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/providers/Microsoft.CostManagement/reservationDetailsOperationResults/{operationId}?api-version=2019-11-01

```

#### <a name="sample-poll-response"></a>ポーリング応答のサンプル

```json
{
  "status": "Completed",
  "properties": {
    "reportUrl": "https://storage.blob.core.windows.net/details/20200911/00000000-0000-0000-0000-000000000000?sv=2016-05-31&sr=b&sig=jep8HT2aphfUkyERRZa5LRfd9RPzjXbzB%2F9TNiQ",
    "validUntil": "2020-09-12T02:56:55.5021869Z"
  }
}

```

#### <a name="response-body-changes"></a>応答本文の変更

以前の同期ベースの Reservation Details API から返される応答は次のとおりです。

_以前の応答_:

```json
{
    "reservationOrderId": "00000000-0000-0000-0000-000000000000",
    "reservationId": "00000000-0000-0000-0000-000000000000",
    "usageDate": "2018-02-01T00:00:00",
    "skuName": "Standard_F2s",
    "instanceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/resourvegroup1/providers/microsoft.compute/virtualmachines/VM1",
    "totalReservedQuantity": 18.000000000000000,
    "reservedHours": 432.000000000000000,
    "usedHours": 400.000000000000000
}

```

_新しい応答_:

新しい API では、自動的に CSV ファイルが作成されます。 次のファイル フィールドをご覧ください。

| **以前のプロパティ** | **新しいプロパティ** | **メモ** |
| --- | --- | --- |
|  | InstanceFlexibilityGroup | インスタンスの柔軟性に関する新しいプロパティ。 |
|  | InstanceFlexibilityRatio | インスタンスの柔軟性に関する新しいプロパティ。 |
| instanceId | InstanceName |  |
|  | 種類 | 新しいプロパティです。 `None`、`Reservation`、`IncludedQuantity` のいずれかの値となります。 |
| reservationId | ReservationId |  |
| reservationOrderId | ReservationOrderId |  |
| reservedHours | ReservedHours |  |
| skuName | SkuName |  |
| totalReservedQuantity | TotalReservedQuantity |  |
| usageDate | UsageDate |  |
| usedHours | UsedHours |  |

### <a name="reserved-instance-usage-summary"></a>Reserved Instance 使用量の概要

新しい Reservation Summaries API を呼び出すには、以下の要求 URI を使用します。

#### <a name="supported-requests"></a>サポートされている要求

 この API は、次のスコープを使用して呼び出します。

- 加入契約: `providers/Microsoft.Billing/billingAccounts/{billingAccountId}`

[_日単位の予約概要を取得する_](/rest/api/consumption/reservationssummaries/list#reservationsummariesdailywithbillingaccountid)

```json
https://management.azure.com/{scope}/Microsoft.Consumption/reservationSummaries?grain=daily&$filter=properties/usageDate ge 2017-10-01 AND properties/usageDate le 2017-11-20&api-version=2019-10-01
```

[_月単位の予約概要を取得する_](/rest/api/consumption/reservationssummaries/list#reservationsummariesmonthlywithbillingaccountid)

```json
https://management.azure.com/{scope}/Microsoft.Consumption/reservationSummaries?grain=daily&$filter=properties/usageDate ge 2017-10-01 AND properties/usageDate le 2017-11-20&api-version=2019-10-01
```

#### <a name="response-body-changes"></a>応答本文の変更

_以前の応答_:

```json
[
     {
        "reservationOrderId": "00000000-0000-0000-0000-000000000000",
        "reservationId": "00000000-0000-0000-0000-000000000000",
        "skuName": "Standard_F1s",
        "reservedHours": 24,
        "usageDate": "2018-05-01T00:00:00",
        "usedHours": 23,
        "minUtilizationPercentage": 0,
        "avgUtilizationPercentage": 95.83,
        "maxUtilizationPercentage": 100
    }
]

```

_新しい応答_:

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/12345/providers/Microsoft.Consumption/reservationSummaries/reservationSummaries_Id1",
      "name": "reservationSummaries_Id1",
      "type": "Microsoft.Consumption/reservationSummaries",
      "tags": null,
      "properties": {
        "reservationOrderId": "00000000-0000-0000-0000-000000000000",
        "reservationId": "00000000-0000-0000-0000-000000000000",
        "skuName": "Standard_B1s",
        "reservedHours": 720,
        "usageDate": "2018-09-01T00:00:00-07:00",
        "usedHours": 0,
        "minUtilizationPercentage": 0,
        "avgUtilizationPercentage": 0,
        "maxUtilizationPercentage": 0
      }
    }
  ]
}

```

### <a name="reserved-instance-recommendations"></a>予約インスタンスの推奨情報

新しい Reservation Recommendations API を呼び出すには、以下の要求 URI を使用します。

#### <a name="supported-requests"></a>サポートされている要求

 この API は、次のスコープを使用して呼び出します。

- 加入契約: `providers/Microsoft.Billing/billingAccounts/{billingAccountId}`
- サブスクリプション: `subscriptions/{subscriptionId}`
- リソース グループ: `subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}`

[_推奨情報を取得する_](/rest/api/consumption/reservationrecommendations/list)

この API からは、共有スコープと単一スコープの両方の推奨情報を入手できます。 スコープには、オプションの API パラメーターとしてフィルターを適用することもできます。

```json
https://management.azure.com/providers/Microsoft.Billing/billingAccounts/123456/providers/Microsoft.Consumption/reservationRecommendations?api-version=2019-10-01
```

#### <a name="response-body-changes"></a>応答本文の変更

共有スコープと単一スコープの推奨情報が 1 つの API に統合されています。

_以前の応答_:

```json
[{
    "subscriptionId": "1111111-1111-1111-1111-111111111111",
    "lookBackPeriod": "Last7Days",
    "meterId": "2e3c2132-1398-43d2-ad45-1d77f6574933",
    "skuName": "Standard_DS1_v2",
    "term": "P1Y",
    "region": "westus",
    "costWithNoRI": 186.27634908960002,
    "recommendedQuantity": 9,
    "totalCostWithRI": 143.12931642978083,
    "netSavings": 43.147032659819189,
    "firstUsageDate": "2018-02-19T00:00:00"
}
]

```

_新しい応答_:

```json
{
  "value": [
    {
      "id": "billingAccount/123456/providers/Microsoft.Consumption/reservationRecommendations/00000000-0000-0000-0000-000000000000",
      "name": "00000000-0000-0000-0000-000000000000",
      "type": "Microsoft.Consumption/reservationRecommendations",
      "location": "westus",
      "sku": "Standard_DS1_v2",
      "kind": "legacy",
      "properties": {
        "meterId": "00000000-0000-0000-0000-000000000000",
        "term": "P1Y",
        "costWithNoReservedInstances": 12.0785105,
        "recommendedQuantity": 1,
        "totalCostWithReservedInstances": 11.4899644807748,
        "netSavings": 0.588546019225182,
        "firstUsageDate": "2019-07-07T00:00:00-07:00",
        "scope": "Shared",
        "lookBackPeriod": "Last7Days",
        "instanceFlexibilityRatio": 1,
        "instanceFlexibilityGroup": "DSv2 Series",
        "normalizedSize": "Standard_DS1_v2",
        "recommendedQuantityNormalized": 1,
        "skuProperties": [
          {
            "name": "Cores",
            "value": "1"
          },
          {
            "name": "Ram",
            "value": "1"
          }
        ]
      }
    },
   ]
}

```

### <a name="reserved-instance-charges"></a>予約インスタンスの料金

新しい Reserved Instance Charges API を呼び出すには、以下の要求 URI を使用します。

#### <a name="supported-requests"></a>サポートされている要求

[_期間ごとの予約料金を取得する_](/rest/api/consumption/reservationtransactions/list)

```json
https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/providers/Microsoft.Consumption/reservationTransactions?$filter=properties/eventDate+ge+2020-05-20+AND+properties/eventDate+le+2020-05-30&api-version=2019-10-01
```

#### <a name="response-body-changes"></a>応答本文の変更

_以前の応答_:

```json
[
    {
        "purchasingEnrollment": "string",
        "armSkuName": "Standard_F1s",
        "term": "P1Y",
        "region": "eastus",
        "PurchasingsubscriptionGuid": "00000000-0000-0000-0000-000000000000",
        "PurchasingsubscriptionName": "string",
        "accountName": "string",
        "accountOwnerEmail": "string",
        "departmentName": "string",
        "costCenter": "",
        "currentEnrollment": "string",
        "eventDate": "string",
        "reservationOrderId": "00000000-0000-0000-0000-000000000000",
        "description": "Standard_F1s eastus 1 Year",
        "eventType": "Purchase",
        "quantity": int,
        "amount": double,
        "currency": "string",
        "reservationOrderName": "string"
    }
]

```
_新しい応答_:

```json
{
  "value": [
    {
      "id": "/billingAccounts/123456/providers/Microsoft.Consumption/reservationtransactions/201909091919",
      "name": "201909091919",
      "type": "Microsoft.Consumption/reservationTransactions",
      "tags": {},
      "properties": {
        "eventDate": "2019-09-09T19:19:04Z",
        "reservationOrderId": "00000000-0000-0000-0000-000000000000",
        "description": "Standard_DS1_v2 westus 1 Year",
        "eventType": "Cancel",
        "quantity": 1,
        "amount": -21,
        "currency": "USD",
        "reservationOrderName": "Transaction-DS1_v2",
        "purchasingEnrollment": "123456",
        "armSkuName": "Standard_DS1_v2",
        "term": "P1Y",
        "region": "westus",
        "purchasingSubscriptionGuid": "11111111-1111-1111-1111-11111111111",
        "purchasingSubscriptionName": "Infrastructure Subscription",
        "accountName": "Microsoft Infrastructure",
        "accountOwnerEmail": "admin@microsoft.com",
        "departmentName": "Unassigned",
        "costCenter": "",
        "currentEnrollment": "123456",
        "billingFrequency": "recurring"
      }
    },
  ]
}

```

## <a name="use-additional-cost-management-apis"></a>その他の Cost Management API を使用する

Azure Resource Manager API に移行して既存のレポート シナリオを実現したら、他のさまざまな API も使ってみましょう。 それらの API も Azure Resource Manager を通じて提供され、サービス プリンシパルベースの認証を使用して自動化することができます。 以下、使用できる新機能を簡単にまとめました。

- [Budgets](/rest/api/consumption/budgets/createorupdate) - しきい値を設定して予防的にコストを監視し、しきい値を超えた場合には、直接の利害関係者にアラートを送信し、対応を自動化する目的で使用します。

- [Alerts](/rest/api/cost-management/alerts) - アラート情報 (予算アラート、請求書アラート、クレジット アラート、クォータ アラートなど) を確認する目的で使用します。

- [Exports](/rest/api/cost-management/exports) - 任意の Azure ストレージ アカウントに対する課金データを定期的にエクスポートするスケジュールの設定に使用します。 Azure を積極的に活用しており、そのデータを分析して独自の内部システムで使用したいと考えるお客様に推奨されるソリューションです。

## <a name="next-steps"></a>次のステップ

- [Azure Resource Manager REST API](/rest/api/azure) について詳しく理解します。
- 必要であれば、現在使用している EA API を調べ、移行先となる Azure Resource Manager API を「[EA API と新しい Azure Resource Manager API のマッピング](#ea-api-mapping-to-new-azure-resource-manager-apis)」で確認します。
- まだ Azure Resource Manager API を使用していない場合は、[クライアント アプリを Azure AD に登録](/rest/api/azure/#register-your-client-application-with-azure-ad)します。
- 必要であれば、[Azure AD 認証](/rest/api/azure/#create-the-request)とサービス プリンシパルを使用するように既存のプログラミング コードを更新します。