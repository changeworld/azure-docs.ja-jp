---
title: Microsoft Azure Consumption Commitment (MACC) を追跡する
description: Microsoft 顧客契約の Microsoft Azure Consumption Commitment (MACC) を追跡する方法について説明します。
author: bandersmsft
ms.reviewer: amberb
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 10/11/2021
ms.author: banders
ms.openlocfilehash: 741efbce1f8578ca425059b2b04b64d9892112cb
ms.sourcegitcommit: d2875bdbcf1bbd7c06834f0e71d9b98cea7c6652
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/12/2021
ms.locfileid: "129857612"
---
# <a name="track-your-microsoft-azure-consumption-commitment-macc"></a>Microsoft Azure Consumption Commitment (MACC) を追跡する

Microsoft Azure Consumption Commitment (MACC) は、お客様の組織が Microsoft Azure の費用に対して長期にわたって行うことがある契約上のコミットメントです。 Microsoft 顧客契約 (MCA) 請求アカウントまたは Enterprise Agreement (EA) 請求アカウントの MACC をお持ちの組織の場合、開始日、終了日、残りのコミットメント、対象となる費用など、コミットメントの重要な側面を Azure portal または REST API で確認できます。

## <a name="track-your-macc-commitment"></a>MACC コミットメントを追跡する

### <a name="azure-portal"></a>[Azure Portal](#tab/portal)

1. [Azure portal](https://portal.azure.com) にサインインします。
2. "**コスト管理 + 請求**" を検索します。  
    :::image type="content" source="./media/track-consumption-commitment/billing-search-cost-management-billing.png" alt-text="ポータルでの [コストの管理と請求] の検索を示すスクリーンショット。" lightbox="./media/track-consumption-commitment/billing-search-cost-management-billing.png" :::
3. コミットメントを追跡する課金アカウントを [課金スコープ] ページで選択します。 請求アカウントの種類は、Microsoft 顧客契約 (MCA) のお客様の **Microsoft 顧客契約** または EA のお客様の **Enterprise Agreement** である必要があります。   
    :::image type="content" source="./media/track-consumption-commitment/list-of-scopes.png" alt-text="課金スコープを示すスクリーンショット。" lightbox="./media/track-consumption-commitment/list-of-scopes.png" :::
    > [!NOTE]
     > ユーザーが最近アクセスした課金スコープは、Azure portal によって記憶されます。次回 [コストの管理と請求] ページにアクセスすると、そのスコープが表示されます。 過去に [コストの管理と請求] にアクセスしたことがなければ、課金スコープ ページは表示されません。 その場合は、[適切なスコープ](#check-access-to-a-microsoft-customer-agreement)になっていることを確認してください。 適切でない場合は、[スコープを切り替え](view-all-accounts.md#switch-billing-scope-in-the-azure-portal)て Microsoft 顧客契約の課金アカウントを選択します。
4. ご利用の契約の種類に応じて、次のいずれかを実行します。
    - MCA のお客様の場合は、左側から **[プロパティ]** を選択し、 **[Microsoft Azure Consumption Commitment (MACC)]** を選択します。  
        :::image type="content" source="./media/track-consumption-commitment/select-macc-tab.png" alt-text="MCA の場合の MACC タブの選択を示すスクリーンショット。" lightbox="./media/track-consumption-commitment/select-macc-tab.png" :::
    - EA のお客様の場合は、左側のナビゲーション メニューで **[クレジットとコミットメント]** を選択し、 **[Microsoft Azure Consumption Commitment (MACC)]** を選択します。  
        :::image type="content" source="./media/track-consumption-commitment/select-macc-tab-ea.png" alt-text="EA の場合の MACC タブの選択を示すスクリーンショット。" lightbox="./media/track-consumption-commitment/select-macc-tab-ea.png" :::
1. [Microsoft Azure Consumption Commitment (MACC)] タブには、次のセクションがあります。

#### <a name="remaining-commitment"></a>[Remaining commitment]\(残りのコミットメント\) 

[Remaining commitment]\(残りのコミットメント\) には、前回の請求書以降の残りのコミットメント金額が表示されます。

:::image type="content" source="./media/track-consumption-commitment/macc-remaining-commitment.png" alt-text="MACC の [Remaining commitment]\(残りのコミットメント\) のスクリーンショット。" lightbox="./media/track-consumption-commitment/macc-remaining-commitment.png" :::

#### <a name="details"></a>詳細

[詳細] セクションには、コミットメントのその他の重要な側面が表示されます。

:::image type="content" source="./media/track-consumption-commitment/macc-details.png" alt-text="MACC の [詳細] のスクリーンショット。" lightbox="./media/track-consumption-commitment/macc-details.png" :::

| 用語 | 定義 |
|---|---|
| id | MACC を一意に識別する識別子。 この識別子は、REST API で MACC の情報を取得するために使用されます。 |
| ご購入日 | コミットメントを行った日付。 |
| 開始日 | コミットメントが有効になった日付。 |
| 終了日 | コミットメントが期限切れになった日付。 |
| コミットメント金額 | MACC 対象の製品またはサービスに使用することをコミットした金額です。 |
| 状態 | コミットメントの状態。 |

MACC の状態は次のいずれかです。

- アクティブ: MACC はアクティブです。 対象となる費用がある場合、MACC コミットメントに反映されます。
- 完了: MACC コミットメントを完了しました。 
- 期限切れ: MACC の有効期限が切れています。 詳細については、Microsoft アカウント チームにお問い合わせください。 
- 取り消し済み: MACC は取り消し済みです。 新しい Azure の費用は、MACC コミットメントに反映されません。

#### <a name="events"></a>events

[イベント] セクションには、MACC コミットメントが減ったイベント (請求済みの費用) が表示されます。

:::image type="content" source="./media/track-consumption-commitment/macc-events.png" alt-text="MACC イベントのスクリーンショット。" lightbox="./media/track-consumption-commitment/macc-events.png" :::

| 用語 | 定義 |
|---|---|
| Date | イベントが発生した日付 |
| 説明 | イベントの説明 |
| 請求プロファイル | イベントが発生した課金プロファイル。 課金プロファイルは、Microsoft 顧客契約にのみ適用されます。 EA の登録を行っている場合、課金プロファイルは表示されません。 |
| MACC decrement (MACC の減少) | イベント以降の MACC 減少額 |
| Remaining commitment (残りのコミットメント) | イベント後の残りの MACC コミットメント |

### <a name="rest-api"></a>[REST API](#tab/rest)

[Azure Billing](/rest/api/billing/) と [Consumption](/rest/api/consumption/) の各 API を使用して、課金アカウントの Microsoft Azure Consumption Commitment (MACC) をプログラムで取得できます。

次に示す例では、REST API を使用します。 現時点では、PowerShell と Azure CLI はサポートされていません。 出力例は Microsoft 顧客契約の場合であるため、Enterprise Agreement の出力は異なります。

### <a name="find-billing-accounts-you-have-access-to"></a>アクセスできる課金アカウントを検索する

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts?api-version=2020-05-01
```
API の応答により、課金アカウントの一覧が返されます。

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/9a157b81-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx",
      "name": "9a157b81-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx",
      "type": "Microsoft.Billing/billingAccounts",
      "properties": {
        "displayName": "Contoso",
        "agreementType": "MicrosoftCustomerAgreement",
        "accountStatus": "Active",
        "accountType": "Enterprise",
        "hasReadAccess": true,
      }
    },
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/9a12f056-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx",
      "name": "9a12f056-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx",
      "type": "Microsoft.Billing/billingAccounts",
      "properties": {
        "displayName": "Kayla Lewis",
        "agreementType": "MicrosoftCustomerAgreement",
        "accountStatus": "Active",
        "accountType": "Individual",
        "hasReadAccess": true,
      }
    }
  ]
}
```

課金アカウントの `displayName` プロパティを使用して、MACC を追跡する課金アカウントを指定します。 課金アカウントの `name` をコピーします。 たとえば、**Contoso** の課金アカウントの MACC を追跡する場合は、`9a157b81-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx` をコピーします。 次のステップで使用できるように、この値をどこかに貼り付けておきます。

### <a name="get-a-list-of-microsoft-azure-consumption-commitments"></a>Microsoft Azure Consumption Commitment の一覧を取得する

次の要求を実行します。その際、`<billingAccountName>` を最初のステップでコピーした `name` (`9a157b81-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx`) と置き換えます。

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/<billingAccountName>/providers/Microsoft.Consumption/lots?api-version=2021-05-01&$filter=source%20eq%20%27ConsumptionCommitment%27
```
API 応答により、課金アカウントの MACC の一覧が返されます。

```json
    {
    "value": [
      {
        "id": "/providers/Microsoft.Billing/billingAccounts/9a157b81-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/providers/Microsoft.Consumption/lots/G2021032459206000XXXX",
        "name": "G2021032459206000XXXX",
        "type": "Microsoft.Consumption/lots",
        "eTag": null,
        "properties": {
          "purchasedDate": "2021-03-24T16:26:46.0000000Z",
          "status": "Active",
          "originalAmount": {
            "currency": "USD",
            "value": 10000.0
          },
          "closedBalance": {
            "currency": "USD",
            "value": 9899.42
          },
          "source": "ConsumptionCommitment",
          "startDate": "2021-03-01T00:00:00.0000000Z",
          "expirationDate": "2024-02-28T00:00:00.0000000Z"
        }
      },
      {
        "id": "/providers/Microsoft.Billing/billingAccounts/9a157b81-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/providers/Microsoft.Consumption/lots/G1011082459206000XXXX",
        "name": "G1011082459206000XXXX",
        "type": "Microsoft.Consumption/lots",
        "eTag": null,
        "properties": {
          "purchasedDate": "2021-03-24T16:26:46.0000000Z",
          "status": "Complete",
          "originalAmount": {
            "currency": "USD",
            "value": 10000.0
          },
          "closedBalance": {
            "currency": "USD",
            "value": 0.00
          },
          "source": "ConsumptionCommitment",
          "startDate": "2020-03-01T00:00:00.0000000Z",
          "expirationDate": "2021-02-28T00:00:00.0000000Z"
        }
      }
    ]
  }
```

| 要素名  | 説明                                                                                               |
|---------------|-----------------------------------------------------------------------------------------------------------|
| `purchasedDate`  | MACC が購入された日付。   |
| `status`  | コミットメントの状態。 |
| `originalAmount` | 元のコミットメント金額。 |
| `closedBalance`   | 前回の請求書以降の残りのコミットメント。    |
| `source`      | MACC の場合、ソースは常に ConsumptionCommitment になります。 |
| `startDate`      |  MACC がアクティブになった日付。  |
| `expirationDate`  | MACC が期限切れになった日付。   |

MACC の状態は次のいずれかです。 

- アクティブ: MACC はアクティブです。 対象となる費用がある場合、MACC コミットメントに反映されます。
- 完了: MACC コミットメントを完了しました。 
- 期限切れ: MACC の有効期限が切れています。 詳細については、Microsoft アカウント チームにお問い合わせください。 
- 取り消し済み: MACC は取り消し済みです。 新しい Azure の費用は、MACC コミットメントに反映されません。 

### <a name="get-events-that-affected-macc-commitment"></a>MACC コミットメントに影響したイベントを取得する

次の要求を実行します。その際、`<billingAccountName>` を最初のステップでコピーした `name` (`5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx`) と置き換えます。 目的とする期間のイベントを取得するには、**startDate** と **endDate** を渡す必要があります。

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/<billingAccountName>/providers/Microsoft.Consumption/events?api-version=2021-05-01&startDate=<startDate>&endDate=<endDate>&$filter=lotsource%20eq%20%27ConsumptionCommitment%27
```

API 応答により、MACC コミットメントに影響したすべてのイベントが返されます。

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/providers/Microsoft.Consumption/events/103axxxx-2c25-7xx3-f2a0-ad9a3f1c91xx",
      "name": "103axxxx-2c25-7xx3-f2a0-ad9a3f1c91xx",
      "type": "Microsoft.Consumption/events",
      "eTag": null,
      "properties": {
        "billingProfileId": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/SWFF-DVM4-XXX-XXX",
        "billingProfileDisplayName": "Finance",
        "lotId": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/providers/Microsoft.Consumption/lots/G2021032459206000XXXX",
        "lotSource": "ConsumptionCommitment",
        "transactionDate": "2021-05-05T00:09:13.0000000Z",
        "description": "Balance after invoice T00075XXXX",
        "charges": {
          "currency": "USD",
          "value": -100.0
        },
        "closedBalance": {
          "currency": "USD",
          "value": 9899.71
        },
        "eventType": "SettledCharges",
        "invoiceNumber": "T00075XXXX"
      }
    },
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/providers/Microsoft.Consumption/events/203axxxx-2c25-7xx3-f2a0-ad9a3f1c91xx",
      "name": "203axxxx-2c25-7xx3-f2a0-ad9a3f1c91xx",
      "type": "Microsoft.Consumption/events",
      "eTag": null,
      "properties": {
        "billingProfileId": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/SWFF-DVM4-XXX-XXX",
        "billingProfileDisplayName": "Engineering",
        "lotId": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/providers/Microsoft.Consumption/lots/G2021032459206000XXXX",
        "lotSource": "ConsumptionCommitment",
        "transactionDate": "2021-04-05T00:09:13.0000000Z",
        "description": "Balance after invoice T00074XXXX",
        "charges": {
          "currency": "USD",
          "value": -0.29
        },
        "closedBalance": {
          "currency": "USD",
          "value": 9999.71
        },
        "eventType": "SettledCharges",
        "invoiceNumber": "T00074XXXX"
      }
    }
  ]
}

```
| 要素名  | 説明                                                                                               |
|---------------|-----------------------------------------------------------------------------------------------------------|
| `billingProfileId` | イベントが発生した課金プロファイルの一意の識別子。 |
| `billingProfileDisplayName` | イベントが発生した課金プロファイルの表示名。 |
| `lotId`   | MACC の一意の識別子。    |
| `lotSource`      | これは MACC の ConsumptionCommitment になります。 |
| `transactionDate`      |  イベントが発生した日付。  |
| `description`  | イベントの説明です。   |
| `charges`  | MACC の減少額。   |
| `closedBalance`  | イベント後の残高。   |
| `eventType`  | MACC には SettledCharges イベントのみがサポートされます。   |
| `invoiceNumber`  | MACC の請求額が減った請求書の一意の ID。   |

---

## <a name="azure-services-and-marketplace-offers-that-are-eligible-for-macc"></a>MACC の対象となる Azure サービスと Marketplace プラン

MACC の減少の対象となる Azure サービスまたは Marketplace プランは、Azure portal で判別できます。 詳細については、「[Azure の消費コミットメント (MACC/CtC) の対象となるオファーを判別する](/marketplace/azure-consumption-commitment-benefit#determine-which-offers-are-eligible-for-azure-consumption-commitments-maccctc)」を参照してください。

## <a name="azure-credits-and-macc"></a>Azure クレジットと MACC

組織が Microsoft から Azure クレジットを受け取った場合、クレジットの対象となる消費または購入は、MACC コミットメントに反映されません。

組織が Azure 前払いを購入した場合、クレジットの対象となる消費または購入は、MACC コミットメントに反映されません。  ただし、実際の前払いの購入自体により、MACC コミットメントは減少します。

たとえば、Contoso は、5 月に 50,000 ドルの MACC コミットメントを行いました。 6 月には、Azure 前払いを 10,000 ドル購入しました。 この購入により、MACC コミットメントは減り、残りのコミットメントは 40,000 ドルになります。 6 月に、Contoso は、Azure 前払いの対象となるサービスを 10,000 ドル消費しました。 このサービス料金は Azure 前払いの対象となりますが、サービス料金によって MACC コミットメントが減ることはありません。 Azure 前払いが完全に使用されると、すべての Azure サービスの消費とその他の対象となる購入により、MACC コミットメントは減ります。

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Microsoft 顧客契約にアクセスできるかどうかを確認する
[!INCLUDE [billing-check-mca](../../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>お困りの際は、 サポートにお問い合せください。

お困りの際は、問題を迅速に解決するために、[サポートにお問い合わせ](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)ください。

## <a name="next-steps"></a>次のステップ

- [Azure の消費コミットメント (MACC/CtC) の対象となるオファーを判別する](/marketplace/azure-consumption-commitment-benefit#determine-which-offers-are-eligible-for-azure-consumption-commitments-maccctc)
- [Azure クレジット残高を追跡する](mca-check-azure-credits-balance.md)