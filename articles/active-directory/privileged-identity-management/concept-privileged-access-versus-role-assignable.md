---
title: 特権アクセス グループとロール割り当て可能グループの違い - Azure AD | Microsoft Docs
description: Azure AD Privileged Identity Management (PIM) で特権アクセス グループとロール割り当て可能グループの違いを特定する方法について説明します。
services: active-directory
documentationcenter: ''
author: curtand
manager: KarenH444
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 10/10/2021
ms.author: curtand
ms.reviewer: shaunliu
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6d903f3aa1e46005ad848a5f63139ce5fd17c8c5
ms.sourcegitcommit: bee590555f671df96179665ecf9380c624c3a072
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/07/2021
ms.locfileid: "129668202"
---
# <a name="whats-the-difference-between-privileged-access-groups-and-role-assignable-groups"></a>特権アクセス グループとロール割り当て可能グループの違い

Privileged Identity Management (PIM) では、ロール割り当て可能グループに対する特権アクセスを有効にする機能がサポートされています。 ただし、ロール割り当て可能グループが使用可能であることは特権アクセス グループの作成の前提条件であるため、この記事では、その違いとこれらの活用方法について説明します。

## <a name="what-are-azure-ad-role-assignable-groups"></a>Azure AD ロール割り当て可能グループとは

Azure ADでは、クラウド Azure AD セキュリティ グループを Azure AD ロールに割り当てることができます。 グローバル管理者と特権ロール管理者は、新しいセキュリティ グループを作成し、作成時にそのグループをロール割り当て可能にする必要があります。 グループのメンバーシップを変更できるのは、グローバル管理者、特権ロール管理者、またはグループの所有者ロールのユーザーのみです。 また、これ以外のユーザーが、グループのメンバーであるユーザーのパスワードをリセットすることはできません。 この機能を利用することで、管理者が要求と承認の手順を行わずに高い特権ロールに昇格するのを防ぎます。

## <a name="what-are-privileged-access-groups"></a>特権アクセス グループとは

特権アクセス グループを利用すると、ユーザーは Azure AD セキュリティ グループの所有者またはメンバーに昇格できます。 この機能を使用すると、Azure AD および Azure のロールの Just-In-Time ワークフローをバッチで設定できるだけでなく、Azure SQL、Azure Key Vault、Intune、その他のアプリケーション ロールなど、他のユース ケースでの Just-In-Time シナリオも可能になります。 詳細については、「[特権アクセス グループの管理機能](groups-features.md)」を参照してください。

>[!Note]
>Azure AD ロールに昇格するために使用される特権アクセス グループについては、資格のあるメンバーの割り当ての承認プロセスを求めることをお勧めします。 承認せずにアクティブ化できる割り当てでは、権限の低い管理者によるセキュリティ リスクに対して脆弱なままである可能性があります。 たとえば、ヘルプデスク管理者には、対象となるユーザーのパスワードをリセットするアクセス許可があります。

## <a name="when-to-use-each-type-of-group"></a>それぞれの種類のグループを使用するタイミング

アクセス許可とロールへの Just-In-Time アクセスは、Azure AD と Azure リソース以外で設定できます。 承認を Azure AD セキュリティ グループに関連付けることができる他のリソースがある場合 (Azure Key Vault、Intune、Azure SQL、または他のアプリやサービスの場合) は、グループに対する特権アクセスを有効にし、グループのメンバーシップの有資格者としてユーザーを割り当てる必要があります。

グループを Azure AD または Azure リソースのロールに割り当て、PIM プロセスによる昇格を求める場合は、次の 2 つの方法があります。

- **グループをロールに永続的に割り当てる**。 この後に、PIM でグループへの有資格メンバーのアクセス権がユーザーに付与されます。 その後、有資格ユーザーは、ロールに永続的に割り当てられているグループに参加するために、自分のメンバーシップをアクティブ化する必要があります。 この方法では、PIM でロール割り当て可能グループを、Azure AD ロールの特権アクセス グループとして有効にする必要があります。
- PIM を使用して **ロールの対象としてグループを割り当てる**。 グループのすべてのユーザーは、ロールにアクセスするためにその割り当てをアクティブ化する必要があります。 この方法では、Azure AD ロールの場合はロール割り当て可能グループ、Azure リソースの場合はセキュリティ グループが必要です。

    ![PIM で特権アクセス グループを使用してロールを割り当てる 2 つの方法を示す図。](./media/concept-privileged-access-versus-role-assignable/concept-privileged-access.png)

これらの方法はどちらも、エンドツーエンドのシナリオで機能します。 ほとんどの場合、最初の方法を使用することをお勧めします。 2 番目の方法は、以下を行おうとしている場合にのみ使用してください。

- 1 つのグループを複数の Azure AD または Azure リソースのロールに割り当て、ユーザーは複数のロールにアクセスするのに 1 回だけアクティブ化を行います。
- Azure AD または Azure リソースのロールにアクセスするために、異なるユーザー セットに対して異なるアクティブ化ポリシーを維持します。 たとえば、グローバル管理者になる前に一部のユーザーが承認されるようにし、その一方で、他のユーザーは自動承認されるようにする場合、2 つの特権アクセス グループを設定し、その両方をグローバル管理者ロールに永続的に割り当て (Privileged Identity Management での "永続的な" 割り当て)、その後に各グループのメンバー ロールに異なるアクティブ化ポリシーを使用することができます。

## <a name="next-steps"></a>次の手順

- [Azure AD ロールに対する要求を承認または拒否する](azure-ad-pim-approval-workflow.md)
- [Azure リソース ロールに対する要求を承認または拒否する](pim-resource-roles-approval-workflow.md)
- [特権アクセス グループのメンバーと所有者のアクティブ化要求を承認する (プレビュー)](groups-approval-workflow.md)
