<properties 
	pageTitle="App Services アプリケーションに Twitter 認証を構成する方法"
	description="App Services アプリケーションに Twitter 認証を構成する方法について説明します。" 
	services="app-service\mobile" 
	documentationCenter="" 
	authors="mattchenderson,ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="app-service-mobile" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="02/19/2015" 
	ms.author="mahender"/>

# Twitter ログインを使用するようにアプリケーションを構成する方法

このトピックでは、認証プロバイダーとして Twitter を使用するように Azure App Services を構成する方法を示します。

このトピックの手順を完了するには、検証済みの電子メール アドレスを持つ Twitter アカウントが必要になります。新しい Twitter アカウントを作成するには、<a href="http://go.microsoft.com/fwlink/p/?LinkID=268287" target="_blank">twitter.com</a> にアクセスしてください。

## <a name="register"> </a>Twitter にアプリケーションを登録する

1. [Twitter Developers] の Web サイトに移動し、Twitter アカウント資格情報でサインインして、**[Create New App]** をクリックします。

2. アプリの**名前**、**説明**、および **Web サイト**の値を入力します。次に、**[Callback URL]** に、パス _/signin-twitter_ が末尾に追加されたゲートウェイの URL を入力します。たとえば、「`https://contosogateway.azurewebsites.net/signin-twitter`」のように入力します。HTTPS スキームを使用していることを確認します。

    ![][0]

3.  ページの下部で、条項を読み、同意します。**[Create your Twitter application]** をクリックします。これでアプリケーションが登録され、アプリケーションの詳細が表示されます。

4. **[Settings]** タブをクリックし、**[Allow this application to be used to sign in with Twitter]** チェック ボックスをオンにして、**[Update Settings]** をクリックします。

5. **[Keys and Access Tokens]** タブをクリックします。**[Consumer Key (API Key)]** と **[Consumer secret (API Secret)]** の値を書き留めます。

    > [AZURE.NOTE]コンシューマー シークレットは、重要なセキュリティ資格情報です。このシークレットは、他のユーザーと共有したり、アプリケーションと共に配布したりしないでください。


## <a name="secrets"> </a>モバイル アプリに Twitter 情報を追加する

6. [Azure の管理ポータル]にログオンし、App Service ゲートウェイに移動します。

7. **[設定]** で、**[ID]**、**[Twitter]** の順に選択します。以前に入手したアプリ ID とアプリ シークレットの値を貼り付けます。その後、**[保存]** をクリックします。

    ![][1]

これで、アプリケーションで認証に Twitter を使用する準備ができました。

## <a name="related-content"> </a>関連コンテンツ

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../includes/app-service-mobile-related-content-get-started-users.md)]



<!-- Images. -->

[0]: ./media/app-service-how-to-configure-twitter-authentication/app-service-twitter-redirect.png
[1]: ./media/app-service-how-to-configure-twitter-authentication/app-service-twitter-settings.png

<!-- URLs. -->

[Twitter Developers]: http://go.microsoft.com/fwlink/p/?LinkId=268300
[Azure の管理ポータル]: https://portal.azure.com/
[xamarin]: app-services-mobile-app-dotnet-backend-xamarin-ios-get-started-users-preview.md

<!--HONumber=54-->