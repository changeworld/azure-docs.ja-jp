<properties
	pageTitle="Azure Active Directory B2C プレビュー: Google+ の構成 | Microsoft Azure"
	description="Azure Active Directory B2C によってセキュリティ保護されたアプリケーションで、Google+ アカウントを使用するコンシューマーにサインアップとサインインを提供します"
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

# Azure Active Directory B2C プレビュー: Google+ アカウントでコンシューマーにサインアップおよびサインインを提供する

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## Google+ アプリケーションを作成する

Azure Active Directory (AD) B2C で ID プロバイダーとして Google+ を使用するには、最初に Google+ アプリケーションを作成し、適切なパラメーターを提供する必要があります。そのためには Google+ アカウントが必要です。ない場合は、[https://accounts.google.com/SignUp](https://accounts.google.com/SignUp) で取得できます。

1. [Google Developers Console](https://console.developers.google.com/) に移動し、Google+ アカウントの資格情報でサインインします。
2. **[Create Project]** をクリックし、**[Project name]** を入力し、サービス規約に同意して、**[Create]** をクリックします。

    ![G+ - 作業開始](./media/active-directory-b2c-setup-goog-app/google-get-started.png)

    ![G+ - 新しいプロジェクト](./media/active-directory-b2c-setup-goog-app/google-new-project.png)

3. 左側のナビゲーションで、**[APIs & Auth]** をクリックし、**[Credentials]** をクリックします。
4. 上部の **[OAuth consent screen]** タブをクリックします。

    ![G+ - 資格情報](./media/active-directory-b2c-setup-goog-app/google-add-cred.png)

5. 有効な **[Email address]** を選択または指定し、**[Product name]** を指定して、**[Save]** をクリックします。

    ![G+ - OAuth 同意画面](./media/active-directory-b2c-setup-goog-app/google-consent-screen.png)

6. **[Application type]** で **[Web application]** を選択します。
7. アプリケーションの **[Name]** を指定し、**[Authorized redirect URIs]** フィールドには「[https://login.microsoftonline.com](https://login.microsoftonline.com)」と入力し、**[Authorized redirect URIs]** フィールドには「[https://login.microsoftonline.com/te/{directory}/oauth2/authresp](https://login.microsoftonline.com/te/{directory}/oauth2/authresp)」と入力します。**{directory}** は、実際のディレクトリの名前に置き換えます (例: contosob2c.onmicrosoft.com)。**[作成]** をクリックします。

    > [AZURE.NOTE] **{directory}** の値は大文字小文字が区別されます。

    ![G+ - クライアント ID の作成](./media/active-directory-b2c-setup-goog-app/google-create-client-id.png)

8. **[Client ID]** および **[Client secret]** の値をコピーします。ディレクトリで ID プロバイダーとして Google+ を構成するには、両方の値が必要です。

    > [AZURE.NOTE]**[Client secret]** は、重要なセキュリティ資格情報です。

    ![G+ - クライアント シークレット](./media/active-directory-b2c-setup-goog-app/google-client-secret.png)

## ディレクトリで ID プロバイダーとして Google+ を構成する

1. [Azure プレビュー ポータルで B2C 機能ブレードに移動します](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade)。
2. B2C 機能ブレードで、**[ソーシャル ID プロバイダー]** をクリックします。
3. ブレードの上部にある **[+追加]** をクリックします。
4. ID プロバイダー構成のわかりやすい **[名前]** を指定します。たとえば、「G+」などと入力します。
5. **[ID プロバイダーの種類]** をクリックし、**[Google]** を選択して、**[OK]** をクリックします。
6. **[この ID プロバイダーを設定する]** をクリックし、先に作成した Google+ アプリケーションの **[クライアント ID]** と **[クライアント シークレット]** を入力します。
7. **[OK]** をクリックし、**[作成]** をクリックして Google+ の構成を保存します。

<!---HONumber=Sept15_HO3-->