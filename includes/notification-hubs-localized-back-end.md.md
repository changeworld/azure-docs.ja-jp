バックエンド アプリケーションで、ネイティブ ペイロードではなくテンプレート通知を送信するように切り替える必要があります。これにより、バックエンド コードが簡素化され、さまざまなプラットフォームに対して複数のペイロードを送信する必要がなくなります。

テンプレート通知を送信する場合、一連のプロファイルの指定だけが必要になります。この場合は、最新ニュースのローカライズされたバージョンを含む一連のプロパティを送信します。次に例を示します。

    {
        "News_English": "World News in English!",
        "News_French": "World News in French!",
        "News_Mandarin": "World News in Mandarin!"
    }

このセクションでは、通知を送信するための 2 つの方法について説明します。

-   コンソール アプリケーションの使用
-   モバイル サービス スクリプトの使用

使用されるコードは、Windows ストアと iOS デバイスの両方に対してブロードキャストされます。これは、バックエンドはサポートされているすべてのデバイスにブロードキャストできるためです。

## C\# コンソール アプリケーションを使用して通知を送信するには

1 つのテンプレート通知を送信するように、*SendNotificationAsync* メソッドを変更します。

    var hub = NotificationHubClient.CreateClientFromConnectionString("<connection string>", "<hub name>");
    var notification = new Dictionary<string, string>() {
                            {"News_English", "World News in English!"},
                            {"News_French", "World News in French!"},
                            {"News_Mandarin", "World News in Mandarin!"}};
    await hub.SendTemplateNotificationAsync(notification, "World");

この単純な呼び出しでは、プラットフォームに関係なく、ローカライズされた各ニュースが適切に**すべての**デバイスに配信されます。これは、通知ハブが適切なネイティブ ペイロードを作成し、そのペイロードを特定のタグにサブスクライブされているすべてのデバイスに配信するためです。

### Mobile Services

モバイル サービス スケジューラで、スクリプトを次のように置き換えます。

    var azure = require('azure');
    var notificationHubService = azure.createNotificationHubService('<hub name>', <connection string with full access>');
    var notification = {
            "News_English": "World News in English!",
            "News_French": "World News in French!",
            "News_Mandarin", "World News in Mandarin!"
    }
    notificationHubService.send('World', notification, function(error) {
        if (!error) {
            console.warn("Notification successful");
        }
    });

この場合、異なるロケールやプラットフォームに対して複数の通知を送信する必要はありません。

