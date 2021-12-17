---
title: Microsoft ID プラットフォーム認証と認可のコード サンプル
description: Microsoft が管理するコードサンプルのインデックス。いくつかのアプリケーションの種類、開発言語、フレームワークにおける認証と承認を示します。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: sample
ms.workload: identity
ms.date: 08/27/2021
ms.author: marsma
ms.reviewer: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 3c3d6a4ed060f26e523bc29f2afe843352b6ee2b
ms.sourcegitcommit: 1f29603291b885dc2812ef45aed026fbf9dedba0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/29/2021
ms.locfileid: "129229869"
---
# <a name="microsoft-identity-platform-code-samples"></a>Microsoft ID プラットフォームのコード サンプル

これらのコードサンプルは、Microsoft によって構築および管理されており、Azure AD と、いくつかの[アプリケーションの種類](v2-app-types.md)、開発言語、フレームワークで Microsoft ID プラットフォームを使用した認証と承認を示しています。

- Web アプリケーションにユーザーをサインインさせ、保護された Web API への承認されたアクセスを提供します。
- API 操作を実行するためのアクセストークンを要求することで、Web API を保護します。

各コードサンプルには、プロジェクトをビルドする方法 (該当する場合) とサンプル アプリケーションを実行する方法を説明する _README.md_ ファイルが含まれています。 コード内のコメントは、認証ライブラリと ID プラットフォームを使用して認証と承認を実装する重要なセクションを理解するのに役立ちます。

## <a name="single-page-applications"></a>シングルページ アプリケーション

これらのサンプルでは、Microsoft ID プラットフォームを使用してセキュリティ保護されているシングルページ アプリケーションの作成方法を示します。 これらのサンプルでは、MSAL.js のいずれかの種類を使用します。

> [!div class="mx-tdCol2BreakAll"]
> | 言語/<br/>プラットフォーム | コード サンプル <br/>GitHub 上 | Auth<br/> libraries | 認証フロー |  
> | ------- | -------- | ------------- | -------------- |  
> | Angular | &#8226; [ユーザーのサインイン](https://github.com/Azure-Samples/ms-identity-javascript-angular-tutorial/tree/main/1-Authentication/1-sign-in/README.md)<br/>&#8226; [ユーザーのサインイン (B2C)](https://github.com/Azure-Samples/ms-identity-javascript-angular-tutorial/tree/main/1-Authentication/2-sign-in-b2c/README.md) <br/> &#8226; [Microsoft Graph の呼び出し](https://github.com/Azure-Samples/ms-identity-javascript-angular-tutorial/tree/main/2-Authorization-I/1-call-graph/README.md)<br/>&#8226; [.NET Core Web API の呼び出し](https://github.com/Azure-Samples/ms-identity-javascript-angular-tutorial/tree/main/3-Authorization-II/1-call-api)<br/>&#8226; [.NET Core Web API の呼び出し(B2C)](https://github.com/Azure-Samples/ms-identity-javascript-angular-tutorial/tree/main/3-Authorization-II/2-call-api-b2c)<br/>&#8226; [OBO を介した Microsoft Graph の呼び出し](https://github.com/Azure-Samples/ms-identity-javascript-angular-tutorial/tree/main/7-AdvancedScenarios/1-call-api-obo/README.md)<br/>&#8226; [PoP を使用した .NET Core Web API](https://github.com/Azure-Samples/ms-identity-javascript-angular-tutorial/tree/main/7-AdvancedScenarios/2-call-api-pop/README.md)<br/>&#8226; [アクセス制御にアプリのロールを使用する](https://github.com/Azure-Samples/ms-identity-javascript-angular-tutorial/tree/main/5-AccessControl/1-call-api-roles/README.md)<br/> &#8226; [アクセス制御にセキュリティ グループを使用する](https://github.com/Azure-Samples/ms-identity-javascript-angular-tutorial/tree/main/5-AccessControl/2-call-api-groups/README.md)<br/>&#8226; [Azure Storage および App Service へのデプロイ](https://github.com/Azure-Samples/ms-identity-javascript-angular-tutorial/tree/main/4-Deployment/README.md)| MSAL Angular | &#8226; 認可コードと PKCE<br/>&#8226; On-Behalf-of (OBO) <br/>&#8226; 所有証明 (PoP)|
> | Blazor WebAssembly | &#8226; [ユーザーのサインイン](https://github.com/Azure-Samples/ms-identity-blazor-wasm/blob/main/WebApp-OIDC/MyOrg/README.md)<br/>&#8226; [ユーザーのサインイン (B2C)](https://github.com/Azure-Samples/ms-identity-blazor-wasm/blob/main/WebApp-OIDC/B2C/README.md)<br/>&#8226; [Microsoft Graph の呼び出し](https://github.com/Azure-Samples/ms-identity-blazor-wasm/blob/main/WebApp-graph-user/Call-MSGraph/README.md)<br/>&#8226; [Azure App Service にデプロイ](https://github.com/Azure-Samples/ms-identity-blazor-wasm/blob/main/Deploy-to-Azure/README.md) | MSAL.js | 認可コードと PKCE |
> | JavaScript | &#8226; [ユーザーのサインイン](https://github.com/Azure-Samples/ms-identity-javascript-tutorial/tree/main/1-Authentication/1-sign-in/README.md)<br/>&#8226; [ユーザーのサインイン (B2C)](https://github.com/Azure-Samples/ms-identity-javascript-tutorial/tree/main/1-Authentication/2-sign-in-b2c/README.md) <br/> &#8226; [Microsoft Graph の呼び出し](https://github.com/Azure-Samples/ms-identity-javascript-tutorial/tree/main/2-Authorization-I/1-call-graph/README.md)<br/>&#8226; [Node.js Web API の呼び出し](https://github.com/Azure-Samples/ms-identity-javascript-tutorial/tree/main/3-Authorization-II/1-call-api/README.md)<br/>&#8226; [Node.js Web API の呼び出し (B2C)](https://github.com/Azure-Samples/ms-identity-javascript-tutorial/tree/main/3-Authorization-II/2-call-api-b2c/README.md)<br/>&#8226; [OBO を介した Microsoft Graph の呼び出し](https://github.com/Azure-Samples/ms-identity-javascript-tutorial/tree/main/4-AdvancedGrants/1-call-api-graph/README.md)<br/>&#8226; [OBO と CA を介した Node.js Web API の呼び出し](https://github.com/Azure-Samples/ms-identity-javascript-tutorial/tree/main/4-AdvancedGrants/2-call-api-api-ca/README.md)<br/>&#8226; [Azure Storage および App Service へのデプロイ](https://github.com/Azure-Samples/ms-identity-javascript-tutorial/tree/main/5-Deployment/README.md)| MSAL.js | &#8226; 認可コードと PKCE<br/>&#8226; On-Behalf-of (OBO) <br/>&#8226; 条件付きアクセス (CA) |
> | React | &#8226; [ユーザーのサインイン](https://github.com/Azure-Samples/ms-identity-javascript-react-tutorial/tree/main/1-Authentication/1-sign-in/README.md)<br/>&#8226; [ユーザーのサインイン (B2C)](https://github.com/Azure-Samples/ms-identity-javascript-react-tutorial/tree/main/1-Authentication/2-sign-in-b2c/README.md) <br/> &#8226; [Microsoft Graph の呼び出し](https://github.com/Azure-Samples/ms-identity-javascript-react-tutorial/tree/main/2-Authorization-I/1-call-graph/README.md)<br/>&#8226; [Node.js Web API の呼び出し](https://github.com/Azure-Samples/ms-identity-javascript-react-tutorial/tree/main/3-Authorization-II/1-call-api)<br/>&#8226; [Node.js Web API の呼び出し (B2C)](https://github.com/Azure-Samples/ms-identity-javascript-react-tutorial/tree/main/3-Authorization-II/2-call-api-b2c)<br/>&#8226; [OBO を介した Microsoft Graph の呼び出し](https://github.com/Azure-Samples/ms-identity-javascript-react-tutorial/tree/main/6-AdvancedScenarios/1-call-api-obo/README.md)<br/>&#8226; [PoP を使用した Node.js Web API の呼び出し](https://github.com/Azure-Samples/ms-identity-javascript-react-tutorial/tree/main/6-AdvancedScenarios/2-call-api-pop/README.md)<br/>&#8226; [アクセス制御にアプリのロールを使用する](https://github.com/Azure-Samples/ms-identity-javascript-react-tutorial/tree/main/5-AccessControl/1-call-api-roles/README.md)<br/>&#8226; [アクセス制御にセキュリティ グループを使用する](https://github.com/Azure-Samples/ms-identity-javascript-react-tutorial/tree/main/5-AccessControl/2-call-api-groups/README.md)<br/>&#8226; [Azure Storage および App Service へのデプロイ](https://github.com/Azure-Samples/ms-identity-javascript-react-tutorial/tree/main/4-Deployment/1-deploy-storage/README.md)<br/>&#8226; [Azure Static Web Apps へのデプロイ](https://github.com/Azure-Samples/ms-identity-javascript-react-tutorial/tree/main/4-Deployment/2-deploy-static/README.md)| MSAL React | &#8226; 認可コードと PKCE<br/>&#8226; On-Behalf-of (OBO) <br/>&#8226; 条件付きアクセス (CA)<br/>&#8226; 所有証明 (PoP) |

## <a name="web-applications"></a>Web アプリケーション

次のサンプルは、ユーザーがサインインする Web アプリケーションを示しています。 サンプルの中には、Microsoft Graph、またはユーザーの ID を使用してユーザー独自の Web API を呼び出すアプリケーションを示しているものもあります。

> [!div class="mx-tdCol2BreakAll"]
> | 言語/<br/>プラットフォーム | コード サンプル<br/> GitHub 上 | Auth<br/> libraries | 認証フロー |
> | ------- | --------------------------- | ------------- | -------------- |
> | ASP.NET Core| ASP.NET Core シリーズ <br/> &#8226; [ユーザーのサインイン](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/1-WebApp-OIDC/README.md) <br/> &#8226; [ユーザーのサインイン (B2C)](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/1-WebApp-OIDC/1-5-B2C/README.md) <br/> &#8226; [Microsoft Graph の呼び出し](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/2-WebApp-graph-user/2-1-Call-MSGraph/README.md) <br/> &#8226; [トークン キャッシュのカスタマイズ](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/2-WebApp-graph-user/2-2-TokenCache/README.md) <br/> &#8226; [Graph の呼び出し(マルチテナント)](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/2-WebApp-graph-user/2-3-Multi-Tenant/README.md) <br/> &#8226; [Azure REST API の呼び出し](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/3-WebApp-multi-APIs/README.md) <br/> &#8226; [Web API の保護](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/4-WebApp-your-API/4-1-MyOrg/README.md) <br/> &#8226; [Web API の保護 (B2C)](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/4-WebApp-your-API/4-2-B2C/README.md) <br/> &#8226; [マルチテナント WEB API を保護](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/4-WebApp-your-API/4-3-AnyOrg/Readme.md) <br/> &#8226; [アクセス制御にアプリのロールを使用する](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/5-WebApp-AuthZ/5-1-Roles/README.md) <br/> &#8226; [アクセス制御にセキュリティ グループを使用する](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/5-WebApp-AuthZ/5-2-Groups/README.md) <br/> &#8226; [Azure Storage および App Service へのデプロイ](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/6-Deploy-to-Azure/README.md) | &#8226; MSAL.NET<br/> &#8226; Microsoft.Identity.Web | &#8226; OpenID Connect <br/> &#8226; 認可コード <br/> &#8226; On-Behalf-Of|
> | Blazor | Blazor Serverシリーズ <br/> &#8226; [ユーザーのサインイン](https://github.com/Azure-Samples/ms-identity-blazor-server/tree/main/WebApp-OIDC/MyOrg) <br/> &#8226; [ユーザーのサインイン (B2C)](https://github.com/Azure-Samples/ms-identity-blazor-server/tree/main/WebApp-OIDC/B2C) <br/> &#8226; [Microsoft Graph の呼び出し](https://github.com/Azure-Samples/ms-identity-blazor-server/tree/main/WebApp-graph-user/Call-MSGraph) <br/> &#8226; [Web API の呼び出し](https://github.com/Azure-Samples/ms-identity-blazor-server/tree/main/WebApp-your-API/MyOrg) <br/> &#8226; [Web API の呼び出し (B2C)](https://github.com/Azure-Samples/ms-identity-blazor-server/tree/main/WebApp-your-API/B2C) | MSAL.NET | 認可コードと PKCE|
> | ASP.NET Core|[高度なトークン キャッシュのシナリオ](https://github.com/Azure-Samples/ms-identity-dotnet-advanced-token-cache) | &#8226; MSAL.NET <br/> &#8226; Microsoft.Identity.Web | On-Behalf-Of (OBO) |
> | ASP.NET Core|[条件付きアクセス認証コンテキストを使用して\-ステップアップ認証を実行](https://github.com/Azure-Samples/ms-identity-dotnetcore-ca-auth-context-app/blob/main/README.md) | &#8226; MSAL.NET <br/> &#8226; Microsoft.Identity.Web | Authorization code (承認コード) |
> | ASP.NET Core|[Active Directory FS から Azure AD への移行](https://github.com/Azure-Samples/ms-identity-dotnet-adfs-to-aad) | MSAL.NET | &#8226; SAML <br/> &#8226; OpenID Connect |
> | ASP.NET | &#8226; [Microsoft Graph トレーニング サンプル](https://github.com/microsoftgraph/msgraph-training-aspnetmvcapp) <br/> &#8226; [ユーザーのサインインと Microsoft Graph の呼び出し](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect) <br/> &#8226; [ユーザーのサインインと、管理者が制限したスコープでの Microsoft Graph の呼び出し](https://github.com/azure-samples/active-directory-dotnet-admin-restricted-scopes-v2) <br/> &#8226; [クイックスタート: ユーザーのサインイン](https://github.com/AzureAdQuickstarts/AppModelv2-WebApp-OpenIDConnect-DotNet) | MSAL.NET | &#8226; OpenID Connect <br/> &#8226; 認可コード |
> | Java </p> Spring |Azure AD Spring Boot スターター シリーズ <br/> &#8226; [ユーザーのサインイン](https://github.com/Azure-Samples/ms-identity-java-spring-tutorial/tree/main/1-Authentication/sign-in) <br/> &#8226; [ユーザーのサインイン (B2C)](https://github.com/Azure-Samples/ms-identity-java-spring-tutorial/tree/main/1-Authentication/sign-in-b2c) <br/> &#8226; [Microsoft Graph の呼び出し](https://github.com/Azure-Samples/ms-identity-java-spring-tutorial/tree/main/2-Authorization-I/call-graph) <br/> &#8226; [アクセス制御にアプリのロールを使用する](https://github.com/Azure-Samples/ms-identity-java-spring-tutorial/tree/main/3-Authorization-II/roles) <br/> &#8226; [アクセス制御にグループを使用する](https://github.com/Azure-Samples/ms-identity-java-spring-tutorial/tree/main/3-Authorization-II/groups) <br/> &#8226; [Azure App Service にデプロイ](https://github.com/Azure-Samples/ms-identity-java-spring-tutorial/tree/main/4-Deployment/deploy-to-azure-app-service) | &#8226; MSAL Java <br/> &#8226; Azure AD Boot Starter | Authorization code (承認コード) |
> | Java </p> Servlets | Spring-less サーブレット シリーズ <br/> &#8226; [ユーザーのサインイン](https://github.com/Azure-Samples/ms-identity-java-servlet-webapp-authentication/tree/main/1-Authentication/sign-in) <br/> &#8226; [ユーザーのサインイン (B2C)](https://github.com/Azure-Samples/ms-identity-java-servlet-webapp-authentication/tree/main/1-Authentication/sign-in-b2c) <br/> &#8226; [Microsoft Graph の呼び出し](https://github.com/Azure-Samples/ms-identity-java-servlet-webapp-authentication/tree/main/2-Authorization-I/call-graph) <br/> &#8226; [アクセス制御にアプリのロールを使用する](https://github.com/Azure-Samples/ms-identity-java-servlet-webapp-authentication/tree/main/3-Authorization-II/roles) <br/> &#8226; [アクセス制御にセキュリティ グループを使用する](https://github.com/Azure-Samples/ms-identity-java-servlet-webapp-authentication/tree/main/3-Authorization-II/groups) <br/> &#8226; [Azure App Service にデプロイする](https://github.com/Azure-Samples/ms-identity-java-servlet-webapp-authentication/tree/main/4-Deployment/deploy-to-azure-app-service) | MSAL Java | Authorization code (承認コード) |
> | Java | [ユーザーのサインインと Microsoft Graph の呼び出し](https://github.com/Azure-Samples/ms-identity-java-webapp)| MSAL Java | Authorization code (承認コード) |
> | Java </p> Spring| ユーザーのサインインと OBO を介した Microsoft Graph の呼び出し </p> &#8226; [Web API](https://github.com/Azure-Samples/ms-identity-java-webapi) | MSAL Java | &#8226; 認可コード <br/> &#8226; On-Behalf-Of (OBO) |
> | Node.js </p> Express | 簡易 Web アプリ シリーズ <br/> &#8226; [ユーザーのサインイン](https://github.com/Azure-Samples/ms-identity-javascript-nodejs-tutorial/blob/main/1-Authentication/1-sign-in/README.md)<br/> &#8226; [ユーザーのサインイン (B2C)](https://github.com/Azure-Samples/ms-identity-javascript-nodejs-tutorial/blob/main/1-Authentication/2-sign-in-b2c/README.md)<br/> &#8226; [Microsoft Graph の呼び出し](https://github.com/Azure-Samples/ms-identity-javascript-nodejs-tutorial/blob/main/2-Authorization/1-call-graph/README.md)<br/> &#8226; [Azure App Service にデプロイする](https://github.com/Azure-Samples/ms-identity-javascript-nodejs-tutorial/blob/main/3-Deployment/README.md)<br/> &#8226; [アクセス制御にアプリのロールを使用する](https://github.com/Azure-Samples/ms-identity-javascript-nodejs-tutorial/blob/main/4-AccessControl/1-app-roles/README.md)<br/> &#8226; [アクセス制御にセキュリティ グループを使用する](https://github.com/Azure-Samples/ms-identity-javascript-nodejs-tutorial/blob/main/4-AccessControl/2-security-groups/README.md) <br/> &#8226; [ユーザーのサインインを行う Web アプリ](https://github.com/Azure-Samples/ms-identity-node) | MSAL Node | Authorization code (承認コード) |
> | Python </p> Flask | Flask シリーズ <br/> &#8226; [ユーザーのサインイン](https://github.com/Azure-Samples/ms-identity-python-flask-tutorial) <br/> &#8226; [ユーザーのサインイン (B2C)](https://github.com/Azure-Samples/ms-identity-python-flask-tutorial) <br/>&#8226; [ユーザーのサインインと Microsoft Graph の呼び出し](https://github.com/Azure-Samples/ms-identity-python-webapp) <br/> &#8226; [Microsoft Graph の呼び出し](https://github.com/Azure-Samples/ms-identity-python-flask-tutorial) <br/> &#8226; [Azure App Service にデプロイする](https://github.com/Azure-Samples/ms-identity-python-flask-tutorial) | MSAL Python | Authorization code (承認コード) |
> | Python </p> Django | Django シリーズ <br/> &#8226; [ユーザーのサインイン](https://github.com/Azure-Samples/ms-identity-python-django-tutorial/tree/main/1-Authentication/sign-in) <br/> &#8226; [ユーザーのサインイン (B2C)](https://github.com/Azure-Samples/ms-identity-python-django-tutorial/tree/main/1-Authentication/sign-in-b2c) <br/> &#8226; [Microsoft Graph の呼び出し](https://github.com/Azure-Samples/ms-identity-python-django-tutorial/tree/main/2-Authorization-I/call-graph) <br/> &#8226; [Azure App Service にデプロイする](https://github.com/Azure-Samples/ms-identity-python-django-tutorial/tree/main/3-Deployment/deploy-to-azure-app-service)| MSAL Python | Authorization code (承認コード) |
> | Ruby | Graph のトレーニング <br/> &#8226; [ユーザーのサインインと Microsoft Graph の呼び出し](https://github.com/microsoftgraph/msgraph-training-rubyrailsapp) | OmniAuth OAuth2 | Authorization code (承認コード) |

## <a name="web-api"></a>Web API

次のサンプルは、Microsoft ID プラットフォームで Web API を保護する方法と、その Web API からダウンストリーム API を呼び出す方法を示しています。

> [!div class="mx-tdCol2BreakAll"]
> | 言語/<br/>プラットフォーム | コード サンプル <br/> GitHub 上 |Auth<br/> libraries |認証フロー |
> | ----------- | ----------- |----------- |----------- |
> | ASP.NET | [Microsoft Graph の呼び出し](https://github.com/Azure-Samples/ms-identity-aspnet-webapi-onbehalfof) | MSAL.NET | On-Behalf-Of (OBO) |
> | ASP.NET Core | [ユーザーのサインインと Microsoft Graph の呼び出し](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/tree/master/2.%20Web%20API%20now%20calls%20Microsoft%20Graph) | MSAL.NET | On-Behalf-Of (OBO) |
> | Java | [ユーザーのサインイン](https://github.com/Azure-Samples/ms-identity-java-webapi) | MSAL Java | On-Behalf-Of (OBO) |
> | Node.js | &#8226; [Node.js Web API を保護する](https://github.com/Azure-Samples/active-directory-javascript-nodejs-webapi-v2) <br/> &#8226; [Azure AD B2C による Node.js Web API の保護](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi) | MSAL Node | 認可ベアラー |

## <a name="desktop"></a>デスクトップ

次のサンプルは、ユーザーの名前で Microsoft Graph API またはお客様独自の Web API にアクセスするパブリック クライアント デスクトップ アプリケーションを示しています。 "_Workspace Application Manager (WAM) を使用するデスクトップ (コンソール)_ " を除き、これらのすべてのクライアント アプリケーションでは Microsoft Authentication Library (MSAL) が使用されます。

> [!div class="mx-tdCol2BreakAll"]
> | 言語/<br/>プラットフォーム | コード サンプル <br/> GitHub 上 | Auth<br/> libraries | 認証フロー |  
> | ------- | -------- | ------------- | -------------- |
> | .NET Core | &#8226; [Microsoft Graph の呼び出し](https://github.com/Azure-Samples/ms-identity-dotnet-desktop-tutorial/tree/master/1-Calling-MSGraph/1-1-AzureAD) <br/> &#8226; [トークン キャッシュを使用した Microsoft Graph の呼び出し](https://github.com/Azure-Samples/ms-identity-dotnet-desktop-tutorial/tree/master/2-TokenCache) <br/> &#8226; [カスタム Web UI HTML を使用した Micrsoft Graph の呼び出し](https://github.com/Azure-Samples/ms-identity-dotnet-desktop-tutorial/tree/master/3-CustomWebUI/3-1-CustomHTML) <br/> &#8226; [カスタム Web ブラウザーを使用した Microsoft Graph の呼び出し](https://github.com/Azure-Samples/ms-identity-dotnet-desktop-tutorial/tree/master/3-CustomWebUI/3-2-CustomBrowser) <br/> &#8226; [デバイス コード フローを使用したユーザーのサインイン](https://github.com/Azure-Samples/ms-identity-dotnet-desktop-tutorial/tree/master/4-DeviceCodeFlow) | MSAL.NET |&#8226; 認可コードと PKCE <br/> &#8226; デバイス コード |
> | .NET | &#8226; [デーモン コンソールを使用した Microsoft Graph の呼び出し](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/tree/master/1-Call-MSGraph) <br/> &#8226; [デーモン コンソールを使用した Web API の呼び出し](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/master/2-Call-OwnApi/README.md) | MSAL.NET | 認可コードと PKCE |
> | .NET | [統合 Windows 認証を使用した保護された API の呼び出し](https://github.com/azure-samples/active-directory-dotnet-iwa-v2) | MSAL.NET | 統合 Windows 認証 |
> | Java | [Microsoft Graph の呼び出し](https://github.com/Azure-Samples/ms-identity-java-desktop/) | MSAL Java | 統合 Windows 認証 |
> | Node.js | [ユーザーのサインイン](https://github.com/Azure-Samples/ms-identity-javascript-nodejs-desktop) | MSAL Node | 認可コードと PKCE |
> | Powershell | [ユーザー名/パスワードを使用したユーザーのサインインによる Microsoft Graph の呼び出し](https://github.com/azure-samples/active-directory-dotnetcore-console-up-v2) | MSAL.NET | リソース所有者のパスワード資格情報 |
> | Python | [ユーザーのサインイン](https://github.com/Azure-Samples/ms-identity-python-desktop) | MSAL Python | 認可コードと PKCE |
> | ユニバーサル Windows プラットフォーム (UWP) | [Microsoft Graph の呼び出し](https://github.com/Azure-Samples/active-directory-xamarin-native-v2/tree/main/2-With-broker) | MSAL.NET | Web アカウント マネージャー |
> | Windows Presentation Foundation (WPF) | [ユーザーのサインインと Microsoft Graph の呼び出し](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/tree/master/2.%20Web%20API%20now%20calls%20Microsoft%20Graph) | MSAL.NET | 認可コードと PKCE |
> | XAML | &#8226; [ユーザーのサインインと ASP.NET Core Web API の呼び出し](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/tree/master/1.%20Desktop%20app%20calls%20Web%20API) <br/> &#8226; [ユーザーのサインインと Microsoft Graph の呼び出し](https://github.com/azure-samples/active-directory-dotnet-desktop-msgraph-v2) | MSAL.NET | 認可コードと PKCE |

## <a name="mobile"></a>モバイル

次のサンプルは、ユーザーの名前で Microsoft Graph API またはお客様独自の Web API にアクセスするパブリック クライアント モバイル アプリケーションを示しています。 これらのクライアント アプリケーションでは、Microsoft Authentication Library (MSAL) が使用されます。

> [!div class="mx-tdCol2BreakAll"]
> | 言語/<br/>プラットフォーム | コード サンプル <br/> GitHub 上 |Auth<br/> libraries |認証フロー |
> | ----------- | ----------- |----------- |----------- |
> | iOS | &#8226; [Microsoft Graph ネイティブの呼び出し](https://github.com/Azure-Samples/ms-identity-mobile-apple-swift-objc) <br/> &#8226; [Azure AD nxoauth を使用した Microsoft Graph の呼び出し](https://github.com/azure-samples/active-directory-ios-native-nxoauth2-v2) | MSAL iOS | 認可コードと PKCE |
> | Java | [ユーザーのサインインと Microsoft Graph の呼び出し](https://github.com/Azure-Samples/ms-identity-android-java) | MSAL Android | 認可コードと PKCE |
> | Kotlin | [ユーザーのサインインと Microsoft Graph の呼び出し](https://github.com/Azure-Samples/ms-identity-android-kotlin) | MSAL Android | 認可コードと PKCE |
> | Xamarin | &#8226; [ユーザーのサインインと Microsoft Graph の呼び出し](https://github.com/Azure-Samples/active-directory-xamarin-native-v2/tree/main/1-Basic) <br/>&#8226; [ブローカーを使用したユーザーのサインインと Microsoft Graph の呼び出し](https://github.com/Azure-Samples/active-directory-xamarin-native-v2/tree/main/2-With-broker) | MSAL.NET | 認可コードと PKCE |

## <a name="service--daemon"></a>サービス/デーモン

以下に、アプリケーション独自の ID (ユーザーなしで) で Microsoft Graph API にアクセスするサンプル アプリケーションを示します。

> [!div class="mx-tdCol2BreakAll"]
> | 言語/<br/>プラットフォーム | コード サンプル <br/> GitHub 上 |Auth<br/> libraries |認証フロー |
> | ----------- | ----------- |----------- |----------- |
> | ASP.NET| &#8226; [Microsoft Graph の呼び出し](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/tree/master/1-Call-MSGraph) <br/> &#8226; [Web API の呼び出し](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/tree/master/2-Call-OwnApi)<br/> &#8226; [独自の Web API の呼び出し](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/tree/master/4-Call-OwnApi-Pop) <br/> &#8226; [マネージド ID と Azure Key Vault の使用](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/tree/master/3-Using-KeyVault) <br/> &#8226; [Microsoft ID プラットフォーム エンドポイントを使用したマルチテナント](https://github.com/Azure-Samples/ms-identity-aspnet-daemon-webapp) | MSAL.NET | クライアント資格情報付与|
> | Java | [Microsoft Graph の呼び出し](https://github.com/Azure-Samples/ms-identity-java-daemon)| MSAL Java| クライアント資格情報付与|
> | Node.js | [ユーザーのサインインと Web API の呼び出し](https://github.com/Azure-Samples/ms-identity-javascript-nodejs-console) | MSAL Node | クライアント資格情報付与 |
> | Python | &#8226; [シークレットを使用した Microsoft Graph の呼び出し](https://github.com/Azure-Samples/ms-identity-python-daemon/tree/master/1-Call-MsGraph-WithSecret) <br/> &#8226; [証明書を使用した Microsoft Graph の呼び出し](https://github.com/Azure-Samples/ms-identity-python-daemon/tree/master/2-Call-MsGraph-WithCertificate) | MSAL Python| クライアント資格情報付与|

## <a name="azure-functions-as-web-apis"></a>Web API としての Azure Functions

次のサンプルは、HttpTrigger を使用し、Microsoft ID プラットフォームで Web API を公開して Azure Function を保護する方法と、その Web API からダウンストリーム API を呼び出す方法を示しています。

> [!div class="mx-tdCol2BreakAll"]
> | 言語/<br/>プラットフォーム | コード サンプル <br/> GitHub 上 |Auth<br/> libraries |認証フロー |
> | ----------- | ----------- |----------- |----------- |
> | .NET | [Azure AD によりセキュリティで保護された .NET Azure 関数 Web API](https://github.com/Azure-Samples/ms-identity-dotnet-webapi-azurefunctions) | MSAL.NET | Authorization code (承認コード) |
> | Node.js | [Azure AD によりセキュリティで保護された Node.js Azure 関数 Web API](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-azurefunctions) | MSAL Node | 認可ベアラー |
> | Node.js | [ユーザーの代理での Microsoft Graph API の呼び出し](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-onbehalfof-azurefunctions) | MSAL Node| On-Behalf-Of (OBO)|
> | Python | [Azure AD によりセキュリティで保護された Python Azure 関数 Web API](https://github.com/Azure-Samples/ms-identity-python-webapi-azurefunctions) | MSAL Python | Authorization code (承認コード) |

## <a name="headless"></a>ヘッドレス

次のサンプルは、Web ブラウザーがないデバイスで実行されるパブリック クライアント アプリケーションを示しています。 このアプリは、コマンド ライン ツール、Linux または Mac 上で実行されるアプリ、あるいは IoT アプリケーションにすることもできます。 このサンプルは、別のデバイス (携帯電話など) で対話的にサインインするユーザーの名前で Microsoft Graph API にアクセスするアプリの特徴を示しています。 このクライアント アプリケーションでは、Microsoft Authentication Library (MSAL) を使用します。

> [!div class="mx-tdCol2BreakAll"]
> | 言語/<br/>プラットフォーム | コード サンプル <br/> GitHub 上 |Auth<br/> libraries |認証フロー |
> | ----------- | ----------- |----------- |----------- |
> | .NET Core | [テキスト専用デバイスからの保護された API の呼び出し](https://github.com/azure-samples/active-directory-dotnetcore-devicecodeflow-v2) | MSAL.NET | デバイス コード|
> | Java | [ユーザーのサインインと保護された API の呼び出し](https://github.com/Azure-Samples/ms-identity-java-devicecodeflow) | MSAL Java | デバイス コード |
> | Python | [Microsoft Graph の呼び出し](https://github.com/Azure-Samples/ms-identity-python-devicecodeflow) | MSAL Python | デバイス コード |

## <a name="multi-tenant-saas"></a>マルチテナント SaaS

次のサンプルでは、任意の Azure Active Directory (Azure AD) テナントからのサインインを受け付けるようにアプリケーションを構成する方法を示します。 アプリケーションを "_マルチテナント_" として構成すると、**サービスとしてのソフトウェア** (SaaS) アプリケーションを多数の組織に提供でき、ユーザーは同意を与えた後でアプリケーションにサインインできるようになります。

> [!div class="mx-tdCol2BreakAll"]
> | 言語/<br/>プラットフォーム | コード サンプル <br/> GitHub 上 |Auth<br/> libraries |認証フロー |
> | ----------- | ----------- |----------- |----------- |
> | ASP.NET Core | [ASP.NET Core MVC Web アプリケーションによる Microsoft Graph API の呼び出し](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-3-Multi-Tenant) | MSAL.NET | OpenID Connect |
> | ASP.NET Core | [ASP.NET Core MVC Web アプリケーションで ASP.NET Core Web API を呼び出す](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/4-WebApp-your-API/4-3-AnyOrg) | MSAL.NET | Authorization code (承認コード) |

## <a name="next-steps"></a>次のステップ

より多くのサンプル コードを詳しく調べる場合は、以下を参照してください。

- [ユーザーのサインインと Angular からの Microsoft Graph API の呼び出し](tutorial-v2-angular-auth-code.md)
- [Node.js および Express Web アプリでのユーザーのサインイン](tutorial-v2-nodejs-webapp-msal.md)
- [ユニバーサル Windows プラットフォームからの Microsoft Graph API の呼び出し](tutorial-v2-windows-uwp.md)
