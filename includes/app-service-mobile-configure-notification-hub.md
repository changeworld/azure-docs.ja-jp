App Service Mobile Apps は [Notification Hubs] を使用してプッシュを送信するため、ここではモバイル アプリの通知ハブを構成します。

1. [Azure Portal] で、**[参照]** > **[App Services]** の順に移動し、アプリ バックエンドをクリックします。 **[設定]** の **[App Service Push (App Service プッシュ)]** をクリックします。
2. **[構成]** をクリックして通知ハブを構成します。 ハブを作成することも、既存のハブに接続することもできます。
   
    ![](./media/app-service-mobile-create-notification-hub/configure-hub-flow.png)

これで、通知ハブがモバイル アプリのバックエンドに接続されました。 後でこの通知ハブを構成して、デバイスにプッシュ通知を送信するプラットフォーム通知システム (PNS) に接続します。

[Azure Portal]: https://portal.azure.com/
[Notification Hubs]: https://azure.microsoft.com/en-us/documentation/articles/notification-hubs-push-notification-overview/

<!--HONumber=Nov16_HO3-->


