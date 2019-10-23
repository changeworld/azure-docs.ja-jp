---
title: パートナー向け Azure Cost Management の利用を開始する
description: この記事では、パートナーが Azure Cost Management の機能を使用する方法と、パートナーの顧客のために Cost Management アクセスを有効にする方法について説明します。
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 10/14/2019
ms.topic: conceptual
ms.service: cost-management
manager: aparnag
ms.custom: secdec18
ms.openlocfilehash: 9d95e23cf92c7ee98291831d60088d610c3e5c52
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2019
ms.locfileid: "72377432"
---
# <a name="get-started-with-azure-cost-management-for-partners"></a>パートナー向け Azure Cost Management の利用を開始する

Azure Cost Management は、Microsoft 顧客契約に顧客をオンボードしたパートナーがネイティブで利用できます。 この記事では、パートナーが [Azure Cost Management](https://docs.microsoft.com/azure/cost-management/) の機能を使用する方法について説明します。 また、パートナーが顧客のために Cost Management アクセスを有効にする方法についても説明します。 CSP のお客様は、CSP パートナーによって有効化された場合は Cost Management の機能を使用できます。

CSP パートナーは、次の目的に Cost Management を使用します。

- 請求済みコストを把握し、コストを顧客、サブスクリプション、リソース グループ、およびサービスに関連付けます。
- [コスト分析](quick-acm-cost-analysis.md)で、Azure のコストを直感的に把握します。顧客、サブスクリプション、リソース グループ、リソース、メーター、サービス、その他多数のディメンションによってコストを分析する機能を備えています。
- コスト分析で、パートナー獲得クレジット (PEC) が適用されているリソース コストを表示します。
- プログラムによる[予算](tutorial-acm-create-budgets.md)と、コストが予算を超過したときのアラートを使用して、通知と自動化を設定します。
- Cost Management データへのアクセスを顧客に提供する Azure Resource Manager ポリシーを有効にします。 顧客はその後、[従量課金制料金](https://azure.microsoft.com/pricing/calculator/)を使用するサブスクリプションの使用量コスト データを表示できます。

Azure Cost Management で使用できるすべての機能は、REST API でも使用できます。 API を使用してコスト管理タスクを自動化します。

## <a name="prerequisites"></a>前提条件

Azure Cost Management には、課金アカウントまたはサブスクリプションへの読み取りアクセスが必要です。 課金アカウントまたは管理グループから、アプリを管理する個々のリソース グループまで、リソースよりも上の任意のレベルでアクセスを許可できます。 サブスクリプション ユーザーが価格とコストを確認するためには、料金を表示するためのアクセス権を課金アカウントに対して有効にする必要があります。 Azure Cost Management に対するアクセス権の有効化と割り当ての詳細については、[データへのアクセスの割り当て](assign-access-acm-data.md)に関するページを参照してください。 サポートされているアカウントの種類の完全な一覧については、「[Cost Management のデータを理解する](understand-cost-mgt-data.md)」を参照してください。


## <a name="how-cost-management-uses-scopes"></a>Cost Management でのスコープの使用方法

スコープは、お客様が課金データの管理、請求書の表示、およびアカウント全般の管理を行う場所であり、支払い専用のロールを持ちます。 課金とアカウントに関するロールは、RBAC を使用するリソース管理用のスコープとは別々に管理されます。 アクセス制御の違いを含め、各スコープを明確に区別するために、両者をそれぞれ "課金スコープ" および "RBAC スコープ" と呼びます。

課金スコープ、RBAC スコープ、コスト管理とスコープの関係について理解するには、「[スコープを理解して使用する](understand-work-scopes.md)」を参照してください。

## <a name="manage-costs-with-partner-tenant-billing-scopes"></a>パートナー テナントの課金スコープを使ってコストを管理する

顧客を Microsoft 顧客契約にオンボードした後、次の_課金スコープ_をテナントで利用できます。 スコープを使用して Cost Management でコストを管理します。

### <a name="billing-account-scope"></a>課金アカウントのスコープ

課金アカウントのスコープを使用して、すべての顧客および課金プロファイルの課税前コストを表示します。 また、Microsoft 顧客契約にある、使用量に基づいた顧客向け製品の請求書コストを表示することもできます。 請求書コストは、Microsoft 顧客契約および CSP オファーにある、購入に基づいた顧客向け製品についても表示されます。 現在、スコープ内のコストを表示する既定の通貨は米ドルです。 スコープに対して設定される予算も米ドル単位です。

顧客の請求通貨にかかわらず、パートナーはスコープを使用して、顧客、サブスクリプション、リソース、リソース グループにまたがって、米ドルで予算を設定し、コストを管理します。

またパートナーはコスト分析ビューで、顧客全体を対象に、特定の請求通貨でコストをフィルター処理します。 **[Actual cost]\(実際のコスト\)** リストを選択して、サポートされている顧客請求通貨でコストを表示します。

![実際のコストの通貨選択を示す例](./media/get-started-partners/actual-cost-selector.png)

課金スコープの[償却コスト ビュー](quick-acm-cost-analysis.md#customize-cost-views)を使用して、予約期間全体を対象に予約インスタンスの償却コストを表示します。

### <a name="billing-profile-scope"></a>課金プロファイル スコープ

請求書に含まれるすべての製品およびサブスクリプションのすべての顧客にわたって請求通貨で課税前コストを表示するには、課金プロファイル スコープを使用します。 **InvoiceID** フィルターを使用して、特定の請求書の課金プロファイルでコストをフィルター処理できます。 このフィルターは、特定の請求書の使用量コストと製品購入コストを示します。 請求書で特定の顧客のコストをフィルター処理して、課税前コストを確認することもできます。

Microsoft 顧客契約に顧客をオンボードした後、パートナーは、SaaS、Azure Marketplace、予約などのエンタイトルメントおよび購入済み製品の料金を示す顧客請求書を受け取ります。 同じ請求通貨で請求される場合、新しい Microsoft 顧客契約に含まれていない顧客料金も請求書に示されます。

顧客請求書に対する料金の調整に役立つよう、課金プロファイル スコープを使用して、顧客に対する請求書で発生するすべてのコストを確認できます。 請求書と同様に、スコープは、新しい Microsoft 顧客契約のすべての顧客に対するコストを示します。 スコープは、現在の CSP オファーに残っている顧客エンタイトルメント製品に対するすべての料金も示します。

エンタイトルメントおよび購入ベースの製品の料金を示すスコープは、課金プロファイル スコープと課金アカウント スコープだけです。

課金プロファイルは、請求書に含まれるサブスクリプションを定義します。 課金プロファイルは、エンタープライズ契約の加入契約に相当する機能です。 加入契約は、請求書が生成されるスコープです。 同様に、Azure Marketplace や予約など、使用量ベースではない購入は課金プロファイル スコープでのみ表示されます。

現在、課金プロファイル スコープでコストを表示するとき、顧客の請求通貨が既定の通貨です。 課金プロファイル スコープで設定される予算の単位は請求通貨です。

パートナーはスコープを使用して請求書との照合を実行できます。 また、スコープを使用して、請求通貨で次の予算を設定します。

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

顧客スコープには、現在の CSP オファーに載っている顧客は含まれません。 現在の CSP オファー顧客のエンタイトルメント コスト (Azure 使用量ではない) は、顧客フィルターを適用したときに課金アカウント スコープと課金プロファイル スコープで確認できます。

## <a name="partner-access-to-billing-scopes-in-cost-management"></a>Cost Management での課金スコープへのパートナー アクセス

**グローバル管理者**および**管理エージェント**のロールを持つユーザーのみが、パートナーの Azure テナントで直接、課金アカウント、課金プロファイル、顧客のコストを管理および表示できます。 パートナー センターのロールの詳細については、「[ユーザー ロールとアクセス許可の割り当て](/partner-center/permissions-overview)」を参照してください。

### <a name="enable-cost-management-in-the-customer-tenant"></a>顧客テナントでコスト管理を有効にする

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
サブスクリプションとリソース グループの RBAC スコープに対して、従量課金制の料金に基づいたコストで、コスト分析、予算、アラートが使用できるようになりました。

![顧客としてコスト分析を表示する ](./media/get-started-partners/customer-tenant-view-cost-analysis.png)

RBAC スコープの予約インスタンスの償却ビューおよび実際のコストでは、料金がゼロと表示されます。 予約インスタンスのコストは、購入が行われた課金スコープでのみ表示されます。

## <a name="analyze-costs-in-cost-analysis"></a>Cost Analysis でコストを分析する

パートナーは顧客間コスト分析で、特定の顧客または請求書についてコストを調査および分析できます。 フィルターとグループ化の機能を使用すると、次のような複数のフィールドによってコストを分析できます。

| **フィールド** | **説明** | **パートナー センターで相当する列** |
| --- | --- | --- |
| PartnerTenantID | パートナーの Azure Active Directory テナントの識別子 | パートナー ID と呼ばれるパートナーの Azure Active Directory TenantID。 GUID 形式。 |
| PartnerName | パートナーの Azure Active Directory テナントの名前 | パートナー名 |
| CustomerTenantID | 顧客のサブスクリプションの Azure Active Directory テナントの識別子 | 顧客の組織 ID。 たとえば、顧客の Azure Active Directory TenantID。 |
| CustomerName | 顧客のサブスクリプションを含む Azure Active Directory テナントの名前 | パートナー センターで報告される顧客の組織名。 システムの情報を使って請求書を調整するために重要です。 |
| ResellerMPNID | サブスクリプションに関連付けられているリセラーの MPNID | サブスクリプションの登録のあるリセラーの MPN ID。 現在のアクティビティでは使用できません。 |
| サブスクリプション ID | Microsoft が生成する、Azure サブスクリプションの一意識別子 | 該当なし |
| subscriptionName | Azure サブスクリプションの名前 | 該当なし |
| billingProfileID | 課金プロファイルの識別子。 請求書全体で、顧客全体の 1 つの請求通貨でコストをグループ化します。 | MCAPI パートナー課金グループ ID。 API 要求で使用しますが、応答には含まれません。 |
| invoiceID | 特定の取引が含まれる請求書の請求書 ID | 指定されたトランザクションが含まれている請求書番号。 |
| resourceGroup | Azure リソース グループの名前。 リソースのライフサイクル管理に使用されます。 | リソース グループの名前。 |
| partnerEarnedCreditRate | パートナー管理者リンク アクセスに基づいたパートナー獲得クレジット (PEC) がある場合に適用される割引率。 | パートナー獲得クレジット (PEC) の割合。 たとえば、0% または 15%。 |
| partnerEarnedCreditApplied | パートナー獲得クレジットが適用されたかどうかを示します。 | 該当なし |

[コスト分析](quick-acm-cost-analysis.md)ビューでは、[ビューを保存](quick-acm-cost-analysis.md#saving-and-sharing-customized-views)したり、データを [CSV および PNG](quick-acm-cost-analysis.md#automation-and-offline-analysis) ファイルにエクスポートすることもできます。

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

パートナー、間接プロバイダー、および顧客は、以下のセクションで説明する Cost Management API を一般的なタスクに使用できます。

### <a name="azure-cost-management-apis-for-partners"></a>パートナー向けの Azure Cost Management API

パートナー テナントの課金スコープにアクセスできるパートナーおよびユーザーは、次の API を使用できます。

#### <a name="to-get-a-list-of-billing-accounts"></a>課金アカウントの一覧を取得するには

```
armclient get "providers/Microsoft.billing/billingAccounts?api-version=2019-10-01-preview"
```

#### <a name="to-get-a-list-of-customers"></a>顧客の一覧を取得するには

```
armclient get "providers/Microsoft.billing/billingAccounts/99a13315-2f87-5b46-9dbd-606071106352:1d100e69-2833-4677-a5d4-8ad35035d9a3_2019-05-31/customers?api-version=2019-10-01-preview"
```
#### <a name="to-get-a-list-of-subscriptions"></a>サブスクリプションの一覧を取得するには

```
armclient get "/providers/Microsoft.Billing/billingAccounts/99a13315-2f87-5b46-9dbd-606071106352:1d100e69-2833-4677-a5d4-8ad35035d9a3_2019-05-31/customers/9553eda2-2bd7-4ae6-a1f8-6a19eb40be22/billingSubscriptions?api-version=2019-10-01-preview"
```

#### <a name="to-create-new-subscription"></a>新しいサブスクリプションを作成するには

```
armclient post "/providers/Microsoft.Billing/billingAccounts/99a13315-2f87-5b46-9dbd-606071106352:1d100e69-2833-4677-a5d4-8ad35035d9a3_2019-05-31/customers/9553eda2-2bd7-4ae6-a1f8-6a19eb40be22/providers/Microsoft.Subscription/createSubscription?api-version=2018-11-01-preview" @createsub.json -verbose
```

#### <a name="to-get-or-download-usage-for-azure-services"></a>Azure サービスの使用状況を取得またはダウンロードするには

```
armclient GET /providers/Microsoft.Billing/BillingAccounts/99a13315-2f87-5b46-9dbd-606071106352:1d100e69-2833-4677-a5d4-8ad35035d9a3_2019-05-31/providers/Microsoft.Consumption/usageDetails?api-version=2019-10-01
```

#### <a name="to-get-a-list-of-billing-profiles"></a>課金プロファイルの一覧を取得するには

```
armclient get "providers/Microsoft.Billing/billingAccounts/99a13315-2f87-5b46-9dbd-606071106352:1d100e69-2833-4677-a5d4-8ad35035d9a3_2019-05-31/billingProfiles?api-version=2019-10-01-preview
```

#### <a name="to-get-or-download-the-price-sheet-for-consumed-azure-services"></a>使用された Azure サービスの価格シートを取得またはダウンロードするには

```
armclient post "/providers/Microsoft.Billing/BillingAccounts/99a13315-2f87-5b46-9dbd-606071106352:1d100e69-2833-4677-a5d4-8ad35035d9a3_2019-05-31/BillingProfiles/JUT6-EU3Q-BG7-TGB/pricesheet/default/download?api-version=2019-10-01-preview&format=csv" -verbose
```

#### <a name="to-get-customer-costs-for-the-last-two-months-sorted-by-month"></a>過去 2 か月間の顧客コストを月別に並べ替えて取得するには

```
armclient post providers/microsoft.billing/billingAccounts/99a13315-2f87-5b46-9dbd-606071106352:1d100e69-2833-4677-a5d4-8ad35035d9a3_2019-05-31//providers/microsoft.costmanagement/query?api-version=2019-10-01 @CCMQueryCustomer.json
```

#### <a name="to-get-azure-subscription-costs-for-the-last-two-months-sorted-by-month"></a>過去 2 か月間の Azure サブスクリプションを月別に並べ替えて取得するには

```
armclient post providers/microsoft.billing/billingAccounts/99a13315-2f87-5b46-9dbd-606071106352:1d100e69-2833-4677-a5d4-8ad35035d9a3_2019-05-31//providers/microsoft.costmanagement/query?api-version=2019-10-01 @CCMQuerySubscription.json
```

#### <a name="to-get-daily-costs-for-the-current-month"></a>当月の日次コストを取得するには

```
armclient post providers/microsoft.billing/billingAccounts/99a13315-2f87-5b46-9dbd-606071106352:1d100e69-2833-4677-a5d4-8ad35035d9a3_2019-05-31//providers/microsoft.costmanagement/query?api-version=2019-10-01 @CCMQueryDaily.json
```

#### <a name="to-get-the-policy-for-customers-to-view-costs"></a>顧客がコストを表示するためのポリシーを取得するには

```
armclient get "providers/Microsoft.Billing/billingAccounts/99a13315-2f87-5b46-9dbd-606071106352:1d100e69-2833-4677-a5d4-8ad35035d9a3_2019-05-31/customers/9553eda2-2bd7-4ae6-a1f8-6a19eb40be22/policies/default?api-version=2019-10-01-preview"
```

#### <a name="to-set-the-policy-for-customers-to-view-costs"></a>顧客がコストを表示するためのポリシーを設定するには

```
armclient put "providers/Microsoft.Billing/billingAccounts/99a13315-2f87-5b46-9dbd-606071106352:1d100e69-2833-4677-a5d4-8ad35035d9a3_2019-05-31/customers/9553eda2-2bd7-4ae6-a1f8-6a19eb40be22/policies/default?api-version=2019-10-01-preview" @policy.json
```

### <a name="azure-cost-management-apis-for-indirect-providers"></a>間接プロバイダー向けの Azure Cost Management API

顧客テナントの RBAC スコープにアクセスできる間接プロバイダーは、次の API を使用できます。 開始するには、ユーザーとして、またはサービス プリンシパルを使用してログインします。

#### <a name="to-get-the-billing-account-information"></a>課金アカウント情報を取得するには

```
armclient get "providers/Microsoft.billing/billingAccounts?api-version=2019-10-01-preview"
```

#### <a name="to-get-a-list-of-customers"></a>顧客の一覧を取得するには

```
armclient get "providers/Microsoft.billing/billingAccounts/ec1b88ba-5681-517e-f657-4cc6a4a407cb:52f143a9-6524-4e5e-9d4a-120c7a79ca65_2019-05-31/customers?api-version=2019-10-01-preview"
```

#### <a name="to-get-a-list-of-resellers-associated-with-the-customer"></a>顧客に関連付けられているリセラーの一覧を取得するには

```
armclient get "/providers/Microsoft.Billing/billingAccounts/ec1b88ba-5681-517e-f657-4cc6a4a407cb:52f143a9-6524-4e5e-9d4a-120c7a79ca65_2019-05-31/customers/b51df1fa-62fa-4c92-9a74-fe860016d4db?api-version=2019-10-01-preview&$expand=resellers
```

#### <a name="to-get-a-list-of-subscriptions-with-reseller-information"></a>リセラー情報と共にサブスクリプションの一覧を取得するには

```
armclient get "/providers/Microsoft.Billing/billingAccounts/ec1b88ba-5681-517e-f657-4cc6a4a407cb:52f143a9-6524-4e5e-9d4a-120c7a79ca65_2019-05-31/customers/b51df1fa-62fa-4c92-9a74-fe860016d4db/billingSubscriptions?api-version=2019-10-01-preview
```

#### <a name="to-create-a-subscription"></a>サブスクリプションを作成するには

```
armclient post "/providers/Microsoft.Billing/billingAccounts/ec1b88ba-5681-517e-f657-4cc6a4a407cb:52f143a9-6524-4e5e-9d4a-120c7a79ca65_2019-05-31/customers/b51df1fa-62fa-4c92-9a74-fe860016d4db/providers/Microsoft.Subscription/createSubscription?api-version=2018-11-01-preview" @createsub_reseller.json
```

### <a name="azure-cost-management-apis-for-customers"></a>顧客向けの Azure Cost Management API

顧客は次の情報を使用して API にアクセスします。 開始するには、ユーザーとしてログインします。

#### <a name="to-get-or-download-azure-consumption-usage-information-with-retail-rates"></a>Azure 使用量情報を小売料金と共に取得またはダウンロードするには

```
armclient post /subscriptions/66bada28-271e-4b7a-aaf5-c0ead63923d7/providers/microsoft.costmanagement/query?api-version=2019-10-01 @CCMQueryDaily.json
```

## <a name="next-steps"></a>次の手順
- Cost Management で[コストの分析を開始する](quick-acm-cost-analysis.md)
- Cost Management で[予算を作成および管理する](tutorial-acm-create-budgets.md)
