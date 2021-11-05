---
title: Azure AD エンタイトルメント管理でアクセス パッケージの要求を確認および削除する - Azure Active Directory
description: Azure Active Directory エンタイトルメント管理で、アクセス パッケージの要求を確認および削除する方法について説明します。
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 9/20/2021
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 140d648ddde6d520fdb27c6df0152cb9eb61c1fa
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131051803"
---
# <a name="view-and-remove-requests-for-an-access-package-in-azure-ad-entitlement-management"></a>Azure AD エンタイトルメント管理でアクセス パッケージの要求を確認および削除する

Azure AD エンタイトルメント管理では、アクセス パッケージを要求したユーザー、そのポリシー、および状態を確認できます。 この記事では、アクセス パッケージの要求を表示し、不要になった要求を削除する方法について説明します。

## <a name="view-requests"></a>要求を表示する

**事前に必要なロール:** グローバル管理者、ID ガバナンス管理者、ユーザー管理者、カタログ所有者、アクセス パッケージ マネージャーまたはアクセス パッケージ割り当てマネージャー

1. Azure portal で **[Azure Active Directory]** をクリックし、 **[Identity Governance]** をクリックします。

1. 左側のメニューで **[アクセス パッケージ]** をクリックして、アクセス パッケージを開きます。

1. **[要求]** をクリックします。

1. 特定の要求をクリックすると、その他の詳細が表示されます。

    ![アクセス パッケージの要求の一覧](./media/entitlement-management-access-package-requests/requests-list.png)

1. **[要求履歴の詳細]** をクリックすると、要求を承認したユーザー、承認の妥当性、およびアクセスが配信された日時を確認できます。

要求が "部分的に配信済み" または "失敗" の状態にあるユーザーのセットがある場合は、[再処理機能](entitlement-management-reprocess-access-package-requests.md)を使用してこれらの要求を再試行できます。

### <a name="view-assignments-with-microsoft-graph"></a>Microsoft Graph を使用して割り当てを表示する
Microsoft Graph を使用して、アクセス パッケージの要求を取得することもできます。  適切なロールのユーザーは、委任された `EntitlementManagement.Read.All` または `EntitlementManagement.ReadWrite.All` アクセス許可が割り当てられたアプリケーションから API を呼び出して [list accessPackageAssignmentRequests](/graph/api/accesspackageassignmentrequest-list?view=graph-rest-beta&preserve-view=true) を一覧表示できます。 `$expand=accessPackage&$filter=accessPackage/id eq '9bbe5f7d-f1e7-4eb1-a586-38cdf6f8b1ea'` などの特定のアクセス パッケージを示すフィルターを指定できます。 アプリケーションのアクセス許可 `EntitlementManagement.Read.All` または `EntitlementManagement.ReadWrite.All` アクセス許可を持つアプリケーションでも、この API を使用できます。

## <a name="remove-request-preview"></a>要求を削除する (プレビュー)

完了して不要になった要求を削除することもできます。 要求を削除するには:

1. Azure portal で **[Azure Active Directory]** をクリックし、 **[Identity Governance]** をクリックします。

1. 左側のメニューで **[アクセス パッケージ]** をクリックして、アクセス パッケージを開きます。

1. **[要求]** をクリックします。

1. アクセスパッケージから削除する要求を見つけます。

1. [削除] ボタンを選択します。

> [!NOTE]
> 完了した要求をアクセス パッケージから削除しても、アクティブな割り当ては削除されず、要求のデータのみが削除されます。 そのため、要求元は引き続きアクセスできます。 また、割り当てとそのアクセス パッケージからのアクセス権を削除する必要がある場合は、左側のメニューで **[割り当て]** をクリックし、割り当てを見つけてから[割り当てを削除](entitlement-management-access-package-assignments.md)します。

### <a name="remove-a-request-with-microsoft-graph"></a>Microsoft Graph で要求を削除する

Microsoft Graph を使用して要求を削除することもできます。  適切なロールのユーザーは、委任された `EntitlementManagement.ReadWrite.All` アクセス許可が割り当てられたアプリケーションから、またはそのアプリケーションのアクセス許可を持つアプリケーションから、API を呼び出して [accessPackageAssignmentRequest](/graph/api/accesspackageassignmentrequest-delete?view=graph-rest-beta&preserve-view=true) を削除できます。

## <a name="next-steps"></a>次の手順

- [アクセスパッケージの要求を再処理します](entitlement-management-reprocess-access-package-requests.md)
- [Change request and approval settings for an access package](entitlement-management-access-package-request-policy.md) (アクセス パッケージの要求と承認の設定を変更する)
- [アクセス パッケージの割り当てを表示、追加、および削除する](entitlement-management-access-package-assignments.md)
- [要求のトラブルシューティング](entitlement-management-troubleshoot.md#requests)
