<properties linkid="develop-mobile-how-to-guides-register-for-google-authentication" urlDisplayName="Google 認証用の登録" pageTitle="Google 認証用の登録 - モバイル サービス" metaKeywords="Azure アプリケーションの登録, Azure 認証, Google アプリケーション認証, モバイル サービスの認証" description="Google を使用して Azure のモバイル サービスで認証できるようにアプリケーションを登録する方法について説明します。" metaCanonical="" services="" documentationCenter="" title="モバイル サービスでの Google ログイン用のアプリケーションの登録" authors="" solutions="" manager="" editor="" />

# モバイル サービスでの Google ログイン用のアプリケーションの登録

このトピックでは、Google を使用して Azure のモバイル サービスで認証できるようにアプリケーションを登録する方法について説明します。

<div class="dev-callout"><b>注</b>
<p>このトピックの手順を完了するには、検証済みの電子メール アドレスを持つ Google アカウントが必要になります。新しい Google アカウントを作成するには、<a href="http://go.microsoft.com/fwlink/p/?LinkId=268302" target="_blank">accounts.google.com</a> にアクセスしてください。</p>
</div> 

1. <a href="http://go.microsoft.com/fwlink/p/?LinkId=268303" target="_blank">Google apis</a> Web サイトに移動し、Google アカウント資格情報でサインインして、**[Create project...]** をクリックします。

   	![][1]   

2. **[API Access]** をクリックし、**[Create an OAuth 2.0 client ID]** をクリックします。

   	![][2]

3. **[Branding Information]** で **[Product name]** に名前を入力し、**[Next]** をクリックします。

   	![][3]

4. **[Client ID Settings]** で **[Web application]** を選択し、**[Your site or hostname]** にモバイル サービスの URL を入力して、**[more options]** をクリックします。**[Authorized Redirect URIs]** に生成された URL を、モバイル サービス URL の末尾にパス _/login/google_ を付加したものに置き換えて、**[Create client ID]** をクリックします。

   	![][4]

	>[WACOM.NOTE]Visual Studio を使用して Azure に発行された .NET バックエンド モバイル サービスについては、リダイレクト URL はユーザーのモバイル サービスの URL の末尾にパス __Hlk384692799signin-google__Hlk384692799 .NET サービスとしてのユーザーのモバイル サービス (<code>https://todolist.azure-mobile.net/signin-google</code> など) を付加したものです。

6. **[Client ID for web applications]** で、**[Client ID]** と **[Client secret]** の値を書き留めます。

   	![][5]

    <div class="dev-callout"><b>セキュリティに関する注意</b>
	<p>クライアント シークレットは、重要なセキュリティ資格情報です。このシークレットは、他のユーザーと共有したり、アプリケーションと共に配布したりしないでください。</p>
    </div>

これで、クライアント ID とクライアント シークレットの値をモバイル サービスに渡すことにより、Google ログインを使用してアプリケーションで認証を使用する準備ができました。

<!-- Anchors. -->

<!-- Images. -->
[1]: ./media/mobile-services-how-to-register-google-authentication/mobile-services-google-developers.png
[2]: ./media/mobile-services-how-to-register-google-authentication/mobile-services-google-create-client.png
[3]: ./media/mobile-services-how-to-register-google-authentication/mobile-services-google-create-client2.png
[4]: ./media/mobile-services-how-to-register-google-authentication/mobile-services-google-create-client3.png
[5]: ./media/mobile-services-how-to-register-google-authentication/mobile-services-google-app-details.png

<!-- URLs. -->

[Google API]: http://go.microsoft.com/fwlink/p/?LinkId=268303
[認証の使用]: /ja-jp/develop/mobile/tutorials/get-started-with-users-dotnet/

[Azure 管理ポータル]: https://manage.windowsazure.com/

