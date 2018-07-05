---
title: カスタム ロールを使用して Azure リソース向けの Privileged Identity Management 設定を対象にする | Microsoft Docs
description: PIM で Azure リソース向けのカスタム ロールを使用する方法について説明します。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: protection
ms.date: 03/30/2018
ms.author: rolyon
ms.openlocfilehash: f086d8038e6d27990c49749438ee05e3e39a5aec
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/03/2018
ms.locfileid: "37442911"
---
# <a name="use-custom-roles-to-target-privileged-identity-management-settings"></a>カスタム ロールを使用して Privileged Identity Management 設定を対象にする

ロールの一部のメンバーには厳密な Privileged Identity Management (PIM) 設定を適用し、他のメンバーにはより自主性を持たせる必要がある場合があります。 Azure サブスクリプションで実行されるアプリケーションの開発を支援するために、組織が複数の契約担当者を雇用するシナリオを考えてみましょう。

リソースの管理者として、承認を必要とせずに従業員がアクセスできるようにする必要があります。 ただし、組織のリソースへのアクセスを要求するときは、すべての契約社員が承認される必要があります。

次のセクションに概要を示した手順に従って、Azure リソース ロール向けに対象 PIM 設定をセットアップします。

## <a name="create-the-custom-role"></a>カスタム ロールを作成する

リソースのカスタム ロールを作成するには、「[Azure のロールベースのアクセス制御のためのカスタム ロールを作成する](../role-based-access-control-custom-roles.md)」に記載された手順に従います。

カスタム ロールを作成するときには、複製する組み込みロールを簡単に覚えておくことができるように、わかりやすい名前を付けます。

> [!NOTE]
> カスタム ロールが複製したい組み込みロールの複製であり、そのスコープが組み込みロールと一致することを確認します。

## <a name="apply-pim-settings"></a>PIM 設定を適用する

Azure ポータルで、テナント ロールを作成した後に、**[Privileged Identity Management - Azure リソース]** ウィンドウに移動します。 ロールが適用されるリソースを選択します。

![[Privileged Identity Management - Azure リソース] ウィンドウ](media/azure-pim-resource-rbac/aadpim_manage_azure_resource_some_there.png)

ロールのこのメンバーに適用する必要がある [PIM ロール設定を構成します](pim-resource-roles-configure-role-settings.md)。

最後に、これらの設定の対象にするメンバーの明確なグループに[ロールを割り当てます](pim-resource-roles-assign-roles.md)。

## <a name="next-steps"></a>次の手順

[レビューのサブスクリプションの所有者とアクセスを確認する](pim-resource-roles-perform-access-review.md)
