<properties
pageTitle="Azure Active Directory プレビューでサインイン ページに言語固有の会社のブランドを追加する | Microsoft Azure"
description="Azure サインイン ページに言語固有の会社のブランドの画像とテキストを追加する方法について説明します。"
services="active-directory"
documentationCenter=""
authors="curtand"
manager="femila"
editor=""/>

<tags
ms.service="active-directory"
ms.workload="identity"
ms.tgt_pltfrm="na"
ms.devlang="na"
ms.topic="article"
ms.date="09/12/2016"
ms.author="curtand"/>

# Azure Active Directory プレビューでサインイン ページに言語固有の会社のブランドを追加する

多くの企業は、管理下にある Web サイトとサービスに関して利用者が戸惑わないよう、そのすべての外観に統一感を持たせたいと考えています。Azure Active Directory プレビューでは、会社のロゴや独自の配色でサインイン ページの外観をカスタマイズできるようにすることでこれを実現します。プレビューの機能については、[こちらの記事](active-directory-preview-explainer.md)をご覧ください。 サインイン ページは、Office 365 など、Azure AD を ID プロバイダーとして使用する Web ベースのアプリケーションにサインインするときに表示されるページです。このページを使用して資格情報を入力します。

## 別の言語のサインイン ページのカスタマイズ

カスタム サインイン ページに言語固有の要素を追加できるのは、[サインイン ページへの会社のブランドの追加](active-directory-branding-custom-signon-azure-portal.md)に関する記事の説明に従って、カスタム サインイン ページを既に作成している場合に限られます。カスタマイズ可能な要素の既定のセットを使用して、ディレクトリごとに 1 つのサインイン ページを構成できます。ページ要素の既定のセットを構成したら、さまざまなロケール用に追加のバージョンを構成できます。さまざまな要素を混在させたり、一致させたりすることもできます。たとえば、次のことが可能です。

- すべてのカルチャに対応する既定の**サインイン ページの画像**を作成してから、英語とフランス語固有のバージョンを作成します。ブラウザーをこの 2 つの言語のいずれかに設定すると、その言語固有の画像が表示されます。それ以外の言語ではすべて既定の画像が表示されます。

- 組織のさまざまなロゴ (日本語やヘブライ語のバージョンなど) を構成します。

メンテナンスとパフォーマンス上の理由から、言語のバリエーションは少数にしておくことをお勧めします。

**ディレクトリに会社のブランドを追加するには**

1.  ディレクトリの全体管理者であるアカウントで [Azure ポータル](https://portal.azure.com)にサインインします。

2.  **[その他のサービス]** を選択し、テキスト ボックスに「**ユーザーとグループ**」と入力して、**Enter** キーを押します。

    ![ユーザー管理を開く](./media/active-directory-branding-localize-azure-portal/user-management.png)

3. **[ユーザーとグループ]** ブレードで、**[Company branding (会社のブランド)]** を選択します。

4. **[Users and groups - Company branding (ユーザーとグループ - 会社のブランド)]** ブレードで、**[言語の追加]** をクリックします。

    ![言語固有のブランド要素を追加する](./media/active-directory-branding-localize-azure-portal/add-language.png)

5. カスタマイズする要素に変更を加えます。すべての要素は省略できます。

6. [**Save**] をクリックします。

サインイン ページのブランドに加えた変更が表示されるまでに、最大 1 時間かかる場合があります。

## 次のステップ

[サインイン ページに会社のブランドを追加する](active-directory-branding-custom-signon-azure-portal.md)

<!---HONumber=AcomDC_0914_2016-->