

新しい Twitter v1.1 API では、リソースにアクセスする前にアプリケーションの認証が求められます。まず、OAuth 2.0 を使用して、アクセスの要求に必要な資格情報を取得する必要があります。次に、モバイル サービス用のアプリケーション設定にその資格情報を安全に保存します。

1. そのような操作をまだ実行していない場合には、トピック「[モバイル サービスでの Twitter ログイン用のアプリケーションの登録](../../includes/mobile-services-how-to-register-twitter-authentication.md)」の手順を実行します。 
  
  	Twitter v1.1 API にアクセスできるようにするために必要な資格情報は、Twitter が生成します。この資格情報は Twitter デベロッパーの Web サイトから取得できます。

2. [Twitter デベロッパー](http://go.microsoft.com/fwlink/p/?LinkId=268300)の Web サイトに移動し、Twitter アカウント資格情報でサインインして、自分の Twitter アプリを選択します。

3. アプリケーションの [**キーとアクセス トークン**] タブで、次の値をメモしておきます。

	+ **コンシューマー キー**
	+ **コンシューマー シークレット**
	+ **アクセス トークン**
	+ **アクセス トークン シークレット**

4. [Azure クラシック ポータル](https://manage.windowsazure.com/)にログオンし、**[Mobile Services]** をクリックして、モバイル サービスをクリックします。

5. **[ID]** タブをクリックし、Twitter から取得した **[コンシューマー キー]** と **[コンシューマー シークレット]** の値を入力して、**[保存]** をクリックします。

	![](./media/mobile-services-register-twitter-access/mobile-identity-tab-twitter-only.png)

2. **[構成]** タブをクリックし、下へスクロールして **[アプリケーション設定]** を選択して、Twitter サイトから取得した次の項目の名前と値をそれぞれ **[名前]** と **[値]** に入力して、**[保存]** をクリックします。

	+ `TWITTER_ACCESS_TOKEN`
	+ `TWITTER_ACCESS_TOKEN_SECRET`

	![](./media/mobile-services-register-twitter-access/mobile-schedule-job-app-settings.png)

	これにより、アプリケーション設定に Twitter アクセス トークンが保存されます。**[ID]** タブのコンシューマー資格情報と同様に、アクセス資格情報も暗号化されてアプリケーション設定に保存され、サーバー スクリプト ファイルに資格情報をハード コーディングすることなくサーバー スクリプト内で資格情報にアクセスすることができます。詳細については、「[アプリ設定]」を参照してください。

<!-- URLs. -->
[Mobile Services server script reference]: http://go.microsoft.com/fwlink/?LinkId=262293
[Register your apps for Twitter login with Mobile Services]: ../articles/mobile-services/mobile-services-how-to-register-twitter-authentication.md
[Twitter Developers]: http://go.microsoft.com/fwlink/p/?LinkId=268300
[アプリ設定]: http://msdn.microsoft.com/library/azure/b6bb7d2d-35ae-47eb-a03f-6ee393e170f7

<!---HONumber=AcomDC_0211_2016-->