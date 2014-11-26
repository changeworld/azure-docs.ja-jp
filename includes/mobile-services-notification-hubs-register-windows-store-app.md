1.  アプリケーションをまだ登録していない場合は、Windows ストア アプリのデベロッパー センターで[アプリの提出のページ][アプリの提出のページ]に移動し、Microsoft アカウントでログインして、**[アプリの名前]** をクリックします。

    ![][0]

2.  **[アプリ名]** にアプリケーションの名前を入力し、**[アプリの名前の予約]** をクリックして、**[保存]** をクリックします。

    ![][1]

    これでアプリケーションの新しい Windows ストア登録が作成されます。

3.  Visual Studio で、チュートリアル「**モバイル サービスの使用**」を実行したときに作成したプロジェクトを開きます。

4.  ソリューション エクスプローラーでプロジェクトを右クリックし、**[ストア]**、**[アプリケーションをストアと関連付ける]** の順にクリックします。

    ![][2]

    **アプリケーションを Windows ストアと関連付ける**ウィザードが表示されます。

5.  ウィザードで **[サインイン]** をクリックし、Microsoft アカウントでログインします。

6.  ステップ 2. で登録したアプリケーションを選択し、**[次へ]**、**[関連付け]** の順にクリックします。

    ![][3]

    この操作により、必要な Windows ストア登録情報がアプリケーション マニフェストに追加されます。

7.  (省略可能) 手順 4 ～ 6 を繰り返して、ユニバーサル Windows アプリの Windows Phone ストア プロジェクトも登録します。

8.  新しいアプリケーションの Windows デベロッパー センター ページに戻り、**[サービス]** をクリックします。

    ![][4]

9.  [サービス] ページで **[Azure Mobile Services]** の **[Live サービス サイト]** をクリックします。

    ![][5]

10. **[サービスの認証]** をクリックし、**[クライアント シークレット]** と **[パッケージ セキュリティ ID (SID)]** の値をメモしておきます。

    ![][6]

    <div class="dev-callout"><b>セキュリティに関する注意</b>
<p>クライアント シークレットとパッケージ SID は、重要なセキュリティ資格情報です。これらの機密情報は、他のユーザーと共有したり、アプリケーションで配信したりしないでください。</p>
</div>

11. [Azure の管理ポータル][Azure の管理ポータル]にログオンし、**[モバイル サービス]** をクリックして、アプリケーションをクリックします。

    ![][7]

12. **[プッシュ]** タブをクリックし、WNS から取得した **[クライアント シークレット]** と **[パッケージ SID]** の値を入力して、**[保存]** をクリックします。

    > [WACOM.NOTE] 古いモバイル サービスを使用してこのチュートリアルを実行する場合は、**[プッシュ]** タブの下に、**[拡張プッシュの有効化]** というリンクが表示される場合があります。このリンクをクリックすると、モバイル サービスが更新され、Notification Hubs と統合されます。この変更を元に戻すことはできません。運用環境のモバイル サービスで拡張プッシュ通知を有効にする方法の詳細については、[このガイダンス][このガイダンス]を参照してください。

    ![][8]

    > [WACOM.NOTE]ポータルの **[プッシュ]** タブで拡張プッシュ通知に対応する WNS の資格情報を設定した場合は、アプリに対して通知ハブを構成する目的で、それらの資格情報が Notification Hubs と共有されます。



  [アプリの提出のページ]: http://go.microsoft.com/fwlink/p/?LinkID=266582
  [0]: ./media/mobile-services-notification-hubs-register-windows-store-app/mobile-services-submit-win8-app.png
  [1]: ./media/mobile-services-notification-hubs-register-windows-store-app/mobile-services-win8-app-name.png
  [2]: ./media/mobile-services-notification-hubs-register-windows-store-app/mobile-services-store-association.png
  [3]: ./media/mobile-services-notification-hubs-register-windows-store-app/mobile-services-select-app-name.png
  [4]: ./media/mobile-services-notification-hubs-register-windows-store-app/mobile-services-win8-edit-app.png
  [5]: ./media/mobile-services-javascript-backend-register-windows-store-app/mobile-services-win8-edit2-app.png
  [6]: ./media/mobile-services-notification-hubs-register-windows-store-app/mobile-services-win8-app-push-auth.png
  [Azure の管理ポータル]: https://manage.windowsazure.com/
  [7]: ./media/mobile-services-notification-hubs-register-windows-store-app/mobile-services-selection.png
  [このガイダンス]: http://go.microsoft.com/fwlink/p/?LinkId=391951
  [8]: ./media/mobile-services-notification-hubs-register-windows-store-app/mobile-push-tab.png
