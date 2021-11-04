---
title: Azure Active Directory での SMS ベースの認証のためのアプリ サポート
description: ユーザーが SMS を使用して Azure Active Directory にサインインするためにサポートされているアプリについて説明します
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 06/28/2021
ms.author: justinha
author: aanjusingh
manager: daveba
ms.reviewer: anjusingh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 31a35994441ad2b0cd2ecdf39675ef51840dc0de
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131027951"
---
# <a name="app-support-for-sms-based-authentication"></a>SMS ベースの認証のためのアプリ サポート

Microsoft ID プラットフォーム (Azure AD) と統合された Microsoft アプリでは、SMS ベースの認証を利用できます。 この表は、SMS ベースの認証がサポートされている Web およびモバイル アプリの一部を示しています。 アプリを追加または検証したい場合は、[お問い合わせください](https://feedback.azure.com/d365community/forum/22920db1-ad25-ec11-b6e6-000d3a4f0789)。 

| アプリ | Web/ブラウザー アプリ | ネイティブ モバイル アプリ |
| --- |:---:|:---:|
| Office 365- Microsoft Online Services* | ● |   |
| Microsoft One Note | ● |   |
| Microsoft Teams | ● | ● |
| Microsoft Intune/会社ポータル | ● | ● |
| マイ アプリ ポータル | ● |使用できません。|
| Microsoft フォーム | ● |使用できません。|
| Microsoft Edge | ● |   |
| Microsoft Power BI | ● |   |
| Microsoft Stream | ● |   |
| Microsoft Power Apps | ● |   |
| Microsoft Azure | ● | ● |
| Microsoft Authenticator |   | ● |
| Azure Virtual Desktop | ● |  | 

*_Word や Excel などの Office アプリケーションでは、SMS サインインは Web で直接アクセスした場合は使用できませんが、[Office 365 Web アプリ](https://www.office.com)を使用してアクセスした場合は使用できます。_

上記の Microsoft アプリでは、SMS サインインがサポートされています。これは、ユーザーが電話番号と SMS コードを入力できる、Microsoft ID ログイン (`https://login.microsoftonline.com/`) が使用されるためです。

## <a name="unsupported-microsoft-apps"></a>サポートされていない Microsoft アプリ

Web で直接アクセスされる Microsoft 365 デスクトップ (Windows または Mac) アプリと Microsoft 365 Web アプリ (MS One Note を除く) では SMS サインインはサポートされません。 これらのアプリでは、サインインにパスワードを必要とする Microsoft Office ログイン (`https://office.live.com/start/*`) が使用されます。
同じ理由で、Microsoft Office モバイル アプリ (Microsoft Teams、Intune ポータル サイト、Microsoft Azure を除く) では SMS サインインはサポートされません。

| サポートされていない Microsoft アプリ| 例 |
| --- | --- |
| ネイティブのデスクトップ Microsoft アプリ | Microsoft Teams、O365 アプリ、Word、Excel など|
| ネイティブのモバイル Microsoft アプリ (Microsoft Teams、Intune ポータル サイト、Microsoft Azure を除く) | Outlook、Edge、Power BI、Stream、SharePoint、Power Apps、Word など|
| Microsoft 365 Web アプリ (Web で直接アクセス) | [Outlook](https://outlook.live.com/owa/)、[Word](https://office.live.com/start/Word.aspx)、[Excel](https://office.live.com/start/Excel.aspx)、[PowerPoint](https://office.live.com/start/PowerPoint.aspx)、[OneDrive](https://onedrive.live.com/about/signin)|  

## <a name="support-for-non-microsoft-apps"></a>Microsoft 以外のアプリのサポート 

Microsoft 以外のアプリを SMS サインイン機能と互換性があるようにするには、次のようにします。 
- Microsoft 以外の Web アプリを Azure AD と統合し、Azure AD 認証を使用します。 Security Assertion Markup Language ([SAML](../manage-apps/add-application-portal-setup-sso.md)) または Open ID Connect ([OIDC](../manage-apps/add-application-portal-setup-oidc-sso.md)) を使用して、Azure AD SSO と統合します。 
- [Azure AD アプリケーション プロキシ](../app-proxy/application-proxy-add-on-premises-application.md)を使用して、Microsoft 以外のオンプレミス アプリを Azure AD と統合します
- Microsoft 以外のクライアント アプリを認証用の [Microsoft ID プラットフォーム](../develop/v2-overview.md)と統合します 
    - [サンプルの iOS アプリ](../develop/tutorial-v2-ios.md)
    - [サンプルの Android アプリ](../develop/tutorial-v2-android.md)

## <a name="next-steps"></a>次のステップ

- [ユーザーに対して SMS ベースのサインインを有効にする方法](howto-authentication-sms-signin.md)
- MSAL ライブラリを使用してネイティブ モバイル アプリの SMS サインインを有効にするには、次のリンクを参照してください。 
  - [iOS](https://github.com/AzureAD/microsoft-authentication-library-for-objc)
  - [Android](https://github.com/AzureAD/microsoft-authentication-library-for-android)
- [SaaS アプリケーションと Azure Active Directory の統合](../saas-apps/tutorial-list.md)

## <a name="recommended-content"></a>推奨コンテンツ

- [Azure Active Directory にアプリケーションを追加する](../manage-apps/add-application-portal.md)
- [ユーザーを認証するために Microsoft ID プラットフォームからトークンを取得するための MSAL ライブラリの概要](../develop/msal-overview.md)
- [Microsoft Managed Home Screen を Azure AD で構成する](/mem/intune/apps/app-configuration-managed-home-screen-app)
