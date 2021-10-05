---
title: Azure AD PIM で Azure AD ロールの監査ログ レポートを表示する | Microsoft Docs
description: Azure AD Privileged Identity Management (PIM) で Azure AD ロールの監査ログ履歴を表示する方法について説明します。
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 09/23/2021
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: f8ecfa670980015acbdd8f5b639734f538910804
ms.sourcegitcommit: 3ef5a4eed1c98ce76739cfcd114d492ff284305b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128708626"
---
# <a name="view-audit-history-for-azure-ad-roles-in-privileged-identity-management"></a>Privileged Identity Management で Azure AD ロールの監査履歴を表示する

Privileged Identity Management (PIM) の監査履歴を使用すると、すべての特権ロールでの過去 30 日間におけるすべてのロール割り当てとアクティブ化を確認できます。 監査データを既定の保有期間よりも長く保持するには、Azure Monitor を使用して Azure ストレージ アカウントにルーティングします。 詳細については、「[Azure AD のログを Azure ストレージ アカウントにアーカイブする](../reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md)」をご覧ください。 管理者、エンド ユーザー、同期アクティビティを含む Azure Active Directory (Azure AD) 組織内のアクティビティの完全な監査履歴を確認するには、[Azure Active Directory のセキュリティおよびアクティビティ レポート](../reports-monitoring/overview-reports.md)を使用できます。

Azure AD ロールの監査履歴を表示するには、次の手順に従います。

## <a name="view-resource-audit-history"></a>リソースの監査履歴を表示する

リソースの監査では、自分の Azure AD ロールに関連付けられているすべてのアクティビティを確認できます。

1. **[Azure AD Privileged Identity Management]** を開きます。

1. **[Azure AD ロール]** を選択します。

1. **[リソースの監査]** を選択します。

1. 定義済みの日付またはカスタムの範囲を使用して履歴をフィルターします。

    ![フィルターを使用した Azure AD ロール監査リスト](media/azure-pim-resource-rbac/rbac-resource-audit.png)

## <a name="view-my-audit"></a>自分の監査を表示する

自分の監査を使用すると、個人のロール アクティビティを表示できます。

1. **[Azure AD Privileged Identity Management]** を開きます。

1. **[Azure AD ロール]** を選択します。

1. 監査履歴を表示するリソースを選択します。

1. **[自分の監査]** を選択します。

1. 定義済みの日付またはカスタムの範囲を使用して履歴をフィルターします。

    ![現在のユーザーに対する監査の一覧](media/azure-pim-resource-rbac/my-audit-time.png)

## <a name="next-steps"></a>次のステップ

- [Privileged Identity Management で Azure リソース ロールのアクティビティおよび監査履歴を表示する](azure-pim-resource-rbac.md)
