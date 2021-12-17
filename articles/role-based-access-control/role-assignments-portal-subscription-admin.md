---
title: Azure サブスクリプションの管理者としてユーザーを割り当てる - Azure RBAC
description: Azure portal と Azure ロールベースのアクセス制御 (Azure RBAC) を使用して、ユーザーを Azure サブスクリプションの管理者にする方法について説明します。
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 10/15/2021
ms.author: rolyon
ms.custom: subject-rbac-steps
ms.openlocfilehash: b4801d18454ad93c4ffa7284ab5bfaabea254cd1
ms.sourcegitcommit: 37cc33d25f2daea40b6158a8a56b08641bca0a43
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2021
ms.locfileid: "130074211"
---
# <a name="assign-a-user-as-an-administrator-of-an-azure-subscription"></a>Azure サブスクリプションの管理者としてユーザーを割り当てる

ユーザーを Azure サブスクリプションの管理者にするには、サブスクリプションのスコープで、そのユーザーに[所有者](built-in-roles.md#owner)ロールを割り当てます。 所有者ロールにより、他のユーザーにアクセス権を付与する権限を含め、サブスクリプションにあるすべてのリソースへのフル アクセス権がユーザーに付与されます。 次の手順は、他のロールの割り当てと同じです。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [Azure role assignment prerequisites](../../includes/role-based-access-control/prerequisites-role-assignments.md)]

## <a name="step-1-open-the-subscription"></a>手順 1: サブスクリプションを開く

1. [Azure portal](https://portal.azure.com) にサインインします。

1. 上部の検索ボックスで、サブスクリプションを検索します。

1. 使用するサブスクリプションをクリックします。

    次にサブスクリプションの例を示します。

    ![サブスクリプションの概要のスクリーンショット](./media/shared/sub-overview.png)

## <a name="step-2-open-the-add-role-assignment-page"></a>手順 2: [ロールの割り当ての追加] ページを開く

**アクセス制御 (IAM)** は、一般的には、ロールを割り当てて Azure リソースへのアクセスを付与するために使用するページです。 "ID とアクセス管理 (IAM)" とも呼ばれており、Azure portal のいくつかの場所に表示されます。

1. **[アクセス制御 (IAM)]** をクリックします。

    サブスクリプションの [アクセス制御 (IAM)] ページの例を次に示します。

    ![サブスクリプションの [アクセス制御 (IAM)] ページのスクリーンショット。](./media/shared/sub-access-control.png)

1. **[ロールの割り当て]** タブをクリックして、このスコープのロールの割り当てを表示します。

1. **[追加]**  >  **[ロールの割り当ての追加]** をクリックします。

   ロールを割り当てるためのアクセス許可がない場合は、[ロールの割り当ての追加] オプションは無効になります。

    ![[追加] > [ロールの割り当ての追加] メニューのスクリーンショット。](./media/shared/add-role-assignment-menu.png)

    [ロールの割り当ての追加] ページが開きます。

## <a name="step-3-select-the-owner-role"></a>手順 3: [所有者] ロールを選択する

Azure RBAC でロールを割り当てる権限を含め、すべてのリソースを管理するためのフル アクセスは、[所有者](built-in-roles.md#owner)ロールによって付与されます。 侵害された所有者による侵害の可能性を減らすため、サブスクリプション所有者は最大 3 人までにします。

1. **[ロール]** タブで **[所有者]** ロールを選択します。

    ロールは、名前または説明で検索できます。 種類とカテゴリでロールをフィルター処理することもできます。

   ![[ロールの割り当ての追加] ページで [ロール] タブが表示された状態のスクリーンショット。](./media/shared/roles.png)

1. **[次へ]** をクリックします。

## <a name="step-4-select-who-needs-access"></a>手順 4:アクセスを必要とするユーザーを選択する

1. **[メンバー]** タブで、 **[User, group, or service principal]\(ユーザー、グループ、またはサービス プリンシパル\)** を選択します。

   ![[ロールの割り当ての追加] ページで [メンバーの追加] タブが表示された状態のスクリーンショット。](./media/shared/members.png)

1. **[メンバーの選択]** をクリックします。

1. ユーザーを見つけて選択します。

    **[選択]** ボックスに表示名またはメール アドレスを入力してディレクトリを検索できます。

   ![[メンバーの選択] ウィンドウのスクリーンショット。](./media/shared/select-members.png)

1. **[保存]** をクリックして、メンバーの一覧にユーザーを追加します。

1. 必要に応じて、 **[説明]** ボックスにこのロール割り当ての説明を入力します。

    後で、ロールの割り当ての一覧にこの説明を表示できます。

1. **[次へ]** をクリックします。

## <a name="step-5-assign-role"></a>手順 5:ロールを割り当てる

1. **[Review + assign]\(確認と割り当て\)** タブで、ロールの割り当ての設定を確認します。

1. **[Review + assign]\(確認と割り当て\)** をクリックしてロールを割り当てます。

   しばらくすると、サブスクリプションの所有者のロールがユーザーに割り当てられます。

    ![ロールを割り当てた後のロールの割り当ての一覧のスクリーンショット。](./media/role-assignments-portal-subscription-admin/sub-role-assignments-owner.png)

## <a name="next-steps"></a>次のステップ

- [Azure portal を使用して Azure ロールを割り当てる](role-assignments-portal.md)
- [Azure portal を使用して Azure でのロールの割り当てを一覧表示する](role-assignments-list-portal.md)
- [Azure 管理グループでリソースを整理する](../governance/management-groups/overview.md)
