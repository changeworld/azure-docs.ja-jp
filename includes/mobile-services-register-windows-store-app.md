1.  アプリケーションをまだ登録していない場合は、Windows ストア アプリのデベロッパー センターで[アプリの提出のページ][アプリの提出のページ]に移動し、Microsoft アカウントでログインして、**[アプリの名前]** をクリックします。

    ![][0]

2.  **[アプリ名]** にアプリケーションの名前を入力し、**[アプリの名前の予約]** をクリックして、**[保存]** をクリックします。

    ![][1]

    これでアプリケーションの新しい Windows ストア登録が作成されます。

3.  Visual Studio で、チュートリアル「[モバイル サービスの使用][モバイル サービスの使用]」を実行したときに作成したプロジェクトを開きます。

4.  ソリューション エクスプローラーでプロジェクトを右クリックし、**[ストア]**、**[アプリケーションをストアと関連付ける]** の順にクリックします。

    ![][2]

    **アプリケーションを Windows ストアと関連付ける**ウィザードが表示されます。

5.  ウィザードで **[サインイン]** をクリックし、Microsoft アカウントでログインします。

6.  ステップ 2. で登録したアプリケーションを選択し、**[次へ]**、**[関連付け]** の順にクリックします。

    ![][3]

    この操作により、必要な Windows ストア登録情報がアプリケーション マニフェストに追加されます。

7.  新しいアプリケーションの Windows デベロッパー センター ページに戻り、**[サービス]** をクリックします。

    ![][4]

8.  [サービス] ページで **[Azure のモバイル サービス]** の **[Live サービス サイト]** をクリックします。

    ![][5]

9.  **[アプリ設定]** の **[クライアント ID]**、**[クライアント シークレット]**、**[パッケージ セキュリティ ID (SID)]** の値をメモしておきます。

    ![][6]

    > [WACOM.NOTE] クライアント シークレットとパッケージ SID は、重要なセキュリティ資格情報です。これらの機密情報は、他のユーザーと共有したり、アプリケーションで配信したりしないでください。

10. (オプション) **[API 設定]** をクリックし、**[高度なリダイレクト セキュリティ]** をオンにし、**[リダイレクト URL]** の `https://<mobile_service>.azure-mobile.net/login/microsoftaccount` で値を設定し、**[保存]** をクリックします。

    ![][7]

    これで、アプリでの Microsoft アカウント認証が可能になります。

11. [Azure の管理ポータル][Azure の管理ポータル]にログオンし、**[モバイル サービス]** をクリックして、アプリケーションをクリックします。

    ![][8]

12. **[プッシュ]** タブをクリックし、ステップ 4. で WNS から取得した **[クライアント シークレット]** と **[パッケージ SID]** の値を入力して、**[保存]** をクリックします。

    ![][9]

13. **[ID]** タブをクリックします。前のステップで入力した **[クライアント シークレット]** と **[パッケージ SID]** の値が既に設定されていることに注意してください。先ほどのメモのとおりに **[クライアント ID]** を入力し、**[保存]** をクリックします。

    ![][10]

これで、アプリケーションで認証に Microsoft アカウントを使用する準備ができました。

 
 


  [アプリの提出のページ]: http://go.microsoft.com/fwlink/p/?LinkID=266582
  [0]: ./media/mobile-services-register-windows-store-app/mobile-services-submit-win8-app.png
  [1]: ./media/mobile-services-register-windows-store-app/mobile-services-win8-app-name.png
  [モバイル サービスの使用]: /ja-jp/develop/mobile/tutorials/get-started/#create-new-service
  [2]: ./media/mobile-services-register-windows-store-app/mobile-services-store-association.png
  [3]: ./media/mobile-services-register-windows-store-app/mobile-services-select-app-name.png
  [4]: ./media/mobile-services-register-windows-store-app/mobile-services-win8-edit-app.png
  [5]: ./media/mobile-services-register-windows-store-app/mobile-services-win8-edit2-app.png
  [6]: ./media/mobile-services-register-windows-store-app/mobile-services-win8-app-push-auth.png
  [7]: ./media/mobile-services-register-windows-store-app/mobile-services-win8-app-push-auth-2.png
  [Azure の管理ポータル]: https://manage.windowsazure.com/
  [8]: ./media/mobile-services-register-windows-store-app/mobile-services-selection.png
  [9]: ./media/mobile-services-register-windows-store-app/mobile-push-tab.png
  [10]: ./media/mobile-services-register-windows-store-app/mobile-services-identity-tab.png
