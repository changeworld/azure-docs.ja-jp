---
title: パートナー向け Azure Cost Management の利用を開始する
description: この記事では、パートナーが Azure Cost Management の機能を使用する方法と、パートナーの顧客のために Cost Management アクセスを有効にする方法について説明します。
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 11/04/2019
ms.topic: conceptual
ms.service: cost-management
manager: aparnag
ms.custom: secdec18
ms.openlocfilehash: 611b3e608d9b0de9423c861ec70e9fc2e7ad67d5
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/07/2019
ms.locfileid: "73720757"
---
# <a name="get-started-with-azure-cost-management-for-partners"></a>パートナー向け Azure Cost Management の利用を開始する

Azure Cost Management は、Microsoft 顧客契約に顧客をオンボードしたパートナーがネイティブで利用できます。 この記事では、パートナーが [Azure Cost Management](https://docs.microsoft.com/azure/cost-management/) の機能を使用する方法について説明します。 また、パートナーが顧客のために Cost Management アクセスを有効にする方法についても説明します。 顧客は、CSP パートナーによって有効化された場合、Cost Management の機能を使用できます。

CSP パートナーは、次の目的に Cost Management を使用します。

- 請求済みコストを把握し、コストを顧客、サブスクリプション、リソース グループ、およびサービスに関連付けます。
- [コスト分析](quick-acm-cost-analysis.md)で、Azure のコストを直感的に把握します。顧客、サブスクリプション、リソース グループ、リソース、メーター、サービス、その他多数のディメンションによってコストを分析する機能を備えています。
- コスト分析で、パートナー獲得クレジット (PEC) が適用されているリソース コストを表示します。
- プログラムによる[予算](tutorial-acm-create-budgets.md)と、コストが予算を超過したときのアラートを使用して、通知と自動化を設定します。
- Cost Management データへのアクセスを顧客に提供する Azure Resource Manager ポリシーを有効にします。 顧客はその後、[従量課金制料金](https://azure.microsoft.com/pricing/calculator/)を使用するサブスクリプションの使用量コスト データを表示できます。

すべての顧客のコストを示す例を次に示します。
![すべての顧客のコストを示す例](./media/get-started-partners/customer-costs1.png)

1 人の顧客のコストを示す例を次に示します。
![1 人の顧客のコストを示す例](./media/get-started-partners/customer-costs2.png)

Azure Cost Management で使用できるすべての機能は、REST API でも使用できます。 API を使用してコスト管理タスクを自動化します。

## <a name="prerequisites"></a>前提条件

Azure Cost Management には、課金アカウントまたはサブスクリプションへの読み取りアクセスが必要です。 課金アカウントまたは管理グループから、アプリを管理する個々のリソース グループまで、リソースよりも上の任意のレベルでアクセスを許可できます。 課金アカウントでの Azure Cost Management へのアクセスの有効化と割り当ての詳細については、「[ユーザー ロールとアクセス許可の割り当て](/partner-center/permissions-overview)」を参照してください。 **全体管理者**ロールと**管理エージェント** ロールは、課金アカウントのコストを管理できます。

サポートされているアカウントの種類の完全な一覧については、「[Cost Management のデータを理解する](understand-cost-mgt-data.md)」を参照してください。


## <a name="how-cost-management-uses-scopes"></a>Cost Management でのスコープの使用方法

スコープは、お客様が課金データの管理、請求書の表示、およびアカウント全般の管理を行う場所であり、支払い専用のロールを持ちます。 課金とアカウントに関するロールは、RBAC を使用するリソース管理用のスコープとは別々に管理されます。 アクセス制御の違いを含め、各スコープを明確に区別するために、両者をそれぞれ "課金スコープ" および "RBAC スコープ" と呼びます。

課金スコープ、RBAC スコープ、コスト管理とスコープの関係について理解するには、「[スコープを理解して使用する](understand-work-scopes.md)」を参照してください。

## <a name="manage-costs-with-partner-tenant-billing-scopes"></a>パートナー テナントの課金スコープを使ってコストを管理する

顧客を Microsoft 顧客契約にオンボードした後、次の_課金スコープ_をテナントで利用できます。 スコープを使用して Cost Management でコストを管理します。

### <a name="billing-account-scope"></a>課金アカウントのスコープ

課金アカウントのスコープを使用して、すべての顧客および課金プロファイルの課税前コストを表示します。 請求書コストは、Microsoft 顧客契約にある、顧客の使用量に基づいた製品についてのみ表示されます。 ただし、請求書コストは、Microsoft 顧客契約と CSP オファーの両方にある、購入に基づいた顧客向け製品について表示されます。 現在、スコープ内のコストを表示する既定の通貨は米ドルです。 スコープに対して設定される予算も米ドル単位です。

さまざまな顧客の請求通貨に関係なく、パートナーは課金アカウント スコープを使用して、米ドルで顧客、サブスクリプション、リソース、リソース グループ全体の予算の設定とコストの管理を行います。

またパートナーはコスト分析ビューで、顧客全体を対象に、特定の請求通貨でコストをフィルター処理します。 **[Actual cost]\(実際のコスト\)** リストを選択して、サポートされている顧客請求通貨でコストを表示します。

![実際のコストの通貨選択を示す例](./media/get-started-partners/actual-cost-selector.png)

課金スコープの[償却コスト ビュー](quick-acm-cost-analysis.md#customize-cost-views)を使用して、予約期間全体を対象に予約インスタンスの償却コストを表示します。

### <a name="billing-profile-scope"></a>課金プロファイル スコープ

請求書に含まれるすべての製品およびサブスクリプションのすべての顧客にわたって請求通貨で課税前コストを表示するには、課金プロファイル スコープを使用します。 **InvoiceID** フィルターを使用して、特定の請求書の課金プロファイルでコストをフィルター処理できます。 このフィルターは、特定の請求書の使用量コストと製品購入コストを示します。 請求書で特定の顧客のコストをフィルター処理して、課税前コストを確認することもできます。

顧客を Microsoft 顧客契約にオンボードすると、Microsoft 顧客契約にある、それらの顧客向けの全製品 (消費、購入、エンタイトルメント) のすべての料金を含む請求書を受け取ります。 同じ通貨で請求される場合、これらの請求書には、引き続き CSP オファーに含まれている顧客のエンタイトルメントと購入済み製品 (SaaS、Azure Marketplace、予約など) の料金も含まれます。

顧客請求書に対する料金の調整に役立つよう、課金プロファイル スコープを使用して、顧客に対する請求書で発生するすべてのコストを確認できます。 請求書と同様に、スコープは、新しい Microsoft 顧客契約のすべての顧客に対するコストを示します。 スコープは、現在の CSP オファーに残っている顧客エンタイトルメント製品に対するすべての料金も示します。

課金プロファイルと課金アカウントのスコープは、Azure Marketplace や予約の購入など、エンタイトルメントと購入ベースの製品の料金を示す、唯一適用可能なスコープです。

課金プロファイルは、請求書に含まれるサブスクリプションを定義します。 課金プロファイルは、エンタープライズ契約の加入契約に相当する機能です。 課金プロファイルは、請求書が生成されるスコープです。

現在、課金プロファイル スコープでコストを表示するとき、顧客の請求通貨が既定の通貨です。 課金プロファイル スコープで設定される予算は請求通貨になります。

パートナーはスコープを使用して請求書との照合を実行できます。 また、スコープを使用して、請求通貨で次の項目の予算を設定します。

- 特定のフィルター処理された請求書
- 顧客
- Subscription
- Resource group
- リソース
- Azure サービス
- 測定
- ResellerMPNID

### <a name="customer-scope"></a>顧客スコープ

パートナーはスコープを使用して、Microsoft 顧客契約にオンボードされている顧客に関連付けられたコストを管理します。 スコープにより、パートナーは特定の顧客の課税前コストを表示できます。 特定のサブスクリプション、リソース グループ、またはリソースの課税前コストをフィルター処理することもできます。

顧客スコープには、現在の CSP オファーに載っている顧客は含まれません。 このスコープには、Microsoft 顧客契約がある顧客のみが含まれます。 現在の CSP オファー顧客のエンタイトルメント コスト (Azure 使用量ではない) は、顧客フィルターを適用したときに課金アカウント スコープと課金プロファイル スコープで確認できます。

## <a name="partner-access-to-billing-scopes-in-cost-management"></a>Cost Management での課金スコープへのパートナー アクセス

**グローバル管理者**および**管理エージェント**のロールを持つユーザーのみが、パートナーの Azure テナントで直接、課金アカウント、課金プロファイル、顧客のコストを管理および表示できます。 パートナー センターのロールの詳細については、「[ユーザー ロールとアクセス許可の割り当て](/partner-center/permissions-overview)」を参照してください。

## <a name="enable-cost-management-in-the-customer-tenant"></a>顧客テナントでコスト管理を有効にする

顧客が Microsoft 顧客契約にオンボードされた後、Cost Management へのアクセスをパートナーが有効化できます。 パートナーはその後、従量課金制の小売料金で計算された顧客コストを顧客が表示することを許可するポリシーを有効化できます。 コストは顧客の請求通貨で、RBAC サブスクリプションおよびリソース グループのスコープで顧客が使用した分量に対して示されます。

コスト可視性のポリシーをパートナーが有効にすると、Azure Resource Manager でサブスクリプションにアクセスできるユーザーが、従量課金制の料金でコストを管理および分析できます。 実質的には、Azure サブスクリプションへの適切な RBAC アクセスを持つリセラーと顧客が、コストを表示できます。

パートナーがサブスクリプションとリソース グループにアクセスできる場合、ポリシーに関係なく、パートナーもコストを表示できます。

### <a name="enable-the-policy-to-view-azure-usage-charges"></a>Azure の使用料金を表示するためのポリシーを有効にする

パートナーは次の情報を使用して、顧客の Azure 使用料金を表示するためのポリシーを有効にします。

Azure portal で、パートナー テナントにサインインし、 **[Cost Management + Billing]\(コスト管理と課金\)** をクリックします。 課金アカウントを選択し、 **[Customers]\(顧客\)** をクリックします。 顧客の一覧は課金アカウントに関連付けられています。

顧客の一覧で、コストの表示を許可する顧客を選択します。

![Cost Management で顧客を選択する](./media/get-started-partners/customer-list.png)

**[設定]** で **[ポリシー]** をクリックします。

選択した顧客のサブスクリプションに関連付けられている **Azure 使用**料金について、現在のコスト可視性ポリシーが表示されます。
![従量課金制の料金を表示することを顧客に許可するポリシー](./media/get-started-partners/cost-management-billing-policies.png)

ポリシーが **[いいえ]** に設定されている場合、顧客に関連付けられているサブスクリプション ユーザーに対して Azure Cost Management は使用できません。 パートナーによって有効化されていない限り、コスト可視性ポリシーはすべてのサブスクリプション ユーザーに対して既定で無効になっています。

コスト ポリシーが **[はい]** に設定されている場合、顧客テナントに関連付けられているサブスクリプション ユーザーは、従量課金制の料金で使用料金を確認できます。

コスト可視性ポリシーが有効になっている場合、サブスクリプション使用量のあるすべてのサービスが、従量課金制の料金でコストを示します。 予約使用量は、実際のコストも償却済みコストも料金がゼロと表示されます。 購入およびエンタイトルメントは特定のサブスクリプションに関連付けられません。 そのため、サブスクリプション スコープでは購入は表示されません。

顧客テナントのコストを表示するには、[Cost Management + Billing] (コスト管理と課金) を開き、[Billing accounts] (課金アカウント) をクリックします。 課金アカウントの一覧で、課金アカウントをクリックします。

![課金アカウントを選択する](./media/get-started-partners/select-billing-account.png)

**[課金]** で **[Azure subscriptions]\(Azure サブスクリプション\)** をクリックし、顧客をクリックします。

![Azure サブスクリプションの顧客を選択する](./media/get-started-partners/subscriptions-select-customer.png)

**[コスト分析]** をクリックして、コストの確認を開始します。
コスト分析、予算、アラートは、従量課金制の料金に基づいたコストで、サブスクリプションとリソース グループの RBAC スコープで利用できます。

![顧客としてコスト分析を表示する ](./media/get-started-partners/customer-tenant-view-cost-analysis.png)

RBAC スコープの予約インスタンスの償却ビューおよび実際のコストでは、料金がゼロと表示されます。 予約インスタンスのコストは、購入が行われた課金スコープでのみ表示されます。

## <a name="analyze-costs-in-cost-analysis"></a>コスト分析でコストを分析する

パートナーは顧客間コスト分析で、特定の顧客または請求書についてコストを調査および分析できます。

使用状況の詳細ファイルと Cost Management API には、次のフィールドがあります。 コスト分析でフィルターとグループ化の機能を使用して、複数のフィールドでコストを分析できます。 フィールドの完全な一覧を表示するには、「[Cost Management のデータ フィールド](understand-cost-mgt-data.md#cost-management-data-fields)」を参照してください。

| フィールド名 | 説明 |
| --- | --- |
| CustomerTenantID | 顧客のサブスクリプションの Azure Active Directory テナントの識別子。 |
| CustomerName | 顧客のサブスクリプションの Azure Active Directory テナントの名前。 |
| CustomerTenantDomainName | 顧客のサブスクリプションの Azure Active Directory テナントのドメイン名。 |
| PartnerTenantID | パートナーの Azure Active Directory テナントの識別子。 |
| PartnerName | パートナーの Azure Active Directory テナントの名前。 |
| ResellerMPNID | サブスクリプションに関連付けられているリセラーの MPNID。 |
| costinUSD | 税引き前の、予測される ExtendedCost またはブレンド コスト (米国ドル単位)。 |
| paygCostInBillingCurrency | 価格が小売価格の場合のコストを表示します。 請求通貨で従量課金制の料金を表示します。 RBAC スコープでのみ使用できます。 |
| paygCostInUSD | 価格が小売価格の場合のコストを表示します。 従量課金制の料金を米国ドルで表示します。 RBAC スコープでのみ使用できます。 |
| partnerEarnedCreditRate | パートナー管理者リンク アクセスに基づいたパートナー獲得クレジット (PEC) がある場合に適用される割引率。 |
| partnerEarnedCreditApplied | パートナー獲得クレジットが適用されているかどうかを示します。 |

[コスト分析](quick-acm-cost-analysis.md)ビューでは、[ビューを保存](quick-acm-cost-analysis.md#saving-and-sharing-customized-views)したり、[CSV および PNG](quick-acm-cost-analysis.md#automation-and-offline-analysis) のファイルにデータをエクスポートしたりすることもできます。

## <a name="view-partner-earned-credit-pec-resource-costs"></a>パートナー獲得クレジット (PEC) リソースのコストを表示する

Azure Cost Management では、パートナーはコスト分析を使用して、PEC 特典を受けたコストを表示できます。

Azure portal で、パートナー テナントにサインインし、 **[Cost Management + Billing]\(コスト管理と課金\)** を選択します。 **[Cost Management]\(コスト管理\)** で、 **[コスト分析]** をクリックします。

[コスト分析] ビューには、パートナーの課金アカウントのコストが表示されます。 請求書を照合するために、パートナー、特定の顧客、または課金プロファイルの **[スコープ]** を必要に応じて選択します。

ドーナツ グラフで、ドロップダウン リストをクリックし、**PartnerEarnedCreditApplied** を選択して PEC コストにドリルダウンします。

![パートナー獲得クレジットの表示方法を示す例](./media/get-started-partners/cost-analysis-pec1.png)

**PartnerEarnedCreditApplied** プロパティが _True_ の場合、関連付けられたコストには、パートナーが獲得した管理者アクセスの特典があります。

**PartnerEarnedCreditApplied** プロパティが _False_ の場合、関連付けられたコストはクレジットの必要資格を満たしていません。 または、購入したサービスはパートナー獲得クレジットの対象ではありません。

通常、サービス使用状況データが Cost Management に表示されるまでに 8 ～ 24 時間かかります。 詳細については、「[使用状況データの更新頻度は一定ではない](understand-cost-mgt-data.md#usage-data-update-frequency-varies)」を参照してください。 PEC クレジットは、Azure Cost Management にアクセスしてから 48 時間以内に表示されます。


**[Group by]\(グループ化\)** オプションを使用して、**PartnerEarnedCreditApplied** プロパティによるグループ化およびフィルター処理を行うこともできます。 オプションを使用して、PEC があるコストとないコストを調べます。

![パートナー獲得クレジットによるグループ化またはフィルター](./media/get-started-partners/cost-analysis-pec2.png)

## <a name="cost-management-rest-apis"></a>Cost Management REST API

パートナーと顧客は、次のセクションに記載されている Cost Management API を一般的なタスクに対して使用できます。

### <a name="azure-cost-management-apis---direct-and-indirect-providers"></a>Azure Cost Management API - 直接プロバイダーと間接プロバイダー

パートナー テナントの課金スコープにアクセスできるパートナーは、次の API を使用して請求されたコストを表示できます。

パートナーは、サブスクリプションにアクセスできる場合、コスト ポリシーに関係なく、サブスクリプション スコープの API を呼び出すことができます。 サブスクリプションにアクセスできる他のユーザー (顧客やリセラーなど) は、パートナーが顧客テナントのコスト ポリシーを有効にした後でのみ、API を呼び出すことができます。


#### <a name="to-get-a-list-of-billing-accounts"></a>課金アカウントの一覧を取得するには

```
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts?api-version=2019-10-01-preview
```

#### <a name="to-get-a-list-of-customers"></a>顧客の一覧を取得するには

```
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/customers?api-version=2019-10-01-preview
```

#### <a name="to-get-a-list-of-subscriptions"></a>サブスクリプションの一覧を取得するには

```
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/billingSubscriptions?api-version=2019-10-01-preview
```

#### <a name="to-get-a-list-of-invoices-for-a-period-of-time"></a>一定期間の請求書の一覧を取得するには

```
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/invoices?api-version=2019-10-01-preview&periodStartDate={periodStartDate}&periodEndDate={periodEndDate}
```

この API 呼び出しは、次の JSON コードのような要素を持つ一連の請求書を返します。

```
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/{billingAccountID}/billingProfiles/{BillingProfileID}/invoices/{InvoiceID}",
      "name": "{InvoiceID}",
      "properties": {
        "amountDue": {
          "currency": "USD",
          "value": x.xx
        },
        ...
    }
```

返された前の ID フィールドの値を使用し、次の例で使用状況の詳細のクエリを実行するためのスコープとして置き換えます。

```
GET https://management.azure.com/{id}/providers/Microsoft.Consumption/UsageDetails?api-version=2019-10-01
```

この例は、特定の請求書に関連付けられた使用状況レコードを返します。


#### <a name="to-get-the-policy-for-customers-to-view-costs"></a>顧客がコストを表示するためのポリシーを取得するには

```
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/customers/{customerID}/policies/default?api-version=2019-10-01-preview
```

#### <a name="to-set-the-policy-for-customers-to-view-costs"></a>顧客がコストを表示するためのポリシーを設定するには

```
PUT https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/customers/{customerID}/policies/default?api-version=2019-10-01-preview
```

#### <a name="to-get-azure-service-usage-for-a-billing-account"></a>課金アカウントの Azure サービスの使用状況を取得するには

```
GET https://management.azure.com/providers/Microsoft.Billing/BillingAccounts/{billingAccountName}/providers/Microsoft.Consumption/usageDetails?api-version=2019-10-01
```

#### <a name="to-download-a-customers-azure-service-usage"></a>顧客の Azure サービスの使用状況をダウンロードするには

次の GET 呼び出しは非同期操作です。

```
GET https://management.azure.com/Microsoft.Billing/billingAccounts/{billingAccountName}/customers/{customerID}/providers/Microsoft.Consumption/usageDetails/download?api-version=2019-10-01 -verbose
```

応答で返された `Location` URI を呼び出して、操作の状態を確認します。 状態が *[完了]* の場合、`downloadUrl` プロパティには、生成されたレポートをダウンロードするために使用できるリンクが含まれています。


#### <a name="to-get-or-download-the-price-sheet-for-consumed-azure-services"></a>使用された Azure サービスの価格シートを取得またはダウンロードするには

まず、次の POST を使用します。

```
POST https://management.azure.com/providers/Microsoft.Billing/BillingAccounts/{billingAccountName}/billingProfiles/{billingProfileID}/pricesheet/default/download?api-version=2019-10-01-preview&format=csv" -verbose
```

次に、非同期操作プロパティの値を呼び出します。 例:

```
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/billingProfiles/{billingProfileID}/pricesheetDownloadOperations/{operation}?sessiontoken=0:11186&api-version=2019-10-01-preview
```
前の GET 呼び出しでは、価格シートを含むダウンロード リンクが返されます。


#### <a name="to-get-aggregated-costs"></a>集計コストを取得するには

```
POST https://management.azure.com/providers/microsoft.billing/billingAccounts/{billingAccountName}/providers/microsoft.costmanagement/query?api-version=2019-10-01
```

#### <a name="create-a-budget-for-a-partner"></a>パートナーの予算を作成する

```
PUT https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/providers/Microsoft.CostManagement/budgets/partnerworkshopbudget?api-version=2019-10-01
```

#### <a name="create-a-budget-for-a-customer"></a>顧客の予算を作成する

```
PUT https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/customers/{customerID}/providers/Microsoft.Consumption/budgets/{budgetName}?api-version=2019-10-01
```

#### <a name="delete-a-budget"></a>予算を削除する

```
PUT
https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/providers/Microsoft.CostManagement/budgets/{budgetName}?api-version=2019-10-01
```


## <a name="next-steps"></a>次の手順
- Cost Management で[コストの分析を開始する](quick-acm-cost-analysis.md)
- Cost Management で[予算を作成および管理する](tutorial-acm-create-budgets.md)
