---
title: Azure リソース向けの Privileged Identity Management - リソースの監査 | Microsoft Docs
description: 特定のリソースに対するすべてのロールのアクティビティを表示する方法について説明します。
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/02/2018
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: b8fa7d5600c0de8a3319ea4de785281372959937
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2018
---
# <a name="privileged-identity-management---resource-roles---audit"></a>Privileged Identity Management - リソース ロール - 監査

リソースの監査では、リソースのすべてのロール アクティビティのビューが提供されます。 定義済みの日付またはカスタムの範囲を使用して情報をフィルターすることができます。
![](media/azure-pim-resource-rbac/rbac-resource-audit.png)

リソースの監査では、ユーザーのアクティビティの詳細表示にすばやくアクセスすることもできます。 [監査の種類] で、[アクティブ化] を選択します。 Azure リソースに関するユーザーのアクションを確認するには、[(アクティビティ)] をクリックします。
![](media/azure-pim-resource-rbac/rbac-audit-activity.png)

![](media/azure-pim-resource-rbac/rbac-audit-activity-details.png)

# <a name="my-audit"></a>自分の監査

[自分の監査] には、ユーザー個人のロールのアクティビティが表示されます。 定義済みの日付またはカスタムの範囲を使用して情報をフィルターすることができます。
![](media/azure-pim-resource-rbac/my-audit-time.png)

## <a name="view-activation-and-azure-resource-activity"></a>アクティブ化と Azure リソースのアクティビティを表示する

各リソースに対して特定ユーザーが実行した操作を確認する必要がある場合は、指定したアクティブ化期間に関連付けられている Azure リソース アクティビティを表示することができます (適格なユーザーに限ります)。 最初に、[メンバー] ビューまたは特定のロールのメンバー一覧からユーザーを選択します。 結果には Azure リソースに対する日別のユーザーの操作と、同一期間の最近のロールのアクティブ化がグラフィカルに表示されます。

![](media/azure-pim-resource-rbac/rbac-user-details.png)

特定のロールのアクティブ化を選択すると、ロールのアクティブ化の詳細とそのユーザーがアクティブな間に発生した、対応する Azure リソース アクティビティが表示されます。

![](media/azure-pim-resource-rbac/rbac-user-resource-activity.png)

