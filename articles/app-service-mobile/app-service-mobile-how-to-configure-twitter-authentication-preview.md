<properties
	pageTitle="App Services アプリケーションに Twitter 認証を構成する方法"
	description="App Services アプリケーションに Twitter 認証を構成する方法について説明します。"
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
	ms.date="07/27/2015"
	ms.author="mahender"/>

# Twitter ログインを使用するようにアプリケーションを構成する方法

[AZURE.INCLUDE [app-service-mobile-note-mobile-services-preview](../../includes/app-service-mobile-note-mobile-services-preview.md)]

このトピックでは、認証プロバイダーとして Twitter を使用するように Azure モバイル アプリを構成する方法を示します。

このトピックの手順を完了するには、検証済みの電子メール アドレスを持つ Twitter アカウントが必要になります。新しい Twitter アカウントを作成するには、<a href="http://go.microsoft.com/fwlink/p/?LinkID=268287" target="_blank">twitter.com</a> にアクセスしてください。

## <a name="register"> </a>Twitter にアプリケーションを登録する


1. [Azure の管理ポータル]にログオンし、モバイル アプリに移動します。**[URL]** をコピーします。この URL は、Twitter アプリの構成で使用します。

2. **[設定]**、**[ユーザー認証]**、**[Twitter]** の順にクリックします。**[コールバック URL]** をコピーします。この URL は、Twitter アプリの構成で使用します。

3. [Twitter Developers] の Web サイトに移動し、Twitter アカウント資格情報でサインインして、**[Create New App]** をクリックします。

4. 新しいアプリの **[名前]** と **[説明]** を入力します。**[Web サイト]** 値に**モバイル アプリの URL** を貼り付けます。次に、**[コールバック URL]** に先ほどコピーした **[コールバック URL]** を貼り付けます。この URL は、モバイル アプリ ゲートウェイの後にパス _/signin-twitter_ を追加したフォーマットです。たとえば、「`https://contosogateway.azurewebsites.net/signin-twitter`」のように入力します。HTTPS スキームを使用していることを確認します。

    ![][0]

3.  ページの下部で、条項を読み、同意します。**[Create your Twitter application]** をクリックします。これでアプリケーションが登録され、アプリケーションの詳細が表示されます。

4. **[Settings]** タブをクリックし、**[Allow this application to be used to sign in with Twitter]** チェック ボックスをオンにして、**[Update Settings]** をクリックします。

5. **[Keys and Access Tokens]** タブをクリックします。**[Consumer Key (API Key)]** と **[Consumer secret (API Secret)]** の値を書き留めます。

    > [AZURE.NOTE]コンシューマー シークレットは、重要なセキュリティ資格情報です。このシークレットは、他のユーザーと共有したり、アプリケーションと共に配布したりしないでください。


## <a name="secrets"> </a>モバイル アプリに Twitter 情報を追加する

1. [Azure の管理ポータル]に戻り、モバイル アプリの [twitter 設定] ブレードに、以前に入手した API キーと API サーバーの値を貼り付けます。その後、**[保存]** をクリックします。

    ![][1]

これで、アプリケーションで認証に Twitter を使用する準備ができました。

## <a name="related-content"> </a>関連コンテンツ

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]



<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-twitter-authentication-preview/app-service-twitter-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-twitter-authentication-preview/mobile-app-twitter-settings.png

<!-- URLs. -->

[Twitter Developers]: http://go.microsoft.com/fwlink/p/?LinkId=268300
[Azure の管理ポータル]: https://portal.azure.com/
[xamarin]: ../app-services-mobile-app-dotnet-backend-xamarin-ios-get-started-users-preview.md
 

<!---HONumber=Oct15_HO3-->