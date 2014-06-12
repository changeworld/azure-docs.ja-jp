最初に、Visual Studio 2013 でプッシュ通知の追加ウィザードを使用して、アプリケーションを Windows ストアに登録します。次に、プッシュ通知を有効にするようにモバイル サービスを構成し、デバイス チャネルを登録するためのコードをアプリケーションに追加します。

0.この操作をまだ行っていない場合は、「[Import your publishsettings file in Visual Studio 2013 (Visual Studio 2013 での publishsettings ファイルのインポート)]」の手順を実行して、Visual Studio に publishersettings ファイルをインポートします。

	既に Visual Studio を使用して Azure サブスクリプションでモバイル サービスを作成または管理している場合は、この操作を行う必要はありません。

1. Visual Studio 2013 で、ソリューション エクスプローラーを開き、プロジェクトを右クリックして、**[追加]**、**[プッシュ通知]** の順にクリックします。

	![mobile-add-push-notifications-vs2013](../includes/media/mobile-services-create-new-push-vs2013/mobile-add-push-notifications-vs2013.png)

	これにより、プッシュ通知の追加ウィザードが開始されます。

2. **[次へ]** をクリックし、Windows ストア アカウントにサインインします。次に、**[新しいアプリケーション名の予約]** に名前を指定し、**[予約]** をクリックします。

	![mobile-add-push-notifications-vs2013-2](../includes/media/mobile-services-create-new-push-vs2013/mobile-add-push-notifications-vs2013-2.png) 

	これで、新しいアプリケーションの登録が作成されます。

3. **[アプリケーション名]** の一覧で新しい登録をクリックし、**[次へ]** をクリックします。

	![mobile-add-push-notifications-vs2013-3](../includes/media/mobile-services-create-new-push-vs2013/mobile-add-push-notifications-vs2013-3.png)

4. **[サービスの選択]** ダイアログ ボックスで、「[モバイル サービスの使用]」または「[モバイル サービスでのデータの使用]」の最後に作成したモバイル サービスの名前をクリックし、**[次へ]**、**[完了]** の順にクリックします。

	
	モバイル サービスが更新され、アプリケーションのパッケージ SID とクライアント シークレットが登録され、新しい **channels** テーブルが作成されます。これで、Windows プッシュ通知サービス (WNS) を使用するようにモバイル サービスが構成され、アプリケーションにプッシュ通知を送信することができます。

	<div class="dev-callout"><b>注</b>
		<p>モバイル サービスに接続するようにアプリケーションがまだ構成されていない場合は、「<strong>データの使用</strong>」で説明しているタスクと同じ構成タスクがウィザードによって実行されます。</p>
	</div>

<!-- URLs. -->
[モバイル サービスの使用]: /ja-jp/develop/mobile/tutorials/get-started/
[モバイル サービスでのデータの使用]: /ja-jp/develop/mobile/tutorials/get-started-with-data-dotnet/
[Import your publishsettings file in Visual Studio 2013 (Visual Studio 2013 での publishsettings ファイルのインポート)]: /ja-jp/documentation/articles/mobile-services-windows-how-to-import-publishsettings/

