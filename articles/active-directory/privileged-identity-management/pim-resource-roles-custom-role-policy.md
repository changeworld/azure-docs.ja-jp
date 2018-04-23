---
title: Azure リソース向けの Privileged Identity Management - カスタム ロールを使用して PIM 設定を対象にする | Microsoft Docs
description: Azure リソース向けの PIM でカスタム ロールを使用する方法について説明します。
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/30/2018
ms.author: billmath
ms.openlocfilehash: 6336d99df1bbdd71c66a9757af1d9fb356a91bf6
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2018
---
# <a name="use-custom-roles-to-target-pim-settings"></a>カスタム ロールを使用して PIM 設定を対象にする

ロールの一部のメンバーには厳密な PIM 設定を適用し、他のメンバーにはより自主性を持たせる必要がある場合があります。 Azure サブスクリプションで実行されるアプリケーションの開発を支援するために、組織が複数の契約担当者を雇用するシナリオを考えてみましょう。 

リソース管理者として、組織の従業員は承認を必要とせずに有資格のアクセスができるようにして、すべての契約担当者はアクティブ化を要求するときに承認を必要とするようにしたいと考えています。 Azure リソース ロール向けに対象 PIM 設定を有効にするプロセスの概要を示した以下の手順に従います。

## <a name="create-the-custom-role"></a>カスタム ロールを作成する

[このガイドを使用して、リソースのカスタム ロールを作成します](../../role-based-access-control/custom-roles.md)。

複製する組み込みロールを簡単に覚えておくことができるように、わかりやすい名前を付けます。

>[!NOTE]
>カスタム ロールが意図したロールの複製であり、そのスコープが組み込みロールと一致することを確認します。

## <a name="apply-pim-settings"></a>PIM 設定を適用する

テナントにロールが作成されたら、PIM に移動し、ロールのスコープとなるリソースを選択します。

![](media/azure-pim-resource-rbac/aadpim_manage_azure_resource_some_there.png)

このメンバーに適用する必要がある [PIM ロール設定を構成します](pim-resource-roles-configure-role-settings.md)。

最後に、この設定の対象にするメンバーの明確なグループに[ロールを割り当てます](pim-resource-roles-assign-roles.md)。

## <a name="next-steps"></a>次の手順

[サブスクリプションの所有者をレビューする](pim-resource-roles-perform-access-review.md)
