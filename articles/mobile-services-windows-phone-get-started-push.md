<properties pageTitle="プッシュ通知の使用 (Windows Phone) | モバイル デベロッパー センター" metaKeywords="" description="Azure のモバイル サービスを使用して Windows Phone アプリにプッシュ通知を送信する方法について説明します。" metaCanonical="" services="" documentationCenter="Mobile" title="モバイル サービスでのプッシュ通知の使用" authors=""  solutions="" writer="glenga" manager="" editor=""  />


# モバイル サービスでのプッシュ通知の使用

<div class="dev-center-tutorial-selector sublanding"><a href="/ja-jp/documentation/articles/mobile-services-windows-store-dotnet-get-started-push" title="Windows ストア C#" >Windows ストア C#</a><a href="/ja-jp/documentation/articles/mobile-services-windows-store-javascript-get-started-push" title="Windows ストア JavaScript">Windows ストア JavaScript</a><a href="/ja-jp/documentation/articles/mobile-services-windows-phone-get-started-push" title="Windows Phone" class="current">Windows Phone</a><a href="/ja-jp/documentation/articles/mobile-services-ios-get-started-push" title="iOS">iOS</a><a href="/ja-jp/documentation/articles/mobile-services-android-get-started-push" title="Android">Android</a><a href="/ja-jp/documentation/articles/partner-xamarin-mobile-services-ios-get-started-push" title="Xamarin.iOS">Xamarin.iOS</a><a href="/ja-jp/documentation/articles/partner-xamarin-mobile-services-android-get-started-push" title="Xamarin.Android">Xamarin.Android</a></div>

<div class="dev-center-tutorial-subselector"><a href="/ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push/" title=".NET バックエンド">.NET バックエンド</a> | <a href="/ja-jp/documentation/articles/mobile-services-windows-phone-get-started-push/"  title="JavaScript バックエンド" class="current">JavaScript バックエンド</a>
</div>

このトピックでは、Azure のモバイル サービスを使用して Windows Phone 8 アプリにプッシュ通知を送信する方法について説明します。
このチュートリアルでは、Microsoft Push Notification Service (MPNS) を使用したプッシュ通知をクイック スタート プロジェクトに追加します。完了すると、モバイル サービスは、レコードが挿入されるたびにプッシュ通知を送信します。

>[WACOM.NOTE]モバイル サービスは Azure 通知ハブに統合され、テンプレート、複数のプラットフォーム、スケールなど、追加のプッシュ通知機能をサポートするようになりました。この統合機能は、現在プレビュー段階にあります。詳細については、「[通知ハブの使用](/ja-jp/documentation/articles/mobile-services-javascript-backend-windows-phone-get-started-push/)」を参照してください。

このチュートリアルでは、プッシュ通知を有効にするための、次の基本的な手順について説明します。

1. [Registrations テーブルを作成する]
2. [アプリケーションにプッシュ通知を追加する]
3. [プッシュ通知を送信するようにスクリプトを更新する]
4. [データを挿入して通知を受け取る]

このチュートリアルには、[Visual Studio 2012 Express for Windows Phone] 以降のバージョンが必要です。

このチュートリアルは、モバイル サービスのクイック スタートに基づいています。このチュートリアルを開始する前に、「[モバイル サービスの使用]」を完了している必要があります。

   >[WACOM.NOTE]ユーザーあたり 500 通を超えるメッセージを毎日送信する場合は、通知ハブを使用する必要があります。詳細については、「<a href="/ja-jp/manage/services/notification-hubs/getting-started-windows-dotnet/">通知ハブの使用</a>」を参照してください。

## <a name="create-table"></a>新しいテーブルを作成する

[WACOM.INCLUDE [mobile-services-create-new-push-table](../includes/mobile-services-create-new-push-table.md)]

<h2><a name="add-push"></a><span class="short-header">プッシュ通知の追加</span>アプリケーションにプッシュ通知を追加する</h2>
		
1. Visual Studio で、MainPage.xaml.cs プロジェクト ファイルを開き、次に示す新しい **Registrations** クラスを作成するコードを追加します。

	    public class Registrations
	    {
	        public string Id { get; set; }
	
	        [JsonProperty(PropertyName = "handle")]
	        public string Handle { get; set; }
	    }
	
	Handle プロパティは、チャネル URI を格納するために使用します。

2. App.xaml.cs ファイルを開き、次の using ステートメントを追加します。

        using Microsoft.Phone.Notification;

3. 次のコードを App.xaml.cs に追加します。
	
        public static HttpNotificationChannel CurrentChannel { get; private set; }

		private void AcquirePushChannel()
        {
            CurrentChannel = HttpNotificationChannel.Find("MyPushChannel");

            if (CurrentChannel == null)
            {
                CurrentChannel = new HttpNotificationChannel("MyPushChannel");
                CurrentChannel.Open();
                CurrentChannel.BindToShellTile();
            }
                  
	       IMobileServiceTable<Registrations> registrationsTable = App.MobileService.GetTable<Registrations>();
	       var registration = new Registrations { Handle = CurrentChannel.Uri };
	       await registrationsTable.InsertAsync(registration);
        }

   	このコードでは、プッシュ通知サブスクリプションのチャネルが取得および保存され、アプリケーションの既定のタイルにバインドされます。

	<div class="dev-callout"><b>メモ</b>
		<p>このチュートリアルでは、モバイル サービスにより、フリップ タイル通知がデバイスに送信されます。トースト通知を送信する場合は、チャネルの <strong>BindToShellToast</strong> メソッドを呼び出す必要があります。トースト通知とタイル通知の両方をサポートするには、<strong>BindToShellTile</strong> と <strong>BindToShellToast</strong> の両方を呼び出します。</p>
	</div>
    
4. App.xaml.cs 内で、**Application_Launching** イベント ハンドラーの先頭に、次に示す新しい **AcquirePushChannel** メソッドへの呼び出しを追加します。

        AcquirePushChannel();

   	これにより、アプリケーションが起動されるたびに必ず **CurrentChannel** プロパティが初期化されます。


5.	ソリューション エクスプローラーで、**[プロパティ]** を展開して WMAppManifest.xml ファイルを開き、**[機能]** タブをクリックして、**ID___CAP___PUSH_NOTIFICATION** 機能がオンであることを確認します。

   	![][1]

   	これにより、アプリケーションでプッシュ通知の受信が有効になります。

<h2><a name="update-scripts"></a><span class="short-header">挿入スクリプトの更新</span>管理ポータルで登録されている挿入スクリプトを更新する</h2>

[WACOM.INCLUDE [mobile-services-update-registrations-script](../includes/mobile-services-update-registrations-script.md)]

4. **[TodoItem]** をクリックし、**[スクリプト]** タブをクリックして、**[挿入]** を選択します。

   	![][10]

3. insert 関数を次のコードに置き換え、**[保存]** をクリックします。

	    function insert(item, user, request) {
    	    request.execute({
        	    success: function() {
            	    request.respond();
            	    sendNotifications();
        	    }
    	    });

	        function sendNotifications() {
        	    var registrationsTable = tables.getTable('Registrations');
        	    registrationsTable.read({
            	    success: function(registrations) {
                	    registrations.forEach(function(registration) {
                    	    push.mpns.sendFlipTile(registration.uri, {
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

    この insert スクリプトでは、**Registrations** テーブルに格納されているすべてのチャネルにプッシュ通知が (挿入された項目のテキストと共に) 送信されます。

<h2><a name="test"></a><span class="short-header">アプリケーションのテスト</span>アプリケーションでプッシュ通知をテストする</h2>

1. Visual Studio で、**[ビルド]** メニューの **[ソリューションの配置]** を選択します。

2. エミュレーターで、左方向へスワイプしてインストールされているアプリケーションの一覧を表示し、新しい **TodoList** アプリケーションを探します。

3. アプリケーション アイコンを長押しして、コンテキスト メニューの **[スタート画面に追加]** を選択します。

  	![][2]

  	これにより、**TodoList** という名前のタイルがスタート メニューに固定されます。

4. **TodoList** という名前のタイルをタップして、アプリケーションを開始します。

  	![][3]

5. そのアプリケーションで、テキスト ボックスに「hello push」というテキストを入力し、**[Save]** をクリックします。

   	![][4]

  	これにより、追加された項目を保存するための挿入要求がモバイル サービスに送信されます。

6. **[スタート]** ボタンを押してスタート メニューに戻ります。

  	![][5]

  	アプリケーションがプッシュ通知を受信したため、タイルのタイトルが **hello push** になっています。

## <a name="next-steps"> </a>次のステップ

このチュートリアルでは、モバイル サービスに用意されている基本的なプッシュ通知機能を示します。より高度な機能 (たとえば、クロスプラットフォーム通知の送信、サブスクリプション ベースのルーティング、大ボリューム) を必要とする場合は、モバイル サービスで Azure 通知ハブを使用することを検討してください。詳細については、通知ハブに関する次のトピックのいずれかを参照してください。

+ [通知ハブの使用]
  <br/>Windows ストア アプリで通知ハブを活用する方法について説明します。

+ [Azure 通知ハブ]
	<br/>プッシュ通知を作成して複数のプラットフォームのユーザーにプッシュ通知を送信する方法について説明します。

+ [登録者への通知の送信]
	<br/>ユーザーが興味のあるカテゴリに関してプッシュ通知を登録して、プッシュ通知を受信できるようにする方法について説明します。

<!--+ [ユーザーへの通知の送信]
	<br/>モバイル サービスから任意のデバイスの特定のユーザーにプッシュ通知を送信する方法について説明します。

+ [ユーザーへのクロスプラットフォーム通知の送信]
	<br/>テンプレートを使用して、バックエンドでプラットフォームに固有のペイロードを作成する必要なくモバイル サービスからプッシュ通知を送信する方法について説明します。
-->

次のモバイル サービスのトピックの詳細を確認することをお勧めします。

* [データの使用]
  <br/>モバイル サービスを使用してデータの格納およびクエリを実行する方法について説明します。

* [認証の使用]
  <br/>Windows アカウントを使用してアプリケーションのユーザーを認証する方法について説明します。

* [モバイル サービスのサーバー スクリプト リファレンス]
  <br/>サーバー スクリプトの登録および使用について説明します。

* [モバイル サービス .NET の使用方法の概念リファレンス]
  <br/>.NET でモバイル サービスを使用する方法について説明します

<!-- Anchors. -->
[Registrations テーブルを作成する]: #create-table
[プッシュ通知を送信するようにスクリプトを更新する]: #update-scripts
[アプリケーションにプッシュ通知を追加する]: #add-push
[データを挿入して通知を受け取る]: #test
[次のステップ]:#next-steps

<!-- Images. -->
[1]: ./media/mobile-services-windows-phone-get-started-push/mobile-app-enable-push-wp8.png
[2]: ./media/mobile-services-windows-phone-get-started-push/mobile-quickstart-push1-wp8.png
[3]: ./media/mobile-services-windows-phone-get-started-push/mobile-quickstart-push2-wp8.png
[4]: ./media/mobile-services-windows-phone-get-started-push/mobile-quickstart-push3-wp8.png
[5]: ./media/mobile-services-windows-phone-get-started-push/mobile-quickstart-push4-wp8.png
[10]: ./media/mobile-services-windows-phone-get-started-push/mobile-insert-script-push2.png



<!-- URLs. -->
[モバイル サービス SDK]: https://go.microsoft.com/fwLink/p/?LinkID=268375
[Visual Studio 2012 Express for Windows Phone]: https://go.microsoft.com/fwLink/p/?LinkID=268374
[モバイル サービスの使用]: /ja-jp/develop/mobile/tutorials/get-started-wp8
[データの使用]: /ja-jp/develop/mobile/tutorials/get-started-with-data-wp8
[認証の使用]: /ja-jp/develop/mobile/tutorials/get-started-with-users-wp8
[プッシュ通知の使用]: /ja-jp/develop/mobile/tutorials/get-started-with-push-wp8
[アプリケーション ユーザーへのプッシュ通知]: /ja-jp/develop/mobile/tutorials/push-notifications-to-users-wp8
[スクリプトを使用したユーザーの承認]: /ja-jp/develop/mobile/tutorials/authorize-users-in-scripts-wp8

[Azure の管理ポータル]: https://manage.windowsazure.com/
[mpns オブジェクト]: http://go.microsoft.com/fwlink/p/?LinkId=271130
[モバイル サービスのサーバー スクリプト リファレンス]: http://go.microsoft.com/fwlink/?LinkId=262293
[モバイル サービス .NET の使用方法の概念リファレンス]: /ja-jp/develop/mobile/how-to-guides/work-with-net-client-library/
[通知ハブの使用]: /ja-jp/manage/services/notification-hubs/get-started-notification-hubs-wp8/
[Azure 通知ハブ]: /ja-jp/develop/net/how-to-guides/service-bus-notification-hubs/
[登録者への通知の送信]: /ja-jp/manage/services/notification-hubs/breaking-news-wp8/
[ユーザーへの通知の送信]: /ja-jp/manage/services/notification-hubs/notify-users/
[ユーザーへのクロスプラットフォーム通知の送信]: /ja-jp/manage/services/notification-hubs/notify-users-xplat-mobile-services/

