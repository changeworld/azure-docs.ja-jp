---
title: Azure portal を使用して外部ゲスト ユーザーに Azure ロールを割り当てる - Azure RBAC
description: Azure portal と Azure ロールベースのアクセス制御 (Azure RBAC) を使用して、組織外のユーザーに Azure リソースへのアクセス権を付与する方法について説明します。
services: active-directory
documentationcenter: ''
author: rolyon
manager: daveba
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: how-to
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 10/15/2021
ms.author: rolyon
ms.custom: it-pro,subject-rbac-steps
ms.openlocfilehash: 808e8e1dc5bcdcdddad490f30338c8f7a9e7ffa3
ms.sourcegitcommit: 37cc33d25f2daea40b6158a8a56b08641bca0a43
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2021
ms.locfileid: "130074268"
---
# <a name="assign-azure-roles-to-external-guest-users-using-the-azure-portal"></a>Azure portal を使用して外部ゲスト ユーザーに Azure ロールを割り当てる

[Azure ロールベースのアクセス制御 (Azure RBAC)](overview.md) を使用すると、環境内の特定のリソースへのアクセスが必要なものの、インフラストラクチャ全体や課金に関連するスコープへのアクセスが必ずしも必要ではない外部のコラボレーター、ベンダー、フリーランサーと連携している大企業や中小企業向けの、優れたセキュリティ管理を実現できます。 [Azure Active Directory B2B](../active-directory/external-identities/what-is-b2b.md) の機能を使用して外部ゲスト ユーザーと共同作業を行うことができます。また、Azure RBAC を使用して、自分の環境でゲスト ユーザーに必要なアクセス許可のみを付与することができます。

## <a name="prerequisites"></a>前提条件

Azure ロールを割り当てたりロール割り当てを削除したりするには、以下が必要です。

- `Microsoft.Authorization/roleAssignments/write` および `Microsoft.Authorization/roleAssignments/delete` のアクセス許可 ([ユーザー アクセス管理者](built-in-roles.md#user-access-administrator)や[所有者](built-in-roles.md#owner)など)

## <a name="when-would-you-invite-guest-users"></a>ゲスト ユーザーを招待するタイミング

ここでは、ゲスト ユーザーを組織に招待し、アクセス許可を付与するシナリオ例をいくつか示します。

- プロジェクトの Azure リソースにアクセスするために、電子メール アカウントしか持っていない外部の自営仕入先を許可します。
- 外部パートナーが特定のリソースまたはサブスクリプション全体を管理することを許可します。
- 組織外のサポート エンジニア (Microsoft サポートなど) に、問題をトラブルシューティングしてもらうために Azure リソースに一時的にアクセスすることを許可します。

## <a name="permission-differences-between-member-users-and-guest-users"></a>メンバー ユーザーとゲスト ユーザーのアクセス許可の違い

ディレクトリのネイティブ メンバー (メンバー ユーザー) は、B2B コラボレーション ゲスト (ゲスト ユーザー) として別のディレクトリから招待されたユーザーとは異なるアクセス許可を持ちます。 たとえば、メンバー ユーザーはほとんどすべてのディレクトリ情報を読み取ることができますが、ゲスト ユーザーにはディレクトリのアクセス許可が制限されています。 メンバー ユーザーとゲスト ユーザーの詳細については、「[Azure Active Directory の既定のユーザー アクセス許可とは](../active-directory/fundamentals/users-default-permissions.md)」を参照してください。

## <a name="add-a-guest-user-to-your-directory"></a>ゲスト ユーザーをディレクトリに追加する

Azure Active Directory ページを使用してディレクトリにゲスト ユーザーを追加するには、次の手順を実行します。

1. [Azure portal](https://portal.azure.com) にサインインします。

1. 組織の外部コラボレーション設定が、ゲストを招待できるように構成されていることを確認します。 詳細については、「[B2B 外部コラボレーションを有効にしてゲストを招待できるユーザーを管理する](../active-directory/external-identities/delegate-invitations.md)」を参照してください。

1. **[Azure Active Directory]**  >  **[ユーザー]**  >  **[新しいゲスト ユーザー]** の順にクリックします。

    ![Azure portal の [新しいゲスト ユーザー] 機能のスクリーンショット。](./media/role-assignments-external-users/invite-guest-user.png)

1. 手順に従って新しいゲスト ユーザーを追加します。 詳細については、「[Azure portal で Azure Active Directory B2B コラボレーション ユーザーを追加する](../active-directory/external-identities/add-users-administrator.md#add-guest-users-to-the-directory)」を参照してください。

ゲスト ユーザーをディレクトリに追加すると、共有アプリへの直接リンクをゲスト ユーザーに送信するか、ゲスト ユーザーが招待メール内の招待の承諾リンクをクリックできます。

![ゲスト ユーザー招待メールのスクリーンショット。](./media/role-assignments-external-users/invite-email.png)

ゲスト ユーザーがディレクトリにアクセスできるようになるには、招待プロセスを完了する必要があります。

![ゲスト ユーザー招待のアクセス許可の確認のスクリーンショット](./media/role-assignments-external-users/invite-review-permissions.png)

招待プロセスの詳細については、「[Azure Active Directory B2B コラボレーションの招待の利用](../active-directory/external-identities/redemption-experience.md)」を参照してください。

## <a name="assign-a-role-to-a-guest-user"></a>ゲスト ユーザーにロールを割り当てる

Azure RBAC でアクセス権を付与するには、ロールを割り当てます。 ゲスト ユーザーにロールを割り当てるには、メンバー ユーザー、グループ、サービス プリンシパル、またはマネージド ID の場合と[同じ手順](role-assignments-portal.md)を実行します。 次の手順に従って、異なるスコープのゲスト ユーザーにロールを割り当てます。

1. [Azure portal](https://portal.azure.com) にサインインします。

1. 上部にある検索ボックスで、アクセス権を付与するスコープを検索します。 たとえば、 **[管理グループ]** 、 **[サブスクリプション]** 、 **[リソース グループ]** 、または特定のリソースを検索します。

1. そのスコープの特定のリソースをクリックします。

1. **[アクセス制御 (IAM)]** をクリックします。

    リソース グループの [アクセス制御 (IAM)] ページの例を次に示します。

    ![リソース グループの [アクセス制御 (IAM)] ページのスクリーンショット。](./media/shared/rg-access-control.png)

1. **[ロールの割り当て]** タブをクリックして、このスコープのロールの割り当てを表示します。

1. **[追加]**  >  **[ロールの割り当ての追加]** をクリックします。

    ロールを割り当てるためのアクセス許可がない場合は、[ロールの割り当ての追加] オプションは無効になります。

    ![[追加] > [ロールの割り当ての追加] メニューのスクリーンショット。](./media/shared/add-role-assignment-menu.png)

    [ロールの割り当ての追加] ページが開きます。

1. **[ロール]** タブで、 **[仮想マシン共同作成者]** などのロールを選択します。

   ![[ロールの割り当ての追加] ページで [ロール] タブが表示された状態のスクリーンショット。](./media/shared/roles.png)

1. **[メンバー]** タブで、 **[User, group, or service principal]\(ユーザー、グループ、またはサービス プリンシパル\)** を選択します。

   ![[ロールの割り当ての追加] ページで [メンバー] タブが表示された状態のスクリーンショット。](./media/shared/members.png)

1. **[メンバーの選択]** をクリックします。

1. ゲストユーザーを検索して選択します。 一覧にユーザーが表示されない場合には、 **[選択]** ボックスに表示名またはメール アドレスを入力してディレクトリを検索します。

    **[選択]** ボックスに表示名またはメール アドレスを入力してディレクトリを検索できます。

    ![[メンバーの選択] ウィンドウのスクリーンショット。](./media/role-assignments-external-users/select-members.png)

1. **[選択]** をクリックして、メンバーの一覧にゲスト ユーザーを追加します。

1. **[Review + assign]\(確認と割り当て\)** タブで **[Review + assign]\(確認と割り当て\)** をクリックします。

    しばらくすると、選択されたスコープで、ゲスト ユーザーにロールが割り当てられます。

    ![仮想マシン共同作成者のロールの割り当てのスクリーンショット。](./media/role-assignments-external-users/access-control-role-assignments.png)

## <a name="assign-a-role-to-a-guest-user-not-yet-in-your-directory"></a>ディレクトリにまだ存在しないゲスト ユーザーにロールを割り当てる

ゲスト ユーザーにロールを割り当てるには、メンバー ユーザー、グループ、サービス プリンシパル、またはマネージド ID の場合と[同じ手順](role-assignments-portal.md)を実行します。

ゲスト ユーザーがディレクトリにまだ存在しない場合は、[メンバーの選択] ペインからユーザーを直接招待できます。

1. [Azure portal](https://portal.azure.com) にサインインします。

1. 上部にある検索ボックスで、アクセス権を付与するスコープを検索します。 たとえば、 **[管理グループ]** 、 **[サブスクリプション]** 、 **[リソース グループ]** 、または特定のリソースを検索します。

1. そのスコープの特定のリソースをクリックします。

1. **[アクセス制御 (IAM)]** をクリックします。

1. **[追加]**  >  **[ロールの割り当ての追加]** をクリックします。

    ロールを割り当てるためのアクセス許可がない場合は、[ロールの割り当ての追加] オプションは無効になります。

    ![[追加] > [ロールの割り当ての追加] メニューのスクリーンショット。](./media/shared/add-role-assignment-menu.png)

    [ロールの割り当ての追加] ページが開きます。

1. **[ロール]** タブで、 **[仮想マシン共同作成者]** などのロールを選択します。

1. **[メンバー]** タブで、 **[User, group, or service principal]\(ユーザー、グループ、またはサービス プリンシパル\)** を選択します。

   ![[ロールの割り当ての追加] ページで [メンバー] タブが表示された状態のスクリーンショット。](./media/shared/members.png)

1. **[メンバーの選択]** をクリックします。

1. **[選択]** ボックスに、招待する相手のメール アドレスを入力し、その人物を選択します。

    ![[メンバーの選択] ペインでゲスト ユーザーを招待する画面のスクリーンショット。](./media/role-assignments-external-users/select-members-new-guest.png)

1. **[選択]** をクリックして、メンバーの一覧にゲスト ユーザーを追加します。

1. **[Review + assign]\(確認と割り当て\)** タブの **[Review + assign]\(確認と割り当て\)** をクリックして、ゲスト ユーザーをディレクトリに追加し、ロールを割り当てて招待状を送信します。

    しばらくすると、ロールの割り当ての通知と、招待に関する情報が表示されます。

    ![ロールの割り当てと招待されたユーザーへの通知のスクリーンショット](./media/role-assignments-external-users/invited-user-notification.png)

1. ゲスト ユーザーを手動で招待するには、右クリックして、通知の招待リンクをコピーします。 招待プロセスを開始するため、招待リンクをクリックしないでください。

    招待リンクの形式は次のとおりです。

    `https://login.microsoftonline.com/redeem?rd=https%3a%2f%2finvitations.microsoft.com%2fredeem%2f%3ftenant%3d0000...`

1. ゲスト ユーザーに招待リンクを送信して招待プロセスを完了します。

    招待プロセスの詳細については、「[Azure Active Directory B2B コラボレーションの招待の利用](../active-directory/external-identities/redemption-experience.md)」を参照してください。

## <a name="remove-a-guest-user-from-your-directory"></a>ディレクトリからゲスト ユーザーを削除する

ディレクトリからゲスト ユーザーを削除する前に、まずそのゲスト ユーザーのすべてのロールの割り当てを削除する必要があります。 ディレクトリからゲスト ユーザーを削除するには、次の手順を実行します。

1. ゲスト ユーザーにロールが割り当てられている管理グループ、サブスクリプション、リソース グループ、リソースなどのスコープで **アクセス制御 (IAM)** を開きます。

1. **[ロールの割り当て]** タブをクリックして、すべてのロールの割り当てを表示します。

1. ロールの割り当ての一覧で、ロールの割り当てを削除するゲスト ユーザーの隣にチェックマークを追加します。

   ![削除するために選択されたロールの割り当てのスクリーンショット。](./media/role-assignments-external-users/remove-role-assignment-select.png)

1. **[削除]** をクリックします。

   ![ロールの割り当ての削除メッセージのスクリーンショット。](./media/shared/remove-role-assignment.png)

1. ロールの割り当ての削除メッセージが表示されたら、 **[はい]** をクリックします。

1. 左側のナビゲーション バーで、 **[Azure Active Directory]**  >  **[ユーザー]** をクリックします。

1. 削除するゲスト ユーザーをクリックします。

1. **[削除]** をクリックします。

   ![ゲスト ユーザーの削除のスクリーンショット。](./media/role-assignments-external-users/delete-guest-user.png)

1. 表示される削除メッセージで、 **[はい]** をクリックします。

## <a name="troubleshoot"></a>トラブルシューティング

### <a name="guest-user-cannot-browse-the-directory"></a>ゲスト ユーザーがディレクトリを参照できません

ゲスト ユーザーは、ディレクトリ アクセス許可を制限されています。 たとえば、ゲスト ユーザーはディレクトリを参照することができず、グループやアプリケーションを検索することができません。 詳細については、「[Azure Active Directory の既定のユーザー アクセス許可とは](../active-directory/fundamentals/users-default-permissions.md)」を参照してください。

![ゲスト ユーザーはディレクトリ内のユーザーを参照できないことを示すスクリーンショット。](./media/role-assignments-external-users/directory-no-users.png)

ゲスト ユーザーがディレクトリで追加の特権を必要とする場合は、ゲスト ユーザーに Azure AD ロールを割り当てることができます。 ゲスト ユーザーにディレクトリへのフル読み取りアクセス権を付与する場合は、Azure AD の[ディレクトリ閲覧者](../active-directory/roles/permissions-reference.md#directory-readers)ロールにゲスト ユーザーを追加できます。 詳細については、「[Azure portal で Azure Active Directory B2B コラボレーション ユーザーを追加する](../active-directory/external-identities/add-users-administrator.md)」を参照してください。

![ディレクトリ閲覧者ロールを割り当てる画面のスクリーンショット。](./media/role-assignments-external-users/directory-roles.png)

### <a name="guest-user-cannot-browse-users-groups-or-service-principals-to-assign-roles"></a>ゲスト ユーザーは、ユーザー、グループ、またはサービス プリンシパルを参照してロールを割り当てることはできません

ゲスト ユーザーは、ディレクトリ アクセス許可を制限されています。 ゲスト ユーザーがあるスコープでの[所有者](built-in-roles.md#owner)であっても、ロールを割り当てて他のユーザーにアクセス権を付与しようとする場合に、ユーザー、グループ、またはサービス プリンシパルの一覧を参照することはできません。

![ゲスト ユーザーがセキュリティ プリンシパルを参照してロールを割り当てることはできないことを示すスクリーンショット。](./media/role-assignments-external-users/directory-no-browse.png)

ゲスト ユーザーは、ディレクトリ内のユーザーの正確なサインイン名を知っていれば、アクセス権を付与することができます。 ゲスト ユーザーにディレクトリへのフル読み取りアクセス権を付与する場合は、Azure AD の[ディレクトリ閲覧者](../active-directory/roles/permissions-reference.md#directory-readers)ロールにゲスト ユーザーを追加できます。 詳細については、「[Azure portal で Azure Active Directory B2B コラボレーション ユーザーを追加する](../active-directory/external-identities/add-users-administrator.md)」を参照してください。

### <a name="guest-user-cannot-register-applications-or-create-service-principals"></a>ゲスト ユーザーがアプリケーションを登録できません、またはサービス プリンシパルを作成できません

ゲスト ユーザーは、ディレクトリ アクセス許可を制限されています。 ゲスト ユーザーがアプリケーションを登録できる、またはサービス プリンシパルを作成できる必要がある場合は、Azure AD の[アプリケーション開発者](../active-directory/roles/permissions-reference.md#application-developer)ロールにゲスト ユーザーを追加します。 詳細については、「[Azure portal で Azure Active Directory B2B コラボレーション ユーザーを追加する](../active-directory/external-identities/add-users-administrator.md)」を参照してください。

![ゲスト ユーザーはアプリケーションを登録できないことを示すスクリーンショット。](./media/role-assignments-external-users/directory-access-denied.png)

### <a name="guest-user-does-not-see-the-new-directory"></a>ゲスト ユーザーに新しいディレクトリが表示されません

ゲスト ユーザーにディレクトリへのアクセス権が付与されていても、 **[ディレクトリ]** ページで切り替えようとしたときに Azure portal の一覧に新しいディレクトリが表示されない場合は、ゲスト ユーザーが招待プロセスを完了していることを確認してください。 招待プロセスの詳細については、「[Azure Active Directory B2B コラボレーションの招待の利用](../active-directory/external-identities/redemption-experience.md)」を参照してください。

### <a name="guest-user-does-not-see-resources"></a>ゲスト ユーザーにリソースが表示されません

ゲスト ユーザーにディレクトリへのアクセスが許可されていても、Azure portal でアクセス権を付与されているリソースが表示されない場合は、ゲスト ユーザーが正しいディレクトリを選択していることを確認してください。 ゲスト ユーザーは、複数のディレクトリにアクセス権を持つ場合があります。 ディレクトリを切り替えるには、左上にある **[設定]**  >  **[ディレクトリ]** をクリックし、適切なディレクトリをクリックします。

![Azure portal の [ポータルの設定] の [ディレクトリ] セクションのスクリーンショット。](./media/role-assignments-external-users/directory-switch.png)

## <a name="next-steps"></a>次のステップ

- [Azure Portal で Azure Active Directory B2B コラボレーション ユーザーを追加する](../active-directory/external-identities/add-users-administrator.md)
- [Azure Active Directory B2B コラボレーション ユーザーのプロパティ](../active-directory/external-identities/user-properties.md)
- [B2B コラボレーションの招待メールの要素 - Azure Active Directory](../active-directory/external-identities/invitation-email-elements.md)
- [ゲスト ユーザーを共同管理者として追加する](classic-administrators.md#add-a-guest-user-as-a-co-administrator)