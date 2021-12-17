---
title: 更新された Azure 課金アカウントの使用を開始する
description: 更新された Azure 課金アカウントの使用を開始して、新しい課金とコスト管理のエクスペリエンスでの変更を把握します
author: bandersmsft
ms.reviewer: amberbhargava
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 10/07/2021
ms.author: banders
ms.openlocfilehash: 07c8b711ae32311cceba0c6ebe3353974b867356
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/09/2021
ms.locfileid: "132061779"
---
# <a name="get-started-with-your-updated-azure-billing-account"></a>更新された Azure 課金アカウントの使用を開始する

コストと請求書の管理は、クラウド エクスペリエンスの主要なコンポーネントの 1 つです。 これは、クラウドに関する支出を管理し、理解するのに役立ちます。 Microsoft では、お客様がコストと請求書をより簡単に管理できるように、お客様の Azure 課金アカウントを更新して、コスト管理と課金の機能を強化しています。 この記事では、課金アカウントの更新について説明し、新機能について紹介します。

> [!IMPORTANT]
> お使いのアカウントは、Microsoft からアカウントの更新について通知するメールを受信すると更新されます。 この通知は、お使いのアカウントが更新される 60 日前に送信されます。

## <a name="more-flexibility-with-your-new-billing-account"></a>新しい課金アカウントによる柔軟性の向上

次の図では、お使いの古い課金アカウントと新しい課金アカウントを比較しています。

:::image type="content" source="./media/mosp-new-customer-experience/comparison-old-new-account.png" alt-text="古いアカウントと新しいアカウントの課金階層の比較を示す図。" border="false" lightbox="./media/mosp-new-customer-experience/comparison-old-new-account.png":::

新しい課金アカウントには、請求書と支払い方法を管理できるようにする課金プロファイルが 1 つ以上含まれています。 各課金プロファイルには、課金プロファイルの請求書上でコストを整理できるようにする、1 つまたは複数の請求書セクションが含まれます。

:::image type="content" source="./media/mosp-new-customer-experience/new-billing-account-hierarchy.png" alt-text="新しい課金階層を示す図。" border="false" lightbox="./media/mosp-new-customer-experience/new-billing-account-hierarchy.png":::

請求先アカウント上のロールは、最高レベルのアクセス許可を持っています。 これらのロールは、組織の財務や IT マネージャーなどのアカウント全体の請求書を表示し、コストを追跡する必要のあるユーザー、またはアカウントにサインアップした個人に割り当てる必要があります。 詳細については、[請求先アカウントのロールとタスク](../manage/understand-mca-roles.md#billing-account-roles-and-tasks)に関するページを参照してください。 お使いのアカウントが更新されると、古い課金アカウントでアカウント管理者ロールが割り当てられていたユーザーには、新しいアカウントの所有者ロールが付与されます。

## <a name="billing-profiles"></a>課金プロファイル

課金プロファイルは、請求書と支払い方法を管理するために使用されます。 月の初めに、アカウントの各課金プロファイルの月次請求書が生成されます。 この請求書には、課金プロファイルに関連付けられているすべてのサブスクリプションについて、前月からのそれぞれの料金が含まれています。

お使いのアカウントが更新されると、課金プロファイルがサブスクリプションごとに自動的に作成されます。 サブスクリプションの料金は、それぞれの課金プロファイルに課金され、その請求書に表示されます。

課金プロファイルでのロールには、請求書と支払方法を表示および管理するアクセス許可があります。 これらのロールは、組織内の会計チームのメンバーなど、請求書の支払いを行うユーザーに割り当てる必要があります。 詳細については、[請求先アカウントのロールとタスク](../manage/understand-mca-roles.md#billing-profile-roles-and-tasks)に関するページを参照してください。

お使いのアカウントが更新されると、[請求書を表示](download-azure-invoice.md#allow-others-to-download-your-subscription-invoice)するアクセス許可を他のユーザーに付与している各サブスクリプションでは、所有者、共同作成者、閲覧者、または課金データ閲覧者の Azure ロールを持つユーザーに、それぞれの課金プロファイルの閲覧者ロールが付与されます。

## <a name="invoice-sections"></a>請求書セクション

請求書セクションは、請求書のコストを整理するために使用されます。 たとえば、必要なのは 1 つの請求書でも、部門、チーム、またはプロジェクト別にコストを整理したいとします。 このシナリオの場合、部門、チーム、またはプロジェクトごとに請求書セクションを作成する単一の課金プロファイルが与えられます。

お使いのアカウントが更新されると、課金プロファイルごとに請求書セクションが作成され、関連するサブスクリプションがその請求書セクションに割り当てられます。 サブスクリプションをさらに追加する場合は、より多くのセクションを作成し、サブスクリプションを請求書セクションに割り当てることができます。 これらのセクションは、課金プロファイルの請求書に表示され、それらに割り当てた各サブスクリプションの使用状況を反映します。

請求書セクションでのロールは、Azure サブスクリプションを作成するユーザーを制御するアクセス許可を持ちます。 これらのロールは、エンジニアリング リードや技術的アーキテクトなど、組織内のチームの Azure 環境を設定するユーザーに割り当てる必要があります。 詳細については、[請求書セクションのロールとタスク](../manage/understand-mca-roles.md#invoice-section-roles-and-tasks)に関するページを参照してください。

## <a name="enhanced-features-in-your-new-experience"></a>新しいエクスペリエンスで強化された機能

新しいエクスペリエンスには、コストと請求書の管理を容易にする、次のコスト管理と課金の機能が含まれています。

#### <a name="invoice-management"></a>請求書管理

**より予測可能な月単位の請求期間** - 新しいアカウントでは、Azure を使用するためにいつサインアップしたかに関係なく、請求期間はその月の 1 日から始まり、その月の末日に終了します。 請求書は、毎月初めに生成され、前月のすべての料金を含みます。

**複数のサブスクリプションに対して 1 つの月次請求書を作成する** - 既存のアカウントでは、Azure サブスクリプションごとに請求書が作成されます。 この既存の動作はアカウントの更新後も維持されますが、サブスクリプションの請求金額を 1 つの請求書に統合することができます。 請求金額を 1 つの請求書に統合するには、アカウントの更新後、次の手順に従ってください。

1. [Azure portal](https://portal.azure.com) にサインインします。
2. "**コスト管理 + 請求**" を検索します。  
   ![Azure portal での [コストの管理と請求] の検索を示すスクリーンショット。](./media/mosp-new-customer-experience/billing-search-cost-management-billing.png)
3. 画面の左側から **[Azure サブスクリプション]** を選択します。 
4. 支払い対象の Azure サブスクリプションが表に一覧表示されます。 [課金プロファイル] 列には、サブスクリプションの請求先となる課金プロファイルが表示されます。 サブスクリプション料は、課金プロファイルの請求書に表示されます。 すべてのサブスクリプションの請求金額を 1 つの請求書に統合するには、すべてのサブスクリプションを 1 つの課金プロファイルにリンクする必要があります。  
    :::image type="content" source="./media/mosp-new-customer-experience/list-azure-subscriptions.png" alt-text="Azure サブスクリプションが一覧表示される画面のスクリーンショット。" lightbox="./media/mosp-new-customer-experience/list-azure-subscriptions.png" :::
5. 使用したい課金プロファイルを選択します。 
6. 手順 5. で選択した課金プロファイルにリンクされていないサブスクリプションを選択します。 サブスクリプションの省略記号 (3 つのドット) をクリックします。 **[請求書セクションの変更]** を選択します。  
    :::image type="content" source="./media/mosp-new-customer-experience/select-change-invoice-section.png" alt-text="請求書セクションの変更オプションの場所を示すスクリーンショット。" lightbox="./media/mosp-new-customer-experience/select-change-invoice-section-zoomed-in.png" :::
7. 手順 5. で選択した課金プロファイルを選択します。  
    :::image type="content" source="./media/mosp-new-customer-experience/change-invoice-section.png" alt-text="請求書セクションの変更方法を示すスクリーンショット。" lightbox="./media/mosp-new-customer-experience/change-invoice-section-zoomed-in.png" :::
8. **[変更]** を選択します。
9. その他すべてのサブスクリプションについて、手順 6. から手順 8. を繰り返します。 

**Azure サブスクリプション、サポート プラン、Azure Marketplace 製品に関する 1 つの月次請求書を受け取る** - Azure サブスクリプションの使用料金、サポート プラン、Azure Marketplace での購入を含むすべての料金に対して 1 つの月次請求書が生成されます。

**ニーズに基づいて請求書のコストをグループ化する** - ニーズに基づいて、部門、プロジェクト、またはチーム別に請求書のコストをグループ化することができます。

**請求書にオプションの発注番号を設定する** - 請求書を内部財務システムに関連付けるために、発注番号を設定します。 これは、Azure portal でいつでも管理および更新できます。

#### <a name="payment-management"></a>支払い管理

**クレジット カードを使用して請求書に対する支払いをすぐに行う** - 自動支払いがクレジット カードに請求されるのを待つ必要はありません。 Azure portal では、任意のクレジット カードを使用して、期日が迫っている請求書または期日を過ぎた請求書の支払いを行うことができます。

**アカウントに適用されているすべての支払いを追跡する** - Azure portal では、使用されている支払い方法、支払済み金額、支払い日など、お使いのアカウントに適用されたすべての支払いが表示されます。

#### <a name="cost-management"></a>コスト管理

**ストレージ アカウントへの使用状況データの毎月のエクスポートをスケジュールする** - コストと使用状況のデータをストレージ アカウントに毎日、毎週、または毎月自動的に発行します。

#### <a name="account-and-subscription-management"></a>アカウントとサブスクリプションの管理

**課金操作を実行する管理者を複数割り当てる** - 自分のアカウントに対する課金を管理するために、複数のユーザーに課金のアクセス許可を割り当てます。 他のユーザーに読み取り、書き込み、またはその両方のアクセス許可を付与することで柔軟性が得られます。

**より多くのサブスクリプションを Azure portal で直接作成する** - Azure portal で 1 回選択するだけですべてのサブスクリプションを作成します。

#### <a name="api-support"></a>API のサポート (API support)

**API、SDK、PowerShell を使用して課金およびコスト管理操作を実行する** - Cost Management、Billing、および Consumption API シリーズを使用して、お使いのデータ分析ツールに課金とコストのデータをプルします。

**API、SDK、PowerShell を使用してすべてのサブスクリプション操作を実行する** - Azure Subscription API を使用して、サブスクリプションの作成、名前変更、キャンセルなど、Azure サブスクリプションの管理を自動化します。

## <a name="get-prepared-for-your-new-experience"></a>新しいエクスペリエンスに備える

新しいエクスペリエンスに備えるために、次のことをお勧めします。

**月単位の請求期間と異なる請求日**

新しいエクスペリエンスでは、請求書は毎月 9 日ごろに生成され、前月のすべての料金が含まれます。 この日付は、古いアカウントで請求書が生成される日付と異なる場合があります。 請求書を他のユーザーと共有する場合は、この日付の変更について通知してください。


**移行後の最初の月の請求書**

自分のアカウントが更新されると、その日に、既にある未請求料金が確定し、通常の請求書受領日にそれらの料金の請求書が届きます。 たとえば、請求期間がその月の 5 日から翌月の 4 日までの Azure sub 01 と、請求期間がその月の 10 日から翌月の 9 日までの Azure sub 02 という 2 つの Azure サブスクリプションを John が所有しているとします。 John は通常、その月の 5 日に、両方の Azure サブスクリプションの請求書を受領します。 ここでもし John のアカウントが 4 月 4 日に更新された場合、3 月 5 日から 4 月 4 日までの Azure sub 01 の料金と、3 月 10 日から 4 月 4 日までの Azure sub 02 の料金が確定します。 John は 2 つの請求書 (サブスクリプションごとに 1 つ) を 4 月 5 日に受領することになります。 アカウントの更新後、John の請求期間はカレンダー月が基準となり、カレンダー月の初日からそのカレンダー月の末日までに発生したすべての料金が対象となります。 前カレンダー月の料金の請求書は、各月の最初の数日の間に入手可能となります。 そのため上の例で言えば、John が次回、請求書を受領するのは 5 月の初めで、その請求期間は 4 月 5 日から 4 月 30 日までとなります。


**新しい Billing API シリーズと Cost Management API シリーズ**

Cost Management または Billing API シリーズを使用して課金またはコストのデータを照会および更新する場合は、新しい API を使用する必要があります。 次の表に、新しい課金アカウントで動作しない API と、新しい課金アカウントで行う必要がある変更を示します。

|API | [変更点]  |
|---------|---------|
|[Billing Accounts - List](/rest/api/billing/2019-10-01-preview/billingaccounts/list) | Billing Accounts - List API では、古い課金アカウントの agreementType は **MicrosoftOnlineServiceProgram** でしたが、新しい課金アカウントの agreementType は **MicrosoftCustomerAgreement** になります。 agreementType に依存している場合は、それを更新してください。 |
|[Invoices - List By Billing Subscription](/rest/api/billing/2019-10-01-preview/invoices/listbybillingsubscription)     | この API を使用すると、お使いのアカウントが更新される前に生成された請求書のみが返されます。 新しい課金アカウントで生成される請求書を取得するには、[Invoices - List By Billing Account](/rest/api/billing/2019-10-01-preview/invoices/listbybillingaccount) API を使用する必要があります。 |


## <a name="cost-management-updates-after-account-update"></a>アカウントの更新後の Cost Management の更新

Microsoft 顧客契約の更新された Azure 課金アカウントでは、Azure portal で、従量課金制アカウントでは使用できなかった Cost Management の新規および拡張エクスペリエンスにアクセスできます。

### <a name="new-capabilities"></a>新機能

Azure 課金アカウントでは、以下の新機能を使用できます。

#### <a name="new-billing-scopes"></a>新しい課金スコープ

更新されたアカウントの一部として、Cost Management + Billing に新しいスコープが用意されています。 これらは、階層型組織や請求処理を支援するだけでなく、複数の基になるサブスクリプションの合計料金を表示することもできます。 課金スコープの詳細については、「[Microsoft 顧客契約のスコープ](../costs/understand-work-scopes.md#microsoft-customer-agreement-scopes)」を参照してください。

Cost Management API シリーズにアクセスして、上位のスコープで結合されたコスト ビューを利用することもできます。 サブスクリプション スコープを使用するすべての Cost Management API は、スキーマに軽微な変更を加えることで引き続き使用できます。 API の詳細については、[Cost Management API シリーズ](/rest/api/cost-management/)および [Azure Consumption API シリーズ](/rest/api/consumption/)に関する記事を参照してください。

#### <a name="cost-allocation"></a>コストの割り当て

更新されたアカウントでは、コストの割り当て機能を使用して、組織内の共有サービスのコストを配分できます。 コストの割り当ての詳細については、[Azure のコストの割り当てルールの作成と管理](../costs/allocate-costs.md)に関する記事を参照してください。

#### <a name="power-bi"></a>Power BI

Power BI Desktop 用の Cost Management コネクタを使用すると、Azure の使用状況と支出に関するカスタムの視覚化とレポートを作成できます。 更新されたアカウントに接続した後、コストと使用状況のデータにアクセスします。 Power BI Desktop 用の Cost Management コネクタの詳細については、「[Power BI Desktop で Cost Management コネクタを使用してビジュアルとレポートを作成する](/power-bi/connect-data/desktop-connect-azure-cost-management)」を参照してください。

### <a name="updated-capabilities"></a>更新された機能

Azure 課金アカウントでは、以下の更新された機能を使用できます。

#### <a name="cost-analysis"></a>コスト分析

引き続き、前月比の消費コストを表示および追跡できます。また、コスト分析で予約コストと Marketplace での購入コストを表示できるようになりました。

更新されたアカウントでは、Azure のすべての料金に対して 1 つの請求書を受け取ります。 また、以前の請求期間ビューに代わって、簡素化された単一の月単位のカレンダー ビューが提供されるようになりました。

たとえば、古いアカウントの請求期間が 11 月 24 日から 12 月 23 日であった場合、アップグレード後は、期間が 11 月 1 日から 11 月 30 日、12 月 1 日から 12 月 31 日 (以降も同様) になります。

:::image type="content" source="./media/mosp-new-customer-experience/billing-periods.png" alt-text="新旧の請求期間の比較を示すスクリーンショット。" lightbox="./media/mosp-new-customer-experience/billing-periods.png" :::

#### <a name="budgets"></a>予算

課金アカウントの予算を作成できるようになりました。これにより、複数のサブスクリプションにわたってコストを追跡できます。 また、予算を使用して購入料金を把握することもできます。 予算の詳細については、[Azure の予算の作成と管理](../costs/tutorial-acm-create-budgets.md)に関する記事を参照してください。

#### <a name="exports"></a>Exports

新しい課金アカウントでは、エクスポート機能が強化されています。 たとえば、購入または償却コスト (購入期間に配分された予約購入コスト) を含む実績コストのエクスポートを作成できます。 課金アカウントのエクスポートを作成して、課金アカウントのすべてのサブスクリプションの使用状況と料金のデータを取得することもできます。 エクスポートの詳細については、[エクスポートされたデータの作成と管理](../costs/tutorial-export-acm-data.md)に関する記事を参照してください。

> [!NOTE]
> 種類として **[Monthly export of last month's costs]\(先月のコストの月単位のエクスポート\)** を使用して、アカウントの更新前に作成されたエクスポートでは、最後の請求期間ではなく、先月 (カレンダー月) のデータがエクスポートされます。

たとえば、請求期間が 12 月 23 日から 1 月 22 日までの場合、エクスポートされた CSV ファイルには、その期間のコストと使用状況のデータが含まれています。 更新後は、エクスポートにカレンダー月のデータが含まれるようになります。 たとえば、1 月 1 日から 1 月 31 日までのデータです (以降も同様)。

:::image type="content" source="./media/mosp-new-customer-experience/export-amortized-costs.png" alt-text="新旧のエクスポートの詳細の比較を示すスクリーンショット。" lightbox="./media/mosp-new-customer-experience/export-amortized-costs.png" :::

## <a name="additional-information"></a>関連情報

次のセクションでは、新しいエクスペリエンスに関する追加情報を提供します。

**サービスのダウンタイムが発生しない** ご利用のサブスクリプションの Azure サービスは、中断することなく引き続き実行されます。 唯一の更新は、課金エクスペリエンスに対するものです。 既存のリソース、リソース グループ、管理グループには影響しません。

**Azure リソースに対する変更がない** Azure ロールベースのアクセス制御 (Azure RBAC) を使用して設定された Azure リソースへのアクセスは、この更新による影響を受けません。

**過去の請求書を新しいエクスペリエンスで利用できる** お使いのアカウントが更新される前に生成された請求書は、引き続き Azure portal で利用できます。

**月の途中で更新されたアカウントの請求書** お使いのアカウントが月の途中で更新された場合、アカウントが更新された日までの累積料金の請求書が 1 つ生成されます。 その月の残りの分については別の請求書が生成されます。 たとえば、お使いのアカウントに 1 つのサブスクリプションがあり、9 月 15 日に更新されたとします。 9 月 15 日までの累積料金の請求書が 1 つ生成されます。 9 月 15 日から 9 月 30 日までの期間に対して別途請求書が生成されます。 10 月以降は、月ごとに 1 つの請求書が生成されます。

## <a name="troubleshooting"></a>トラブルシューティング

### <a name="you-get-an-email-to-accept-the-terms-of-the-microsoft-customer-agreement-but-the-link-in-the-email-doesnt-work"></a>Microsoft 顧客契約の条項に同意するための電子メールを受け取ったが、電子メール内のリンクが機能しない

Azure の課金アカウントが更新されると、Microsoft 顧客契約の条項に同意するための電子メールが Microsoft から送信されます。 次の理由により、電子メール内のリンクが機能しないことがあります。

- 他のいずれかの電子メールを使用して課金管理の実行にアクセスする必要がある。 

    これは通常、Microsoft 顧客契約の条項に同意するように求める電子メールを受け取ったときに発生します。 そのリンクをクリックすると、"`You don't have permission to accept the agreement. This typically happens when you sign in with an email, which doesn’t have permission to accept the agreement. Check you’ve signed in with the correct email address. If you are still seeing the error, see Why I can't accept an agreement`" というエラー メッセージが表示されます。 課金管理を実行するためのアクセス許可があるメール アドレスでサインインしていることを確認してください。

- 別の ID を使用して請求書にアクセスする必要がある。 

    お客様によっては、同じメール アドレスで 2 つの ID (職場アカウントと Microsoft アカウント) を所有している場合があります。 通常、課金管理を実行するアクセス許可があるのは、そのいずれかの ID のみです。 アクセス許可がない ID でサインインした場合、条項に同意することはできません。 正しい ID を使用してサインインしていることを確認してください。

- 間違った Azure Active Directory (Azure AD) テナントにサインインしている。 

    課金アカウントは Azure AD テナントに関連付けられます。 間違ったテナントにサインインした場合、条項に同意することはできません。 正しい Azure AD テナントにサインインしていることを確認してください。 正しいテナントにサインインしていない場合は、Azure portal で次の手順に従ってテナントを切り替えてください。

    1. ページの右上から自分のメールを選択します。

    2. **[ディレクトリの切り替え]** を選択します。

        ![ポータルで [ディレクトリの切り替え] を選択する操作を示すスクリーンショット](./media/mosp-new-customer-experience/select-switch-directory.png)

    3. **[すべてのディレクトリ]** セクションからディレクトリの **[切り替え]** を選択します。

        ![ポータルでのディレクトリの選択操作を示すスクリーンショット](./media/mosp-new-customer-experience/select-directory.png)

## <a name="need-help-contact-support"></a>お困りの際は、 サポートにお問い合せください。

お困りの際は、問題を迅速に解決するために、[サポートにお問い合わせ](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)ください。

## <a name="next-steps"></a>次のステップ

課金アカウントの詳細については、次の記事を参照してください。

- [新しい課金アカウントの管理者ロールについて](../manage/understand-mca-roles.md)
- [新しい課金アカウントに追加の Azure サブスクリプションを作成する](../manage/create-subscription.md)
- [請求書にセクションを作成してコストを整理する](../manage/mca-section-invoice.md)