
##<a name="update-app"></a>カスタム API を呼び出すようにアプリケーションを更新する

1. Visual Studio でクイック スタート プロジェクトの default.html ファイルを開いて、 `buttonRefresh` という **button** 要素を探し、その直後に次の要素を新たに追加します。 

		<button id="buttonCompleteAll" style="margin-left: 5px">Complete All</button>

	新しいボタンがページに追加されます。 

2.  `js` プロジェクト フォルダー内の default.js コード ファイルを開いて、**refreshTodoItems** 関数を探し、次のコードが含まれていることを確認します。

	    todoTable.where({ complete: false })
	       .read()
	       .done(function (results) {
	           todoItems = new WinJS.Binding.List(results);
	           listItems.winControl.itemDataSource = todoItems.dataSource;
	       });            

	ここでは、完了済みの項目が返されないように、項目をフィルターで処理しています。

3. **refreshTodoItems** 関数の後に、次のコードを追加します。

		var completeAllTodoItems = function () {
		    var okCommand = new Windows.UI.Popups.UICommand("OK");
		
		    // Asynchronously call the custom API using the POST method. 
		    mobileService.invokeApi("completeall", {
		        body: null,
		        method: "post"
		    }).done(function (results) {
		        var message = results.result.count + " item(s) marked as complete.";
		        var dialog = new Windows.UI.Popups.MessageDialog(message);
		        dialog.commands.append(okCommand);
		        dialog.showAsync().done(function () {
		            refreshTodoItems();
		        });
		    }, function (error) {
		        var dialog = new Windows.UI.Popups
		            .MessageDialog(error.message);
		        dialog.commands.append(okCommand);
		        dialog.showAsync().done();
		    });
		};

        buttonCompleteAll.addEventListener("click", function () {
            completeAllTodoItems();
        });

	これは、新しいボタンの **Click** イベントを処理するメソッドです。POST 要求を新しいカスタム API に送信する **InvokeApiAsync** メソッドがクライアントで呼び出されます。カスタム API から返された結果は、メッセージ ダイアログに表示されます。エラーが発生した場合はそれらも表示されます。

## <a name="test-app"></a>アプリケーションをテストする

1. Visual Studio で **F5** キーを押してプロジェクトをリビルドし、アプリケーションを開始します。

2. アプリケーションで、**[Insert a TodoItem]** に任意のテキストを入力し、**[Save]** をクリックします。

3. 前の手順を繰り返して、複数の Todo 項目をリストに追加します。

4. **[Complete All]** ボタンをクリックします。

  	![](./media/mobile-services-windows-store-javascript-call-custom-api/mobile-custom-api-windows-store-completed.png)

	完了としてマークされた項目の数を示すメッセージ ダイアログが表示され、フィルター処理済みのクエリが再度実行されて、すべての項目がリストから消去されます。<!--HONumber=42-->
