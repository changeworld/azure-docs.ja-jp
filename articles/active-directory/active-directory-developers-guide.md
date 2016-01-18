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
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="01/06/2016"
   ms.author="mbaldwin"/>


# Azure Active Directory 開発者ガイド

## 概要
Identity Management As A Service (IDMaaS) プラットフォームとして Azure Active Directory を使用することにより、開発者は ID 管理をアプリケーションに効果的に統合することができます。次の記事では、Azure Active Directory の実装と重要な機能の概要について説明しています。これらの記事を順番に読むことをお勧めします。実作業に取りかかる準備ができている場合は、「[使用の開始](#getting-started)」に進んでください。


1. [Azure Active Directory 統合のメリット](active-directory-how-to-integrate.md): Azure Active Directory との統合が、セキュリティで保護されたサインインと承認に最適なソリューションである理由を説明します。

1. [Active Directory の認証シナリオ](active-directory-authentication-scenarios.md): Azure Active Directory の簡素化された認証を利用して、アプリケーションにサインオン機能を組み込む方法を説明します。

1. [アプリケーションと Azure Active Directory の統合](active-directory-integrating-applications.md): Azure Active Directory にアプリケーションを追加し、それを更新および削除する方法と、統合されたアプリのブランド化のガイドラインについて説明します。

1. [Azure Active Directory Graph API](active-directory-graph-api.md)： Azure Active Directory Graph API を使用して、REST API エンドポイント経由でプログラムによって Azure Active Directory にアクセスする方法を説明します。Azure AD Graph API には、[Microsoft Graph](https://graph.microsoft.io/) からもアクセスできます。Microsoft Graph は、単一の REST API エンドポイントから単一のアクセス トークンを使用して複数の Microsoft クラウド サービス API にアクセスできる統合 API です。

1. [Azure Active Directory 認証ライブラリ](active-directory-authentication-libraries.md): Azure AD 認証ライブラリ (.NET、JavaScript、Objective-C、Android など用) を使用して、ユーザーを認証し、アクセス トークンを取得する処理を簡単に組み込む方法を説明します。


## 使用の開始

次のチュートリアルは複数のプラットフォームに対応しているため、Azure Active Directory を使用した開発をすぐに開始できます。前提条件として、[Azure Active Directory テナントを取得](active-directory-howto-tenant.md)する必要があります。

### モバイル アプリケーションと PC アプリケーションのクイック スタート ガイド

|[![iOS](./media/active-directory-developers-guide/ios.png)](active-directory-devquickstarts-ios.md)|[![Android](./media/active-directory-developers-guide/android.png)](active-directory-devquickstarts-android.md)|[![.NET](./media/active-directory-developers-guide/net.png)](active-directory-devquickstarts-dotnet.md)| [![Windows Phone](./media/active-directory-developers-guide/windows.png)](active-directory-devquickstarts-windowsphone.md)|[![Windows ストア](./media/active-directory-developers-guide/windows.png)](active-directory-devquickstarts-windowsstore.md)|[![Xamarin](./media/active-directory-developers-guide/xamarin.png)](active-directory-devquickstarts-xamarin.md)|[![Cordova](./media/active-directory-developers-guide/cordova.png)](active-directory-devquickstarts-cordova.md)
|:--:|:--:|:--:|:--:|:--:|:--:|:--:
|[iOS](active-directory-devquickstarts-ios.md)|[Android](active-directory-devquickstarts-android.md)|[.NET](active-directory-devquickstarts-dotnet.md)|[Windows Phone](active-directory-devquickstarts-windowsphone.md)|[Windows ストア](active-directory-devquickstarts-windowsstore.md)|[Xamarin](active-directory-devquickstarts-xamarin.md)|[Cordova](active-directory-devquickstarts-cordova.md)

### Web アプリケーション クイック スタート ガイド

|[![.NET](./media/active-directory-developers-guide/net.png)](active-directory-devquickstarts-webapp-dotnet.md)|[![Java](./media/active-directory-developers-guide/java.png)](active-directory-devquickstarts-webapp-java.md)|[![JavaScript](./media/active-directory-developers-guide/javascript.png)](active-directory-devquickstarts-angular.md)|[![Node.js](./media/active-directory-developers-guide/nodejs.png)](active-directory-devquickstarts-openidconnect-nodejs.md)
|:--:|:--:|:--:|:--:|
|[.NET](active-directory-devquickstarts-webapp-dotnet.md)|[Java](active-directory-devquickstarts-webapp-java.md)|[Javascript](active-directory-devquickstarts-angular.md)|[Node.JS](active-directory-devquickstarts-openidconnect-nodejs.md)

### Web API クイック スタート ガイド

|[![.NET](./media/active-directory-developers-guide/net.png)](active-directory-devquickstarts-webapi-dotnet.md)|[![Node.js](./media/active-directory-developers-guide/nodejs.png)](active-directory-devquickstarts-webapi-nodejs.md)
|:--:|:--:|
|[.NET](active-directory-devquickstarts-webapi-dotnet.md)|[Node.JS](active-directory-devquickstarts-webapi-nodejs.md)

### ディレクトリのクエリに関するクイック スタート ガイド

| [![.NET](./media/active-directory-developers-guide/graph.png)](active-directory-graph-api-quickstart.md)|
|:--:|
|[Graph API](active-directory-graph-api-quickstart.md)|

## 方法

次の記事では、Azure Active Directory を使用して特定のタスクを実行する方法について説明しています。

- [Azure Active Directory テナントを取得する](active-directory-howto-tenant.md)
- [Azure Active Directory アプリケーション ギャラリーでアプリケーションを一覧表示する](active-directory-app-gallery-listing.md)
- [Azure Active Directory のアプリケーション マニフェストについて理解する](active-directory-application-manifest.md)
- [Create an app with Office 365 APIs (Office 365 API を使用するアプリの作成)](https://msdn.microsoft.com/office/office365/howto/getting-started-Office-365-APIs)
- [Submit Web apps for Office 365 to the Seller Dashboard (販売者ダッシュボードへの Office 365 用 Web アプリの送信)](https://msdn.microsoft.com/office/office365/howto/submit-web-apps-seller-dashboard)
- [プレビュー: ユーザーが、個人カウント、および職場または学校のアカウントの両方でサインインできるアプリを構築する方法](active-directory-appmodel-v2-overview.md)
- [プレビュー: コンシューマーをサインアップおよびサインインさせるアプリケーションを作成する方法](active-directory-b2c-overview.md)


## リファレンス

以下の記事は、REST と認証ライブラリ API、プロトコル、エラー、コード サンプル、エンドポイントの基礎的なリファレンスです。

###  サポート
- [タグ付けされた質問に関するページ](http://stackoverflow.com/questions/tagged/azure-active-directory): Stack Overflow で [azure-active-directory](http://stackoverflow.com/questions/tagged/azure-active-directory) タグと [adal](http://stackoverflow.com/questions/tagged/adal) タグを検索すると、Azure Active Directory 関連のソリューションが見つかります。

### コード

- [Azure Active Directory オープン ソース ライブラリ](http://github.com/AzureAD): [ライブラリ リスト](active-directory-authentication-libraries.md)を使用すると、ライブラリのソースを最も簡単に見つけることができます。

- [Azure Active Directory のサンプル](https://github.com/azure-samples?query=active-directory): [コード サンプルのインデックス](active-directory-code-samples.md)を使用すると、サンプルのリスト内を最も簡単に移動できます。


### Graph API

- [Graph API リファレンス](https://msdn.microsoft.com/library/azure/hh974476.aspx): Azure Active Directory Graph API の REST リファレンスです。[対話型の Graph API リファレンスを表示します](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog)。

- [Graph API Permission Scopes (Graph API のアクセス許可スコープ)](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes): OAuth 2.0 のアクセス許可スコープは、アプリケーションに付与されている、テナント内のディレクトリ データに対するアクセス権を制御するために使用されます。

### 認証ライブラリ

- [.NET](https://msdn.microsoft.com/library/azure/mt417579.aspx): .NET 認証ライブラリに関するドキュメント。

### 認証プロトコル

- [SAML 2.0 プロトコル リファレンス](https://msdn.microsoft.com/library/azure/dn195591.aspx): SAML 2.0 プロトコルにより、アプリケーションでユーザーにシングル サインオン エクスペリエンスを提供できます。


- [Azure AD での OAuth 2.0](https://msdn.microsoft.com/library/azure/dn645545.aspx): OAuth 2.0 プロトコルを使用して、Azure Active Directory テナントの Web アプリケーションと Web API へのアクセスを承認できます。


- [OpenID Connect 1.0](https://msdn.microsoft.com/library/azure/dn645541.aspx): OpenID Connect 1.0 プロトコルは、OAuth 2.0 を拡張し、認証プロトコルとして使用できるようにします。


- [WS-Federation 1.2](https://msdn.microsoft.com/library/azure/dn903702.aspx): WS-Federation 1.2 プロトコルは、Web Services Federation バージョン 1.2 仕様で指定されています。

- [サポートされているトークンと要求の種類](active-directory-token-and-claims.md): このガイドを使用すると、SAML 2.0 トークンと JSON Web Tokens (JWT) トークンの要求を理解し、評価することができます。

## ビデオ

### Build 2015

Azure Active Directory を使用したアプリケーションの開発に関する次の概要プレゼンテーションには、エンジニアリング チームで実務に携わっている話し手が登場します。プレゼンテーションでは、IDMaaS、認証、ID フェデレーション、シングル サインオンなどの基本的なトピックを取り上げています。

- [Azure Active Directory: モダン アプリケーション向けのサービスとしての ID 管理](http://azure.microsoft.com/documentation/videos/build-2015-azure-active-directory-identity-management-as-a-service-for-modern-applications)
- [Azure Active Directory を使ったモダン Web アプリケーションの開発](http://azure.microsoft.com/documentation/videos/build-2015-develop-modern-web-applications-with-azure-active-directory)
- [Azure Active Directory を使ったモダン ネイティブ Web アプリケーションの開発](http://azure.microsoft.com/documentation/videos/build-2015-develop-modern-native-applications-with-azure-active-directory)

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

<!---HONumber=AcomDC_0107_2016-->