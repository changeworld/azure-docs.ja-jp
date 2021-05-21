---
title: Microsoft ID プラットフォームのコード サンプル
description: シナリオ別に整理された、利用可能な Microsoft ID プラットフォーム コード サンプルのインデックスを提供します。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: sample
ms.workload: identity
ms.date: 11/04/2020
ms.author: marsma
ms.reviewer: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: bbc068db59bb0a5cd4ef334d3e8ce0a63f74079d
ms.sourcegitcommit: 1b19b8d303b3abe4d4d08bfde0fee441159771e1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/11/2021
ms.locfileid: "109752663"
---
# <a name="microsoft-identity-platform-code-samples-v20-endpoint"></a>Microsoft ID プラットフォームのコード サンプル (v2.0 エンドポイント)

Microsoft ID プラットフォームを使用して、次の操作を行うことができます。

- Web アプリケーション Web API に認証と承認を追加する。
- 保護された Web API にアクセスするためにアクセス トークンを要求する。

この記事では、Microsoft ID プラットフォームのサンプルについて簡単に説明し、サンプルへのリンクを提供します。 これらのサンプルでは、その実行方法を示し、アプリケーションで使用できるコード スニペットを提供します。 サンプル コードのページには、要件、インストール、設定に役立つ詳細な Readme のトピックが含まれています。 コード内のコメントは、重要なセクションを理解するのに役立ちます。

サンプルの種類ごとの基本的なシナリオについては、[Microsoft ID プラットフォームのアプリの種類](v2-app-types.md)に関する記事をご覧ください。

GitHub でサンプルに協力することもできます。 その方法については、[Microsoft Azure Active Directory のサンプルとドキュメント](https://github.com/Azure-Samples?page=3&query=active-directory)をご覧ください。

## <a name="single-page-applications"></a>シングルページ アプリケーション

これらのサンプルでは、Microsoft ID プラットフォームを使用してセキュリティ保護されているシングルページ アプリケーションの作成方法を示します。 これらのサンプルでは、MSAL.js のいずれかの種類を使用します。

> [!div class="mx-tdCol2BreakAll"]
> | 言語/<br/>プラットフォーム | コード サンプル | 説明 | 認証ライブラリ | 認証フロー |  
> | ------- | -------- | --------------------------- | ------------- | -------------- |  
> |Angular|[GitHub リポジトリ](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa)| &#8226; AAD を使用してユーザーをサインインする <br/>&#8226; Microsoft Graph を呼び出す | MSAL Angular | 認証コード フロー (PKCE を使用) |
> | Angular | [GitHub リポジトリ](https://github.com/Azure-Samples/ms-identity-javascript-angular-tutorial) | &#8226; [ユーザーをサインインする](https://github.com/Azure-Samples/ms-identity-javascript-angular-tutorial/blob/main/1-Authentication/1-sign-in/README.md)<br/>&#8226; [ユーザーをサインインする (B2C)](https://github.com/Azure-Samples/ms-identity-javascript-angular-tutorial/blob/main/1-Authentication/2-sign-in-b2c/README.md) <br/> &#8226; [Microsoft Graph を呼び出す](https://github.com/Azure-Samples/ms-identity-javascript-angular-tutorial/blob/main/2-Authorization-I/1-call-graph/README.md)<br/>&#8226; [.NET Core Web API を呼び出す](https://github.com/Azure-Samples/ms-identity-javascript-angular-tutorial/tree/main/3-Authorization-II/1-call-api)<br/>&#8226; [.NET Core Web API を呼び出す (B2C)](https://github.com/Azure-Samples/ms-identity-javascript-angular-tutorial/tree/main/3-Authorization-II/2-call-api-b2c)<br/>&#8226; [OBO を使用して Microsoft Graph を呼び出す](https://github.com/Azure-Samples/ms-identity-javascript-tutorial/blob/main/4-AdvancedGrants/1-call-api-graph/README.md)<br/>&#8226; [PoP を使用して .NET Core Web API を呼び出す](https://github.com/Azure-Samples/ms-identity-javascript-tutorial/blob/main/4-AdvancedGrants/2-call-api-api-ca/README.md)<br/>&#8226; [アクセス制御にアプリのロールを使用する](https://github.com/Azure-Samples/ms-identity-javascript-angular-tutorial/blob/main/5-AccessControl/1-call-api-roles/README.md)<br/>&#8226; [アクセス制御にセキュリティ グループを使用する](https://github.com/Azure-Samples/ms-identity-javascript-angular-tutorial/blob/main/5-AccessControl/2-call-api-groups/README.md)<br/>&#8226; [Azure Storage および App Service にデプロイする](https://github.com/Azure-Samples/ms-identity-javascript-angular-tutorial/blob/main/4-Deployment/README.md)| MSAL Angular | &#8226; 認証コード フロー (PKCE を使用)<br/>&#8226; On-Behalf-Of (OBO) フロー<br/>&#8226; 所有証明 (PoP)|
> | Blazor WebAssembly | [GitHub リポジトリ](https://github.com/Azure-Samples/ms-identity-blazor-wasm) | &#8226; ユーザーをサインインする<br/>&#8226; Microsoft Graph を呼び出す | MSAL.js | 認証コード フロー (PKCE を使用) |
> | JavaScript | [GitHub リポジトリ](https://github.com/Azure-Samples/ms-identity-javascript-v2) | &#8226; ユーザーをサインインする<br/>&#8226; Microsoft Graph を呼び出す | MSAL.js | 認証コード フロー (PKCE を使用) |
> | JavaScript | [GitHub リポジトリ](https://github.com/Azure-Samples/ms-identity-b2c-javascript-spa) | &#8226; ユーザーをサインインする (B2C)<br/>&#8226; Node.js Web API を呼び出す |  MSAL.js | 認証コード フロー (PKCE を使用) |
> | JavaScript | [GitHub リポジトリ](https://github.com/Azure-Samples/ms-identity-javascript-tutorial) | &#8226; [ユーザーをサインインする](https://github.com/Azure-Samples/ms-identity-javascript-tutorial/blob/main/1-Authentication/1-sign-in/README.md)<br/>&#8226; [ユーザーをサインインする (B2C)](https://github.com/Azure-Samples/ms-identity-javascript-tutorial/blob/main/1-Authentication/2-sign-in-b2c/README.md) <br/> &#8226; [Microsoft Graph を呼び出す](https://github.com/Azure-Samples/ms-identity-javascript-tutorial/blob/main/2-Authorization-I/1-call-graph/README.md)<br/>&#8226; [Node.js Web API を呼び出す](https://github.com/Azure-Samples/ms-identity-javascript-tutorial/blob/main/3-Authorization-II/1-call-api/README.md)<br/>&#8226; [Node.js Web API を呼び出す (B2C)](https://github.com/Azure-Samples/ms-identity-javascript-tutorial/blob/main/3-Authorization-II/2-call-api-b2c/README.md)<br/>&#8226; [OBO を使用して Microsoft Graph を呼び出す](https://github.com/Azure-Samples/ms-identity-javascript-tutorial/blob/main/4-AdvancedGrants/1-call-api-graph/README.md)<br/>&#8226; [OBO と CA を使用して Node.js Web API を呼び出す](https://github.com/Azure-Samples/ms-identity-javascript-tutorial/blob/main/4-AdvancedGrants/2-call-api-api-ca/README.md)<br/>&#8226; [Azure Storage および App Service にデプロイする](https://github.com/Azure-Samples/ms-identity-javascript-tutorial/blob/main/5-Deployment/README.md)| MSAL.js | &#8226; 認証コード フロー (PKCE を使用)<br/>&#8226; On-Behalf-Of (OBO) フロー<br/>&#8226; 条件付きアクセス (CA) |
> | React | [GitHub リポジトリ](https://github.com/Azure-Samples/ms-identity-javascript-react-spa) | &#8226; ユーザーをサインインする<br/>&#8226; Microsoft Graph を呼び出す | MSAL React | 認証コード フロー (PKCE を使用) |
> | React | [GitHub リポジトリ](https://github.com/Azure-Samples/ms-identity-javascript-react-tutorial) | &#8226; [ユーザーをサインインする](https://github.com/Azure-Samples/ms-identity-javascript-react-tutorial/blob/main/1-Authentication/1-sign-in/README.md)<br/>&#8226; [ユーザーをサインインする (B2C)](https://github.com/Azure-Samples/ms-identity-javascript-react-tutorial/blob/main/1-Authentication/2-sign-in-b2c/README.md) <br/> &#8226; [Microsoft Graph を呼び出す](https://github.com/Azure-Samples/ms-identity-javascript-react-tutorial/blob/main/2-Authorization-I/1-call-graph/README.md)<br/>&#8226; [Node.js Web API を呼び出す](https://github.com/Azure-Samples/ms-identity-javascript-react-tutorial/tree/main/3-Authorization-II/1-call-api)<br/>&#8226; [Node.js Web API を呼び出す (B2C)](https://github.com/Azure-Samples/ms-identity-javascript-react-tutorial/tree/main/3-Authorization-II/2-call-api-b2c)<br/>&#8226; [OBO を使用して Microsoft Graph を呼び出す](https://github.com/Azure-Samples/ms-identity-javascript-react-tutorial/blob/main/6-AdvancedScenarios/1-call-api-obo/README.md)<br/>&#8226; [アクセス制御にアプリのロールを使用する](https://github.com/Azure-Samples/ms-identity-javascript-react-tutorial/blob/main/5-AccessControl/1-call-api-roles/README.md)<br/>&#8226; [アクセス制御にセキュリティ グループを使用する](https://github.com/Azure-Samples/ms-identity-javascript-react-tutorial/blob/main/5-AccessControl/2-call-api-groups/README.md)<br/>&#8226; [Azure Storage および App Service にデプロイする](https://github.com/Azure-Samples/ms-identity-javascript-react-tutorial/blob/main/4-Deployment/1-deploy-storage/README.md)<br/>&#8226; [Azure Static Web Apps にデプロイする](https://github.com/Azure-Samples/ms-identity-javascript-react-tutorial/blob/main/4-Deployment/2-deploy-static/README.md)| MSAL React | &#8226; 認証コード フロー (PKCE を使用)<br/>&#8226; On-Behalf-Of (OBO) フロー<br/>&#8226; 条件付きアクセス (CA) |

## <a name="web-applications"></a>Web アプリケーション

次のサンプルは、ユーザーがサインインする Web アプリケーションを示しています。 サンプルの中には、Microsoft Graph、またはユーザーの ID を使用してユーザー独自の Web API を呼び出すアプリケーションを示しているものもあります。

| プラットフォーム | ユーザーのサインインのみ | ユーザーのサインインと Microsoft Graph の呼び出し |
| -------- | ------------------- | --------------------------------- |
| ![ASP.NET Core のロゴを示す画像](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET Core | [ASP.NET Core Web アプリのユーザーのサインインに関するチュートリアル](https://aka.ms/aspnetcore-webapp-sign-in) | [ASP.NET Core Web アプリが Microsoft Graph を呼び出す](https://aka.ms/aspnetcore-webapp-call-msgraph)フェーズと同じサンプル</p>高度なサンプル「[バックグラウンド アプリ、API、サービスから、ログインしているユーザーのトークン キャッシュへのアクセス](https://github.com/Azure-Samples/ms-identity-dotnet-advanced-token-cache)」 |
| ![この画像は ASP.NET Framework ロゴを示しています](media/sample-v2-code/logo_NETframework.png)</p>ASP.NET Core | [開発者向けの、AD FS から Azure AD へのアプリケーション移行プレイブック](https://github.com/Azure-Samples/ms-identity-dotnet-adfs-to-aad)。Active Directory フェデレーション サービス (AD FS) と統合されているアプリケーションを Azure Active Directory (Azure AD) に安全に移行する方法を学習 | |
| ![この画像は ASP.NET Framework ロゴを示しています](media/sample-v2-code/logo_NETframework.png)</p> ASP.NET | [ASP.NET クイック スタート](https://github.com/AzureAdQuickstarts/AppModelv2-WebApp-OpenIDConnect-DotNet) </p> [dotnet-webapp-openidconnect-v2](https://github.com/azure-samples/active-directory-dotnet-webapp-openidconnect-v2)  |  [dotnet-admin-restricted-scopes-v2](https://github.com/azure-samples/active-directory-dotnet-admin-restricted-scopes-v2) </p> [msgraph-training-aspnetmvcapp](https://github.com/microsoftgraph/msgraph-training-aspnetmvcapp) |
| ![Java のロゴを示す画像](media/sample-v2-code/logo_java.png)  |[Java サーブレットのチュートリアル - 第 1.1 章](https://github.com/Azure-Samples/ms-identity-java-servlet-webapp-authentication/blob/main/README.md) AAD でのサインイン| |
| ![Java のロゴを示す画像](media/sample-v2-code/logo_java.png)  |[Java サーブレットのチュートリアル - 第 1.2 章](https://github.com/Azure-Samples/ms-identity-java-servlet-webapp-authentication/blob/main/README.md) B2C でのサインイン |
| ![Java のロゴを示す画像](media/sample-v2-code/logo_java.png)  | | [Java サーブレットのチュートリアル - 第 2.1 章](https://github.com/Azure-Samples/ms-identity-java-servlet-webapp-authentication/blob/main/README.md) AAD でのサインインと Graph の呼び出し|
| ![Java のロゴを示す画像](media/sample-v2-code/logo_java.png)  |[Java サーブレットのチュートリアル - 第 3.1 章](https://github.com/Azure-Samples/ms-identity-java-servlet-webapp-authentication/blob/main/README.md) AAD でのサインインと Roles 要求でのアクセス制御| |
| ![Java のロゴを示す画像](media/sample-v2-code/logo_java.png)  | | [Java サーブレットのチュートリアル - 第 3.2 章](https://github.com/Azure-Samples/ms-identity-java-servlet-webapp-authentication/blob/main/README.md) AAD でのサインインと Groups 要求でのアクセス制御|
| ![Java のロゴを示す画像](media/sample-v2-code/logo_java.png) | |[Java サーブレットのチュートリアル - 第 4.1 章](https://github.com/Azure-Samples/ms-identity-java-servlet-webapp-authentication/blob/main/README.md) Azure App Service へのデプロイ|
| ![Java のロゴを示す画像](media/sample-v2-code/logo_java.png)  | | [ms-identity-java-webapp](https://github.com/Azure-Samples/ms-identity-java-webapp) |
| ![Java のロゴを示す画像](media/sample-v2-code/logo_java.png)  | [ms-identity-b2c-java-servlet-webapp-authentication](https://github.com/Azure-Samples/ms-identity-b2c-java-servlet-webapp-authentication)|  |
| ![Node.js のロゴを示す画像](media/sample-v2-code/logo_nodejs.png)</p>Node.js (MSAL Node) | [Express Web アプリのユーザーのサインインに関するチュートリアル](https://github.com/Azure-Samples/ms-identity-node) | |
| ![Python のロゴを示す画像](media/sample-v2-code/logo_python.png)  | [Python Flask のチュートリアル - 第 1.1 章](https://github.com/Azure-Samples/ms-identity-python-flask-tutorial/blob/main/README.md) AAD でのサインイン  |  |
| ![Python のロゴを示す画像](media/sample-v2-code/logo_python.png)  | [Python Flask のチュートリアル - 第 1.2 章](https://github.com/Azure-Samples/ms-identity-python-flask-tutorial/blob/main/README.md) B2C でのサインイン                    |  |
| ![Python のロゴを示す画像](media/sample-v2-code/logo_python.png)  | | [Python Flask のチュートリアル - 第 2.1 章](https://github.com/Azure-Samples/ms-identity-python-flask-tutorial/blob/main/README.md) AAD でのサインインと Graph の呼び出し |
| ![Python のロゴを示す画像](media/sample-v2-code/logo_python.png)  | |[Python Flask のチュートリアル - 第 3.1 章](https://github.com/Azure-Samples/ms-identity-python-flask-tutorial/blob/main/README.md) Azure App Service へのデプロイ  |
| ![Python のロゴを示す画像](media/sample-v2-code/logo_python.png)  | [Python Django のチュートリアル - 第 1.1 章](https://github.com/Azure-Samples/ms-identity-python-django-tutorial/blob/main/README.md) AAD でのサインイン  | |
| ![Python のロゴを示す画像](media/sample-v2-code/logo_python.png)  | [Python Django のチュートリアル - 第 1.2 章](https://github.com/Azure-Samples/ms-identity-python-django-tutorial/blob/main/README.md) B2C でのサインイン                    |  |
| ![Python のロゴを示す画像](media/sample-v2-code/logo_python.png)  | | [Python Django のチュートリアル - 第 2.1 章](https://github.com/Azure-Samples/ms-identity-python-django-tutorial/blob/main/README.md) AAD でのサインインと Graph の呼び出し|
| ![Python のロゴを示す画像](media/sample-v2-code/logo_python.png)  | | [Python Django のチュートリアル - 第 3.1 章](https://github.com/Azure-Samples/ms-identity-python-django-tutorial/blob/main/README.md) Azure App Service へのデプロイ                    |
| ![Python のロゴを示す画像](media/sample-v2-code/logo_python.png)  | | [Python Flask Web アプリ](https://github.com/Azure-Samples/ms-identity-python-webapp) |
| ![Ruby のロゴを示す画像](media/sample-v2-code/logo_ruby.png) |                   | [msgraph-training-rubyrailsapp](https://github.com/microsoftgraph/msgraph-training-rubyrailsapp) |
| ![この画像は、Blazor ロゴを示しています](media/sample-v2-code/logo-blazor.png)</p>Blazor サーバー | [Blazor Server アプリのユーザーのサインインに関するチュートリアル](https://github.com/Azure-Samples/ms-identity-blazor-server/tree/main/WebApp-OIDC) | [Blazor Server app calls Microsoft Graph (Blazor Server アプリが Microsoft Graph を呼び出す)](https://github.com/Azure-Samples/ms-identity-blazor-server/tree/main/WebApp-graph-user/Call-MSGraph)</p>チャプター形式のチュートリアル:[Blazor Server app to sign-in users and call APIs with Azure Active Directory (Azure Active Directory を使用してユーザーをサインインさせ API を呼び出す Blazor Server アプリ)](https://github.com/Azure-Samples/ms-identity-blazor-server) |

## <a name="desktop-and-mobile-public-client-apps"></a>デスクトップおよびモバイルのパブリック クライアント アプリ

次のサンプルは、ユーザーの名前で Microsoft Graph API またはお客様独自の Web API にアクセスするパブリック クライアント アプリケーション (デスクトップまたはモバイル アプリケーション) を示しています。 "*WAM を使用するデスクトップ (コンソール)* " を除き、これらのすべてのクライアント アプリケーションで Microsoft Authentication Library (MSAL) を使用します。

| クライアント アプリケーション | プラットフォーム | フロー/許可 | Microsoft Graph の呼び出し | ASP.NET Core Web API の呼び出し |
| ------------------ | -------- |  ----------| ---------- | ------------------------- |
| デスクトップ チュートリアル (.NET Core) - 必要に応じて以下を使用</p>- クロス プラットフォーム トークン キャッシュ</p>- カスタム Web UI | ![.NET/C# のロゴを示す画像](media/sample-v2-code/logo_NETcore.png) | [承認コード](msal-authentication-flows.md#authorization-code)| [ms-identity-dotnet-desktop-tutorial](https://github.com/azure-samples/ms-identity-dotnet-desktop-tutorial) | |
| デスクトップ (WPF)      | ![.NET デスクトップ/C# のロゴを示す画像](media/sample-v2-code/logo_NET.png) | [承認コード](msal-authentication-flows.md#authorization-code)| [dotnet-desktop-msgraph-v2](https://github.com/azure-samples/active-directory-dotnet-desktop-msgraph-v2) | [dotnet-native-aspnetcore-v2](https://aka.ms/msidentity-aspnetcore-webapi) |
| デスクトップ (コンソール)   | ![.NET/C# (デスクトップ) のロゴを示す画像](media/sample-v2-code/logo_NET.png) | [統合 Windows 認証](msal-authentication-flows.md#integrated-windows-authentication) | [dotnet-iwa-v2](https://github.com/azure-samples/active-directory-dotnet-iwa-v2) |  |
| デスクトップ (コンソール) | ![.NET/C# (デスクトップ) のロゴを示す画像](media/sample-v2-code/logo_NET.png) |  [承認コード](msal-authentication-flows.md#authorization-code) | [active-directory-dotnetcore-daemon-v2](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/tree/master/1-Call-MSGraph) |[active-directory-dotnetcore-daemon-v2](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/master/2-Call-OwnApi/README.md)  |
| デスクトップ (コンソール) <br> シークレットではなく証明書を使用する | ![.NET/C# (デスクトップ) のロゴを示す画像](media/sample-v2-code/logo_NET.png) |  [承認コード](msal-authentication-flows.md#authorization-code) | [active-directory-dotnetcore-daemon-v2](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/tree/master/1-Call-MSGraph#variation-daemon-application-using-client-credentials-with-certificates) |[active-directory-dotnetcore-daemon-v2](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/tree/master/2-Call-OwnApi#variation-daemon-application-using-client-credentials-with-certificates)  |
| デスクトップ (コンソール)   | ![Java のロゴを示す画像](media/sample-v2-code/logo_java.png) | [統合 Windows 認証](msal-authentication-flows.md#integrated-windows-authentication) |[ms-identity-java-desktop](https://github.com/Azure-Samples/ms-identity-java-desktop/) |  |
| デスクトップ (コンソール)   | ![.NET/C# (デスクトップ) のロゴ](media/sample-v2-code/logo_NETcore.png) | [ユーザー名/パスワード](msal-authentication-flows.md#usernamepassword) |[dotnetcore-up-v2](https://github.com/azure-samples/active-directory-dotnetcore-console-up-v2) |  |
| WAM を使用するデスクトップ (コンソール)  | ![.NET/C# (デスクトップ) のロゴ](media/sample-v2-code/logo_NETcore.png) | [Web アカウント マネージャー](/windows/uwp/security/web-account-manager) (WAM) による対話型 |[dotnet-native-uwp-wam](https://github.com/azure-samples/active-directory-dotnet-native-uwp-wam) |  |
| デスクトップ (コンソール)   | ![Java のロゴを示す画像](media/sample-v2-code/logo_java.png) | [ユーザー名/パスワード](msal-authentication-flows.md#usernamepassword) |[ms-identity-java-desktop](https://github.com/Azure-Samples/ms-identity-java-desktop/) |  |
| デスクトップ (コンソール)   | ![Python のロゴを示す画像](media/sample-v2-code/logo_python.png) | [ユーザー名/パスワード](msal-authentication-flows.md#usernamepassword) |[ms-identity-python-desktop](https://github.com/Azure-Samples/ms-identity-python-desktop) |  |
| デスクトップ (Electron)   | ![Node.js のロゴを示す画像](media/sample-v2-code/logo_nodejs.png)</p>Node.js (MSAL Node) | [承認コード (PKCE)](msal-authentication-flows.md#authorization-code) |[ms-identity-javascript-nodejs-desktop](https://github.com/Azure-Samples/ms-identity-javascript-nodejs-desktop) |  |
| モバイル (Android、iOS、UWP)   | ![.NET/C# (Xamarin) のロゴを示す画像](media/sample-v2-code/logo_xamarin.png) | [承認コード](msal-authentication-flows.md#authorization-code) |[xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) |  |
| モバイル (iOS)       | ![iOS/Objective C または Swift を示す画像](media/sample-v2-code/logo_iOS.png) | [承認コード](msal-authentication-flows.md#authorization-code) |[ios-swift-objc-native-v2](https://github.com/azure-samples/active-directory-ios-swift-native-v2) </p> [ios-native-nxoauth2-v2](https://github.com/azure-samples/active-directory-ios-native-nxoauth2-v2) |  |
| デスクトップ (macOS)       | macOS | [承認コード](msal-authentication-flows.md#authorization-code) |[macOS-swift-objc-native-v2](https://github.com/Azure-Samples/ms-identity-macOS-swift-objc) |  |
| Mobile (Android-Java)   | ![Android のロゴを示す画像](media/sample-v2-code/logo_Android.png) | [承認コード](msal-authentication-flows.md#authorization-code) |  [android-Java](https://github.com/Azure-Samples/ms-identity-android-java) |  |
| Mobile (Android-Kotlin)   | ![Android のロゴを示す画像](media/sample-v2-code/logo_Android.png) | [承認コード](msal-authentication-flows.md#authorization-code) |  [android-Kotlin](https://github.com/Azure-Samples/ms-identity-android-kotlin) |  |

## <a name="daemon-applications"></a>デーモン アプリケーション

以下に、アプリケーション独自の ID (ユーザーなしで) で Microsoft Graph API にアクセスするサンプル アプリケーションを示します。

| クライアント アプリケーション | プラットフォーム | フロー/許可 | Microsoft Graph の呼び出し |
| ------------------ | -------- | ---------- | -------------------- |
| コンソール | ![.NET Core のロゴを示す画像](media/sample-v2-code/logo_NETcore.png)</p> ASP.NET  | [クライアントの資格情報](msal-authentication-flows.md#client-credentials) | [dotnetcore-daemon-v2](https://github.com/azure-samples/active-directory-dotnetcore-daemon-v2) |
| Web アプリ | ![ASP.NET のロゴを示すスクリーンショット。](media/sample-v2-code/logo_NETframework.png)</p> ASP.NET  | [クライアントの資格情報](msal-authentication-flows.md#client-credentials) | [dotnet-daemon-v2](https://github.com/azure-samples/active-directory-dotnet-daemon-v2) |
| コンソール | ![Java のロゴを示す画像](media/sample-v2-code/logo_java.png) | [クライアントの資格情報](msal-authentication-flows.md#client-credentials) | [ms-identity-java-daemon](https://github.com/Azure-Samples/ms-identity-java-daemon) |
| コンソール | ![Node.js のロゴを示す画像](media/sample-v2-code/logo_nodejs.png)</p>Node.js (MSAL Node)| [クライアントの資格情報](msal-authentication-flows.md#client-credentials) | [ms-identity-javascript-nodejs-console](https://github.com/Azure-Samples/ms-identity-javascript-nodejs-console) |
| コンソール | ![Python のロゴを示す画像](media/sample-v2-code/logo_python.png) | [クライアントの資格情報](msal-authentication-flows.md#client-credentials) | [ms-identity-python-daemon](https://github.com/Azure-Samples/ms-identity-python-daemon) |

## <a name="headless-applications"></a>ヘッドレス アプリケーション

次のサンプルは、Web ブラウザーがないデバイスで実行されるパブリック クライアント アプリケーションを示しています。 このアプリは、コマンド ライン ツール、Linux または Mac 上で実行されるアプリ、あるいは IoT アプリケーションにすることもできます。 このサンプルは、別のデバイス (携帯電話など) で対話的にサインインするユーザーの名前で Microsoft Graph API にアクセスするアプリの特徴を示しています。 このクライアント アプリケーションでは、Microsoft Authentication Library (MSAL) を使用します。

| クライアント アプリケーション | プラットフォーム | フロー/許可 | Microsoft Graph の呼び出し |
| ------------------ | -------- |  ----------| ---------- |
| デスクトップ (コンソール)   | ![.NET/C# (デスクトップ) のロゴを示す画像](media/sample-v2-code/logo_NETcore.png) | [デバイス コード フロー](msal-authentication-flows.md#device-code) |[dotnetcore-devicecodeflow-v2](https://github.com/azure-samples/active-directory-dotnetcore-devicecodeflow-v2) |
| デスクトップ (コンソール)   | ![Java のロゴを示す画像](media/sample-v2-code/logo_java.png) | [デバイス コード フロー](msal-authentication-flows.md#device-code) |[ms-identity-java-devicecodeflow](https://github.com/Azure-Samples/ms-identity-java-devicecodeflow) |
| デスクトップ (コンソール)   | ![Python のロゴを示す画像](media/sample-v2-code/logo_python.png) | [デバイス コード フロー](msal-authentication-flows.md#device-code) |[ms-identity-python-devicecodeflow](https://github.com/Azure-Samples/ms-identity-python-devicecodeflow) |

## <a name="multi-tenant-saas-applications"></a>マルチテナント SaaS アプリケーション

次のサンプルでは、任意の Azure Active Directory (Azure AD) テナントからのサインインを受け付けるようにアプリケーションを構成する方法を示します。 アプリケーションを "*マルチテナント*" として構成すると、**サービスとしてのソフトウェア** (SaaS) アプリケーションを多数の組織に提供でき、ユーザーは同意を与えた後でアプリケーションにサインインできるようになります。

| プラットフォーム | 説明 | Link |
| -------- | --------------------- | -------- |
| ![Angular のロゴを示す画像](media/sample-v2-code/logo_angular.png) [Angular (MSAL Angular 2.0)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular) | マルチテナント SPA からマルチテナント カスタム Web API を呼び出します。 |[ms-identity-javascript-angular-spa-aspnet-webapi-multitenant](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-aspnet-webapi-multitenant/tree/master/Chapter2) |
| ![ASP.NET Core のロゴを示す画像](media/sample-v2-code/logo_NETcore.png) [.NET Core (MSAL.NET)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) | ASP.NET Core MVC Web アプリケーションで Graph API を呼び出す |[active-directory-aspnetcore-webapp-openidconnect-v2](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-3-Multi-Tenant) |
| ![ASP.NET Core のロゴを示す画像](media/sample-v2-code/logo_NETcore.png) [.NET Core (MSAL.NET)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) | ASP.NET Core MVC Web アプリケーションで ASP.NET Core Web API を呼び出す |[active-directory-aspnetcore-webapp-openidconnect-v2](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/4-WebApp-your-API/4-3-AnyOrg) |

## <a name="web-apis"></a>Web API

次のサンプルは、Microsoft ID プラットフォームで Web API を保護する方法と、その Web API からダウンストリーム API を呼び出す方法を示しています。

| プラットフォーム | サンプル |
| -------- | ------------------- |
| ![ASP.NET Core のロゴを示す画像](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET Core | [dotnet-native-aspnetcore-v2](https://aka.ms/msidentity-aspnetcore-webapi-calls-msgraph) の ASP.NET Core Web API (サービス)  |
| ![ASP.NET のロゴを示す画像](media/sample-v2-code/logo_NET.png)</p>ASP.NET MVC | [ms-identity-aspnet-webapi-onbehalfof](https://github.com/Azure-Samples/ms-identity-aspnet-webapi-onbehalfof) の Web API (サービス) |
| ![Java のロゴを示す画像](media/sample-v2-code/logo_java.png) | [ms-identity-java-webapi](https://github.com/Azure-Samples/ms-identity-java-webapi) の Web API (サービス) |
| ![Node.js のロゴを示す画像](media/sample-v2-code/logo_nodejs.png)</p>Node.js (Passport.js)| [active-directory-javascript-nodejs-webapi-v2](https://github.com/Azure-Samples/active-directory-javascript-nodejs-webapi-v2) の Web API (サービス) |
| ![Node.js のロゴを示す画像](media/sample-v2-code/logo_nodejs.png)</p>Node.js (Passport.js)| [active-directory-b2c-javascript-nodejs-webapi](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi) の B2C Web API (サービス) |

## <a name="azure-functions-as-web-apis"></a>Web API としての Azure Functions

次のサンプルは、HttpTrigger を使用し、Microsoft ID プラットフォームで Web API を公開して Azure Function を保護する方法と、その Web API からダウンストリーム API を呼び出す方法を示しています。

| プラットフォーム | サンプル |
| -------- | ------------------- |
| ![ASP.NET Core のロゴを示す画像](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET Core | [dotnet-native-aspnetcore-v2](https://github.com/Azure-Samples/ms-identity-dotnet-webapi-azurefunctions) の ASP.NET Core Web API (サービス) Azure Function  |
| ![Python のロゴを示す画像](media/sample-v2-code/logo_python.png)</p>Python | [Python](https://github.com/Azure-Samples/ms-identity-python-webapi-azurefunctions) の Web API (サービス) |
| ![Node.js のロゴを示す画像](media/sample-v2-code/logo_nodejs.png)</p>Node.js (Passport.js)| [Node.js および passport-azure-ad](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-azurefunctions) の Web API (サービス) |
| ![Node.js のロゴを示す画像](media/sample-v2-code/logo_nodejs.png)</p>Node.js (Passport.js)| [Node.js and passport-azure-ad using on behalf of](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-onbehalfof-azurefunctions) の Web API (サービス) |

## <a name="other-microsoft-graph-samples"></a>Microsoft Graph のその他のサンプル

Azure AD での認証を含む、Microsoft Graph API のさまざまな使用パターンを示す[サンプル](https://github.com/microsoftgraph/msgraph-community-samples/tree/master/samples#aspnet)とチュートリアルについては、[Microsoft Graph コミュニティのサンプルとチュートリアル](https://github.com/microsoftgraph/msgraph-community-samples)をご覧ください。

## <a name="see-also"></a>関連項目

[Microsoft Graph API の概念とリファレンス](/graph/use-the-api?context=graph%2fapi%2fbeta&view=graph-rest-beta&preserve-view=true)
