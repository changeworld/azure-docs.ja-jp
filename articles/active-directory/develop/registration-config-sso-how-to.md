---
title: アプリケーションのシングル サインオンを構成する
description: Azure AD で開発中または登録中のカスタム アプリケーションのシングル サインオンを構成する方法。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev, has-adal-ref
ms.workload: identity
ms.topic: conceptual
ms.date: 07/15/2019
ms.author: ryanwi
ROBOTS: NOINDEX
ms.openlocfilehash: 62d4c17ff1482208c4a70bb73a257f709a0d7380
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131505823"
---
# <a name="how-to-configure-single-sign-on-for-an-application"></a>アプリケーションのシングル サインオンを構成する方法

アプリのフェデレーション シングル サインオン (SSO) は、OpenID Connect、SAML 2.0、または WS-Fed を使用して Azure AD 経由でフェデレーションしている場合、自動的に有効になります。 Azure AD とのセッションが既に存在しているのにエンド ユーザーのサインインが必要な場合、アプリが正しく構成されていない可能性があります。

* Microsoft Authentication Library (MSAL) を使用している場合、**PromptBehavior** を、**Always** ではなく **Auto** に設定します。

* モバイル アプリを作成している場合、仲介型または仲介型でない SSO を有効にするために、追加の構成が必要になることがあります。

Android については、[Android でクロス アプリ SSO を有効にする方法](../azuread-dev/howto-v1-enable-sso-android.md)に関するページを参照してください。<br>

iOS については、[iOS でクロス アプリ SSO を有効にする方法](../azuread-dev/howto-v1-enable-sso-ios.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

[Azure AD SSO](../manage-apps/what-is-single-sign-on.md)<br>

[Android でクロス アプリ SSO を有効にする方法](../azuread-dev/howto-v1-enable-sso-android.md)<br>

[iOS でクロス アプリ SSO を有効にする方法](../azuread-dev/howto-v1-enable-sso-ios.md)<br>

[アプリと Azure AD の統合](./quickstart-register-app.md)<br>

[Microsoft ID プラットフォームでのアクセス許可と同意](./v2-permissions-and-consent.md)<br>

[AzureAD Microsoft Q&A](/answers/topics/azure-active-directory.html)
