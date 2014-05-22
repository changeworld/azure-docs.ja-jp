<properties linkid="notification-hubs-how-to-guides-howto-register-user-with-mobile-service-windowsphonedotnet" urlDisplayName="モバイル サービスを使用した Windows ストア アプリケーション ユーザーへの通知" pageTitle="モバイル サービスを使用した現在のユーザーのプッシュ通知への登録 - 通知ハブ" metaKeywords="Azure 登録アプリケーション, 通知ハブ, Azure プッシュ通知, プッシュ通知 Windows ストア アプリケーション" description="Azure モバイル サービスにより登録が実行されるときに、Windows ストア アプリケーションで Azure 通知ハブを使用してプッシュ通知登録を要求する方法について説明します。" metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="" title="モバイル サービスを使用した現在のユーザーのプッシュ通知への登録" authors="" solutions="" manager="" editor="" />
#モバイル サービスを使用した現在のユーザーのプッシュ通知への登録

<div class="dev-center-tutorial-selector sublanding">
    <a href="/ja-jp/documentation/articles/notification-hubs-windows-store-mobile-services-register-user-push-notifications/" title="Windows ストア C#" class="current">Windows ストア C#</a><a href="/ja-jp/documentation/articles/notification-hubs-ios-mobile-services-register-user-push-notifications/" title="iOS">iOS</a>
</div>

このトピックでは、Azure のモバイル サービスにより登録が実行されるときに Azure 通知ハブへのプッシュ通知登録を要求する方法について説明します。このトピックは、チュートリアル「[通知ハブによるユーザーへの通知]」を拡張したものです。認証されたモバイル サービスを作成するには、このチュートリアルの必要な手順を既に完了している必要があります。ユーザー通知シナリオの詳細については、「[通知ハブによるユーザーへの通知]」を参照してください。

1. Visual Studio 2012 Express for Windows 8 で、前提条件のチュートリアルである「[認証の使用]」を実行したときに作成したプロジェクトを開きます。

2. ソリューション エクスプローラーでプロジェクトを右クリックし、**[ストア]**、**[アプリケーションをストアと関連付ける]** の順にクリックします。

  	![][1]

   	**アプリケーションを Windows ストアと関連付ける**ウィザードが表示されます。

3. ウィザードで **[サインイン]** をクリックし、Microsoft アカウントでログインします。

4. 「[通知ハブによるユーザーへの通知]」で登録したアプリケーションを選択し、**[次へ]**、**[関連付け]** の順にクリックします。

   	![][2]

   	この操作により、必要な Windows ストア登録情報がアプリケーション マニフェストに追加されます。

	<div class="dev-callout"><b>注</b>
	<p>このモバイル サービス アプリケーションでは、通知ハブ チュートリアル アプリケーションの Windows ストア登録が再利用されます。このため、通知ハブ チュートリアル アプリケーションが通知を受信できなくなる可能性があります。</p>
	</div>

5. ソリューション エクスプローラーで、Package.appxmanifest プロジェクト ファイルをダブルクリックして、Visual Studio エディターで開きます。

6. **[すべてのイメージ資産]** まで下へスクロールして、**[バッジ ロゴ]** をクリックします。**[通知]** で、**[トースト対応]** を **[はい]** に設定します。

   	![][3]

	これにより、このモバイル サービス チュートリアル アプリケーションが、トースト通知を受信できるようになります。

7. Visual Studio で、MainPage.xaml.cs ファイルを開き、**NotificationRequest** クラスと **RegistrationResult** クラスを定義する次のコードを追加します。

        public class NotificationRequest
        {
            public string channelUri { get; set; }
            public string platform { get; set; }
        }

        public class RegistrationResult
        {
            public string RegistrationId { get; set; }
            public string ExpirationTime { get; set; }
        }

	これらのクラスには、それぞれ要求の本文と、カスタム API が呼び出されたときに返された登録 ID が格納されます。

8. **MainPage** クラス内で、次のメソッドを追加します。

        private async System.Threading.Tasks.Task RegisterNotification()
        {
            string message;

            // Get a channel for push notifications.
            var channel =
                await Windows.Networking.PushNotifications
                .PushNotificationChannelManager
                .CreatePushNotificationChannelForApplicationAsync();

            // Create the body of the request.
            var body = new NotificationRequest 
            {
                channelUri = channel.Uri, 
                platform = "win8" 
            }; 

            try
            {
                // Call the custom API POST method with the supplied body.
                var result = await App.MobileService
                    .InvokeApiAsync<NotificationRequest, 
                    RegistrationResult>("register_notifications", body,
                    System.Net.Http.HttpMethod.Post, null);

                // Set the response, which is the ID of the registration.
                message = string.Format("Registration ID: {0}", result.RegistrationId);
            }
            catch (MobileServiceInvalidOperationException ex)
            {
                message = ex.Message;
            }

            // Display a message dialog.
            var dialog = new MessageDialog(message);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
        }

	このメソッドは、プッシュ通知のチャネルを作成し、通知ハブで登録を作成するカスタム API メソッドに、デバイスの種類と共に送信します。このカスタム API は、「[通知ハブによるユーザーへの通知]」で定義されたものです。

9. **OnNavigatedTo** メソッドに次のコード行を追加します (**Authenticate** メソッドの呼び出しの直後)。

		await RegisterNotification();

	<div class="dev-callout"><b>注</b>
	<p>これにより、ページが読み込まれるたびに登録が要求されるようになります。アプリケーションでは、この登録が常に最新の状態となるように、定期的な登録のみ行うことができます。</p>
	</div>

これで、クライアント アプリケーションが更新されました。「[通知ハブによるユーザーへの通知]」に戻り、通知ハブを使用することで通知を送信するようにモバイル サービスを更新します。

<!-- Anchors. -->

<!-- Images. -->
[1]: ./media/notification-hubs-windows-store-mobile-services-register-user-push-notifications/mobile-services-select-app-name.png
[2]: ./media/notification-hubs-windows-store-mobile-services-register-user-push-notifications/notification-hub-associate-win8-app.png
[3]: ./media/notification-hubs-windows-store-mobile-services-register-user-push-notifications/notification-hub-win8-app-toast.png

<!-- URLs. -->
[通知ハブによるユーザーへの通知]: /ja-jp/manage/services/notification-hubs/notify-users
[認証の使用]: /ja-jp/develop/mobile/tutorials/get-started-with-users-dotnet/

[Azure 管理ポータル]: https://manage.windowsazure.com/

