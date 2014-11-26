<properties linkid="develop-mobile-how-to-guides-register-for-facebook-authentication" urlDisplayName="Register for Facebook Authentication" pageTitle="Register for Facebook authentication - Mobile Services" metaKeywords="Azure Facebook, Azure Facebook, Azure authenticate Mobile Services" description="Learn how to use Facebook authentication in your Azure Mobile Services app." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Register your apps for Facebook authentication with Mobile Services" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# モバイル サービスでの Facebook 認証用のアプリケーションの登録

このトピックでは、Facebook を使用して Azure のモバイル サービスで認証できるようにアプリケーションを登録する方法について説明します。

> [WACOM.NOTE] このチュートリアルはあらゆるプラットフォームにおいて拡張性の高いモバイル アプリケーションを作成するソリューションの [Azure モバイル サービス][Azure モバイル サービス]について説明します。モバイル サービスによって簡単にデータの同期化を行い、ユーザーを認証して、プッシュ通知を送信できます。このページはアプリケーションへユーザーをログインさせる方法を説明する「[認証の使用][認証の使用]」チュートリアルをサポートしています。モバイル サービスを初めて使用する場合は、チュートリアル「[モバイル サービスの使用][モバイル サービスの使用]」を完了することをお勧めします。

このトピックの手順を完了するには、検証済みの電子メール アドレスを持つ Facebook アカウントおよび携帯電話番号が必要になります。新しい Facebook アカウントを作成するには、[facebook.com][facebook.com] にアクセスしてください。

1.  [Facebook Developers][Facebook Developers] の Web サイトに移動し、Facebook アカウントの資格情報でサインインします。

2.  (省略可能) まだ登録していない場合は、**[Apps]**、**[Register as a Developer]** の順にクリックし、ポリシーに同意して、登録手順に従います。

    ![][0]

3.  **[Apps]** をクリックし、**[Create a New App]** をクリックします。

    ![][1]

4.  アプリケーションの一意の名前を選択し、**[Apps for Pages]** を選択して **[Create App]** をクリックし、登録時の質問に回答します。

    ![][2]

    これでアプリケーションが Facebook に登録されます。

5.  **[設定]** をクリックして **[App Domains]** にモバイル サービルのドメインを入力します。また **[Contact Email]** を入力し、**[Add Platform]** をクリックして **[Website]** を選択します。

    ![][3]

6.  **[Site URL]** にモバイル サービスの URL を入力し、**[Save Changes]** をクリックします。

    ![][4]

7.  **[Show]** をクリックし、入力を求められたらパスワードを入力し、**[App ID]** と **[App Secret]** の値を書き留めておきます。

    ![][5]

    <div class="dev-callout"><b>セキュリティに関する注意</b>
<p>アプリケーション シークレットは、重要なセキュリティ資格情報です。このシークレットは、他のユーザーと共有したり、アプリケーションと共に配布したりしないでください。</p>
</div>

8.  **[詳細設定]** タブをクリックし、**[Valid OAuth redirect URIs]** にモバイル サービス URL の末尾にパス */login/facebook* を付加したものを入力して、**[変更を保存]** をクリックします。

    > [WACOM.NOTE] Visual Studio を使用して Azure に発行された .NET バックエンド モバイル サービスについては、モバイル サービスのリダイレクト URL は、そのモバイル サービスの URL の末尾に .NET サービスとしてパス *signin-facebook* を付加したものです。例：`https://todolist.azure-mobile.net/signin-facebook`.

    ![][6]

9.  新しいアプリを定義した Facebook アカウントは、アプリの管理者であり、アプリへの管理者としてのアクセス権があります。その他の Facebook アカウントを認証するには、アプリにアクセスする必要があります。この手順で汎用パブリック アクセスが与えられるため、アプリはその他の Facebook アカウントを認証できるようになります。**[ステータスおよびレビュー]** をクリックします。その後 **[はい]** をクリックして汎用パブリック アクセスを有効にします。

    ![][7]

これで、App ID と App Secret の値をモバイル サービスに渡すことにより、Facebook ログインを使用してアプリケーションで認証を使用する準備ができました。

 
 


  [Azure モバイル サービス]: http://azure.microsoft.com/ja-jp/services/mobile-services/
  [認証の使用]: http://azure.microsoft.com/ja-jp/documentation/articles/mobile-services-ios-get-started-users/
  [モバイル サービスの使用]: http://azure.microsoft.com/ja-jp/documentation/articles/mobile-services-ios-get-started/
  [facebook.com]: http://go.microsoft.com/fwlink/p/?LinkId=268285
  [Facebook Developers]: http://go.microsoft.com/fwlink/p/?LinkId=268286
  [0]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-developer-register.png
  [1]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-add-app.png
  [2]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-new-app-dialog.png
  [3]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-configure-app.png
  [4]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-configure-app-2.png
  [5]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-completed.png
  [6]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-configure-app-3.png
  [7]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-configure-app-general-public.png
