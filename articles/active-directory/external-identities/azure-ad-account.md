---
title: Azure AD アカウント ID プロバイダー
description: Azure Active Directory を使用して、外部ユーザー (ゲスト) が Azure Active Directory 職場アカウントで Azure AD アプリにサインインできるようにします。
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 06/02/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9d9d7a8e74767982d5089a1308ca36108eb5f106
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/10/2021
ms.locfileid: "111972535"
---
# <a name="azure-active-directory-azure-ad-identity-provider-for-external-identities"></a>外部 ID 向けの Azure Active Directory (Azure AD) ID プロバイダー

既定では、B2B コラボレーションの ID プロバイダーオプションとして Azure Active Directory を使用できます。 外部のゲスト ユーザーが職場または学校を通じて Azure AD アカウントを持っている場合は、Azure AD アカウントを使用して B2B コラボレーションの招待を利用したり、サインアップ ユーザーフローを完了したりできます。

## <a name="guest-sign-in-using-azure-active-directory-accounts"></a>Azure Active Directory アカウント を使用したゲスト サインイン

Azure Active Directory は、既定では、External Identities の ID プロバイダーの一覧で入手できます。 追加の構成を行わなくても、ゲスト ユーザーは招待フローまたはセルフサービス サインアップ ユーザー フローのどちらかを使用して、自分の Azure AD アカウントでサインインできます。

![ID プロバイダー一覧の Azure AD アカウント](media/azure-ad-account/azure-ad-account-identity-provider.png)

### <a name="azure-ad-account-in-the-invitation-flow"></a>招待フローの Azure AD アカウント

B2B コラボレーションに[ゲスト ユーザーを招待](add-users-administrator.md)する場合、その Azure AD アカウントを、サインインに使用する電子メール アドレスとして指定できます。

![Azure AD アカウントを使用して招待する](media/azure-ad-account/azure-ad-account-invite.png)

### <a name="azure-ad-account-in-self-service-sign-up-user-flows"></a>セルフサービス サインアップ ユーザー フローの Azure AD アカウント

Azure AD アカウントは、セルフサービス サインアップ ユーザー フロー用の ID プロバイダー オプションです。 ユーザーは、自分の Azure AD アカウントを使用してアプリケーションにサインアップできます。 最初に、テナントに対して[セルフサービス サインアップを有効にする](self-service-sign-up-user-flow.md)必要があります。 次に、アプリケーション用にユーザー フローを設定し、サインイン オプションの 1 つとして Azure Active Directory を選択します。

![セルフサービス サインアップ ユーザー フローの Azure AD アカウント](media/azure-ad-account/azure-ad-account-user-flow.png)

## <a name="next-steps"></a>次のステップ

- [Azure Active Directory B2B コラボレーション ユーザーを追加する](add-users-administrator.md)
- [セルフサービス サインアップをアプリに追加する](self-service-sign-up-user-flow.md)