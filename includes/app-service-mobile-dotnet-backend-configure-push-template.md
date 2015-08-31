1. Visual Studio の**ソリューション エクスプローラー**で、モバイル バックエンド プロジェクトの **[Controllers]** フォルダーを展開します。**TodoItemController.cs** を開きます。ファイルの先頭に、次の `using` ステートメントを追加します。

        using Microsoft.Azure.Mobile.Server.Notifications;


2. `PostTodoItem` メソッドを次のコードに置き換えます。
        
        public async Task<IHttpActionResult> PostTodoItem(TodoItem item)
        {
            TodoItem current = await InsertAsync(item);

            HttpConfiguration config = this.Configuration;

            TemplatePushMessage message = new TemplatePushMessage();
            message.Add("message", item.Text);

            try
            {
                var client = new PushClient(config);
                var result = await client.SendAsync(message);

                config.Services.GetTraceWriter().Info(result.State.ToString());
            }
            catch (System.Exception ex)
            {
                config.Services.GetTraceWriter().Error(ex.Message, null, "Push.SendAsync Error");
            }

            return CreatedAtRoute("Tables", new { id = current.Id }, current);
        }

<!---HONumber=August15_HO8-->