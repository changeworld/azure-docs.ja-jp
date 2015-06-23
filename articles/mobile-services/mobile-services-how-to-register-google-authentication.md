<properties 
	pageTitle="Google 認証用の登録 - Mobile Services" 
	description="Google を使用して Azure Mobile Services で認証できるようにアプリケーションを登録する方法について説明します。" 
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
	ms.date="04/13/2015" 
	ms.author="glenga"/>

# モバイル サービスでの Google ログイン用のアプリケーションの登録

このトピックでは、Google を使用して Azure のモバイル サービスで認証できるようにアプリケーションを登録する方法について説明します。

>[AZURE.NOTE]このチュートリアルはあらゆるプラットフォームにおいて拡張性の高いモバイル アプリケーションを作成するソリューションの [Azure Mobile Services](http://azure.microsoft.com/services/mobile-services/) について説明します。モバイル サービスによって簡単にデータの同期化を行い、ユーザーを認証して、プッシュ通知を送信できます。このページはアプリケーションへユーザーをログインさせる方法を説明する「<a href="http://azure.microsoft.com/documentation/articles/mobile-services-ios-get-started-users/">認証の使用</a>」チュートリアルをサポートしています。モバイル サービスを初めて使用する場合は、チュートリアル「<a href="http://azure.microsoft.com/documentation/articles/mobile-services-ios-get-started/">モバイル サービスの使用</a>」を完了することをお勧めします。

このトピックの手順を完了するには、検証済みの電子メール アドレスを持つ Google アカウントが必要になります。新しい Google アカウントを作成するには、<a href="http://go.microsoft.com/fwlink/p/?LinkId=268302" target="_blank">accounts.google.com</a> にアクセスしてください。

1. <a href="http://go.microsoft.com/fwlink/p/?LinkId=268303" target="_blank">Google apis</a> Web サイトに移動して、Google アカウント資格情報でサインインします。**[プロジェクトの作成]** をクリックして**プロジェクト名**を入力し、**[作成]** をクリックします。

   	![][1]

2. **[同意画面]** をクリックし、自分の**メール アドレス**を選択し、**製品名**を入力して、**[保存]** をクリックします。

3. **[API & Auth]**、**[Credentials]**、**[Create new Client ID]** の順にクリックします。

   	![][2]

4. **[Web アプリケーション]** を選択して、**[Authorized JavaScript Origins]** にモバイル サービスの URL を入力し、**[Authorized Redirect URI]** で生成された URL をモバイル サービスの URL の末尾に _/login/google_ を付けたものと置き換えて、**[Create client ID]** をクリックします。

	>[AZURE.NOTE]Visual Studio を使用して Azure に発行された .NET バックエンド モバイル サービスについては、リダイレクト URL は、ユーザーのモバイル サービスの URL の末尾に .NET サービスとしてパス _signin-google_ を付加したものです。例:<code>https://todolist.azure-mobile.net/signin-google</code>

   	![][3]

5. **[Client ID for web applications]** で、**[クライアント ID]** と **[クライアント シークレット]** の値を書き留めます。

   	![][4]

    > [AZURE.IMPORTANT]クライアント シークレットは、重要なセキュリティ資格情報です。このシークレットは、他のユーザーと共有したり、アプリケーションと共に配布したりしないでください。

これで、クライアント ID とクライアント シークレットの値をモバイル サービスに渡すことにより、Google ログインを使用してアプリケーションで認証を使用する準備ができました。

<!-- Anchors. -->

<!-- Images. -->
[1]: ./media/mobile-services-how-to-register-google-authentication/mobile-services-google-new-project.png
[2]: ./media/mobile-services-how-to-register-google-authentication/mobile-services-google-create-client.png
[3]: ./media/mobile-services-how-to-register-google-authentication/mobile-services-google-create-client2.png
[4]: ./media/mobile-services-how-to-register-google-authentication/mobile-services-google-create-client3.png
[5]: ./media/mobile-services-how-to-register-google-authentication/mobile-services-google-app-details.png

<!-- URLs. -->

[Google apis]: http://go.microsoft.com/fwlink/p/?LinkId=268303
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-dotnet/

[Azure Management Portal]: https://manage.windowsazure.com/

<!--HONumber=54--> 