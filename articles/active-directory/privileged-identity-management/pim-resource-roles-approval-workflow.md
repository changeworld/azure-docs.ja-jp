---
title: Privileged Identity Management - Azure リソース ロールの承認ワークフロー | Microsoft Docs
description: Azure リソースの承認ワークフロー プロセスについて説明します。
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/02/2018
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: c02d595d75b2d63558896054c185102ebb23cc9e
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2018
---
# <a name="privileged-identity-management---resource-roles---approve"></a>Privileged Identity Management - リソース ロール - 承認

Azure リソース ロール用の PIM での承認ワークフローにより、管理者はロールの割り当てをアクティブにするために承認を要求することで、重要なリソースへのアクセスの保護または制限を強化することができます。 Azure リソース ロールに固有の概念として、リソース階層があります。 この階層により、親リソース オブジェクトから親コンテナー内のすべての従属子リソースに、ロールの割り当てを継承できます。 

たとえば、リソース管理者の Bob が、PIM を使用して Alice を有資格メンバーとして Contoso サブスクリプションの所有者ロールに割り当てます。 この割り当てにより、Alice は Contoso サブスクリプション内のすべてのリソース グループ コンテナーと、Contoso サブスクリプションの各リソース グループに含まれるすべてのリソース (仮想マシンなど) の有資格所有者にもなります。 Contoso サブスクリプションには、Fabrikam Test、Fabrikam Dev、Fabrikam Prod という、3 つのリソース グループがあると仮定しましょう。これらの各リソース グループには、単一の仮想マシンが含まれています。

PIM 設定はリソースのロールごとに構成されます。割り当てとは異なり、PIM 設定は継承されず、厳密にリソース ロールに適用されます。 [有資格割り当てとリソースの可視性に関する詳細を参照してください。](pim-resource-roles-eligible-visibility.md)

上記の例を使用して、Bob が PIM を使用し、Contoso サブスクリプションの所有者ロール内のすべてのメンバーに対して、アクティブ化のために承認依頼を要求します。 Fabrikam Prod リソース グループに含まれるリソースを保護するために、Bob はこのリソースの所有者ロールのメンバーにも承認を要求します。 Fabrikam Test と Fabrikam Dev の所有者ロールには、アクティブ化のために承認を要求しません。

Alice が Contoso サブスクリプションの所有者ロールのアクティブ化を要求すると、Alice がロール内でアクティブになる前に、承認者が Alice の要求を承認または拒否する必要があります。 また、Alice が Fabrikam Prod リソース グループを[アクティブ化のスコープに設定](pim-resource-roles-activate-your-roles.md#just-enough-administration)した場合、承認者はこの要求も承認または拒否する必要があります。 ただし、Alice が Fabrikam Test と Fabrikam Dev のいずれかまたは両方をアクティブ化のスコープに設定した場合、承認は要求されません。

承認ワークフローがロールのすべてのメンバーに必要でない場合もあります。 Azure サブスクリプションで実行されるアプリケーションの開発を支援するために、組織が複数の契約担当者を雇用するシナリオを考えてみましょう。 リソース管理者として、従業員は承認を必要とせずに有資格のアクセスができるようにして、契約担当者は承認の要求を必要とするようにしたいと考えています。 契約担当者のみの承認ワークフローを構成するには、従業員に割り当てられているロールと同じ権限を持つカスタム ロールを作成し、そのカスタム ロールをアクティブにするために承認を要求することができます。 [カスタム ロールの詳細については、こちらを参照してください](pim-resource-roles-custom-role-policy.md)。

以下の手順に従い承認ワークフローを構成し、誰が要求を承認または拒否できるかを指定します。

## <a name="require-approval-to-activate"></a>アクティブ化に承認を要求する

Azure Portal で PIM に移動し、一覧からリソースを選択します。

![](media/azure-pim-resource-rbac/aadpim_manage_azure_resource_some_there.png)

左側のナビゲーション メニューから、**[ロールの設定]** を選択します。

ロールを検索して選択し、**[編集]** をクリックして設定を変更します。

![](media/azure-pim-resource-rbac/aadpim_rbac_role_settings_view_settings.png)

アクティブ化のセクションで、**[アクティブにするには承認が必要です]** チェック ボックスをオンにします。

![](media/azure-pim-resource-rbac/aadpim_rbac_settings_require_approval_checkbox.png)

## <a name="specify-approvers"></a>承認者を指定する

**[承認者の選択]** をクリックして選択画面を開きます。

>[!NOTE]
>設定を更新するには、少なくとも 1 人のユーザーまたは 1 つのグループを選択する必要があります。 既定の承認者はいません。

リソース管理者は、承認者の一覧にユーザーとグループの任意の組み合わせを追加できます。 

![](media/azure-pim-resource-rbac/aadpim_rbac_role_settings_select_approvers.png)

## <a name="request-approval-to-activate"></a>アクティブ化のために承認を要求する

承認を要求しても、アクティブ化のためにメンバーが従う必要がある手順には影響しません。 [ロールをアクティブにするための手順を確認してください](pim-resource-roles-activate-your-roles.md)。

メンバーが承認を必要とするロールのアクティブ化を要求し、そのロールが必要ではなくなった場合、メンバーは PIM で要求をキャンセルできます。

キャンセルするには、PIM に移動し、[申請] を選択します。 要求を見つけて、[キャンセル] をクリックします。

![](media/azure-pim-resource-rbac/aadpim_rbac_role_approval_request_pending.png)

## <a name="approve-or-deny-a-request"></a>要求を承認または拒否する

要求を承認または拒否するには、承認者一覧のメンバーである必要があります。 PIM で、左側のナビゲーション メニューのタブから [申請の承認] を選択し、要求を見つけます。

![](media/azure-pim-resource-rbac/aadpim_rbac_approve_requests_list.png)

要求を選択して、決定の理由を入力し、承認または拒否を選択して、要求を解決します。

![](media/azure-pim-resource-rbac/aadpim_rbac_approve_request_approved.png)

## <a name="workflow-notifications"></a>ワークフロー通知

- ロールの要求のレビューが保留中の場合、承認者一覧のすべてのメンバーに電子メールで通知されます。 電子メール通知には、承認者が承認または拒否できる、要求への直接リンクが含まれています。
- 要求は、承認または拒否した一覧の最初のメンバーによって解決されます。 
- 承認者が要求に応答すると、承認者一覧のすべてのメンバーにアクションが通知されます。 
- リソース管理者には、承認されたメンバーがロール内でアクティブになったときに通知されます。 

>[!Note]
>リソース管理者が承認されたメンバーをアクティブにしないほうがよいと見なした場合は、PIM でアクティブなロールの割り当てを削除できます。 承認者一覧のメンバーでない限り、保留中の要求はリソース管理者には通知されませんが、リソース管理者は PIM で保留中の要求を表示することで、すべてのユーザーの保留中の要求を確認およびキャンセルできます。 

## <a name="next-steps"></a>次の手順

[固有のユーザー グループに PIM 設定を適用する](pim-resource-roles-custom-role-policy.md)
