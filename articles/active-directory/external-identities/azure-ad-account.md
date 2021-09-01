---
title: Azure AD アカウント ID プロバイダー
description: Azure Active Directory を使用して、外部ユーザー (ゲスト) が Azure Active Directory 職場アカウントで Azure AD アプリにサインインできるようにします。
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 08/09/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.collection: M365-identity-device-management
ms.openlocfilehash: 00edf144e06710204bf5d6eb477187668b3e5237
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/14/2021
ms.locfileid: "122182964"
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

## <a name="verifying-the-applications-publisher-domain"></a>アプリケーションのパブリッシャー ドメインを検証する
2020 年 11 月現在、新しいアプリケーション登録は、[アプリケーションの発行元ドメインが検証済み](../develop/howto-configure-publisher-domain.md) "***かつ***" 会社の ID が Microsoft Partner Network で検証され、アプリケーションに関連付けられている場合を除き、ユーザーの同意プロンプトで未検証として表示されます。 (この変更の詳細については[こちら](../develop/publisher-verification-overview.md)を参照してください)。Azure AD ユーザー フローの場合、発行元のドメインは、Microsoft アカウントまたはその他の [Azure AD テナント](microsoft-account.md)を ID プロバイダーとして使用する場合にのみ表示されることに注意してください。 これらの新しい要件を満たすには、以下を実行します。

1. [自分の Microsoft Partner Network (MPN) アカウントを使用して会社 ID を確認します](/partner-center/verification-responses)。 このプロセスにより、会社と会社の主要連絡先に関する情報が検証されます。
1. 発行元の確認プロセスを完了し、次のいずれかのオプションを使用して、MPN アカウントをアプリ登録に関連付けます。
   - Microsoft アカウント ID プロバイダーのアプリの登録が Azure AD テナント内にある場合は、[アプリ登録ポータルでアプリを検証](../develop/mark-app-as-publisher-verified.md)します。
   - Microsoft アカウント ID プロバイダーのアプリの登録が Azure AD B2C テナント内にある場合、[Microsoft Graph API を使用して発行元がアプリを検証済みとしてマーク](../develop/troubleshoot-publisher-verification.md#making-microsoft-graph-api-calls)します (たとえば、Graph Explorer を使用)。

## <a name="next-steps"></a>次のステップ

- [Azure Active Directory B2B コラボレーション ユーザーを追加する](add-users-administrator.md)
- [セルフサービス サインアップをアプリに追加する](self-service-sign-up-user-flow.md)