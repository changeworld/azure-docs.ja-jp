---
title: Microsoft 顧客契約の請求先の設定 - Azure
description: Microsoft 顧客契約の請求先アカウントの設定方法について説明します。 設定の前提条件を参照して、使用可能なその他のリソースを確認してください。
author: bandersmsft
ms.reviewer: amberb
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 10/07/2021
ms.author: banders
ms.openlocfilehash: 74f49b2df909c157555390fedd280d1d51615530
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131433954"
---
# <a name="set-up-your-billing-account-for-a-microsoft-customer-agreement"></a>Microsoft 顧客契約の請求先アカウントの設定

直接 Enterprise Agreement 加入契約の有効期限が切れているか、期限切れが近い場合は、Microsoft 顧客契約に署名して加入契約を更新できます。 この記事では、セットアップ後の既存の請求先に対する変更について説明し、新しい請求先アカウントのセットアップ手順について説明します。 現在、有効期限が切れた間接 Enterprise Agreement は、Microsoft 顧客契約では更新できません。

更新の手順は次のとおりです。

1. 新しい Microsoft 顧客契約に同意します。 Microsoft フィールド担当に問い合わせて、新しい契約の詳細を理解し、同意します。
2. 新しい Microsoft 顧客契約用に作成された新しい請求先アカウントを設定します。

請求先アカウント設定を完了するには、Azure サブスクリプションの請求先を Enterprise Agreement 加入契約から新しいアカウントに切り替える必要があります。 セットアップは、サブスクリプションで実行されている Azure サービスに影響しません。 ただし、サブスクリプションに対する課金を管理する方法は変更されます。

- [EA ポータル](https://ea.azure.com)の代わりに、[Azure portal](https://portal.azure.com) 上で Azure サービスと課金を管理します。
- 料金に対して月単位のデジタル請求書を受け取ります。 [Cost Management + Billing] ページで、請求書を確認して分析します。
- Enterprise Agreement 加入契約での部署とアカウントの代わりに、新しいアカウントの課金の体系とスコープを使用して課金を管理および整理します。

セットアップを開始する前に、次の操作を行うことをお勧めします。

- **新しい請求先アカウントを確認する**
  - 新しいアカウントでは、組織の課金が簡素化されます。 [新しい請求先アカウントの概要を取得します](../understand/mca-overview.md)
- **セットアップを完了するためのアクセス権を確認する**
  - 特定の管理アクセス許可を持つユーザーだけがセットアップを完了できます。 [セットアップを完了するために必要なアクセス権](#access-required-to-complete-the-setup)があるかどうかを確認します。
- **課金階層に対する変更を確認する**
  - 新しい請求先アカウントは、Enterprise Agreement 加入契約とは異なる方法で構成されます。 [新規アカウントの課金階層に対する変更を確認します](#understand-changes-to-your-billing-hierarchy)。
- **課金管理者のアクセス権に対する変更を確認する**
  - Enterprise Agreement 加入契約の管理者は、新しいアカウントの課金スコープへのアクセス権を取得します。[アクセス権に対する変更を確認](#changes-to-billing-administrator-access)します。
- **新しいアカウントで置き換えられる Enterprise Agreement 機能を表示する**
  - 新しいアカウントの機能で置き換えられる Enterprise Agreement 加入契約の機能を表示します。
- **よく寄せられる質問に対する回答を表示する**
  - [追加情報](#additional-information)を表示して、セットアップの詳細を確認します。

## <a name="access-required-to-complete-the-setup"></a>セットアップを完了するために必要なアクセス権

セットアップを完了するには、以下のアクセス権が必要です。

- Microsoft 顧客契約に署名したときに作成された課金プロファイルの所有者。 課金プロファイルについて詳しくは、「[課金プロファイルを理解する」](../understand/mca-overview.md#billing-profiles)をご覧ください。  
&mdash; および &mdash;
- 更新される加入契約のエンタープライズ管理者。

### <a name="start-migration-and-get-permission-needed-to-complete-setup"></a>移行を開始し、セットアップを実行するために必要なアクセス許可を取得する

EA 加入契約から Microsoft 顧客契約への移行エクスペリエンスを開始するにあたっては、次の選択肢があります。


- Microsoft 顧客契約に署名したときに送信された電子メール内のリンクを使用して、Azure portal にサインインします。

- メールがない場合は、次のリンクを使用してサインインします。

  `https://portal.azure.com/#blade/Microsoft_Azure_SubscriptionManagement/TransitionEnrollment`

エンタープライズ管理者ロールに加え、課金アカウント所有者ロールまたは課金プロファイル ロールが割り当てられている場合、Azure portal に次のページが表示されます。 引き続き、EA 加入契約と Microsoft 顧客契約の課金アカウントの切り替えを設定できます。

:::image type="content" source="./media/mca-setup-account/setup-billing-account-page.png" alt-text="[課金アカウントのセットアップ] ページのスクリーンショット" lightbox="./media/mca-setup-account/setup-billing-account-page.png" :::

Enterprise Agreement のエンタープライズ管理者ロールも Microsoft 顧客契約の課金プロファイル所有者ロールもない場合は、以下の情報を使用して、セットアップの実行に必要なアクセス権を取得してください。

#### <a name="if-youre-not-an-enterprise-administrator-on-the-enrollment"></a>加入契約のエンタープライズ管理者でない場合

課金アカウント所有者ロールまたは課金プロファイル所有者ロールは割り当てられているものの、エンタープライズ管理者ではない場合、Azure portal に次のページが表示されます。

:::image type="content" source="./media/mca-setup-account/setup-billing-account-page-not-ea-administrator.png" alt-text="[課金アカウントのセットアップ] ページのスクリーンショット - [Prepare your Enterprise Agreement enrollments for transition]\(Enterprise Agreement 加入契約の切り替えを準備する\)" lightbox="./media/mca-setup-account/setup-billing-account-page-not-ea-administrator.png" :::

2 つのオプションがあります。

- 加入契約のエンタープライズ管理者に依頼してエンタープライズ管理者ロールを割り当ててもらいます。 詳細については、「[別のエンタープライズ管理者を作成する](ea-portal-administration.md#create-another-enterprise-administrator)」を参照してください。
-  エンタープライズ管理者に、課金アカウント所有者ロールまたは課金プロファイル所有者ロールを割り当てることができます。 詳細については、「[Azure portal での組み込みロールを管理する](understand-mca-roles.md#manage-billing-roles-in-the-azure-portal)」を参照してください。

エンタープライズ管理者ロールを割り当ててもらった場合は、[課金アカウントのセットアップ] ページにあるリンクをコピーします。 それを Web ブラウザーで開いて、Microsoft 顧客契約のセットアップを続行してください。 それ以外の場合は、エンタープライズ管理者にそれを送信します。

#### <a name="if-youre-not-an-owner-of-the-billing-profile"></a>課金プロファイルの所有者でない場合

エンタープライズ管理者ではあるものの、課金アカウントをお持ちでない場合、Azure portal に以下のエラーが表示されて切り替えができません。

適切な Microsoft 顧客契約に対する課金プロファイル所有者のアクセス権があるにもかかわらず次のメッセージが表示される場合は、現在のテナントが、所属組織の正しいテナントであることを確認してください。 ディレクトリの変更が必要になる場合があります。

:::image type="content" source="./media/mca-setup-account/setup-billing-account-page-not-billing-account-profile-owner.png" alt-text="[課金アカウントのセットアップ] ページのスクリーンショット - [Microsoft Customer Agreement billing account]\(Microsoft 顧客契約の課金アカウント\)。" lightbox="./media/mca-setup-account/setup-billing-account-page-not-billing-account-profile-owner.png" :::

2 つのオプションがあります。

- 既存の課金アカウント所有者に依頼して、課金アカウント所有者ロールまたは課金プロファイル所有者ロールを割り当ててもらいます。 詳細については、「[Azure portal での組み込みロールを管理する](understand-mca-roles.md#manage-billing-roles-in-the-azure-portal)」を参照してください。
- 既存の課金アカウント所有者にエンタープライズ管理者ロールを割り当てます。 詳細については、「[別のエンタープライズ管理者を作成する](ea-portal-administration.md#create-another-enterprise-administrator)」を参照してください。

課金アカウント所有者ロールまたは課金プロファイル所有者ロールを割り当ててもらった場合は、[課金アカウントのセットアップ] ページにあるリンクをコピーします。 それを Web ブラウザーで開いて、Microsoft 顧客契約のセットアップを続行してください。 それ以外の場合は、課金アカウント所有者にリンクを送信します。

#### <a name="prepare-enrollment-for-transition"></a>切り替えに向けて加入契約を準備する

EA 加入契約と課金プロファイルの両方について所有者アクセス権を取得したら、切り替えに向けた準備を行います。

以前に提示された移行 (メールで送信されたリンク) を開きます。 リンクは `https://portal.azure.com/#blade/Microsoft_Azure_SubscriptionManagement/TransitionEnrollment` です。

次の画像は、[Prepare your enterprise agreement enrollments for transition]\(Enterprise Agreement 加入契約の切り替えを準備する\) ウィンドウの例です。

:::image type="content" source="./media/mca-setup-account/setup-billing-account-prepare-enrollment-transition.png" alt-text="[課金アカウントのセットアップ] ページのスクリーンショット - [Prepare your Enterprise Agreement enrollments for transition]\(Enterprise Agreement 加入契約の切り替えを準備する\) の選択前。" lightbox="./media/mca-setup-account/setup-billing-account-prepare-enrollment-transition.png" :::

次に、切り替え元の加入契約を選択します。 さらに、課金アカウントと課金プロファイルを選択します。 問題が生じることなく検証にパスした場合 (次の画面を参照)、 **[Continue]\(続行\)** を選択して続行します。

:::image type="content" source="./media/mca-setup-account/setup-billing-account-prepare-enrollment-transition-continue.png" alt-text="[課金アカウントのセットアップ] ページのスクリーンショット - [Prepare your Enterprise Agreement enrollments for transition]\(Enterprise Agreement 加入契約の切り替えを準備する\) と検証済みの選択肢。" lightbox="./media/mca-setup-account/setup-billing-account-prepare-enrollment-transition-continue.png" :::

**エラー条件**

エンタープライズ管理者 (読み取り専用) ロールが割り当てられている場合、以下のエラーが表示されて切り替えができません。 加入契約を切り替えるには、エンタープライズ管理者ロールが必要です。

`Select another enrollment. You do not hve Enterprise Administrator write permission to the enrollment.`

加入契約の終了日までの日数が 60 日を超える場合、以下のエラーが表示されて切り替えができません。 加入契約を切り替えるには、現在の日付が、加入契約の終了日の 60 日以内である必要があります。

`Select another enrollment. This enrollment has more than 60 days before its end date.`

加入契約にクレジットが残っている場合、以下のエラーが表示されて切り替えができません。 加入契約を切り替えるには、クレジットをすべて使い切る必要があります。

`Select another enrollment. This enrollment still has credits and can't be transitioned to a billing account.`

課金プロファイルに対する所有者アクセス許可がない場合、以下のエラーが表示されて切り替えができません。 加入契約を切り替えるには、課金プロファイル所有者ロールが必要です。

`Select another Billing Profile. You do not have owner permission to this profile.`

新しい課金プロファイルで新しいプランが有効になっていない場合は、以下のエラーが表示されます。 加入契約を切り替えるには、プランを有効にする必要があります。

`Select another Billing Profile. The current selection does not have Azure Plan and Azure dev test plan enabled on it.`

## <a name="understand-changes-to-your-billing-hierarchy"></a>課金階層に対する変更を確認する

新しい請求先アカウントでは、課金とコスト管理の機能を強化しながら、組織の課金が簡素化されます。 次の図では、新しい請求先アカウントの課金を整理する方法について説明します。

![EA から MCA への移行後の階層の図](./media/mca-setup-account/mca-post-transition-hierarchy.png)

1. 請求先アカウントを使用して、Microsoft 顧客契約の課金を管理します。 エンタープライズ管理者が課金アカウントの所有者になります。 請求先アカウントについて詳しくは、「[請求先アカウントを理解する](../understand/mca-overview.md#your-billing-account)」をご覧ください。
2. Enterprise Agreement 加入契約の場合と同様に、課金プロファイルを使用して組織の課金を管理します。 エンタープライズ管理者が課金プロファイルの所有者になります。 課金プロファイルについて詳しくは、「[課金プロファイルを理解する」](../understand/mca-overview.md#billing-profiles)をご覧ください。
3. Enterprise Agreement 加入契約の部署と同様に、請求書セクションを使用して、ニーズに基づいてコストを整理します。 部署が請求書セクションになり、部署の管理者がそれぞれの請求書セクションの所有者になります。 請求書セクションについて詳しくは、「[請求書セクションを理解する](../understand/mca-overview.md#invoice-sections)」をご覧ください。
4. マイクロソフト エンタープライズ契約で作成されたアカウントは、新しい課金アカウントではサポートされません。 アカウントのサブスクリプションは、各部署の請求書セクションに属します。 アカウント オーナーは、各自の請求書セクションのサブスクリプションを作成および管理できます。

## <a name="changes-to-billing-administrator-access"></a>課金管理者のアクセス権を変更する

アクセス権に応じて、Enterprise Agreement 加入契約の課金管理者は、新しいアカウントの課金スコープへのアクセス権を取得します。 次の表では、セットアップ中のアクセスの変化について説明します。

| 既存のロール | 切り替え後のロール |
| --- | --- |
| **エンタープライズ管理者 (読み取り専用 = いいえ)** | **- 課金アカウント所有者** </br> 課金アカウントに関するすべてを管理します </br> **- 課金プロファイル所有者** </br> 課金プロファイルに関するすべてを管理します </br> **- すべての請求書セクションの請求書セクション所有者** </br> 請求書セクションに関するすべてを管理します |
| **エンタープライズ管理者 (読み取り専用 = はい)** | **- 課金アカウント閲覧者** </br> 請求先アカウントに関するすべての読み取り専用ビュー</br> **- 課金プロファイル閲覧者** </br> 課金プロファイルに関するすべての読み取り専用ビュー</br>**- すべての請求書セクションの請求書セクション閲覧者**</br> 請求書セクションに関するすべての読み取り専用ビュー|
| **部署管理者 (読み取り専用 = いいえ)** |**- それぞれの部署用に作成された請求書セクションの請求書セクション所有者** </br>請求書セクションに関するすべてを管理します|
| **部署管理者 (読み取り専用 = はい)**|**- それぞれの部署用に作成された請求書セクションの請求書セクション閲覧者**</br> 請求書セクションに関するすべての読み取り専用ビュー|
| **アカウント オーナー** | **- それぞれの部署用に作成された請求書セクションの Azure サブスクリプション作成者** </br>  請求書セクションの Azure サブスクリプションを作成します|

Azure Active Directory (AD) テナントは、Microsoft 顧客契約への同意時に新しい課金アカウントに対して選択されます。 組織のテナントが存在しない場合は、新しいテナントが作成されます。 テナントは、Azure Active Directory 内の組織を表します。 組織のテナントの全体管理者は、組織内のアプリケーションとデータへのアクセスを管理するためにテナントを使用します。

新しいアカウントでは、Microsoft 顧客契約に署名する際に選択したテナントのユーザーのみがサポートされます。 マイクロソフト エンタープライズ契約に対する管理アクセス許可を持つユーザーは、テナントに属していれば、セットアップ中も新しい請求先アカウントにアクセスできます。 テナントに属していない場合は、招待されない限り、新しい課金アカウントにアクセスできません。

ユーザーを招待すると、そのユーザーはテナントにゲスト ユーザーとして追加され、課金アカウントにアクセスできます。 ユーザーを招待するには、テナントに対してゲスト アクセスをオンする必要があります。 詳しくは、「[Azure Active Directory 内のゲスト アクセスの制御](/microsoftteams/teams-dependencies#control-guest-access-in-azure-active-directory)」をご覧ください。 ゲスト アクセスがオフの場合は、テナントの全体管理者に連絡してオンにしてください。 <!-- Todo - How can they find their global administrator -->

## <a name="view-replaced-features"></a>置き換えられた機能を表示する

以下の Enterprise Agreement 機能は、Microsoft 顧客契約の請求先アカウントの新機能に置き換えられます。

### <a name="enterprise-agreement-accounts"></a>Enterprise Agreement アカウント

Enterprise Agreement 加入契約で作成されたアカウントは、新しい請求先アカウントではサポートされません。 アカウントのサブスクリプションは、それぞれの部署に対して作成された請求書セクションに属します。 アカウント オーナーは Azure サブスクリプション作成者になり、請求書セクションでのサブスクリプションを作成および管理できます。

### <a name="notification-contacts"></a>通知の連絡窓口

通知の連絡窓口には Azure Enterprise Agreement に関する電子メール通信が送信されます。 新しい課金アカウントではサポートされません。 Azure クレジットと請求書に関する電子メールは、請求先アカウントの課金プロファイルへのアクセス権を持っているユーザーに送信されます。

### <a name="spending-quotas"></a>支出クォータ

Enterprise Agreement 加入契約で部署に対して設定された支出クォータは、新しい請求先アカウント内で予算に置き換えられます。 加入契約内の部署に対して設定された支出クォータごとに予算が作成されます。 予算の詳細については、「[チュートリアル: Azure の予算を作成して管理する](../costs/tutorial-acm-create-budgets.md)」を参照してください。

### <a name="cost-centers"></a>コスト センター

Enterprise Agreement 加入契約で Azure サブスクリプションに設定されたコスト センターは、新しい請求先アカウントに引き継がれます。 ただし、部署と Enterprise Agreement アカウントのコスト センターはサポートされていません。

## <a name="additional-information"></a>関連情報

以下のセクションでは、請求先アカウントの設定に関する追加情報を示します。

### <a name="no-service-downtime"></a>サービスのダウンタイムは発生しない

ご利用のサブスクリプションの Azure サービスは、中断することなく引き続き実行されます。 Azure サブスクリプションの課金関係だけが切り替えられます。 既存のリソース、リソース グループ、管理グループには影響しません。

### <a name="user-access-to-azure-resources"></a>Azure リソースに対するユーザー アクセス

Azure ロールベースのアクセス制御 (Azure RBAC) を使用して設定された Azure リソースへのアクセスは、切り替え中に影響を受けることはありません。

### <a name="azure-reservations"></a>Azure の予約

Enterprise Agreement 加入契約での Azure の予約は、新しい請求先アカウントに移行されます。 切り替え中に、ご利用のサブスクリプションに適用されている予約割引が変更されることはありません。

### <a name="azure-marketplace-products"></a>Azure Marketplace 製品

Enterprise Agreement 加入契約の Azure Marketplace 製品は、サブスクリプションと共に移行されます。 切り替え中に、Marketplace 製品のサービス アクセスが変更されることはありません。

### <a name="support-plan"></a>サポート プラン

サポート特典は切り替えの一環として譲渡されません。 新しい請求先アカウントで Azure サブスクリプションの特典を取得するには新しいサポート プランを購入します。

### <a name="past-charges-and-balance"></a>過去の料金と残高

切り替え前の料金とクレジット残高は、Azure portal を通じて Enterprise Agreement 加入契約内で確認できます。 <!--Todo - Add a link for this-->

### <a name="when-should-the-setup-be-completed"></a>セットアップを完了する必要があるタイミング

Enterprise Agreement 加入契約が期限切れになる前に、請求先アカウントのセットアップを完了してください。 加入契約の期限が切れた場合、Azure サブスクリプション内のサービスは中断なく稼働を続けます。 ただし、サービスには従量課金料金が請求されます。

### <a name="changes-to-the-enterprise-agreement-enrollment-after-the-setup"></a>セットアップ後の Enterprise Agreement 加入契約に対する変更

切り替え後に Enterprise Agreement 加入契約に対して作成される Azure サブスクリプションは、新しい請求先アカウントに手動で移行できます。 詳しくは、「[他のユーザーから Azure サブスクリプションの課金所有権を取得する](mca-request-billing-ownership.md)」をご覧ください。 切り替え後に購入した Azure の予約を移行するには、[Azure サポートにお問い合わせください](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。 切り替え後に請求先アカウントへのアクセス権をユーザーに付与することもできます。 詳しくは、「[Azure portal での組み込みロールを管理する](understand-mca-roles.md#manage-billing-roles-in-the-azure-portal)」をご覧ください

### <a name="revert-the-transition"></a>切り替えを元に戻す

切り替えを元に戻すことはできません。 Azure サブスクリプションの請求が新しい請求先アカウントに切り替わったら、Enterprise Agreement 加入契約に戻すことはできません。

### <a name="closing-your-browser-during-setup"></a>セットアップ中にブラウザーを閉じる

**[切り替えを開始]** を選択する前に、ブラウザーを閉じることができます。 電子メールで取得したリンクを使用してセットアップに戻り、切り替えを開始できます。 切り替えの開始後にブラウザーを閉じた場合、切り替えは実行を続けます。 切り替え状態ページに戻って切り替えの最新状態を監視します。 切り替えが完了すると、電子メールが送られてきます。

## <a name="complete-the-setup-in-the-azure-portal"></a>Azure portal でのセットアップを完了する

セットアップを完了するには、新しい請求先アカウントと Enterprise Agreement 加入契約の両方へのアクセス権が必要です。 詳しくは、[請求先アカウントの設定を完了するために必要なアクセス権](#access-required-to-complete-the-setup)に関するセクションをご覧ください。

1. Microsoft 顧客契約に署名したときに送信された電子メール内のリンクを使用して、Azure portal にサインインします。

2. メールがない場合は、次のリンクを使用してサインインします。

   `https://portal.azure.com/#blade/Microsoft_Azure_SubscriptionManagement/TransitionEnrollment`

3. セットアップの最後の手順で **[切り替えを開始]** を選択します。 [切り替えを開始] を選択した後で、次の操作を行います。

    ![セットアップウィザードを示すスクリーンショット](./media/mca-setup-account/ea-mca-set-up-wizard.png)

    - Enterprise Agreement 階層に対応する課金階層は、新しい請求先アカウントに作成されます。 詳しくは、「[課金階層に対する変更を確認する](#understand-changes-to-your-billing-hierarchy)」をご覧ください。
    - Enterprise Agreement 加入契約の管理者には、新しい請求先アカウントへのアクセス権が付与されるので、引き続き組織の課金を管理できます。
    - Azure サブスクリプションの請求先は、新しいアカウントに切り替えられます。 **この切り替え中に Azure サービスは影響を受けません。中断なく稼働し続けます**。
    - Azure の予約がある場合は、特典や条件の変更なしで新しい課金アカウントに移行されます。

4. 切り替えの状態は、 **[切り替え状態]** ページで監視することができます。

   ![切り替え状態を示すスクリーンショット](./media/mca-setup-account/ea-mca-set-up-status.png)

## <a name="validate-billing-account-setup"></a>課金アカウントの設定を検証する

 以下を検証して、新しい請求先アカウントが正しく設定することを確認します。

### <a name="azure-subscriptions"></a>Azure サブスクリプション

1. [Azure portal](https://portal.azure.com) にサインインします。

2. "**コスト管理 + 請求**" を検索します。

   ![Azure portal の検索を表示するスクリーンショット](./media/mca-setup-account/search-cmb.png)

3. 請求先アカウントを選択します。 課金アカウントの種類は、**Microsoft 顧客契約** です。

4. 左側の **[Azure サブスクリプション]** を選択します。

   ![サブスクリプションの一覧を示すスクリーンショット](./media/mca-setup-account/mca-subscriptions-post-transition.png)

Enterprise Agreement 加入契約から新しい請求先アカウントに切り替えられた Azure サブスクリプションが [Azure サブスクリプション] ページに表示されます。 不足しているサブスクリプションがあると思われる場合は、Azure portal 上でサブスクリプションの請求先を手動で切り替えます。 詳しくは、「[他のユーザーから Azure サブスクリプションの課金所有権を取得する」](mca-request-billing-ownership.md)をご覧ください

### <a name="azure-reservations"></a>Azure の予約

Enterprise Agreement 加入契約での Azure の予約は、特典や条件の変更なしで、新しい課金アカウントに移行されます。 切り替え前に完了したトランザクションは、新しい課金アカウントには表示されません。 ただし、[Azure の予約ページ](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade)にアクセスして、予約の特典がサブスクリプションに適用されていることを確認できます。

### <a name="access-of-enterprise-administrators-on-the-billing-account"></a>課金アカウントに対するエンタープライズ管理者のアクセス権

1. [Azure portal](https://portal.azure.com) にサインインします。

2. "**コスト管理 + 請求**" を検索します。

   ![Azure portal の検索を表示するスクリーンショット](./media/mca-setup-account/search-cmb.png)

3. **Microsoft 顧客契約** の課金アカウントを選択します。

4. 左側から **[アクセス制御 (IAM)]** を選択します。

   ![切り替え後の課金アカウント所有者として表示されるエンタープライズ管理者のアクセス権を示すスクリーンショット。](./media/mca-setup-account/mca-ea-admins-ba-access-post-transition.png)

エンタープライズ管理者は課金アカウント所有者として表示されますが、読み取り専用アクセス許可を持つエンタープライズ管理者は課金アカウント閲覧者として表示されます。 エンタープライズ管理者のアクセス権が不足していると思われる場合は、Azure portal 上でアクセス権を付与できます。 詳しくは、「[Azure portal での組み込みロールを管理する](understand-mca-roles.md#manage-billing-roles-in-the-azure-portal)」をご覧ください。

### <a name="access-of-enterprise-administrators-on-the-billing-profile"></a>課金プロファイルに対するエンタープライズ管理者のアクセス権

1. [Azure portal](https://portal.azure.com) にサインインします。

2. "**コスト管理 + 請求**" を検索します。

   ![Azure portal の検索を表示するスクリーンショット](./media/mca-setup-account/search-cmb.png)

3. 加入契約に対して作成された課金プロファイルを選択します。 お持ちのアクセス権によっては、請求先アカウントを選択することが必要な場合があります。 課金アカウントから [課金プロファイル] を選択し、課金プロファイルを選択します。

4. 左側から **[アクセス制御 (IAM)]** を選択します。

   ![切り替え後の課金プロファイル所有者として表示されるエンタープライズ管理者のアクセス権を示すスクリーンショット。](./media/mca-setup-account/mca-ea-admins-bp-access-post-transition.png)

エンタープライズ管理者は課金プロファイル所有者として表示されますが、読み取り専用アクセス許可を持つエンタープライズ管理者は課金プロファイル閲覧者として表示されます。 エンタープライズ管理者のアクセス権が不足していると思われる場合は、Azure portal 上でアクセス権を付与できます。 詳しくは、「[Azure portal での組み込みロールを管理する](understand-mca-roles.md#manage-billing-roles-in-the-azure-portal)」をご覧ください。

### <a name="access-of-enterprise-administrators-department-administrators-and-account-owners-on-invoice-sections"></a>請求書セクションに対するエンタープライズ管理者、部署管理者、アカウント オーナーのアクセス権

1. [Azure portal](https://portal.azure.com) にサインインします。

2. "**コスト管理 + 請求**" を検索します。

   ![Azure portal の検索を表示するスクリーンショット](./media/mca-setup-account/search-cmb.png).

3. 請求書セクションを選択します。 請求書セクションは、Enterprise Agreement 加入契約内にそれぞれの部署と同じ名前を持ちます。 お持ちのアクセス権によっては、請求先アカウントを選択することが必要な場合があります。 課金アカウントから **[課金プロファイル]** を選択し、 **[請求書セクション]** を選択します。 請求書セクションの一覧から請求書セクションを選択します。

   ![切り替え後の請求書セクションの一覧を示すスクリーンショット](./media/mca-setup-account/mca-invoice-sections-post-transition.png)

4. 左側から **[アクセス制御 (IAM)]** を選択します。

    ![切り替え後の部署管理者とアカウント管理者のアクセス権を示すスクリーンショット](./media/mca-setup-account/mca-department-account-admins-access-post-transition.png)

エンタープライズ管理者と部署管理者は請求書セクション所有者または請求書セクション閲覧者として表示され、部署のアカウント オーナーは Azure サブスクリプション作成者として表示されます。 すべての請求書セクションについて手順を繰り返して、Enterprise Agreement 加入契約のすべての部署に対するアクセス権を確認します。 どの部署にも属していなかったアカウント オーナーは、 **[Default invoice section]\(既定請求書セクション\)** という請求書セクションに対するアクセス許可を取得します。 管理者のアクセス権が不足していると思われる場合は、Azure portal 上でアクセス権を付与できます。 詳しくは、「[Azure portal での組み込みロールを管理する](understand-mca-roles.md#manage-billing-roles-in-the-azure-portal)」をご覧ください。

## <a name="need-help-contact-support"></a>お困りの際は、 サポートにお問い合せください

お困りの際は、問題を迅速に解決するために、[サポートにお問い合わせ](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)ください。

## <a name="next-steps"></a>次のステップ

- [新しい請求先アカウントを開始する](../understand/mca-overview.md)
- [Microsoft 顧客契約用の課金アカウントでの Enterprise Agreement タスクを実行する](mca-enterprise-operations.md)
- [請求先アカウントへのアクセスを管理する](understand-mca-roles.md)