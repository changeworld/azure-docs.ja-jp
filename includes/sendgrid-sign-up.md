Azure ユーザーは 1 か月あたり 25,000 通の電子メールを無料で利用できます。毎月 25,000 通の無料電子メールにより、高度なレポートおよび分析と[すべての API][] \(Web、SMTP、イベント、解析など) を利用できるようになります。SendGrid の提供するその他のサービスについては、[SendGrid の特徴][]に関するページを参照してください。

### SendGrid アカウントにサインアップするには

1. [Azure 管理ポータル][]にログインします。

2. 管理ポータルの下のウィンドウの **[新規]** をクリックします。

	![command-bar-new][command-bar-new]

3. **[Marketplace]** をクリックします。

	![sendgrid-store][sendgrid-store]

4. **[アプリケーションとサービスの選択]** ダイアログで **[SendGrid]** を選択し、右矢印をクリックします。

5. **[アプリケーションとサービスの個人用設定]** ダイアログで、サインアップする **[SendGrid]** プランを選択します。

6. Azure 設定で **SendGrid** サービスを特定する名前を入力するか、**SendGridEmailDelivery.Simplified.SMTPWebAPI** の既定値を使用します。名前は 1 ～ 100 文字にする必要があります。また、名前に含めることができるのは英数字、ダッシュ、ピリオド、およびアンダースコアのみです。この名前は、Azure ストアのサブスクライブ項目の一覧で一意になっている必要があります。

	![store-screen-2][store-screen-2]

7. リージョンの値を選択します (たとえば [米国西部])。

8. 右矢印をクリックします。

9. **[購入の確認]** タブで、プランと価格情報および法律条項を確認します。条項に同意したら、チェックマークをクリックします。チェック マークをクリックすると、SendGrid アカウントの [SendGrid のプロビジョニング プロセス]が開始されます。

	![store-screen-3][store-screen-3]

10. 購入を確認したら、アドオン ダッシュボードにリダイレクトされ、"**SendGrid を購入しています**" というメッセージが表示されます。

	![sendgrid-purchasing-message][sendgrid-purchasing-message]

	SendGrid アカウントが直ちにプロビジョニングされ、"**アドオン SendGrid の購入が完了しました**" というメッセージが表示されます。アカウントと資格情報が作成されました。この時点で、電子メールを送信する準備が整っています。

	サブスクリプション プランを変更するか、SendGrid 連絡先設定を表示するには、SendGrid サービスの名前をクリックし、SendGrid Marketplace ダッシュボードを開きます。

	![sendgrid-add-on-dashboard][sendgrid-add-on-dashboard]

	SendGrid を使用して電子メールを送信するには、アカウントの資格情報 (ユーザー名とパスワード) を指定する必要があります。

### SendGrid の資格情報を確認するには ###

1. **[接続文字列]** をクリックします。

	![sendgrid-connection-info-button][sendgrid-connection-info-button]

2. *[接続文字列]* ダイアログ ボックスで、このチュートリアルの後半で使用できるように**パスワード**とユーザー名をコピーします。

	![sendgrid-connection-info][sendgrid-connection-info]

	電子メール配信を設定するには、**[管理]** ボタンをクリックします。これで、SendGrid のコントロール パネルにリダイレクトされます。

	![sendgrid-control-panel][sendgrid-control-panel]

	SendGrid の概要については、[SendGrid の概要に関するページ][]を参照してください。

<!--images-->

[command-bar-new]: ./media/sendgrid-sign-up/sendgrid_BAR_NEW.PNG
[sendgrid-store]: ./media/sendgrid-sign-up/sendgrid_offerings_store.png
[store-screen-2]: ./media/sendgrid-sign-up/sendgrid_store_scrn2.png
[store-screen-3]: ./media/sendgrid-sign-up/sendgrid_store_scrn3.png
[sendgrid-purchasing-message]: ./media/sendgrid-sign-up/sendgrid_purchasing_message.png
[sendgrid-add-on-dashboard]: ./media/sendgrid-sign-up/sendgrid_add-on_dashboard.png
[sendgrid-connection-info]: ./media/sendgrid-sign-up/sendgrid_connection_info.png
[sendgrid-connection-info-button]: ./media/sendgrid-sign-up/sendgrid_connection_info_button.png
[sendgrid-control-panel]: ./media/sendgrid-sign-up/sendgrid_control_panel.png

<!--Links-->

[SendGrid の特徴]: http://sendgrid.com/features
[Azure 管理ポータル]: https://manage.windowsazure.com
[SendGrid の概要に関するページ]: http://sendgrid.com/docs
[SendGrid のプロビジョニング プロセス]: https://support.sendgrid.com/hc/articles/200181628-Why-is-my-account-being-provisioned-
[すべての API]: https://sendgrid.com/docs/API_Reference/index.html

<!---HONumber=Oct15_HO3-->