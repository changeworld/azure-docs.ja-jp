<properties
	pageTitle="Azure Active Directory B2C プレビュー: Azure Active Directory B2C テナントの作成 | Microsoft Azure"
	description="Azure Active Directory B2C テナントの作成方法に関するトピック"
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
	ms.date="01/21/2016"
	ms.author="swkrish"/>

# Azure Active Directory B2C プレビュー: Azure AD B2C テナントを作成する方法

Azure Active Directory (AD) B2C を使い始めるには、以下に示す 3 つの手順を実行します。

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## 手順 1: Azure サブスクリプションにサインアップする

Azure サブスクリプションが既にある場合は、次の手順に進みます。ない場合は、[Azure サブスクリプション](sign-up-organization.md)にサインアップし、Azure AD B2C にアクセスできるようにします。

> [AZURE.NOTE]
現在、Azure AD B2C プレビューは無料で使用できますが、制限があります (テナントあたり最大 50,000 ユーザー)。[Azure クラシック ポータル](http://manage.windowsazure.com/)にアクセスするには、Azure サブスクリプションが必要です。

## 手順 2: Azure AD B2C テナントを作成する

次の手順に従って、新しい Azure AD B2C テナントを作成します。現在、既存のディレクトリで B2C の機能を有効にすることはできません。

1. サブスクリプション管理者として [Azure クラシック ポータル](https://manage.windowsazure.com/)にサインインします。これは、Azure へのサインアップに使用したものと同じ職場または学校アカウント、または同じ Microsoft アカウントです。
2. **[新規]**、**[App Services]**、**[Active Directory]**、**[ディレクトリ]**、**[カスタム作成]** の順にクリックします。

    ![テナントの作成](./media/active-directory-b2c-get-started/new-directory.png)

3. テナントの**名前**、**ドメイン名**、**国またはリージョン**を選択します。
4. **[これは B2C ディレクトリです]** オプションをオンにします。
5. チェック マークをクリックして設定を完了します。

    ![B2C テナントの作成](./media/active-directory-b2c-get-started/create-b2c-directory.png)

6. テナントが作成され、Active Directory 拡張機能に表示されます。また、現在のユーザーがテナントのグローバル管理者になります。必要に応じて、他のグローバル管理者を追加できます。

    > [AZURE.IMPORTANT]
    テナントの作成には最大で 2 分かかることがあります。テナントの作成中に問題が発生した場合は、この[記事](active-directory-b2c-support-create-directory.md)を参考にしてください。

## 手順 3: Azure ポータルで B2C 機能ブレードに移動する

1. 左側のナビゲーション バーで Active Directory 拡張機能に移動します。
2. **[ディレクトリ]** タブで、作成した作成したテナントを見つけてクリックします。
3. **[構成]** タブをクリックします。
4. **[B2C 管理]** セクションの **[B2C 設定の管理]** リンクをクリックします。

    ![B2C テナントの作成](./media/active-directory-b2c-get-started/b2c-directory-configure-tab.png)

4. 新しいブラウザー タブまたはウィンドウで、B2C 機能ブレードが含まれた Azure ポータルが開きます。

    > [AZURE.IMPORTANT]
    (テナント数が少ない場合に) このページが正しく読み込まれないという既知の問題があります。この問題は、ブラウザーを更新することで解決されます。解決されない場合は、サポートにご連絡ください。

5. アクセスしやすいように、このブレードをスタート画面にピン留めします (右上隅)。

    ![B2C 機能ブレード](./media/active-directory-b2c-get-started/b2c-features-blade.png)

    > [AZURE.NOTE]
    [Azure クラシック ポータル](https://manage.windowsazure.com/)では、テナントのユーザーとグループ、セルフサービスによるパスワードのリセット構成、会社のブランド化機能を管理できます。

## 次のステップ

[Azure AD B2C へのアプリケーションの登録とクイック スタート アプリケーションの作成](active-directory-b2c-app-registration.md)に進みます。

<!---HONumber=AcomDC_0128_2016-->