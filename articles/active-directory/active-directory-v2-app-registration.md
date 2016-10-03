<properties
	pageTitle="v2.0 アプリケーションの登録 | Microsoft Azure"
	description="v2.0 エンドポイントを使用し、アプリケーションを Microsoft に登録して Microsoft サービスへのサインインとアクセスを有効にする方法"
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

# v2.0 エンドポイントを使用してアプリケーションを登録する方法

MSA および Azure AD サインインの両方を受け付けるアプリを構築するには、最初に、アプリを Microsoft に登録する必要があります。現時点では、Azure AD または MSA で利用している既存のアプリは使用できません。新しいアプリを作成してください。

> [AZURE.NOTE]
	Azure Active Directory のシナリオおよび機能のすべてが v2.0 エンドポイントでサポートされているわけではありません。v2.0 エンドポイントを使用する必要があるかどうかを判断するには、[v2.0 の制限事項](active-directory-v2-limitations.md)に関するページをお読みください。

## Microsoft アプリ登録ポータルにアクセスする
まず、[https://apps.dev.microsoft.com](https://apps.dev.microsoft.com) に移動します。これは、マイクロソフトのアプリに関するあらゆることを管理できる新しいアプリ登録ポータルです。

Microsoft の個人または職場/学校アカウントのいずれかでサインインします。いずれのアカウントもをお持ちでない場合は、新しい個人アカウントを新規登録します。簡単に登録できますので、今すぐサインアップしましょう。

サインアップできましたか? おそらく空の Microsoft アプリの一覧が表示されているはずです。これを変更してみましょう。

**[アプリの追加]** をクリックし、名前を付けます。ポータルにより、アプリにグローバルに一意のアプリケーション ID が割り当てられます。これは、後ほどコードで使用します。アプリに API を呼び出すためのアクセス トークンが必要なサーバー側コンポーネントが含まれる場合 (Office、Azure、独自の Web API など)、**アプリケーションのシークレット**を作成することもできます。
<!-- TODO: Link for app secrets -->

次に、アプリで使用するプラットフォームを追加します。

- Web ベースのアプリの場合、サインイン メッセージを送信できる**リダイレクト URI** を指定します。
- モバイル アプリの場合、自動的に作成される既定のリダイレクト URI をメモしておきます。

必要に応じて、[プロファイル] セクションでサインイン ページの外観をカスタマイズできます。続行する前に、忘れずに **[保存]** をクリックします。

> [AZURE.NOTE] [https://apps.dev.microsoft.com](https://apps.dev.microsoft.com) を使用して作成したアプリケーションは、このポータルへのサインインに使用しているアカウントのホーム テナントに登録されます。つまり、個人の Microsoft アカウントを使用して Azure AD テナントにアプリケーションを登録することはできません。アプリケーションを特定のテナントに登録する場合は、そのテナントで作成したアカウントを使ってサインインしてください。

## クイック スタート アプリケーションを構築する
Microsoft アプリを構築したら、いずれかの v2.0 クイック スタート チュートリアルを実行します。以下に、推奨事項をいくつか示します。

[AZURE.INCLUDE [active-directory-v2-quickstart-table](../../includes/active-directory-v2-quickstart-table.md)]

<!---HONumber=AcomDC_0921_2016-->