<properties linkid="develop-net-tutorials-push-notifications-to-users-wp8" urlDisplayName="ユーザーへのプッシュ通知 (WP8)" pageTitle="ユーザーへのプッシュ通知 (Windows Phone) | モバイル デベロッパー センター" metaKeywords="" description="モバイル サービスを使用して Windows Phone アプリケーションのユーザーにプッシュ通知を送信する方法について説明します。" metaCanonical="" services="" documentationCenter="" title="モバイル サービスによるユーザーへのプッシュ通知" authors="glenga" solutions="" manager="" editor="" />




#モバイル サービスによるユーザーへのプッシュ通知
<div class="dev-center-tutorial-selector sublanding">
	<a href="/ja-jp/develop/mobile/tutorials/push-notifications-to-users-wp8" title="Windows Phone" class="current">Windows Phone</a><a href="/ja-jp/develop/mobile/tutorials/push-notifications-to-users-ios" title="iOS">iOS</a><a href="/ja-jp/develop/mobile/tutorials/push-notifications-to-users-android" title="Android">Android</a>
</div>


このトピックは、Microsoft Push Notification Service (MPNS) チャネル URI を格納する新しいテーブルを追加して、[1 つ前のプッシュ通知に関するチュートリアル][Get started with push notifications]を拡張したものです。これらのチャネルを使用すると、Windows Phone 8 アプリケーションのユーザーにプッシュ通知を送信することができます。

このチュートリアルでは、アプリケーションでプッシュ通知を更新するための次の手順について説明します。

1. [Channel テーブルを作成する]
2. [アプリケーションを更新する]
3. [サーバー スクリプトを更新する]
4. [プッシュ通知の動作を確認する]

このチュートリアルは、モバイル サービスのクイック スタートと、1 つ前のチュートリアル「[モバイル サービスでのプッシュ通知の使用]」の内容を前提としています。このチュートリアルを開始する前に、「[モバイル サービスでのプッシュ通知の使用]」を完了している必要があります。

## <a name="create-table"></a>新しいテーブルを作成する

1. [Azure の管理ポータル]にログインし、**[モバイル サービス]** をクリックして、アプリケーションをクリックします。

   	![][0]

2. **[データ]** タブをクリックし、**[作成]** をクリックします。

   	![][1]

   	**[新しいテーブルの作成]** ダイアログ ボックスが表示されます。

3. すべてのアクセス許可について既定の **[アプリケーション キーを持つユーザー]** 設定をそのままにし、**[テーブル名]** に「_Channel_」と入力してチェック ボタンをクリックします。

   	![][2]

  	**Channel** テーブルが作成されます。このテーブルには、項目データとは別にプッシュ通知を送信するために使用されるチャネル URI が格納されます。

次は、**TodoItem** テーブルの代わりにこの新しいテーブルにデータを格納するようにプッシュ通知アプリケーションを変更します。

## <a name="update-app"></a>アプリケーションを更新する

1. Visual Studio 2012 Express for Windows Phone で、[プッシュ通知の使用に関するチュートリアル]で使用したプロジェクトを開き、MainPage.xaml.cs ファイルを開いて、**TodoItem** クラスから **Channel** プロパティを削除します。その結果、次のようになります。

        public class TodoItem
        {
        	public int Id { get; set; }

	       	[JsonProperty(PropertyName = "text")]
	        public string Text { get; set; }
	
	        [JsonProperty(PropertyName = "complete")]
	        public bool Complete { get; set; }
        }

2. **ButtonSave_Click** イベント ハンドラー メソッドを、次のようにこのメソッドの元のバージョンに置き換えます。

        private void ButtonSave_Click(object sender, RoutedEventArgs e)
        {
            var todoItem = new TodoItem { Text = TextInput.Text };
            InsertTodoItem(todoItem);
        }

3. 新しい **Channel** クラスを作成する次のコードを追加します。

	    public class Channel
	    {
	        public int Id { get; set; }
	
	        [JsonProperty(PropertyName = "uri")]
	        public string Uri { get; set; }
	    }

4. App.xaml.cs ファイルを開き、**AcquirePushChannel** メソッドを次のコードに置き換えます。

        private void AcquirePushChannel()
        {
            CurrentChannel = HttpNotificationChannel.Find("MyPushChannel");

            if (CurrentChannel == null)
            {
                CurrentChannel = new HttpNotificationChannel("MyPushChannel");
                CurrentChannel.Open();
                CurrentChannel.BindToShellTile();
            }
                  
           IMobileServiceTable<Channel> channelTable = App.MobileService.GetTable<Channel>();
           var channel = new Channel { Uri = CurrentChannel.ChannelUri.ToString() };
           channelTable.InsertAsync(channel);
        }

     このコードでは、チャネルが Channel テーブルに挿入されます。

##<a name="update-scripts"></a>サーバー スクリプトを更新する

1. 管理ポータルで、**[データ]** タブをクリックし、**Channel** テーブルをクリックします。

   	![][3]

2. **[channel]** で、**[スクリプト]** タブをクリックし、**[挿入]** をクリックします。
   
   	![][4]

   	**Channel** テーブルで挿入が発生したときに呼び出される関数が表示されます。

3. insert 関数を次のコードに置き換え、**[保存]** をクリックします。

			function insert(item, user, request) {
				var channelTable = tables.getTable('Channel');
				channelTable
					.where({ uri: item.uri })
					.read({ success: insertChannelIfNotFound });
		        function insertChannelIfNotFound(existingChannels) {
	        	    if (existingChannels.length > 0) {
	            	    request.respond(200, existingChannels[0]);
	        	    } else {
	            	    request.execute();
	        	    }
	    	    }
		    }

   	このスクリプトでは、**Channel** テーブルに同じ URI を持つ既存のチャネルがないかチェックが行われます。insert の実行は、一致するチャネルが見つからなかった場合にのみ、先に進みます。これにより、チャネル レコードの重複が防止されます。

4. **[TodoItem]** をクリックし、**[スクリプト]** タブをクリックして、**[挿入]** を選択します。

   	![][5]

5. insert 関数を次のコードに置き換え、**[保存]** をクリックします。

	    function insert(item, user, request) {
    	    request.execute({
        	    success: function() {
            	    request.respond();
            	    sendNotifications();
        	    }
    	    });

	        function sendNotifications() {
        	    var channelTable = tables.getTable('Channel');
        	    channelTable.read({
            	    success: function(channels) {
                	    channels.forEach(function(channel) {
                    	    push.mpns.sendFlipTile(channel.uri, {
                        	    title: item.text
                    	    }, {
                        	    success: function(pushResponse) {
                            	    console.log("Sent push:", pushResponse);
                        	    }
                    	    });
                	    });
            	    }
        	    });
    	    }
	    }

    この insert スクリプトでは、**Channel** テーブルに格納されているすべてのチャネルにプッシュ通知が (挿入された項目のテキストと共に) 送信されます。

## <a name="test-app"></a>アプリケーションをテストする

1. Visual Studio で、**[ビルド]** メニューの **[ソリューションの配置]** を選択します。

3. **TodoList** または **hello push** という名前のタイルをタップして、アプリケーションを開始します。

  	![][23]

5. アプリケーションで、テキスト ボックスに「hello push again」というテキストを入力し、**[Save]** をクリックします。

   	![][24]

  	これにより、追加された項目を保存するための挿入要求がモバイル サービスに送信されます。

6. **[スタート]** ボタンを押して [スタート] メニューに戻ります。

  	![][25]

  	アプリケーションがプッシュ通知を受信したため、タイルのタイトルが **hello push** になっています。

9. (省略可能) アプリケーションを 2 台のデバイスで同時に実行し、前の手順を繰り返します。

    実行中のアプリケーション インスタンスすべてに通知が送信されます。

## 次のステップ

これで、プッシュ通知の基本について説明するチュートリアルは終了です。次のモバイル サービスのトピックの詳細を確認することをお勧めします。

* [データの使用]
  <br/>モバイル サービスを使用してデータの格納およびクエリを実行する方法について説明します。

* [認証の使用]
  <br/>Windows アカウントを使用してアプリケーションのユーザーを認証する方法について説明します。

* [モバイル サービスのサーバー スクリプト リファレンス]
  <br/>サーバー スクリプトの登録および使用について説明します。

<!-- anchors -->
[Channel テーブルを作成する]: #create-table
[アプリケーションを更新する]: #update-app
[サーバー スクリプトを更新する]: #update-scripts
[プッシュ通知の動作を確認する]: #test-app
[次のステップ]: #next-steps

<!-- Images. -->
[0]: ./media/mobile-services-windows-phone-push-notifications-app-users/mobile-services-selection.png
[1]: ./media/mobile-services-windows-phone-push-notifications-app-users/mobile-create-table.png
[2]: ./media/mobile-services-windows-phone-push-notifications-app-users/mobile-create-channel-table.png
[3]: ./media/mobile-services-windows-phone-push-notifications-app-users/mobile-portal-data-tables-channel.png
[4]: ./media/mobile-services-windows-phone-push-notifications-app-users/mobile-insert-script-channel.png
[5]: ./media/mobile-services-windows-phone-push-notifications-app-users/mobile-insert-script-push2.png


[23]: ./media/mobile-services-windows-phone-push-notifications-app-users/mobile-quickstart-push4-wp8.png
[24]: ./media/mobile-services-windows-phone-push-notifications-app-users/mobile-quickstart-push5-wp8.png
[25]: ./media/mobile-services-windows-phone-push-notifications-app-users/mobile-quickstart-push6-wp8.png

<!-- URLs. -->
[モバイル サービスのサーバー スクリプト リファレンス]: http://go.microsoft.com/fwlink/p/?LinkId=262293
[モバイル サービスの使用]: /ja-jp/develop/mobile/tutorials/get-started-wp8
[データの使用]: /ja-jp/develop/mobile/tutorials/get-started-with-data-wp8
[認証の使用]: /ja-jp/develop/mobile/tutorials/get-started-with-users-wp8
[プッシュ通知の使用に関するチュートリアル]: /ja-jp/develop/mobile/tutorials/get-started-with-push-wp8

[Azure の管理ポータル]: https://manage.windowsazure.com/

