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

# Google ログインを使用するように App Service アプリケーションを構成する方法

[AZURE.INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]&nbsp;

[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

このトピックでは、認証プロバイダーとして Google を使用するように Azure App Services を構成する方法を示します。

このトピックの手順を完了するには、検証済みの電子メール アドレスを持つ Google アカウントが必要になります。新しい Google アカウントを作成するには、<a href="http://go.microsoft.com/fwlink/p/?LinkId=268302" target="_blank">accounts.google.com</a> にアクセスしてください。


	> [AZURE.NOTE]
	This topic demonstrates use of the App Service Authentication / Authorization feature. This replaces the App Service gateway for most applications. Differences that apply to using the gateway are called out in notes throughout the topic.


## <a name="register"> </a>Google にアプリケーションを登録する

1. [Microsoft Azure 管理ポータル]にログオンし、目的のアプリケーションに移動します。**[URL]** をコピーします。この URI は、Google アプリの構成に使用します。
 
2. [Google apis](http://go.microsoft.com/fwlink/p/?LinkId=268303) Web サイトに移動して、Google アカウント資格情報でサインインします。**[プロジェクトの作成]** をクリックして**プロジェクト名**を入力し、**[作成]** をクリックします。

3. 左側のナビゲーション バーで、**[API & Auth]** をクリックした後、**[Social APIs]** の下の **[Google+ API]**、**[Enable API]** の順にクリックします。

4. **[API & Auth]**、**[Credentials]**、**[OAuth consent screen]** の順にクリックした後、自分の **電子メール アドレス** を選択し、**製品名** を入力し、**[Save]** をクリックします。

5. **[Credentials]** タブで、**[Add credentials]**、**[OAuth 2.0 client ID]** の順にクリックした後、**[Web application]** を選択します。

6. 前にコピーした App Service の **URL** を **[Authorized JavaScript Origins]** に貼り付け、前にコピーした **リダイレクト URI** を **[Authorized Redirect URI]** に貼り付けます。リダイレクト URI は、アプリケーションの URL にパス _/.auth/login/google/callback_ を追加したものです。たとえば、「`https://contoso.azurewebsites.net/.auth/login/google/callback`」のように入力します。HTTPS スキームを使用していることを確認します。**[Create]** をクリックします。


	> [AZURE.NOTE]App Service の認証/承認機能ではなく、App Service ゲートウェイを使用している場合、リダイレクト URL では、ゲートウェイ URL とパス _/signin-google_ を使用します。


7. 次の画面で、表示されているクライアント ID とクライアント シークレットの値を書き留めます。


    > [AZURE.IMPORTANT]クライアント シークレットは、重要なセキュリティ資格情報です。このシークレットを他のユーザーと共有したり、クライアント アプリケーション内で配信したりしないでください。


## <a name="secrets"> </a>Google の情報をアプリケーションに追加する


	> [AZURE.NOTE]
	If using the App Service Gateway, ignore this section and instead navigate to your gateway in the portal. Select **Settings**, **Identity**, and then **Google**. Paste in the values you obtained earlier and click **Save**.


8. [Microsoft Azure 管理ポータル]に戻り、目的のアプリケーションに移動します。**[設定]**、**[認証/承認]** の順にクリックします。

9. [認証/承認] 機能が有効になっていない場合は、スイッチを **[オン]** に切り替えます。

10. **[Google]** をクリックします。前の手順で取得した App ID と App Secret の値を貼り付けます。アプリケーションで必要なスコープを有効にします (省略可能)。次に、 **[OK]** をクリックします

    ![][1]
	
11. 既定では、App Service はログインを提供しますが、サイトのコンテンツと API へのアクセスは制限されません。これはアプリケーション コードで行います。Google ログインからサイトを完全に保護する場合は、**[要求が認証されていないときに実行するアクション]** ドロップダウンを **[Google]** オプションを使用するように変更します。これにより、すべての要求で認証が必要になり、認証されていない要求は、Google を使用してログインするようにリダイレクトされます。

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

[Microsoft Azure 管理ポータル]: https://portal.azure.com/
 

<!---HONumber=Nov15_HO4-->