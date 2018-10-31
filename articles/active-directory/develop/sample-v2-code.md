---
title: Azure Active Directory のコード サンプル | Microsoft Docs
description: シナリオ別に整理された Azure Active Directory (V2 エンドポイント) のサンプル コードのインデックスを提供します。
services: active-directory
documentationcenter: dev-center-name
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: a242a5ff-7300-40c2-ba83-fb6035707433
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/17/2018
ms.author: celested
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 7323f532787fa767d875e13262cce9f1ceaa79d8
ms.sourcegitcommit: 5c00e98c0d825f7005cb0f07d62052aff0bc0ca8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/24/2018
ms.locfileid: "49955523"
---
# <a name="azure-active-directory-code-samples-v2-endpoint"></a>Azure Active Directory のサンプル コード (V2 エンドポイント)

[!INCLUDE [active-directory-develop-applies-v2-msal](../../../includes/active-directory-develop-applies-v2-msal.md)]

Microsoft Azure Active Directory (Azure AD) を使用すると、次の操作を実行できます。

- Web アプリケーション Web API に認証と承認を追加する。
- 保護された Web API にアクセスするためにアクセス トークンを要求する。

この記事は、Azure AD V2 エンドポイントについて簡単に説明し、サンプルへのリンクを提供します。 これらのサンプルでは、その実行方法と、アプリケーションで使用できるコード スニペットを示します。 サンプル コードのページには、要件、インストール、設定に役立つ詳細な Readme のトピックが含まれています。 コード内のコメントは、重要なセクションを理解するのに役立ちます。

> [!NOTE]
> V1 のサンプルに関心をお持ちの場合は、[Azure AD のサンプル コード (V1 エンドポイント)](sample-v1-code.md) に関するページをご覧ください。

サンプルの種類ごとの基本的なシナリオを理解するには、「[Azure Active Directory v2.0 エンドポイントのアプリの種類](v2-app-types.md)」をご覧ください。

GitHub でサンプルに協力することもできます。 その方法については、[Microsoft Azure Active Directory のサンプルとドキュメント](https://github.com/Azure-Samples?page=3&query=active-directory)をご覧ください。

## <a name="single-page-applications-spa"></a>シングル ページ アプリケーション (SPA)

このサンプルでは、Azure AD を使用してセキュリティ保護されているシングル ページ アプリケーションの作成方法を示します。 これらのサンプルでは、[JavaScript 用 Microsoft Authentication Library](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core)、[Angular 用 Microsoft Authentication Library](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular)、[AngularJS 用 Microsoft Authentication Library](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angularjs) のいずれかの MSAL.js の種類を使用します。

 プラットフォーム |  Microsoft Graph の呼び出し
 -------- |  ---------------------
![JavaScript](media/sample-v2-code/logo_js.png) JavaScript (msal.js)  | [javascript-graphapi-web-v2](https://github.com/Azure-Samples/active-directory-javascript-graphapi-web-v2)
![Angular JS](media/sample-v2-code/logo_angular.png) JavaScript (MSAL AngularJS) | [MsalAngularjsDemoApp](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-angularjs/samples/MsalAngularjsDemoApp)
![Angular](media/sample-v2-code/logo_angular.png) JavaScript (MSAL Angular) | [MSALAngularDemoApp](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-angular/samples/MSALAngularDemoApp)

## <a name="web-applications"></a>Web アプリケーション

次のサンプルは、ユーザーがサインインする Web アプリケーションを示しています。 サンプルの中には、Microsoft Graph、またはユーザーの ID を使用してユーザー独自の Web API を呼び出すアプリケーションを示しているものもあります。

 プラットフォーム | ユーザーのサインインのみ | ユーザーのサインインと Microsoft Graph の呼び出し
 -------- | ------------------- | ---------------------------------
![ASP.NET](media/sample-v2-code/logo_NETframework.png)<p/> ASP.NET | [appmodelv2-webapp-openIDConnect-dotNet](https://GitHub.com/AzureAdQuickstarts/AppModelv2-WebApp-OpenIDConnect-DotNet) <p/> [dotnet-webapp-openidconnect-v2](https://GitHub.com/azure-samples/active-directory-dotnet-webapp-openidconnect-v2)  |              [aspnet-connect-rest-sample](https://github.com/microsoftgraph/aspnet-connect-rest-sample)
![ASP.NET](media/sample-v2-code/logo_NETcore.png)<p/>ASP.NET Core 2.0 | [aspnetcore-webapp-openidconnect-v2](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2) |              [aspnetcore-connect-sample](https://github.com/microsoftgraph/aspnetcore-connect-sample)
![Node.js](media/sample-v2-code/logo_nodejs.png)  |                   | [AppModelv2-WebApp-OpenIDConnect-nodejs](https://github.com/azureadquickstarts/appmodelv2-webapp-openidconnect-nodejs)
![Ruby](media/sample-v2-code/logo_ruby.png) |                   | [ruby-connect-rest-sample](https://github.com/microsoftgraph/ruby-connect-rest-sample)

## <a name="desktop-and-mobile-public-client-apps"></a>デスクトップおよびモバイルのパブリック クライアント アプリ

次のサンプルは、ユーザーの名前で Microsoft Graph またはお客様独自の Web API にアクセスするパブリック クライアント アプリケーション (デスクトップ/モバイル アプリケーション) を示しています。 これらすべてのクライアント アプリケーションが Microsoft Authentication Library (MSAL) を使用します。

クライアント アプリケーション | プラットフォーム | フロー/許可 | Microsoft Graph の呼び出し | ASP.NET Core 2.0 Web API の呼び出し
------------------ | -------- |  ----------| ---------- | -------------------------
デスクトップ (WPF)      | ![.NET/C#](media/sample-v2-code/logo_NET.png) | 対話型 | [dotnet-desktop-msgraph-v2](http://github.com/azure-samples/active-directory-dotnet-desktop-msgraph-v2) <p/> [dotnet-admin-restricted-scopes-v2](https://github.com/azure-samples/active-directory-dotnet-admin-restricted-scopes-v2) | [dotnet-native-aspnetcore-v2](https://GitHub.com/azure-samples/active-directory-dotnet-native-aspnetcore-v2)
デスクトップ (コンソール)   | ![.NET/C# (デスクトップ)](media/sample-v2-code/logo_NET.png) | 統合 Windows 認証 |[dotnet-iwa-v2](https://github.com/azure-samples/active-directory-dotnet-iwa-v2)
デスクトップ (コンソール)   | ![.NET/C# (デスクトップ)](media/sample-v2-code/logo_NETcore.png) | ユーザー名/パスワード |[dotnetcore-up-v2](https://github.com/azure-samples/active-directory-dotnetcore-up-v2)
モバイル (UWP)   | ![.NET/C# (UWP)](media/sample-v2-code/logo_windows.png) | 対話型 |[dotnet-native-uwp-v2](https://github.com/azure-samples/active-directory-dotnet-native-uwp-v2) |
モバイル (Android、iOS、UWP)   | ![.NET/C# (Xamarin)](media/sample-v2-code/logo_xamarin.png) | 対話型 |[xamarin-native-v2](https://Github.com/azure-samples/active-directory-xamarin-native-v2) |
モバイル (iOS)       | ![iOS / Objective C または Swift](media/sample-v2-code/logo_iOS.png) | 対話型 |[ios-swift-native-v2](https://github.com/azure-samples/active-directory-ios-swift-native-v2) <p/> [ios-native-nxoauth2-v2](https://github.com/azure-samples/active-directory-ios-native-nxoauth2-v2) |
モバイル (Android)   | ![Android/Java](media/sample-v2-code/logo_Android.png) | 対話型 |  [android-native-v2](https://github.com/azure-samples/active-directory-android-native-v2 ) |

## <a name="daemon-applications"></a>デーモン アプリケーション

以下は、アプリケーション独自の ID (ユーザーなしで) で Microsoft Graph にアクセスするサンプル アプリケーションです。

クライアント アプリケーション | プラットフォーム | フロー/許可 | Microsoft Graph の呼び出し
------------------ | -------- | ---------- | --------------------
コンソール | ![.NET Core](media/sample-v2-code/logo_NETcore.png)<p/> ASP.NET  | クライアントの資格情報 | [dotnetcore-daemon-v2](https://github.com/azure-samples/active-directory-dotnetcore-daemon-v2)
Web アプリ | ![ASP.NET](media/sample-v2-code/logo_NETframework.png)<p/> ASP.NET  | クライアントの資格情報 | [dotnet-daemon-v2](https://github.com/azure-samples/active-directory-dotnet-daemon-v2)

## <a name="headless-applications"></a>ヘッドレス アプリケーション

次のサンプルは、Web ブラウザーがないデバイスで実行されるパブリック クライアント アプリケーションを示しています。 これらのアプリは、コマンド ライン ツール、Linux/Mac 上で実行されるアプリ、または IoT アプリケーションにすることもできます。 このサンプルは、(携帯電話のインスタンスを表す) 別のデバイスに対話的にサインインするユーザーの名前で Microsoft Graph にアクセスするアプリの特徴を示しています。 これらすべてのクライアント アプリケーションが Microsoft Authentication Library (MSAL) を使用します。

クライアント アプリケーション | プラットフォーム | フロー/許可 | Microsoft Graph の呼び出し
------------------ | -------- |  ----------| ----------
デスクトップ (コンソール)   | ![.NET/C# (デスクトップ)](media/sample-v2-code/logo_NETcore.png) | デバイス コード フロー |[dotnetcore-devicecodeflow-v2](https://github.com/azure-samples/active-directory-dotnetcore-devicecodeflow-v2)

## <a name="web-apis"></a>Web API

次のサンプルは、Azure AD V2 エンドポイントで Web API を保護する方法を示しています。 この API は、WPF アプリケーションによって実行されます (ただし、実際には任意のアプリケーションから呼び出すことができます)。

プラットフォーム | サンプル
 -------- | -------------------
![.NET/C#](media/sample-v2-code/logo_NET.png) | [dotnet-native-aspnetcore-v2](https://GitHub.com/azure-samples/active-directory-dotnet-native-aspnetcore-v2)

## <a name="other-microsoft-graph-samples"></a>Microsoft Graph のその他のサンプル

Azure AD での認証を含む、Microsoft Graph API のさまざまな使用パターンを示す[サンプル](https://github.com/microsoftgraph/msgraph-community-samples/tree/master/samples#aspnet)とチュートリアルについては、[Microsoft Graph コミュニティのサンプルとチュートリアル](https://github.com/microsoftgraph/msgraph-community-samples)をご覧ください。

## <a name="see-also"></a>関連項目

[Azure Active Directory 開発者ガイド](v1-overview.md)

[Active Directory Graph API の概念とリファレンス](https://msdn.microsoft.com/library/azure/hh974476.aspx)

[Azure AD Graph API Helper Library](https://www.nuget.org/packages/Microsoft.Azure.ActiveDirectory.GraphClient)
