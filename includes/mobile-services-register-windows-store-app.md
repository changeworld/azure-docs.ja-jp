
1. アプリケーションをまだ登録していない場合は、Windows ストア アプリのデベロッパー センターで[アプリの提出のページ]に移動し、Microsoft アカウントでログインして、**[アプリの名前]** をクリックします。

   	![][0]

2. **[アプリ名]** にアプリケーションの名前を入力し、**[アプリの名前の予約]** をクリックして、**[保存]** をクリックします。

   	![][1]

   	これでアプリケーションの新しい Windows ストア登録が作成されます。

3. Visual Studio 2012 Express for Windows 8 で、チュートリアル「[モバイル サービスの使用]」を実行したときに作成したプロジェクトを開きます。

4. ソリューション エクスプローラーでプロジェクトを右クリックし、**[ストア]**、**[アプリケーションをストアと関連付ける]** の順にクリックします。

  	![][2]

   	**アプリケーションを Windows ストアと関連付ける**ウィザードが表示されます。

5. ウィザードで **[サインイン]** をクリックし、Microsoft アカウントでログインします。

6. ステップ 2. で登録したアプリケーションを選択し、**[次へ]**、**[関連付け]** の順にクリックします。

   	![][3]

   	この操作により、必要な Windows ストア登録情報がアプリケーション マニフェストに追加されます。

7. 新しいアプリケーションの Windows デベロッパー センター ページに戻り、**[サービス]** をクリックします。

   	![][4] 

8. [サービス] ページで **[Windows Azure のモバイル サービス]** の **[Live サービス サイト]** をクリックします。

	![][5]

9. **[サービスの認証]** をクリックし、**[クライアント シークレット]** と **[パッケージ セキュリティ ID (SID)]** の値をメモしておきます。

   	![][6]

    >[WACOM.NOTE]クライアント シークレットとパッケージ SID は、重要なセキュリティ資格情報です。これらの機密情報は、他のユーザーと共有したり、アプリケーションで配信したりしないでください。

10. [Windows Azure の管理ポータル]にログオンし、**[モバイル サービス]** をクリックして、アプリケーションをクリックします。

   	![][7]

11. **[プッシュ]** タブをクリックし、ステップ 4. で WNS から取得した **[クライアント シークレット]** と **[パッケージ SID]** の値を入力して、**[保存]** をクリックします。

   	![][8]

<!-- Anchors. -->

<!-- Images. -->
[0]: ./media/mobile-services-register-windows-store-app/mobile-services-submit-win8-app.png
[1]: ./media/mobile-services-register-windows-store-app/mobile-services-win8-app-name.png
[2]: ./media/mobile-services-register-windows-store-app/mobile-services-store-association.png
[3]: ./media/mobile-services-register-windows-store-app/mobile-services-select-app-name.png
[4]: ./media/mobile-services-register-windows-store-app/mobile-services-win8-edit-app.png
[5]: ./media/mobile-services-register-windows-store-app/mobile-services-win8-edit2-app.png
[6]: ./media/mobile-services-register-windows-store-app/mobile-services-win8-app-push-auth.png
[7]: ./media/mobile-services-register-windows-store-app/mobile-services-selection.png
[8]: ./media/mobile-services-register-windows-store-app/mobile-push-tab.png

<!-- URLs. -->
[Get started with Mobile Services (モバイル サービスの使用)]: /ja-jp/develop/mobile/tutorials/get-started/#create-new-service
[アプリケーションの提出に関するページ]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Windows Azure の管理ポータル]: https://manage.windowsazure.com/

