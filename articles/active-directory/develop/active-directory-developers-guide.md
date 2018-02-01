---
title: "開発者のための Azure Active Directory | Microsoft Docs"
description: "この記事では、Azure Active Directory を使用した Microsoft の職場および学校アカウントのサインインの概要について説明します。"
services: active-directory
author: dstrockis
manager: mtillman
editor: 
ms.assetid: 5c872c89-ef04-4f4c-98de-bc0c7460c7c2
ms.service: active-directory
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/07/2017
ms.author: dastrock
ms.custom: aaddev
ms.openlocfilehash: e1f9fbf6cb80065ea796e2d53d09f48fe57b207b
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/01/2018
---
# <a name="azure-active-directory-for-developers"></a>開発者のための Azure Active Directory
Azure Active Directory (Azure AD) はクラウドの ID サービスです。開発者はこのサービスを使って、Microsoft の職場または学校アカウントによる安全なサインインをユーザーに提供することができます。 このドキュメントでは、業界標準のプロトコルである OAuth 2.0 と OpenID Connect を使用して、アプリケーションに Azure AD のサポートを追加する方法を示します。

| | |
| --- | --- |
|[認証の基本](active-directory-authentication-scenarios.md) | Azure AD での認証の概要。 |
|[アプリケーションの種類](active-directory-authentication-scenarios.md#application-types-and-scenarios) | Azure AD でサポートされる認証シナリオの概要。 |                                
                                                                              
## <a name="get-started"></a>作業開始
以下に示したガイド付きの手順では、Microsoft の認証ライブラリを使用して、Azure AD ユーザーをサインインさせる方法について説明します。

|  |  |  |  |
| --- | --- | --- | --- |
| <center>![モバイル アプリとデスクトップ アプリ](./media/active-directory-developers-guide/NativeApp_Icon.png)<br />モバイル アプリとデスクトップ アプリ</center> | [概要](active-directory-authentication-scenarios.md#native-application-to-web-api)<br /><br />[iOS](active-directory-devquickstarts-ios.md)<br /><br />[Android](active-directory-devquickstarts-android.md) | [.NET (WPF)](active-directory-devquickstarts-dotnet.md)<br /><br />[.NET (UWP)](active-directory-devquickstarts-windowsstore.md)<br /><br />[Xamarin](active-directory-devquickstarts-xamarin.md) | [Cordova](active-directory-devquickstarts-cordova.md) |
| <center>![Web アプリ](./media/active-directory-developers-guide/Web_app.png)<br />Web アプリ</center> | [概要](active-directory-authentication-scenarios.md#web-browser-to-web-application)<br /><br />[ASP.NET](active-directory-devquickstarts-webapp-dotnet.md)<br /><br />[Java](active-directory-devquickstarts-webapp-java.md) | [Node.JS](active-directory-devquickstarts-openidconnect-nodejs.md) |  |
| <center>![シングル ページ アプリ](./media/active-directory-developers-guide/SPA.png)<br />シングル ページ アプリ</center> | [概要](active-directory-authentication-scenarios.md#single-page-application-spa)<br /><br />[AngularJS](active-directory-devquickstarts-angular.md)<br /><br />[JavaScript](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) |  |  |
| <center>![Web API](./media/active-directory-developers-guide/Web_API.png)<br />Web API</center> | [概要](active-directory-authentication-scenarios.md#web-application-to-web-api)<br /><br />[ASP.NET](active-directory-devquickstarts-webapi-dotnet.md)<br /><br />[Node.JS](active-directory-devquickstarts-webapi-nodejs.md) | &nbsp; |
| <center>![サービス間](./media/active-directory-developers-guide/Service_App.png)<br />サービス間</center> | [概要](active-directory-authentication-scenarios.md#daemon-or-server-application-to-web-api)<br /><br />[.NET](active-directory-code-samples.md#server-or-daemon-application-to-web-api)|  |

## <a name="how-to-guides"></a>ハウツー ガイド
次のガイドでは、Azure AD を使用して一般的なタスクを実行する方法について説明しています。

|                                                                           |  |
|---------------------------------------------------------------------------| --- |
|[アプリケーションの登録](active-directory-integrating-applications.md)           | Azure AD にアプリケーションを登録する方法。 |
|[マルチテナント アプリケーション](active-directory-devhowto-multi-tenant-overview.md)    | Microsoft の職場アカウントでサインインできるようにする方法。 |
|[OAuth と OpenID Connect プロトコル](active-directory-protocols-openid-connect-code.md)| Microsoft の認証プロトコルを使用してユーザーをサインインさせたり、Web API を呼び出したりする方法。 |
|[その他のガイド](active-directory-developers-guide-index.md#guides)        |  Azure AD に関連したガイドの一覧。   |

## <a name="reference-topics"></a>参照トピック
次の記事には、Azure AD で使用される API、プロトコル メッセージ、用語に関する詳細な情報が記載されています。

|                                                                                   | |
| ----------------------------------------------------------------------------------| --- |
| [認証ライブラリ (ADAL)](active-directory-authentication-libraries.md)   | Azure AD が提供するライブラリと SDK の概要。 |
| [コード サンプル](active-directory-code-samples.md)                                  | Azure AD のコード サンプルの全一覧。 |
| [用語集](active-directory-dev-glossary.md)                                      | このドキュメント全体で使用されている用語と語句の定義。 |
| [その他のリファレンス トピック](active-directory-developers-guide-index.md#reference)| Azure AD に関連したリファレンス トピックの一覧。   |


> [!NOTE]
> Microsoft の個人アカウントでサインインできるようにする必要がある場合は、[Azure AD v2.0 エンドポイント](active-directory-appmodel-v2-overview.md)の使用を検討してください。 Azure AD v2.0 エンドポイントは、(Azure AD から) Microsoft の個人アカウントと Microsoft の職場アカウントを 1 つの認証システムに統合します。


[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
