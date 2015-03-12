

1. todoItems リストを定義するコード行のすぐ下にある default.js ファイルで、次の関数の定義を追加します。
 
        // Add a filter that adds a header to prevent caching. This makes sure that the 
		// latest data is returned when the 'Refresh; button is clicked.        
        var noCachingFilter = function (request, next, callback) {
            if (request.type === 'GET' && !request.headers['If-Modified-Since']) {
                request.headers['If-Modified-Since'] = 'Mon, 27 Mar 1972 00:00:00 GMT';
            }
            next(request, callback);
        };

	クライアントで caching しないようにする  `If-Modified-Since` ヘッダーを追加するフィルター関数が定義されます。
 
2. 次に、コメントを解除するか次のコード行を追加して、`<yourClient>` をプロジェクトをモバイル サービスに接続したときの service.js ファイルに追加された変数で置き換えます。

		var todoTable = <yourClient>.withFilter(noCachingFilter).getTable('TodoItem');

   	This code creates a proxy object (**todoTable**) for the new database table, using the caching filter. 

3. **InsertTodoItem** 関数を次のコードに置き換えます。

		var insertTodoItem = function (todoItem) {
		    // Inserts a new row into the database. When the operation completes
		    // and Mobile Services has assigned an id, the item is added to the binding list.
		    todoTable.insert(todoItem).done(function (item) {
		        todoItems.push(item);
		    });
		};

	このコードでは、新しい項目をテーブルに挿入します。

3. **RefreshTodoItems** 関数を次のコードで置き換えます。

        var refreshTodoItems = function () {
            // This code refreshes the entries in the list by querying the table.
            // Results are filtered to remove completed items.
            todoTable.where({ complete: false })
                .read().done(function (results) {
                todoItems = new WinJS.Binding.List(results);
                listItems.winControl.itemDataSource = todoItems.dataSource;
            });
        };

   	これにより、todoTable 内で、モバイル サービスから返されたすべての **TodoItem** オブジェクトが格納される項目のコレクションへのバインディングが設定されます。 

4. **UpdateCheckedTodoItem** 関数を次のコードで置き換えます。
        
        var updateCheckedTodoItem = function (todoItem) {
            // This code takes a freshly completed TodoItem and updates the database. 
            todoTable.update(todoItem);
            // Remove the completed item from the filtered list.
            todoItems.splice(todoItems.indexOf(todoItem), 1);
        };

   	これにより、項目の更新がモバイル サービスに送信されます。

バックエンド ストレージのモバイル サービスを使用するようにアプリケーションを更新した後は、モバイル サービスに対してアプリケーションをテストします。<!--HONumber=42-->
