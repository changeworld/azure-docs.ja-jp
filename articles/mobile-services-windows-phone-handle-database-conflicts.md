<properties pageTitle="オプティミスティック同時実行制御でデータベース書き込み競合を処理 (Windows ストア) | モバイル デベロッパー センター" description="サーバーと Windows ストア アプリケーションの両方でデータベースへの書き込みの競合を処理する方法について説明します。" documentationCenter="windows" authors="wesmc7777" manager="dwrede" editor="" services=""/>

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-phone" ms.devlang="dotnet" ms.topic="article" ms.date="09/23/2014" ms.author="wesmc"/>

# データベースの書き込み競合の処理

<div class="dev-center-tutorial-selector sublanding">
<a href="/ja-jp/develop/mobile/tutorials/handle-database-write-conflicts-dotnet/" title="Windows Store C#">Windows ストア C#</a>
<a href="/ja-jp/documentation/articles/mobile-services-windows-store-javascript-handle-database-conflicts/" title="Windows Store JavaScript">Windows ストア JavaScript</a>
<a href="/ja-jp/develop/mobile/tutorials/handle-database-write-conflicts-wp8/" title="Windows Phone" class="current">Windows Phone</a>
</div>

このチュートリアルでは、Windows Phone 8 アプリケーションの同じデータベース レコードに複数のクライアントが書き込みを行ったときに発生する競合を処理する方法について説明します。シナリオによっては、複数のクライアントが同じ項目に対して同時に変更を書き込む場合があります。競合を検知しない場合、それを意図していなくても、最後に行われた書き込みによってそれ以前の更新がすべて上書きされます。モバイル サービスには、このような競合を検出して解決する機能がサポートされています。このトピックでは、サーバー上とアプリケーション内でデータベース書き込み競合を処理する手順を紹介します。

このチュートリアルでは、クイック スタート アプリケーションに機能を追加して、TodoItem データベースを更新するときに発生する競合を処理します。このチュートリアルでは、次の基本的な手順について説明します。

1. [更新を実行できるようにアプリケーションを更新する]
2. [アプリケーションでの競合検出を有効にする]
3. [アプリケーションでデータベース書き込み競合をテストする]
4. [サーバー スクリプトで競合の解決を自動的に処理する]


このチュートリアルには、次のものが必要です。

+ Microsoft Visual Studio 2012 Express for Windows Phone 8 以降。
+ Windows 8 上で動作する [Windows Phone 8 SDK]。
+ [Azure アカウント]
+ このチュートリアルは、モバイル サービスのクイック スタートに基づいています。このチュートリアルを開始する前に、[Mobile Services の使用]に関するチュートリアルを完了している必要があります。
+ Azure モバイル サービス NuGet パッケージ 1.1.0 以降。最新バージョンを入手するには、次の手順に従います。
	1.Visual Studio のソリューション エクスプローラーで、プロジェクトを開いて右クリックし、**[NuGet パッケージの管理]** をクリックします。 

		![][13]

	2.**[オンライン]** を展開し、**[Microsoft and .NET]** をクリックします。検索ボックスに「**Azure Mobile Services**」と入力します。**[Azure Mobile Services]** NuGet パッケージで **[インストール]** をクリックします。

		![][14]


 

<h2><a name="uiupdate"></a>更新を実行できるようにアプリケーションを更新する</h2>

このセクションでは、TodoList ユーザー インターフェイスを更新して、ListBox コントロールの各項目のテキストを更新できるようにします。ListBox にはデータベース テーブルの各項目に対応する CheckBox コントロールと TextBox コントロールが含まれます。TodoItem のテキスト フィールドを更新できるようになります。アプリケーションで TextBox の `LostFocus` イベントを処理して、データベース内の項目を更新できるようになります。


1. Visual Studio で、[モバイル サービスの使用] チュートリアルでダウンロードした TodoList プロジェクトを開きます。
2. Visual Studio ソリューション エクスプローラーで MainPage.xaml を開き、その中の `phone:LongListSelector` 定義を、次に示す ListBox で置き換え、変更を保存します。

		<ListBox Grid.Row="4" Grid.ColumnSpan="2" Name="ListItems">
			<ListBox.ItemTemplate>
				<DataTemplate>
					<StackPanel Orientation="Horizontal">
						<CheckBox Name="CheckBoxComplete" IsChecked="{Binding Complete, Mode=TwoWay}" Checked="CheckBoxComplete_Checked" Margin="10,5" VerticalAlignment="Center"/>
						<TextBox x:Name="ToDoText" Width="330" Text="{Binding Text, Mode=TwoWay}" AcceptsReturn="False" LostFocus="ToDoText_LostFocus"/>
					</StackPanel>
				</DataTemplate>
			</ListBox.ItemTemplate>
		</ListBox>


2. Visual Studio ソリューション エクスプローラーで MainPage.xaml.cs を開き、次の `using` ディレクティブを追加します。

		using System.Threading.Tasks;


3. Visual Studio ソリューション エクスプローラーで MainPage.xaml.cs を開きます。次に示すように、MainPage に TextBox の `LostFocus` イベントのイベント ハンドラーを追加します。


        private async void ToDoText_LostFocus(object sender, RoutedEventArgs e)
        {
            TextBox tb = (TextBox)sender;
            TodoItem item = tb.DataContext as TodoItem;
            //let's see if the text changed
            if (item.Text != tb.Text)
            {
                item.Text = tb.Text;
                await UpdateToDoItem(item);
            }
        }

4. MainPage.xaml.cs で、次に示すように、イベント ハンドラーで参照される MainPage の `UpdateToDoItem()` メソッドの定義を追加します。

        private async Task UpdateToDoItem(TodoItem item)
        {
            try
            {
                //update at the remote table
                await todoTable.UpdateAsync(item);
            }
            catch (Exception ex)
            {
                MessageBox.Show(ex.Message, "Update Failed", MessageBoxButton.OK);
            }
        }

これで、TextBox からフォーカスが外れたときに各項目のテキストの変更がデータベースに書き戻されるようになります。

<h2><a name="enableOC"></a>アプリケーションでの競合検出を有効にする</h2>

シナリオによっては、複数のクライアントが同じ項目に対して同時に変更を書き込む場合があります。競合を検知しない場合、それを意図していなくても、最後に行われた書き込みによってそれ以前の更新がすべて上書きされます。[オプティミスティック同時実行制御]では、それぞれのトランザクションがコミットでき、そのためリソース ロックが一切使用されないことを前提としています。オプティミスティック同時実行制御ではトランザクションをコミットする前に、他のトランザクションがそのデータを変更していないことを確認します。データが変更されている場合、トランザクションのコミットはロール バックされます。Azure Mobile Services はオプティミスティック同時実行制御をサポートしており、各テーブルに追加されている `__version` システム プロパティ列を使用して各項目の変更を追跡します。このセクションでは、アプリケーションで `__version` システム プロパティを利用してこのような書き込み競合を検出できるようにします。前回のクエリ以降にレコードが変更されていた場合、アプリケーションで更新しようとしたときに `MobileServicePreconditionFailedException` によって通知されます。その際、データベースに変更をコミットするか、前回の変更をそのままデータベースに残すかというどちらかの処理を選択することができます。モバイル サービスのシステム プロパティの詳細については、[システム プロパティ]に関するページを参照してください。

1. MainPage.xaml.cs で、**TodoItem** クラスの定義を次のコードに更新します。このコードには **__version** システム プロパティが含まれており、書き込み競合の検出がサポートされます。

		public class TodoItem
		{
			public string Id { get; set; }            
			[JsonProperty(PropertyName = "text")]
			public string Text { get; set; }            
			[JsonProperty(PropertyName = "complete")]
			public bool Complete { get; set; }            
			[JsonProperty(PropertyName = "__version")]
			public string Version { set; get; }
		}

	> [AZURE.NOTE[ 型指定のないテーブルを使用する場合にオプティミスティック同時実行制御を有効にするには、テーブルの SystemProperties に Version フラグを追加します。  
	>
	>````` 
	//Enable optimistic concurrency by retrieving __version
todoTable.SystemProperties |= MobileServiceSystemProperties.Version;
`````


2.  `TodoItem` クラスに `Version` プロパティを追加することで、前回のクエリ以降にレコードが変更されていた場合、更新中にアプリケーションに対して `MobileServicePreconditionFailedException` 例外が通知されます。この例外には、サーバーから取得された最新バージョンの項目が含まれます。MainPage.xaml.cs で、例外を処理するための次のコードを `UpdateToDoItem()` メソッドに追加します。

        private async Task UpdateToDoItem(TodoItem item)
        {
            Exception exception = null;
            try
            {
                //update at the remote table
                await todoTable.UpdateAsync(item);
            }
            catch (MobileServicePreconditionFailedException<TodoItem> writeException)
            {
                exception = writeException;
            }
            catch (Exception ex)
            {
                exception = ex;
            }
            if (exception != null)
            {
                if (exception is MobileServicePreconditionFailedException<TodoItem>)
                {
                    //conflict detected, the item has changed since the last query
                    await ResolveConflict(item, ((MobileServicePreconditionFailedException<TodoItem>)exception).Item);
                }
                else
                    MessageBox.Show(exception.Message, "Update Failed", MessageBoxButton.OK);
            }
        }


3. MainPage.xaml.cs に、 `UpdateToDoItem()` で参照されている `ResolveConflict()` メソッドの定義を追加します。競合を解決する順序に注意してください。ローカル項目のバージョンをサーバーから取得された更新後のバージョンに設定した後で、ユーザーの決定をコミットします。この順序に従わない場合、競合が次々と発生します。


        private async Task ResolveConflict(TodoItem localItem, TodoItem serverItem)		
        {
            // Ask user to choose between the local text value or leaving the 
			// server's updated text value
            MessageBoxResult mbRes = MessageBox.Show(String.Format("The item has already been updated on the server.\n\n" +
                                                                   "Server value: {0} \n" +
                                                                   "Local value: {1}\n\n" +
                                                                   "Press OK to update the server with the local value.\n\n" +
                                                                   "Press CANCEL to keep the server value.", serverItem.Text, localItem.Text), 
                                                                   "CONFLICT DETECTED ", MessageBoxButton.OKCancel);
            // OK : After examining the updated text from the server, overwrite it
            //      with the changes made in this client.
            if (mbRes == MessageBoxResult.OK)
            {
                // Update the version of the item to the latest version
                // to resolve the conflict. Otherwise the exception
                // will be thrown again for the attempted update.
                localItem.Version = serverItem.Version;
                // Recursively updating just in case another conflict 
				// occurs while the user is deciding.
                await this.UpdateToDoItem(localItem);
            }
            // CANCEL : After examining the updated text from the server, leave 
			// the server item intact and refresh this client's query discarding 
			// the proposed changes.
            if (mbRes == MessageBoxResult.Cancel)
            {
                RefreshTodoItems();
            }
        }



<h2><a name="test-app"></a>アプリケーションでデータベース書き込み競合をテストする</h2>

このセクションでは、2 つの異なる Windows Phone 8 エミュレーター (WVGA と WVGA 512M) でアプリケーションを実行して、書き込み競合を処理するコードをテストします。2 つのクライアント アプリケーションが、同じ項目の `text` プロパティを更新しようとして、ユーザーに競合の解決を要求します。


1. Visual Studio で、次のスクリーン ショットに示すように、展開のターゲットを指定するボックスの一覧で **[Emulator WVGA 512MB]** が選択されていることを確認します。

	![][0]

2. Visual Studio のメニューで、**[ビルド]**、**[ソリューションの配置]** の順にクリックします。このエミュレーターが事前に実行されていない場合、エミュレーターに Windows Phone 8 オペレーティング システムが読み込まれるまで数分かかります。下の出力ウィンドウで、ビルドと Windows Phone 8 エミュレーターへの展開が正常に実行されたことを確認します。

	![][2]

3. Visual Studio で、展開のターゲットを **[Emulator WVGA]** に変更します。

	![][1]

4. Visual Studio のメニューで、**[ビルド]**、**[ソリューションの配置]** の順にクリックします。下の出力ウィンドウで、ビルドと Windows Phone 8 エミュレーターへの展開が正常に実行されたことを確認します。

   	![][2]
  
5. この 2 つのエミュレーターを並行して実行します。この 2 つのエミュレーターで実行されるクライアント アプリケーションの間での同時書き込み競合をシミュレートすることができます。どちらのエミュレーターでも、右から左方向へスワイプすると、インストールされているアプリケーションの一覧が表示されます。それぞれの一覧の下までスクロールして、**todolist** アプリケーションをクリックします。

	![][3]

6. 左側のエミュレーターで、最後の TodoItem の `text` を「**Test Write 1**」に更新した後、 `LostFocus` イベント ハンドラーによってデータベースが更新されるように、別のテキスト ボックスをクリックします。次のスクリーンショットのようになります。 

	![][4]

7. この時点で、右側のエミュレーター内の対応する項目には、古いバージョンと古いテキスト値が含まれています。右側のエミュレーターで、text プロパティに「**Test Write 2**」と入力します。その後で別のテキスト ボックスをクリックすると、右側のエミュレーターの `LostFocus` イベント ハンドラーが古いバージョンでデータベースを更新しようとします。

	![][5]

8. この更新の試行で使用されたバージョンの値がサーバー側のバージョンと一致していないため、アプリケーションでこの競合を解決できるように Mobile Services SDK から `MobileServicePreconditionFailedException` がスローされます。この競合を解決するには、**[ok]** をクリックして右側のアプリケーションの値をコミットします。または、**[cancel]** をクリックして右側のアプリケーションの値を破棄し、左側のアプリケーションでコミットされた値を残します。 

	![][6]



<h2><a name="scriptsexample"></a>サーバー スクリプトで競合の解決を自動的に処理する</h2>

サーバー スクリプトで書き込み競合を検出し、解決することができます。ユーザーの操作ではなくスクリプト ロジックによって競合を解決できる場合、この方法を使用することをお勧めします。このセクションでは、アプリケーションで使用する TodoItem テーブルにサーバー側スクリプトを追加します。このスクリプトで使用されるロジックでは、次の方法で競合を解決します。

+  TodoItem の ` complete` フィールドが true に設定されている場合、これは完了していると見なされ、 `text` を変更できなくなります。
+  TodoItem の ` complete` フィールドが false のままである場合、 `text` の更新の試行がコミットされます。

次の手順で、サーバー更新スクリプトの追加とテストの方法を説明します。

1. [Azure の管理ポータル] にログインし、**[モバイル サービス]** をクリックして、アプリケーションをクリックします。 

   	![][7]

2. **[データ]** タブをクリックし、**TodoItem** テーブルをクリックします。

   	![][8]

3. **[スクリプト]** をクリックし、**[更新]** 操作を選択します。

   	![][9]

4. 既存のスクリプトを次の関数に置き換え、**[保存]** をクリックします。

		function update(item, user, request) { 
			request.execute({ 
				conflict: function (serverRecord) {
					// Only committing changes if the item is not completed.
					if (serverRecord.complete === false) {
						//write the updated item to the table
						request.execute();
					}
					else
					{
						request.respond(statusCodes.FORBIDDEN, 'The item is already completed.');
					}
				}
			}); 
		}   
5. 左側のエミュレーターで、アプリケーションの最後の項目の TodoItem テキストを変更します。その後で別のテキスト ボックスをクリックすると、 `LostFocus` イベント ハンドラーによってデータベースが更新されます。

	![][4]

6. 右側のエミュレーターで、最後の TodoItem の text プロパティに別の値を入力します。その後で別のテキスト ボックスをクリックすると、右側のエミュレーターの `LostFocus` イベント ハンドラーが古いバージョンでデータベースを更新しようとします。

	![][5]

7. この項目は完了としてマークされていないため、サーバー側スクリプトによって更新が許可され、競合が解決されます。したがって、アプリケーション内では例外が発生しません。更新が本当に成功したかどうか確認するには、左側のエミュレーターのアプリケーションで **[Refresh]** をクリックして、データベースに再びクエリを実行してください。

	![][10]

8. 左側のエミュレーターで、最後の TodoItem のチェック ボックスをオンにして完了としてマークします。

	![][11]

9. 右側のエミュレーターのアプリで、同じ TodoItem のテキストを更新して `LostFocus` イベントを発生させます。この項目は既に完了しているため、スクリプトが更新を拒否することで競合が解決されます。 

	![][12]


## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Windows Phone 8 アプリケーションで Mobile Services のデータを操作する際の書き込み競合を処理できるようにする方法を説明しました。次に、データ シリーズの次のチュートリアルのいずれかを行うことをお勧めします。

* [スクリプトを使用したデータの検証および変更]
  <br/>モバイル サービスでサーバー スクリプトを使用して、アプリケーションから送信されたデータを検証および変更する方法について説明します。

* [ページングを使用したクエリの改善]
  <br/>クエリ内でページングを使用して、単一の要求で渡されるデータの量を制御する方法について説明します。

データ シリーズを完了した後は、次に示すいずれかの Windows Phone 8 チュートリアルを行うことができます。

* [認証の使用]
  <br/>アプリケーションのユーザーを認証する方法について説明します。

* [プッシュ通知の使用]
  <br/>モバイル サービスを使用してアプリケーションにごく基本的なプッシュ通知を送信する方法について説明します。
 
<!-- Anchors. -->
[更新を実行できるようにアプリケーションを更新する]: #uiupdate
[アプリケーションでの競合検出を有効にする]: #enableOC
[アプリケーションでデータベース書き込み競合をテストする]: #test-app
[サーバー スクリプトで競合の解決を自動的に処理する]: #scriptsexample
[次のステップ]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-windows-phone-handle-database-conflicts/mobile-EmulatorWVGA512MB.png
[1]: ./media/mobile-services-windows-phone-handle-database-conflicts/mobile-EmulatorWVGA.png
[2]: ./media/mobile-services-windows-phone-handle-database-conflicts/mobile-build-deploy-wp8.png
[3]: ./media/mobile-services-windows-phone-handle-database-conflicts/mobile-start-apps-oc-wp8.png
[4]: ./media/mobile-services-windows-phone-handle-database-conflicts/mobile-oc-apps-write1-wp8.png
[5]: ./media/mobile-services-windows-phone-handle-database-conflicts/mobile-oc-apps-write2-wp8.png
[6]: ./media/mobile-services-windows-phone-handle-database-conflicts/mobile-oc-apps-exception-wp8.png
[7]: ./media/mobile-services-windows-phone-handle-database-conflicts/mobile-services-selection.png
[8]: ./media/mobile-services-windows-phone-handle-database-conflicts/mobile-portal-data-tables.png
[9]: ./media/mobile-services-windows-phone-handle-database-conflicts/mobile-insert-script-users.png
[10]: ./media/mobile-services-windows-phone-handle-database-conflicts/mobile-oc-apps-insync-wp8.png
[11]: ./media/mobile-services-windows-phone-handle-database-conflicts/mobile-oc-apps-complete-checkbox-wp8.png
[12]: ./media/mobile-services-windows-phone-handle-database-conflicts/mobile-oc-apps-already-completed-wp8.png
[13]: ./media/mobile-services-windows-phone-handle-database-conflicts/mobile-manage-nuget-packages-VS.png
[14]: ./media/mobile-services-windows-phone-handle-database-conflicts/mobile-manage-nuget-packages-dialog.png


<!-- URLs. -->
[オプティミスティック同時実行制御]: http://go.microsoft.com/fwlink/?LinkId=330935
[Mobile Services の使用]: /ja-jp/develop/mobile/tutorials/get-started/#create-new-service
[ページングを使用したクエリの改善]: http://www.windowsazure.com/ja-jp/pricing/free-trial/
[スクリプトを使用したデータの検証および変更]: /ja-jp/develop/mobile/tutorials/validate-modify-and-augment-data-wp8
[Azure の管理ポータル]: /ja-jp/develop/mobile/tutorials/add-paging-to-data-wp8
[Mobile Services の使用]: /ja-jp/develop/mobile/tutorials/get-started-wp8
[データの使用]:./mobile-services-get-started-with-data-wp8.md
[認証の使用]: /ja-jp/develop/mobile/tutorials/get-started-with-users-wp8
[プッシュ通知の使用]: /ja-jp/develop/mobile/tutorials/get-started-with-push-wp8

[Azure の管理ポータル]: https://manage.windowsazure.com/
[管理ポータル]: https://manage.windowsazure.com/
[Windows Phone 8 SDK]: http://go.microsoft.com/fwlink/p/?LinkID=268374
[モバイル サービス SDK]: http://go.microsoft.com/fwlink/p/?LinkID=268375
[デベロッパー サンプル コード集のサイト]:  http://go.microsoft.com/fwlink/p/?LinkId=271146
[システムのプロパティ]: http://go.microsoft.com/fwlink/?LinkId=331143


<!--HONumber=42-->
