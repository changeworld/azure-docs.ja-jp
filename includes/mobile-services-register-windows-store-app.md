
1. アプリケーションをまだ登録していない場合は、Windows ストア アプリのデベロッパー センターで[アプリの提出のページ]に移動し、Microsoft アカウントでログインして、**[アプリの名前]** をクリックします。

   	![](./media/mobile-services-register-windows-store-app/mobile-services-submit-win8-app.png)

2. [**一意の名前を予約することにより新しいアプリを作成する**] をクリックし、[**続行**] をクリックし、[**アプリ名**] にアプリケーションの名前を入力し、[**アプリの名前の予約**] をクリックし、[**保存**] をクリックします。

   	![](./media/mobile-services-register-windows-store-app/mobile-services-win8-app-name.png)

   	これでアプリケーションの新しい Windows ストア登録が作成されます。

3. Visual Studio で、チュートリアル「[モバイル サービスの使用]」を実行したときに作成したプロジェクトを開きます。

4. ソリューション エクスプローラーで Windows ストア アプリ プロジェクトを右クリックし、[**ストア**]、[**アプリケーションをストアと関連付ける**] の順にクリックします。

  	![](./media/mobile-services-register-windows-store-app/mobile-services-store-association.png)

   	**アプリケーションを Windows ストアと関連付ける**ウィザードが表示されます。

5. ウィザードで、[**サインイン**] をクリックし、Microsoft アカウントでログインし、手順 2 で登録したアプリケーションを選択し、[**次へ**] をクリックし、[**関連付け**] をクリックします。

   	この操作により、必要な Windows ストア登録情報がアプリケーション マニフェストに追加されます。

6. (省略可能) ユニバーサル Windows アプリケーションの場合、Windows Phone ストア プロジェクトの手順 4 および 5 を繰り返します。

6. 新しいアプリケーションの Windows デベロッパー センター ページに戻り、**[サービス]** をクリックします。

   	![](./media/mobile-services-register-windows-store-app/mobile-services-win8-edit-app.png)

7. [サービス] ページで **[Azure のモバイル サービス]** の **[Live サービス サイト]** をクリックします。

	![](./media/mobile-services-register-windows-store-app/mobile-services-win8-edit2-app.png)

8. [**API 設定**] をクリックし、[**モバイル アプリまたはデスクトップ クライアント アプリ**] を選択し、モバイル サービス URL を [**ターゲット ドメイン**] として指定し、[**リダイレクト URL**] の `https://<mobile_service>.azure-mobile.net/login/microsoftaccount/` の値を指定し、[**保存**] をクリックします。

	![](./media/mobile-services-register-windows-store-app/mobile-services-win8-app-push-auth-2.png)

9. **[アプリ設定]** の **[クライアント ID]**、**[クライアント シークレット]**、**[パッケージ セキュリティ ID (SID)]** の値をメモしておきます。

   	![](./media/mobile-services-register-windows-store-app/mobile-services-win8-app-push-auth.png)

    >[AZURE.NOTE]クライアント シークレットとパッケージ SID は、重要なセキュリティ資格情報です。これらの機密情報は、他のユーザーと共有したり、アプリケーションで配信したりしないでください。

10. [Azure の管理ポータル]にログオンし、**[モバイル サービス]** をクリックして、アプリケーションをクリックします。

11. [**ID**] タブをクリックし、手順 4 で WNS から取得した [**クライアント シークレット**] および [**パッケージ SID**] の値を入力し、[**保存**] をクリックします。

   	![](./media/mobile-services-register-windows-store-app/mobile-push-tab.png)

13. **[ID]** タブをクリックします。前のステップで入力した **[クライアント シークレット]** と **[パッケージ SID]** の値が既に設定されていることに注意してください。先ほどのメモのとおりに **[クライアント ID]** を入力し、**[保存]** をクリックします。

   	![](./media/mobile-services-register-windows-store-app/mobile-services-identity-tab.png)
 
これで、アプリケーションで認証に Microsoft アカウントを使用する準備ができました。

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->
[モバイル サービスの使用]: /develop/mobile/tutorials/get-started/#create-new-service
[アプリの提出のページ]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Azure の管理ポータル]: https://manage.windowsazure.com/

<!---HONumber=July15_HO4-->