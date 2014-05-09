<properties linkid="manage-services-notification-hubs-notify-users-xplat-aspnet" urlDisplayName="ユーザーへの通知 xplat aspnet" pageTitle="通知ハブによるユーザーへのクロスプラットフォーム通知の送信 (ASP.NET)" metaKeywords="" description="通知ハブ テンプレートを使用して、すべてのプラットフォームをターゲットとするプラットフォームにとらわれない通知を 1 つの要求で送信する方法について説明します。" metaCanonical="" services="notification-hubs" documentationCenter="" title="通知ハブによるユーザーへのクロスプラットフォーム通知の送信" authors="glenga" solutions="" manager="" editor="" />
# 通知ハブによるユーザーへのクロスプラットフォーム通知の送信

<div class="dev-center-tutorial-selector sublanding">
    <a href="/ja-jp/manage/services/notification-hubs/notify-users-xplat-mobile-services/" title="モバイル サービス">モバイル サービス</a><a href="/ja-jp/manage/services/notification-hubs/notify-users-xplat-aspnet/" title="ASP.NET" class="current">ASP.NET</a>
</div> 

前のチュートリアル「[通知ハブによるユーザーへの通知]」では、認証された特定のユーザーにより登録されたすべてのデバイスにプッシュ通知を行う方法について説明しました。そのチュートリアルでは、サポートされる各クライアント プラットフォームに通知を送信するため、複数の要求が必要でした。通知ハブでは、テンプレートがサポートされています。テンプレートを使用すると、特定のデバイスが通知を受信する方法を指定できます。これにより、クロスプラットフォーム通知の送信が簡単になります。このトピックでは、テンプレートを活用して、すべてのプラットフォームをターゲットとするプラットフォームにとらわれない通知を 1 つの要求で送信する方法を示します。テンプレートの詳細については、「[Azure 通知ハブの概要][Templates]」を参照してください。

<div class="dev-callout"><b>注</b>
	<p>通知ハブを使用すると、デバイスでは同じタグを持つ複数のテンプレートを登録できます。この場合、そのタグをターゲットとするメッセージが受信されると複数の通知がデバイスに配信されます (テンプレートごとに 1 つずつ)。これにより、複数のビジュアル通知に同じメッセージを表示することが可能になります (Windows ストア アプリケーションでバッジおよびトースト通知の両方として表示するなど)。</p>
</div>

次の手順を実行し、テンプレートを使用してクロスプラットフォーム通知を送信します。

1. Visual Studio のソリューション エクスプローラーで **Controllers** フォルダーを展開し、RegisterController.cs ファイルを開きます。

2. `updated` の値が **false** のときに新しい登録を作成する **Post** メソッドでコード ブロックを見つけ、次のコードで置き換えます。

		if (!updated)
        {
            switch (platform)
            {
                case "windows":
                    var template = @"<toast><visual><binding template=""ToastText01""><text id=""1"">$(message)</text></binding></visual></toast>";
                    await hubClient.CreateWindowsTemplateRegistrationAsync(channelUri, template, new string[] { instId, userTag });
                    break;
                case "ios":
                    template = "{\"aps\":{\"alert\":\"$(message)\"}, \"inAppMessage\":\"$(message)\"}";
                    await hubClient.CreateAppleTemplateRegistrationAsync(deviceToken, template, new string[] { instId, userTag });
                    break;
            } 
        }
	
	このコードは、プラットフォーム固有のメソッドを呼び出して、ネイティブ登録の代わりにテンプレート登録を作成します。テンプレート登録がネイティブ登録から派生している場合、既存の登録を変更する必要はありません。

3. **sendNotification** メソッドを次のコードで置き換えます。

        // Send a cross-plaform notification by using templates. 
        private async Task sendNotification(string notificationText, string tag)
        {           
                var notification = new Dictionary<string, string> { { "message", "Hello, " + tag } };
                await hubClient.SendTemplateNotificationAsync(notification, tag);        
        }

	このコードは、ネイティブ ペイロードを指定しなくても、すべてのプラットフォームに通知を同時に送信します。通知ハブは、登録されたテンプレートでの指定内容に従って、提供された _tag_ 値を使用して適切なペイロードを作成して各デバイスに配信します。

4. もう一度クライアント アプリケーションを実行し、登録に成功したことを確認します。

5. (省略可能) クライアント アプリケーションを 2 つ目のデバイスに展開し、アプリケーションを実行します。

	各デバイスに通知が表示される点に注目してください。

## 次のステップ

このチュートリアルを完了したら、以下のトピックで通知ハブとテンプレートの詳細を参照してください。

+ **通知ハブを使用したニュース速報の送信 ([Windows ストア C#][Breaking news .NET] / [iOS][Breaking news iOS])**<br/>別のテンプレート使用シナリオを紹介します。

+  **[Azure 通知ハブの概要][Templates]**<br/>この概要トピックでは、テンプレートについて詳細に説明します。

+  **[方法: Azure 通知ハブ (Windows ストア アプリケーション)]**<br/>テンプレート式言語のリファレンスを示します。



<!-- Anchors. -->

<!-- Images. -->




<!-- URLs. -->
[ASP.NET ユーザーへのプッシュ通知の送信]: /ja-jp/manage/services/notification-hubs/notify-users-aspnet
[モバイル サービス ユーザーへのプッシュ通知の送信]: /ja-jp/manage/services/notification-hubs/notify-users/
[Visual Studio 2012 Express for Windows 8]: http://go.microsoft.com/fwlink/?LinkId=257546

[管理ポータル]: https://manage.windowsazure.com/
[通知ハブによるユーザーへのクロスプラットフォーム通知の送信]: /ja-jp/manage/services/notification-hubs/notify-users-xplat-mobile-services/
[ニュース速報 .NET]: /ja-jp/manage/services/notification-hubs/breaking-news-dotnet
[ニュース速報 iOS]: /ja-jp/manage/services/notification-hubs/breaking-news-dotnet
[Azure 通知ハブ]: http://go.microsoft.com/fwlink/p/?LinkId=314257
[通知ハブによるユーザーへの通知]: /ja-jp/manage/services/notification-hubs/notify-users-aspnet
[テンプレート]: http://go.microsoft.com/fwlink/p/?LinkId=317339
[方法: Azure 通知ハブ (Windows ストア アプリケーション)]: http://msdn.microsoft.com/ja-jp/library/windowsazure/jj927172.aspx

