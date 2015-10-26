1. Visual Studio の**ソリューション エクスプローラー**で、モバイル バックエンド プロジェクトの **[Controllers]** フォルダーを展開します。**TodoItemController.cs** を開きます。ファイルの先頭に、次の `using` ステートメントを追加します。

        using System.Collections.Generic;        
        using Microsoft.Azure.Mobile.Server.Notifications;


2. `PostTodoItem` メソッドを次のコードに置き換えます。
        
        public async Task<IHttpActionResult> PostTodoItem(TodoItem item)
        {
            TodoItem current = await InsertAsync(item);

            Dictionary<string, string> data = new Dictionary<string, string>()
            {
                { "message", item.Text}
            };

            HttpConfiguration config = this.Configuration;

            GooglePushMessage message = new GooglePushMessage(data, System.TimeSpan.FromHours(1));

            try
            {
                var client = new PushClient(config);
                var result = await client.SendAsync(message);

                ServiceSettingsDictionary settings = config.GetServiceSettingsProvider().GetServiceSettings();
                config.Services.GetTraceWriter().Info(result.State.ToString());
            }
            catch (System.Exception ex)
            {
                config.Services.GetTraceWriter().Error(ex.Message, null, "Push.SendAsync Error");
            }

            return CreatedAtRoute("Tables", new { id = current.Id }, current);
        }

<!---HONumber=Oct15_HO3-->