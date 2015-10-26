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
	ms.date="08/28/2015"
	ms.author="mahender"/>

# Google ログインを使用するようにアプリケーションを構成する方法

[AZURE.INCLUDE [app-service-mobile-note-mobile-services-preview](../../includes/app-service-mobile-note-mobile-services-preview.md)]

このトピックでは、認証プロバイダーとして Google を使用するように Mobile Apps を構成する方法を示します。

このトピックの手順を完了するには、検証済みの電子メール アドレスを持つ Google アカウントが必要になります。新しい Google アカウントを作成するには、<a href="http://go.microsoft.com/fwlink/p/?LinkId=268302" target="_blank">accounts.google.com</a> にアクセスしてください。

1. [Azure の管理ポータル]にログオンし、モバイル アプリに移動します。**[URL]** をコピーします。この URL は、後で Google アプリで使用します。
 
2. **[設定]**、**[ユーザー認証]**、**[Google]** の順にクリックします。**[リダイレクト URI]** をコピーします。この URI は、Google アプリの構成に使用します。

3. [Google apis](http://go.microsoft.com/fwlink/p/?LinkId=268303) Web サイトに移動して、Google アカウント資格情報でサインインします。**[プロジェクトの作成]** をクリックして**プロジェクト名**を入力し、**[作成]** をクリックします。

4. 左側のナビゲーション バーで、**[API & Auth]** をクリックした後、**[Social APIs]** の下の **[Google+ API]**、**[Enable API]** の順にクリックします。

5. **[API & Auth]**、**[Credentials]**、**[OAuth consent screen]** の順にクリックした後、自分の **電子メール アドレス** を選択し、**製品名** を入力し、**[Save]** をクリックします。

6. **[Credentials]** タブで、**[Add credentials]**、**[OAuth 2.0 client ID]** の順にクリックした後、**[Web application]** を選択します。

7. 前にコピーした Mobile Apps の **URL** を **[Authorized JavaScript Origins]** に貼り付け、前にコピーした **リダイレクト URI** を **[Authorized Redirect URI]** に貼り付けます。リダイレクト URI は、後ろにパス _/signin-google_ が追加された Mobile App ゲートウェイです。たとえば、「`https://contosogateway.azurewebsites.net/signin-google`」のように入力します。HTTPS スキームを使用していることを確認します。**[Create]** をクリックします。

8. 次の画面で、表示されているクライアント ID とクライアント シークレットの値を書き留めます。

    > [AZURE.IMPORTANT]クライアント シークレットは、重要なセキュリティ資格情報です。このシークレットを他のユーザーと共有したり、クライアント アプリケーション内で配信したりしないでください。

9. [Microsoft Azure 管理ポータル]に戻り、Mobile App の [Google 設定] ブレードに、取得したクライアント ID とクライアント シークレットの値を貼り付けます。その後、**[保存]** をクリックします。

     ![][1]

これで、アプリケーションで認証に Google を使用する準備ができました。

## <a name="related-content"> </a>関連コンテンツ

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]


<!-- Anchors. -->

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-google-authentication-preview/mobile-app-google-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-google-authentication-preview/mobile-app-google-settings.png

<!-- URLs. -->

[Google apis]: http://go.microsoft.com/fwlink/p/?LinkId=268303

[Azure の管理ポータル]: https://portal.azure.com/
[Microsoft Azure 管理ポータル]: https://portal.azure.com/
 

<!---HONumber=Oct15_HO3-->