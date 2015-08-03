<properties 
	pageTitle="Facebook 認証用の登録 - Mobile Services" 
	description="Azure Mobile Services アプリケーションで Facebook 認証を使用する方法について説明します。" 
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
	ms.date="06/27/2015" 
	ms.author="glenga"/>

# Mobile Services での Facebook 認証用のアプリケーションの登録

[AZURE.INCLUDE [mobile-services-selector-register-identity-provider](../../includes/mobile-services-selector-register-identity-provider.md)]

このトピックでは、Facebook を使用して Azure Mobile Services で認証できるようにアプリケーションを登録する方法について説明します。

>[AZURE.NOTE]このチュートリアルはあらゆるプラットフォームにおいて拡張性の高いモバイル アプリケーションを作成するソリューションの [Azure Mobile Services] について説明します。Mobile Services によって簡単にデータの同期化を行い、ユーザーを認証して、プッシュ通知を送信できます。このページはアプリケーションへユーザーをログインさせる方法を説明する<a href="http://azure.microsoft.com/documentation/articles/mobile-services-ios-get-started-users/">認証の使用</a>チュートリアルをサポートしています。Mobile Services を初めて使用する場合は、チュートリアル<a href="http://azure.microsoft.com/documentation/articles/mobile-services-ios-get-started/">Mobile Services の使用</a>を完了することをお勧めします。
	
このトピックの手順を完了するには、検証済みの電子メール アドレスを持つ Facebook アカウントおよび携帯電話番号が必要になります。新しい Facebook アカウントを作成するには、<a href="http://go.microsoft.com/fwlink/p/?LinkId=268285" target="_blank">facebook.com</a> にアクセスしてください。

1. <a href="http://go.microsoft.com/fwlink/p/?LinkId=268286" target="_blank">Facebook Developers</a> の Web サイトに移動し、Facebook アカウントの資格情報でサインインします。

2. (省略可能) まだ登録していない場合は、[**My Apps**]、[**Register as a Developer**] の順にクリックし、ポリシーに同意して、登録手順に従います。

3. [**My Apps**] 、[**Add a New App**] 、[**Advanced setup**] の順にクリックします。

4. [**Display name**] にアプリケーションの一意の名前を入力し、[**Category**] の [**Apps for Pages**] を選択してから、[**Create App ID**] をクリックしてセキュリティの手順を完了します。

	これにより、新しい Facebook アプリ ID が作成されます。

5. [**Settings**] をクリックして、[**App Domains**] にモバイル サービスのドメインを入力し、[**Contact Email**] を入力してから (省略可能)、[**Add Platform**] をクリックして [**Website**] を選択します。

   	![][3]

6. **[Site URL]** にモバイル サービスの URL を入力し、**[Save Changes]** をクリックします。

7. **[Show]** をクリックし、入力を求められたらパスワードを入力し、**[App ID]** と **[App Secret]** の値を書き留めておきます。

   	![][5] &nbsp;
	
    >[AZURE.IMPORTANT]アプリケーション シークレットは、重要なセキュリティ資格情報です。このシークレットは、他のユーザーと共有したり、アプリケーションと共に配布したりしないでください。&nbsp;

8. **[詳細設定]** タブをクリックし、**[Valid OAuth redirect URIs]** にモバイル サービス URL の末尾にパス _/login/facebook_ を付加したものを入力して、**[変更を保存]** をクリックします。&nbsp;

     >[AZURE.NOTE]Visual Studio を使用して Azure に発行された .NET バックエンド モバイル サービスについては、リダイレクト URL は、ユーザーのモバイル サービスの URL の末尾に .NET サービスとしてパス _signin-facebook_ を付加したものです。例: `https://todolist.azure-mobile.net/signin-facebook`
       

9. [**Status & Review**]、[**Yes**] の順にクリックし、アプリへの汎用パブリック アクセスを有効にします。

	新しいアプリを登録するために使用した Facebook アカウントは、アプリの管理者であり、アプリへの管理者としてのアクセス権があります。この手順で汎用パブリック アクセスが与えられるため、アプリはその他の Facebook アカウントを使用して認証できるようになります。


これで、App ID と App Secret の値を Mobile Services に渡すことにより、Facebook ログインを使用してアプリケーションで認証を使用する準備ができました。

<!-- Anchors. -->

<!-- Images. -->
[3]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-configure-app.png
[5]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-completed.png

<!-- URLs. -->
[Facebook Developers]: http://go.microsoft.com/fwlink/p/?LinkId=268286
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-dotnet/
[Azure Management Portal]: https://manage.windowsazure.com/
[Azure Mobile Services]: http://azure.microsoft.com/services/mobile-services/
 

<!---HONumber=July15_HO4-->