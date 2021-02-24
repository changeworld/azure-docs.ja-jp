---
title: Azure AD エンタイトルメント管理でアクセス パッケージへの要求を表示する - Azure Active Directory
description: Azure Active Directory のエンタイトルメント管理でアクセス パッケージへの要求を表示する方法について説明します。
services: active-directory
documentationCenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 12/23/2020
ms.author: barclayn
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: b484b19282c8ad42890f6e04f03563c29baa49dd
ms.sourcegitcommit: 6e2d37afd50ec5ee148f98f2325943bafb2f4993
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/23/2020
ms.locfileid: "97745999"
---
# <a name="view-requests-for-an-access-package-in-azure-ad-entitlement-management"></a>Azure AD エンタイトルメント管理でアクセス パッケージへの要求を表示する

Azure AD エンタイトルメント管理では、アクセス パッケージを要求したユーザー、そのポリシー、および状態を確認できます。 この記事では、アクセス パッケージへの要求を表示する方法について説明します。

## <a name="view-requests"></a>要求を表示する

**事前に必要なロール:** グローバル管理者、ユーザー管理者、カタログ所有者、アクセス パッケージ マネージャーまたはアクセス パッケージ割り当てマネージャー

1. Azure portal で **[Azure Active Directory]** をクリックし、 **[Identity Governance]** をクリックします。

1. 左側のメニューで **[アクセス パッケージ]** をクリックして、アクセス パッケージを開きます。

1. **[要求]** をクリックします。

1. 特定の要求をクリックすると、その他の詳細が表示されます。

    ![アクセス パッケージの要求の一覧](./media/entitlement-management-access-package-requests/requests-list.png)

## <a name="next-steps"></a>次のステップ

- [Change request and approval settings for an access package](entitlement-management-access-package-request-policy.md) (アクセス パッケージの要求と承認の設定を変更する)
- [アクセス パッケージの割り当てを表示、追加、および削除する](entitlement-management-access-package-assignments.md)
- [要求のトラブルシューティング](entitlement-management-troubleshoot.md#requests)
