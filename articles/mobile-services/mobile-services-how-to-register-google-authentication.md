<properties 
	pageTitle="Google 認証用の登録 | Microsoft Azure" 
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
	ms.date="08/27/2015" 
	ms.author="glenga"/>

# Mobile Services での Google ログイン用のアプリケーションの登録

[AZURE.INCLUDE [mobile-services-selector-register-identity-provider](../../includes/mobile-services-selector-register-identity-provider.md)]

このトピックでは、Google を使用して Azure Mobile Services で認証できるようにアプリケーションを登録する方法について説明します。

>[AZURE.NOTE]このチュートリアルはあらゆるプラットフォームにおいて拡張性の高いモバイル アプリケーションを作成するソリューションの [Azure Mobile Services](http://azure.microsoft.com/services/mobile-services/) について説明します。Mobile Services によって簡単にデータの同期化を行い、ユーザーを認証して、プッシュ通知を送信できます。このページはアプリケーションにユーザーをサインインさせる方法を説明する「[認証の使用](mobile-services-ios-get-started-users.md)」チュートリアルをサポートしています。<br/>Mobile Services を初めて使用する場合は、チュートリアル「[Mobile Services の使用](mobile-services-ios-get-started.md)」を完了することをお勧めします。

このトピックの手順を完了するには、検証済みの電子メール アドレスを持つ Google アカウントが必要になります。新しい Google アカウントを作成するには、<a href="http://go.microsoft.com/fwlink/p/?LinkId=268302" target="_blank">accounts.google.com</a> にアクセスしてください。

3. [Google apis](http://go.microsoft.com/fwlink/p/?LinkId=268303) Web サイトに移動して、Google アカウント資格情報でサインインします。**[プロジェクトの作成]** をクリックして**プロジェクト名**を入力し、**[作成]** をクリックします。

4. 左側のナビゲーション バーで、**[API & Auth]** をクリックした後、**[Social APIs]** の下の **[Google+ API]**、**[Enable API]** の順にクリックします。

5. **[API & Auth]**、**[Credentials]**、**[OAuth consent screen]** の順にクリックした後、自分の **電子メール アドレス** を選択し、**製品名** を入力し、**[Save]** をクリックします。

6. **[Credentials]** タブで、**[Add credentials]**、**[OAuth 2.0 client ID]** の順にクリックした後、**[Web application]** を選択します。

7. **[承認済みの JAVASCRIPT 生成元]** にモバイル サービスの URL を入力し、**[承認済みのリダイレクト URI]** で生成された URL をモバイル サービスの URL の末尾に `/login/google` を付けたものと置き換えて、**[クライアント ID を生成]** をクリックします。

	>[AZURE.NOTE]Visual Studio を使用して Azure に発行された .NET バックエンド モバイル サービスについては、リダイレクト URL は、ユーザーのモバイル サービスの URL の末尾に .NET サービスとしてパス _signin-google_ を付加したものです。例: `https://todolist.azure-mobile.net/signin-google`
	
8. 次の画面で、表示されているクライアント ID とクライアント シークレットの値を書き留めます。

    > [AZURE.IMPORTANT]クライアント シークレットは、重要なセキュリティ資格情報です。このシークレットを他のユーザーと共有したり、クライアント アプリケーション内で配信したりしないでください。

アプリ内の認証で Google サインインを使用するようにモバイル サービスを構成する準備が整いました。

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->

[Google apis]: http://go.microsoft.com/fwlink/p/?LinkId=268303
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-dotnet/

[Azure Management Portal]: https://manage.windowsazure.com/
 

<!---HONumber=Oct15_HO3-->