---
title: Azure Active Directory のコード サンプル | Microsoft Docs
description: シナリオ別に整理された Azure Active Directory (v1 エンドポイント) のコード サンプルのインデックスを提供します。
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: mtillman
editor: ''
ms.assetid: a242a5ff-7300-40c2-ba83-fb6035707433
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/24/2018
ms.author: celested
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 221ff82e9c8d5164dfc7d7ee25623e6f73f59e13
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2018
ms.locfileid: "39580420"
---
# <a name="azure-active-directory-code-samples-v1-endpoint"></a>Azure Active Directory のコード サンプル (V1 エンドポイント)

[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

Microsoft Azure Active Directory (Azure AD) を使用すると、Web アプリケーションおよび Web API に認証と承認を追加できます。

このセクションでは、Azure AD V1 エンドポイントの詳細を学ぶために使用できるサンプルへのリンクを提供します。 これらのサンプルでは、その実行方法と、アプリケーションで使用できるコード スニペットを示します。 コード サンプルのページには、要件、インストール、設定に関する詳細な Readme トピックが含まれています。 また、重要なセクションの理解に役立つように、コードにはコメントが付けられています。

> [!NOTE]
> Azure AD V2 のコード サンプルに関心がある場合は、「[シナリオ別の v2.0 コード サンプル](sample-v2-code.md)」をご覧ください。

各サンプル タイプの基本的なシナリオを理解するには、「[Azure AD の認証シナリオ](authentication-scenarios.md)」をご覧ください。

GitHub でサンプルに協力することもできます。 その方法については、[Microsoft Azure Active Directory のサンプルとドキュメント](https://github.com/Azure-Samples?page=3&query=active-directory)をご覧ください。

## <a name="desktop-and-mobile-public-client-applications-calling-microsoft-graph-or-a-web-api"></a>Microsoft Graph または Web API を呼び出すデスクトップおよびモバイルのパブリック クライアント アプリケーション

次のサンプルは、ユーザーの名前で Microsoft Graph または Web API にアクセスするパブリック クライアント アプリケーション (デスクトップ/モバイル アプリケーション) を示しています。

クライアント アプリケーション | プラットフォーム | フロー/許可 | Microsoft Graph の呼び出し | ASP.NET または ASP.NET Core 2.0 Web API の呼び出し
------------------ | -------- | ---------- | -------------------- | -------------------------
デスクトップ (WPF)           | .NET/C# | Interactive | [dotnet-native-multitarget](https://github.com/azure-samples/active-directory-dotnet-native-multitarget) | [Dotnet-native-desktop](https://github.com/Azure-Samples/active-directory-dotnet-native-desktop) </p> [dotnet-native-aspnetcore](https://azure.microsoft.com/resources/samples/active-directory-dotnet-native-aspnetcore/)</p> [dotnet-webapi-manual-jwt-validation](https://github.com/azure-samples/active-directory-dotnet-webapi-manual-jwt-validation)
モバイル (UWP)            | .NET/C#  | Interactive | [dotnet-native-uwp-wam](https://github.com/azure-samples/active-directory-dotnet-native-uwp-wam) |  [dotnet-windows-store](https://github.com/Azure-Samples/active-directory-dotnet-windows-store) (シングル テナント Web API) </p> [dotnet-webapi-multitenant-windows-store](https://github.com/Azure-Samples/active-directory-dotnet-webapi-multitenant-windows-store) (マルチテナント Web API)|
モバイル (Android、iOS、UWP)   | .NET/C# (Xamarin) | Interactive | [dotnet-native-multitarget](https://github.com/azure-samples/active-directory-dotnet-native-multitarget) |
モバイル (Android)           | Android/Java | Interactive |   [android](https://github.com/Azure-Samples/active-directory-android) |
モバイル (iOS)           | iOS/Objective C | Interactive |   [nativeClient-iOS](https://github.com/azureadquickstarts/nativeclient-ios) |
デスクトップ (コンソール)          | .NET/C# | ユーザー名/パスワード </p> Windows 統合認証 | | [dotnet-native-headless](https://github.com/azure-samples/active-directory-dotnet-native-headless)
デスクトップ (コンソール)           | .NET Core/C# | デバイスのプロファイル | | [dotnet-deviceprofile](https://github.com/Azure-Samples/active-directory-dotnet-deviceprofile)

## <a name="web-applications"></a>Web アプリケーション

### <a name="web-applications-signing-in-users-calling-microsoft-graph-or-a-web-api-with-the-users-identity"></a>Web アプリケーションでのユーザーのサインインと、ユーザーの ID による Microsoft Graph または Web API の呼び出し

 プラットフォーム | ユーザーのサインインのみ | Microsoft Graph または AAD Graph の呼び出し| 別の ASP.NET または ASP.NET Core 2.0 Web API の呼び出し
 -------- | ------------------- | --------------------- | -------------------------
ASP.NET 4.5 | [webApp-openidconnect-dotnet](https://docs.microsoft.com/azure/active-directory/develop/guidedsetups/active-directory-aspnetwebapp-v1) </p> [webapp-WSFederation-dotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-wsfederation) | [dotnet-webapp-multitenant-openidconnect](https://github.com/Azure-Samples/active-directory-dotnet-webapp-multitenant-openidconnect) (AAD Graph) |
ASP.NET Core 2.0 | [dotnet-webapp-openidconnect-aspnetcore](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect-aspnetcore) | [webapp-webapi-multitenant-openidconnect-aspnetcore](https://github.com/Azure-Samples/active-directory-webapp-webapi-multitenant-openidconnect-aspnetcore/) (AAD Graph) | [dotnet-webapp-webapi-openidconnect-aspnetcore](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-openidconnect-aspnetcore)
ASP.NET 4.5 | [dotnet-webapp-webapi-oauth2-useridentity](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-oauth2-useridentity) | |
Python | | [python-webapp-graphapi](https://github.com/Azure-Samples/active-directory-python-webapp-graphapi)  |
Java | | [java-webapp-openidconnect](https://github.com/azure-samples/active-directory-java-webapp-openidconnect)  |
PHP | | [php-graphapi-web](https://github.com/Azure-Samples/active-directory-php-graphapi-web)  |

### <a name="web-applications-demonstrating-role-based-access-control-authorization"></a>ロールベースのアクセス制御 (承認) を示す Web アプリケーション

次のサンプルでは、Web アプリケーションの特定機能のアクセス許可を特定のユーザーに制限するために使用する、ロールベースのアクセス制御の実装方法を示します。 ユーザーには、Azure AD のグループまたはロールに属しているかどうかに応じて権限が与えられます。

プラットフォーム | サンプル | 説明
 -------- | ------------------- | ---------------------
ASP.NET 4.5 | [dotnet-webapp-groupclaims](https://github.com/Azure-Samples/active-directory-dotnet-webapp-groupclaims) | Azure AD の**グループ**を承認に使用する .NET 4.5 MVC Web アプリ
ASP.NET 4.5 | [dotnet-webapp-roleclaims](https://github.com/Azure-Samples/active-directory-dotnet-webapp-roleclaims) | Azure AD の**ロール**を承認に使用する .NET 4.5 MVC Web アプリ

## <a name="daemon-applications-accessing-web-apis-with-the-applications-identity"></a>デーモン アプリケーション (アプリケーションの ID で Web API にアクセスする)

次のサンプルは、ユーザーなしで (アプリケーション ID で) Microsoft Graph または Web API にアクセスするデスクトップまたは Web アプリケーションを示しています。

クライアント アプリケーション | プラットフォーム | フロー/許可 | Microsoft Graph の呼び出し | ASP.NET または ASP.NET Core 2.0 Web API の呼び出し
------------------ | -------- | ---------- | -------------------- | -------------------------
デーモン アプリ (コンソール)          | .NET/C#  | アプリ シークレットまたは証明書によるクライアント資格情報 | | [dotnet-daemon](https://github.com/azure-samples/active-directory-dotnet-daemon)</p> [dotnet-daemon-certificate-credential](https://github.com/azure-samples/active-directory-dotnet-daemon-certificate-credential)
デーモン アプリ (コンソール)         | .NET Core/C# | 証明書によるクライアント資格情報| | [dotnetcore-daemon-certificate-credential](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-certificate-credential)
デスクトップ            | Java | クライアントの資格情報 |   [java-native-headless](https://github.com/azure-samples/active-directory-java-native-headless) |
ASP.NET Web アプリ  | .NET/C# | クライアントの資格情報 |    | [dotnet-webapp-webapi-oauth2-appidentity](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-oauth2-appidentity)

## <a name="web-apis"></a>Web API

### <a name="web-api-protected-by-azure-active-directory"></a>Azure Active Directory によって保護される Web API

次のサンプルは、Azure AD で node.js Web API を保護する方法を示しています。

プラットフォーム | サンプル | 説明
 -------- | ------------------- | ---------------------
Node.js | [node-webapi](https://github.com/Azure-Samples/active-directory-node-webapi) |  Azure AD と OAuth 2.0 のアクセス トークンをセキュリティに使用した NodeJS Web API。

### <a name="web-api-calling-microsoft-graph-or-another-web-api"></a>Microsoft Graph または別の Web API を呼び出す Web API

次のサンプルでは、別の Web API を呼び出す Web API を示します。 2 番目のサンプルでは、条件付きアクセスを処理する方法を示します。

 プラットフォーム |  Microsoft Graph の呼び出し | 別の ASP.NET または ASP.NET Core 2.0 Web API の呼び出し
 -------- |  --------------------- | -------------------------
ASP.NET 4.5 | [dotnet-webapi-onbehalfof](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof) |[dotnet-webapi-onbehalfof](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof)
ASP.NET 4.5 | [dotnet-webapi-onbehalfof-ca](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof-ca) |[dotnet-webapi-onbehalfof-ca](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof-ca)

## <a name="single-page-applications"></a>シングル ページ アプリケーション

このサンプルでは、Azure AD を使用してセキュリティ保護されているシングル ページ アプリケーションの作成方法を示します。

 プラットフォーム |  Microsoft Graph の呼び出し | 独自 API の呼び出し | 別の Web API の呼び出し
 -------- |  --------------------- | ------------------ | ----------------
JavaScript / ASP.NET 4.x |  | [javascript-singlepageapp](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) |
JavaScript (AngularJS) / ASP.NET 4.x |  | [angularjs-singlepageapp](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp) |
JavaScript (AngularJS) / ASP.NET 4.x |  |  | [angularjs-singlepageapp-cors](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp-dotnet-webapi)

## <a name="other-microsoft-graph-samples"></a>Microsoft Graph のその他のサンプル

Azure AD での認証を含む、Microsoft Graph API のさまざまな使用パターンを示すサンプルとチュートリアルについては、[Microsoft Graph コミュニティのサンプルとチュートリアル](https://github.com/microsoftgraph/msgraph-community-samples)をご覧ください。

## <a name="see-also"></a>関連項目

[Azure Active Directory 開発者ガイド](azure-ad-developers-guide.md)

[Active Directory Graph API の概念とリファレンス](https://msdn.microsoft.com/library/azure/hh974476.aspx)

[Azure AD Graph API Helper Library](https://www.nuget.org/packages/Microsoft.Azure.ActiveDirectory.GraphClient)
