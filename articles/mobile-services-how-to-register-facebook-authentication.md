<properties pageTitle="Facebook 認証用の登録 - モバイル サービス" description="Azure Mobile Services アプリケーションで Facebook 認証を使用する方法について説明します。" services="mobile-services" documentationCenter="" authors="ggailey777" manager="dwrede" editor=""/>

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="11/21/2014" ms.author="glenga"/>

# モバイル サービスでの Facebook 認証用のアプリケーションの登録

このトピックでは、Facebook を使用して Azure のモバイル サービスで認証できるようにアプリケーションを登録する方法について説明します。 

> [AZURE.NOTE] このチュートリアルはあらゆるプラットフォームにおいて拡張性の高いモバイル アプリケーションを作成するソリューションの [Azure Mobile Services] について説明します。モバイル サービスによって簡単にデータの同期化を行い、ユーザーを認証して、プッシュ通知を送信できます。このページはアプリケーションへユーザーをログインさせる方法を説明する「<a href="http://azure.microsoft.com/ja-jp/documentation/articles/mobile-services-ios-get-started-users/">認証の使用</a>」チュートリアルをサポートしています。Mobile Services を初めて使用する場合は、チュートリアル「<a href="http://azure.microsoft.com/ja-jp/documentation/articles/mobile-services-ios-get-started/">Mobile Services の使用</a>」を完了することをお勧めします。
	
このトピックの手順を完了するには、検証済みの電子メール アドレスを持つ Facebook アカウントおよび携帯電話番号が必要になります。新しい Facebook アカウントを作成するには、<a href="http://go.microsoft.com/fwlink/p/?LinkId=268285" target="_blank">facebook.com</a> にアクセスしてください。

1. <a href="http://go.microsoft.com/fwlink/p/?LinkId=268286" target="_blank">Facebook Developers</a> の Web サイトに移動し、Facebook アカウントの資格情報でサインインします。

2. (省略可能) まだ登録していない場合は、**[Apps]**、**[Register as a Developer]** の順にクリックし、ポリシーに同意して、登録手順に従います。 

   	![][0]

3. **[Apps]** をクリックし、**[Create a New App]** をクリックします。

   	![][1]

4. アプリケーションの一意の名前を選択し、**[Apps for Pages]** を選択して **[Create App]** をクリックし、登録時の質問に回答します。

   	![][2]

	これでアプリケーションが Facebook に登録されます。 

5. **[設定]** をクリックして **[App Domains]** にモバイル サービルのドメインを入力します。また **[Contact Email]** を入力し、**[Add Platform]** をクリックして **[Website]** を選択します。

   	![][3]

6. **[Site URL]** にモバイル サービスの URL を入力し、**[Save Changes]** をクリックします。

	![][4]

7. **[Show]** をクリックし、入力を求められたらパスワードを入力し、**[App ID]** と **[App Secret]** の値を書き留めておきます。 

   	![][5]

	> [AZUTE.NOTE] **セキュリティに関する注意**
	アプリケーション シークレットは、重要なセキュリティ資格情報です。このシークレットは、他のユーザーと共有したり、アプリケーションと共に配布したりしないでください。


8. **[詳細設定]** タブをクリックし、**[Valid OAuth redirect URIs]** にモバイル サービス URL の末尾にパス _/login/facebook_ を付加したものを入力して、**[変更を保存]** をクリックします。 

	> [AZURE.NOTE] Visual Studio を使用して Azure に発行された .NET バックエンド モバイル サービスについては、リダイレクト URL は、ユーザーのモバイル サービスの URL の末尾に .NET サービスとしてパス _signin-facebook_ を付加したものです。例:  <code>https://todolist.azure-mobile.net/signin-facebook</code>。  
	
	![][7]

9. 新しいアプリを定義した Facebook アカウントは、アプリの管理者であり、アプリへの管理者としてのアクセス権があります。その他の Facebook アカウントを認証するには、アプリにアクセスする必要があります。この手順で汎用パブリック アクセスが与えられるため、アプリはその他の Facebook アカウントを認証できるようになります。**[ステータスおよびレビュー]** をクリックします。その後 **[はい]** をクリックして汎用パブリック アクセスを有効にします。

    ![][6]



これで、App ID と App Secret の値をモバイル サービスに渡すことにより、Facebook ログインを使用してアプリケーションで認証を使用する準備ができました。  

<!-- Anchors. -->

<!-- Images. -->
[0]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-developer-register.png
[1]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-add-app.png
[2]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-new-app-dialog.png
[3]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-configure-app.png
[4]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-configure-app-2.png
[5]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-completed.png
[6]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-configure-app-general-public.png
[7]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-configure-app-3.png

<!-- URLs. -->
[Facebook Developers]: http://go.microsoft.com/fwlink/p/?LinkId=268286
[認証の使用]: /ja-jp/develop/mobile/tutorials/get-started-with-users-dotnet/
[Azure 管理ポータル]: https://manage.windowsazure.com/
[Azure Mobile Services]: http://azure.microsoft.com/ja-jp/services/mobile-services/


<!--HONumber=42-->
