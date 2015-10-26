<properties 
	pageTitle="Twitter 認証用の登録 | Microsoft Azure" 
	description="Azure Mobile Services アプリケーションで Twitter 認証を使用する方法について説明します。" 
	services="mobile-services" 
	documentationCenter="" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="08/08/2015" 
	ms.author="glenga"/>

#モバイル サービスでの Twitter ログイン用のアプリケーションの登録

[AZURE.INCLUDE [mobile-services-selector-register-identity-provider](../../includes/mobile-services-selector-register-identity-provider.md)]

このトピックでは、Twitter を使用して Azure のモバイル サービスで認証できるようにアプリケーションを登録する方法について説明します。

>[AZURE.NOTE] このチュートリアルはあらゆるプラットフォームにおいて拡張性の高いモバイル アプリケーションを作成するソリューションの [Azure Mobile Services](http://azure.microsoft.com/services/mobile-services/) について説明します。Mobile Services によって簡単にデータの同期化を行い、ユーザーを認証して、プッシュ通知を送信できます。このページはアプリケーションへユーザーをログインさせる方法を説明する「<a href="http://azure.microsoft.com/documentation/articles/mobile-services-ios-get-started-users/">認証の使用</a>」チュートリアルをサポートしています。モバイル サービスを初めて使用する場合は、チュートリアル「<a href="http://azure.microsoft.com/documentation/articles/mobile-services-ios-get-started/">モバイル サービスの使用</a>」を完了することをお勧めします。

このトピックの手順を完了するには、検証済みの電子メール アドレスを持つ Twitter アカウントが必要になります。新しい Twitter アカウントを作成するには、<a href="http://go.microsoft.com/fwlink/p/?LinkID=268287" target="_blank">twitter.com</a> にアクセスしてください。

1. <a href="http://go.microsoft.com/fwlink/p/?LinkId=268300" target="_blank">Twitter デベロッパー</a>の Web サイトに移動し、Twitter アカウント資格情報でサインインして、**[Create a new application]** をクリックします。

   	![][1]

2. アプリケーションの**名前**、**説明**、**Web サイト**を入力し、_[Callback URL]_ にモバイル サービスの URL の末尾にパス **/login/twitter** を付けて入力します。

	>[AZURE.NOTE] Visual Studio を使用して Azure に発行された .NET バックエンド モバイル サービスについては、リダイレクト URL は、ユーザーのモバイル サービスの URL の末尾にパス _signin-twitter_ を追加したものです。この例では、モバイル サービスにはコールバック URL ```https://todolist.azure-mobile.net/signin-twitter``` が含まれます。

   	![][2]

3.  ページの下部に記載されている条項を読んで同意し、**[Create your Twitter application] ** をクリックします。

   	これでアプリケーションが登録され、アプリケーションの詳細が表示されます。

6. アプリケーションのダッシュ ボードで **[キーとアクセス トークン] ** タブをクリックし、**[Cコンシューマー キー]** と **[コンシューマー シークレット]** の値をメモします。

    > [AZURE.NOTE] コンシューマー シークレットは、重要なセキュリティ資格情報です。このシークレットは、他のユーザーと共有したり、アプリケーションと共に配布したりしないでください。

7. **[設定]** タブをクリックして下方向へスクロールし、**[このアプリケーションを使用して Twitter でログインする許可します]** チェック ボックスをオンにして、**[更新設定]** をクリックします。

これで、コンシューマー キーとコンシューマー シークレットの値をモバイル サービスに渡すことにより、アプリケーションで Twitter ログインを認証に使用する準備ができました。

<!-- Anchors. -->

<!-- Images. -->
[1]: ./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-developers.png
[2]: ./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-register-app1.png

<!-- URLs. -->

[Twitter Developers]: http://go.microsoft.com/fwlink/p/?LinkId=268300
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-dotnet/

[Azure Management Portal]: https://manage.windowsazure.com/
 

<!---HONumber=Oct15_HO3-->