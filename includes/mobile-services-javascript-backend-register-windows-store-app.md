

1. アプリケーションをまだ登録していない場合は、Windows ストア アプリのデベロッパー センターで[アプリ送信のページ]に移動し、Microsoft アカウントでログインして、**[アプリ名]** をクリックします。

   	![](./media/mobile-services-javascript-backend-register-windows-store-app/mobile-services-submit-win8-app.png)

2. **[アプリケーション名]** にアプリケーションの名前を入力し、**[アプリケーション名の予約]** をクリックして、**[保存]** をクリックします。

   	![](./media/mobile-services-javascript-backend-register-windows-store-app/mobile-services-win8-app-name.png)

   	これでアプリケーションの新しい Windows ストア登録が作成されます。

3. Visual Studio 2012 Express for Windows 8 で、チュートリアル「[モバイル サービスの使用]」を実行したときに作成したプロジェクトを開きます。

4. ソリューション エクスプローラーでプロジェクトを右クリックし、**[ストア]**、**[アプリケーションをストアと関連付ける]** の順にクリックします。

  	![](./media/mobile-services-javascript-backend-register-windows-store-app/mobile-services-store-association.png)

   	**アプリケーションを Windows ストアと関連付ける**ウィザードが表示されます。

5. ウィザードで **[サインイン]** をクリックし、Microsoft アカウントでログインします。

6. 手順 2. で登録したアプリケーションを選択し、**[次へ]**、**[関連付け]** の順にクリックします。

   	![](./media/mobile-services-javascript-backend-register-windows-store-app/mobile-services-select-app-name.png)

   	この操作により、必要な Windows ストア登録情報がアプリケーション マニフェストに追加されます。

7. 新しいアプリケーションに対応する Windows デベロッパー センター ページに戻り、**[サービス]** をクリックします。

   	![](./media/mobile-services-javascript-backend-register-windows-store-app/mobile-services-win8-edit-app.png) 

8. [サービス] ページで **[Azure のモバイル サービス]** の **[Live サービス サイト]** をクリックします。

	![](./media/mobile-services-javascript-backend-register-windows-store-app/mobile-services-win8-edit2-app.png)

9. **[サービスの認証]** をクリックし、**[クライアント シークレット]** と **[パッケージ セキュリティ ID (SID)]** の値をメモしておきます。

   	![](./media/mobile-services-javascript-backend-register-windows-store-app/mobile-services-win8-app-push-auth.png)

    <div class="dev-callout"><b>セキュリティに関する注意</b>
	<p>クライアント シークレットとパッケージ SID は、重要なセキュリティ資格情報です。これらの機密情報を、他のユーザーと共有することや、アプリケーションで配信することを避けてください。</p>
    </div> 

10. [Azure 管理ポータル]にログオンし、**[モバイル サービス]** をクリックして、アプリケーションをクリックします。

   	![](./media/mobile-services-javascript-backend-register-windows-store-app/mobile-services-selection.png)

11. **[プッシュ]** タブをクリックし、**[拡張プッシュの有効化]** をクリックしてから、**[はい]** をクリックして構成の変更を受け入れます。


	![](./media/mobile-services-javascript-backend-register-windows-store-app/mobile-enable-enhanced-push.png)

	この結果、モバイル サービスの構成が更新され、通知ハブによって提供される拡張プッシュ通知機能を使用するようになります。いくつかの通知ハブは、お客様の有料モバイル サービスと組み合わせた状況で、利用が無料になります。詳細については、「[モバイル サービスの料金詳細](http://go.microsoft.com/fwlink/p/?LinkID=311786)」を参照してください。

    <div class="dev-callout"><b>重要</b>
	<p>この操作は、プッシュの資格情報をリセットし、スクリプト内にあるプッシュ メソッドの動作を変更します。これらの変更を元に戻すことはできません。運用環境のモバイル サービスに通知ハブを追加する目的で、このメソッドを使用しないでください。運用環境のモバイル サービスで拡張プッシュ通知を有効にする方法については、<a href="http://go.microsoft.com/fwlink/p/?LinkId=391951">このガイダンス</a>を参照してください。</p>
    </div>

12. 手順 4. で WNS から取得した **[クライアント シークレット]** と **[パッケージ SID]** の値を入力して、**[保存]** をクリックします。

   	![](./media/mobile-services-javascript-backend-register-windows-store-app/mobile-push-tab.png)

	>[WACOM.NOTE]ポータルの **[プッシュ]** タブで拡張プッシュ通知に対応する WNS の資格情報を設定した場合は、アプリに対して通知ハブを構成する目的で、それらの資格情報が通知ハブと共有されます。






[モバイル サービスの使用]: /ja-jp/documentation/articles/mobile-services-windows-store-get-started/
[アプリ送信のページ]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Azure 管理ポータル]: https://manage.windowsazure.com/

