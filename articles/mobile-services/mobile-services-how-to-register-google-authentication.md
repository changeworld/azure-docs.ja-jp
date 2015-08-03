<properties 
	pageTitle="Google 認証用の登録 - Mobile Services" 
	description="Google を使用して Azure Mobile Services で認証できるようにアプリケーションを登録する方法について説明します。" 
	services="mobile-services" 
	documentationCenter="android" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-android" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="06/11/2015" 
	ms.author="glenga"/>

# Mobile Services での Google ログイン用のアプリケーションの登録

[AZURE.INCLUDE [mobile-services-selector-register-identity-provider](../../includes/mobile-services-selector-register-identity-provider.md)]

このトピックでは、Google を使用して Azure Mobile Services で認証できるようにアプリケーションを登録する方法について説明します。

>[AZURE.NOTE]このチュートリアルはあらゆるプラットフォームにおいて拡張性の高いモバイル アプリケーションを作成するソリューションの [Azure Mobile Services](http://azure.microsoft.com/services/mobile-services/) について説明します。Mobile Services によって簡単にデータの同期化を行い、ユーザーを認証して、プッシュ通知を送信できます。このページはアプリケーションにユーザーをサインインさせる方法を説明する「[認証の使用](mobile-services-ios-get-started-users.md)」チュートリアルをサポートしています。<br/>Mobile Services を初めて使用する場合は、チュートリアル「[Mobile Services の使用](mobile-services-ios-get-started.md)」を完了することをお勧めします。

このトピックの手順を完了するには、検証済みの電子メール アドレスを持つ Google アカウントが必要になります。新しい Google アカウントを作成するには、<a href="http://go.microsoft.com/fwlink/p/?LinkId=268302" target="_blank">accounts.google.com</a> にアクセスしてください。

1. <a href="http://go.microsoft.com/fwlink/p/?LinkId=268303" target="_blank">Google apis</a> Web サイトに移動して、Google アカウント資格情報でサインインします。**[プロジェクトの作成]** をクリックして**プロジェクト名**を入力し、**[作成]** をクリックします。

   	![Google API 新しいプロジェクト](./media/mobile-services-how-to-register-google-authentication/mobile-services-google-new-project.png)

2. **[同意画面]** をクリックし、自分の**メール アドレス**を選択し、**製品名**を入力して、**[保存]** をクリックします。

3. **[API & Auth]**、**[Credentials]**、**[Create new Client ID]** の順にクリックします。

   	![新しいクライアント ID の作成](./media/mobile-services-how-to-register-google-authentication/mobile-services-google-create-client.png)

4. **[ウェブ アプリケーション]** を選択して、**[承認済みの JAVASCRIPT 生成元]** にモバイル サービスの URL を入力し、**[承認済みのリダイレクト URI]** で生成された URL をモバイル サービスの URL の末尾に `/login/google` を付けたものと置き換えて、**[クライアント ID を生成]** をクリックします。

	>[AZURE.NOTE]Visual Studio を使用して Azure に発行された .NET バックエンド モバイル サービスについては、リダイレクト URL は、ユーザーのモバイル サービスの URL の末尾に .NET サービスとしてパス _signin-google_ を付加したものです。例: `https://todolist.azure-mobile.net/signin-google`

   	![](./media/mobile-services-how-to-register-google-authentication/mobile-services-google-create-client2.png)

5. **[Client ID for web applications]** で、**[クライアント ID]** と **[クライアント シークレット]** の値を書き留めます。

   	![クライアントの資格情報](./media/mobile-services-how-to-register-google-authentication/mobile-services-google-create-client3.png)

    >[AZURE.IMPORTANT]クライアント シークレットは、重要なセキュリティ資格情報です。このシークレットは、他のユーザーと共有したり、アプリケーションと共に配布したりしないでください。

アプリ内の認証で Google サインインを使用するようにモバイル サービスを構成する準備が整いました。

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->

[Google apis]: http://go.microsoft.com/fwlink/p/?LinkId=268303
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-dotnet/

[Azure Management Portal]: https://manage.windowsazure.com/
 

<!---HONumber=July15_HO4-->