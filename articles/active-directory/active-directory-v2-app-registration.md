<properties
	pageTitle="アプリ モデル v2.0 | Microsoft Azure"
	description="Microsoft にアプリを登録して、サインインを有効にする、またアプリをアプリ モデル v2.0 と統合する方法。"
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
	ms.date="08/12/2015"
	ms.author="dastrock"/>

# アプリ モデル v2.0 プレビュー: Microsoft にアプリを登録する方法

MSA および Azure AD サインインの両方を受け付けるアプリを構築するには、最初に、アプリを Microsoft に登録する必要があります。Azure AD または MSA で利用している既存のアプリは使用できなくなります。新しいアプリを作成しましょう。

> [AZURE.NOTE]この情報は、v2.0 アプリ モデルのパブリック プレビューに関するものです。一般公開されている Azure AD サービスと連携する手順については、「[Azure Active Directory 開発者ガイド](active-directory-developers-guide.md)」を参照してください。

## Microsoft アプリ登録ポータルにアクセスする
まず、[https://apps.dev.microsoft.com](https://apps.dev.microsoft.com) に移動します。これは、マイクロソフトのアプリに関するあらゆることを管理できる新しいアプリ登録ポータルです。

Microsoft の個人または職場/学校アカウントのいずれかでサインインします。いずれのアカウントもをお持ちでない場合は、新しい個人アカウントを新規登録します。簡単に登録できますので、今すぐサインアップしましょう。

サインアップできましたか? おそらく空の Microsoft アプリの一覧が表示されているはずです。これを変更してみましょう。

<!-- TODO: Verify strings here -->
**[アプリの追加]** をクリックし、名前を付けます。ポータルにより、アプリにグローバルに一意のアプリケーション ID が割り当てられます。これは、後ほどコードで使用します。アプリに API を呼び出すためのアクセス トークンが必要なサーバー側コンポーネントが含まれる場合 (Office、Azure、サード パーティ製の製品など)、**アプリケーションのシークレット**を作成することもできます。<!-- TODO: Link for app secrets -->

次に、アプリで使用するプラットフォームを追加します。- Web ベースのアプリの場合、サインイン メッセージを送信できる**リダイレクト URI** を指定します。- モバイル アプリの場合、自動的に作成された既定のリダイレクト URI を書き留めます。

必要に応じて、[プロファイル] セクションでサインイン ページの外観をカスタマイズできます。続行する前に、忘れずに **[保存]** をクリックします。

## クイック スタート アプリケーションの構築
Microsoft アプリを構築したら、いずれかのクイック スタート チュートリアルを実行して、アプリ モデル v2.0 を起動して使用します。以下に、推奨事項をいくつか示します。

[AZURE.INCLUDE [active-directory-v2-quickstart-table](../../includes/active-directory-v2-quickstart-table.md)]

<!---HONumber=Oct15_HO3-->