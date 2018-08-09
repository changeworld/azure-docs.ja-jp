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
ms.date: 04/26/2018
ms.author: celested
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 0be97bfbbaafd6045fd36be57d85e917f0325779
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/07/2018
ms.locfileid: "39600654"
---
# <a name="azure-active-directory-code-samples-v2-endpoint"></a>Azure Active Directory のサンプル コード (V2 エンドポイント)

Microsoft Azure Active Directory (Azure AD) を使用すると、次の操作を実行できます。

- Web アプリケーション Web API に認証と承認を追加する。
- 保護された Web API にアクセスするためにアクセス トークンを要求する。

この記事は、Azure AD V2 エンドポイントについて簡単に説明し、サンプルへのリンクを提供します。 これらのサンプルでは、その実行方法と、アプリケーションで使用できるコード スニペットを示します。 サンプル コードのページには、要件、インストール、設定に役立つ詳細な Readme のトピックが含まれています。 コード内のコメントは、重要なセクションを理解するのに役立ちます。

> [!NOTE]
> V1 のサンプルに関心をお持ちの場合は、[Azure AD のサンプル コード (V1 エンドポイント)](sample-v1-code.md) に関するページをご覧ください。

サンプルの種類ごとの基本的なシナリオを理解するには、「[Azure Active Directory v2.0 エンドポイントのアプリの種類](v2-app-types.md)」をご覧ください。

GitHub でサンプルに協力することもできます。 その方法については、[Microsoft Azure Active Directory のサンプルとドキュメント](https://github.com/Azure-Samples?page=3&query=active-directory)をご覧ください。

## <a name="desktop-and-mobile-public-client-apps"></a>デスクトップおよびモバイルのパブリック クライアント アプリ

次のサンプルは、ユーザーの名前で Microsoft Graph または Web API にアクセスするパブリック クライアント アプリケーション (デスクトップ/モバイル アプリケーション) を示しています。

クライアント アプリケーション | プラットフォーム | フロー/許可 | Microsoft Graph の呼び出し | ASP.NET Core 2.0 Web API の呼び出し
------------------ | -------- | ---------- | -------------------- | -------------------------
デスクトップ (WPF)      | .NET/C#  | Interactive | [dotnet-desktop-msgraph-v2](http://github.com/azure-samples/active-directory-dotnet-desktop-msgraph-v2) <p/> [dotnet-admin-restricted-scopes-v2](https://github.com/azure-samples/active-directory-dotnet-admin-restricted-scopes-v2) | [dotnet-native-aspnetcore-v2](https://GitHub.com/azure-samples/active-directory-dotnet-native-aspnetcore-v2)
モバイル (UWP)   | .NET/C# (UWP) | Interactive | [dotnet-native-uwp-v2](https://github.com/azure-samples/active-directory-dotnet-native-uwp-v2) |
モバイル (Android、iOS、UWP)   | .NET/C# (Xamarin) | Interactive | [xamarin-native-v2](https://Github.com/azure-samples/active-directory-xamarin-native-v2) |
モバイル (iOS)       | iOS / Objective C または Swift | Interactive | [ios-swift-native-v2](https://github.com/azure-samples/active-directory-ios-swift-native-v2) <p/> [ios-native-nxoauth2-v2](https://github.com/azure-samples/active-directory-ios-native-nxoauth2-v2) |
モバイル (Android)   | Android/Java | Interactive |   [android-native-v2](https://github.com/azure-samples/active-directory-android-native-v2 ) |

## <a name="web-applications"></a>Web アプリケーション

次のサンプルは、ユーザーのサインイン、Microsoft Graph の呼び出し、またはユーザーの ID を使った Web API の呼び出しを行う Web アプリケーションを示しています。

 プラットフォーム | ユーザーのサインインのみ | ユーザーのサインインと Microsoft Graph の呼び出し 
 -------- | ------------------- | --------------------------------- 
ASP.NET 4.x | [appmodelv2-webapp-openIDConnect-dotNet](https://GitHub.com/AzureAdQuickstarts/AppModelv2-WebApp-OpenIDConnect-DotNet) <p/> [dotnet-webapp-openidconnect-v2](https://GitHub.com/azure-samples/active-directory-dotnet-webapp-openidconnect-v2)  |              [aspnet-connect-rest-sample](https://github.com/microsoftgraph/aspnet-connect-rest-sample)   
ASP.NET Core 2.0 | [aspnetcore-webapp-openidconnect-v2](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2) |              [aspnetcore-connect-sample](https://github.com/microsoftgraph/aspnetcore-connect-sample)   
Node.js      |                   | [AppModelv2-WebApp-OpenIDConnect-nodejs](https://github.com/azureadquickstarts/appmodelv2-webapp-openidconnect-nodejs)     
Ruby      |                   | [ruby-connect-rest-sample](https://github.com/microsoftgraph/ruby-connect-rest-sample)     

## <a name="daemon-applications"></a>デーモン アプリケーション

次のサンプルは、アプリケーション ID で Microsoft Graph または Web API にアクセスするデスクトップまたは Web アプリケーションを示しています。

クライアント アプリケーション | プラットフォーム | フロー/許可 | Microsoft Graph の呼び出し 
------------------ | -------- | ---------- | -------------------- 
Web アプリ | .NET/C#  | クライアントの資格情報 | [dotnet-daemon-v2](https://github.com/azure-samples/active-directory-dotnet-daemon-v2) 

## <a name="single-page-applications-spa"></a>シングル ページ アプリケーション (SPA)

このサンプルでは、Azure AD を使用してセキュリティ保護されているシングル ページ アプリケーションの作成方法を示します。

 プラットフォーム |  Microsoft Graph の呼び出し 
 -------- |  --------------------- 
JavaScript (msal.js)  | [javascript-graphapi-v2](https://github.com/azure-samples/active-directory-javascript-graphapi-v2) 
JavaScript (msal.js + AngularJS) | [angular-connect-rest-sample](https://github.com/microsoftgraph/angular-connect-rest-sample) 
JavaScript (Hello.JS)  | [javascript-graphapi-web-v2](https://github.com/azure-samples/active-directory-javascript-graphapi-web-v2) 
JavaScript (hello.js + Angular 4) | [angular4-connect-sample](https://github.com/microsoftgraph/angular4-connect-sample) 

## <a name="web-apis"></a>Web API

### <a name="web-api-protected-by-azure-ad"></a>Azure AD によって保護されている Web API

次のサンプルは、Azure AD V2 エンドポイントで Web API を保護する方法を示しています。

プラットフォーム | サンプル | 説明
 -------- | ------------------- | ---------------------
Node.js | [dotnet-native-aspnetcore-v2](https://GitHub.com/azure-samples/active-directory-dotnet-native-aspnetcore-v2) | Azure AD V2 を使用して、WPF アプリケーションから ASP.NET Core Web API を呼び出します。

### <a name="web-api-calling-microsoft-graph-or-another-web-api"></a>Microsoft Graph または別の Web API を呼び出す Web API

このサンプルはまだ利用できません。

## <a name="other-microsoft-graph-samples"></a>Microsoft Graph のその他のサンプル

Azure AD での認証を含む、Microsoft Graph API のさまざまな使用パターンを示す[サンプル](https://github.com/microsoftgraph/msgraph-community-samples/tree/master/samples#aspnet)とチュートリアルについては、[Microsoft Graph コミュニティのサンプルとチュートリアル](https://github.com/microsoftgraph/msgraph-community-samples)をご覧ください。

## <a name="see-also"></a>関連項目

[Azure Active Directory 開発者ガイド](azure-ad-developers-guide.md)

[Active Directory Graph API の概念とリファレンス](https://msdn.microsoft.com/library/azure/hh974476.aspx)

[Azure AD Graph API Helper Library](https://www.nuget.org/packages/Microsoft.Azure.ActiveDirectory.GraphClient)
