---
title: "開発者向けの azure Active Directory |Microsoft ドキュメント"
description: "この記事は、Microsoft 作業では署名の概要を説明し、学校アカウントを使用して Azure Active Directory です。"
services: active-directory
author: dstrockis
manager: mbaldwin
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
ms.openlocfilehash: dce813b885d492343428428056a2e8aada27b461
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2017
---
# <a name="azure-active-directory-for-developers"></a>開発者向けの azure Active Directory
Azure Active Directory は、クラウド id サービスにより、開発者は安全にサインインがあるユーザーが職場または学校のアカウントが Microsoft によってサポートです。  こちらのドキュメントでは、業界標準の認証プロトコル、OAuth および OpenID Connect を使用してアプリケーションを Azure AD のサポートを追加する方法を示します。

| | |
| --- | --- |
|[認証の基本](active-directory-authentication-scenarios.md) | Azure AD での認証の概要 |
|[アプリケーションの種類](active-directory-authentication-scenarios.md#application-types-and-scenarios) | Azure AD によってサポートされる認証のシナリオの概要 |                                
                                                                              
## <a name="get-started"></a>作業開始
これらの画面の指示に従って設定では、Azure Active Directory ユーザーのサインイン、認証ライブラリを使用して説明します。

|  |  |  |  |
| --- | --- | --- | --- |
| <center>![モバイル アプリとデスクトップ アプリ](./media/active-directory-developers-guide/NativeApp_Icon.png)<br />モバイル アプリとデスクトップ アプリ</center> | [概要](active-directory-authentication-scenarios.md#native-application-to-web-api)<br /><br />[iOS](active-directory-devquickstarts-ios.md)<br /><br />[Android](active-directory-devquickstarts-android.md) | [.NET](active-directory-devquickstarts-dotnet.md)<br /><br />[Windows](active-directory-devquickstarts-windowsstore.md)<br /><br />[Xamarin](active-directory-devquickstarts-xamarin.md) | [Cordova](active-directory-devquickstarts-cordova.md)<br /><br />[OAuth 2.0](active-directory-protocols-oauth-code.md) |
| <center>![Web アプリ](./media/active-directory-developers-guide/Web_app.png)<br />Web アプリ</center> | [概要](active-directory-authentication-scenarios.md#web-browser-to-web-application)<br /><br />[ASP.NET](active-directory-devquickstarts-webapp-dotnet.md)<br /><br />[Java](active-directory-devquickstarts-webapp-java.md) | [NodeJS](active-directory-devquickstarts-openidconnect-nodejs.md)<br /><br />[OpenID Connect 1.0](active-directory-protocols-openid-connect-code.md) |  |
| <center>![単一ページ アプリケーション](./media/active-directory-developers-guide/SPA.png)<br />単一ページ アプリケーション</center> | [概要](active-directory-authentication-scenarios.md#single-page-application-spa)<br /><br />[AngularJS](active-directory-devquickstarts-angular.md)<br /><br />[JavaScript](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) |  |  |
| <center>![Web Api](./media/active-directory-developers-guide/Web_API.png)<br />Web Api</center> | [概要](active-directory-authentication-scenarios.md#web-application-to-web-api)<br /><br />[ASP.NET](active-directory-devquickstarts-webapi-dotnet.md)<br /><br />[NodeJS](active-directory-devquickstarts-webapi-nodejs.md) | &nbsp; |
| <center>![Service to service](./media/active-directory-developers-guide/Service_App.png)<br />Service to Service</center> | [概要](active-directory-authentication-scenarios.md#daemon-or-server-application-to-web-api)<br /><br />[.NET](active-directory-code-samples.md#server-or-daemon-application-to-web-api)<br /><br />[OAuth 2.0 クライアント資格情報](active-directory-protocols-oauth-service-to-service.md) |  |

## <a name="guides"></a>ガイド
これらの記事では、Azure Active Directory での一般的なタスクを実行する方法を通知します。

|                                                                           |  |
|---------------------------------------------------------------------------| --- |
|[アプリの登録](active-directory-integrating-applications.md)           | Azure AD にアプリを登録する方法 |
|[マルチ テナント アプリ](active-directory-devhowto-multi-tenant-overview.md)    | Microsoft 作業アカウントにサインインする方法 |
|[OAuth および OpenID 接続します。](active-directory-protocols-openid-connect-code.md)| サインインする方法のユーザーと呼び出し web Api の最新の認証プロトコルを使用して |
|[. その他のガイド](active-directory-developers-guide-index.md#guides)        |     |

## <a name="reference"></a>リファレンス
これらの記事では、Api、プロトコル メッセージ、および Azure Active Directory で使用される用語の詳細な情報を提供します。

|                                                                                   | |
| ----------------------------------------------------------------------------------| --- |
| [認証ライブラリ (ADAL)](active-directory-authentication-libraries.md)   | ライブラリと Azure AD によって提供される Sdk の概要 |
| [コード サンプル](active-directory-code-samples.md)                                  | すべての Azure AD コード サンプルの一覧 |
| [用語集](active-directory-dev-glossary.md)                                      | 用語と、このドキュメント全体で使用される単語の定義 |
| [複数の参考資料しています.](active-directory-developers-guide-index.md#reference)|     |

## <a name="help--support"></a>ヘルプとサポート
これらは、Azure Active Directory での開発のヘルプを表示する最適な場所です。

|  |  
|---|
|[スタック オーバーフローの`azure-active-directory`と`adal`タグ](http://stackoverflow.com/questions/tagged/azure-active-directory+or+adal)      |
|[Azure Active Directory に関するフィードバック](https://feedback.azure.com/forums/169401-azure-active-directory/category/164757-developer-experiences)|
| [Microsoft デベロッパー チャット (無料時間が限られている) を試す](http://aka.ms/devchat) |

<br />

> [!NOTE]
> Microsoft をサインインする必要がある場合個人アカウントは、することも検討して、 [Azure AD v2.0 エンドポイント](active-directory-appmodel-v2-overview.md)です。  Azure AD v2.0 エンドポイントは、1 つの認証システムに (Azure AD) から Microsoft 作業アカウント (&)、個人アカウントは、Microsoft の統一がします。
