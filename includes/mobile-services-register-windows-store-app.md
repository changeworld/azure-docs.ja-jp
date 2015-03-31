
1. アプリケーションをまだ登録していない場合は、Windows ストア アプリのデベロッパー センターで[アプリの提出のページ]に移動し、Microsoft アカウントでログインして、**[アプリの名前]** をクリックします。

   	![](./media/mobile-services-register-windows-store-app/mobile-services-submit-win8-app.png)

2. **[アプリ名]** にアプリケーションの名前を入力し、**[アプリの名前の予約]** をクリックして、**[保存]** をクリックします。

   	![](./media/mobile-services-register-windows-store-app/mobile-services-win8-app-name.png)

   	これでアプリケーションの新しい Windows ストア登録が作成されます。

3. Visual Studio で、チュートリアル「[Mobile Services の使用]」を実行したときに作成したプロジェクトを開きます。

4. ソリューション エクスプローラーでプロジェクトを右クリックし、**[ストア]**、**[アプリケーションをストアと関連付ける]** の順にクリックします。 

  	![](./media/mobile-services-register-windows-store-app/mobile-services-store-association.png)

   	**アプリケーションを Windows ストアと関連付ける**ウィザードが表示されます。

5. ウィザードで **[サインイン]** をクリックし、Microsoft アカウントでログインします。

6. 手順 2. で登録したアプリケーションを選択し、**[次へ]**、**[関連付け]** の順にクリックします。

   	![](./media/mobile-services-register-windows-store-app/mobile-services-select-app-name.png)

   	この操作により、必要な Windows ストア登録情報がアプリケーション マニフェストに追加されます。    

7. 新しいアプリケーションの Windows デベロッパー センター ページに戻り、**[サービス]** をクリックします。 

   	![](./media/mobile-services-register-windows-store-app/mobile-services-win8-edit-app.png) 

8. [サービス] ページで **[Azure のモバイル サービス]** の **[Live サービス サイト]** をクリックします。

	![](./media/mobile-services-register-windows-store-app/mobile-services-win8-edit2-app.png) 

9. **[アプリ設定]** の **[クライアント ID]**、**[クライアント シークレット]**、**[パッケージ セキュリティ ID (SID)]** の値をメモしておきます。 

   	![](./media/mobile-services-register-windows-store-app/mobile-services-win8-app-push-auth.png)

    >[AZURE.NOTE]クライアント シークレットとパッケージ SID は、重要なセキュリティ資格情報です。これらの秘密情報は、他のユーザーと共有したり、アプリケーションで配信したりしないでください。

10. (省略可能) **[API 設定]** をクリックし、**[高度なリダイレクト セキュリティ]** をオンにして、**[リダイレクト URL]** で  `https://<mobile_service>.azure-mobile.net/login/microsoftaccount` の値を設定し、**[保存]** をクリックします。

	![](./media/mobile-services-register-windows-store-app/mobile-services-win8-app-push-auth-2.png)

	これで、アプリでの Microsoft アカウント認証が可能になります。

11. [Azure 管理ポータル] にログオンし、**[モバイル サービス]** をクリックして、アプリケーションをクリックします。

   	![](./media/mobile-services-register-windows-store-app/mobile-services-selection.png)

12. **[プッシュ]** タブをクリックし、ステップ 4. で WNS から取得した **[クライアント シークレット]** と **[パッケージ SID]** の値を入力して、**[保存]** をクリックします。

   	![](./media/mobile-services-register-windows-store-app/mobile-push-tab.png)

13. **[ID]** タブをクリックします。前のステップで入力した **[クライアント シークレット]** と **[パッケージ SID]** の値が既に設定されていることに注意してください。先ほどのメモのとおりに **[クライアント ID]** を入力し、**[保存]** をクリックします。

   	![](./media/mobile-services-register-windows-store-app/mobile-services-identity-tab.png)
 
これで、アプリケーションで認証に Microsoft アカウントを使用する準備ができました。  

<!-- Anchors. -->

<!-- Images. -->
 

<!-- URLs. -->
[Mobile Services の使用]:/develop/mobile/tutorials/get-started/#create-new-service
[アプリの提出のページ]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Azure 管理ポータル]: https://manage.windowsazure.com/

<!--HONumber=47-->
