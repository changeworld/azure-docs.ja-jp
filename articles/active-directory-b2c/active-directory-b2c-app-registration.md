<properties
	pageTitle="Azure Active Directory B2C プレビュー: アプリケーションの登録 | Microsoft Azure"
	description="Azure Active Directory B2C にアプリケーションを登録する方法"
	services="active-directory-b2c"
	documentationCenter=""
	authors="swkrish"
	manager="mbaldwin"
	editor="bryanla"/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/15/2015"
	ms.author="swkrish"/>

# Azure Active Directory B2C プレビュー: アプリケーションを登録する方法

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## 前提条件

コンシューマーのサインアップおよびサインインを受け付けるアプリケーションを作成するには、最初にアプリケーションを Azure Active Directory B2C ディレクトリに登録する必要があります。[ここ](active-directory-b2c-get-started.md)で説明されている手順を使用して、ディレクトリを取得します。この記事のすべての手順を実行した場合、B2C 機能ブレードがスタート画面にピン留めされているはずです。

## B2C 機能ブレードに移動する

次の 2 つの方法のどちらかで、B2C 機能ブレードに移動できます。

### 1\.Azure プレビュー ポータルで直接アクセスする

B2C 機能ブレードをスタート画面にピン留めしてある場合、B2C ディレクトリのグローバル管理者として [Azure プレビュー ポータル](https://portal.azure.com/)にサインインするとすぐに、B2C 機能ブレードが表示されます。

[https://portal.azure.com/{directory}.onmicrosoft.com/?Microsoft\_AAD\_B2CAdmin=true#blade/Microsoft\_AAD\_B2CAdmin/TenantManagementBlade/id/{directory}.onmicrosoft.com](https://portal.azure.com/{directory}.onmicrosoft.com/?Microsoft_AAD_B2CAdmin=true#blade/Microsoft_AAD_B2CAdmin/TenantManagementBlade/id/{directory}.onmicrosoft.com) に移動して B2C 機能ブレードに直接アクセスすることもできます。**{directory}** はディレクトリ作成時に使用した名前に置き換え (例: contosob2c)、B2C ディレクトリのグローバル管理者としてサインインします。後で参照するときのために、このリンクをブックマークできます。

### 2\.Azure ポータルからアクセスする

サブスクリプション管理者として [Azure ポータル](https://manage.windowsazure.com/)にサインインします (これは、Azure へのサインアップに使用したものと同じ職場または学校アカウント、または同じ Microsoft アカウントです)。左側の [Active Directory] 拡張機能に移動し、B2C ディレクトリをクリックします。**[クイック スタート]** タブ (表示される最初のタブ) で、**[管理]** の **[B2C 設定の管理]** をクリックします。新しいブラウザー ウィンドウまたはタブで、B2C 機能ブレードが開きます。

**[構成]** タブの **[B2C 管理]** セクションで **[B2C 設定の管理]** を検索することもできます。

## アプリケーションを登録する

1. Azure プレビュー ポータルの B2C 機能ブレードで、**[アプリケーション]** をクリックします。
2. ブレードの上部にある **[+追加]** をクリックします。
3. アプリケーションの **[名前]** には、コンシューマーがアプリケーションの機能を把握できるような名前を設定します。たとえば、"Contoso B2C app" などと入力します。
4. Web ベースのアプリケーションを作成している場合は、**[Web アプリ/Web API を含める]** スイッチを **[はい]** に切り替えます。**[応答 URL]** は、アプリケーションが要求したトークンを Azure AD B2C が返すエンドポイントです。たとえば、「`https://localhost:44321/`」などと入力します。セキュリティ保護の必要なサーバー側コンポーネント (API) がアプリケーションに含まれる場合は、**[キーの生成]** ボタンをクリックして **[アプリケーションのシークレット]** も作成 (およびコピー) できます。

    > [AZURE.NOTE]**[アプリケーションのシークレット]** は、重要なセキュリティ資格情報です。

5. モバイル アプリケーションを作成している場合は、**[ネイティブ クライアントを含める]** スイッチを **[はい]** に切り替えます。自動的に作成される既定の **[リダイレクト URI]** をコピーします。
6. **[作成]** をクリックして、アプリケーションを登録します。
7. 作成したアプリケーションをクリックし、後でコードの作成時に使用するグローバルに一意な **[アプリケーション ID]** をコピーします。

## クイック スタート アプリケーションを構築する

アプリケーションを Azure AD B2C に登録したので、いずれかのクイック スタート チュートリアルを実行して使い始めることができます。以下に、推奨事項をいくつか示します。

[AZURE.INCLUDE [active-directory-v2-quickstart-table](../../includes/active-directory-b2c-quickstart-table.md)]

<!---HONumber=Sept15_HO3-->