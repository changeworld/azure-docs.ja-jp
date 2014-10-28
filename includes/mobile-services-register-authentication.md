ユーザーを認証できるようにするには、アプリケーションを ID プロバイダーに登録する必要があります。その後、プロバイダーによって生成されたクライアント シークレットをモバイル サービスに登録する必要があります。

1.  [Azure 管理ポータル][Azure 管理ポータル]にログオンし、**[Mobile Services]** をクリックして、目的のモバイル サービスをクリックします。

    ![][]

2.  **[ダッシュボード]** タブをクリックし、**[モバイル サービス URL]** の値をメモしておきます。

    ![][1]

    アプリケーションを登録するときに、この値を ID プロバイダーに指定する必要が生じる場合があります。

3.  以下の一覧から、サポートされている ID プロバイダーを選択し、手順に従ってそのプロバイダーにアプリケーションを登録します。

 -   [Microsoft アカウント][Microsoft アカウント]
 -   [Facebook ログイン][Facebook ログイン]
 -   [Twitter ログイン][Twitter ログイン]
 -   [Google ログイン][Google ログイン]
 -   [Azure Active Directory][Azure Active Directory]

    プロバイダーによって生成されるクライアント ID およびシークレット値をメモしておいてください。

    <div class="dev-callout"><b>セキュリティに関する注意</b>
<p>プロバイダーによって生成されるシークレットは、重要なセキュリティ資格情報です。このシークレットは、他のユーザーと共有したり、アプリケーションと共に配布したりしないでください。</p>
</div>

1.  管理ポータルに戻って **[識別]** タブをクリックし、アプリケーションの識別子と、ID プロバイダーから取得した共有シークレット値を入力して、**[保存]** をクリックします。

    ![][2]

    これで、モバイル サービスとアプリケーションの両方が、選択した認証プロバイダーと連係するように構成されました。

<!-- URLs. -->

  [Azure 管理ポータル]: https://manage.windowsazure.com/
  []: ./media/mobile-services-register-authentication/mobile-services-selection.png
  [1]: ./media/mobile-services-register-authentication/mobile-service-uri.png
  [Microsoft アカウント]: /ja-jp/documentation/articles/mobile-services-how-to-register-microsoft-authentication/
  [Facebook ログイン]: /ja-jp/documentation/articles/mobile-services-how-to-register-facebook-authentication/
  [Twitter ログイン]: /ja-jp/documentation/articles/mobile-services-how-to-register-twitter-authentication/
  [Google ログイン]: /ja-jp/documentation/articles/mobile-services-how-to-register-google-authentication/
  [Azure Active Directory]: /ja-jp/documentation/articles/mobile-services-how-to-register-active-directory-authentication/
  [2]: ./media/mobile-services-register-authentication/mobile-identity-tab.png
