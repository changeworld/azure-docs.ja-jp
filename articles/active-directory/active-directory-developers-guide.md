<properties
   pageTitle="Azure Active Directory 開発者ガイド"
   description="Azure Active Directory の開発者向けリソースの包括的なガイドです。"
   services="active-directory"
   documentationCenter="dev-center-name"
   authors="msmbaldwin"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="06/10/2015"
   ms.author="mbaldwin"/>


# Azure Active Directory 開発者ガイド

## 概要
Identity Management As A Service (IDMaaS) プラットフォームとして Azure Active Directory を使用することにより、管理者は、ID 管理をアプリケーションに効果的に統合することができます。次に示す記事は、Azure Active Directory の実装、および重要な機能の概要について説明しています。これらの記事は順番どおりに読むことをお勧めします。実作業に取りかかることができる場合は、「[作業の開始](#getting-started)」から読むこともできます。


1. **[Azure AD 統合のメリット](active-directory-how-to-integrate.md)**: Azure Active Directory との統合が、セキュリティで保護されたサインインと承認に最適なソリューションである理由を説明します。

1. **[Authentication Scenarios for Azure AD](active-directory-authentication-scenarios.md)**: Azure Active Directory の簡略化された認証のメリットを活用して、アプリケーションにサインオン機能を組み込む方法を説明します。

1. **[Azure AD Graph API](https://msdn.microsoft.com/library/azure/hh974476.aspx)**: Azure Active Directory Graph API を使用して、REST API エンドポイント経由で Azure AD にプログラムからアクセスする方法を説明します。

1. **[Azure Active Directory へのアプリケーションの登録](https://msdn.microsoft.com/library/azure/dn151122.aspx)**: アプリケーションの登録と、マルチテナント アプリケーションのブランド化のガイドラインについて説明します。

1. **[Azure Active Directory の認証ライブラリ](https://msdn.microsoft.com/library/azure/dn151135.aspx)**: Azure の認証ライブラリを使用して、ユーザーを認証しアクセス トークンを取得する処理を簡単に組み込む方法を説明します。

Build 2015 のカンファレンスで発表された AAD の概要を参照するには、下の「[ビデオ](#videos)」セクションを参照してください。


## Getting Started (概要)

これらのチュートリアルは複数のプラットフォームに対応しているため、Azure Active Directory を使用した開発を迅速に開始できます。前提条件として、[Azure Active Directory テナントを取得](active-directory-howto-tenant.md)する必要があります。

#### モバイルまたは PC のアプリケーションのクイック スタート ガイド

- [iOS](active-directory-devquickstarts-ios.md)
- [Android](active-directory-devquickstarts-android.md)
- [.NET](active-directory-devquickstarts-dotnet.md)
- [Windows Phone](active-directory-devquickstarts-windowsphone.md)
- [Windows ストア](active-directory-devquickstarts-windowsstore.md)
- [Xamarin](active-directory-devquickstarts-xamarin.md)
- [Cordova](active-directory-devquickstarts-cordova.md)


####Web アプリケーションまたは Web API のクイック スタート ガイド

- [.NET Web アプリ](active-directory-devquickstarts-webapp-dotnet.md)
- [.NET Web API](active-directory-devquickstarts-webapi-dotnet.md)
- [Javascript](active-directory-devquickstarts-angular.md)
- [Node.JS](active-directory-devquickstarts-webapi-nodejs.md)


## 方法

次の記事では、Azure Active Directory (AD) を使用して特定のタスクを実行する方法について説明しています。

- [How to get an Azure Active Directory tenant](active-directory-howto-tenant.md)
- [Listing your application in the Azure Active Directory application gallery](active-directory-app-gallery-listing.md)
- [Get started with Office 365 APIs in apps](https://msdn.microsoft.com/office/office365/howto/getting-started-Office-365-APIs)
- [販売者ダッシュボードに Office 365 用 Web アプリを送信する方法](https://msdn.microsoft.com/office/office365/howto/submit-web-apps-seller-dashboard)


## リファレンス

以下の記事は、REST と認証ライブラリ API、プロトコル、エラー、コード サンプル、エンドポイントの基礎的なリファレンスです。

###  サポート
- **[サポート情報](http://stackoverflow.com/questions/tagged/azure-active-directory)**: [azure-active-directory](http://stackoverflow.com/questions/tagged/azure-active-directory) および [adal](http://stackoverflow.com/questions/tagged/adal) というタブを検索すると、スタック オーバーフローについての Azure AD 関連のソリューションが見つかります。

### コード

- **[Azure AD オープン ソース ライブラリ](http://github.com/AzureAD)**: [ライブラリ リスト](https://msdn.microsoft.com/library/azure/dn151135.aspx)を使用すると、ライブラリのソースを最も簡単に見つけることができます。

- **[Azure AD のサンプル](http://github.com/AzureADSamples)**: [コード サンプルのインデックス](active-directory-code-samples.md)を使用すると、サンプルの一覧を最も簡単に参照することができます。


### Graph API

- **[Azure AD Graph API](https://msdn.microsoft.com/library/azure/hh974476.aspx)**: Azure Active Directory Graph API の REST リファレンスです。[新しくわかりやすい対話方式で、Graph API リファレンスを表示します](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog)。

- **[Graph API Permission Scopes](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/graph-api-permission-scopes)**: アプリケーションに付与されている、テナント内のディレクトリ データに対するアクセス権を制御するために使用される、OAuth 2.0 のアクセス許可のスコープ。


### 認証プロトコル

- **[SAML 2.0 プロトコル リファレンス](https://msdn.microsoft.com/library/azure/dn195591.aspx)**: SAML 2.0 プロトコルにより、アプリケーションでユーザーにシングル サインオン エクスペリエンスを提供することができます。


- **[Azure AD での OAuth 2.0](https://msdn.microsoft.com/library/azure/dn645545.aspx)**: OAuth 2.0 プロトコルを使用することにより、Azure AD テナントの Web アプリケーションと Web API へのアクセスを承認することができます。


- **[OpenID Connect 1.0](https://msdn.microsoft.com/library/azure/dn645541.aspx)**: OpenID Connect 1.0 プロトコルは、OAuth 2.0 を認証プロトコルとして使用するように拡張します。


- **[WS-Federation 1.2](https://msdn.microsoft.com/library/azure/dn903702.aspx)**: Web Services Federation バージョン 1.2 仕様で指定される、WS-Federation 1.2 プロトコル。

- **[Supported Token and Claim Types](active-directory-token-and-claims.md)**: SAML 2.0 および JSON Web Tokens (JWT) トークンのクレームを理解して評価するためのガイドです。

## ビデオ

### Build 2015

Azure Active Directory を使用したアプリ開発に関する次の概要のプレゼンテーションでは、エンジニアリング チームで直接実務を担当している話し手が登場し、IDMaaS、認証、ID フェデレーション、シングル サインオンなどの基本的なトピックを取り上げています。

- **[Azure Active Directory: Identity Management as a Service for Modern Applications](http://azure.microsoft.com/documentation/videos/build-2015-azure-active-directory-identity-management-as-a-service-for-modern-applications)**
- **[Develop Modern Web Applications with Azure Active Directory](http://azure.microsoft.com/documentation/videos/build-2015-develop-modern-web-applications-with-azure-active-directory)**
- **[Develop Modern Native Applications with Azure Active Directory](http://azure.microsoft.com/documentation/videos/build-2015-develop-modern-native-applications-with-azure-active-directory)**

### Azure Friday
[Azure Friday](http://azure.microsoft.com/documentation/videos/azure-friday/) は、金曜日ごとに定期的に配信される 1 対 1 の対話形式のビデオ シリーズで、さまざまな Azure のトピックに関する専門家への 10 ～ 15 分間の短いインタビューで構成されています。ページにあるサービス フィルター機能を使用すると、すべての Azure Active Directory ビデオを視聴することができます。

- **[Azure Identity 101](http://azure.microsoft.com/documentation/videos/azure-identity-basics/)**
- **[Azure Identity 102](http://azure.microsoft.com/documentation/videos/azure-identity-creating-active-directory/)**
- **[Azure Identity 103](http://azure.microsoft.com/documentation/videos/azure-identity-application-to-authenticate/)**

## ソーシャル

- **[Active Directory チームのブログ](http://blogs.technet.com/b/ad/)**: Azure AD の世界における最新の開発状況が紹介されます。

- **[Azure AD Graph のブログ](http://blogs.msdn.com/b/aadgraphteam)**: Graph API に固有の Azure AD 情報を参照できます。

- **[Cloud Identity](http://www.cloudidentity.net)**: Active Directory のプリンシパル PM による、サービスとしての ID 管理の考察を読むことができます。

- **[Twitter での Azure AD](https://twitter.com/azuread)**: Azure AD に関する発表事項を 140 文字以内で受け取ることができます。

<!---HONumber=July15_HO3-->