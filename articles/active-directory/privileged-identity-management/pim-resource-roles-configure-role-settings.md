---
title: PIM で Azure リソース ロールの設定を構成する | Microsoft Docs
description: Azure AD Privileged Identity Management (PIM) で Azure リソース ロールの設定を構成する方法について説明します。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: pim
ms.date: 04/02/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 901eb5ef43ddb2840ed7a3d83fc08f2f05849461
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/29/2018
ms.locfileid: "43189737"
---
# <a name="configure-azure-resource-role-settings-in-pim"></a>PIM で Azure リソース ロールの設定を構成する

ロール設定を構成するときに、Privileged Identity Management (PIM) 環境での割り当てに適用される既定の設定を定義します。 リソースのこれらの設定を定義するには、左側のウィンドウで **[ロールの設定]** を選択します。 アクション バーからロールの設定 (任意のロール) を選択して現在のオプションを表示することもできます。

## <a name="overview"></a>概要

Azure リソース ロール用の Privileged Identity Management (PIM) での承認ワークフローにより、管理者は重要なリソースへのアクセスの保護または制限を強化することができます。 つまり、管理者は、ロールの割り当てのアクティブ化に承認を要求できます。 

リソース階層の概念は、Azure リソース ロールに固有です。 この階層により、親リソース オブジェクトから親コンテナー内のすべての子リソースに、ロールの割り当てを継承できます。 

たとえば、リソース管理者の Bob が、PIM を使用して Alice を有資格メンバーとして Contoso サブスクリプションの所有者ロールに割り当てます。 この割り当てにより、Alice は、Contoso サブスクリプション内のすべてのリソース グループ コンテナーの有資格所有者です。 また、Alice は、サブスクリプションの各リソース グループ内にあるすべてのリソース (仮想マシンなど) の有資格所有者でもあります。 

Contoso サブスクリプションには、Fabrikam Test、Fabrikam Dev、Fabrikam Prod という、3 つのリソース グループがあると仮定しましょう。これらの各リソース グループには、単一の仮想マシンが含まれています。

PIM 設定はリソースのロールごとに構成されます。 割り当てとは異なり、PIM 設定は継承されず、厳密にリソース ロールに適用されます。 [有資格割り当てとリソースの可視性に関する詳細を参照してください](pim-resource-roles-eligible-visibility.md)。

引き続き同じ例で、Bob が PIM を使用し、Contoso サブスクリプションの所有者ロール内のすべてのメンバーに対して、アクティブ化のために承認依頼を要求します。 Fabrikam Prod リソース グループのリソースの保護に役立つように、Bob はこのリソースの所有者ロールのメンバーにも承認を要求します。 Fabrikam Test と Fabrikam Dev の所有者ロールには、アクティブ化のために承認を要求しません。

Alice が Contoso サブスクリプションの所有者ロールのアクティブ化を要求すると、Alice がロール内でアクティブになる前に、承認者が Alice の要求を承認または拒否する必要があります。 Alice が Fabrikam Prod リソース グループを[アクティブ化のスコープに設定](pim-resource-roles-activate-your-roles.md#apply-just-enough-administration-practices)した場合、承認者はこの要求も承認または拒否する必要があります。 ただし、Alice が Fabrikam Test と Fabrikam Dev のいずれかまたは両方をアクティブ化のスコープに設定した場合、承認は要求されません。

承認ワークフローがロールのすべてのメンバーに必要でない場合もあります。 Azure サブスクリプションで実行されるアプリケーションの開発を支援するために、組織が複数の契約担当者を雇用するシナリオを考えてみましょう。 リソース管理者として、従業員は承認を必要とせずに有資格のアクセスができるようにして、契約担当者は承認の要求を必要とするようにしたいと考えています。 契約担当者のみの承認ワークフローを構成するには、従業員に割り当てられているロールと同じ権限を持つカスタム ロールを作成することができます。 そのカスタム ロールをアクティブにするために承認を要求することができます。 [カスタム ロールの詳細については、こちらを参照してください](pim-resource-roles-custom-role-policy.md)。

承認ワークフローを構成し、誰が要求を承認または拒否できるかを指定するには、次の手順を使用します。

## <a name="require-approval-to-activate"></a>アクティブ化の承認を必須にする

1. Azure Portal で PIM を参照し、一覧からリソースを選択します。

   ![[Azure リソース] ウィンドウでリソースが選択されている](media/azure-pim-resource-rbac/aadpim_manage_azure_resource_some_there.png)

2. 左側のウィンドウで **[ロールの設定]** を選択します。

3. ロールを検索して選択し、**[編集]** を選択して設定を変更します。

   ![オペレーター ロールの [編集] ボタン](media/azure-pim-resource-rbac/aadpim_rbac_role_settings_view_settings.png)

4. **[アクティブ化]** のセクションで、**[アクティブにするには承認が必要です]** チェック ボックスをオンにします。

   ![ロール設定の [アクティブ化] セクション](media/azure-pim-resource-rbac/aadpim_rbac_settings_require_approval_checkbox.png)

## <a name="specify-approvers"></a>承認者を指定する

**[承認者の選択]** をクリックして **[Select a user or group]**(ユーザーまたはグループの選択) ウィンドウを開きます。

>[!NOTE]
>設定を更新するには、少なくとも 1 人のユーザーまたは 1 つのグループを選択する必要があります。 既定の承認者はいません。

リソース管理者は、承認者の一覧にユーザーとグループの任意の組み合わせを追加できます。 

![[ユーザーまたはグループの選択] ウィンドウでユーザーが選択されている](media/azure-pim-resource-rbac/aadpim_rbac_role_settings_select_approvers.png)

## <a name="request-approval-to-activate"></a>アクティブ化のために承認を要求する

承認を要求しても、アクティブ化のためにメンバーが従う必要がある手順には影響しません。 [ロールをアクティブにするための手順を確認してください](pim-resource-roles-activate-your-roles.md)。

メンバーが承認を必要とするロールのアクティブ化を要求し、そのロールが必要ではなくなった場合、メンバーは PIM で要求をキャンセルできます。

キャンセルするには、PIM を参照し、**[申請]** を選択します。 要求を見つけて、**[キャンセル]** を選択します。

![[申請] ウィンドウ](media/azure-pim-resource-rbac/aadpim_rbac_role_approval_request_pending.png)

## <a name="next-steps"></a>次の手順

- [PIM で Azure リソース ロールに対して多要素認証を要求する](pim-resource-roles-require-mfa.md)
- [PIM で Azure リソース ロールに対するセキュリティの警告を構成する](pim-resource-roles-configure-alerts.md)
