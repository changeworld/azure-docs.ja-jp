
1. Visual Studio でサーバー プロジェクトを右クリックし、**[NuGet パッケージを管理]** をクリックして `Microsoft.Azure.NotificationHubs` を見つけ、**[インストール]** をクリックします。これにより、バックエンドから通知を送信するために必要な Notification Hubs ライブラリがインストールされます。

3. サーバー プロジェクトで、**[Controllers]**、**[TodoItemController.cs]** の順に開き、次の using ステートメントを追加します。

		using System.Collections.Generic;
		using Microsoft.Azure.NotificationHubs;
		using Microsoft.Azure.Mobile.Server.Config;
	

2. **PostTodoItem** メソッドで、次のコードを **InsertAsync** に対する呼び出しの後ろに追加します。

        // Get the settings for the server project.
        HttpConfiguration config = this.Configuration;
        MobileAppSettingsDictionary settings = 
			this.Configuration.GetMobileAppSettingsProvider().GetMobileAppSettings();
        
        // Get the Notification Hubs credentials for the Mobile App.
        string notificationHubName = settings.NotificationHubName;
        string notificationHubConnection = settings
            .Connections[MobileAppSettingsKeys.NotificationHubConnectionString].ConnectionString;

        // Create a new Notification Hub client.
        NotificationHubClient hub = NotificationHubClient
        .CreateClientFromConnectionString(notificationHubConnection, notificationHubName);

        // Sending the message so that all template registrations that contain "messageParam"
        // will receive the notifications. This includes APNS, GCM, WNS, and MPNS template registrations.
        Dictionary<string,string> templateParams = new Dictionary<string,string>();
        templateParams["messageParam"] = item.Text + " was added to the list.";

        try
        {
            // Send the push notification and log the results.
            var result = await hub.SendTemplateNotificationAsync(templateParams);

            // Write the success result to the logs.
            config.Services.GetTraceWriter().Info(result.State.ToString());
        }
        catch (System.Exception ex)
        {
            // Write the failure result to the logs.
            config.Services.GetTraceWriter()
                .Error(ex.Message, null, "Push.SendAsync Error");
        }

    このコードは、"messageParam" を含んでいるすべてのテンプレート登録にテンプレート通知を送信するよう、通知ハブに指示を出します。登録に "messageParam" が使用されている各 PNS で、messageParam の位置に文字列が挿入されます。これにより、APNS、GCM、WNS、またはその他の任意の PNS に通知を送信できるようになります。

	通知ハブを使用するテンプレートの詳細については、[テンプレート](notification-hubs-templates.md)を参照してください。

<!---HONumber=AcomDC_1203_2015-->