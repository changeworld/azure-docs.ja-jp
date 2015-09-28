<properties
	pageTitle="Azure Active Directory B2C プレビュー: Azure Active Directory B2C ディレクトリの作成 | Microsoft Azure"
	description="Azure Active Directory B2C ディレクトリの作成方法についてのトピック"
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

# Azure Active Directory B2C プレビュー: Azure AD B2C ディレクトリを作成する方法

Azure Active Directory (AD) B2C を使い始めるには、以下に示す 3 つの手順を実行します。

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## 手順 1: Azure サブスクリプションにサインアップする

Azure サブスクリプションが既にある場合は、次の手順に進みます。ない場合は、[Azure サブスクリプション](sign-up-organization.md)にサインアップし、Azure AD B2C にアクセスできるようにします。

> [AZURE.NOTE]現在、Azure AD B2C プレビューは無料で使用できますが制限があります (ディレクトリあたり最大 50,000 ユーザー)。[Azure ポータル](http://manage.windowsazure.com/)にアクセスするには、Azure サブスクリプションが必要です。

## 手順 2: Azure AD B2C ディレクトリを作成する

新しい Azure AD B2C ディレクトリは次の手順で作成します。現在、既存のディレクトリで B2C の機能を有効にすることはできません。

1. サブスクリプション管理者として [Azure ポータル](https://manage.windowsazure.com/)にサインインします。これは、Azure へのサインアップに使用したものと同じ職場または学校アカウント、または同じ Microsoft アカウントです。
2. **[新規]**、**[App Services]**、**[Active Directory]**、**[ディレクトリ]**、**[カスタム作成]** の順にクリックします。

    ![ディレクトリを作成する](./media/active-directory-b2c-get-started/new-directory.png)

3. ディレクトリの **[名前]**、**[ドメイン名]**、**[国/地域]** を選択します。
4. **[これは B2C ディレクトリです]** オプションをオンにします。
5. チェック マークをクリックして設定を完了します。

    ![B2C ディレクトリを作成する](./media/active-directory-b2c-get-started/create-b2c-directory.png)

6. ディレクトリが作成され、Active Directory 拡張機能に表示されます。また、現在のユーザーがディレクトリのグローバル管理者になります。必要に応じて、他のグローバル管理者を追加できます。

    > [AZURE.IMPORTANT]ディレクトリの作成には最大で 2 分かかります。ディレクトリの作成中に問題が発生した場合は、この[記事](active-directory-b2c-support-create-directory.md)を参考にしてください。

## 手順 3: Azure プレビュー ポータルで B2C 機能ブレードに移動する

1. 左側のナビゲーション バーで Active Directory 拡張機能に移動します。
2. **[ディレクトリ]** タブで、作成したディレクトリをクリックします。
3. **[構成]** タブをクリックします。
4. **[B2C 管理]** セクションの **[B2C 設定の管理]** リンクをクリックします。

    ![B2C ディレクトリを作成する](./media/active-directory-b2c-get-started/b2c-directory-configure-tab.png)

4. 新しいブラウザー タブまたはウィンドウで、B2C 機能ブレードを含む [Azure プレビュー ポータル](https://portal.azure.com/)が開きます。
5. アクセスしやすいように、このブレードをスタート画面にピン留めします (右上隅)。

    ![B2C 機能ブレード](./media/active-directory-b2c-get-started/b2c-features-blade.png)

    > [AZURE.NOTE][Azure ポータル](https://manage.windowsazure.com/)では、ディレクトリのユーザーとグループ、セルフ サービスのパスワードのリセット構成、会社のブランド機能を管理できます。

## 次のステップ

[Azure AD B2C へのアプリケーションの登録とクイック スタート アプリケーションの作成](active-directory-b2c-app-registration.md)に進みます。

<!---HONumber=Sept15_HO3-->