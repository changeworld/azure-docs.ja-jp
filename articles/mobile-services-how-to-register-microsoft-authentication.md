<properties pageTitle="Microsoft 認証用の登録 - モバイル サービス" metaKeywords="Azure でのアプリケーションの登録, Azure Microsoft 認証, アプリケーション認証, モバイル サービスの認証" description="Azure のモバイル サービス アプリケーションで Microsoft 認証に登録する方法について説明します。" metaCanonical="" disqusComments="0" umbracoNaviHide="1" title="Microsoft アカウント ログインを使用するためのアプリケーションの登録" authors="" />

# Microsoft アカウント ログインを使用するためのアプリケーションの登録

このトピックでは、Azure のモバイル サービスの認証プロバイダーとして Live Connect を使用できるようにアプリケーションを登録する方法について説明します。

>[WACOM.NOTE]また、Windows ストア アプリからシングル サインオン (SSO) またはプッシュ通知に対応するためにクライアント主体の認証を提供する場合は、アプリケーションを Windows ストアに登録することも検討します。詳細については、「<a href="/ja-jp/develop/mobile/how-to-guides/register-for-single-sign-on">Windows Live Connect シングル サインオンを使用するための Windows ストア アプリの登録</a>」を参照してください。


1. Live Connect デベロッパー センターの<a href="http://go.microsoft.com/fwlink/p/?LinkId=262039" target="_blank">アプリケーション管理</a>のページに移動し、必要な場合は Microsoft アカウントでログオンします。

2. **[アプリケーションの作成]** をクリックし、**[アプリケーション名]** に名前を入力して、**[同意する]** をクリックします。

   	![][1] 

   	これでアプリケーションが Live Connect に登録されます。

10. **[設定の編集]**、**[API 設定]** の順にクリックし、**[クライアント ID]** と **[クライアント シークレット]** の値を書き留めておきます。

   	![][2]

    <div class="dev-callout"><b>セキュリティに関する注意</b>
	<p>クライアント シークレットは、重要なセキュリティ資格情報です。クライアント シークレットは、他のユーザーと共有したり、アプリケーションで配信したりしないでください。</p>
    </div>

11. **[リダイレクト ドメイン]** ボックスにモバイル サービスの URL を入力し、**[保存]** をクリックします。

これで、クライアント ID とクライアント シークレットの値をモバイル サービスに渡すことにより、Microsoft アカウントを使用してアプリケーションで認証を使用する準備ができました。

<!-- Anchors. -->

<!-- Images. -->
[1]: ./media/mobile-services-how-to-register-microsoft-authentication/mobile-services-live-connect-add-app.png
[2]: ./media/mobile-services-how-to-register-microsoft-authentication/mobile-live-connect-app-api-settings.png
<!-- URLs. -->
[Live Connect を使用した Windows ストア アプリへのシングル サインオン]: /ja-jp/develop/mobile/how-to-guides/register-for-single-sign-on/
[アプリケーションの提出に関するページ: ]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[マイ アプリケーション]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[モバイル サービスの使用]: /ja-jp/develop/mobile/tutorials/get-started/
[認証の使用]: /ja-jp/develop/mobile/tutorials/get-started-with-users-dotnet/
[プッシュ通知の使用]: /ja-jp/develop/mobile/tutorials/get-started-with-push-dotnet/
[スクリプトを使用したユーザーの承認]: /ja-jp/develop/mobile/tutorials/authorize-users-in-scripts-dotnet/
[JavaScript と HTML]: /ja-jp/develop/mobile/tutorials/get-started-with-users-js/

[Azure 管理ポータル]: https://manage.windowsazure.com/

