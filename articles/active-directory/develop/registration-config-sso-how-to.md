---
title: アプリケーションのシングル サインオンを構成する
description: Azure AD で開発中または登録中のカスタム アプリケーションのシングル サインオンを構成する方法。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: conceptual
ms.date: 07/15/2019
ms.author: ryanwi
ms.openlocfilehash: 9b6451d29a3a874e09d74cbe664662a395b647f9
ms.sourcegitcommit: b396c674aa8f66597fa2dd6d6ed200dd7f409915
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/07/2020
ms.locfileid: "82890476"
---
# <a name="how-to-configure-single-sign-on-for-an-application"></a>アプリケーションのシングル サインオンを構成する方法

アプリのフェデレーション シングル サインオン (SSO) は、OpenID Connect、SAML 2.0、または WS-Fed を使用して Azure AD 経由でフェデレーションしている場合、自動的に有効になります。 Azure AD とのセッションが既に存在しているのにエンド ユーザーのサインインが必要な場合、アプリが正しく構成されていない可能性があります。

* ADAL/MSAL を使用している場合、**PromptBehavior** を、**Always** ではなく **Auto** にしてください。

* モバイル アプリを作成している場合、仲介型または仲介型でない SSO を有効にするために、追加の構成が必要になることがあります。

Android については、[Android でクロス アプリ SSO を有効にする方法](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-android)に関するページを参照してください。<br>

iOS については、[iOS でクロス アプリ SSO を有効にする方法](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-ios)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

[Azure AD SSO](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)<br>

[Android でクロス アプリ SSO を有効にする方法](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-android)<br>

[iOS でクロス アプリ SSO を有効にする方法](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-ios)<br>

[アプリと Azure AD の統合](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)<br>

[Microsoft ID プラットフォーム エンドポイントでのアクセス許可と同意](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes)<br>

[Azure AD StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory)
