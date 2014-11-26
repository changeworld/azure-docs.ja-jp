<properties linkid="notification-hubs-how-to-guides-howto-register-user-with-mobile-service-windowsphonedotnet" urlDisplayName="Notify Windows Store app users by using Mobile Services" pageTitle="Register the current user for push notifications by using a mobile service - Notification Hubs" metaKeywords="Azure registering application, Notification Hubs, Azure push notifications, push notification Windows Store app" description="Learn how to request push notification registration in a Windows Store app with Azure Notification Hubs when registeration is performed by Azure Mobile Services." metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="" title="Register the current user for push notifications by using a mobile service" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# モバイル サービスを使用した現在のユーザーのプッシュ通知への登録

<div class="dev-center-tutorial-selector sublanding">
<a href="/ja-jp/documentation/articles/notification-hubs-windows-store-mobile-services-register-user-push-notifications/" title="Windows ストア C#" class="current">Windows ストア C#</a><a href="/ja-jp/documentation/articles/notification-hubs-ios-mobile-services-register-user-push-notifications/" title="iOS">iOS</a>
</div>

このトピックでは、Azure のモバイル サービスにより登録が実行されるときに Azure 通知ハブへのプッシュ通知登録を要求する方法について説明します。このトピックは、チュートリアル「[通知ハブによるユーザーへの通知][通知ハブによるユーザーへの通知]」を拡張したものです。認証されたモバイル サービスを作成するには、このチュートリアルの必要な手順を既に完了している必要があります。ユーザー通知シナリオの詳細については、「[通知ハブによるユーザーへの通知][通知ハブによるユーザーへの通知]」を参照してください。

1.  Visual Studio 2012 Express for Windows 8 で、前提条件のチュートリアルである「[認証の使用][認証の使用]」を実行したときに作成したプロジェクトを開きます。

2.  ソリューション エクスプローラーでプロジェクトを右クリックし、**[ストア]**、**[アプリケーションをストアと関連付ける]** の順にクリックします。

    ![][0]

    **アプリケーションを Windows ストアと関連付ける**ウィザードが表示されます。

3.  ウィザードで **[サインイン]** をクリックし、Microsoft アカウントでログインします。

4.  「[Notification Hubs によるユーザーへの通知][通知ハブによるユーザーへの通知]」で登録したアプリを選択し、**[次へ]**、**[関連付け]** の順にクリックします。

    ![][1]

    この操作により、必要な Windows ストア登録情報がアプリケーション マニフェストに追加されます。

    <div class="dev-callout"><b>注</b>
<p>この Mobile Services アプリでは、Notification Hubs チュートリアル アプリの Windows ストア登録が再利用されます。このため、Notification Hubs チュートリアル アプリが通知を受信できなくなる可能性があります。</p>
</div>

5.  ソリューション エクスプローラーで、Package.appxmanifest プロジェクト ファイルをダブルクリックして、Visual Studio エディターで開きます。

6.  下方向へ **[すべてのイメージ資産]** までスクロールし、**[バッジ ロゴ]** をクリックします。**[通知]** で、**[トースト対応]** を **[はい]** に設定します。

    ![][2]

    これにより、このモバイル サービス チュートリアル アプリケーションが、トースト通知を受信できるようになります。

7.  Visual Studio で、MainPage.xaml.cs ファイルを開き、**NotificationRequest** クラスと **RegistrationResult** クラスを定義する次のコードを追加します。

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

8.  **MainPage** クラス内で、次のメソッドを追加します。

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

    このメソッドは、プッシュ通知のチャネルを作成し、通知ハブで登録を作成するカスタム API メソッドに、デバイスの種類と共に送信します。このカスタム API は、「[通知ハブによるユーザーへの通知][通知ハブによるユーザーへの通知]」で定義されたものです。

9.  **OnNavigatedTo** メソッドに次のコード行を追加します (**Authenticate** メソッドの呼び出し直後)。

        await RegisterNotification();

    <div class="dev-callout"><b>注</b>
    <p>これにより、ページが読み込まれるたびに登録が要求されるようになります。アプリケーションでは、この登録が常に最新の状態となるように、定期的な登録のみ行うことができます。</p>
    </div>

これで、クライアント アプリケーションが更新されました。「[通知ハブによるユーザーへの通知][通知ハブによるユーザーへの通知]」に戻り、通知ハブを使用することで通知を送信するようにモバイル サービスを更新します。

 
 


  [通知ハブによるユーザーへの通知]: /ja-jp/manage/services/notification-hubs/notify-users
  [認証の使用]: /ja-jp/develop/mobile/tutorials/get-started-with-users-dotnet/
  [0]: ./media/notification-hubs-windows-store-mobile-services-register-user-push-notifications/mobile-services-select-app-name.png
  [1]: ./media/notification-hubs-windows-store-mobile-services-register-user-push-notifications/notification-hub-associate-win8-app.png
  [2]: ./media/notification-hubs-windows-store-mobile-services-register-user-push-notifications/notification-hub-win8-app-toast.png
