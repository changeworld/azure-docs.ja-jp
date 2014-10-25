<properties pageTitle="Get started with push notifications (legacy push) | Mobile Dev Center" metaKeywords="" description="Learn how to use Azure Mobile Services to send push notifications to your Windows Phone app (legacy push)." metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services (legacy push)" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-phone" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="glenga"></tags>

# モバイル サービスでのプッシュ通知の使用 (従来のプッシュ)

<div class="dev-center-tutorial-selector sublanding">
<a href="/ja-jp/documentation/articles/mobile-services-windows-store-dotnet-get-started-push" title="Windows ストア C#" >Windows ストア C#</a>
<a href="/ja-jp/documentation/articles/mobile-services-windows-store-javascript-get-started-push" title="Windows ストア JavaScript">Windows ストア JavaScript</a>
<a href="/ja-jp/documentation/articles/mobile-services-windows-phone-get-started-push" title="Windows Phone" class="current">Windows Phone</a>
<a href="/ja-jp/documentation/articles/mobile-services-ios-get-started-push" title="iOS">iOS</a>
<a href="/ja-jp/documentation/articles/mobile-services-android-get-started-push" title="Android">Android</a>
<!--    <a href="/ja-jp/documentation/articles/partner-xamarin-mobile-services-ios-get-started-push" title="Xamarin.iOS">Xamarin.iOS</a>     <a href="/ja-jp/documentation/articles/partner-xamarin-mobile-services-android-get-started-push" title="Xamarin.Android">Xamarin.Android</a>    -->
<a href="/ja-jp/documentation/articles/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push" title="Appcelerator">Appcelerator</a>
</div>

<div class="dev-center-tutorial-subselector"><a href="/ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push/" title=".NET バックエンド">.NET バックエンド</a> |  <a href="/ja-jp/documentation/articles/mobile-services-windows-phone-get-started-push/"  title="JavaScript バックエンド" class="current">JavaScript バックエンド</a>
</div>

このトピックでは、Azure モバイル サービスを使用して Windows Phone 8 アプリケーションにプッシュ通知を送信する方法について説明します。
このチュートリアルでは、Microsoft Push Notification Service (MPNS) を使用したプッシュ通知をクイック スタート プロジェクトに追加します。完了すると、モバイル サービスは、レコードが挿入されるたびにプッシュ通知を送信します。

> [WACOM.NOTE]このトピックは、通知ハブ統合を使用できるようにする*アップグレードが行われていない既存の*モバイル サービスを対象にしています。*新しい*モバイル サービスを作成した場合は、この統合機能は自動的に有効になります。新しいモバイル サービスについては、[プッシュ通知の使用][プッシュ通知の使用]を参照してください。
>
> モバイル サービスは Azure 通知ハブに統合され、テンプレート、複数のプラットフォーム、改良されたスケールなど、追加のプッシュ通知機能をサポートするようになりました。*可能な限り、既存のモバイル サービスをアップグレードして通知ハブを使用するようにしてください*。アップグレードしたら、「[通知ハブの使用][プッシュ通知の使用]」を参照してください。

このチュートリアルでは、プッシュ通知を有効にするための、次の基本的な手順について説明します。

1.  [Registrations テーブルを作成する][Registrations テーブルを作成する]
2.  [アプリケーションにプッシュ通知を追加する][アプリケーションにプッシュ通知を追加する]
3.  [プッシュ通知を送信するようにスクリプトを更新する][プッシュ通知を送信するようにスクリプトを更新する]
4.  [データを挿入して通知を受け取る][データを挿入して通知を受け取る]

このチュートリアルには、[Visual Studio 2012 Express for Windows Phone][Visual Studio 2012 Express for Windows Phone] 以降のバージョンが必要です。

このチュートリアルは、モバイル サービスのクイック スタートに基づいています。このチュートリアルを開始する前に、「[モバイル サービスの使用][モバイル サービスの使用]」を完了している必要があります。

> [WACOM.NOTE] ユーザーあたり 500 通を超えるメッセージを毎日送信する場合は、通知ハブを使用する必要があります。詳細については、「[通知ハブの使用][通知ハブの使用]」を参照してください。

## <a name="create-table"></a>新しいテーブルを作成する

[WACOM.INCLUDE [mobile-services-create-new-push-table][mobile-services-create-new-push-table]]

## <a name="add-push"></a><span class="short-header">プッシュ通知の追加</span>アプリケーションにプッシュ通知を追加する

1.  Visual Studio で、MainPage.xaml.cs プロジェクト ファイルを開き、次に示す新しい **Registrations** クラスを作成するコードを追加します。

        public class Registrations
        {
            public string Id { get; set; }

            [JsonProperty(PropertyName = "handle")]
            public string Handle { get; set; }
        }

    Handle プロパティは、チャネル URI を格納するために使用します。

2.  App.xaml.cs ファイルを開き、次の using ステートメントを追加します。

        using Microsoft.Phone.Notification;

3.  次のコードを App.xaml.cs に追加します。

        public static HttpNotificationChannel CurrentChannel { get; private set; }

        private async void AcquirePushChannel()
        {
            CurrentChannel = HttpNotificationChannel.Find("MyPushChannel");

            if (CurrentChannel == null)
            {
                CurrentChannel = new HttpNotificationChannel("MyPushChannel");
                CurrentChannel.Open();
                CurrentChannel.BindToShellTile();
            }

           IMobileServiceTable<Registrations> registrationsTable = App.MobileService.GetTable<Registrations>();
           var registration = new Registrations { Handle = CurrentChannel.ChannelUri.AbsoluteUri };
           await registrationsTable.InsertAsync(registration);
        }

    このコードでは、プッシュ通知サブスクリプションのチャネルが取得および保存され、アプリケーションの既定のタイルにバインドされます。

    <div class="dev-callout"><b>注</b>
<p>このチュートリアルでは、モバイル サービスにより、フリップ タイル通知がデバイスに送信されます。トースト通知を送信する場合は、チャネルの <strong>BindToShellToast</strong> メソッドを呼び出す必要があります。トースト通知とタイル通知の両方をサポートするには、<strong>BindToShellTile</strong> と <strong>BindToShellToast</strong> の両方を呼び出します。 </p>
</div>

4.  App.xaml.cs 内で、**Application\_Launching** イベント ハンドラーの先頭に、次に示す新しい **AcquirePushChannel** メソッドへの呼び出しを追加します。

        AcquirePushChannel();

    これにより、アプリケーションが開始されるたびに必ず **CurrentChannel** プロパティが初期化されます。

5.  ソリューション エクスプローラーで、**[プロパティ]** を展開して WMAppManifest.xml ファイルを開き、**[機能]** タブをクリックして、**ID___CAP___PUSH_NOTIFICATION** 機能がオンであることを確認します。

    ![][]

    これにより、アプリケーションでプッシュ通知の受信が有効になります。

## <a name="update-scripts"></a><span class="short-header">挿入スクリプトの更新</span>管理ポータルで登録されている挿入スクリプトを更新する

[WACOM.INCLUDE [mobile-services-update-registrations-script][mobile-services-update-registrations-script]]

1.  **[TodoItem]** をクリックし、**[スクリプト]** タブをクリックして、**[挿入]** を選択します。

    ![][1]

2.  insert 関数を次のコードに置き換え、**[保存]** をクリックします。

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
                            push.mpns.sendFlipTile(registration.handle, {
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

## <a name="test"></a><span class="short-header">アプリケーションのテスト</span>アプリケーションでプッシュ通知をテストする

1.  Visual Studio で、**[ビルド]** メニューの **[ソリューションの配置]** を選択します。

2.  エミュレーターで、左方向へスワイプしてインストールされているアプリケーションの一覧を表示し、新しい **TodoList** アプリケーションを探します。

3.  アプリケーション アイコンを長押しして、コンテキスト メニューの **[スタート画面に追加]** を選択します。

    ![][2]

    これにより、**TodoList** という名前のタイルがスタート メニューに固定されます。

4.  **TodoList** という名前のタイルをタップして、アプリケーションを開始します。

    ![][3]

5.  そのアプリケーションで、テキスト ボックスに「hello push」というテキストを入力し、**[Save]** をクリックします。

    ![][4]

    これにより、追加された項目を保存するための挿入要求がモバイル サービスに送信されます。

6.  **[スタート]** ボタンを押してスタート メニューに戻ります。

    ![][5]

    アプリケーションがプッシュ通知を受信したため、タイルのタイトルが **hello push** になっています。

## <a name="next-steps"> </a>次のステップ

このチュートリアルでは、モバイル サービスに用意されている基本的なプッシュ通知機能を示します。より高度な機能 (たとえば、クロスプラットフォーム通知の送信、サブスクリプション ベースのルーティング、大ボリューム) を必要とする場合は、モバイル サービスで Azure 通知ハブを使用することを検討してください。詳細については、通知ハブに関する次のトピックのいずれかを参照してください。

-   [通知ハブの使用][6]
    Windows ストア アプリで通知ハブを活用する方法について説明します。

-   [通知ハブとは][通知ハブとは]
    プッシュ通知を作成して複数のプラットフォームのユーザーにプッシュ通知を送信する方法について説明します。

-   [登録者への通知の送信][登録者への通知の送信]
    ユーザーが興味のあるカテゴリに関してプッシュ通知を登録して、プッシュ通知を受信できるようにする方法について説明します。

<!--+ [Send notifications to users]     <br/>Learn how to send push notifications from a Mobile Service to specific users on any device.  + [Send cross-platform notifications to users]     <br/>Learn how to use templates to send push notifications from a Mobile Service, without having to craft platform-specific payloads in your back-end. -->

次のモバイル サービスのトピックの詳細を確認することをお勧めします。

-   [データの使用][データの使用]
    Mobile Services を使用してデータの格納およびクエリを実行する方法について説明します。

-   [認証の使用][認証の使用]
    Windows アカウントを使用してアプリケーションのユーザーを認証する方法について説明します。

-   [モバイル サービスのサーバー スクリプト リファレンス][モバイル サービスのサーバー スクリプト リファレンス]
    サーバー スクリプトの登録および使用について説明します。

-   [モバイル サービス .NET の使用方法の概念リファレンス][モバイル サービス .NET の使用方法の概念リファレンス]
    .NET で Mobile Services を使用する方法について説明します。

<!-- Anchors. --> 
<!-- Images. --> 
<!-- URLs. -->

  [Windows ストア C#]: /ja-jp/documentation/articles/mobile-services-windows-store-dotnet-get-started-push "Windows ストア C#"
  [Windows ストア JavaScript]: /ja-jp/documentation/articles/mobile-services-windows-store-javascript-get-started-push "Windows ストア JavaScript"
  [Windows Phone]: /ja-jp/documentation/articles/mobile-services-windows-phone-get-started-push "Windows Phone"
  [iOS]: /ja-jp/documentation/articles/mobile-services-ios-get-started-push "iOS"
  [Android]: /ja-jp/documentation/articles/mobile-services-android-get-started-push "Android"
  [Appcelerator]: /ja-jp/documentation/articles/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push "Appcelerator"
  [.NET バックエンド]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push/ ".NET バックエンド"
  [JavaScript バックエンド]: /ja-jp/documentation/articles/mobile-services-windows-phone-get-started-push/ "JavaScript バックエンド"
  [プッシュ通知の使用]: /ja-jp/documentation/articles/mobile-services-javascript-backend-windows-phone-get-started-push/
  [Registrations テーブルを作成する]: #create-table
  [アプリケーションにプッシュ通知を追加する]: #add-push
  [プッシュ通知を送信するようにスクリプトを更新する]: #update-scripts
  [データを挿入して通知を受け取る]: #test
  [Visual Studio 2012 Express for Windows Phone]: https://go.microsoft.com/fwLink/p/?LinkID=268374
  [モバイル サービスの使用]: /ja-jp/develop/mobile/tutorials/get-started-wp8
  [通知ハブの使用]: /ja-jp/manage/services/notification-hubs/getting-started-windows-dotnet/
  [mobile-services-create-new-push-table]: ../includes/mobile-services-create-new-push-table.md
  []: ./media/mobile-services-windows-phone-get-started-push/mobile-app-enable-push-wp8.png
  [mobile-services-update-registrations-script]: ../includes/mobile-services-update-registrations-script.md
  [1]: ./media/mobile-services-windows-phone-get-started-push/mobile-insert-script-push2.png
  [2]: ./media/mobile-services-windows-phone-get-started-push/mobile-quickstart-push1-wp8.png
  [3]: ./media/mobile-services-windows-phone-get-started-push/mobile-quickstart-push2-wp8.png
  [4]: ./media/mobile-services-windows-phone-get-started-push/mobile-quickstart-push3-wp8.png
  [5]: ./media/mobile-services-windows-phone-get-started-push/mobile-quickstart-push4-wp8.png
  [6]: /ja-jp/manage/services/notification-hubs/get-started-notification-hubs-wp8/
  [通知ハブとは]: /ja-jp/develop/net/how-to-guides/service-bus-notification-hubs/
  [登録者への通知の送信]: /ja-jp/manage/services/notification-hubs/breaking-news-wp8/
  [データの使用]: /ja-jp/develop/mobile/tutorials/get-started-with-data-wp8
  [認証の使用]: /ja-jp/develop/mobile/tutorials/get-started-with-users-wp8
  [モバイル サービスのサーバー スクリプト リファレンス]: http://go.microsoft.com/fwlink/?LinkId=262293
  [モバイル サービス .NET の使用方法の概念リファレンス]: /ja-jp/develop/mobile/how-to-guides/work-with-net-client-library/
