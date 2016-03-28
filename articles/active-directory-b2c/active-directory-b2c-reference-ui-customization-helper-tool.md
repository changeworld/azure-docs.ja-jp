<properties
	pageTitle="Azure Active Directory B2C プレビュー: ページの UI カスタマイズ ヘルパー ツール | Microsoft Azure"
	description="Azure Active Directory B2C のページの UI カスタマイズ機能を試すために使用するヘルパー ツール"
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
	ms.topic="article"
	ms.date="03/15/2016"
	ms.author="swkrish"/>

# Azure Active Directory B2C プレビュー: ページのユーザー インターフェイス (UI) カスタマイズ機能を試すために使用するヘルパー ツール

この記事は、Azure Active Directory (Azure AD) B2C での [UI のカスタマイズに関するメインの記事](active-directory-b2c-reference-ui-customization.md)に付随するものです。以下の手順では、用意されているサンプルの HTML および CSS の内容を使用して、ページの UI カスタマイズ機能を試してみる方法について説明します。

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## Azure AD B2C テナントを取得する

Azure AD B2C テナントがまだない場合は、カスタマイズを始める前に、[Azure AD B2C テナントを取得](active-directory-b2c-get-started.md)する必要があります。

## サインアップ ポリシーを作成する

用意されているサンプルの内容では、[サインアップ ポリシー](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy)の 2 つのページ、[ID プロバイダー選択ページ](active-directory-b2c-reference-ui-customization.md#identity-provider-selection-page)と[ローカル アカウント サインアップ ページ](active-directory-b2c-reference-ui-customization.md#local-account-sign-up-page)をカスタマイズします。[サインアップ ポリシーを作成する](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy)ときは、ローカル アカウント (電子メール アドレス)、Facebook、Google+ を **ID プロバイダー**として追加します。サンプルの HTML コンテンツが受け付ける IDP はこれらだけです。

## アプリケーションを登録する

B2C テナントに、ポリシーを実行するために使用できる[アプリケーションを登録する](active-directory-b2c-app-registration.md)必要があります。アプリケーションを登録した後、サインアップ ポリシーを実際に実行するにはいくつかのオプションを使用できます。

- 「[アプリケーションにコンシューマーをサインアップおよびサインインする](active-directory-b2c-overview.md#getting-started)」の「使用の開始」セクションで示されている Azure AD B2C クイック スタート アプリケーションのいずれかをビルドします。
- ビルド済みの [Azure AD B2C プレイグラウンド](https://aadb2cplayground.azurewebsites.net) アプリケーションを使用します。プレイグラウンドを使用する場合は、**リダイレクト URI** `https://aadb2cplayground.azurewebsites.net/` を使用して B2C テナントにアプリケーションを登録する必要があります。
- [Azure ポータル](https://portal.azure.com/)で、ポリシーの **[今すぐ実行]** ボタンを使用します。

## ポリシーをカスタマイズする

ポリシーの外観と操作性をカスタマイズするには、最初に Azure AD B2C の固有の規則を使用して HTML および CSS ファイルを作成する必要があります。その後は、静的なコンテンツを、Azure AD B2C がアクセスできる公開されている場所にアップロードできます。そのような場所としては、独自の専用 Web サーバー、Azure BLOB ストレージ、Azure Content Delivery Network、その他の静的リソース ホスティング プロバイダーを使用できます。唯一の要件は、コンテンツが HTTPS 経由で使用でき、CORS を使用してアクセスできることです。静的コンテンツを Web で公開した後は、この場所を参照してユーザーにそのコンテンツを表示するようにポリシーを編集できます。Azure AD B2Cカスタマイズ機能の動作の詳細については、[UI のカスタマイズに関するメインの記事](active-directory-b2c-reference-ui-customization.md)を参照してください。

このチュートリアルでは、既にいくつかのサンプル コンテンツを作成し、Azure BLOB ストレージでホストしてあります。サンプル コンテンツは、架空の会社 "Contoso B2C" のテーマでの非常に基本的なカスタマイズです。独自のポリシーで試してみるには、次の手順を実行します。

1. [Azure ポータル](https://portal.azure.com/)でテナントにサインインし、B2C 機能ブレードに移動します。
2. **[サインアップ ポリシー]** をクリックし、作成したサインアップ ポリシー ("b2c\_1\_sign\_up" など) をクリックします。
3. **[ページの UI のカスタマイズ]**、**[ID プロバイダーの選択ページ]** の順にクリックします。
4. **[カスタム テンプレートを使用する]** を **[はい]** に切り替えます。**[カスタム ページの URI]** フィールドに、「`https://contosob2c.blob.core.windows.net/static/Index.html`」と入力します。**[OK]** をクリックします。
5. **[ローカル アカウントのサインアップ ページ]** をクリックします。**[カスタム テンプレートを使用する]** を **[はい]** に切り替えます。**[カスタム ページの URI]** フィールドに、「`https://contosob2c.blob.core.windows.net/static/EmailVerification.html`」と入力します。**[OK]** を 2 回クリックして、UI カスタマイズ ブレードを閉じます。
6. **[保存]** をクリックします。

カスタマイズしたポリシーを試すことができるようになります。独自のアプリケーションまたは Azure AD B2C プレイグラウンドを使用することもできますが、単にポリシー ブレードで **[今すぐ実行]** コマンドをクリックするのでもかまいません。ドロップダウン ボックスでアプリケーションを選び、適切なリダイレクト URI を選びます。**[今すぐ実行]** ボタンをクリックします。新しいブラウザー タブが開き、アプリケーションへのサインアップについて、新しいコンテンツが所定の場所に置かれた状態のユーザー エクスペリエンスを確認できます。

## サンプル コンテンツを Azure BLOB ストレージにアップロードする

Azure BLOB ストレージを使用してページのコンテンツをホストする場合は、独自のストレージ アカウントを作成し、B2C ヘルパー ツールを使用してファイルをアップロードできます。

### ストレージ アカウントの作成

1. [Azure ポータル](https://portal.azure.com/)にサインインします。
2. **[+ 新規]**、**[データ + ストレージ]**、**[ストレージ アカウント]** の順にクリックします。Azure BLOB ストレージ アカウントを作成するには、Azure サブスクリプションが必要です。[Azure Web サイト](https://azure.microsoft.com/pricing/free-trial/)で無料試用版にサインアップできます。
3. ストレージ アカウントの **[名前]** を指定し ("contoso" など)、適切な **[価格レベル]**、**[リソース グループ]**、**[サブスクリプション]** を選択します。**[スタート画面にピン留めする]** がオンになっていることを確認します。**[作成]** をクリックします。
4. スタート画面に戻り、作成したストレージ アカウントをクリックします。
5. **[概要]** セクションで、**[コンテナー]**、**[+ 追加]** の順にクリックします。
6. コンテナーの **[名前]** ("b2c" など) を指定し、**[アクセスの種類]** として **[BLOB]** を選択します。**[OK]** をクリックします。
7. 作成したコンテナーは、**[BLOB]** ブレードに一覧表示されます。コンテナーの URL (`https://contoso.blob.core.windows.net/b2c` のような形式) をメモしておきます。**[BLOB]** ブレードを閉じます。
8. [ストレージ アカウント] のブレードで、**[キー]** をクリックし、**[ストレージ アカウント名]** および **[プライマリ アクセス キー]** フィールドの値をメモしておきます。

> [AZURE.NOTE]
	**プライマリ アクセス キー**は、重要なセキュリティ資格情報です。

### ヘルパー ツールとサンプル ファイルをダウンロードする

[Azure BLOB ストレージ ヘルパー ツールとサンプル ファイルを .zip ファイルとして](https://github.com/azureadquickstarts/b2c-azureblobstorage-client/archive/master.zip)ダウンロードできます。また、次のように GitHub から複製することもできます。

```
git clone https://github.com/azureadquickstarts/b2c-azureblobstorage-client
```

このリポジトリには `sample_templates\contoso` ディレクトリが含まれており、そこには HTML、CSS、イメージの例が含まれています。これらのテンプレートで独自の Azure BLOB ストレージ アカウントを参照するには、HTML ファイルを編集する必要があります。`Index.htnml` および `EmailValidation.html` を開き、`https://localhost` のすべてのインスタンスを、前の手順でメモした独自のコンテナーの URL に置き換えます。この場合、HTML は Azure AD によってドメイン `https://login.microsoftonline.com` で提供されるので、HTML ファイルの絶対パスを使用する必要があります。

### サンプル ファイルをダウンロードする

同じリポジトリで、`B2CAzureStorageClient.zip` を解凍し、その中にある `B2CAzureStorageClient.exe` ファイルを実行します。このプログラムは単に、指定されたディレクトリ内のすべてのファイルをストレージ アカウントにアップロードし、それらのファイルに対する CORS アクセスを有効にします。上記の手順を実行した場合、HTML および CSS ファイルはストレージ アカウントを指すようになります。ストレージ アカウントの名前は、`blob.core.windows.net` の前の部分 (`contoso` など) です。ブラウザーで `https://{storage-account-name}.blob.core.windows.net/{container-name}/Index.html` にアクセスしてみることで、コンテンツが正常にアップロードされていることを確認できます。コンテンツで CORS が有効であることを確認するために、[http://test-cors.org/](http://test-cors.org/) を使用することもできます。(結果で "XHR status: 200" を探します。)

### 再びポリシーをカスタマイズする

独自のストレージ アカウントにサンプル コンテンツをアップロードしたので、それを参照するようにサインアップ ポリシーを編集する必要があります。前述の「[ポリシーをカスタマイズする](#customize-your-policy)」の手順を繰り返します。ただし、今回は独自のストレージ アカウントの URL を使用します。たとえば、`Index.html` ファイルの場所は `<url-of-your-container>/Index.html` になります。

これで、**[今すぐ実行]** ボタンまたは独自のアプリケーションを使用して、ポリシーを再度実行できるようになります。どちらの場合も同じサンプルの HTML と CSS を使用したので、結果はほぼ同じになるはずです。ただし今度は、ポリシーは Azure BLOB ストレージの独自のインスタンスを参照しているので、自由にファイルを編集して再びアップロードできます。HTML および CSS のカスタマイズの詳細については、[UI のカスタマイズに関するメインの記事](active-directory-b2c-reference-ui-customization.md)を参照してください。

<!---HONumber=AcomDC_0316_2016-->