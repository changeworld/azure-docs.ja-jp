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
	ms.topic="get-started-article"
	ms.date="02/25/2016"
	ms.author="swkrish"/>


# Azure Active Directory B2C プレビュー: アプリケーションを登録する

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## 前提条件

コンシューマーのサインアップおよびサインインを受け付けるアプリケーションを作成するには、最初にアプリケーションを Azure Active Directory B2C テナントに登録する必要があります。「[Azure AD B2C テナントを作成する](active-directory-b2c-get-started.md)」に書かれている手順に従って独自のテナントを取得してください。この記事のすべての手順を実行したら、B2C 機能ブレードがスタート画面にピン留めされているはずです。

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## B2C 機能ブレードに移動する

Azure ポータルまたは Azure クラシック ポータルから B2C 機能ブレードに移動できます。

### 1.Azure ポータルからアクセスする

B2C 機能ブレードがスタート画面にピン留めされている場合、B2C テナントのグローバル管理者として [Azure ポータル](https://portal.azure.com/)にサインインするとすぐに、B2C 機能ブレードが表示されます。

また、**[参照]** をクリックし、[Azure ポータル](https://portal.azure.com/)の左側のナビゲーション ウィンドウで **[Azure AD B2C]** をクリックして、ブレードにアクセスすることもできます。

[https://portal.azure.com/{tenant}.onmicrosoft.com/?#blade/Microsoft\_AAD\_B2CAdmin/TenantManagementBlade/id/](https://portal.azure.com/{tenant}.onmicrosoft.com/?#blade/Microsoft_AAD_B2CAdmin/TenantManagementBlade/id/) に移動して、B2C 機能ブレードに直接アクセスすることもできます。**{tenant}** をテナント作成時に使用した名前 (例: contosob2c) に置き換えます。今後も使用できるように、このリンクをブックマークできます。

    > [AZURE.IMPORTANT]
    B2C 機能ブレードにアクセスするには、B2Cテナントのグローバル管理者である必要があります。他のテナントのグローバル管理者やテナントのユーザーはアクセスできません。

### 2.Azure クラシック ポータルからアクセスする

サブスクリプション管理者として [Azure クラシック ポータル](https://manage.windowsazure.com/)にサインインします (これは、Azure へのサインアップに使用したものと同じ職場/学校アカウント、または同じ Microsoft アカウントです)。 左側の Active Directory 拡張機能に移動し、B2C テナントをクリックします。**[クイック スタート]** タブ (表示される最初のタブ) で、**[管理]** の **[B2C 設定の管理]** をクリックします。新しいブラウザー ウィンドウまたはタブで、B2C 機能ブレードが開きます。

**[構成]** タブの **[B2C 管理]** セクションにも、**[B2C 設定の管理]** リンクがあります。

## アプリケーションを登録する

1. Azure ポータルの B2C 機能ブレードで、**[アプリケーション]** をクリックします。
2. ブレードの上部にある **[+追加]** をクリックします。
3. アプリケーションの **[名前]** には、コンシューマーがアプリケーションの機能を把握できるような名前を設定します。たとえば、「Contoso B2C app」などと入力します。
4. Web ベースのアプリケーションを作成している場合は、**[Web アプリ/Web API を含める]** スイッチを **[はい]** に切り替えます。**[応答 URL]** は、アプリケーションが要求したトークンを Azure AD B2C が返すエンドポイントです。たとえば、「`https://localhost:44321/`」のように入力します。セキュリティで保護する必要があるサーバー側コンポーネント (API) がアプリケーションに含まれる場合は、**[キーの生成]** ボタンをクリックして **[アプリケーションのシークレット]** も作成 (およびコピー) できます。

    > [AZURE.NOTE]
    **[アプリケーションのシークレット]** は、重要なセキュリティ資格情報です。

5. モバイル アプリケーションを作成している場合は、**[ネイティブ クライアントを含める]** スイッチを **[はい]** に切り替えます。自動的に作成される既定の **[リダイレクト URI]** をメモしておきます。
6. **[作成]** をクリックして、アプリケーションを登録します。
7. 作成したアプリケーションをクリックし、後でコードの作成時に使用するために、グローバルに一意の**アプリケーション ID** をメモしておきます。

## クイック スタート アプリケーションを構築する

アプリケーションを Azure AD B2C に登録したので、いずれかのクイック スタート チュートリアルを実行して使い始めることができます。以下に、推奨事項をいくつか示します。

[AZURE.INCLUDE [active-directory-v2-quickstart-table](../../includes/active-directory-b2c-quickstart-table.md)]

<!---HONumber=AcomDC_0302_2016-->