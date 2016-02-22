<properties
	pageTitle="App Services アプリケーションに Google 認証を構成する方法"
	description="App Services アプリケーションに Google 認証を構成する方法について説明します。"
    services="app-service\mobile"
	documentationCenter=""
	authors="mattchenderson"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="02/04/2016"
	ms.author="mahender"/>

# Google ログインを使用するように App Service アプリケーションを構成する方法

[AZURE.INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

このトピックでは、認証プロバイダーとして Google を使用するように Azure App Services を構成する方法を示します。

このトピックの手順を完了するには、検証済みの電子メール アドレスを持つ Google アカウントが必要になります。新しい Google アカウントを作成するには、[accounts.google.com](http://go.microsoft.com/fwlink/p/?LinkId=268302) にアクセスしてください。

> [AZURE.NOTE]
このトピックでは、App Service 認証/承認の使用例を示します。ほとんどのアプリケーションで、これが App Service ゲートウェイに取って代わっています。ゲートウェイを使用する場合との相違点は、トピック全体を通してメモに記載しています。


## <a name="register"> </a>Google にアプリケーションを登録する

1. [Azure ポータル]にログオンし、アプリケーションに移動します。**[URL]** をコピーします。この URI は、Google アプリの構成に使用します。

2. [Google apis](http://go.microsoft.com/fwlink/p/?LinkId=268303) Web サイトに移動して、Google アカウント資格情報でサインインします。**[プロジェクトの作成]** をクリックして**プロジェクト名**を入力し、**[作成]** をクリックします。

3. 左側のナビゲーション バーで、**[API & Auth]** をクリックした後、**[Social APIs]** の下の **[Google+ API]**、**[Enable API]** の順にクリックします。

4. **[API & Auth]**、**[Credentials]**、**[OAuth consent screen]** の順にクリックした後、自分の **電子メール アドレス** を選択し、**製品名** を入力し、**[Save]** をクリックします。

5. **[Credentials]** タブで、**[Add credentials]**、**[OAuth 2.0 client ID]** の順にクリックした後、**[Web application]** を選択します。

6. 前にコピーした App Service の **URL** を **[Authorized JavaScript Origins]** に貼り付け、前にコピーした **リダイレクト URI** を **[Authorized Redirect URI]** に貼り付けます。リダイレクト URI は、アプリケーションの URL にパス _/.auth/login/google/callback_ を追加したものです。たとえば、「`https://contoso.azurewebsites.net/.auth/login/google/callback`」のように入力します。HTTPS スキームを使用していることを確認します。**[Create]** をクリックします。


	> [AZURE.NOTE]
	App Service の認証/承認機能ではなく、App Service ゲートウェイを使用している場合、リダイレクト URL では、ゲートウェイ URL とパス _/signin-google_ を使用します。


7. 次の画面で、表示されているクライアント ID とクライアント シークレットの値を書き留めます。


    > [AZURE.IMPORTANT]
	クライアント シークレットは、重要なセキュリティ資格情報です。このシークレットを他のユーザーと共有したり、クライアント アプリケーション内で配信したりしないでください。


## <a name="secrets"> </a>Google の情報をアプリケーションに追加する

> [AZURE.NOTE]
App Service ゲートウェイを使用する場合は、このセクションの内容は無視し、ポータル内で目的のゲートウェイに移動します。**[設定]**、**[ID]**、**[Google]** を選択します。先に取得した値を貼り付けて、**[保存]** をクリックします。


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

<!---HONumber=AcomDC_0211_2016-->