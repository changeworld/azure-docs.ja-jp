<properties
	pageTitle="App Services アプリケーションに Google 認証を構成する方法"
	description="App Services アプリケーションに Google 認証を構成する方法について説明します。"
    services="app-service"
	documentationCenter=""
	authors="mattchenderson"
	manager="erikre"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="05/04/2016"
	ms.author="mahender"/>

# Google ログインを使用するように App Service アプリケーションを構成する方法

[AZURE.INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

このトピックでは、認証プロバイダーとして Google を使用するように Azure App Services を構成する方法を示します。

このトピックの手順を完了するには、検証済みの電子メール アドレスを持つ Google アカウントが必要になります。新しい Google アカウントを作成するには、[accounts.google.com](http://go.microsoft.com/fwlink/p/?LinkId=268302) にアクセスしてください。

## <a name="register"> </a>Google にアプリケーションを登録する

1. [Azure ポータル]にログオンし、アプリケーションに移動します。**[URL]** をコピーします。この URI は、Google アプリの構成に使用します。

2. [Google apis](http://go.microsoft.com/fwlink/p/?LinkId=268303) Web サイトに移動して、Google アカウント資格情報でサインインします。**[プロジェクトの作成]** をクリックして**プロジェクト名**を入力し、**[作成]** をクリックします。

3. **[Social APIs]** で、**[Google+ API]**、**[Enable]** の順にクリックします。

4. 左側のナビゲーションで、**[Credentials]**、**[OAuth consent screen]** の順にクリックした後、自分の**電子メール アドレス**を選択し、**製品名**を入力して、**[Save]** をクリックします。

5. **[Credentials]** タブで、**[Create credentials]**、**[OAuth client ID]** の順にクリックし、**[Web application]** を選択します。

6. 前にコピーした App Service の **URL** を **[Authorized JavaScript Origins]** に、リダイレクト URI を **[Authorized Redirect URI]** に貼り付けます。リダイレクト URI は、アプリケーションの URL にパス _/.auth/login/google/callback_ を追加したものです。たとえば、「`https://contoso.azurewebsites.net/.auth/login/google/callback`」のように入力します。HTTPS スキームを使用していることを確認します。**[Create]** をクリックします。

7. 次の画面で、表示されているクライアント ID とクライアント シークレットの値を書き留めます。


    > [AZURE.IMPORTANT]
	クライアント シークレットは、重要なセキュリティ資格情報です。このシークレットを他のユーザーと共有したり、クライアント アプリケーション内で配信したりしないでください。


## <a name="secrets"> </a>Google の情報をアプリケーションに追加する

8. [Azure ポータル]に戻り、アプリケーションに移動します。**[設定]**、**[認証/承認]** の順にクリックします。

9. [認証/承認] 機能が有効になっていない場合は、スイッチを **[オン]** に切り替えます。

10. **[Google]** をクリックします。前の手順で取得した App ID と App Secret の値を貼り付けます。アプリケーションで必要なスコープを有効にします (省略可能)。次に、 **[OK]** をクリックします

    ![][1]

	App Service は既定では認証を行いますが、サイトのコンテンツと API へのアクセス承認については制限を設けていません。アプリケーション コードでユーザーを承認する必要があります。

17. (省略可能) Google によって認証されたユーザーしかサイトにアクセスできないように制限するには、**[要求が認証されていないときに実行するアクション]** を **[Google]** に設定します。この場合、要求はすべて認証される必要があり、認証されていない要求はすべて認証のために Google にリダイレクトされます。

12. **[保存]** をクリックします。

これで、アプリケーションで認証に Google を使用する準備ができました。

## <a name="related-content"> </a>関連コンテンツ

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]


<!-- Anchors. -->

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-settings.png

<!-- URLs. -->

[Google apis]: http://go.microsoft.com/fwlink/p/?LinkId=268303

[Azure ポータル]: https://portal.azure.com/

<!---HONumber=AcomDC_0511_2016-->