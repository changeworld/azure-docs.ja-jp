---
title: PIM で Azure リソース ロールの監査履歴を表示する | Microsoft Docs
description: Azure AD Privileged Identity Management (PIM) で Azure リソース ロールの監査履歴を表示する方法について説明します。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.component: pim
ms.date: 04/02/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: c0536423e9640f78149b612ec66b0a07cdcf24bb
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/29/2018
ms.locfileid: "43189313"
---
# <a name="view-audit-history-for-azure-resource-roles-in-pim"></a>PIM で Azure リソース ロールの監査履歴を表示する

リソースの監査では、リソースのすべてのロール アクティビティのビューが提供されます。 定義済みの日付またはカスタムの範囲を使用して情報をフィルターすることができます。
![情報のフィルター処理](media/azure-pim-resource-rbac/rbac-resource-audit.png)

リソースの監査では、ユーザーのアクティビティの詳細にすばやくアクセスすることもできます。 **[監査の種類]** で、**[アクティブ化]** を選択します。 Azure リソースに関するユーザーのアクションを確認するには、**[(アクティビティ)]** を選択します。
![アクティビティの詳細](media/azure-pim-resource-rbac/rbac-audit-activity.png)

![アクティビティの詳細](media/azure-pim-resource-rbac/rbac-audit-activity-details.png)

## <a name="my-audit"></a>自分の監査

[自分の監査] には、ユーザー個人のロールのアクティビティが表示されます。 定義済みの日付またはカスタムの範囲を使用して情報をフィルターすることができます。
![個人のロール アクティビティ](media/azure-pim-resource-rbac/my-audit-time.png)

## <a name="view-activation-and-azure-resource-activity"></a>アクティブ化と Azure リソースのアクティビティを表示する

各リソースに対して特定のユーザーが行った操作を表示する場合、指定したアクティブ化期間に関連付けられている Azure リソース アクティビティを確認することができます。 まず、**[メンバー]** ビューまたは特定のロールのメンバー一覧からユーザーを選択します。 結果には、日付ごとの Azure リソースに対するユーザー操作のグラフィカル ビューが表示されます。 その同じ期間での最近のロールのアクティブ化も表示されます。

![ユーザーの詳細](media/azure-pim-resource-rbac/rbac-user-details.png)

特定のロールのアクティブ化を選択すると、ロールのアクティブ化の詳細とそのユーザーがアクティブな間に発生した、対応する Azure リソース アクティビティが表示されます。

![ロールのアクティブ化の選択](media/azure-pim-resource-rbac/rbac-user-resource-activity.png)

## <a name="next-steps"></a>次の手順

- [PIM で Azure AD ディレクトリ ロールの監査履歴を表示する](pim-how-to-use-audit-log.md)
