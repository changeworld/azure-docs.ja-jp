<properties
	pageTitle="アプリ モデル v2.0 の概要 | Microsoft Azure"
	description="Microsoft アカウントと Azure Active Directory の両方でサインインできるアプリケーション構築を紹介します。"
	services="active-directory"
	documentationCenter=""
	authors="dstrockis"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/16/2016"
	ms.author="dastrock"/>

# Microsoft アカウント ユーザーおよび Azure AD ユーザーによる単一アプリでのサインイン

アプリケーション開発者はこれまで、Microsoft アカウントと Azure Active Directory の両方をサポートするには、2 つの異なるシステムを統合する必要がありました。そこで、どちらのアカウントのユーザーも Azure AD システムを使ってサインインできるようにする、新しい認証 API のバージョンを導入しました。この集中型の認証システムを **v2.0 エンドポイント**と呼びます。v2.0 エンドポイントを使用すれば、1 回のシンプルな統合によって、個人アカウントと職場/学校のアカウントを両方持っている何百万ものユーザーにリーチできるようになります。

v2.0 エンドポイントを使うアプリは、どちらの種類のアカウントを使用していても、[Microsoft Graph](https://graph.microsoft.io) や [Office 365](https://msdn.microsoft.com/office/office365/howto/authenticate-Office-365-APIs-using-v2) の REST API を使用できます。

<!-- For a quick introduction to the v2.0 endpoint, please view the [Getting Started with Microsoft Identities: Enterprise Grade Sign In For Your Apps](https://azure.microsoft.com/documentation/videos/build-2016-getting-started-with-microsoft-identities-enterprise-grade-sign-in-for-your-apps/) video. -->

## Getting Started (概要)
[AZURE.VIDEO build-2016-getting-started-with-microsoft-identities-enterprise-grade-sign-in-for-your-apps]

次の中からプラットフォームを選択し、Microsoft のオープン ソース ライブラリとフレームワークを利用してアプリを構築します。または、OAuth 2.0 と OpenID Connect のプロトコル ドキュメントを使用し、認証ライブラリを使用せずに直接プロトコル メッセージを送受信することもできます。

<!-- TODO: Finalize this table  -->
[AZURE.INCLUDE [active-directory-v2-quickstart-table](../../includes/active-directory-v2-quickstart-table.md)]

## 新機能
ここで説明する概念は、v2.0 エンドポイントでできることとできないことを理解するうえで役立ちます。

- v2.0 エンドポイント 2015 のプレビュー期間中にアプリを構築した場合は、最近行われた[プロトコル変更に関する情報をお読みください](active-directory-v2-preview-oidc-changes.md)。
- [v2.0 エンドポイントを使用して構築できるアプリケーションの種類](active-directory-v2-flows.md)について詳しく学習します。
- Azure Active Directory を使い慣れた開発者の方は、[プロトコルの更新内容と v2.0 エンドポイントの相違点](active-directory-v2-compare.md)を確認してください。
- v2.0 エンドポイントの[制限事項と制約事項](active-directory-v2-limitations.md)を確認してください。

## リファレンス
これらのリンクは、プラットフォームの詳細を確認する上で役立ちます。

- ビルド 2016: [Getting Started with Microsoft Identities: Enterprise Grade Sign In For Your Apps (Microsoft ID の概要: アプリへのエンタープライズ グレードのサインイン)](https://azure.microsoft.com/documentation/videos/build-2016-getting-started-with-microsoft-identities-enterprise-grade-sign-in-for-your-apps/)
- [azure-active-directory](http://stackoverflow.com/questions/tagged/azure-active-directory) または [adal](http://stackoverflow.com/questions/tagged/adal) タグを使用したスタック オーバーフローの対処法についてのヒントが得られます。
- [v2.0 プロトコルのリファレンス](active-directory-v2-protocols.md)
- [v2.0 トークンのリファレンス](active-directory-v2-tokens.md)
- [v2.0 エンドポイントのスコープと同意](active-directory-v2-scopes.md)
- [Microsoft アプリ登録ポータル](https://apps.dev.microsoft.com)
- [Office 365 の REST API リファレンス](https://msdn.microsoft.com/office/office365/howto/authenticate-Office-365-APIs-using-v2)
- [Microsoft Graph](https://graph.microsoft.io)
- 以下に、v2.0 エンドポイントでテスト済みのオープン ソース クライアント ライブラリとサンプルを示します。

  - [Java WSO2 ID サーバー](https://docs.wso2.com/display/IS500/Introducing+the+Identity+Server)
  - [Java Gluu フェデレーション](https://github.com/GluuFederation/oxAuth)
  - [Node.Js Passport OpenID Connect](https://www.npmjs.com/package/passport-openidconnect)
  - [PHP OpenID Connect の基本的なクライアント](https://github.com/jumbojett/OpenID-Connect-PHP)
  - [iOS OAuth2 クライアント](https://github.com/nxtbgthng/OAuth2Client)
  - [Android OAuth2 クライアント](https://github.com/wuman/android-oauth-client)
  - [Android OpenID Connect クライアント](https://github.com/kalemontes/OIDCAndroidLib)

<!---HONumber=AcomDC_0921_2016-->