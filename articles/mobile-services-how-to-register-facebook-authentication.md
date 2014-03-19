<properties linkid="develop-mobile-how-to-guides-register-for-facebook-authentication" urlDisplayName="Facebook 認証用の登録" pageTitle="Facebook 認証用の登録 - モバイル サービス" metaKeywords="Windows Azure Facebook, Azure Facebook, モバイル サービス Azure 認証" description="Windows Azure モバイル サービス アプリケーションで Facebook 認証を使用する方法について説明します。" metaCanonical="" services="" documentationCenter="" title="モバイル サービスでの Facebook 認証用のアプリケーションの登録" authors=""  solutions="" writer="glenga" manager="" editor=""  />

# モバイル サービスでの Facebook 認証用のアプリケーションの登録

このトピックでは、Facebook を使用して Windows Azure モバイル サービスで認証できるようにアプリケーションを登録する方法について説明します。

<div class="dev-callout"><b>メモ</b>
<p>このトピックの手順を完了するには、検証済みの電子メール アドレスを持つ Facebook アカウントおよび携帯電話番号が必要になります。新しい Facebook アカウントを作成するには、<a href="http://go.microsoft.com/fwlink/p/?LinkId=268285" target="_blank">facebook.com</a> にアクセスしてください。</p>
</div> 

1. <a href="http://go.microsoft.com/fwlink/p/?LinkId=268286" target="_blank">Facebook Developers</a> の Web サイトに移動し、Facebook アカウントの資格情報でサインインします。

2. (省略可能) まだ登録していない場合は、**[Apps]**、**[Register as a Developer]** の順にクリックし、ポリシーに同意して、登録手順に従います。

   	![][0]

3. **[Apps]** をクリックし、**[Create a New App]** をクリックします。

   	![][1]

4. アプリケーションの一意の名前を選択し、**[Apps for Pages]** を選択して **[Create App]** をクリックし、登録時の質問に回答します。

   	![][2]

	これでアプリケーションが Facebook に登録されます。

5. **[Settings]** をクリックして、**[App Domains]** にモバイル サービスのドメインを入力し、**[Add Platform]** をクリックして **[Website]** を選択します。

   	![][3]

6. **[Site URL]** にモバイル サービスの URL を入力し、**[Save Changes]** をクリックします。

	![][4]

7. **[Show]** をクリックし、入力を求められたらパスワードを入力し、**[App ID]** と **[App Secret]** の値を書き留めておきます。

   	![][5]

    <div class="dev-callout"><b>セキュリティに関する注意</b>
	<p>アプリケーション シークレットは、重要なセキュリティ資格情報です。このシークレットは、他のユーザーと共有したり、アプリケーションと共に配布したりしないでください。</p>
    </div>

これで、App ID と App Secret の値をモバイル サービスに渡すことにより、Facebook ログインを使用してアプリケーションで認証を使用する準備ができました。

<!-- Anchors. -->

<!-- Images. -->
[0]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-developer-register.png
[1]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-add-app.png
[2]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-new-app-dialog.png
[3]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-configure-app.png
[4]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-configure-app-2.png
[5]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-completed.png

<!-- URLs. -->
[Facebook Developers]: http://go.microsoft.com/fwlink/p/?LinkId=268286
[認証の使用]: /en-us/develop/mobile/tutorials/get-started-with-users-dotnet/
[Windows Azure の管理ポータル]: https://manage.windowsazure.com/


