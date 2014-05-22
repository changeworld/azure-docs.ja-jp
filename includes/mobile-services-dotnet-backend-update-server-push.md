1. Visual Studio のソリューション エクスプローラーで、モバイル サービス プロジェクト内の **[コントローラー]** フォルダーを右クリックします。TodoItemController.cs を開き、次のコードを使用して `PostTodoItem` メソッドの定義を更新します。

        public async Task<IHttpActionResult> PostTodoItem(TodoItem item)
        {
            TodoItem current = await InsertAsync(item);
            WindowsPushMessage message = new WindowsPushMessage();
            message.XmlPayload = @"<?xml version=""1.0"" encoding=""utf-8""?>" +
                                 @"<toast><visual><binding template=""ToastText01"">" +
                                 @"<text id=""1"">" + item.Text + @"</text>" +
                                 @"</binding></visual></toast>";
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




