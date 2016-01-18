<properties
	pageTitle="Azure Active Directory B2C プレビュー: Amazon の構成 | Microsoft Azure"
	description="Azure Active Directory B2C によってセキュリティ保護されたアプリケーションで、LinkedIn アカウントを使用するコンシューマーにサインアップとサインインを提供します"
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
	ms.date="01/06/2016"
	ms.author="swkrish"/>

# Azure Active Directory B2C プレビュー: Amazon アカウントでコンシューマーにサインアップおよびサインインを提供する

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## Amazon アプリケーションを作成する

Azure Active Directory (AD) B2C で ID プロバイダーとして Amazon を使用するには、最初に Amazon アプリケーションを作成し、適切なパラメーターを提供する必要があります。そのためには Amazon アカウントが必要です。ない場合は、[http://www.amazon.com/](http://www.amazon.com/) で取得できます。

1. [Amazon Developers Center](https://login.amazon.com/) に移動し、Amazon アカウントの資格情報でサインインします。
2. まだ行っていない場合は、**[Sign Up]** をクリックして、開発者登録手順に従い、ポリシーを受け入れます。
3. **[Register new application]** をクリックします。

    ![Amazon - 新しいアプリ](./media/active-directory-b2c-setup-amzn-app/amzn-new-app.png)

4. アプリケーションの情報 (**[Name]**、**[Description]**、**[Privacy Notice URL]**) を提供し、**[Save]** をクリックします。

    ![Amazon - アプリの登録](./media/active-directory-b2c-setup-amzn-app/amzn-register-app.png)

5. **[Web Settings]** セクションで、**[Client ID]** および **[Client secret]** の値をコピーします (これを表示するには **[Show Secret]** ボタンをクリックする必要があります)。テナントで ID プロバイダーとして Amazon を構成するには、両方の値が必要です。セクションの下部にある **[Edit]** をクリックします。

> [AZURE.NOTE]**[Client secret]** は、重要なセキュリティ資格情報です。

    ![Amazon - Client secret](./media/active-directory-b2c-setup-amzn-app/amzn-client-secret.png)

6. **[Allowed JavaScript origins]** フィールドに「[https://login.microsoftonline.com](https://login.microsoftonline.com)」と入力し、**[Allowed Return URLs]** フィールドに「`https://login.microsoftonline.com/te/{tenant}/oauth2/authresp`」と入力します。**{tenant}** は、実際のテナントの名前 (例: contoso.onmicrosoft.com) に置き換えます。**[保存]** をクリックします。

> [AZURE.NOTE]**{tenant}** の値は大文字小文字が区別されます。

    ![Amazon - URLs](./media/active-directory-b2c-setup-amzn-app/amzn-urls.png)

## テナントで ID プロバイダーとして Amazon を構成する

1. [この手順に従って、Azure ポータルで B2C 機能ブレードに移動します。](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade)
2. B2C 機能ブレードで、**[ID プロバイダー]** をクリックします。
3. ブレードの上部にある **[+追加]** をクリックします。
4. ID プロバイダー構成のわかりやすい **[名前]** を指定します。たとえば、「Amzn」などと入力します。
5. **[ID プロバイダーの種類]** をクリックし、**[Amazon]** を選択して、**[OK]** をクリックします。
6. **[この ID プロバイダーを設定する]** をクリックし、先に作成した Amazon アプリケーションの **[クライアント ID]** と **[クライアント シークレット]** を入力します。
7. **[OK]** をクリックし、**[作成]** をクリックして Amazon の構成を保存します。

<!---HONumber=AcomDC_0107_2016-->