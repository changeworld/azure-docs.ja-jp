<properties
	pageTitle="Azure Active Directory B2C プレビュー: Microsoft アカウントの構成 | Microsoft Azure"
	description="Azure Active Directory B2C によってセキュリティ保護されたアプリケーションで、Microsoft アカウントを使用するコンシューマーにサインアップとサインインを提供します。"
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
	ms.date="06/27/2016"
	ms.author="swkrish"/>

# Azure Active Directory B2C プレビュー: Microsoft アカウントでコンシューマーにサインアップおよびサインインを提供する

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## Microsoft アカウント アプリケーションを作成する

Azure Active Directory (Azure AD) B2C で ID プロバイダーとして Microsoft アカウントを使用するには、Microsoft アカウント アプリケーションを作成し、適切なパラメーターを提供する必要があります。そのためには Microsoft アカウントが必要です。アカウントがない場合は、[https://www.live.com/](https://www.live.com/) で取得できます。

1. [Microsoft アカウント デベロッパー センター](https://account.live.com/developers/applications)に移動し、Microsoft アカウントの資格情報でサインインします。
2. **[アプリケーションの作成]** をクリックします。

    ![Microsoft アカウント - 新しいアプリケーションの追加](./media/active-directory-b2c-setup-msa-app/msa-add-new-app.png)

3. **アプリケーション名**を入力し、**[同意する]** をクリックします。Microsoft サービスの利用規約に同意する必要があります。

    ![Microsoft アカウント - アプリケーション名](./media/active-directory-b2c-setup-msa-app/msa-app-name.png)

4. 左側のナビゲーションで **[API 設定]** をクリックします。有効な **[Contact Email]** を入力します。

    ![Microsoft アカウント - API 設定](./media/active-directory-b2c-setup-msa-app/msa-api-settings.png)

5. **[リダイレクト URL]** フィールドに、「`https://login.microsoftonline.com/te/{tenant}/oauth2/authresp`」と入力します。**{tenant}** は、実際のテナントの名前 (例: contosob2c.onmicrosoft.com) に置き換えます。ページの下部にある **[保存]** をクリックします。

    ![Microsoft アカウント - リダイレクト URL](./media/active-directory-b2c-setup-msa-app/msa-redirect-url.png)

6. 左側のナビゲーションで **[アプリケーション設定]** をクリックします。**[クライアント ID]** と **[クライアント シークレット]** の値をコピーします。テナントで ID プロバイダーとして Microsoft アカウントを構成するには、両方の値が必要です。**[Client secret]** は、重要なセキュリティ資格情報です。

    ![Microsoft アカウント - クライアント シークレット](./media/active-directory-b2c-setup-msa-app/msa-client-secret.png)

## テナントで ID プロバイダーとして Microsoft アカウントを構成する

1. この手順に従って、Azure ポータルで [B2C 機能ブレードに移動](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade)します。
2. B2C 機能ブレードで、**[ID プロバイダー]** をクリックします。
3. ブレードの上部にある **[+追加]** をクリックします。
4. ID プロバイダー構成のわかりやすい **[名前]** を指定します。たとえば、「MSA」と入力します。
5. **[ID プロバイダーの種類]** をクリックし、**[Microsoft アカウント]** を選択して、**[OK]** をクリックします。
6. **[この ID プロバイダーを設定する]** をクリックし、前に作成した Microsoft アカウント アプリケーションのクライアント ID とクライアント シークレットを入力します。
7. **[OK]** をクリックし、**[作成]** をクリックして Microsoft アカウントの構成を保存します。

<!---HONumber=AcomDC_0629_2016-->