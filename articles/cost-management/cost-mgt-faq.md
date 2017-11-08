---
title: "Azure Cost Management に関してよく寄せられる質問 | Microsoft Docs"
description: "Azure Cost Management についてよく寄せられる質問にお答えします。"
services: cost-management
keywords: 
author: bandersmsft
ms.author: banders
ms.date: 10/23/2017
ms.topic: article
ms.service: cost-management
manager: carmonm
ms.custom: 
ms.openlocfilehash: a01d8d1ed0f5234f4950d448b54087767353c8ef
ms.sourcegitcommit: 3ab5ea589751d068d3e52db828742ce8ebed4761
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/27/2017
---
# <a name="frequently-asked-questions-for-azure-cost-management"></a>Azure Cost Management に関してよく寄せられる質問


この記事では、Azure Cost Management についてよく寄せられる質問にお応えします (Cloudyn とも呼ばれます)。 Cost Management について質問がある場合は、「[FAQs for Azure Cost Management by Cloudyn (Azure Cost Management by Cloudyn に関する FAQ)](https://social.msdn.microsoft.com/Forums/en-US/231bf072-2c71-4121-8339-ac9d868137b9/faqs-for-azure-cost-management-by-cloudyn?forum=Cloudyn)」で質問してください。

## <a name="how-can-i-resolve-common-indirect-enterprise-setup-problems"></a>間接加入契約のエンタープライズ設定に関する問題全般を解決するにはどうすればよいですか

Cloudyn ポータルを始めて使用する際、Enterprise Agreement またはクラウド ソリューション プロバイダー (CSP) ユーザーのお客様の場合、次のメッセージが表示されることがあります。

- **Azure Cost Management 設定**ウィザードに "The specified API key is not a top level enrollment key\(指定された API キーは、最上位レベルの登録キーではありません\)" と表示される。
- Enterprise Agreement ポータルに "Direct Enrollment – No\(直接登録 – いいえ\)" と表示される。
- Cloudyn ポータルに "No usage data was found for the last 30 days. Please contact your distributor to make sure markup was enabled for your Azure account\(過去 30 日間に使用状況データが見つかりませんでした。ディストリビューターに連絡してお使いの Azure アカウントに対してマークアップが有効になっていることを確認してください\)" と表示される。

前述のメッセージは、Azure Enterprise Agreement を再販業者または CSP から購入したことを示します。 Cloudyn でデータを確認するには、再販業者または CSP がユーザーの Azure アカウントの "_マークアップ_" を有効にする必要があります。

問題の修正方法は次のとおりです。

1. 再販業者がユーザーのアカウントの "_マークアップ_" を有効にする。 手順については、「[間接加入契約者向けオンボード ガイド](https://ea.azure.com/api/v3Help/v2IndirectCustomerOnboardingGuide)」をご覧ください。

2. ユーザーが Cloudyn で使用する Azure Enterprise Agreement キーを生成する。 手順については、「[Adding Your Azure EA (Azure EA を追加する)](https://support.cloudyn.com/hc/en-us/articles/210429585-Adding-Your-AZURE-EA)」または[自分の EA 登録 ID と API キーを探す方法](https://youtu.be/u_phLs_udig)に関するビデオをご覧ください。

Cost Management を有効にできるのは Azure のサービス管理者のみです。 共同管理者のアクセス許可では不十分です。

Azure Enterprise Agreement API キーを生成して Cloudyn を設定する前に、次の手順に従って Azure Billing API を有効にする必要があります。

- [企業ユーザー向けの Reporting API の概要](../billing/billing-enterprise-api.md)
- 「**Enabling data access to the API (API へのデータ アクセスを有効にする)**」の「[Microsoft Azure Enterprise Portal Reporting API](https://ea.azure.com/helpdocs/reportingAPI)」


また、部門管理者、アカウント所有者、およびエンタープライズ管理者に、Billing API による "_請求を確認する_" アクセス許可を付与する必要がある場合もあります。

## <a name="how-do-i-enable-suspended-or-locked-out-users"></a>中断またはロックアウトされたユーザーを有効にするにはどうすればよいですか

ユーザーのアクセスの許可を要求するアラートを受け取った場合、そのユーザーのアカウントをアクティブにする必要があります。

ユーザー アカウントをアクティブにするには:

1. Cloudyn の設定に使用した Azure の管理ユーザーのアカウントを使用して Cloudyn にサインインします。 または、管理者アクセスが付与されたユーザー アカウントでサインインします。

2. 右上にある歯車記号を選択し、**[ユーザー管理]** を選択します。

3. ユーザーを検索し、鉛筆記号を選択してユーザーを編集します。

4. **[ユーザーの状態]** で、状態を **[中断]** から **[アクティブ]** に変更します。

Cloudyn のユーザー アカウントが Azure のシングル サインオンを使用して接続されます。 パスワードの入力を間違えると、Azure にはアクセスできても、Cloudyn からロックアウトされることがあります。

Cloudyn の電子メール アドレスを Azure の既定のアドレスから変更すると、アカウントがロックアウトされる場合があります。"status initiallySuspended\(ステータス initiallySuspended\)" というメッセージが表示される場合があります。 お使いのユーザー アカウントがロックアウトされた場合は、別の管理者にアカウントのリセットを依頼します。

一方のアカウントがロックアウトされた場合を考慮して、Cloudyn の管理者アカウントを 2 つ以上作成することをお勧めします。

Cloudyn ポータルにサインインできない場合は、Azure Cost Management の正しい URL を使用して Cloudyn にサインインしていることを確認します。 次のいずれかの URL を使用します。

- https://azure.cloudyn.com
- https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/CloudynMainBlade

Cloudyn のダイレクト URL https://app.cloudyn.com は使用しないでください。

## <a name="how-do-i-activate-unactivated-accounts-with-azure-credentials"></a>Azure の資格情報でアクティブになっていないアカウントをアクティブにする方法を教えてください

Cloudyn で Azure のアカウントが見つかるとすぐに、コスト データがコストベースのレポートに提供されます。 ただし、Cloudyn が使用状況データとパフォーマンス データを提供するには、そのアカウントの Azure の資格情報を登録する必要があります。 手順については、[Azure Resource Manager の追加](https://support.cloudyn.com/hc/en-us/articles/212784085-Adding-Azure-Resource-Manager)に関するページを参照してください。

アカウントの Azure の資格情報を追加するには、Cloudyn ポータルで、サブスクリプションではなく、アカウント名の右にある編集記号を選択します。

Cloudyn にお使いの Azure の資格情報が追加されるまで、アカウントは "_非アクティブ_" として表示されます。

## <a name="how-do-i-add-multiple-accounts-and-entities-to-an-existing-subscription"></a>既存のサブスクリプションに複数のアカウントやエンティティを追加するにはどうすればよいですか

追加のエンティティは、Cloudyn のサブスクリプションに Enterprise Agreement をさらに追加するために使用されます。 エンティティをさらに追加する方法については、次のリンクで説明されています。

- 「[Adding an Entity (エンティティを追加する)](https://support.cloudyn.com/hc/en-us/articles/212016145-Adding-an-Entity)」の記事
- [コスト エンティティでの階層の定義](https://support.cloudyn.com/hc/en-us/articles/115005142529-Video-Defining-your-hierarchy-with-Cost-Entities)に関するビデオ

CSP の場合:

エンティティに CSP のアカウントをさらに追加するには、新しいエンティティを作成するときに、**[エンタープライズ]** の代わりに **[MSP Access]\(MSP アクセス\)** を選択します。 Enterprise Agreement として登録されているアカウントに CSP の資格情報を追加するには、Cloudyn のサポート担当者がそのアカウントの設定を変更する必要がある場合があります。 有料の Azure サブスクライバーの場合は、Azure Portal で新しいサポート要求を作成できます。 **[ヘルプとサポート]** を選択し、**[新しいサポート要求]** を選択します。

## <a name="how-do-i-change-the-currency-symbol-used-in-cloudyn"></a>Cloudyn で使用されている通貨記号はどのようにして変更すればよいですか

単一のエンティティ内のすべての Azure アカウントが同じ通貨を使用している場合、使用する通貨は自動的に検出されます。 ただし、次の通貨を使用している場合、通貨記号が誤って **$** として表示されます。

- GBP = イギリス スターリング ポンド
- EUR = ユーロ
- INR = インド ルピー
- NOK =ノルウェー クローネ

米ドルの通貨記号は **$** と表示される場合があるものの、そのコスト価格は正しい通貨で表示されます。 たとえば、同じエンティティ内のすべてのアカウントでユーロが使用されている場合、Cloudyn で表示される "_価格_" は、誤って **$** 記号が表示されている場合でも、ユーロになります。

Azure Enterprise Agreement のお客様の場合、Cloudyn のサポート担当者は $ で表示されているコスト レポートの通貨記号を変更できます。 Azure Portal で新しいサポート要求を作成できます。 **[ヘルプとサポート]** を選択し、**[新しいサポート要求]** を選択します。

CSP のお客様の場合、通貨記号は変更できません。 Cloudyn は米ドルを使用する価格カードのみをサポートします。 Cloudyn では、別の通貨の価格カードをサポートするオプションを検討しています。

## <a name="what-are-cloudyn-data-refresh-timelines"></a>Cloudyn のデータ更新タイムラインは何ですか

Cloudyn のデータ更新タイムラインは次のとおりです。

- **初期**: Cloudyn でコスト データを表示するまでに最大で 24 時間かかる場合があります。 また、サイズ変更に関する推奨事項を表示するために Cloudyn が十分なデータを収集するのに、最大で 10 日かかります。
- **日次**: 各月の 10 日から末日まで、Cloudyn では前日からのデータをその次の日の UTC+3 くらいに表示します。
- **月次**: 各月の 1 日から 10 日まで、Cloudyn は前月の終わりまでのデータのみを表示する場合があります。

Cloudyn は前日の全データが利用可能になったタイミングで、前日のデータを処理します。 通常、前日のデータは各日の UTC+3 くらいに Cloudyn で使用できるようになります。 タグなどの一部のデータの処理には、さらに 24 時間かかる場合があります。

毎月月初に、当月のデータは収集できません。 その間に、サービス プロバイダーは前月の請求額を確定します。 前月のデータは、各月の月初から 5 ～ 10 日後に Cloudyn に表示されます。 この間は、前月からの償却されたコストのみが表示される場合があります。 請求や使用状況に関する日次データは表示されない場合があります。 データが利用可能になると、Cloudyn はそれをさかのぼって処理します。 処理が終わると、すべての月次データが各月の 5 日から 10 日までの間に表示されます。

Azure から Cloudyn へのデータの送信が遅延した場合、データは Azure に記録されたままになります。 データは接続が復元されたタイミングで Cloudyn に転送されます。

## <a name="how-can-a-direct-csp-configure-cloudyn-access-for-indirect-csp-customers-or-partners"></a>直接 CSP が間接 CSP のお客様やパートナーの Cloudyn のアクセスを構成するにはどうすればよいですか

手順については、「[Cloudyn で間接 CSP アクセスを構成する](quick-register-csp.md#configure-indirect-csp-access-in-cloudyn)」をご覧ください。
