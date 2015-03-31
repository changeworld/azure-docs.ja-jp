

ユーザーを認証するには、ID プロバイダーにアプリを登録し、次に Azure Mobile Services にプロバイダーが生成したクライアント資格情報を登録します。

1. Azure 管理ポータルにログオンし、**[Mobile Services]** をクリックして、目的の Mobile Services をクリックします。

2. **[ダッシュボード]** タブをクリックし、**[Mobile Services URL]** の値をメモしておきます。アプリケーションを登録するときに、この値を ID プロバイダーに指定する必要が生じる場合があります。

3. 次のリストから、サポート対象の ID プロバイダーを選択します。手順に従って、アプリをそのプロバイダーに登録します。プロバイダーによって生成されるクライアント ID とシークレット値をメモしておいてください。

 - <a href="/documentation/articles/mobile-services-how-to-register-microsoft-authentication/" target="_blank">Microsoft アカウント</a>
 - <a href="/documentation/articles/mobile-services-how-to-register-facebook-authentication/" target="_blank">Facebook</a>
 - <a href="/documentation/articles/mobile-services-how-to-register-twitter-authentication/" target="_blank">Twitter</a>
 - <a href="/documentation/articles/mobile-services-how-to-register-google-authentication/" target="_blank">Google </a>
 - <a href="/documentation/articles/mobile-services-how-to-register-active-directory-authentication/" target="_blank">Azure Active Directory</a>

    > [AZURE.IMPORTANT] プロバイダーによって生成されるシークレットは、重要なセキュリティ資格情報です。このシークレットは、他のユーザーと共有したり、アプリケーションと共に配布したりしないでください。

4. 管理ポータルに戻って **[識別]** タブをクリックし、アプリケーションの識別子と、ID プロバイダーから取得した共有シークレット値を入力して、**[保存]** をクリックします。これで、Mobile Services とアプリケーションの両方が、選択した認証プロバイダーと連係するように構成されました。

    > [AZURE: 重要] ID プロバイダーの開発者向けサイトに、正しいリダイレクト URI が設定されていることをご確認くださいください。上記リンクの各プロバイダーに関する手順で説明しているように、リダイレクト URI は .NET バックエンド サービス用と JavaScript バックエンド サービス用とで異なる場合があります。リダイレクト URI が正しく構成されていないと、ログイン画面が正しく表示されず、アプリが予期しない異常を起こす場合があります。

<!--HONumber=47-->
