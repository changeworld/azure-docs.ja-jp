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
	ms.date="11/06/2015"
	ms.author="dastrock"/>

# アプリ モデル v2.0 プレビュー: Microsoft アカウント ユーザーおよび Azure AD ユーザーによる単一アプリでのサインイン

> [AZURE.NOTE]この情報は、v2.0 アプリ モデルのパブリック プレビューに関するものです。一般公開されている Azure AD サービスと連携する手順については、[Azure Active Directory Developer’s Guide (Azure Active Directory 開発者ガイド)](active-directory-developers-guide.md) を参照してください。

アプリケーション開発者はこれまで、Microsoft アカウントと Azure Active Directory の両方をサポートするには、2 つの異なるシステムを統合する必要がありました。v2.0 アプリ モデルでは、ユーザーは、いずれのアカウントでもサインインできるようになりました。1 回のシンプルな統合によって、個人アカウントと職場または学校のアカウントを両方持っている何百万ものユーザーを含む対象ユーザーに利用していただけるようになりました。

いずれの種類のアカウントを使用しても、一連の [Office 365 の REST API](https://www.msdn.com/office/office365/howto/authenticate-Office-365-APIs-using-v2) を利用できます現在これらの API には、Outlook のメール、連絡先、および予定表の API が含まれています。その他のサービスは、今後追加される予定です。
<!-- TODO: customer reference article -->
<!-- Several apps have already begun to bridge the gap between consumer and enterprise accounts, including: [Boomerang](), [TripIt](), & [Uber](). -->

v2.0 のアプリ モデルはプレビュー期間中です。プレビュー期間中は、新しいアプリ モデルに関する皆様のフィードバックや感想をお聞かせください。フィードバックに基づいて、サービス向上のために、大幅な変更を加える場合があります。この期間中は、v2.0 のアプリ モデルで実稼働アプリケーションをリリースしないようにしてください。
<!-- TODO: Get approval on how it looks  -->

## 使用の開始
v2.0 のアプリ モデルを使用して、ご自分のアプリケーションを稼働する方法は 2 つあります。1 つは [OAuth 2.0](active-directory-v2-protocols.md#oauth2-authorization-code-flow) または [Open ID Connect](active-directory-v2-protocols.md#openid-connect-sign-in-flow) を使用して、プロトコル メッセージを直接送信する方法です。もう 1 つは、Microsoft のライブラリを使用する方法です。次の中からお好みのプラットフォームを選択して、作業を開始してください。
<!-- TODO: Finalize this table  -->

[AZURE.INCLUDE [active-directory-v2-quickstart-table](../../includes/active-directory-v2-quickstart-table.md)]

## 新機能
今後の v2.0 アプリ モデルのパブリック プレビューの変更については、このページを頻繁に確認してください。すべての更新については、@AzureAD からツイートもしていきます。

- [アプリ モデル v2.0 を使用して構築できるアプリケーションの種類](active-directory-v2-flows.md)について詳しく学習します。
- Azure Active Directory を使い慣れた開発者の方は、[プロトコルの更新および v2.0 アプリ モデルで異なる点](active-directory-v2-compare.md)を確認してください。
- 現在の[プレビューの制限事項および制約事項](active-directory-v2-limitations.md)を参照してください。

## リファレンス
これらのリンクは、プラットフォームの詳細を確認する上で役立ちます。

- [azure-active-directory](http://stackoverflow.com/questions/tagged/azure-active-directory) または [adal](http://stackoverflow.com/questions/tagged/adal) タグを使用したスタック オーバーフローの対処法についてのヒントが得られます。
- [User Voice](http://feedback.azure.com/forums/169401-azure-active-directory) を使用して、プレビューに関する感想をお寄せください。皆様からのご意見をお待ちしております。 識別しやすいように、投稿のタイトルに "AppModelv2:" という言葉を入れてください。
- [アプリ モデル v2.0 プロトコルのリファレンス](active-directory-v2-protocols.md)
- [アプリ モデル v2.0 トークンのリファレンス](active-directory-v2-tokens.md)
- [Office 365 の REST API リファレンス](https://msdn.microsoft.com/office/office365/howto/authenticate-Office-365-APIs-using-v2)
- [v2 のエンドポイントでのスコープと同意](active-directory-v2-scopes.md)

<!-- TODO: These articles
- [ADAL Library Reference]()
- [v2 Endpoint FAQs](active-directory-v2-faq.md)
-->

<!---HONumber=AcomDC_1217_2015-->