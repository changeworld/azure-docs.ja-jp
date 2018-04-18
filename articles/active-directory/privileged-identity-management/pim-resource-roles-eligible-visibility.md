---
title: Azure リソース向けの Privileged Identity Management - 有資格割り当てとリソースの可視性 | Microsoft Docs
description: メンバーをリソース ロールに有資格として割り当てる方法について説明します。
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: mwahl
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/02/2018
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: 273b06c91d68a764fe814374c0eca6ed1698cc2e
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2018
---
# <a name="eligible-assignments-and-resource-visibility"></a>有資格割り当てとリソースの可視性

Azure リソース ロールに対する PIM は、重要な Azure リソースのセキュリティ強化を組織に提供します。 PIM では、リソース管理者が、メンバーをリソース ロールに対して有資格であると割り当てることができます。 Azure リソース ロールに対する割り当ての種類と状態については、以下を参照してください。 

## <a name="assignment-types"></a>割り当ての種類

Azure リソース向けの PIM には、2 つの明確な割り当ての種類があります。

- 有資格
- アクティブ

有資格割り当ては、ロールを使用するためのアクションを実行することをロールのメンバーに要求します。 これらのアクションには、Multi-Factor Authentication チェックの成功、妥当性の提供、指定された承認者に対する承認の要求などがあります。

アクティブ割り当ては、ロールを使用するために何らかのアクションを実行することをメンバーに要求しません。 アクティブとして割り当てられたメンバーは、ロールによって提供される特権を常に所有します。

## <a name="assignment-duration"></a>割り当て期間

リソース管理者は、ロールに PIM 設定を構成するときに、割り当ての種類ごとに 2 つのオプションのいずれかを選択できます。 これらのオプションは、PIM でメンバーがロールに割り当てられるときの既定の最大期間になります。

- 永続的な有資格割り当てを許可する
- 永続的なアクティブ割り当てを許可する

または

- 有資格割り当ては次の期間後に期限切れになる
- アクティブ割り当ては次の期間後に期限切れになる

リソース管理者が [Allow permanent eligible assignment]\(永続的な有資格割り当てを許可する\) と [Allow permanent active assignment]\(永続的なアクティブ割り当てを許可する\) の両方またはいずれかを選択した場合、リソースにメンバーを割り当てるすべての管理者は、永続的なメンバーシップを割り当てることができます。

[Expire eligible assignments after]\(有資格割り当ては次の期間後に期限切れになる\) と [Expire active assignments after]\(アクティブ割り当ては次の期間後に期限切れになる\) の両方またはいずれかを選択すると、すべての割り当てに開始日時と終了日時の指定を要求することで、割り当てのライフサクルを制御できます。

>[!NOTE] 
>リソース管理者は、終了日時が指定されたすべての割り当てを更新でき、メンバーは、[割り当てを延長するか更新する](pim-resource-roles-renew-extend.md)セルフ サービス要求を開始できます。


## <a name="assignment-states"></a>割り当ての状態

Azure リソース向けの PIM には、PIM の [自分のロール]、[ロール]、および [メンバー] ビューの [アクティブなロール] タブに表示される 2 つの明確な割り当ての状態があります。 これらの状態を次に示します。

- 割り当て済み
- アクティブ化済み

[アクティブなロール] に表示されるメンバーシップの [状態] 列で、アクティブとして [割り当て済み] のユーザーと、有資格割り当てをアクティブ化して [アクティブ化済み] になっているユーザーを区別できます。

## <a name="next-steps"></a>次の手順

[PIM でロールを割り当てる](pim-resource-roles-assign-roles.md)

