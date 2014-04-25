<properties linkid="develop-mobile-how-to-guides-register-for-twitter-authentication" urlDisplayName="Twitter 認証用の登録" pageTitle="Twitter 認証用の登録 - モバイル サービス" metaKeywords="Azure アプリケーションの登録, Azure Twitter 認証, アプリケーション認証, モバイル サービスの認証, モバイル サービス Twitter" description="Azure のモバイル サービス アプリケーションで Twitter 認証を使用する方法について説明します。" metaCanonical="" services="" documentationCenter="" title="モバイル サービスでの Twitter ログイン用のアプリケーションの登録" authors="" solutions="" manager="" editor="" />

#モバイル サービスでの Twitter ログイン用のアプリケーションの登録

このトピックでは、Twitter を使用して Azure のモバイル サービスで認証できるようにアプリケーションを登録する方法について説明します。

<div class="dev-callout"><b>注</b>
<p>このトピックの手順を完了するには、検証済みの電子メール アドレスを持つ Twitter アカウントが必要になります。新しい Twitter アカウントを作成するには、<a href="http://go.microsoft.com/fwlink/p/?LinkID=268287" target="_blank">twitter.com</a> にアクセスしてください。</p>
</div> 

1. <a href="http://go.microsoft.com/fwlink/p/?LinkId=268300" target="_blank">Twitter デベロッパー</a>の Web サイトに移動し、Twitter アカウント資格情報でサインインして、**[Create a new application]** をクリックします。

   	![][1]

2. **[Name]**、**[Description]**、および **[Website]** に、アプリケーションに応じた値を入力し、**[Callback URL]** にモバイル サービスの URL を入力します。

   	![][2]

    
	>[WACOM.NOTE]Visual Studio を使用して Azure に発行された .NET バックエンド モバイル サービスについては、リダイレクト URL はユーザーのモバイル サービスの URL の末尾にパス _signin-google_ .NET サービスとしてのユーザーのモバイル サービス (<code>https://todolist.azure-mobile.net/signin-twitter</code> など) を追加したものです。<br /><strong>[Website]</strong> の値は必須ですが、使用されません。

3. ページの下部に記載されている条項を読んで同意し、正しい CAPTCHA 文字列を入力して、**[Create your Twitter application]** をクリックします。

   	![][3]

   	これでアプリケーションが登録され、アプリケーションの詳細が表示されます。

6. **[Consumer key]** と **[Consumer secret]** の値を書き留めます。

   	![][4]

    <div class="dev-callout"><b>セキュリティに関する注意</b>
	<p>コンシューマー シークレットは、重要なセキュリティ資格情報です。このシークレットは、他のユーザーと共有したり、アプリケーションと共に配布したりしないでください。</p>
    </div>

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

