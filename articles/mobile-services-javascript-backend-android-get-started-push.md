<properties linkid="develop-mobile-tutorials-get-started-with-push-js-vs2013" urlDisplayName="Get Started with Push (JS)" pageTitle="Get started with push notifications (Android JavaScript) | Mobile Dev Center" metaKeywords="" description="Learn how to use Azure Mobile Services to send push notifications to your Android JavaScript app." metaCanonical="http://www.windowsazure.com/ja-jp/develop/mobile/tutorials/get-started-with-push-dotnet/" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="ricksal"  solutions="" writer="ricksal" manager="" editor=""   />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="Mobile-Android" ms.devlang="Java" ms.topic="article" ms.date="01/01/1900" ms.author="ricksal"></tags>

# <a name="getting-started-with-push"> </a>モバイル サービスでのプッシュ通知の使用

<div class="dev-center-tutorial-selector sublanding"><a href="/ja-jp/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started-push" title="Windows ストア C#">Windows ストア C#</a><a href="/ja-jp/documentation/articles/mobile-services-javascript-backend-windows-store-javascript-get-started-push" title="Windows ストア JavaScript">Windows ストア JavaScript</a><a href="/ja-jp/documentation/articles/mobile-services-javascript-backend-windows-phone-get-started-push" title="Windows Phone">Windows Phone</a><a href="/ja-jp/documentation/articles/mobile-services-javascript-backend-ios-get-started-push" title="iOS">iOS</a><a href="/ja-jp/documentation/articles/mobile-services-javascript-backend-android-get-started-push" title="Android" class="current">Android</a>
<!-- <a href="/ja-jp/develop/mobile/tutorials/get-started-with-push-xamarin-ios" title="Xamarin.iOS">Xamarin.iOS</a><a href="/ja-jp/develop/mobile/tutorials/get-started-with-push-xamarin-android" title="Xamarin.Android">Xamarin.Android</a>-->
</div>

<div class="dev-center-tutorial-subselector"><a href="/ja-jp/documentation/articles/mobile-services-dotnet-backend-android-get-started-push/" title=".NET バックエンド" >.NET バックエンド</a> |  <a href="/ja-jp/documentation/articles/mobile-services-javascript-backend-android-get-started-push/" title="JavaScript バックエンド" class="current">JavaScript バックエンド</a></div>

このトピックでは、Azure モバイル サービスを使用して Android アプリにプッシュ通知を送信する方法について説明します。このチュートリアルでは、Google Cloud Messaging (GCM) を使用したプッシュ通知をクイック スタート プロジェクトに追加します。完了すると、モバイル サービスは、レコードが挿入されるたびにプッシュ通知を送信します。

> [WACOM.NOTE] このチュートリアルでは、モバイル サービスと通知ハブの統合を説明します。これはモバイル サービスからプッシュ通知を送信する方法になります。従来のプッシュを使用する古いモバイル サービスを使用していて、通知ハブを使用できるようにアップグレードしていない場合、このチュートリアルの一環として*アップグレードすることをお勧めします*。今回アップグレードしない場合は、チュートリアル「[プッシュ通知の使用 (従来のプッシュ)][プッシュ通知の使用 (従来のプッシュ)]」に従う必要があります。.

このチュートリアルでは、プッシュ通知を有効にするための、次の基本的な手順について説明します。

1.  [Google Cloud Messaging を有効にする][Google Cloud Messaging を有効にする]
2.  [モバイル サービスを構成する][モバイル サービスを構成する]
3.  [アプリケーションにプッシュ通知を追加する][アプリケーションにプッシュ通知を追加する]
4.  [プッシュ通知を送信するようにスクリプトを更新する][プッシュ通知を送信するようにスクリプトを更新する]
5.  [データを挿入して通知を受け取る][データを挿入して通知を受け取る]

このチュートリアルは、モバイル サービスのクイック スタートに基づいています。このチュートリアルを開始する前に、「[モバイル サービスの使用][モバイル サービスの使用]」または「[モバイル サービスでのデータの使用][モバイル サービスでのデータの使用]」を完了してプロジェクトをモバイル サービスに接続している必要があります。

## <span id="register"></span></a>Google Cloud Messaging を有効にする

> [WACOM.NOTE] この手順を完了するには、検証済みの電子メール アドレスを持つ Google アカウントが必要になります。新しい Google アカウントを作成するには、[accounts.google.com][accounts.google.com] にアクセスしてください。

[WACOM.INCLUDE [Enable GCM][Enable GCM]]

次に、この API キー値を使用して、モバイル サービスが GCM で認証し、アプリケーションの代わりにプッシュ通知を送信できるようにします。

## <span id="configure"></span></a>プッシュ要求を送信するようにモバイル サービスを構成する

1.  [Azure の管理ポータル][Azure の管理ポータル]にログオンし、**[モバイル サービス]** をクリックして、アプリケーションをクリックします。

    ![][]

2.  **[プッシュ]** タブをクリックし、前の手順で GCM から取得した **API キー**値を入力して、**[保存]** をクリックします。

    > [WACOM.NOTE] 古いモバイル サービスを使用してこのチュートリアルを完了すると、**[プッシュ]** タブの下にある **[拡張プッシュの有効化]** にリンクが表示される場合があります。ここをクリックしてモバイル サービスをアップグレードして通知ハブを統合させます。この変更は元に戻すことはできません。運用環境のモバイル サービスで拡張プッシュ通知を有効にする方法についての詳細は、[このガイダンス][このガイダンス]を参照してください。

    ![][1]

    <div class="dev-callout"><b>重要</b>
<p>ポータルの [プッシュ] タブで拡張プッシュ通知に対応する GCM の資格情報を設定した場合は、アプリに対して通知ハブを構成する目的で、それらの資格情報が通知ハブと共有されます。</p>
</div>

これで、GCM および通知ハブと連携するようにモバイル サービスとアプリケーションが構成されました。

## <span id="add-push"></span></a>アプリケーションにプッシュ通知を追加する

### Android SDK バージョンの検証

[WACOM.INCLUDE [Verify SDK][Verify SDK]]

次の手順は、Google Play サービスをインストールすることです。Google Cloud Messaging には、マニフェストの **minSdkVersion** プロパティが準拠する必要がある、開発およびテストに関する最小 API レベル要件があります。

古いデバイスを使用している場合は、[Google Play Services SDK のセットアップに関するページ][Google Play Services SDK のセットアップに関するページ]を参考に、どれだけ小さな値を設定できるか判断し、適切に設定してください。

### プロジェクトへの Google Play Services の追加

[WACOM.INCLUDE [Add Play Services][Add Play Services]]

### コードの追加

[WACOM.INCLUDE [mobile-services-android-getting-started-with-push][mobile-services-android-getting-started-with-push]]

## <span id="update-scripts"></span></a>管理ポータルで登録されている挿入スクリプトを更新する

1.  管理ポータルで、**[データ]** タブをクリックし、**TodoItem** テーブルをクリックします。

    ![][2]

2.  **[todoitem]** で、**[スクリプト]** タブをクリックし、**[挿入]** をクリックします。

    ![][3]

    **TodoItem** テーブルで挿入が発生したときに呼び出される関数が表示されます。

3.  insert 関数を次のコードに置き換え、**[保存]** をクリックします。

        function insert(item, user, request) {
        // Define a payload for the Google Cloud Messaging toast notification.
        var payload = 
            '{"data":{"message" : ' + item.text + ' }}';

        request.execute({
            success: function() {
                // If the insert succeeds, send a notification.
                push.gcm.send(null, payload, {
                    success: function(pushResponse) {
                        console.log("Sent push:", pushResponse, payload);
                        request.respond();
                        },              
                    error: function (pushResponse) {
                        console.log("Error Sending push:", pushResponse);
                        request.respond(500, { error: pushResponse });
                        }
                    });
                },
            error: function(err) {
                console.log("request.execute error", err)
                request.respond();
            }
          });
        }

    これで、新しい insert スクリプトが登録されます。このスクリプトは [gcm オブジェクト][gcm オブジェクト]を使用して、挿入が成功した後に、登録されているすべてのデバイスにプッシュ通知を送信します。

## <span id="test"></span></a>アプリケーションでプッシュ通知をテストする

Android フォンを USB ケーブルで直接接続するか、エミュレーターで仮想デバイスを使用する方法により、アプリケーションをテストできます。

### テスト用のエミュレーターの設定

このアプリケーションをエミュレーターで実行する場合は、Google API をサポートしている Android Virtual Device (AVD) を使用してください。

1.  Eclipse を再開し、Package Explorer でプロジェクトを右クリックして、**[Properties]**、**[Android]** の順にクリックし、**[Google APIs]** をオンにしてから **[OK]** をクリックします。

    ![][4]

    これで、プロジェクトの対象が Google API になります。

2.  **[Window]** で **[Android Virtual Device Manager]** を選択し、デバイスを選択してから **[Edit]** をクリックします。

    ![][5]

3.  **[Target]** で **[Google APIs]** を選択し、[OK] をクリックします。

    ![][6]

    これで、AVD が Google API を使用するようになります。

### テストの実行

1.  Eclipse の **[Run]** メニューの **[Run]** をクリックして、アプリケーションを開始します。

2.  アプリケーションで、意味のあるテキスト (たとえば、「*新しいモバイル サービス タスク*」) を入力し、**[Add]** をクリックします。

    ![][7]

3.  画面の上端から下へスワイプし、通知を表示するためのデバイスの通知センターを開きます。

これで、このチュートリアルは終了です。

## <a name="next-steps"> </a>次のステップ

<!---This tutorial demonstrated the basics of enabling an Android app to use Mobile Services and Notification Hubs to send push notifications. Next, consider completing the next tutorial, [Send push notifications to authenticated users], which shows how to use tags to send push notifications from a Mobile Service to only an authenticated user.  + [Send push notifications to authenticated users]     <br/>Learn how to use tags to send push notifications from a Mobile Service to only an authenticated user.  + [Send broadcast notifications to subscribers]     <br/>Learn how users can register and receive push notifications for categories they're interested in.  + [Send template-based notifications to subscribers]     <br/>Learn how to use templates to send push notifications from a Mobile Service, without having to craft platform-specific payloads in your back-end. -->

Mobile Services と通知ハブについては次のトピックを参照してください。

-   [データの使用][モバイル サービスでのデータの使用]
    モバイル サービスを使用してデータの格納およびクエリの実行の方法について説明します。

-   [認証の使用][認証の使用]
    モバイル サービスを使用して別のアカウントの種類のアプリケーションのユーザーを認証する方法について説明します。

-   [Windows Azure 通知ハブとは][Windows Azure 通知ハブとは]
    通知ハブがすべての主要なクライアント プラットフォーム全体のアプリケーションに通知を配信するための動作を説明します。

-   [モバイル サービス向け Android クライアント ライブラリの使用方法][モバイル サービス向け Android クライアント ライブラリの使用方法]
    Android と共に Mobile Services を使用する方法を説明します。

-   [モバイル サービスのサーバー スクリプト リファレンス][モバイル サービスのサーバー スクリプト リファレンス]
    モバイル サービスでビジネス ロジックを実装する方法を説明します。

<!-- Anchors. --> 
<!-- Images. --> 
<!-- URLs. -->

  [Windows ストア C#]: /ja-jp/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started-push "Windows ストア C#"
  [Windows ストア JavaScript]: /ja-jp/documentation/articles/mobile-services-javascript-backend-windows-store-javascript-get-started-push "Windows ストア JavaScript"
  [Windows Phone]: /ja-jp/documentation/articles/mobile-services-javascript-backend-windows-phone-get-started-push "Windows Phone"
  [iOS]: /ja-jp/documentation/articles/mobile-services-javascript-backend-ios-get-started-push "iOS"
  [Android]: /ja-jp/documentation/articles/mobile-services-javascript-backend-android-get-started-push "Android"
  [.NET バックエンド]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-android-get-started-push/ ".NET バックエンド"
  [JavaScript バックエンド]: /ja-jp/documentation/articles/mobile-services-javascript-backend-android-get-started-push/ "JavaScript バックエンド"
  [プッシュ通知の使用 (従来のプッシュ)]: /ja-jp/documentation/articles/mobile-services-android-get-started-push/
  [Google Cloud Messaging を有効にする]: #register
  [モバイル サービスを構成する]: #configure
  [アプリケーションにプッシュ通知を追加する]: #add-push
  [プッシュ通知を送信するようにスクリプトを更新する]: #update-scripts
  [データを挿入して通知を受け取る]: #test
  [モバイル サービスの使用]: /ja-jp/documentation/articles/mobile-services-android-get-started/
  [モバイル サービスでのデータの使用]: /ja-jp/documentation/articles/mobile-services-android-get-started-data/
  [accounts.google.com]: http://go.microsoft.com/fwlink/p/?LinkId=268302
  [Enable GCM]: ../includes/mobile-services-enable-Google-cloud-messaging.md
  [Azure の管理ポータル]: https://manage.windowsazure.com/
  []: ./media/mobile-services-android-get-started-push/mobile-services-selection.png
  [このガイダンス]: http://go.microsoft.com/fwlink/p/?LinkId=391951
  [1]: ./media/mobile-services-android-get-started-push/mobile-push-tab-android.png
  [Verify SDK]: ../includes/mobile-services-verify-android-sdk-version.md
  [Google Play Services SDK のセットアップに関するページ]: http://go.microsoft.com/fwlink/?LinkId=389801
  [Add Play Services]: ../includes/mobile-services-add-Google-play-services.md
  [mobile-services-android-getting-started-with-push]: ../includes/mobile-services-android-getting-started-with-push.md
  [2]: ./media/mobile-services-android-get-started-push/mobile-portal-data-tables.png
  [3]: ./media/mobile-services-android-get-started-push/mobile-insert-script-push2.png
  [gcm オブジェクト]: http://go.microsoft.com/fwlink/p/?LinkId=282645
  [4]: ./media/mobile-services-android-get-started-push/mobile-services-import-android-properties.png
  [5]: ./media/mobile-services-android-get-started-push/mobile-services-android-virtual-device-manager.png
  [6]: ./media/mobile-services-android-get-started-push/mobile-services-android-virtual-device-manager-edit.png
  [7]: ./media/mobile-services-android-get-started-push/mobile-quickstart-push1-android.png
  [認証の使用]: /ja-jp/documentation/articles/mobile-services-android-get-started-users
  [Windows Azure 通知ハブとは]: /ja-jp/documentation/articles/notification-hubs-overview/
  [モバイル サービス向け Android クライアント ライブラリの使用方法]: /ja-jp/documentation/articles/mobile-services-android-how-to-use-client-library
  [モバイル サービスのサーバー スクリプト リファレンス]: http://go.microsoft.com/fwlink/?LinkId=262293
