1. Visual Studio でサーバー プロジェクトを右クリックし、**[NuGet パッケージを管理]** をクリックして `Microsoft.Azure.NotificationHubs` を見つけ、**[インストール]** をクリックします。これにより、Notification Hubs のクライアント ライブラリがインストールされます。

2. バックエンドの Visual Studio プロジェクトで、**[Controllers]**、**[TodoItemController.cs]** の順に開きます。ファイルの先頭に、次の `using` ステートメントを追加します。


        using Microsoft.Azure.Mobile.Server.Config;
        using Microsoft.Azure.NotificationHubs;



3. `PostTodoItem` メソッドを次のコードに置き換えます。

      
        public async Task<IHttpActionResult> PostTodoItem(TodoItem item)
        {
            TodoItem current = await InsertAsync(item);
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

            // Android payload
            var androidNotificationPayload = "{ "data" : {"message":"" + item.Text + ""}}";

            try
            {
                // Send the push notification and log the results.
                var result = await hub.SendGcmNativeNotificationAsync(androidNotificationPayload);

                // Write the success result to the logs.
                config.Services.GetTraceWriter().Info(result.State.ToString());
            }
            catch (System.Exception ex)
            {
                // Write the failure result to the logs.
                config.Services.GetTraceWriter()
                    .Error(ex.Message, null, "Push.SendAsync Error");
            }
            return CreatedAtRoute("Tables", new { id = current.Id }, current);
        }

<!---HONumber=AcomDC_1203_2015-->