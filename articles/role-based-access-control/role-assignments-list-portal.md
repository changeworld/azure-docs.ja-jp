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
ms.date: 11/25/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 25e11ae1311df9d0392340b32e0691298f78ee1c
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/03/2019
ms.locfileid: "74709891"
---
# <a name="list-role-assignments-using-azure-rbac-and-the-azure-portal"></a>Azure RBAC と Azure portal を使用してロールの割り当てを一覧表示する

[!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control-definition-list.md)] この記事では、Azure portal を使用してロールの割り当てを一覧表示する方法を説明します。

## <a name="list-role-assignments-for-a-user-or-group"></a>ユーザーまたはグループのロールの割り当てを一覧表示する

サブスクリプションのユーザーまたはグループに割り当てられているロールを最も簡単に確認する方法は、 **[Azure リソース]** ペインを使用することです。

1. Azure portal で、 **[すべてのサービス]** をクリックしてから **[ユーザー]** または **[グループ]** を選択します。

1. ロールの割り当てを一覧表示するユーザーまたはグループをクリックします。

1. **[Azure リソース]** をクリックします。

    管理グループ、サブスクリプション、リソース グループ、リソースなどのさまざまなスコープで、選択したユーザーまたはグループに割り当てられているロールの一覧が表示されます。 この一覧では、自分が読み取りアクセス許可を認められているすべてのロールの割り当てが表示されます。

    ![ユーザーのロールの割り当て](./media/role-assignments-list-portal/azure-resources-user.png)    

1. サブスクリプションを変更するには、 **[サブスクリプション]** の一覧をクリックします。

## <a name="list-role-assignments-at-a-scope"></a>あるスコープのロールの割り当てを一覧表示する

1. Azure portal で、 **[すべてのサービス]** をクリックしてからスコープを選びます。 たとえば、 **[管理グループ]** 、 **[サブスクリプション]** 、 **[リソース グループ]** 、またはリソースを選択できます。

1. 特定のリソースをクリックします。

1. **[アクセス制御 (IAM)]** をクリックします。

1. **[ロールの割り当て]** タブをクリックして、このスコープのすべてのロールの割り当てを表示します。

   ![アクセス制御 - [ロールの割り当て] タブ](./media/role-assignments-list-portal/access-control-role-assignments.png)

   [ロールの割り当て] タブでは、このスコープにアクセス権があるユーザーを確認できます。 スコープが **[このリソース]** のロールもあれば、別のスコープからスコープを **[(継承)]** しているロールもあることに注目してください。 アクセス権は、このリソースに明確に割り当てられるか、または親スコープへの割り当てから継承されます。

## <a name="list-role-assignments-for-a-user-at-a-scope"></a>あるスコープのユーザーのロールの割り当てを一覧表示する

ユーザー、グループ、サービス プリンシパル、またはマネージド ID のアクセス権を一覧表示するには、ロールの割り当てを一覧表示します。 特定のスコープで単独のユーザー、グループ、サービス プリンシパル、またはマネージド ID のロールの割り当てを一覧表示するには、次の手順のようにします。

1. Azure portal で、 **[すべてのサービス]** をクリックしてからスコープを選びます。 たとえば、 **[管理グループ]** 、 **[サブスクリプション]** 、 **[リソース グループ]** 、またはリソースを選択できます。

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

## <a name="next-steps"></a>次の手順

- [Azure RBAC と Azure Portal を使用してロールの割り当てを追加または削除する](role-assignments-portal.md)
- [Azure リソースの RBAC のトラブルシューティング](troubleshooting.md)
