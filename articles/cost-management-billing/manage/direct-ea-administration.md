---
title: Azure portal での直接 Enterprise Agreement の管理
description: この記事では、直接エンタープライズ管理者が Azure portal で行う一般的なタスクについて説明します。
author: bandersmsft
ms.author: banders
ms.date: 11/16/2021
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: enterprise
ms.reviewer: sapnakeshari
ms.openlocfilehash: 0cd07afa3b5bb63d017443ee4bc9de5e21f652ff
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132718566"
---
# <a name="azure-portal-administration-for-direct-enterprise-agreements"></a>Azure portal での直接 Enterprise Agreement の管理

この記事では、直接 Enterprise Agreement (EA) 管理者が [Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_GTM/ModernBillingMenuBlade/AllBillingScopes) で行う一般的なタスクについて説明します。 直接 Enterprise Agreement は、Microsoft と Enterprise Agreement のお客様の間で取り交わされます。

これに対し、間接 EA は、お客様と Microsoft パートナーの間で取り交わされる契約です。 間接 EA は、[Azure Enterprise Portal](https://ea.azure.com/) を使用して管理します。 間接 EA の管理の詳細については、「[Azure EA Portal の管理](ea-portal-administration.md)」を参照してください。

## <a name="manage-your-enrollment"></a>登録を管理する

サービスを管理するには、最初のエンタープライズ管理者が  [Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_GTM/ModernBillingMenuBlade/AllBillingScopes) を開き、招待メールのメール アドレスを使用してサインインします。

自分がエンタープライズ管理者としてセットアップされている場合は、Azure portal にアクセスし、職場、学校、または Microsoft アカウントのメール アドレスとパスワードを使用してサインインします。

複数の課金アカウントがある場合は、課金スコープのメニューから課金アカウントを選択します。 課金アカウントのプロパティとポリシーは、左側のメニューから表示できます。

「[EA 管理者が登録を管理する](https://www.youtube.com/watch?v=NUlRrJFF1_U)」ビデオをご覧ください。 これは、[Azure portal でのダイレクト Enterprise のお客様の請求エクスペリエンス](https://www.youtube.com/playlist?list=PLeZrVF6SXmsoHSnAgrDDzL0W5j8KevFIm) シリーズ ビデオの一部です。

>[!VIDEO https://www.youtube.com/embed/NUlRrJFF1_U]

## <a name="select-a-billing-scope"></a>課金スコープを選択する

Enterprise Agreement と契約にアクセスするお客様は、複数の登録を行うことができます。 ユーザーは、複数の登録スコープ (課金アカウント スコープ) にアクセスできます。 Azure portal のすべての情報とアクティビティは、課金アカウント スコープのコンテキスト内にあります。 エンタープライズ管理者は最初に課金スコープを選択してから、管理タスクを行うことが重要です。

### <a name="to-select-a-billing-scope"></a>課金スコープを選択するには

1. [Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_GTM/ModernBillingMenuBlade/AllBillingScopes) にサインインします。
1. **[Cost Management + Billing]** を見つけて選択します。  
    :::image type="content" source="./media/direct-ea-administration/search-cost-management.png" alt-text="[Cost Management + Billing] の検索を示すスクリーンショット。" lightbox="./media/direct-ea-administration/search-cost-management.png" :::
1. ナビゲーション メニューから **[課金スコープ]** を選択した後、使用する課金アカウントを選択します。  
    :::image type="content" source="./media/direct-ea-administration/select-billing-scope.png" alt-text="課金アカウントの選択を示すスクリーンショット。" lightbox="./media/direct-ea-administration/select-billing-scope.png" :::

## <a name="view-enrollment-details"></a>登録の詳細を表示する

Azure エンタープライズ管理者 (EA 管理者) は、登録のプロパティとポリシーを表示して管理し、登録の設定が正しく構成されていることを確保できます。

### <a name="to-view-enrollment-properties"></a>登録のプロパティを表示するには

1. [Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_GTM/ModernBillingMenuBlade/AllBillingScopes) にサインインします。
1. **[コストの管理と請求]** に移動します。
1. 左側のメニューで **[課金スコープ]** を選択し、課金アカウントのスコープを選択します。
1. ナビゲーション メニューの **[設定]** で、 **[プロパティ]** を選択します。
1. 課金アカウントの情報を表示します。  
    :::image type="content" source="./media/direct-ea-administration/billing-account-properties.png" alt-text="課金アカウントのプロパティを示すスクリーンショット。" lightbox="./media/direct-ea-administration/billing-account-properties.png" :::

### <a name="view-and-manage-enrollment-policies"></a>登録ポリシーを表示して管理する

1. [Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_GTM/ModernBillingMenuBlade/AllBillingScopes) にサインインします。
1. **[コストの管理と請求]** に移動します。
1. 左側のメニューで **[課金スコープ]** を選択し、課金アカウントのスコープを選択します。
1. ナビゲーション メニューで、 **[ポリシー]** を選択します。
1. **[オン]** または **[オフ]** を選択して、ポリシーを有効または無効にします。  
    :::image type="content" source="./media/direct-ea-administration/enrollment-policies.png" alt-text="EA 登録ポリシーを示すスクリーンショット。" lightbox="./media/direct-ea-administration/enrollment-policies.png" :::

部門管理者 (DA) とアカウント オーナー (AO) の料金ポリシー設定の詳細については、[さまざまなユーザー ロールの価格](understand-ea-roles.md#see-pricing-for-different-user-roles)に関する記事を参照してください。

## <a name="add-another-enterprise-administrator"></a>別のエンタープライズ管理者を追加する

他のエンタープライズ管理者を作成できるのは、既存の EA 管理者だけです。 実際の状況に基づいて、次のいずれかのオプションを使用します。

### <a name="if-youre-already-an-enterprise-administrator"></a>既にエンタープライズ管理者となっている場合

1. [Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_GTM/ModernBillingMenuBlade/AllBillingScopes) にサインインします。
1. **[コストの管理と請求]** に移動します。
1. 左側のメニューで **[課金スコープ]** を選択し、課金アカウントのスコープを選択します。
1. 左側のメニューで、 **[アクセス制御 (IAM)]** を選択します。
1. 上部のメニューで **[+ 追加]** を選択して、 **[エンタープライズ管理者]** を選択します。  
    :::image type="content" source="./media/direct-ea-administration/add-enterprise-admin-navigate.png" alt-text="エンタープライズ管理者への移動を示すスクリーンショット。" lightbox="./media/direct-ea-administration/add-enterprise-admin-navigate.png" :::
1. ロール割り当て追加フォームの項目をすべて指定して、 **[追加]** を選択します。

ユーザーのメール アドレスと、職場、学校、または Microsoft アカウントなどの適切な優先認証方法が設定されていることを確認します。

EA 管理者は、各ユーザーの右にある省略記号 **[...]** を選択して、既存のエンタープライズ管理者のアクセスを管理できます。 既存のユーザーを **[編集]** および **[削除]** できます。

### <a name="if-youre-not-an-enterprise-administrator"></a>まだエンタープライズ管理者となっていない場合

EA 管理者でない場合は、EA 管理者に登録への追加を依頼してください。 EA 管理者が、前の手順を使用して、そのユーザーをエンタープライズ管理者として追加します。 加入契約に追加された後、アクティブ化の電子メールを受け取ります。

### <a name="if-your-enterprise-administrator-cant-help-you"></a>エンタープライズ管理者のサポートが得られない場合

エンタープライズ管理者が支援できない場合は、 [Azure のサポート リクエスト](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)を作成します。 次の情報を指定します。

- 加入契約番号
- 追加するメール アドレスと認証の種類 (職場、学校、または Microsoft アカウント)
- 既存のエンタープライズ管理者からのメールの承認

既存のエンタープライズ管理者を利用できない場合は、パートナーまたはソフトウェア アドバイザーに連絡して、ボリューム ライセンス サービス センター (VLSC) ツールで連絡先の詳細を変更するように依頼します。

## <a name="create-an-azure-enterprise-department"></a>Azure エンタープライズ部署を作成する

EA 管理者と部署管理者は部署を使用して、エンタープライズ Azure サービスと使用状況を部門およびコスト センター別に整理してレポートします。 エンタープライズ管理者は、次のことができます。

- 部署を追加または削除する。
- アカウントを部署に関連付ける。
- 部署管理者を作成する。
- 部署管理者に価格とコストの表示を許可する。

部門管理者は、部門に新しいアカウントを追加できます。 部署からアカウントを削除することもできますが、登録からは削除できません。

「[EA 管理者が部署を管理する](https://www.youtube.com/watch?v=NUlRrJFF1_U)」ビデオをご覧ください。 これは、[Azure portal でのダイレクト Enterprise のお客様の請求エクスペリエンス](https://www.youtube.com/playlist?list=PLeZrVF6SXmsoHSnAgrDDzL0W5j8KevFIm) シリーズ ビデオの一部です。

>[!VIDEO https://www.youtube.com/embed/cxAtOSSE6UI]

### <a name="to-create-a-department"></a>部署を作成するには

1. [Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_GTM/ModernBillingMenuBlade/AllBillingScopes) にサインインします。
1. **[コストの管理と請求]** に移動します。
1. 左側のメニューで **[課金スコープ]** を選択し、課金アカウントのスコープを選択します。
1. 左側のナビゲーション メニューで、 **[部門]** を選択します。
1. **[+ 追加]** を選択します。  
    :::image type="content" source="./media/direct-ea-administration/add-department.png" alt-text="部署を追加するためのナビゲーションを示すスクリーンショット。" lightbox="./media/direct-ea-administration/add-department.png" :::
1. [新しい部署の追加] フォームで、情報を入力します。 部署名は唯一の必須フィールドです。 これは 3 文字以上である必要があります。
1. 完了したら、**[保存]** を選択します。

## <a name="add-a-department-administrator"></a>部署管理者を追加する

部署を作成した後、EA 管理者は部署管理者を追加し、それぞれを部署に関連付けることができます。 部署管理者は、部署に関する次の作業を行うことができます。

- 他の部門管理者を作成する
- 名前やコスト センターなどの部署のプロパティを表示および編集する
- アカウントの追加
- アカウントを削除する
- 使用量の詳細をダウンロードする
- 毎月の使用量と料金を表示する<sup>1</sup>

 <sup>1</sup> EA 管理者はアクセス許可を付与する必要があります。

### <a name="to-add-a-department-administrator"></a>部署管理者を追加するには

エンタープライズ管理者として:

1. [Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_GTM/ModernBillingMenuBlade/AllBillingScopes) にサインインします。
1. **[コストの管理と請求]** に移動します。
1. 左側のメニューで **[課金スコープ]** を選択し、課金アカウントのスコープを選択します。
1. ナビゲーション メニューで、 **[部門]** を選択します。
1. 管理者を追加する部署を選択します。
1. 部署ビューで **[アクセス制御 (IAM)]** を選択します。
1. **[+ 追加]** を選択して、 **[部署管理者]** を選択します。
1. メール アドレスと他の必要な情報を入力します。
1. 読み取り専用アクセスの場合は、 **[読み取り専用]** オプションを **[はい]** に設定して、 **[追加]** を選択します。  
    :::image type="content" source="./media/direct-ea-administration/add-department-admin.png" alt-text="部署管理者への移動を示すスクリーンショット。" lightbox="./media/direct-ea-administration/add-department-admin.png" :::

### <a name="to-set-read-only-access"></a>読み取り専用アクセス権を設定するには

EA 管理者は、部門管理者に読み取り専用アクセス権を付与することができます。 新しい部署管理者を作成するには、読み取り専用オプションを **[はい]** に設定します。

既存の部門管理者を編集するには:

1. 行の右側にある省略記号 **[...]** を選択します。
1. 読み取り専用オプションを   **[はい]** に設定して、 **[適用]** を選択します。

エンタープライズ管理者には、部署管理者のアクセス許可が自動的に設定されます。

## <a name="add-an-account-and-account-owner"></a>アカウントとアカウント オーナーを追加する

アカウントとサブスクリプションの構造は、管理方法と、請求書やレポートでの表示方法に影響します。 一般的な組織の構造の例としては、事業部、機能チーム、地理的な場所などがあります。

新しいアカウントが登録に追加された後、所有権の確認に使用されるアカウント所有権メールがアカウント オーナーに送信されます。

「[EA 管理者がアカウントを管理する](https://www.youtube.com/watch?v=VKWAEx6qfPc)」ビデオをご覧ください。 これは、[Azure portal でのダイレクト Enterprise のお客様の請求エクスペリエンス](https://www.youtube.com/playlist?list=PLeZrVF6SXmsoHSnAgrDDzL0W5j8KevFIm) シリーズ ビデオの一部です。

>[!VIDEO https://www.youtube.com/embed/VKWAEx6qfPc]

### <a name="to-add-an-account-and-account-owner"></a>アカウントとアカウント オーナーを追加するには

1. [Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_GTM/ModernBillingMenuBlade/AllBillingScopes) にサインインします。
1. **[コストの管理と請求]** に移動します。
1. 左側のメニューで **[課金スコープ]** を選択し、課金アカウントのスコープを選択します。
1. 左側のメニューで **[アカウント]** を選択します。
1. **[+ 追加]** を選択します。
1. [アカウントの追加] ページで、レポートに使用されるアカウントを識別するフレンドリ名を入力します。
1. 新しいアカウントに関連付ける **[アカウント所有者のメール]** アドレスを入力します。
1. 部署を選択するか、未割り当てのままにします。
1. 完了したら、 **[追加]** を選択します。

### <a name="to-confirm-account-ownership"></a>アカウントの所有権を確認するには

アカウント オーナーは、アカウント所有権のメールを受け取った後、所有権を確認する必要があります。

1. アカウント オーナーとして設定された職場、学校、または Microsoft アカウントに関連付けられているメール アカウントにサインインします。
1. "_Invitation to Activate your Account on the Microsoft Azure Service_" (Microsoft Azure Service でアカウントをアクティブ化するための招待) という件名のメール通知を開きます。
1. 招待の **[アカウントの有効化]** リンクを選択します。
1. Azure portal にサインインします。
1. [アカウントの有効化] ページで、 **[Yes, I wish to continue]\(はい、続けます\)** を選択してアカウントの所有権の確認を続けます。  
    :::image type="content" source="./media/direct-ea-administration/activate-account.png" alt-text="[アカウントの有効化] ページを示すスクリーンショット。" lightbox="./media/direct-ea-administration/activate-account.png" :::

アカウントの所有権を確認した後は、サブスクリプションを作成し、そのサブスクリプションでリソースを購入できます。

## <a name="change-azure-subscription-or-account-ownership"></a>Azure サブスクリプションまたはアカウントの所有権を変更する

EA 管理者は、Azure portal を使用して、加入契約に含まれるサブスクリプション (選択されたサブスクリプションまたはすべてのサブスクリプション) のアカウント所有権を移転できます。 サブスクリプションまたはアカウント所有権の譲渡が完了すると、Microsoft によってアカウント所有者が更新されます。

所有権の移転を始める前に、次の Azure ロールベースのアクセス制御 (Azure RBAC) ポリシーについて理解を深めてください。

- 同じテナント内の 2 つの組織 ID 間でサブスクリプションまたはアカウント所有権の移転を行う場合、次の項目は保持されます。
    - Azure RBAC ポリシー
    - 既存のサービス管理者
    - 共同管理者ロール
- 異なるテナント間で行うサブスクリプションまたはアカウント所有権の譲渡では、Azure RBAC ポリシーとロールの割り当てが失われます。
- ポリシーと管理者ロールが、異なるディレクトリ間で転送されることはありません。 サービス管理者は、ターゲット アカウントの所有者に更新されます。
- テナント間でサブスクリプションを移転するときに、Azure RBAC のポリシーやロールの割り当てが失われるのを防ぐため、 **[サブスクリプションを受信者の Azure AD テナントに移動します]** は確実にオフのままにします。 そうすることで、現在の Azure AD テナントにあるサービス、Azure ロール、ポリシーは維持され、アカウントの課金所有権だけが移転されます。

アカウント所有者を変更する前に:

1. **[アカウント]** タブを表示し、ソース アカウントを特定します。 ソース アカウントはアクティブである必要があります。
1. ターゲット アカウントを特定し、それがアクティブであることを確認します。

すべてのサブスクリプションのアカウント所有権を転送するには:

1. [Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_GTM/ModernBillingMenuBlade/AllBillingScopes) にサインインします。
1. **[コストの管理と請求]** に移動します。
1. 左側のメニューで **[課金スコープ]** を選択し、課金アカウントのスコープを選択します。
1. 左側のメニューで **[アカウント]** を選択します。
1. **アカウント** を選択し、行の右側にある省略記号 **[...]** を選択します。
1. **[サブスクリプションの転送]** を選択します。  
    :::image type="content" source="./media/direct-ea-administration/transfer-subscriptions-01.png" alt-text="サブスクリプションを転送する場所を示すスクリーンショット。" lightbox="./media/direct-ea-administration/transfer-subscriptions-01.png" :::
1. [サブスクリプションの転送] ページで、移転先のアカウントを選択した後、 **[次へ]** を選択します。
1. Azure AD テナント間でアカウント所有権を移転したい場合は、 **[Yes, I would also like to move the subscriptions to the new account's Azure AD tenant]\(はい、サブスクリプションも新しいアカウントの Azure AD テナントに移動します\)** の確認を選択します。
1. 移転を確認して、 **[送信]** を選択します。  
    :::image type="content" source="./media/direct-ea-administration/transfer-account-confirmation.png" alt-text="サブスクリプション移転の確認を示すスクリーンショット。" lightbox="./media/direct-ea-administration/transfer-account-confirmation.png" :::

単一のサブスクリプションのアカウント所有権を転送するには:

1. [Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_GTM/ModernBillingMenuBlade/AllBillingScopes) にサインインします。
1. **[コストの管理と請求]** に移動します。
1. 左側のメニューで **[課金スコープ]** を選択し、課金アカウントのスコープを選択します。
1. 左側のメニューで、 **[Azure サブスクリプション]** を選択します。
1. [Azure サブスクリプション] ページで、サブスクリプションの右側にある省略記号 **[...]** を選択します。
1. **[サブスクリプションの譲渡]** を選択します。
1. [サブスクリプションの譲渡] ページで、サブスクリプション移転先のアカウントを選択した後、  **[次へ]** を選択します。
1. Azure AD テナント間でサブスクリプション所有権を移転したい場合は、 **[Yes, I would like to also move the subscriptions to the to the new account's Azure AD tenant]\(はい、サブスクリプションも新しいアカウントの Azure AD テナントに移動します\)** の確認を選択します。
1. 転送を確認してから、 **[送信]** を選択します。

## <a name="associate-an-account-to-a-department"></a>アカウントを部門に関連付ける

EA 管理者は、既存のアカウントを登録の部署に関連付けることができます。

### <a name="to-associate-an-account-to-a-department"></a>アカウントを部門に関連付ける方法

1. [Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_GTM/ModernBillingMenuBlade/AllBillingScopes) にサインインします。
1. **[コストの管理と請求]** に移動します。
1. 左側のメニューで **[課金スコープ]** を選択し、課金アカウントのスコープを選択します。
1. 左側のメニューで **[アカウント]** を選択します。
1. アカウントの右側にある省略記号 **[...]** を選択して、 **[編集]** を選択します。
1. [アカウントの編集] ページで、一覧から **[部署]** を選択します。
1.  **[保存]** を選択します。

## <a name="associate-an-account-with-a-pay-as-you-go-subscription"></a>アカウントを従量課金制サブスクリプションに関連付ける

Azure 従量課金制サブスクリプション用の学校、職場、または Microsoft アカウントを既に持っている場合は、それを Enterprise Agreement の登録に関連付けることができます。

アカウントを関連付けるには、EA 管理者または部署管理者ロールが必要です。

### <a name="to-associate-an-existing-account"></a>既存のアカウントを関連付けるには

1. [Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_GTM/ModernBillingMenuBlade/AllBillingScopes) にサインインします。
1. **[コストの管理と請求]** に移動します。
1. 左側のメニューで **[課金スコープ]** を選択し、課金アカウントのスコープを選択します。
1. 左側のメニューで **[アカウント]** を選択します。
1. **[+ 追加]** を選択します。
1. [アカウントの追加] ページで、レポートでアカウントを識別するために使用する名前を入力します。
1. 既存の Azure アカウントに関連付けられている **Microsoft** あるいは **職場または学校アカウント** を選択します。
1. アカウント オーナーのメール アドレスを入力し、確認します。
1. **[追加]** を選択します。

アカウントが Azure portal に表示されるまで、最大で 8 時間かかる場合があります。

### <a name="to-confirm-account-ownership"></a>アカウントの所有権を確認するには

1. 前の手順で関連付けた職場、学校、または Microsoft アカウントに関連付けられているメール アカウントにサインインします。
1. "_Invitation to Activate your Account on the Microsoft Azure Service_" (Microsoft Azure Service でアカウントをアクティブ化するための招待) という件名のメール通知を開きます。
1. 招待の **[アカウントの有効化]** リンクを選択します。
1. Azure portal にサインインします。
1. [アカウントの有効化] ページで、 **[Yes, I wish to continue]\(はい、続けます\)** を選択してアカウントの所有権の確認を続けます。  
    :::image type="content" source="./media/direct-ea-administration/activate-account.png" alt-text="[アカウントの有効化] ページを示すスクリーンショット。" lightbox="./media/direct-ea-administration/activate-account.png" :::

## <a name="enable-azure-marketplace-purchases"></a>Azure Marketplace での購入を有効にする

ほとんどの従量課金制 "_サブスクリプション_" は Azure Enterprise Agreement と関連付けることができますが、以前に購入した Azure Marketplace "_サービス_" はできません。 すべてのサブスクリプションと料金を 1 つのビューで表示するには、Azure Marketplace の購入を有効にすることをお勧めします。

1. [Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_GTM/ModernBillingMenuBlade/AllBillingScopes) にサインインします。
1. **[コストの管理と請求]** に移動します。
1. 左側のメニューで **[課金スコープ]** を選択し、課金アカウントのスコープを選択します。
1. 左側のメニューで **[ポリシー]** を選択します。
1. **[Azure Marketplace]** で、ポリシーを **[オン]** に設定します。  
   :::image type="content" source="./media/direct-ea-administration/azure-marketplace.png" alt-text="Azure Marketplace のポリシーの設定を示すスクリーンショット。" lightbox="./media/direct-ea-administration/azure-marketplace.png" :::

このようにすると、アカウント オーナーは、以前に従量課金制サブスクリプションで所有していた Azure Marketplace サービスを購入できるようになります。

この設定は、登録のすべてのアカウント オーナーに適用されます。 これにより、ユーザーは Azure Marketplace で購入できます。

Azure EA 登録でサブスクリプションをアクティブにした後、従量課金制サブスクリプションで作成された Azure Marketplace サービスをキャンセルします。 このステップは、従量課金制支払い方法の有効期限が切れた場合に重要です。

## <a name="msdn-subscription-transfer"></a>MSDN サブスクリプションの移転

MSDN サブスクリプションを EA 登録に移転すると、MSDN Dev/Test サブスクリプションに自動的に変換されます。 変換後、サブスクリプションの既存の金銭的クレジットは失われます。 そのため、Enterprise Agreement に移転する前に、クレジットを使い切ることをお勧めします。

## <a name="azure-in-open-subscription-transfer"></a>Azure イン オープン プランのサブスクリプションの移転

Azure イン オープン プランのサブスクリプションを Enterprise Agreement に移転すると、Azure イン オープン プランの未使用のクレジットはすべて失われます。 Enterprise Agreement に移転する前に、Azure イン オープン プランのサブスクリプションのクレジットを使い切ることをお勧めします。

## <a name="subscription-transfers-with-support-plans"></a>サポート プランを使用したサブスクリプションの移転

所有している Enterprise Agreement にサポート プランが存在しない場合、サポート プランが含まれる既存の Microsoft Online Support Agreement (MOSA) サブスクリプションを移転しようとすると、サブスクリプションは自動的に移転されません。 猶予期間 (次の月の終わりまで) の間に EA 登録用のサポート プランを再度購入する必要があります。

## <a name="manage-department-and-account-spending-with-budgets"></a>予算で部署とアカウントの支出を管理する

EA のお客様は、登録に含まれる各部署とアカウントに予算を設定できます。 Cost Management での予算は、組織のアカウンタビリティを計画および推進するのに役立ちます。 コストを事前に管理するために支出を他のユーザーに通知したり、支出の時間変化を監視したりするのに役立ちます。 実際のコストまたは予測コストに基づいてアラートを構成して、支出が組織の支出制限内に収まるようにすることができます。 作成した予算のしきい値を超えた場合は、通知のみがトリガーされます。 どのリソースも影響を受けることはなく、消費が停止されることはありません。 予算を使用して、コストを分析するときに支出を比較および追跡できます。 予算の作成方法の詳細については、「[チュートリアル: Azure の予算を作成して管理する](../costs/tutorial-acm-create-budgets.md)」を参照してください。

## <a name="enterprise-agreement-user-roles"></a>Enterprise Agreement のユーザー ロール

Azure portal は、Azure EA のコストと使用状況を管理するのに役立ちます。 3 つの主要な EA ロールがあります。

- Enterprise Agreement (EA) 管理者
- 部門管理者
- アカウント所有者

各ロールには、さまざまなレベルのアクセス権と特権があります。 ユーザー ロールの詳細については、「 [エンタープライズ ユーザー ロール](understand-ea-roles.md#enterprise-user-roles)」を参照してください。

## <a name="add-an-azure-ea-account"></a>Azure EA アカウントを追加する

Azure EA アカウントは、Azure portal における組織単位です。 Azure portal では、それは "_アカウント_" と呼ばれます。 これは、サブスクリプションの管理に使用されるほか、レポートにも使用されます。 Azure サービスにアクセスして使用するには、自分でアカウントを作成するか、誰かにアカウントを作成してもらう必要があります。 アカウントの詳細については、[アカウントの追加](#add-an-account-and-account-owner)に関するセクションを参照してください。

## <a name="enable-the-enterprise-devtest-offer"></a>Enterprise Dev/Test プランを有効にする

EA 管理者は、組織のアカウント オーナーによる EA Dev/Test プランに基づくサブスクリプションの作成を許可できます。 これを行うには、アカウントのプロパティで **[Dev/Test]** オプションを選択します。 [Dev/Test] オプションを選択した後、Dev/Test サブスクライバーのチームに必要な EA Dev/Test サブスクリプションを作成できることを、アカウント オーナーに通知します。 このプランにより、アクティブな Visual Studio サブスクライバーは、特別な Dev/Test 料金で Azure 上で開発およびテストのワークロードを実行できます。 Windows 8.1 および Windows 10 を含む Dev/Test イメージの完全なギャラリーへのアクセスが提供されます。

### <a name="to-set-up-the-enterprise-devtest-offer"></a>Enterprise Dev/Test プランを設定するには

1. [Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_GTM/ModernBillingMenuBlade/AllBillingScopes) にサインインします。
1. **[コストの管理と請求]** に移動します。
1. 左側のメニューで **[課金スコープ]** を選択し、課金アカウントのスコープを選択します。
1. 左側のメニューで **[アカウント]** を選択します。
1. Dev/Test へのアクセスを有効にするアカウントを選択します。
1. 登録アカウントのページで、 **[編集]** を選択します。
1. [アカウントの編集] ページで、 **[Dev/Test]** を選択してから **[保存]** を選択します。

ユーザーがアカウント オーナーとして追加されると、従量課金制 Dev/Test プランまたは Visual Studio サブスクライバー向けの月単位のクレジット プランのいずれかに基づいてそのユーザーに関連付けられているすべての Azure サブスクリプションは、EA Dev/Test プランに変換されます。 アカウント オーナーに関連付けられている従量課金制などの他の種類のプランに基づくサブスクリプションは、Microsoft Azure エンタープライズ プランに変換されます。

現在、Dev/Test プランは Azure Gov のお客様には適用されません。

## <a name="create-a-subscription"></a>サブスクリプションの作成

アカウント所有者は、サブスクリプションの表示と管理を行うことができます。 サブスクリプションを使用して、組織内のチームに開発環境やプロジェクトへのアクセスを許可できます。 次に例を示します。 

- テスト
- Production
- 開発
- ステージング

アプリケーション環境ごとに異なるサブスクリプションを作成すると、各環境のセキュリティ保護に役立ちます。

- また、サブスクリプションごとに異なるサービス管理者アカウントを割り当てることもできます。
- サブスクリプションを任意の数のサービスに関連付けることができます。
- アカウント所有者は、サブスクリプションを作成し、アカウント内の各サブスクリプションにサービス管理者アカウントを割り当てます。

「[EA 管理者がサブスクリプションを管理する](https://www.youtube.com/watch?v=KFfcg2eqPo8)」ビデオをご覧ください。 これは、[Azure portal でのダイレクト Enterprise のお客様の請求エクスペリエンス](https://www.youtube.com/playlist?list=PLeZrVF6SXmsoHSnAgrDDzL0W5j8KevFIm) シリーズ ビデオの一部です。

>[!VIDEO https://www.youtube.com/embed/KFfcg2eqPo8]

## <a name="add-a-subscription"></a>サブスクリプションの追加

アカウント オーナーは、自分の登録アカウント内にサブスクリプションを作成します。 アカウントにサブスクリプションを初めて追加するときに、マイクロソフト オンライン サブスクリプション契約 (MOSA) と料金プランに同意するように求められます。 これらは Enterprise Agreement のお客様には適用されませんが、MOSA と料金はプランはサブスクリプションを作成するために必要です。 Microsoft Azure Enterprise Agreement の登録変更は、上記の項目よりも優先され、契約関係は変更されません。 メッセージが表示されたら、条項に同意することを示すオプションを選択します。

サブスクリプションが作成されるときの既定の名前は、"_Microsoft Azure エンタープライズ_" です。 加入契約内の他のサブスクリプションと区別し、エンタープライズ レベルのレポートで認識できるようにするため、名前を変更してもかまいません。

### <a name="to-add-a-subscription"></a>サブスクリプションを追加するには

1. [Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_GTM/ModernBillingMenuBlade/AllBillingScopes) にサインインします。
1. **[コストの管理と請求]** に移動します。
1. 左側のメニューで **[課金スコープ]** を選択し、登録の課金アカウント スコープを選択します。
1. 左側のメニューで **[アカウント]** を選択します。
1. 登録アカウントの一覧で、サブスクリプションを作成するものを選択します。
1. 登録アカウントのページで、 **[+ 新規サブスクリプション]** を選択します。  
    :::image type="content" source="./media/direct-ea-administration/new-subscription.png" alt-text="新しいサブスクリプション オプションを示すスクリーンショット。" lightbox="./media/direct-ea-administration/new-subscription.png" :::
1. [サブスクリプションの作成] ページでサブスクリプションの名前を入力して、 **[作成]** を選択します。

サブスクリプション名はレポートに表示されます。 それは、開発ポータルでサブスクリプションに関連付けられているプロジェクトの名前です。

新しいサブスクリプションがサブスクリプション リストに表示されるまで、最大 24 時間かかることがあります。 サブスクリプションを作成した後、次のことができます。

- サブスクリプションの詳細を編集する
- サブスクリプション サービスを管理する

Azure サブスクリプションのページに移動して **[+ 追加]** を選択し、サブスクリプションを作成することもできます。

## <a name="cancel-a-subscription"></a>サブスクリプションを取り消す

自分のサブスクリプションを取り消すことができるのは、アカウント オーナーだけです。

アカウント オーナーであるサブスクリプションを削除するには:

1. アカウントに関連付けられている資格情報を使用して Azure portal にサインインします。
1. ナビゲーション メニューで、 **[サブスクリプション]** を選択します。
1. サブスクリプションを選択します。
1. サブスクリプションの詳細ページの左上隅にある **[サブスクリプションの取り消し]** を選択します。
1. サブスクリプションの名前を入力し、取り消す理由を選択して、 **[キャンセル]** を選択します。

詳細については、 [サブスクリプションの取り消し後の流れ](cancel-azure-subscription.md#what-happens-after-subscription-cancellation)に関するセクションを参照してください。

## <a name="delete-an-enrollment-account"></a>登録アカウントを削除する

アクティブなサブスクリプションがない場合にのみ、登録アカウントを削除できます。

1. [Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_GTM/ModernBillingMenuBlade/AllBillingScopes) にサインインします。
1. **[コストの管理と請求]** に移動します。
1. 左側のメニューで **[課金スコープ]** を選択し、課金アカウントのスコープを選択します。
1. 左側のメニューで **[アカウント]** を選択します。
1. [アカウント] の一覧で、削除するアカウントを検索します。
1. 削除するアカウントの行で、省略記号 **[…]** を選択してから、 **[削除]** を選択します。
1. [アカウントの削除] ページで、 **[Yes, I want to delete this account]\(はい、このアカウントを削除します\)** の確認を選択した後、 **[削除]** を選択します。

## <a name="azure-sponsorship-offer"></a>Azure スポンサー プラン

Azure スポンサー プランは、Microsoft Azure の限定スポンサー アカウントです。 Microsoft が選択した一部のお客様のみに送られる招待メールを通じて利用可能になります。 Microsoft Azure スポンサー プランの対象となるお客様には、ご使用のアカウント ID に電子メールの招待状が送信されます。

詳細については、 [スポンサー アクティブ化のサポート リクエスト](https://aka.ms/azrsponsorship)を作成してください。

## <a name="convert-to-work-or-school-account-authentication"></a>職場または学校アカウント認証に変換する

Azure エンタープライズのユーザーは、Microsoft アカウント (MSA または Live ID) から職場または学校アカウントに変換できます。 職場または学校アカウントでは、Azure Active Directory の認証の種類が使用されます。

### <a name="to-begin"></a>始めるには

1. 必要なロールで Azure portal に職場または学校アカウントを追加します。
1. エラーが発生する場合は、Azure Active Directory でアカウントが有効でないおそれがあります。 Azure では、ユーザー プリンシパル名 (UPN) が使用されますが、これは常にメール アドレスと同じであるとは限りません。
1. 職場または学校アカウントを使用して、Azure portal に対する認証を行います。

### <a name="to-convert-subscriptions-from-microsoft-accounts-to-work-or-school-accounts"></a>Microsoft アカウントから職場または学校アカウントにサブスクリプションを変換するには

1. サブスクリプションを所有している Microsoft アカウントを使用して、Azure portal にサインインします。
1. アカウント所有権の移転を使用して、新しいアカウントに移行します。
1. Microsoft アカウントがアクティブなサブスクリプションから解放され、削除できるようになります。
1. 過去の請求上の理由から、削除されたアカウントは、Azure portal に非アクティブ状態で引き続き表示されます。 **[Show only active accounts]\(アクティブなアカウントのみを表示\)** を選択することで、表示から除外できます。

## <a name="azure-ea-term-glossary"></a>Azure EA 用語集

**アカウント**<br>
組織単位。 サブスクリプションの管理とレポートのために使用されます。

**アカウント オーナー**<br>
Azure でサブスクリプションとサービス管理者を管理するユーザー。 このアカウントとそれに関連付けられているサブスクリプションの使用状況データを表示できます。

**変更契約サブスクリプション**<br>
加入契約変更契約書における 1 年間または同時終了サブスクリプション。

**前払い**<br>
この前払いに対する使用状況の割引前払い料金での Azure サービスの年額前払い料金。

**部署管理者**<br>
部署の管理、新しいアカウントとアカウント所有者の作成、管理対象部署の使用状況の詳細の表示、コストの表示 (アクセス許可が付与された場合) を行うユーザー。

**加入契約番号**<br>
Enterprise Agreement に関連付けられている特定の加入契約を識別するために Microsoft によって提供される一意の識別子。

**エンタープライズ管理者**<br>
Azure で部署、部署所有者、アカウント、アカウント所有者を管理するユーザー。 エンタープライズ管理者を管理し、エンタープライズ登録に関連付けられているすべてのアカウントとサブスクリプションの使用状況データ、請求数量、および未請求料金を表示することができます。

**エンタープライズ契約**<br>
Microsoft テクノロジで組織全体を標準化し、Microsoft のソフトウェア標準において情報技術を維持することを希望する、一元的な購入を行う顧客向けの Microsoft ライセンス契約。

**Enterprise Agreement 登録**<br>
Microsoft 製品を割引料金で大量に提供する、Enterprise Agreement プログラムの加入契約。

**Microsoft アカウント**<br>
複数のサイトにアクセスするときに、単一の資格情報を使用してユーザーを認証できるようにする Web ベース サービス。

**Microsoft Azure エンタープライズ登録変更 (登録変更)**<br>
エンタープライズ加入契約の一部として Azure へのアクセスを提供する、企業によって署名される変更契約書。

**消費済みリソース数量**<br>
1 か月間に使用された個別の Azure サービスの数量。

**サービス管理者**<br>
サブスクリプションと開発者プロジェクトにアクセスして管理するユーザー。

**サブスクリプション**<br>
Azure EA サブスクリプションを表し、同じサービス管理者によって管理される Azure サービスのコンテナーです。

**職場または学校アカウント**<br>
クラウドへのフェデレーションを使用して Azure Active Directory を設定しており、1 つのテナントにすべてのアカウントが存在する組織。

## <a name="enrollment-status"></a>登録ステータス

**[新規作成]**<br>
この状態は、過去 24 時間以内に作成された登録に割り当てられ、24 時間以内に保留中の状態に更新されます。

**保留中**<br>
登録管理者は Azure portal にサインインする必要があります。 サインインすると、加入契約がアクティブな状態に切り替わります。

**アクティブ**<br>
加入契約はアクティブであり、Azure portal でアカウントとサブスクリプションを作成できます。 加入契約は、Enterprise Agreement の終了日までアクティブな状態が維持されます。

**無期限の延長期間**<br>
Enterprise Agreement の終了日に達した後、無期限の延長期間の状態になります。 これにより、拡張期間にオプトインした Azure EA のお客様は、Enterprise Agreement の終了後に、Azure サービスを引き続き無期限に使用できます。

Azure EA 加入契約が Enterprise Agreement の終了日に達する前に、加入契約管理者は次のどのオプションを採用するかを決定する必要があります。

- さらに Azure 前払いを追加して登録を更新します。
- 新しい加入契約に転送する。
- マイクロソフト オンライン サブスクリプション プログラム (MOSP) に移行する。
- 登録に関連付けられているすべてのサービスが無効になることを確認する。

**Expired**<br>
Azure EA のお客様は延長期間からオプトアウトされ、Azure EA 加入契約は Enterprise Agreement の終了日に達しています。 加入登録は期限切れになり、関連付けられているすべてのサービスは無効になります。

**移転済み**<br>
関連付けられているすべてのアカウントとサービスが新しい加入契約に転送された加入契約は、転送済みの状態で表示されます。
 > [!NOTE]
 > 更新時に新しい登録番号が生成された場合、加入契約は自動的には転送されません。 自動転送を容易にするには、前の加入契約番号を、お客様の更新事務処理に含める必要があります。

## <a name="next-steps"></a>次のステップ

- EA 登録に関する Azure サポート リクエストを作成する必要がある場合は、[Enterprise Agreement の問題に関する Azure サポート リクエストを作成する方法](how-to-create-azure-support-request-ea.md)に関するページを参照してください。
- EA サブスクリプションの所有権に関する質問については、[Cost Management + Billing に関する FAQ](../cost-management-billing-faq.yml) を参照してください。