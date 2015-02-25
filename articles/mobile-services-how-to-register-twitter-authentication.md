<properties pageTitle="Twitter 認証用の登録 - モバイル サービス" description="Azure Mobile Services アプリケーションで Twitter 認証を使用する方法について説明します。" services="mobile-services" documentationCenter="" authors="ggailey777" manager="dwrede" editor=""/>

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="11/21/2014" ms.author="glenga"/>

#モバイル サービスでの Twitter ログイン用のアプリケーションの登録

このトピックでは、Twitter を使用して Azure のモバイル サービスで認証できるようにアプリケーションを登録する方法について説明します。

>[AZURE.NOTE] このチュートリアルはあらゆるプラットフォームにおいて拡張性の高いモバイル アプリケーションを作成するソリューションの [Azure Mobile Services](http://azure.microsoft.com/ja-jp/services/mobile-services/) について説明します。モバイル サービスによって簡単にデータの同期化を行い、ユーザーを認証して、プッシュ通知を送信できます。このページはアプリケーションへユーザーをログインさせる方法を説明する「<a href="http://azure.microsoft.com/ja-jp/documentation/articles/mobile-services-ios-get-started-users/">認証の使用</a>」チュートリアルをサポートしています。Mobile Services を初めて使用する場合は、チュートリアル「<a href="http://azure.microsoft.com/ja-jp/documentation/articles/mobile-services-ios-get-started/">Mobile Services の使用</a>」を完了することをお勧めします。

このトピックの手順を完了するには、検証済みの電子メール アドレスを持つ Twitter アカウントが必要になります。新しい Twitter アカウントを作成するには、<a href="http://go.microsoft.com/fwlink/p/?LinkID=268287" target="_blank">twitter.com</a> にアクセスしてください。

1. <a href="http://go.microsoft.com/fwlink/p/?LinkId=268300" target="_blank">Twitter デベロッパー</a>の Web サイトに移動し、Twitter アカウント資格情報でサインインして、**[Create a new application]** をクリックします。

   	![][1]

2. アプリケーションの**名前**、**説明**、**Web サイト**を入力し、**[Callback URL]** にモバイル サービスの URL の末尾にパス _/login/twitter_ を付けて入力します。

	>[AZURE.NOTE]Visual Studio を使用して Azure に発行された .NET バックエンド モバイル サービスについては、リダイレクト URL は、ユーザーのモバイル サービスの URL の末尾に .NET サービスとしてパス _signin-twitter_ を付加したものです。例: <code>https://todolist.azure-mobile.net/signin-twitter</code>。

   	![][2]

3.  ページの下部に記載されている条項を読んで同意し、正しい CAPTCHA 文字列を入力して、**[Create your Twitter application]** をクリックします。 

   	![][3]

   	これでアプリケーションが登録され、アプリケーションの詳細が表示されます。

6. **[Consumer key]** と **[Consumer secret]** の値を書き留めます。 

   	![][4]

    > [AZURE.NOTE] コンシューマー シークレットは、重要なセキュリティ資格情報です。このシークレットは、他のユーザーと共有したり、アプリケーションと共に配布したりしないでください。

7. **[Settings]** タブをクリックして下方向へスクロールし、**[Allow this application to be used to sign in with Twitter]** チェック ボックスをオンにして、**[Update this Twitter application's settings]** をクリックします。

	![][5]

これで、コンシューマー キーとコンシューマー シークレットの値をモバイル サービスに渡すことにより、アプリケーションで Twitter ログインを認証に使用する準備ができました。

<!-- Anchors. -->

<!-- Images. -->
[1]: ./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-developers.png
[2]: ./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-register-app1.png
[3]: ./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-register-app2.png
[4]: ./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-app-details.png
[5]: ./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-register-settings.png

<!-- URLs. -->

[Twitter デベロッパー]: http://go.microsoft.com/fwlink/p/?LinkId=268300
[認証の使用]: /ja-jp/develop/mobile/tutorials/get-started-with-users-dotnet/

[Azure 管理ポータル]: https://manage.windowsazure.com/


<!--HONumber=42-->
