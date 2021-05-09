---
title: Azure の予約を表示および管理するためのアクセス許可
description: Azure portal で Azure の予約を表示および管理する方法について説明します。
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 04/15/2021
ms.author: banders
ms.openlocfilehash: fe2f36b08f98ceb2a5f6085510b589a712ff194d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/20/2021
ms.locfileid: "107780463"
---
# <a name="permissions-to-view-and-manage-azure-reservations"></a>Azure の予約を表示および管理するためのアクセス許可

この記事では、予約のアクセス許可のしくみと、ユーザーが Azure portal で Azure の予約を表示および管理する方法について説明します。

## <a name="who-can-manage-a-reservation-by-default"></a>既定で予約を管理できるユーザー

既定では、次のユーザーが予約を表示および管理できます。

- 予約を購入した担当者と、予約の購入に使用した課金サブスクリプションのアカウント管理者が、予約注文に追加されます。
- マイクロソフト エンタープライズ契約および Microsoft 顧客契約の課金管理者。
- すべての Azure サブスクリプションと管理グループを管理するための昇格されたアクセス権を持つユーザー

予約のライフサイクルは Azure サブスクリプションに依存しないため、予約は Azure サブスクリプションに属するリソースではありません。 そうではなく、サブスクリプションとは別の独自の Azure RBAC アクセス許可があるテナントレベルのリソースです。 予約には、購入後にサブスクリプションからアクセス許可が継承されません。

## <a name="how-billing-administrators-can-view-or-manage-reservations"></a>課金管理者が予約を表示または管理する方法

課金管理者の場合は、次の手順を使用して、すべての予約と予約トランザクションを表示および管理します。

1. [Azure portal](https://portal.azure.com) にサインインし、 **[Cost Management + Billing]** に移動します。
    - EA 管理者の場合は、左側のメニューで **[課金スコープ]** を選択し、課金スコープの一覧でスコープを選択します。
    - Microsoft 顧客契約の課金プロファイル所有者の場合は、左側のメニューで **[課金プロファイル]** を選択します。 課金プロファイルの一覧でプロファイルを選択します。
1. 左側のメニューで、 **[製品とサービス]**  >  **[予約]** を選択します。
1. EA 加入契約または課金プロファイルの予約の完全な一覧が表示されます。
1. 課金管理者は、予約を選択し、表示されたウィンドウで **[アクセスの付与]** を選択することで、予約の所有権を取得できます。

### <a name="how-to-add-billing-administrators"></a>課金管理者を追加する方法

ユーザーを課金管理者としてマイクロソフト エンタープライズ契約または Microsoft 顧客契約に追加するには、次の手順を実行します。

- マイクロソフト エンタープライズ契約の場合は、"_エンタープライズ管理者_" ロールを持つユーザーを追加することで、マイクロソフト エンタープライズ契約に適用されるすべての予約注文を表示および管理できます。 エンタープライズ管理者は、 **[コストの管理と請求]** で予約を表示および管理できます。
    - "_エンタープライズ管理者 (読み取り専用)_ " ロールを持つユーザーは、 **[コストの管理と請求]** から予約の表示のみを実行できます。 
    - 部門管理者およびアカウント所有者は、アクセス制御 (IAM) を使用して明示的にこれらに追加されて "_いない限り_"、予約を表示することはできません。 詳細については、「[Azure エンタープライズ ロールの管理](../manage/understand-ea-roles.md)」を参照してください。
- Microsoft 顧客契約の場合は、課金プロファイル所有者ロールまたは課金プロファイル共同作成者ロールを持つユーザーが、課金プロファイルを使用して行われたすべての予約購入を管理できます。 課金プロファイル閲覧者と請求書管理者は、請求プロファイルを使用して支払われるすべての予約を表示できます。 ただし、予約に変更を加えることはできません。
    詳細については、「[課金プロファイルのロールとタスク](../manage/understand-mca-roles.md#billing-profile-roles-and-tasks)」を参照してください。

## <a name="view-reservations-with-azure-rbac-access"></a>Azure RBAC アクセス権がある予約を表示する

予約を購入した場合、または自分が予約に追加された場合は、次の手順を使用して予約を表示および管理します。

1. [Azure portal](https://portal.azure.com) にサインインします。
1. **[すべてのサービス]**  >  **[予約]** の順に選択し、自分にアクセス権がある予約を一覧表示します。

## <a name="users-with-elevated-access-can-manage-all-azure-subscriptions-and-management-groups"></a>昇格されたアクセス権を持つユーザーは、すべての Azure サブスクリプションと管理グループを管理できる

ユーザーの[アクセス権を昇格して、すべての Azure サブスクリプションと管理グループを管理](../../role-based-access-control/elevate-access-global-admin.md?toc=/azure/cost-management-billing/reservations/toc.json)できます。

アクセス権を昇格した後:

1. **[すべてのサービス]**  >  **[予約]** に移動して、テナント内のすべての予約を表示します。
1. 予約に変更を加えるには、アクセス制御 (IAM) を使用して自分自身を予約注文の所有者として追加します。

## <a name="give-users-azure-rbac-access-to-individual-reservations"></a>ユーザーに個々の予約への Azure RBAC アクセス権を付与する

予約の所有者アクセス権を持つユーザーと課金管理者は、個々の予約注文のアクセス管理を委任できます。

1. [Azure portal](https://portal.azure.com) にサインインします。
1. **[すべてのサービス]**  >  **[予約]** の順に選択し、自分にアクセス権がある予約を一覧表示します。
1. どの予約のアクセス権を他のユーザーに委任するかを選択します。
1. [予約の詳細] で、予約注文を選択します。
1. **[アクセス制御 (IAM)]** を選択します。
1. **[ロールの割り当ての追加]**  >  **[ロール]**  >  **[所有者]** を選択します。 制限付きアクセス権を付与する場合は、別のロールを選択します。
1. 所有者として追加するユーザーの電子メール アドレスを入力します。
1. ユーザーを選択し、 **[保存]** を選択します。

## <a name="next-steps"></a>次のステップ

- [Azure の予約を管理する](manage-reserved-vm-instance.md)。