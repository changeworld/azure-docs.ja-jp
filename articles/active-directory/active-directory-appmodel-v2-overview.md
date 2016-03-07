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
	ms.date="02/20/2016"
	ms.author="dastrock"/>

# Microsoft アカウント ユーザーおよび Azure AD ユーザーによる単一アプリでのサインイン

アプリケーション開発者はこれまで、Microsoft アカウントと Azure Active Directory の両方をサポートするには、2 つの異なるシステムを統合する必要がありました。そこで、どちらのアカウントのユーザーも Azure AD システムを使ってサインインできるようにする、新しい認証 API のバージョンを導入しました。この集中型の認証システムを **v2.0 エンドポイント**と呼びます。v2.0 エンドポイントを使用すれば、1 回のシンプルな統合によって、個人アカウントと職場/学校のアカウントを両方持っている何百万ものユーザーにリーチできるようになります。

v2.0 エンドポイントを使うアプリは、どちらの種類のアカウントを使用していても、[Microsoft Graph](https://graph.microsoft.io) や [Office 365](https://msdn.microsoft.com/office/office365/howto/authenticate-Office-365-APIs-using-v2) の REST API を使用できます。

> [AZURE.NOTE]
	Azure Active Directory のシナリオおよび機能のすべてが v2.0 エンドポイントでサポートされているわけではありません。v2.0 エンドポイントを使用する必要があるかどうかを判断するには、[v2.0 の制限事項](active-directory-v2-limitations.md)に関するページをお読みください。


## 始めに
次の中からプラットフォームを選択し、Microsoft のオープン ソース ライブラリとフレームワークを利用してアプリを構築します。または、OAuth 2.0 と OpenID Connect のプロトコル ドキュメントを使用し、認証ライブラリを使用せずに直接プロトコル メッセージを送受信することもできます。<!-- TODO: Finalize this table  -->

[AZURE.INCLUDE [active-directory-v2-quickstart-table](../../includes/active-directory-v2-quickstart-table.md)]

## 新機能	
ここで説明する概念は、v2.0 エンドポイントでできることとできないことを理解するうえで役立ちます。

- v2.0 エンドポイント 2015 のプレビュー期間中にアプリを構築した場合は、最近行われた[プロトコル変更に関する情報をお読みください](active-directory-v2-preview-oidc-changes.md)。
- [v2.0 エンドポイントを使用して構築できるアプリケーションの種類](active-directory-v2-flows.md)について詳しく学習します。
- Azure Active Directory を使い慣れた開発者の方は、[プロトコルの更新内容と v2.0 エンドポイントの相違点](active-directory-v2-compare.md)を確認してください。
- v2.0 エンドポイントの[制限事項と制約事項](active-directory-v2-limitations.md)を確認してください。

## リファレンス
これらのリンクは、プラットフォームの詳細を確認する上で役立ちます。

- [azure-active-directory](http://stackoverflow.com/questions/tagged/azure-active-directory) または [adal](http://stackoverflow.com/questions/tagged/adal) タグを使用したスタック オーバーフローの対処法についてのヒントが得られます。
- [v2.0 プロトコルのリファレンス](active-directory-v2-protocols.md)
- [v2.0 トークンのリファレンス](active-directory-v2-tokens.md)
- [v2.0 エンドポイントのスコープと同意](active-directory-v2-scopes.md)
- [Microsoft アプリ登録ポータル](https://apps.dev.microsoft.com)
- [Office 365 の REST API リファレンス](https://msdn.microsoft.com/office/office365/howto/authenticate-Office-365-APIs-using-v2)
- [Microsoft Graph](https://graph.microsoft.io)
- 以下に、v2.0 エンドポイントでテスト済みのオープン ソース クライアント ライブラリとサンプルを示します。[OpenID Connect Dynamic Client Registration](https://openid.net/specs/openid-connect-registration-1_0.html) やトークン検証エンドポイントなどの機能はまだサポートされていないため、v2.0 エンドポイントを使用するライブラリではこれらの機能の無効化が必要になる場合があるので注意してください。  

  - [Java WSO2 ID サーバー](https://docs.wso2.com/display/IS500/Introducing+the+Identity+Server)
  - [Java Gluu フェデレーション](https://github.com/GluuFederation/oxAuth)
  - [Node.Js Passport OpenID Connect](https://www.npmjs.com/package/passport-openidconnect)
  - [PHP OpenID Connect の基本的なクライアント](https://github.com/jumbojett/OpenID-Connect-PHP)
  - [Android OpenID Connect のサンプル](https://github.com/learning-layers/android-openid-connect)

<!-- TODO: These articles
- [ADAL Library Reference]()
- [v2 Endpoint FAQs](active-directory-v2-faq.md)
- Give us your thoughts on the preview using [User Voice](http://feedback.azure.com/forums/169401-azure-active-directory) - we want to hear them!  Use the phrase "AppModelv2:" in the title of your post so we can find it.
-->

<!---HONumber=AcomDC_0224_2016-->