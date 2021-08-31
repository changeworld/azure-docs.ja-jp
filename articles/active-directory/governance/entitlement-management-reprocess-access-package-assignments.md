---
title: Azure AD エンタイトルメント管理でアクセス パッケージの割り当てを再処理する - Azure Active Directory
description: Azure Active Directory のエンタイトルメント管理でアクセス パッケージの割り当てを再処理する方法について説明します。
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
ms.date: 06/25/2021
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: b1b488f8c9331932b82ab0ab55db9c7dcb652add
ms.sourcegitcommit: 695a33a2123429289ac316028265711a79542b1c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/01/2021
ms.locfileid: "113129472"
---
# <a name="reprocess-assignments-for-an-access-package-in-azure-ad-entitlement-management"></a>Azure AD エンタイトルメント管理でアクセス パッケージの割り当てを再処理する

アクセス パッケージ マネージャーは、再処理機能を使用して、アクセス パッケージ内のユーザーの元の割り当てを自動的に再評価して適用できます。 再処理によって、ユーザーは、リソースへのアクセスがエンタイトルメント管理外部での変更の影響を受けた場合にアクセス パッケージ要求プロセスを繰り返す必要がなくなります。

たとえば、ユーザーが手動でグループから削除されたために、そのユーザーが必要なリソースにアクセスできなくなる可能性があります。 

アクセス パッケージのリソースに対する外部の更新はエンタイトルメント管理によってブロックされないため、エンタイトルメント管理 UI ではこの変更は正確に表示されません。 そのため、ユーザーがもうリソースへのアクセス権を持っていなくても、ユーザーの割り当て状態は "配信済み" と表示されます。 ただし、ユーザーの割り当てが再処理された場合は、アクセス パッケージのリソースに再度追加されます。 再処理により、アクセス パッケージの割り当てが最新であること、ユーザーが必要なリソースのアクセス権を持っていること、および割り当てが UI に正確に反映されていることが確認されます。

この記事では、既存のアクセス パッケージ内の割り当てを再処理する方法について説明します。

## <a name="prerequisites"></a>前提条件

Azure AD エンタイトルメント管理を使用し、ユーザーをアクセス パッケージに割り当てるには、次のいずれかのライセンスが必要です。

- Azure AD Premium P2
- Enterprise Mobility + Security (EMS) E5 ライセンス

## <a name="open-an-existing-access-package-and-reprocess-user-assignments"></a>既存のアクセス パッケージを開いてユーザー割り当てを再処理する

**必要なロール:** グローバル管理者、ID ガバナンス管理者、ユーザー管理者、カタログ所有者、アクセス パッケージ マネージャー、またはアクセス パッケージ割り当てマネージャー

"配信済み" 状態なのに、アクセス パッケージの一部であるリソースにアクセスできないユーザーがいる場合は、割り当てを再処理して、それらのユーザーをアクセス パッケージのリソースに再割り当てする必要がある可能性があります。 既存のアクセス パッケージの割り当てを再処理するには、次の手順に従ってください。

1.  [Azure portal](https://portal.azure.com) にサインインします。

1.  **[Azure Active Directory]** をクリックしてから **[Identity Governance]** をクリックします。

1.  左側のメニューで **[アクセス パッケージ]** をクリックして、再処理したいユーザー割り当てが含まれているアクセス パッケージを開きます。

1.  左側の **[管理]** の下にある **[割り当て]** をクリックします。

    ![Azure portal でのエンタイトルメント管理](./media/entitlement-management-reprocess-access-package-assignments/reprocess-access-package-assignment.png)

1.  割り当てを再処理するすべてのユーザーを選択します。

1.  **[再処理]** をクリックします。

## <a name="next-steps"></a>次のステップ

- [アクセス パッケージの割り当てを表示、追加、および削除する](entitlement-management-access-package-assignments.md)
- [レポートとログを表示する](entitlement-management-reports.md)
