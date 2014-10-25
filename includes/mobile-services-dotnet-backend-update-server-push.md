1.  Visual Studio のソリューション エクスプローラーで、モバイル サービス プロジェクト内の **Controllers** フォルダーを右クリックします。TodoItemController.cs を開き、次のコードを使用して `PostTodoItem` メソッドの定義を更新します。

        public async Task<IHttpActionResult> PostTodoItem(TodoItem item)
        {
            TodoItem current = await InsertAsync(item);

            // Create a WNS native toast.
            WindowsPushMessage message = new WindowsPushMessage();

            // Define the XML paylod for a WNS native toast notification 
            // that contains the text of the inserted item.
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

    > [WACOM.NOTE] テンプレート通知を使用して、1 つのプッシュ通知を複数のプラットフォームのクライアントに送信できます。詳細については、「[1 つのモバイル サービスから複数のクライアントをサポートするための考慮事項][1 つのモバイル サービスから複数のクライアントをサポートするための考慮事項]」を参照してください。

2.  モバイル サービス プロジェクトを Azure に対して再発行します。

  [1 つのモバイル サービスから複数のクライアントをサポートするための考慮事項]: /ja-jp/documentation/articles/mobile-services-how-to-use-multiple-clients-single-service/#push
