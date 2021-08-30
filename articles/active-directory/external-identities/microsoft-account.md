---
title: Azure AD での Microsoft アカウント (MSA) ID プロバイダー
description: Azure AD を使用して、外部ユーザー (ゲスト) が Microsoft アカウント (MSA) で Azure AD アプリにサインインできるようにします。
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 08/09/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2333ba6aec10d124d960dfc746bc9df2c449f3bb
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/14/2021
ms.locfileid: "122178844"
---
# <a name="microsoft-account-msa-identity-provider-for-external-identities"></a>External Identities 用の Microsoft アカウント (MSA) ID プロバイダー

B2B ゲスト ユーザーは、追加の構成を行わなくても、B2B コラボレーションに自分の個人用 Microsoft アカウントを使用できます。 ゲスト ユーザーは、自分の個人用 Microsoft アカウントを使用して、B2B コラボレーションの招待に応じたり、サインアップ ユーザー フローを実行したりすることができます。

コンシューマー向けの Microsoft 製品およびクラウド サービス (Outlook、OneDrive、Xbox LIVE、Microsoft 365 など) にアクセスするために、ユーザー自身が Microsoft アカウントを設定します。 このアカウントは、Microsoft が運営する Microsoft コンシューマー ID アカウント システムを使用して作成、保存されます。

## <a name="guest-sign-in-using-microsoft-accounts"></a>Microsoft アカウントを使用したゲストのサインイン

Microsoft アカウントは、既定では、External Identities の ID プロバイダーの一覧で入手できます。 追加の構成を行わなくても、ゲスト ユーザーは招待フローまたはセルフサービス サインアップ ユーザー フローのどちらかを使用して、自分の Microsoft アカウントでサインインできます。

![ID プロバイダー一覧の Microsoft アカウント](media/microsoft-account/microsoft-account-identity-provider.png)

### <a name="microsoft-account-in-the-invitation-flow"></a>招待フローの Microsoft アカウント

B2B コラボレーションに[ゲスト ユーザーを招待](add-users-administrator.md)する場合、その Microsoft アカウントを、サインインに使用する電子メール アドレスとして指定できます。

![Microsoft アカウントを使用した招待](media/microsoft-account/microsoft-account-invite.png)

### <a name="microsoft-account-in-self-service-sign-up-user-flows"></a>セルフサービス サインアップ ユーザー フローの Microsoft アカウント

Microsoft アカウントは、セルフサービス サインアップ ユーザー フロー用の ID プロバイダー オプションです。 ユーザーは、自分の Microsoft アカウントを使用してアプリケーションにサインアップできます。 最初に、テナントに対して[セルフサービス サインアップを有効にする](self-service-sign-up-user-flow.md)必要があります。 次に、アプリケーション用にユーザー フローを設定し、サインイン オプションの 1 つとして Microsoft アカウントを選択します。

![セルフサービス サインアップ ユーザー フローの Microsoft アカウント](media/microsoft-account/microsoft-account-user-flow.png)

## <a name="verifying-the-applications-publisher-domain"></a>アプリケーションのパブリッシャー ドメインを検証する
2020 年 11 月現在、新しいアプリケーション登録は、[アプリケーションの発行元ドメインが検証済み](../develop/howto-configure-publisher-domain.md)***かつ*** 会社の ID が Microsoft Partner Network で検証され、アプリケーションに関連付けられている場合を除き、ユーザーの同意プロンプトで未検証として表示されます (この変更の詳細については[こちら](../develop/publisher-verification-overview.md)を参照してください)。Azure AD ユーザー フローの場合、発行元のドメインは、Microsoft アカウントまたはその他の [Azure AD テナント](azure-ad-account.md)を ID プロバイダーとして使用する場合にのみ表示されることに注意してください。 これらの新しい要件を満たすには、次の手順を実行します。

1. [自分の Microsoft Partner Network (MPN) アカウントを使用して会社 ID を確認します](/partner-center/verification-responses)。 このプロセスにより、会社と会社の主要連絡先に関する情報が検証されます。
1. 発行元の確認プロセスを完了し、次のいずれかのオプションを使用して、MPN アカウントをアプリ登録に関連付けます。
   - Microsoft アカウント ID プロバイダーのアプリの登録が Azure AD テナント内にある場合は、[アプリ登録ポータルでアプリを検証](../develop/mark-app-as-publisher-verified.md)します。
   - Microsoft アカウント ID プロバイダーのアプリの登録が Azure AD B2C テナント内にある場合、[Microsoft Graph API を使用して発行元がアプリを検証済みとしてマーク](../develop/troubleshoot-publisher-verification.md#making-microsoft-graph-api-calls)します (たとえば、Graph Explorer を使用)。

## <a name="next-steps"></a>次のステップ

- [Azure Active Directory B2B コラボレーション ユーザーを追加する](add-users-administrator.md)
- [セルフサービス サインアップをアプリに追加する](self-service-sign-up-user-flow.md)