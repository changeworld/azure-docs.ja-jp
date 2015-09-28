<properties
	pageTitle="Azure Active Directory B2C プレビュー: ページの UI カスタマイズ ヘルパー ツール | Microsoft Azure"
	description="Azure Active Directory B2C のページの UI カスタマイズ機能を試すために使用するヘルパー ツール"
	services="active-directory-b2c"
	documentationCenter=""
	authors="swkrish"
	manager="msmbaldwin"
	editor="curtand"/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/15/2015"
	ms.author="swkrish"/>

# Azure Active Directory B2C プレビュー: ページのユーザー インターフェイス (UI) カスタマイズ機能を試すために使用するヘルパー ツール

この記事は、Azure Active Directory (AD) B2C の、ページのユーザー インターフェイス (UI) カスタマイズ機能についての[メイン記事](active-directory-b2c-reference-ui-customization)に関連する記事です。

次の手順に従うことで、サンプル コンテンツを使用して、ページの UI カスタマイズ機能を実行することができます。サンプル コンテンツは、デモンストレーションのみの使用を目的として、**静的** HTML5 で作成されています。

1. [Azure BLOB ストレージ](http://azure.microsoft.com/documentation/services/storage/)にサンプル コンテンツをアップロードし、HTTPS 経由でパブリック アクセスができるようにしてから、その URL で CORS (クロス オリジン リソース共有) を有効にします。
2. 既存のサインアップ ポリシーでページの UI カスタマイズ設定を変更し、上の URL を指定します。

作業開始前の準備:

- [ここに](active-directory-b2c-overview.md)一覧表示された Azure AD B2C のクイック スタート アプリケーションのいずれかを構築します。その過程で、ここで変更を加えるサインアップ ポリシーを作成します。
- ヘルパー ツールを[ここ](https://github.com/AzureADSamples/B2C-AzureBlobStorage-Client)からダウンロードします。

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

### 手順 1. 適切な場所にサンプル コンテンツをアップロードする

1. [Azure プレビュー ポータル](https://portal.azure.com/)にサインインします。
2. **[+ 新規]**、**[データ + ストレージ]**、**[ストレージ アカウント]** の順にクリックします。Azure BLOB ストレージ アカウントを作成するには、Azure サブスクリプションが必要です。[ここ](https://azure.microsoft.com/ja-JP/pricing/free-trial/)で無料試用版にサインアップできます。
3. ストレージ アカウントの**名前**を指定し ("contoso.core.windows.net" など)、適切な**価格レベル**、**リソース グループ**、および**サブスクリプション**を選択します。**[スタート画面にピン留めする]** がオンになっていることを確認します。**[作成]** をクリックします。
4. スタート画面に戻り、作成したストレージ アカウントをクリックします。
5. **[概要]** セクションで、**[コンテナー]**、**[+ 追加]** の順にクリックします。
6. コンテナーの**名前** ("b2c" など) を指定し、**アクセス タイプ**として **[BLOB]** を選択します。**[OK]** をクリックします。
7. 作成したコンテナーは、**[BLOB]** ブレードに一覧表示されます。コンテナーの URL (`https://contoso.blob.core.windows.net/b2c` のような形式) をメモしておきます。**[BLOB]** ブレードを閉じます。
8. [ストレージ アカウント] のブレードで、**[キー]** をクリックし、**[ストレージ アカウント名]** および **[プライマリ アクセス キー]** フィールドの値をメモしておきます。

    > [AZURE.NOTE]**プライマリ アクセス キー**は、重要なセキュリティ資格情報です。

9. ヘルパー ツールを実行し、前の手順で書き留めた**ストレージ アカウント名**と**プライマリ アクセス キー**を指定します。これにより、ストレージ アカウントにサンプル コンテンツがアップロードされます。
10. ブラウザーで `https://contoso.blob.core.windows.net/b2c/idp.html` にアクセスし、コンテンツが正常にアップロードされていることを確認します。コンテンツで CORS が有効であることを確認するために、[http://test-cors.org/](http://test-cors.org/) を使用することもできます (結果で `XHR status: 200` を確認します)。

### 手順 2. サインアップ ポリシーで、ページの UI カスタマイズ オプションを変更する

1. [Azure プレビュー ポータル](https://portal.azure.com)で、ディレクトリにサインインし、[B2C 機能] ブレードに移動します。
2. **[サインアップ ポリシー]** をクリックし、作成したサインアップ ポリシー ("B2C\_1\_SiIn" など) をクリックします。
3. **[ページの UI のカスタマイズ]**、**[ID プロバイダーの選択ページ]**の順にクリックします。
4. **[カスタム テンプレートを使用する]** を **[はい]** に切り替えます。**[カスタム ページの URI]** フィールドで、ストレージ アカウントにアップロードされたコンテンツの適切な URL を入力します。たとえば、「`https://contoso.blob.core.windows.net/b2c/idp.html`」のように入力します。**[OK]** をクリックします。
5. **[ローカル アカウントのサインアップ ページ]** をクリックします。**[カスタム テンプレートを使用する]** を **[はい]** に切り替えます。**[カスタム ページの URI]** フィールドで、ストレージ アカウントにアップロードされたコンテンツの適切な URL を入力します。たとえば、「`https://contoso.blob.core.windows.net/b2c/su.html`」のように入力します。**[OK]** を 2 回クリックします。
6. **[保存]** をクリックします。
7. ブレード上部にある **[今すぐ実行]** をクリックします。**[アプリケーション]** ボックスで、[B2C アプリ] を選択し、**[応答 URL/リダイレクト URI]** ボックスで `https://localhost:44321/` を選択します。**[今すぐ実行]** ボタンをクリックします。
8. 新しいブラウザー タブが開き、アプリケーションへのサインアップについて、新しいコンテンツが所定の場所に置かれた状態のユーザー エクスペリエンスを確認できます。

> [AZURE.NOTE]ポリシーの変更が適用されるまでに、最大で 1 分間かかります。

<!---HONumber=Sept15_HO3-->