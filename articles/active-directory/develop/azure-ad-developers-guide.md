---
title: 開発者のための Azure Active Directory | Microsoft Docs
description: この記事では、Azure Active Directory を使用した Microsoft の職場および学校アカウントのサインインの概要について説明します。
services: active-directory
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 5c872c89-ef04-4f4c-98de-bc0c7460c7c2
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/30/2018
ms.author: celested
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 666a677943811af05cd3403eab4887271c1f87b3
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/07/2018
ms.locfileid: "39591212"
---
# <a name="azure-active-directory-for-developers"></a>開発者のための Azure Active Directory

Azure Active Directory (Azure AD) はクラウドの ID サービスです。開発者はこのサービスを使って、Microsoft の職場または学校アカウントによる安全なサインインをユーザーに提供するアプリを構築できます。 Azure AD は、シングル テナント アプリと基幹業務 (LOB) アプリを構築する開発者、さらには、マルチテナント アプリの開発を目指す開発者を支援します。 Azure AD を使用することで、基本的なサインイン機能が得られることに加え、Microsoft の API ([Microsoft Graph](https://developer.microsoft.com/en-us/graph/docs/concepts/overview) など) のほか、Azure AD プラットフォーム上に構築されたカスタム API をアプリから呼び出せるようになります。 このドキュメントでは、OAuth 2.0 や OpenID Connect といった業界標準のプロトコルを使用して、アプリケーションに Azure AD のサポートを追加する方法を示します。

> [!NOTE]
> このページのコンテンツの大部分は、Azure AD v1.0 エンドポイントを重点的に取り上げていますが、このエンドポイントでサポートされるのは、Microsoft の職場または学校アカウントだけです。 コンシューマー用または個人用 Microsoft アカウントでのサインインに対応する必要がある場合は、[Azure AD v2.0 エンドポイント](active-directory-appmodel-v2-overview.md)に関する情報を参照してください。 Azure AD v2.0 エンドポイントでは、Azure AD アカウント (職場や学校) を使用するユーザーと個人用 Microsoft アカウントを使用するユーザーの両方のサインインに対応したアプリの開発エクスペリエンスが統一されています。

| | |
| --- | --- |
|[認証の基本](authentication-scenarios.md) | Azure AD での認証の概要。 |
|[アプリケーションの種類](authentication-scenarios.md#application-types-and-scenarios) | Azure AD でサポートされる認証シナリオの概要。 |      
| | |

## <a name="get-started"></a>作業開始
以下のガイド付きの手順では、Azure AD Authentication Library (ADAL) SDK を使って好みのプラットフォームでアプリを構築する方法をわかりやすく説明しています。 Microsoft Authentication Library (MSAL) の使用に関する情報をお探しの場合は、[Azure AD v2.0 エンドポイント](active-directory-appmodel-v2-overview.md)に関する Microsoft のドキュメントをご覧ください。

|  |  |  |  |
| --- | --- | --- | --- |
| <center>![モバイル アプリとデスクトップ アプリ](./media/azure-ad-developers-guide/NativeApp_Icon.png)<br />モバイル アプリとデスクトップ アプリ</center> | [概要](authentication-scenarios.md#native-application-to-web-api)<br /><br />[iOS](quickstart-v1-ios.md)<br /><br />[Android](quickstart-v1-android.md) | [.NET (WPF)](quickstart-v1-dotnet.md)<br /><br />[Xamarin](quickstart-v1-xamarin.md) |
| <center>![Web アプリ](./media/azure-ad-developers-guide/Web_app.png)<br />Web アプリ</center> | [概要](authentication-scenarios.md#web-browser-to-web-application)<br /><br />[ASP.NET](quickstart-v1-aspnet-webapp.md)<br /><br />[Java](https://github.com/Azure-Samples/active-directory-java-webapp-openidconnect) | [Python](https://github.com/Azure-Samples/active-directory-python-webapp-graphapi)<br/><br/> [Node.js](quickstart-v1-openid-connect-code.md) |
| <center>![シングル ページ アプリ](./media/azure-ad-developers-guide/SPA.png)<br />シングル ページ アプリ</center> | [概要](authentication-scenarios.md#single-page-application-spa)<br /><br />[AngularJS](quickstart-v1-angularjs-spa.md)<br /><br />[JavaScript](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) |  |
| <center>![Web API](./media/azure-ad-developers-guide/Web_API.png)<br />Web API</center> | [概要](authentication-scenarios.md#web-application-to-web-api)<br /><br />[ASP.NET](quickstart-v1-dotnet-webapi.md)<br /><br />[Node.js](quickstart-v1-nodejs-webapi.md) | &nbsp; |
| <center>![サービス間](./media/azure-ad-developers-guide/Service_App.png)<br />サービス間</center> | [概要](authentication-scenarios.md#daemon-or-server-application-to-web-api)<br /><br />[.NET](sample-v1-code.md#daemon-applications-accessing-web-apis-with-the-applications-identity)|  |
|  |  |  |  |  |

## <a name="how-to-guides"></a>ハウツー ガイド
次のガイドでは、Azure AD での最も一般的な作業の手順を説明しています。

|                                                                           |  |
|---------------------------------------------------------------------------| --- |
|[アプリケーションの登録](quickstart-v1-integrate-apps-with-azure-ad.md)           | Azure AD にアプリケーションを登録する方法。 |
|[マルチテナント アプリケーション](howto-convert-app-to-be-multi-tenant.md)    | Microsoft の職場アカウントでサインインできるようにする方法。 |
|[OAuth と OpenID Connect プロトコル](v1-protocols-openid-connect-code.md)| Microsoft の認証プロトコルを使用してユーザーをサインインさせたり、Web API を呼び出したりする方法。 |
|  |  |

## <a name="reference-topics"></a>参照トピック
次の記事には、Azure AD で使用される API、プロトコル メッセージ、用語に関する詳細な情報が記載されています。

|                                                                                   | |
| ----------------------------------------------------------------------------------| --- |
| [認証ライブラリ (ADAL)](active-directory-authentication-libraries.md)   | Azure AD が提供するライブラリと SDK の概要。 |
| [コード サンプル](sample-v1-code.md)                                  | Azure AD のコード サンプルの全一覧。 |
| [用語集](developer-glossary.md)                                      | このドキュメント全体で使用されている用語と語句の定義。 |
|  |  |


[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
