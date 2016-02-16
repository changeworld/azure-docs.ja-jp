<properties
	pageTitle="Azure Active Directory B2C プレビュー: 概要 | Microsoft Azure"
	description="Azure Active Directory B2C によるコンシューマー向けアプリケーションの開発"
	services="active-directory-b2c"
	documentationCenter=""
	authors="swkrish"
	manager="msmbaldwin"
	editor="bryanla"/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="01/06/2016"
	ms.author="swkrish"/>

# Azure Active Directory B2C プレビュー: アプリケーションにコンシューマーをサインアップおよびサインインする

**Azure Active Directory B2C** は、コンシューマー向け Web アプリケーションおよびモバイル アプリケーション用の包括的なクラウド ID 管理ソリューションです。億単位のコンシューマー ID まで拡張可能な高可用性のグローバル サービスです。エンタープライズ レベルのセキュリティ保護されたプラットフォームに構築された Azure Active Directory B2C は、アプリケーション、ビジネス、およびコンシューマーを保護された状態に保ちます。

これまで、開発したアプリケーションにコンシューマーがサインアップおよびサインインすることを望むアプリケーション開発者は、独自のコードを記述していました。開発者は、オンプレミスのデータベースまたはシステムを使用して、ユーザー名とパスワードを保存していました。Azure Active Directory B2C では、セキュリティ保護された標準ベースのプラットフォームと機能豊富で拡張可能なポリシーのセットを使用して、より簡単にコンシューマーの ID 管理をアプリケーションに統合できます。Azure Active Directory B2C を使用すると、コンシューマーは、既存のソーシャル アカウント (Facebook、Google、Amazon、LinkedIn) を使用するか、または新しい資格情報 (電子メール アドレスとパスワードまたはユーザー名とパスワード) を作成することによって、アプリケーションにサインアップできます。後者は「ローカル アカウント」と呼ばれます。

Azure Active Directory B2C はプレビュー状態です。プレビュー期間中、Azure Active Directory B2C を使ってみてフィードバックやご意見をお送りください。フィードバックに基づいて、サービス向上のために、大幅な変更を加える場合があります。この期間中は、プレビューを使用して運用アプリケーションをリリースしないようにしてください。[User Voice](https://feedback.azure.com/forums/169401-azure-active-directory/) を使用してご意見をお送りください。

## 使用の開始

コンシューマーのサインアップおよびサインインを受け付けるアプリケーションを作成するには、最初にアプリケーションを Azure Active Directory B2C テナントに登録する必要があります。この[記事](active-directory-b2c-get-started.md)で説明されている手順を使用して、テナントを取得します。

Azure Active Directory B2C サービス対応のアプリケーションを作成するには、プロトコル メッセージを直接送信する方法、[OAuth 2.0](active-directory-b2c-reference-protocols.md#oauth2-authorization-code-flow) または [Open ID Connect](active-directory-b2c-reference-protocols.md#openid-connect-sign-in-flow) を使用する方法、またはライブラリを使用する方法があります (次の表で好みのプラットフォームを選択してください)。

[AZURE.INCLUDE [active-directory-b2c-quickstart-table](../../includes/active-directory-b2c-quickstart-table.md)]

## 新機能

今後の Azure Active Directory B2C プレビューに変更については、このページを頻繁に確認してください。すべての更新については、@AzureAD からツイートもしていきます。

- [拡張可能ポリシー フレームワーク](active-directory-b2c-reference-policies.md)と、アプリケーションで作成および使用できるポリシーの種類について、理解してください。
- 現在の[プレビューの制限事項および制約事項](active-directory-b2c-limitations.md)を参照してください。

## 方法

Azure Active Directory B2C プレビューの特定の機能を使用する方法を理解してください。

- コンシューマー向けアプリケーションで使用するアカウント ([Facebook](active-directory-b2c-setup-fb-app.md)、[Google+](active-directory-b2c-setup-goog-app.md)、[Microsoft アカウント](active-directory-b2c-setup-msa-app.md)、[Amazon](active-directory-b2c-setup-amzn-app.md)、[LinkedIn](active-directory-b2c-setup-li-app.md)) を構成する。
- [カスタム属性を使用してコンシューマーに関する情報を収集する](active-directory-b2c-reference-custom-attr.md)。
- [コンシューマー向けアプリケーションで Multi-factor Authentication を有効にする](active-directory-b2c-reference-mfa.md)。
- [コンシューマー向けにセルフ サービス パスワードのリセットをセットアップする](active-directory-b2c-reference-sspr.md)。
- Azure Active Directory B2C によって提供される[サインアップ、サインイン、その他のコンシューマー向けページをカスタマイズする](active-directory-b2c-reference-ui-customization.md)。
- Azure Active Directory B2C テナントで [Azure Active Directory Graph API を使用してプログラムによりコンシューマーを作成、読み取り、更新、削除する](active-directory-b2c-devquickstarts-graph-dotnet.md)。

## リファレンス

以下のリンクは、サービスの詳細を調べるのに役立ちます。

- 「[Azure Active Directory B2C の価格](https://azure.microsoft.com/pricing/details/active-directory-b2c/)」をご覧ください。
- [azure-active-directory](http://stackoverflow.com/questions/tagged/azure-active-directory) または [adal](http://stackoverflow.com/questions/tagged/adal) タグを使用したスタック オーバーフローの対処法についてのヒントが得られます。
- [User Voice](https://feedback.azure.com/forums/169401-azure-active-directory/) を使用して、プレビューに関する感想をお寄せください。皆様からのご意見をお待ちしております。 識別しやすいように、投稿のタイトルに "AzureADB2C:" という言葉を入れてください。
- Azure Active Directory B2C は、"App Model v2.0" と呼ばれるアプリケーション登録モデルを使用して、業界標準のプロトコル (OpenID Connect、OAuth 2.0) をサポートします。
  - [アプリ モデル v2.0 プロトコルのリファレンス](active-directory-b2c-reference-protocols.md)
  - [アプリ モデル v2.0 トークンの参照](active-directory-b2c-reference-tokens.md)
- [Azure Active Directory B2C の FAQ](active-directory-b2c-faqs.md)
- [Azure Active Directory B2C に対するサポート要求の提出](active-directory-b2c-support.md)

<!---HONumber=AcomDC_0211_2016-->