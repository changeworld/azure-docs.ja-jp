<properties
	pageTitle="Azure Active Directory B2C プレビュー: Facebook の構成 | Microsoft Azure"
	description="Azure Active Directory B2C によってセキュリティ保護されたアプリケーションで、Facebook アカウントを使用するコンシューマーにサインアップとサインインを提供します。"
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
	ms.date="01/12/2016"
	ms.author="swkrish"/>

# Azure Active Directory B2C プレビュー: Facebook アカウントでコンシューマーにサインアップおよびサインインを提供する

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## Facebook アプリケーションを作成する

Azure Active Directory (Azure AD) B2C で ID プロバイダーとして Facebook を使用するには、Facebook アプリケーションを作成し、適切なパラメーターを提供する必要があります。そのためには Facebook アカウントが必要です。ない場合は、[https://www.facebook.com/](https://www.facebook.com/) で取得できます。

1. [Facebook for developers](https://developers.facebook.com/) の Web サイトに移動し、Facebook アカウントの資格情報でサインインします。
2. まだ登録していない場合は、Facebook 開発者として登録する必要があります。そのためには、ページの右上隅にある **[Register]** をクリックし、Facebook のポリシーに同意して登録手順を完了します。
3. **[My Apps]** をクリックし、**[Add a New App]** をクリックします。プラットフォームとして **[Website]** を選択し、**[Skip and Create App ID]** をクリックします。

    ![Facebook - 新規アプリの追加](./media/active-directory-b2c-setup-fb-app/fb-add-new-app.png)

    ![Facebook - 新規アプリの追加 - ウェブサイト](./media/active-directory-b2c-setup-fb-app/fb-add-new-app-website.png)

    ![Facebook - アプリ ID の作成](./media/active-directory-b2c-setup-fb-app/fb-new-app-skip.png)

4. フォームで、**[Display Name]** を指定し、適切な **[Category]** を選択して、**[Create App ID]** をクリックします。Facebook プラットフォームのポリシーを受け入れ、オンライン セキュリティ チェックを完了する必要があります。

    ![Facebook - 新しいアプリ ID の作成](./media/active-directory-b2c-setup-fb-app/fb-create-app-id.png)

5. 左側のナビゲーションで **[Settings]** をクリックします。有効な **[Contact Email]** を入力します。
6. **[+Add Platform]** をクリックし、**[Website]** を選択します。

    ![Facebook - 設定](./media/active-directory-b2c-setup-fb-app/fb-settings.png)

    ![Facebook - 設定 - ウェブサイト](./media/active-directory-b2c-setup-fb-app/fb-website.png)

7. **[Site URL]** フィールドに「[https://login.microsoftonline.com/](https://login.microsoftonline.com/)」と入力し、**[Save Changes]** をクリックします。
8. **[App ID]** の値をコピーします。**[Show]** をクリックし、**[App Secret]** の値をコピーします。テナントで ID プロバイダーとして Facebook を構成するには、両方の値が必要です。**[App Secret]** は、重要なセキュリティ資格情報です。

    ![Facebook - サイトの URL](./media/active-directory-b2c-setup-fb-app/fb-site-url.png)

9. 上部の **[Advanced]** タブをクリックし、**[Security]** セクションの **[Valid OAuth redirect URIs]** フィールドに「`https://login.microsoftonline.com/te/{tenant}/oauth2/authresp`」と入力します。**{tenant}** は、実際のテナントの名前 (例: contosob2c.onmicrosoft.com) に置き換えます。ページの下部にある **[Save Changes]** をクリックします。

    ![Facebook - OAuth リダイレクト URI](./media/active-directory-b2c-setup-fb-app/fb-oauth-redirect-uri.png)

10. Facebook アプリケーションを Azure AD B2C で使用できるようにするには、アプリケーションをパブリックに利用できるようにする必要があります。そのためには、左側のナビゲーションで **[Status & Review]** をクリックし、レビューのためにアプリケーションを申請します(**[Start a Submission]** をクリックします)。 アプリケーションが Facebook によって承認されたら、ページの上部にあるスイッチを **[YES]** に切り替え、**[Confirm]** をクリックすることでアプリケーションを公開できます。

    ![Facebook - アプリの申請](./media/active-directory-b2c-setup-fb-app/fb-app-submission.png)

    ![Facebook - アプリの公開](./media/active-directory-b2c-setup-fb-app/fb-app-public.png)

## テナントで ID プロバイダーとして Facebook を構成する

1. この手順に従って、Azure ポータルで [B2C 機能ブレードに移動](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade)します。
2. B2C 機能ブレードで、**[ID プロバイダー]** をクリックします。
3. ブレードの上部にある **[+追加]** をクリックします。
4. ID プロバイダー構成のわかりやすい **[名前]** を指定します。たとえば、「FB」などと入力します。
5. **[ID プロバイダーの種類]** をクリックし、**[Facebook]** を選択して、**[OK]** をクリックします。
6. **[この ID プロバイダーを設定する]** をクリックし、先に作成した Facebook アプリケーションのアプリ ID とアプリ シークレットを **[クライアント ID]** および **[クライアント シークレット]** フィールドに入力します。
7. **[OK]** をクリックし、**[作成]** をクリックして Facebook の構成を保存します。

<!---HONumber=AcomDC_0224_2016-->