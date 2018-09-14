---
title: PIM での有資格割り当てとリソースの可視性 - Azure | Microsoft Docs
description: Azure AD Privileged Identity Management (PIM) で Azure リソース ロールに有資格としてメンバーを割り当てる方法を説明します。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.component: pim
ms.date: 04/02/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: fb52bc92c86261831d0e8d8e9e863a4863fe8fb9
ms.sourcegitcommit: 31241b7ef35c37749b4261644adf1f5a029b2b8e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/04/2018
ms.locfileid: "43666891"
---
# <a name="eligible-assignments-and-resource-visibility-in-pim"></a>PIM での有資格割り当てとリソースの可視性

Azure リソース ロールに対して Privileged Identity Management (PIM) を使用すると､重要な Azure リソースに対するセキュリティを強化することができます｡ リソース管理者はPIMを使用すると、メンバーにリソース ロールの有資格割り当てを行うことができます｡ 以下のセクションでは､Azure リソース ロールの割り当ての種類と割り当て状態を説明します｡ 

## <a name="assignment-types"></a>割り当ての種類

Azure リソース向けの PIM には、2 つの明確な割り当ての種類があります。

- 有資格
- アクティブ

有資格割り当ては、ロールを使用するためのアクションを実行することをロールのメンバーに要求します。 要求されるアクションには、多要素認証チェックの成功、業務上の妥当性の指定、指定された承認者に対する承認要求などがあります。

アクティブ割り当ては、ロールを使用するために何らかのアクションを実行することをメンバーに要求しません。 アクティブ割り当てされたメンバーは、ロールによって提供される特権を常に所有します。

## <a name="assignment-duration"></a>割り当て期間

リソース管理者は、ロールに対する PIM 設定を構成するさい､割り当ての種類に 2 つあるオプションのいずれかを選択できます。 これらのオプションは、PIM でメンバーがロールに割り当てられるときの既定の最大期間になります。 

管理者は以下の割り当ての種類の 1 つを選択できます｡

- 永続的な有資格割り当てを許可する
- 永続的なアクティブ割り当てを許可する

管理者は以下の割り当ての種類の 1 つを選択できます｡

- 有資格割り当ては次の期間後に期限切れになる
- アクティブ割り当ては次の期間後に期限切れになる

リソース管理者が **[Allow permanent eligible assignment]**(永続的な有資格割り当てを許可する) または **[Allow permanent active assignment]**(永続的なアクティブ割り当てを許可する) のいずれかを選択した場合、そのリソースにメンバーを割り当てる管理者は誰でも、永続的なメンバーシップを割り当てることができます。

リソース管理者が **Expire eligible assignments after** または **Expire active assignments after** のいずれかを選択した場合､そのリソース管理者は､あらゆる割り当てに開始および終了日時を指定することを要求することで割り当てのライフサイクルを管理することができます｡

> [!NOTE] 
> リソース管理者は､終了日時が指定されている割り当てのどれでも更新することができます｡ これに対し､メンバーは割り当てを[延長または更新](pim-resource-roles-renew-extend.md)するセルフサービス要求を開始することができます｡


## <a name="assignment-states"></a>割り当ての状態

Azure リソースに対する PIM の **[自分のロール]**、**[ロール]**、および **[メンバー]** ビューの **[アクティブなロール]** タブには､明確に異なる 2 つの割り当て状態が表示されます これらの状態を次に示します。

- 割り当て済み
- アクティブ化済み

**[アクティブなロール]** はメンバーシップの表示であり､ **[状態]** 列の値によって **[割り当て済み]** でアクティブなユーザーと、有資格割り当てが **[アクティブ化済み]** でアクティブ になっているユーザーを区別できます。

## <a name="azure-resource-role-approval-workflow"></a>Azure リソース ロールの承認ワークフロー

Azure リソース ロールの PIM での承認ワークフローにより、管理者は重要なリソースへのアクセスの保護または制限を強化することができます。 つまり、管理者は、ロールの割り当てのアクティブ化に承認を要求できます。

リソース階層の概念は、Azure リソース ロールに固有です。 この階層により、親リソース オブジェクトから親コンテナー内のすべての子リソースに、ロールの割り当てを継承できます。 

たとえば、リソース管理者の Bob が、PIM を使用して Alice を有資格メンバーとして Contoso サブスクリプションの所有者ロールに割り当てます。 この割り当てにより、Alice は、Contoso サブスクリプション内のすべてのリソース グループ コンテナーの有資格所有者です。 また、Alice は、サブスクリプションの各リソース グループ内にあるすべてのリソース (仮想マシンなど) の有資格所有者でもあります。

Contoso サブスクリプションには、Fabrikam Test、Fabrikam Dev、Fabrikam Prod という、3 つのリソース グループがあると仮定しましょう。これらの各リソース グループには、単一の仮想マシンが含まれています。

PIM 設定はリソースのロールごとに構成されます。 割り当てとは異なり、PIM 設定は継承されず、厳密にリソース ロールに適用されます。

引き続き同じ例で、Bob が PIM を使用し、Contoso サブスクリプションの所有者ロール内のすべてのメンバーに対して、アクティブ化のために承認依頼を要求します。 Fabrikam Prod リソース グループのリソースの保護に役立つように、Bob はこのリソースの所有者ロールのメンバーにも承認を要求します。 Fabrikam Test と Fabrikam Dev の所有者ロールには、アクティブ化のために承認を要求しません。

Alice が Contoso サブスクリプションの所有者ロールのアクティブ化を要求すると、Alice がロール内でアクティブになる前に、承認者が Alice の要求を承認または拒否する必要があります。 Alice が Fabrikam Prod リソース グループを[アクティブ化のスコープに設定](pim-resource-roles-activate-your-roles.md)した場合、承認者はこの要求も承認または拒否する必要があります。 ただし、Alice が Fabrikam Test と Fabrikam Dev のいずれかまたは両方をアクティブ化のスコープに設定した場合、承認は要求されません。

承認ワークフローがロールのすべてのメンバーに必要でない場合もあります。 Azure サブスクリプションで実行されるアプリケーションの開発を支援するために、組織が複数の契約担当者を雇用するシナリオを考えてみましょう。 リソース管理者として、従業員は承認を必要とせずに有資格のアクセスができるようにして、契約担当者は承認の要求を必要とするようにしたいと考えています。 契約担当者のみの承認ワークフローを構成するには、従業員に割り当てられているロールと同じ権限を持つカスタム ロールを作成することができます。 そのカスタム ロールをアクティブにするために承認を要求することができます。 [カスタム ロールの詳細については、こちらを参照してください](pim-resource-roles-custom-role-policy.md)。

## <a name="next-steps"></a>次の手順

- [PIM で Azure リソース ロールを割り当てる](pim-resource-roles-assign-roles.md)
