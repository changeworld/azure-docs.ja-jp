

ユーザーを認証できるようにするには、アプリケーションを ID プロバイダーに登録する必要があります。その後、プロバイダーによって生成されたクライアント シークレットをアプリ サービスに登録する必要があります。

1. [Azure 管理ポータル]にログオンし、**[参照]**、**[リソース グループ]** をクリックした後、モバイル アプリのリソース グループを選択します。

2. ゲートウェイを選択し、**[プロパティ]** の下に表示されている **URL** の値を記録します。アプリケーションを登録するときに、この値を ID プロバイダーに指定する必要が生じる場合があります。

   	![](./media/app-service-mobile-register-authentication/gateway-uri.png)

3. 以下の一覧から、サポートされている ID プロバイダーを選択し、手順に従ってそのプロバイダーにアプリケーションを構成します。

 - <a href="/ja-jp/documentation/articles/app-service-mobile-how-to-configure-active-directory-authentication-preview/" target="_blank">Azure Active Directory</a>
 - <a href="/ja-jp/documentation/articles/app-service-mobile-how-to-configure-facebook-authentication-preview/" target="_blank">Facebook ログイン</a>
 - <a href="/ja-jp/documentation/articles/app-service-mobile-how-to-configure-google-authentication-preview/" target="_blank">Google ログイン</a>
 - <a href="/ja-jp/documentation/articles/app-service-mobile-how-to-configure-microsoft-authentication-preview/" target="_blank">Microsoft アカウント</a>
 - <a href="/ja-jp/documentation/articles/app-service-mobile-how-to-configure-twitter-authentication-preview/" target="_blank">Twitter ログイン</a>

	これで、アプリケーションは、選択された認証プロバイダーで動作するように構成されました。

<!-- URLs. -->
[Azure 管理ポータル]: https://manage.windowsazure.com/

<!--HONumber=49-->