---
title: Azure AD エンタイトルメント管理でアクセス パッケージへの要求を再処理する - Azure Active Directory
description: Azure Active Directory のエンタイトルメント管理でアクセス パッケージへの要求を再処理する方法について説明します。
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
ms.openlocfilehash: a85d796f8b66cca16d4d3c01ecbfc5f6c43c79f9
ms.sourcegitcommit: 695a33a2123429289ac316028265711a79542b1c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/01/2021
ms.locfileid: "113129469"
---
# <a name="reprocess-requests-for-an-access-package-in-azure-ad-entitlement-management"></a>Azure AD エンタイトルメント管理でアクセス パッケージへの要求を再処理する

アクセス パッケージ マネージャーは、再処理機能を使用して、いつでもユーザーのアクセス パッケージへのアクセスの要求を自動的に再試行できます。 再処理によって、リソースへのアクセスが正常にプロビジョニングされていない場合にユーザーがアクセス パッケージ要求プロセスを繰り返す必要がなくなります。

> [!NOTE]
> 元の要求が完了した時点から最大 14 日間、要求を再処理できます。 14 日より前に完了した要求の場合、ユーザーは、MyAccess でキャンセルして新しい要求を行う必要があります。

この記事では、既存のアクセス パッケージへの要求を再処理する方法について説明します。

## <a name="prerequisites"></a>前提条件

Azure AD エンタイトルメント管理を使用し、ユーザーをアクセス パッケージに割り当てるには、次のいずれかのライセンスが必要です。

- Azure AD Premium P2
- Enterprise Mobility + Security (EMS) E5 ライセンス

## <a name="open-an-existing-access-package-and-reprocess-user-requests"></a>既存のアクセス パッケージを開いてユーザー要求を再処理する

**事前に必要なロール**: グローバル管理者、ID ガバナンス管理者、ユーザー管理者、カタログ所有者、アクセス パッケージ マネージャーまたはアクセス パッケージ割り当てマネージャー

要求が "一部配信済み" または "失敗" の状態にある一連のユーザーがいる場合は、それらの要求の一部を再処理する必要がある場合があります。 既存のアクセス パッケージへの要求を再処理するには、次の手順に従います。

1.  [Azure portal](https://portal.azure.com) にサインインします。

1.  **[Azure Active Directory]** をクリックしてから **[Identity Governance]** をクリックします。

1.  左側のメニューで **[アクセス パッケージ]** をクリックして、アクセス パッケージを開きます。

1.  左側の **[管理]** の下にある **[要求]** をクリックします。

1.  再処理する要求を持つすべてのユーザーを選択します。

1.  **[再処理]** をクリックします。

## <a name="next-steps"></a>次のステップ

- [View requests for an access package](entitlement-management-access-package-requests.md) (アクセス パッケージの要求を表示する)
- [アクセス要求を承認または拒否する](entitlement-management-request-approve.md)