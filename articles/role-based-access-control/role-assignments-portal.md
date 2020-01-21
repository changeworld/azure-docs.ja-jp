---
title: Azure RBAC と Azure portal を使用してロールの割り当てを追加または削除する
description: Azure のロールベースのアクセス制御 (RBAC) と Azure portal を使用して、ユーザー、グループ、サービス プリンシパル、またはマネージド ID に対して Azure リソースへのアクセス権を付与する方法について説明します。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 8078f366-a2c4-4fbb-a44b-fc39fd89df81
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/25/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 17a325e96e9709b60da2f23d1dc68e3300fde80c
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/03/2019
ms.locfileid: "74707865"
---
# <a name="add-or-remove-role-assignments-using-azure-rbac-and-the-azure-portal"></a>Azure RBAC と Azure portal を使用してロールの割り当てを追加または削除する

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control-definition-grant.md)] この記事では、Azure portal を使用してロールを割り当てる方法について説明します。

Azure Active Directory で管理者ロールを割り当てる必要がある場合、「[Azure Active Directory で管理者ロールを表示して割り当てる](../active-directory/users-groups-roles/directory-manage-roles-portal.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

ロールの割り当てを追加または削除するには、以下が必要です。

- `Microsoft.Authorization/roleAssignments/write` および `Microsoft.Authorization/roleAssignments/delete` のアクセス許可 ([ユーザー アクセス管理者](built-in-roles.md#user-access-administrator)や[所有者](built-in-roles.md#owner)など)

## <a name="overview-of-access-control-iam"></a>アクセス制御 (IAM) の概要

**[アクセス制御 (IAM)]** は、ロールの割り当てに使用するブレードです。 ID とアクセス管理とも呼ばれ、Azure portal のいくつかの場所に示されます。 サブスクリプションの [アクセス制御 (IAM)] ブレードの例を次に示します。

![サブスクリプションの [アクセス制御 (IAM)] ブレード](./media/role-assignments-portal/access-control-subscription.png)

[アクセス制御 (IAM)] ブレードを最も効果的に使用するには、ロールを割り当てる際に、次の 3 つの質問に回答できると役立ちます。

1. **誰がアクセスを必要としていますか?**

    誰が、にあてはまるのはユーザー、グループ、サービス プリンシパル、またはマネージド ID になります。 これは *セキュリティ プリンシパル* とも呼ばれます。

1. **それらには、どのようなロールが必要ですか?**

    アクセス許可はロールでまとめてグループ化されます。 複数の[組み込みロール](built-in-roles.md)の一覧から選択することも、独自のカスタム ロールを使用することもできます。

1. **どこでアクセスが必要ですか?**

    どこで、にあてはまるのはアクセスが適用されるリソースのセットになります。 管理グループ、サブスクリプション、リソース グループ、またはストレージ アカウントなどの単一のリソースである場合があります。 これは*スコープ* と呼ばれます。

## <a name="add-a-role-assignment"></a>ロールの割り当てを追加する

さまざまなスコープでロールを割り当てるには、次の手順に従います。

1. Azure portal で、 **[すべてのサービス]** をクリックしてからスコープを選びます。 たとえば、 **[管理グループ]** 、 **[サブスクリプション]** 、 **[リソース グループ]** 、またはリソースを選択できます。

1. 特定のリソースをクリックします。

1. **[アクセス制御 (IAM)]** をクリックします。

1. **[ロールの割り当て]** タブをクリックして、このスコープのすべてのロールの割り当てを表示します。

1. **[追加]**  >  **[ロールの割り当ての追加]** をクリックして、[ロールの割り当ての追加] ウィンドウを開きます。

   ロールを割り当てるためのアクセス許可がない場合は、[ロールの割り当ての追加] オプションは無効になります。

   ![[追加] メニュー](./media/role-assignments-portal/add-menu.png)

   ![[ロールの割り当ての追加] ウィンドウ](./media/role-assignments-portal/add-role-assignment.png)

1. **[ロール]** ボックスの一覧で、 **[仮想マシン共同作成者]** などのロールを選択します。

1. **[選択]** 一覧で、ユーザー、グループ、サービス プリンシパル、またはマネージド ID を選択します。 一覧にセキュリティ プリンシパルが表示されない場合には、 **[選択]** ボックスに表示名、メール アドレス、オブジェクト識別子を入力してディレクトリを検索します。

1. **[保存]** をクリックしてロールを割り当てます。

   しばらくすると、セキュリティ プリンシパルに選択されたスコープのロールが割り当てられます。

## <a name="assign-a-user-as-an-administrator-of-a-subscription"></a>サブスクリプションの管理者としてユーザーを割り当てる

ユーザーを Azure サブスクリプションの管理者にするには、サブスクリプション スコープで、そのユーザーに[所有者](built-in-roles.md#owner)ロールを割り当てます。 所有者ロールにより、他のユーザーにアクセス権を付与する権限を含め、サブスクリプションにあるすべてのリソースへのフル アクセス権がユーザーに付与されます。 次の手順は、他のロールの割り当てと同じです。

1. Azure portal で、 **[すべてのサービス]** 、 **[サブスクリプション]** の順にクリックします。

1. ロールの割り当てを追加するサブスクリプションをクリックします。

1. **[アクセス制御 (IAM)]** をクリックします。

1. **[ロールの割り当て]** タブをクリックして、このサブスクリプションのすべてのロールの割り当てを表示します。

1. **[追加]**  >  **[ロールの割り当ての追加]** をクリックして、[ロールの割り当ての追加] ウィンドウを開きます。

   ロールを割り当てるためのアクセス許可がない場合は、[ロールの割り当ての追加] オプションは無効になります。

   ![[追加] メニュー](./media/role-assignments-portal/add-menu.png)

   ![[ロールの割り当ての追加] ウィンドウ](./media/role-assignments-portal/add-role-assignment.png)

1. **[ロール]** ドロップダウン リストで、 **[所有者]** ロールを選択します。

1. **[選択]** 一覧で、ユーザーを選択します。 一覧にユーザーが表示されない場合には、 **[選択]** ボックスに表示名とメール アドレスを入力して、ディレクトリを検索します。

1. **[保存]** をクリックしてロールを割り当てます。

   しばらくすると、サブスクリプション スコープで、ユーザーに所有者のロールが割り当てられます。

## <a name="remove-a-role-assignment"></a>ロールの割り当てを削除する

RBAC では、アクセス権を削除するにはロールの割り当てを削除する必要があります。 ロールの割り当てを削除するには、次の手順に従います。

1. アクセス権を削除する管理グループ、サブスクリプション、リソース グループ、リソースなどのスコープで **[アクセス制御 (IAM)]** を開きます。

1. **[ロールの割り当て]** タブをクリックして、このサブスクリプションのすべてのロールの割り当てを表示します。

1. ロールの割り当ての一覧で、ロールの割り当てを削除するセキュリティ プリンシパルの隣にチェックマークを追加します。

   ![ロールの割り当ての削除メッセージ](./media/role-assignments-portal/remove-role-assignment-select.png)

1. **[削除]** をクリックします。

   ![ロールの割り当ての削除メッセージ](./media/role-assignments-portal/remove-role-assignment.png)

1. 表示されるロールの割り当ての削除メッセージで、 **[はい]** をクリックします。

    継承されたロールの割り当ては削除できません。 そのようなロールの割り当てを削除する場合には、ロールの割り当てが作成されたスコープで削除する必要があります。 **[スコープ]** 列の **[(継承済み)]** の横に、このロールが割り当てられているスコープへのリンクが表示されています。 その一覧に表示されているスコープにアクセスして、ロールの割り当てを削除してください。

   ![ロールの割り当ての削除メッセージ](./media/role-assignments-portal/remove-role-assignment-inherited.png)

## <a name="next-steps"></a>次の手順

- [Azure RBAC と Azure portal を使用してロールの割り当てを一覧表示する](role-assignments-list-portal.md)
- [チュートリアル:RBAC と Azure portal を使用して Azure リソースへのアクセス権をユーザーに付与する](quickstart-assign-role-user-portal.md)
- [Azure リソースの RBAC のトラブルシューティング](troubleshooting.md)
- [Azure 管理グループでリソースを整理する](../governance/management-groups/overview.md)
