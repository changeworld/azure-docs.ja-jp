新しい Twitter v1.1 API では、リソースにアクセスする前にアプリケーションの認証が求められます。まず、OAuth 2.0 を使用して、アクセスの要求に必要な資格情報を取得する必要があります。次に、モバイル サービス用のアプリケーション設定にその資格情報を安全に保存します。

1.  そのような操作をまだ実行していない場合には、トピック「[モバイル サービスでの Twitter ログイン用のアプリケーションの登録][モバイル サービスでの Twitter ログイン用のアプリケーションの登録]」の手順を実行します。

    Twitter v1.1 API にアクセスできるようにするために必要な資格情報は、Twitter が生成します。この資格情報は Twitter デベロッパーの Web サイトから取得できます。

2.  [Twitter デベロッパー][Twitter デベロッパー]の Web サイトに移動し、Twitter アカウント資格情報でサインインして、**[My Applications]** に移動し、自分の Twitter アプリケーションを選択します。

    ![][]

3.  アプリケーションの **[Details]** タブで、次の値を調べます。

    -   **コンシューマー キー**
    -   **コンシューマー シークレット**
    -   **アクセス トークン**
    -   **アクセス トークン シークレット**

    ![][1]

4.  [Windows Azure 管理ポータル][Windows Azure 管理ポータル]にログオンし、**[モバイル サービス]** をクリックして、目的のモバイル サービスをクリックします。

5.  **[ID]** タブをクリックし、Twitter から取得した **[コンシューマー キー]** と **[コンシューマー シークレット]** の値を入力して、**[保存]** をクリックします。

    ![][2]

6.  **[構成]** タブをクリックし、下へスクロールして **[アプリケーション設定]** を選択して、Twitter サイトから取得した次の項目の名前と値をそれぞれ **[名前]** と **[値]** に入力して、**[保存]** をクリックします。

    -   `TWITTER_ACCESS_TOKEN`
    -   `TWITTER_ACCESS_TOKEN_SECRET`

    ![][3]

    これにより、アプリケーション設定に Twitter アクセス トークンが保存されます。**[ID]** タブのコンシューマー資格情報と同様に、アクセス資格情報も暗号化されてアプリケーション設定に保存され、サーバー スクリプト ファイルに資格情報をハード コーディングすることなくサーバー スクリプト内で資格情報にアクセスすることができます。詳細については、「[アプリ設定][アプリ設定]」を参照してください。

<!-- URLs. -->

  [モバイル サービスでの Twitter ログイン用のアプリケーションの登録]: /ja-jp/documentation/articles/mobile-services-how-to-register-twitter-authentication/
  [Twitter デベロッパー]: http://go.microsoft.com/fwlink/p/?LinkId=268300
  []: ./media/mobile-services-register-twitter-access/mobile-twitter-my-apps.png
  [1]: ./media/mobile-services-register-twitter-access/mobile-twitter-app-secrets.png
  [Windows Azure 管理ポータル]: https://manage.windowsazure.com/
  [2]: ./media/mobile-services-register-twitter-access/mobile-identity-tab-twitter-only.png
  [3]: ./media/mobile-services-register-twitter-access/mobile-schedule-job-app-settings.png
  [アプリ設定]: http://msdn.microsoft.com/ja-jp/library/windowsazure/b6bb7d2d-35ae-47eb-a03f-6ee393e170f7
