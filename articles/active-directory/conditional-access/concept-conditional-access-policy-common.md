---
title: 一般的な条件付きアクセス ポリシー - Azure Active Directory
description: 組織で一般的に使用される条件付きアクセス ポリシー
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 12/03/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: bf338ad0c555038cd99c3604d69ab80371d6ea22
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/04/2019
ms.locfileid: "74804976"
---
# <a name="common-conditional-access-policies"></a>一般的な条件付きアクセス ポリシー

ベースライン保護ポリシーは優れていますが、多くの組織では、それによって提供されるものよりも高い柔軟性が必要です。 たとえば、多くの組織では、緊急アクセス用または非常用の管理アカウントのような特定のアカウントを、多要素認証を要求する条件付きアクセス ポリシーから除外できることが必要です。 それらの組織では、この記事で参照している一般的なポリシーが役に立つ可能性があります。

![Azure portal における条件付きアクセス ポリシー](./media/concept-conditional-access-policy-common/conditional-access-policies-azure-ad-listing.png)

## <a name="emergency-access-accounts"></a>緊急アクセス アカウント

緊急アクセス アカウントとそれが重要である理由の詳細については、以下の記事を参照してください。 

* [Azure AD で緊急アクセス用アカウントを管理する](../users-groups-roles/directory-emergency-access.md)
* [Azure Active Directory で回復性があるアクセス制御管理戦略を作成する](../authentication/concept-resilient-controls.md)

## <a name="typical-policies-deployed-by-organizations"></a>組織によってデプロイされる一般的なポリシー

* [管理者に対して MFA を必須にする](howto-conditional-access-policy-admin-mfa.md)
* [Azure 管理のために MFA を必須にする](howto-conditional-access-policy-azure-management.md)
* [すべてのユーザーに対して MFA を必須にする](howto-conditional-access-policy-all-users-mfa.md)
* [レガシ認証をブロックする](howto-conditional-access-policy-block-legacy.md)
* [リスクベースの条件付きアクセス (Azure AD Premium P2 が必要)](howto-conditional-access-policy-risk.md)
* [信頼できる場所を MFA 登録で必須にする](howto-conditional-access-policy-registration.md)
* [場所ごとにアクセスをブロックする](howto-conditional-access-policy-location.md)
* [準拠しているデバイスを要求する](howto-conditional-access-policy-compliant-device.md)

## <a name="next-steps"></a>次の手順

[Simulate sign in behavior using the Conditional Access What If tool](troubleshoot-conditional-access-what-if.md) (条件付きアクセスの What If ツールを使用したサインイン動作のシミュレート)
