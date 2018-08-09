---
title: Privileged Identity Management での Azure の有資格割り当てとリソース可視性 | Microsoft Docs
description: PIM でメンバーにリソース ロールの 有資格割り当てを行う方法を説明します｡
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
ms.openlocfilehash: 336453c1ef6ef8d0295d00f31afc6a5e7e42e8b6
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/08/2018
ms.locfileid: "39621550"
---
# <a name="eligible-assignments-and-resource-visibility-with-privileged-identity-management"></a>Privileged Identity Management での有資格割り当てとリソース可視性

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

## <a name="next-steps"></a>次の手順

[Privileged Identity Manager でロールを割り当てる](pim-resource-roles-assign-roles.md)
