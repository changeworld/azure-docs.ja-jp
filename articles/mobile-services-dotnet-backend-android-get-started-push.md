<properties linkid="develop-mobile-tutorials-dotnet-backend-get-started-with-push-android" urlDisplayName="Get Started with Push" pageTitle="Get started with push (Android) | Mobile Dev Center" metaKeywords="" description="Learn how to use Azure Mobile Services to send push notifications to your Android .Net app." metaCanonical="" services="" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="ricksal" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="Mobile-Android" ms.devlang="Java" ms.topic="article" ms.date="01/01/1900" ms.author="ricksal" />

# モバイル サービスでのプッシュ通知の使用

<div class="dev-center-tutorial-selector sublanding"><a href="/ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push/" title="Windows ストア C#">Windows ストア C#</a><a href="/ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push/" title="Windows ストア JavaScript">Windows ストア JavaScript</a><a href="/ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push/" title="Windows Phone">Windows Phone</a><a href="/ja-jp/documentation/articles/mobile-services-dotnet-backend-ios-get-started-push/" title="iOS">iOS</a><a href="/ja-jp/documentation/articles/mobile-services-dotnet-backend-android-get-started-push/" title="Android" class="current">Android</a>
<!---<a href="/ja-jp/documentation/articles/mobile-services-dotnet-backend-xamarin-ios-get-started-push" title="Xamarin.iOS">Xamarin.iOS</a><a href="/ja-jp/documentation/articles/mobile-services-dotnet-backend-xamarin-android-get-started-push" title="Xamarin.Android">Xamarin.Android</a>-->
</div>

<div class="dev-center-tutorial-subselector"><a href="/ja-jp/documentation/articles/mobile-services-dotnet-backend-android-get-started-push/" title=".NET バックエンド" class="current">.NET バックエンド</a> |  <a href="/ja-jp/documentation/articles/mobile-services-javascript-backend-android-get-started-push/"  title="JavaScript バックエンド">JavaScript バックエンド</a></div>

このトピックでは、Azure モバイル サービスを使用して Android アプリにプッシュ通知を送信する方法について説明します。このチュートリアルでは、Google Cloud Messaging (GCM) を使用したプッシュ通知をクイック スタート プロジェクトに追加します。完了すると、モバイル サービスは、レコードが挿入されるたびにプッシュ通知を送信します。

このチュートリアルでは、次の手順について説明します。

1.  [Google Cloud Messaging を有効にする][Google Cloud Messaging を有効にする]
2.  [プッシュ要求を送信するようにモバイル サービスを構成する][プッシュ要求を送信するようにモバイル サービスを構成する]
3.  [サーバーを更新してプッシュ通知を送信する][サーバーを更新してプッシュ通知を送信する]
4.  [アプリケーションにプッシュ通知を追加する][アプリケーションにプッシュ通知を追加する]
5.  [ローカル テストのためにプッシュ通知を有効にする][ローカル テストのためにプッシュ通知を有効にする]
6.  [発行されたモバイル サービスに対してアプリケーションをテストする][発行されたモバイル サービスに対してアプリケーションをテストする]

このチュートリアルは、モバイル サービスのクイック スタートに基づいています。このチュートリアルを開始する前に、「[モバイル サービスの使用][モバイル サービスの使用]」または「[モバイル サービスでのデータの使用][モバイル サービスでのデータの使用]」を完了してプロジェクトをモバイル サービスに接続している必要があります。そのため、このチュートリアルには、Visual Studio 2013 も必要です。

> [WACOM.NOTE] このチュートリアルを完了するには、Azure アカウントが必要です。アカウントがない場合は、無料の試用アカウントを数分で作成することができます。詳細については、[Azure の無料評価版サイト][Azure の無料評価版サイト]を参照してください。

## <span id="register"></span></a>Google Cloud Messaging を有効にする

[WACOM.INCLUDE [Enable GCM](../includes/mobile-services-enable-Google-cloud-messaging.md)]

## <span id="configure"></span></a>プッシュ要求を送信するようにモバイル サービスを構成する

1.  [Azure の管理ポータル][Azure の管理ポータル]にログオンし、**[モバイル サービス]** をクリックして、アプリケーションをクリックします。

    ![][0]

2.  **[プッシュ]** タブをクリックし、前の手順で GCM から取得した **API キー**値を入力して、**[保存]** をクリックします。

    ![][1]

    <div class="dev-callout"><b>重要</b><p>ポータルの [プッシュ] タブで拡張プッシュ通知に対応する GCM の資格情報を設定した場合は、アプリに対して通知ハブを構成する目的で、それらの資格情報が通知ハブと共有されます。</p></div>

これで、GCM および通知ハブと連携するようにモバイル サービスが構成されました。

## <a name="download-the-service"></a><span class="short-header">サービスのダウンロード</span>サービスをローカル コンピューターにダウンロードする

[WACOM.INCLUDE [mobile-services-download-service-locally](../includes/mobile-services-download-service-locally.md)]

## <a name="test-the-service"></a><span class="short-header">サービスのテスト</span>モバイル サービスをテストする

[WACOM.INCLUDE [mobile-services-dotnet-backend-test-local-service](../includes/mobile-services-dotnet-backend-test-local-service.md)]

## <span id="update-server"></span></a> サーバーを更新してプッシュ通知を送信する

1.  Visual Studio のソリューション エクスプローラーで、モバイル サービス プロジェクト内の **Controllers** フォルダーを右クリックします。TodoItemController.cs を開きます。ファイルの先頭に次の `using` ステートメントを追加します。

        using System;
        using System.Collections.Generic;

2.  次のコードで `PostTodoItem` メソッドの定義を更新します。

        public async Task<IHttpActionResult> PostTodoItem(TodoItem item)
        {
            TodoItem current = await InsertAsync(item);

            Dictionary<string, string> data = new Dictionary<string, string>()
            {
                { "message", item.Text}
            };
            GooglePushMessage message = new GooglePushMessage(data, TimeSpan.FromHours(1));

            try
            {
                var result = await Services.Push.SendAsync(message);
                Services.Log.Info(result.State.ToString());
            }
            catch (System.Exception ex)
            {
                Services.Log.Error(ex.Message, null, "Push.SendAsync Error");
            }
            return CreatedAtRoute("Tables", new { id = current.Id }, current);
        }

    Todo 項目を挿入した後、このコードは (挿入された項目のテキストを使用して) プッシュ通知を送信します。エラー イベントが発生した場合は、コードはエラー ログ エントリを追加します。そのエントリは、管理ポータル内でモバイル サービスに対応する **[ログ]** タブに表示されます。

## <a name="publish-the-service"></a><span class="short-header">サービスの発行</span>モバイル サービスを Azure に発行する

[WACOM.INCLUDE [mobile-services-dotnet-backend-publish-service](../includes/mobile-services-dotnet-backend-publish-service.md)]

## <a name="update-app"></a>アプリケーションにプッシュ通知を追加する

### Android SDK バージョンの検証

[WACOM.INCLUDE [mobile-services-verify-android-sdk-version](../includes/mobile-services-verify-android-sdk-version.md)]

次の手順は、Google Play サービスをインストールすることです。Google Cloud Messaging には、マニフェストの **minSdkVersion** プロパティが準拠する必要がある、開発およびテストに関する最小 API レベル要件があります。

古いデバイスを使用している場合は、Google Play Services SDK のセットアップに関するページを参考に、どれだけ小さな値を設定できるか判断し、適切に設定してください。

### プロジェクトへの Google Play Services の追加

[WACOM.INCLUDE [Add Play Services](../includes/mobile-services-add-Google-play-services.md)]

### コードの追加

[WACOM.INCLUDE [mobile-services-android-getting-started-with-push](../includes/mobile-services-android-getting-started-with-push.md)]

## <a name="test-app"></a><span class="short-header">アプリケーションのテスト</span>発行されたモバイル サービスに対してアプリケーションをテストする

Android フォンを USB ケーブルで直接接続するか、エミュレーターで仮想デバイスを使用する方法により、アプリケーションをテストできます。

### テスト用のエミュレーターを使用している場合...

Google API をサポートしている Android Virtual Device (AVD) を使用します。

1.  **[Window]** で **[Android Virtual Device Manager]** を選択し、デバイスを選択してから **[Edit]** をクリックします (または、デバイスを持っていない場合は、**[New]** をクリックします)。

    ![][2]

2.  **[Target]** で **[Google APIs]** (または **[Google APIs x86]**) を選択し、[OK] をクリックします。

    ![][3]

    これで、AVD が Google API を使用するようになります。複数のバージョンの Android SDK をインストールしている場合、API Level が以前プロジェクトのプロパティに設定した値と一致することを確認します。

### <span id="local-testing"></span></a> ローカル テストのためにプッシュ通知を有効にする

[WACOM.INCLUDE [mobile-services-dotnet-backend-configure-local-push](../includes/mobile-services-dotnet-backend-configure-local-push.md)]

### テストの実行

1.  Eclipse の **[Run]** メニューの **[Run]** をクリックして、アプリケーションを開始します。

2.  アプリケーションで、意味のあるテキスト (たとえば、「*新しいモバイル サービス タスク*」) を入力し、**[Add]** をクリックします。

    ![][4]

3.  画面の上端から下へスワイプし、通知を表示するためのデバイスの通知センターを開きます。

これで、このチュートリアルは終了です。

## <a name="next-steps"> </a>次のステップ

<!---This tutorial demonstrated the basics of enabling an Android app to use Mobile Services and Notification Hubs to send push notifications. Next, consider completing the next tutorial, [Send push notifications to authenticated users], which shows how to use tags to send push notifications from a Mobile Service to only an authenticated user.   + [Send push notifications to authenticated users]     <br/>Learn how to use tags to send push notifications from a Mobile Service to only an authenticated user.  + [Send broadcast notifications to subscribers]     <br/>Learn how users can register and receive push notifications for categories they're interested in.  + [Send template-based notifications to subscribers]     <br/>Learn how to use templates to send push notifications from a Mobile Service, without having to craft platform-specific payloads in your back-end. -->

Mobile Services と通知ハブについては次のトピックを参照してください。

-   [データの使用][モバイル サービスでのデータの使用]
    モバイル サービスを使用してデータの格納およびクエリの実行の方法について説明します。

-   [認証の使用][認証の使用]
    モバイル サービスを使用して別のアカウントの種類のアプリケーションのユーザーを認証する方法について説明します。

-   [Windows Azure 通知ハブとは][Windows Azure 通知ハブとは]
    通知ハブがすべての主要なクライアント プラットフォーム全体のアプリケーションに通知を配信するための動作を説明します。

-   [モバイル サービス向け Android クライアント ライブラリの使用方法][モバイル サービス向け Android クライアント ライブラリの使用方法]
    Android と共に Mobile Services を使用する方法を説明します。

 

 



  [Google Cloud Messaging を有効にする]: #register
  [プッシュ要求を送信するようにモバイル サービスを構成する]: #configure
  [サーバーを更新してプッシュ通知を送信する]: #update-server
  [アプリケーションにプッシュ通知を追加する]: #update
  [ローカル テストのためにプッシュ通知を有効にする]: #local-testing
  [発行されたモバイル サービスに対してアプリケーションをテストする]: #test-app
  [モバイル サービスの使用]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-android-get-started
  [モバイル サービスでのデータの使用]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-android-get-started-data
  [Azure の無料評価版サイト]: http://www.windowsazure.com/ja-jp/pricing/free-trial/?WT.mc_id=AE564AB28&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fja-jp%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-windows-store-dotnet-get-started-data%2F
  [Azure の管理ポータル]: https://manage.windowsazure.com/
  [0]: ./media/mobile-services-android-get-started-push/mobile-services-selection.png
  [1]: ./media/mobile-services-android-get-started-push/mobile-push-tab-android.png
  [2]: ./media/mobile-services-android-get-started-push/mobile-services-android-virtual-device-manager.png
  [3]: ./media/mobile-services-android-get-started-push/mobile-services-android-virtual-device-manager-edit.png
  [4]: ./media/mobile-services-android-get-started-push/mobile-quickstart-push1-android.png
  [認証の使用]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-android-get-started-users
  [Windows Azure 通知ハブとは]: /ja-jp/documentation/articles/notification-hubs-overview/
  [モバイル サービス向け Android クライアント ライブラリの使用方法]: /ja-jp/documentation/articles/mobile-services-android-how-to-use-client-library
