---
title: Azure Active Directory を使用したパスワードベースの認証
description: Azure Active Directory を使用したパスワードベースの認証の取得に関するアーキテクチャ ガイダンス。
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/10/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 737a073a0360842d2ddd8010970e8a3461193742
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/10/2021
ms.locfileid: "111966048"
---
# <a name="password-based-authentication-with-azure-active-directory"></a>Azure Active Directory を使用したパスワードベースの認証

パスワードベースのシングル サインオン (SSO) では、アプリケーション用の既存の認証プロセスが使用されます。 パスワードベースの SSO を有効にすると、Azure Active Directory (Azure AD) でユーザー資格情報が収集され、暗号化され、ディレクトリに安全に格納されます。 ユーザーがサインインを試みると、Azure AD からユーザー名とパスワードがアプリケーションに渡されます。

アプリケーションでアクセス トークンとヘッダーではなく、ユーザー名とパスワードを使用して認証が行われる場合は、パスワードベースの SSO を選択します。 パスワードベースの SSO は、HTML ベースのサインイン ページを持つどのクラウドベース アプリケーションでもサポートされます。 

## <a name="use-when"></a>使用する状況

事前認証を使用して保護し、Web アプリへのパスワード保管を使用して SSO を提供する必要があります。

![アーキテクチャの図](./media/authentication-patterns/password-based-sso-auth.png)


## <a name="components-of-system"></a>システムのコンポーネント

* **[ユーザー]** :フォームベースのアプリケーションにマイ アプリからアクセスするか、サイトに直接アクセスしてアクセスします。 

* **Web ブラウザー**:アプリケーションの外部 URL にアクセスするためにユーザーが操作するコンポーネントです。 ユーザーは MyApps 拡張機能を使用して、フォームベースのアプリケーションにアクセスします。 

* **MyApps 拡張機能**:構成されたパスワードベースの SSO アプリケーションを識別し、サインイン フォームに資格情報を入力します。 MyApps 拡張機能は、Web ブラウザーにインストールされます。 

* **Azure AD**:ユーザーを認証します。

## <a name="implement-password-based-sso-with-azure-ad"></a>Azure AD を使用してパスワードベースの SSO を実装する

* [パスワードベースの SSO とは](../manage-apps/what-is-single-sign-on.md) 

* [クラウド アプリケーションにパスワードベースの SSO を構成する](../manage-apps/configure-password-single-sign-on-non-gallery-applications.md)

* [アプリケーション プロキシを使用してオンプレミスのアプリケーションにパスワードベースの SSO を構成する](../app-proxy/application-proxy-configure-single-sign-on-password-vaulting.md)