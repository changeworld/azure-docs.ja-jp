<properties linkid="develop-mobile-tutorials-get-started-with-push-dotnet" urlDisplayName="プッシュ通知の使用" pageTitle="プッシュ通知の使用 - モバイル サービス" metaKeywords="プッシュ通知 c#" description="Azure モバイル サービスでプッシュ通知を使用する方法について説明します。" metaCanonical="http://www.windowsazure.com/ja-jp/develop/mobile/tutorials/get-started-with-push-dotnet/" disqusComments="0" umbracoNaviHide="1" documentationCenter="Mobile" title="Visual Studio 2012 を使用したモバイル サービスでのプッシュ通知の使用" authors="" />
# Visual Studio 2012 を使用したモバイル サービスでのプッシュ通知の使用

<div class="dev-center-tutorial-selector sublanding"> 
	<a href="/ja-jp/develop/mobile/tutorials/get-started-with-push-dotnet-vs2012" title="Windows ストア C#" class="current">Windows ストア C#</a>
	<a href="/ja-jp/develop/mobile/tutorials/get-started-with-push-js-vs2012" title="Windows ストア JavaScript">Windows ストア JavaScript</a>
	<a href="/ja-jp/develop/mobile/tutorials/get-started-with-push-wp8" title="Windows Phone">Windows Phone</a>
	<a href="/ja-jp/develop/mobile/tutorials/get-started-with-push-ios" title="iOS">iOS</a>
	<a href="/ja-jp/develop/mobile/tutorials/get-started-with-push-android" title="Android">Android</a>
	<a href="/ja-jp/develop/mobile/tutorials/get-started-with-push-xamarin-ios" title="Xamarin.iOS">Xamarin.iOS</a>
	<a href="/ja-jp/develop/mobile/tutorials/get-started-with-push-xamarin-android" title="Xamarin.Android">Xamarin.Android</a>
</div>	

このトピックでは、Azure モバイル サービスを使用して Windows ストア アプリケーションにプッシュ通知を送信する方法について説明します。
このチュートリアルでは、Windows プッシュ通知サービス (WNS) を使用したプッシュ通知をクイック スタート プロジェクトに追加します。完了すると、モバイル サービスは、レコードが挿入されるたびにプッシュ通知を送信します。

<div class="dev-callout"><b>注</b>
	<p>このチュートリアルでは、Visual Studio 2012 で作成された Windows ストア アプリにプッシュ通知を追加します。Visual Studio 2013 には、モバイル サービスを使用する Windows ストア アプリのプッシュ通知を簡単にセットアップできるようにする新機能が含まれています。Visual Studio 2012 バージョンについては、「<a href="/ja-jp/develop/mobile/tutorials/get-started-with-push-dotnet">モバイル サービスでのプッシュ通知の使用</a>」を参照してください。</p>
</div>

このチュートリアルでは、プッシュ通知を有効にするための、次の基本的な手順について説明します。

1. [プッシュ通知用のアプリケーションを登録してモバイル サービスを構成する]
2. [Registrations テーブルを作成する]
3. [アプリケーションにプッシュ通知を追加する]
4. [プッシュ通知を送信するようにスクリプトを更新する]
5. [データを挿入して通知を受け取る]

このチュートリアルには、次のものが必要です。

+ Microsoft Visual Studio 2012 Express for Windows 8
+ アクティブな Windows ストア アカウント

このチュートリアルは、チュートリアル「[データの使用]」に基づいています。このため、このチュートリアルの前に、[このチュートリアル][Get started with data]を完了している必要があります。

<h2><a name="register"></a><span class="short-header">アプリケーションの登録</span>アプリケーションを Windows ストアに登録する</h2>

モバイル サービスから Windows ストア アプリケーションにプッシュ通知を送信できるようにするには、アプリケーションを Windows ストアに提出する必要があります。さらに、WNS に統合するためにモバイル サービスを構成する必要があります。

[WACOM.INCLUDE [mobile-services-register-windows-store-app](../includes/mobile-services-register-windows-store-app.md)]

これで、WNS と連携するようにモバイル サービスとアプリケーションが構成されました。次に、登録を格納するための新しいテーブルを作成します。

## <a name="create-table"></a>新しいテーブルを作成する

[WACOM.INCLUDE [mobile-services-create-new-push-table](../includes/mobile-services-create-new-push-table.md)]

<h2><a name="add-push"></a><span class="short-header">プッシュ通知の追加</span>アプリケーションにプッシュ通知を追加する</h2>

1. MainPage.xaml.cs プロジェクト ファイルを開き、次に示す新しい **Registrations** クラスを作成するコードを追加します。

	    public class Registrations
	    {
	        public string Id { get; set; }
	
	        [JsonProperty(PropertyName = "handle")]
	        public string Handle { get; set; }
	    }
	
	Handleプロパティは、URIチャネルを格納するために使用されます。

2. App.xaml.cs ファイルを開き、次の using ステートメントを追加します。

        using Windows.Networking.PushNotifications;

3. 次のコードを App.xaml.cs に追加します。
	
        private async void AcquirePushChannel()
	    {
	       CurrentChannel = 
               await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();
	
	       IMobileServiceTable<Registrations> registrationsTable = App.MobileService.GetTable<Registrations>();
	       var registration = new Registrations { Handle = CurrentChannel.Uri };
	       await registrationsTable.InsertAsync(registration);
        }

     このコードでは、現在のチャネルが Registrations テーブルに挿入されます。
    
4. App.xaml.cs 内で、**OnLaunched** イベント ハンドラーの先頭に、次に示す新しい **AcquirePushChannel** メソッドへの呼び出しを追加します。

        AcquirePushChannel();

   これにより、アプリケーションが開始されるたびに必ず **CurrentChannel** プロパティが初期化されます。
		
5. Package.appxmanifest ファイルを開き、**[アプリケーション UI]** タブで **[トースト対応]** が **[はい]** に設定されていることを確認します。

   	![][15]

   	これにより、アプリケーションでトースト通知の使用が有効になります。

<h2><a name="update-scripts"></a><span class="short-header">挿入スクリプトの更新</span>管理ポータルで登録されている挿入スクリプトを更新する</h2>

[WACOM.INCLUDE [mobile-services-update-registrations-script](../includes/mobile-services-update-registrations-script.md)]

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
        	    var registrationsTable = tables.getTable('Registrations');
        	    registrationsTable.read({
            	    success: function(registrations) {
                	    registrations.forEach(function(registration) {
                    	    push.wns.sendToastText04(registration.handle, {
                        	    text1: item.text
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

1. Visual Studio で、F5 キーを押してアプリケーションを実行します。

2. アプリケーションで、**[Insert a TodoItem]** にテキストを入力し、**[Save]** をクリックします。

   	![][13]

   	挿入が完了すると、アプリケーションは、WNS からプッシュ通知を受け取ります。

   	![][14]

## <a name="next-steps"> </a>次のステップ

このチュートリアルでは、モバイル サービスに用意されている基本的なプッシュ通知機能を示します。より高度な機能 (たとえば、クロスプラットフォーム通知の送信、サブスクリプション ベースのルーティング、大ボリューム) を必要とする場合は、モバイル サービスで Azure 通知ハブを使用することを検討してください。詳細については、通知ハブに関する次のトピックのいずれかを参照してください。

+ [通知ハブの使用]
  <br/>Windows ストア アプリで通知ハブを活用する方法について説明します。

+ [通知ハブとは]
	<br/>プッシュ通知を作成して複数のプラットフォームのユーザーにプッシュ通知を送信する方法について説明します。

+ [登録者への通知の送信]
	<br/>ユーザーが興味のあるカテゴリに関してプッシュ通知を登録して、プッシュ通知を受信できるようにする方法について説明します。

+ [ユーザーへの通知の送信]
	<br/>モバイル サービスから任意のデバイスを使用している特定のユーザーにプッシュ通知を送信する方法について説明します。

+ [ユーザーへのクロスプラットフォーム通知の送信]
	<br/>テンプレートを使用して、バックエンドでプラットフォームに固有のペイロードを作成する必要なくモバイル サービスからプッシュ通知を送信する方法について説明します。

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
[プッシュ通知用のアプリケーションを登録してモバイル サービスを構成する]: #register
[Registrations テーブルを作成する]: #create-table
[プッシュ通知を送信するようにスクリプトを更新する]: #update-scripts
[アプリケーションにプッシュ通知を追加する]: #add-push
[データを挿入して通知を受け取る]: #test
[次のステップ]:#next-steps

<!-- Images. -->





[5]: ./media/mobile-services-windows-store-dotnet-get-started-push-vs2012/mobile-insert-script-push2.png







[13]: ./media/mobile-services-windows-store-dotnet-get-started-push-vs2012/mobile-quickstart-push1.png
[14]: ./media/mobile-services-windows-store-dotnet-get-started-push-vs2012/mobile-quickstart-push2.png
[15]: ./media/mobile-services-windows-store-dotnet-get-started-push-vs2012/mobile-app-enable-toast-win8.png



<!-- URLs. -->
[アプリケーションの提出に関するページ]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[マイ アプリケーション]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Windows 向け Live SDK]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[データの使用]: /ja-jp/develop/mobile/tutorials/get-started-with-data-dotnet
[認証の使用]: /ja-jp/develop/mobile/tutorials/get-started-with-users-dotnet
[プッシュ通知の使用]: ../tutorials/mobile-services-get-started-with-push-dotnet.md
[アプリケーション ユーザーへのプッシュ通知]: ../tutorials/mobile-services-push-notifications-to-app-users-dotnet.md
[スクリプトを使用したユーザーの認証]: ../tutorials/mobile-services-authorize-users-dotnet.md
[JavaScript と HTML]: ../tutorials/mobile-services-get-started-with-push-js.md

[Azure の管理ポータル]: https://manage.windowsazure.com/
[モバイル サービスのサーバー スクリプト リファレンス]: http://go.microsoft.com/fwlink/?LinkId=262293
[wns オブジェクト]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[モバイル サービス .NET の使用方法の概念リファレンス]: /ja-jp/develop/mobile/how-to-guides/work-with-net-client-library/
[通知ハブの使用]: /ja-jp/manage/services/notification-hubs/getting-started-windows-dotnet/
[通知ハブとは]: /ja-jp/develop/net/how-to-guides/service-bus-notification-hubs/
[登録者への通知の送信]: /ja-jp/manage/services/notification-hubs/breaking-news-dotnet/
[ユーザーへの通知の送信]: /ja-jp/manage/services/notification-hubs/notify-users/
[ユーザーへのクロスプラットフォーム通知の送信]: /ja-jp/manage/services/notification-hubs/notify-users-xplat-mobile-services/

