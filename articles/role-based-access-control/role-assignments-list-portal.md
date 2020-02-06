---
title: Azure RBAC と Azure portal を使用してロールの割り当てを一覧表示する
description: Azure ロールベースのアクセス制御 (RBAC) と Azure portal を使用して、ユーザー、グループ、サービス プリンシパル、およびマネージド IDがアクセスできるリソースを決定する方法について説明します。
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
ms.date: 01/23/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 099cf74dd27f39a4289397d5178511125d9ebf6f
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/24/2020
ms.locfileid: "76720722"
---
# <a name="list-role-assignments-using-azure-rbac-and-the-azure-portal"></a>Azure RBAC と Azure portal を使用してロールの割り当てを一覧表示する

[!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control-definition-list.md)] この記事では、Azure portal を使用してロールの割り当てを一覧表示する方法を説明します。

> [!NOTE]
> 組織が、[Azure の委任されたリソース管理](../lighthouse/concepts/azure-delegated-resource-management.md)を使用するサービス プロバイダーに管理機能を外部委託している場合、そのサービス プロバイダーによって承認されているロールの割り当てはここに表示されません。

## <a name="list-role-assignments-for-a-user-or-group"></a>ユーザーまたはグループのロールの割り当てを一覧表示する

サブスクリプションのユーザーまたはグループに割り当てられているロールを最も簡単に確認する方法は、 **[Azure リソース]** ペインを使用することです。

1. Azure portal で、 **[すべてのサービス]** をクリックしてから **[ユーザー]** または **[グループ]** を選択します。

1. ロールの割り当てを一覧表示するユーザーまたはグループをクリックします。

1. **[Azure リソース]** をクリックします。

    管理グループ、サブスクリプション、リソース グループ、リソースなどのさまざまなスコープで、選択したユーザーまたはグループに割り当てられているロールの一覧が表示されます。 この一覧では、自分が読み取りアクセス許可を認められているすべてのロールの割り当てが表示されます。

    ![ユーザーのロールの割り当て](./media/role-assignments-list-portal/azure-resources-user.png)    

1. サブスクリプションを変更するには、 **[サブスクリプション]** の一覧をクリックします。

## <a name="list-owners-of-a-subscription"></a>サブスクリプションの所有者を一覧表示する

サブスクリプションの[所有者](built-in-roles.md#owner)ロールが割り当てられているユーザーは、サブスクリプション内のすべてを管理できます。 サブスクリプションの所有者を一覧表示するには、次の手順に従います。

1. Azure portal で、 **[すべてのサービス]** 、 **[サブスクリプション]**  の順にクリックします。

1. 所有者を一覧表示するサブスクリプションをクリックします。

1. **[アクセス制御 (IAM)]** をクリックします。

1. **[ロールの割り当て]** タブをクリックして、このサブスクリプションのすべてのロールの割り当てを表示します。

1. **[所有者]** セクションまでスクロールして、このサブスクリプションの所有者ロールが割り当てられているすべてのユーザーを表示します。

   ![サブスクリプションへのアクセスの制御 - [ロールの割り当て] タブ](./media/role-assignments-list-portal/access-control-role-assignments-subscription.png)

## <a name="list-role-assignments-at-a-scope"></a>あるスコープのロールの割り当てを一覧表示する

1. Azure portal で、 **[すべてのサービス]**  をクリックしてからスコープを選びます。 たとえば、 **[管理グループ]** 、 **[サブスクリプション]** 、 **[リソース グループ]** 、またはリソースを選択できます。

1. 特定のリソースをクリックします。

1. **[アクセス制御 (IAM)]** をクリックします。

1. **[ロールの割り当て]** タブをクリックして、このスコープのすべてのロールの割り当てを表示します。

   ![アクセス制御 - [ロールの割り当て] タブ](./media/role-assignments-list-portal/access-control-role-assignments.png)

   [ロールの割り当て] タブでは、このスコープにアクセス権があるユーザーを確認できます。 スコープが **[このリソース]** のロールもあれば、別のスコープからスコープを **[(継承)]** しているロールもあることに注目してください。 アクセス権は、このリソースに明確に割り当てられるか、または親スコープへの割り当てから継承されます。

## <a name="list-role-assignments-for-a-user-at-a-scope"></a>あるスコープのユーザーのロールの割り当てを一覧表示する

ユーザー、グループ、サービス プリンシパル、またはマネージド ID のアクセス権を一覧表示するには、ロールの割り当てを一覧表示します。 特定のスコープで単独のユーザー、グループ、サービス プリンシパル、またはマネージド ID のロールの割り当てを一覧表示するには、次の手順のようにします。

1. Azure portal で、 **[すべてのサービス]**  をクリックしてからスコープを選びます。 たとえば、 **[管理グループ]** 、 **[サブスクリプション]** 、 **[リソース グループ]** 、またはリソースを選択できます。

1. 特定のリソースをクリックします。

1. **[アクセス制御 (IAM)]** をクリックします。

1. **[アクセスの確認]** タブをクリックします。

    ![アクセス制御 - [アクセスの確認] タブ](./media/role-assignments-list-portal/access-control-check-access.png)

1. **[検索]** 一覧で、アクセス権を確認するセキュリティ プリンシパルの種類を選択します。

1. 検索ボックスに、表示名、メール アドレス、またはオブジェクト識別子のディレクトリを検索するための文字列を入力します。

    ![[アクセスの確認] の選択リスト](./media/role-assignments-list-portal/check-access-select.png)

1. セキュリティ プリンシパルをクリックして **[割り当て]** ウィンドウを開きます。

    ![[割り当て] ウィンドウ](./media/role-assignments-list-portal/check-access-assignments.png)

    このウィンドウでは、選択したセキュリティ プリンシパルに割り当てられているロールとスコープを確認できます。 このスコープに拒否割り当てがある場合、またはこのスコープに継承されている場合は、それが表示されます。

## <a name="list-role-assignments-for-a-system-assigned-managed-identity"></a>システム割り当てマネージド ID のロールの割り当てを一覧表示する

1. Azure portal で、システム割り当てマネージド ID を開きます。

1. 左側のメニューで、 **[ID]** をクリックします。

    ![システム割り当てマネージド ID](./media/role-assignments-list-portal/identity-system-assigned.png)

1. **[ロールの割り当て]** で、 **[このマネージド ID に割り当てられた Azure RBAC ロールを表示します]** をクリックします。

    管理グループ、サブスクリプション、リソース グループ、リソースなどのさまざまなスコープで、選択したシステム割り当てマネージド ID に割り当てられているロールの一覧が表示されます。 この一覧では、自分が読み取りアクセス許可を認められているすべてのロールの割り当てが表示されます。

    ![システム割り当てマネージド ID のロールの割り当て](./media/role-assignments-list-portal/azure-resources-system-assigned.png)

## <a name="list-role-assignments-for-a-user-assigned-managed-identity"></a>ユーザー割り当てマネージド ID のロールの割り当てを一覧表示する

1. Azure portal で、システム割り当てマネージド ID を開きます。

1. **[Azure リソース]** をクリックします。

    管理グループ、サブスクリプション、リソース グループ、リソースなどのさまざまなスコープで、選択したユーザー割り当てマネージド ID に割り当てられているロールの一覧が表示されます。 この一覧では、自分が読み取りアクセス許可を認められているすべてのロールの割り当てが表示されます。

    ![システム割り当てマネージド ID のロールの割り当て](./media/role-assignments-list-portal/azure-resources-user-assigned.png)

1. サブスクリプションを変更するには、 **[サブスクリプション]** の一覧をクリックします。

## <a name="list-number-of-role-assignments"></a>ロールの割り当ての数の一覧

各サブスクリプションには、最大 **2,000** 個のロールの割り当てを保持できます。 **[ロールの割り当て]** タブには、これらの制限の追跡に役立つ、現在のロールの割り当ての数を示すグラフが表示されます。

![アクセス制御 - ロールの割り当ての数のグラフ](./media/role-assignments-list-portal/access-control-role-assignments-chart.png)

最大数に近づいているときにロールの割り当てを追加しようとすると、 **[ロールの割り当ての追加]** ウィンドウに警告が表示されます。 不要になったロールの割り当てを削除したり、個々のユーザーではなくグループにロールを割り当てることで、ロールの割り当ての数を減らすことができます。

![アクセス制御 - ロールの割り当ての追加の警告](./media/role-assignments-list-portal/add-role-assignment-warning.png)

## <a name="next-steps"></a>次のステップ

- [Azure RBAC と Azure Portal を使用してロールの割り当てを追加または削除する](role-assignments-portal.md)
- [Azure リソースの RBAC のトラブルシューティング](troubleshooting.md)
