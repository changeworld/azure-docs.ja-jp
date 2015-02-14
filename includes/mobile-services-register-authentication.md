

ユーザーを認証できるようにするには、アプリケーションを ID プロバイダーに登録する必要があります。その後、プロバイダーによって生成されたクライアント シークレットをモバイル サービスに登録する必要があります。

1. [Azure 管理ポータル]にログオンし、**[モバイル サービス]** をクリックして、目的のモバイル サービスをクリックします。

   	![](./media/mobile-services-register-authentication/mobile-services-selection.png)

2. **[ダッシュボード]** タブをクリックし、**[モバイル サービス URL]** の値をメモしておきます。

   	![](./media/mobile-services-register-authentication/mobile-service-uri.png)

    アプリケーションを登録するときに、この値を ID プロバイダーに指定する必要が生じる場合があります。

3. 以下の一覧から、サポートされている ID プロバイダーを選択し、手順に従ってそのプロバイダーにアプリケーションを登録します。

 - <a href="/ja-jp/documentation/articles/mobile-services-how-to-register-microsoft-authentication/" target="_blank">Microsoft アカウント</a>
 - <a href="/ja-jp/documentation/articles/mobile-services-how-to-register-facebook-authentication/" target="_blank">Facebook ログイン</a>
 - <a href="/ja-jp/documentation/articles/mobile-services-how-to-register-twitter-authentication/" target="_blank">Twitter ログイン</a>
 - <a href="/ja-jp/documentation/articles/mobile-services-how-to-register-google-authentication/" target="_blank">Google ログイン</a>
 - <a href="/ja-jp/documentation/articles/mobile-services-how-to-register-active-directory-authentication/" target="_blank">Azure Active Directory</a>


    プロバイダーによって生成されるクライアント ID およびシークレット値をメモしておいてください。

    > [AZURE.IMPORTANT]プロバイダーによって生成されるシークレットは、重要なセキュリティ資格情報です。このシークレットは、他のユーザーと共有したり、アプリケーションと共に配布したりしないでください。

4. 管理ポータルに戻って **[識別]** タブをクリックし、アプリケーションの識別子と、ID プロバイダーから取得した共有シークレット値を入力して、**[保存]** をクリックします。

   	![](./media/mobile-services-register-authentication/mobile-identity-tab.png)

	これで、モバイル サービスとアプリケーションの両方が、選択した認証プロバイダーと連係するように構成されました。

<!-- URLs. -->
[Azure 管理ポータル]: https://manage.windowsazure.com/<!--HONumber=42-->
