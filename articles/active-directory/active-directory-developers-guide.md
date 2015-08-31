<properties
   pageTitle="Azure Active Directory 開発者ガイド | Microsoft Azure"
   description="この記事は、Azure Active Directory の開発者向けリソースの包括的なガイドです。"
   services="active-directory"
   documentationCenter="dev-center-name"
   authors="msmbaldwin"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="08/12/2015"
   ms.author="mbaldwin"/>


# Azure Active Directory 開発者ガイド

## 概要
Identity Management As A Service (IDMaaS) プラットフォームとして Azure Active Directory を使用することにより、開発者は ID 管理をアプリケーションに効果的に統合することができます。次の記事では、Azure Active Directory の実装と重要な機能の概要について説明しています。これらの記事を順番に読むことをお勧めします。実作業に取りかかる準備ができている場合は、「[使用の開始](#getting-started)」に進んでください。


1. [Azure Active Directory との統合](active-directory-how-to-integrate.md): Azure Active Directory との統合が、セキュリティで保護されたサインインと承認に最適なソリューションである理由を説明します。

1. [Active Directory の認証シナリオ](active-directory-authentication-scenarios.md): Azure Active Directory の簡素化された認証を利用して、アプリケーションにサインオン機能を組み込む方法を説明します。

1. [Azure AD Graph API](https://msdn.microsoft.com/library/azure/hh974476.aspx)： Azure Active Directory Graph API を使用して、REST API エンドポイント経由でプログラムによって Azure Active Directory にアクセスする方法を説明します。

1. [Azure Active Directory とアプリケーションの統合](active-directory-integrating-applications.md): アプリケーションの登録と、マルチテナント アプリケーションのブランド化ガイドラインについて説明します。

1. [Azure Active Directory の認証ライブラリ](active-directory-authentication-libraries.md): Azure 認証ライブラリを使用して、ユーザーを認証し、アクセス トークンを取得する処理を簡単に組み込む方法を説明します。

Build 2015 のカンファレンスで発表された Azure Active Directory の概要を参照するには、後述の「[ビデオ](#videos)」をご覧ください。


## 使用の開始

次のチュートリアルは複数のプラットフォームに対応しているため、Azure Active Directory を使用した開発をすぐに開始できます。前提条件として、[Azure Active Directory テナントを取得](active-directory-howto-tenant.md)する必要があります。

### モバイルまたは PC のアプリケーションのクイック スタート ガイド

|[![iOS](./media/active-directory-developers-guide/ios.png)](active-directory-devquickstarts-ios.md)|[![Android](./media/active-directory-developers-guide/android.png)](active-directory-devquickstarts-android.md)|[![.NET](./media/active-directory-developers-guide/net.png)](active-directory-devquickstarts-dotnet.md)| [![Windows Phone](./media/active-directory-developers-guide/windows.png)](active-directory-devquickstarts-windowsphone.md)|[![Windows ストア](./media/active-directory-developers-guide/windows.png)](active-directory-devquickstarts-windowsstore.md)|[![Xamarin](./media/active-directory-developers-guide/xamarin.png)](active-directory-devquickstarts-xamarin.md)|[![Cordova](./media/active-directory-developers-guide/cordova.png)](active-directory-devquickstarts-cordova.md)
|:--:|:--:|:--:|:--:|:--:|:--:|:--:
|[iOS](active-directory-devquickstarts-ios.md)|[Android](active-directory-devquickstarts-android.md)|[.NET](active-directory-devquickstarts-dotnet.md)|[Windows Phone](active-directory-devquickstarts-windowsphone.md)|[Windows ストア](active-directory-devquickstarts-windowsstore.md)|[Xamarin](active-directory-devquickstarts-xamarin.md)|[Cordova](active-directory-devquickstarts-cordova.md)


### Web アプリケーションまたは Web API のクイック スタート ガイド

|[![.NET](./media/active-directory-developers-guide/net.png)](active-directory-devquickstarts-webapp-dotnet.md)|[![.NET](./media/active-directory-developers-guide/net.png)](active-directory-devquickstarts-webapi-dotnet.md)|[![JavaScript](./media/active-directory-developers-guide/javascript.png)](active-directory-devquickstarts-angular.md)|[![Node.js](./media/active-directory-developers-guide/nodejs.png)](active-directory-devquickstarts-webapi-nodejs.md)
|:--:|:--:|:--:|:--:
|[.NET Web アプリ](active-directory-devquickstarts-webapp-dotnet.md)|[.NET Web API](active-directory-devquickstarts-webapi-dotnet.md)|[Javascript](active-directory-devquickstarts-angular.md)|[Node.JS](active-directory-devquickstarts-webapi-nodejs.md)


## 方法

次の記事では、Azure Active Directory を使用して特定のタスクを実行する方法について説明しています。

- [Azure Active Directory テナントを取得する方法](active-directory-howto-tenant.md)
- [Azure Active Directory アプリケーション ギャラリーでのアプリケーションの表示](active-directory-app-gallery-listing.md)
- [アプリでの Office 365 API の使用](https://msdn.microsoft.com/office/office365/howto/getting-started-Office-365-APIs)
- [販売者ダッシュボードに Office 365 用 Web アプリを送信する方法](https://msdn.microsoft.com/office/office365/howto/submit-web-apps-seller-dashboard)
- [プレビュー: ユーザーが、個人カウント、および職場または学校のアカウントの両方でサインインできるアプリを構築する方法](active-directory-appmodel-v2-overview.md)


## リファレンス

以下の記事は、REST と認証ライブラリ API、プロトコル、エラー、コード サンプル、エンドポイントの基礎的なリファレンスです。

###  サポート
- [Tagged questions (タグ付けされた質問)](http://stackoverflow.com/questions/tagged/azure-active-directory): [azure-active-directory](http://stackoverflow.com/questions/tagged/azure-active-directory) タグと [adal](http://stackoverflow.com/questions/tagged/adal) タグを検索すると、スタック オーバーフローについての Azure Active Directory 関連のソリューションが見つかります。

### コード

- [Azure Active Directory オープン ソース ライブラリ](http://github.com/AzureAD): [ライブラリ リスト](active-directory-authentication-libraries.md)を使用すると、ライブラリのソースを最も簡単に見つけることができます。

- [Azure Active Directory のサンプル](http://github.com/AzureADSamples): [コード サンプルのインデックス](active-directory-code-samples.md)を使用すると、サンプルのリスト内を最も簡単に移動できます。


### Graph API

- [Azure AD Graph API](https://msdn.microsoft.com/library/azure/hh974476.aspx): Azure Active Directory Graph API の REST リファレンスです。[対話方式で Graph API リファレンスを表示します](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog)。

- [Graph API Permission Scopes (Graph API のアクセス許可スコープ)](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/graph-api-permission-scopes): OAuth 2.0 のアクセス許可スコープは、アプリケーションに付与されている、テナント内のディレクトリ データに対するアクセス権を制御するために使用されます。


### 認証プロトコル

- [SAML Protocol Reference (SAML プロトコル リファレンス)](https://msdn.microsoft.com/library/azure/dn195591.aspx): SAML 2.0 プロトコルにより、アプリケーションでユーザーにシングル サインオン エクスペリエンスを提供することができます。


- [Azure AD での OAuth 2.0](https://msdn.microsoft.com/library/azure/dn645545.aspx): OAuth 2.0 プロトコルを使用して、Azure Active Directory テナントの Web アプリケーションと Web API へのアクセスを承認できます。


- [OpenID Connect 1.0](https://msdn.microsoft.com/library/azure/dn645541.aspx): OpenID Connect 1.0 プロトコルは、OAuth 2.0 を拡張し、認証プロトコルとして使用できるようにします。


- [WS-Federation 1.2](https://msdn.microsoft.com/library/azure/dn903702.aspx): WS-Federation 1.2 プロトコルは、Web Services Federation バージョン 1.2 仕様で指定されています。

- [サポートされているトークンと要求の種類](active-directory-token-and-claims.md): このガイドを使用すると、SAML 2.0 トークンと JSON Web Tokens (JWT) トークンの要求を理解し、評価することができます。

## ビデオ

### Build 2015

Azure Active Directory を使用したアプリケーションの開発に関する次の概要プレゼンテーションには、エンジニアリング チームで実務に携わっている話し手が登場します。プレゼンテーションでは、IDMaaS、認証、ID フェデレーション、シングル サインオンなどの基本的なトピックを取り上げています。

- **[Azure Active Directory: モダン アプリケーション向けのサービスとしての ID 管理](http://azure.microsoft.com/documentation/videos/build-2015-azure-active-directory-identity-management-as-a-service-for-modern-applications)**
- **[Azure Active Directory を使用したモダン Web アプリケーションの開発](http://azure.microsoft.com/documentation/videos/build-2015-develop-modern-web-applications-with-azure-active-directory)**
- **[Azure Active Directory を使用したモダン ネイティブ アプリケーションの開発](http://azure.microsoft.com/documentation/videos/build-2015-develop-modern-native-applications-with-azure-active-directory)**

### Azure Friday
[Azure Friday](http://azure.microsoft.com/documentation/videos/azure-friday/) は、金曜日ごとに定期的に配信される 1 対 1 の対話形式のビデオ シリーズで、Azure のさまざまなトピックに関する専門家への短いインタビュー (10 ～ 15 分) で構成されています。ページにあるサービス フィルター機能を使用すると、すべての Azure Active Directory ビデオを視聴することができます。

- [Azure Identity 101](http://azure.microsoft.com/documentation/videos/azure-identity-basics/)
- [Azure Identity 102](http://azure.microsoft.com/documentation/videos/azure-identity-creating-active-directory/)
- [Azure Identity 103](http://azure.microsoft.com/documentation/videos/azure-identity-application-to-authenticate/)

## ソーシャル

- [Active Directory チームのブログ](http://blogs.technet.com/b/ad/): Azure Active Directory の世界における最新の開発状況を紹介します。

- [Azure Active Directory Graph チームのブログ](http://blogs.msdn.com/b/aadgraphteam): Graph API に固有の Azure Active Directory 情報を提供します。

- [Cloud Identity](http://www.cloudidentity.net): Azure Active Directory のプリンシパル PM による、サービスとしての ID 管理に関する考察を読むことができます。

- [Twitter での Azure Active Directory](https://twitter.com/azuread): Azure Active Directory に関するお知らせを 140 文字以内で受け取ることができます。

<!---HONumber=August15_HO8-->