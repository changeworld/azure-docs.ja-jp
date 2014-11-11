<properties linkid="develop-mobile-tutorials-optimistic-concurrent-data-dotnet" urlDisplayName="Optimistic concurrency" pageTitle="Handle database write conflicts with optimistic concurrency (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to handle database write conflicts on both the server and in your Windows Store application." metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Handling database write conflicts" authors="wesmc" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="09/23/2014" ms.author="wesmc" />

# データベースの書き込み競合の処理

<div class="dev-center-tutorial-selector sublanding">
<a href="/ja-jp/develop/mobile/tutorials/handle-database-write-conflicts-dotnet/" title="Windows ストア C#" class="current">Windows ストア C#</a>
<a href="/ja-jp/documentation/articles/mobile-services-windows-store-javascript-handle-database-conflicts/" title="Windows ストア JavaScript">Windows ストア JavaScript</a>
<a href="/ja-jp/develop/mobile/tutorials/handle-database-write-conflicts-wp8/" title="Windows Phone">Windows Phone</a></div>


このチュートリアルでは、Windows ストア アプリケーションの同じデータベース レコードに複数のクライアントが書き込みを行ったときに発生する競合を処理する方法について説明します。シナリオによっては、複数のクライアントが同じ項目に対して同時に変更を書き込む場合があります。競合を検知しない場合、それを意図していなくても、最後に行われた書き込みによってそれ以前の更新がすべて上書きされます。Azure モバイル サービスには、このような競合を検出して解決する機能がサポートされています。このトピックでは、サーバー上とアプリケーション内でデータベース書き込み競合を処理する手順を紹介します。

このチュートリアルでは、クイック スタート アプリケーションに機能を追加して、TodoItem データベースを更新するときに発生する競合を処理します。このチュートリアルでは、次の基本的な手順について説明します。

1.  [更新を実行できるようにアプリケーションを更新する]
2.  [アプリケーションでの競合検出を有効にする]
3.  [アプリケーションでデータベース書き込み競合をテストする]
4.  [サーバー スクリプトで競合の解決を自動的に処理する]

このチュートリアルには、次のものが必要です。

-   Microsoft Visual Studio 2012 Express for Windows 以降。
-   このチュートリアルは、モバイル サービスのクイック スタートに基づいています。このチュートリアルを開始する前に、「[モバイル サービスの使用]」を完了している必要があります。
-   [Azure アカウント]
-   Azure モバイル サービス NuGet パッケージ 1.1.0 以降。最新バージョンを入手するには、次の手順に従います。

    1.  Visual Studio のソリューション エクスプローラーで、プロジェクトを開いて右クリックし、**[NuGet パッケージの管理]** をクリックします。

        ![][19]

    2.  **[オンライン]** を展開し、**[Microsoft and .NET]** をクリックします。検索ボックスに「**Azure Mobile Services**」と入力します。**[Azure Mobile Services]** NuGet パッケージで **[インストール]** をクリックします。

        ![][20]

## <a name="uiupdate"></a><span class="short-header">UI の更新</span>更新を実行できるようにアプリケーションを更新する

このセクションでは、TodoList ユーザー インターフェイスを更新して、ListBox コントロールの各項目のテキストを更新できるようにします。ListBox にはデータベース テーブルの各項目に対応する CheckBox コントロールと TextBox コントロールが含まれます。TodoItem のテキスト フィールドを更新できるようになります。アプリケーションで TextBox の `LostFocus` イベントを処理して、データベース内の項目を更新できるようになります。

1.  Visual Studio で、「[モバイル サービスの使用]」チュートリアルでダウンロードした TodoList プロジェクトを開きます。
2.  Visual Studio ソリューション エクスプローラーで MainPage.xaml を開き、その中の `ListView` 定義を次に示す `ListView` で置き換えて、変更を保存します。

        <ListView Name="ListItems" Margin="62,10,0,0" Grid.Row="1">
            <ListView.ItemTemplate>
                <DataTemplate>
                    <StackPanel Orientation="Horizontal">
                        <CheckBox Name="CheckBoxComplete" IsChecked="{Binding Complete, Mode=TwoWay}" Checked="CheckBoxComplete_Checked" Margin="10,5" VerticalAlignment="Center"/>
                        <TextBox x:Name="ToDoText" Height="25" Width="300" Margin="10" Text="{Binding Text, Mode=TwoWay}" AcceptsReturn="False" LostFocus="ToDoText_LostFocus"/>
                    </StackPanel>
                </DataTemplate>
            </ListView.ItemTemplate>
        </ListView>

3.  MainPage.xaml.cs ファイルで、次の `using` ディレクティブをページの先頭に追加します。

        using System.Threading.Tasks;

4.  Visual Studio ソリューション エクスプローラーで、MainPage.xaml.cs を開きます。次に示すように、イベント ハンドラーを TextBox `LostFocus` イベントの MainPage に追加します。

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

5.  MainPage.xaml.cs で、次に示すように、イベント ハンドラーで参照される MainPage の `UpdateToDoItem()` メソッドの定義を追加します。

        private async Task UpdateToDoItem(TodoItem item)
        {
            Exception exception = null;         
            try
            {
                //update at the remote table
                await todoTable.UpdateAsync(item);
            }
            catch (Exception ex)
            {
                exception = ex;
            }           
            if (exception != null)
            {
                await new MessageDialog(exception.Message, "Update Failed").ShowAsync();
            }
        }

これで、TextBox からフォーカスが外れたときに各項目のテキストの変更がデータベースに書き戻されるようになります。

## <a name="enableOC"></a><span class="short-header">オプティミスティック同時実行制御の有効化</span>アプリケーションでの競合検出を有効にする

シナリオによっては、複数のクライアントが同じ項目に対して同時に変更を書き込む場合があります。競合を検知しない場合、それを意図していなくても、最後に行われた書き込みによってそれ以前の更新がすべて上書きされます。[オプティミスティック同時実行制御]では、それぞれのトランザクションがコミットでき、そのためリソース ロックが一切使用されないことを前提としています。オプティミスティック同時実行制御ではトランザクションをコミットする前に、他のトランザクションがそのデータを変更していないことを確認します。データが変更されている場合、トランザクションのコミットはロール バックされます。Azure のモバイル サービスはオプティミスティック同時実行制御をサポートしており、各テーブルに追加されている `__version` システム プロパティ列を使用して各項目の変更を追跡します。このセクションでは、アプリケーションで `__version` システム プロパティを利用してこのような書き込み競合を検出できるようにします。前回のクエリ以降にレコードが変更されていた場合、アプリケーションで更新しようとしたときに `MobileServicePreconditionFailedException` が通知されます。その際、データベースに変更をコミットするか、前回の変更をそのままデータベースに残すかというどちらかの処理を選択することができます。モバイル サービスのシステム プロパティの詳細については、[システム プロパティ]に関するページを参照してください。

1.  MainPage.xaml.cs で、**TodoItem** クラスの定義を次のコードに更新します。このコードには **__version** システム プロパティが含まれており、書き込み競合の検出がサポートされます。

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

    <div class="dev-callout">

    <b>注</b>
<br/>
<br/>
    型指定のないテーブルを使用する場合にオプティミスティック同時実行制御を有効にするには、テーブルの SystemProperties に Version フラグを追加します。

	<pre><code>//Enable optimistic concurrency by retrieving __version
todoTable.SystemProperties |= MobileServiceSystemProperties.Version;

    </div>

2.  `TodoItem` クラスに `Version` プロパティを追加することで、前回のクエリ以降にレコードが変更されていた場合、更新中にアプリケーションに対して `MobileServicePreconditionFailedException` 例外が通知されます。この例外には、サーバーから取得された最新バージョンの項目が含まれます。MainPage.xaml.cs に、例外を処理するための次のコードを `UpdateToDoItem()` メソッドに追加します。

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
                if (exception is MobileServicePreconditionFailedException)
                {
                    //Conflict detected, the item has changed since the last query
                    //Resolve the conflict between the local and server item
                    await ResolveConflict(item, ((MobileServicePreconditionFailedException<TodoItem>) exception).Item);
                }
                else
                {
                    await new MessageDialog(exception.Message, "Update Failed").ShowAsync();
                }
            }
        }

3.  MainPage.xaml.cs に、`UpdateToDoItem()` で参照される `ResolveConflict()` メソッドの定義を追加します。競合を解決する順序に注意してください。ローカル項目のバージョンをサーバーから取得された更新後のバージョンに設定した後で、ユーザーの決定をコミットします。この順序に従わない場合、競合が次々と発生します。

        private async Task ResolveConflict(TodoItem localItem, TodoItem serverItem)
        {
            //Ask user to choose the resolution between versions
            MessageDialog msgDialog = new MessageDialog(String.Format("Server Text: \"{0}\" \nLocal Text: \"{1}\"\n", 
                                                        serverItem.Text, localItem.Text), 
                                                        "CONFLICT DETECTED - Select a resolution:");
            UICommand localBtn = new UICommand("Commit Local Text");
            UICommand ServerBtn = new UICommand("Leave Server Text");
            msgDialog.Commands.Add(localBtn);
            msgDialog.Commands.Add(ServerBtn);          
            localBtn.Invoked = async (IUICommand command) =>
            {
                // To resolve the conflict, update the version of the 
                // item being committed. Otherwise, you will keep
                // catching a MobileServicePreConditionFailedException.
                localItem.Version = serverItem.Version;             
                // Updating recursively here just in case another 
                // change happened while the user was making a decision
                await UpdateToDoItem(localItem);
            };          
            ServerBtn.Invoked = async (IUICommand command) =>
            {
                RefreshTodoItems();
            };          
            await msgDialog.ShowAsync();
        }

## <a name="test-app"></a><span class="short-header">アプリケーションのテスト</span>アプリケーションでデータベース書き込み競合をテストする

このセクションでは、Windows ストア アプリケーション パッケージをビルドして、そのアプリケーションを 2 台目のコンピューターまたは仮想マシンにインストールします。その後、2 台のコンピューターでアプリケーションを実行し、書き込み競合を発生させて、コードをテストします。2 つのアプリケーション インスタンスが同じ項目の `text` プロパティを更新しようとして、ユーザーに競合の解決を要求します。

1.  Windows ストア アプリケーション パッケージを作成し、2 台目のコンピューターまたは仮想マシンにインストールします。それには、Visual Studio で **[プロジェクト]**、**[ストア]**、**[アプリケーション パッケージの作成]** の順にクリックします。

    ![][0]

2.  [パッケージの作成] 画面で、Windows ストアにこのパッケージをアップロードするかどうかを確認する質問に対して **[いいえ]** をクリックします。その後、**[次へ]** をクリックします。

    ![][1]

3.  [パッケージの選択と構成] 画面で、既定の設定をそのまま適用し、**[作成]** をクリックします。

    ![][10]

4.  [パッケージの作成が完了しました] 画面で、**[出力場所]** リンクをクリックして、パッケージの出力場所を開きます。

    ![][11]

5.  パッケージ フォルダー "todolist\_1.0.0.0\_AnyCPU\_Debug\_Test" を 2 台目のコンピューターにコピーします。このコンピューター上でこのパッケージ フォルダーを開き、次に示すように、**Add-AppDevPackage.ps1** PowerShell スクリプトを右クリックして **[PowerShell で実行]** をクリックします。表示される手順に従ってアプリケーションをインストールします。

    ![][12]

6.  Visual Studio で **[デバッグ]**、**[デバッグの開始]** の順にクリックして、アプリケーション インスタンス 1 を実行します。2 台目のコンピューターの [スタート] 画面で、下向きの矢印ボタンをクリックし、[アプリ 名前順] を表示します。**todolist** アプリケーションをクリックして、アプリケーション インスタンス 2 を実行します。

    アプリケーション インスタンス 1

    ![][2]

    アプリケーション インスタンス 2

    ![][2]

7.  アプリケーション インスタンス 1 で、最後の項目のテキストを「**Test Write 1**」に更新した後、`LostFocus` イベント ハンドラーによってデータベースが更新されるように、別のテキスト ボックスをクリックします。次のスクリーンショットのようになります。

    アプリケーション インスタンス 1

    ![][3]

    アプリケーション インスタンス 2

    ![][2]

8.  この時点で、アプリケーション インスタンス 2 内の対応する項目には古いバージョンが含まれています。このアプリケーション インスタンスで、`text` プロパティに「**Test Write 2**」と入力します。その後で別のテキスト ボックスをクリックすると、`LostFocus` イベント ハンドラーは古い `_version` プロパティでデータベースを更新しようとします。

    アプリケーション インスタンス 1

    ![][4]

    アプリケーション インスタンス 2

    ![][5]

9.  この更新の試行で使用された `__version` の値がサーバー側の `__version` の値と一致していないため、アプリケーションでこの競合を解決できるようにモバイル サービス SDK から `MobileServicePreconditionFailedException` がスローされます。競合を解決するには、**[Commit Local Text]** をクリックしてインスタンス 2 の値をコミットします。または、**[Leave Server Text]** をクリックしてインスタンス 2 の値を破棄し、アプリケーションのインスタンス 1 でコミットされた値を残します。

    アプリケーション インスタンス 1

    ![][4]

    アプリケーション インスタンス 2

    ![][6]

## <a name="scriptsexample"></a><span class="short-header">スクリプトを使用した競合処理</span>サーバー スクリプトで競合の解決を自動的に処理する

サーバー スクリプトで書き込み競合を検出し、解決することができます。ユーザーの操作ではなくスクリプト ロジックによって競合を解決できる場合、この方法を使用することをお勧めします。このセクションでは、アプリケーションで使用する TodoItem テーブルにサーバー側スクリプトを追加します。このスクリプトで使用されるロジックでは、次の方法で競合を解決します。

-   TodoItem の `complete` フィールドが true に設定されている場合、これは完了していると見なされ、`text` を変更できなくなります。
-   TodoItem の `complete` フィールドが false のままである場合、`text` の更新の試行がコミットされます。

次の手順で、サーバー更新スクリプトの追加とテストの方法を説明します。

1.  [Azure の管理ポータル]にログインし、**[モバイル サービス]** をクリックして、アプリケーションをクリックします。

    ![][7]

2.  **[データ]** タブをクリックし、**TodoItem** テーブルをクリックします。

    ![][8]

3.  **[スクリプト]** をクリックし、**[更新]** 操作を選択します。

    ![][9]

4.  既存のスクリプトを次の関数に置き換え、**[保存]** をクリックします。

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

5.  両方のコンピューターで **todolist** アプリケーションを実行します。インスタンス 2 で、最後の項目の TodoItem `text` を変更します。次に、`LostFocus` イベント ハンドラーによってデータベースが更新されるように、別のテキスト ボックスをクリックします。

    アプリケーション インスタンス 1	

    ![][4]

    アプリケーション インスタンス 2	

    ![][5]

6.  アプリケーション インスタンス 1 で、最後の text プロパティに別の値を入力します。その後で別のテキスト ボックスをクリックすると、`LostFocus` イベント ハンドラーは不適切な `__version` プロパティでデータベースを更新しようとします。

    アプリケーション インスタンス 1	

    ![][13]

    アプリケーション インスタンス 2	

    ![][14]

7.  この項目は完了としてマークされていないため、サーバー側スクリプトによって更新が許可され、競合が解決されることから、アプリケーション内では例外が発生しません。更新が本当に成功したかどうか確認するには、インスタンス 2 の **[Refresh]** をクリックして、データベースに再びクエリを実行してください。

    アプリケーション インスタンス 1

    ![][15]

    アプリケーション インスタンス 2

    ![][15]

8.  インスタンス 1 で、最後の Todo 項目のチェック ボックスをオンにして、完了としてマークします。

    アプリケーション インスタンス 1

    ![][16]

    アプリケーション インスタンス 2

    ![][15]

9.  インスタンス 2 で、最後の TodoItem のテキストを更新し、`LostFocus` イベントを発生させます。この項目は既に完了しているため、スクリプトが更新を拒否することで競合が解決されます。

    アプリケーション インスタンス 1	

    ![][17]

    アプリケーション インスタンス 2	

    ![][18]

## <a name="next-steps"> </a>次のステップ

このチュートリアルでは、Windows ストア アプリケーションでモバイル サービスのデータを操作する際の書き込み競合を処理できるようにする方法について説明しました。次に、データ シリーズの次のチュートリアルのいずれかを行うことをお勧めします。

-   [サーバー スクリプトを使用したモバイル サービスのデータの検証および変更]

    Mobile Services でサーバー スクリプトを使用して、アプリケーションから送信されたデータを検証および変更する方法について説明します。

-   [ページングを使用したモバイル サービス クエリの改善]

    クエリ内でページングを使用して、1 回の要求で渡されるデータの量を制御する方法について説明します。

データ シリーズを完了した後は、次に示すいずれかの Windows ストア チュートリアルを行うことができます。

-   [認証の使用]

    アプリケーションのユーザーを認証する方法について説明します。

-   [プッシュ通知の使用]

    Mobile Services を使用してアプリケーションにごく基本的なプッシュ通知を送信する方法について説明します。



  [更新を実行できるようにアプリケーションを更新する]: #uiupdate
  [アプリケーションでの競合検出を有効にする]: #enableOC
  [アプリケーションでデータベース書き込み競合をテストする]: #test-app
  [サーバー スクリプトで競合の解決を自動的に処理する]: #scriptsexample
  [モバイル サービスの使用]: /ja-jp/develop/mobile/tutorials/get-started
  [Azure アカウント]: http://www.windowsazure.com/ja-jp/pricing/free-trial/
  [オプティミスティック同時実行制御]: http://go.microsoft.com/fwlink/?LinkId=330935
  [システム プロパティ]: http://go.microsoft.com/fwlink/?LinkId=331143
  [Azure の管理ポータル]: https://manage.windowsazure.com/
  [サーバー スクリプトを使用したモバイル サービスのデータの検証および変更]: /ja-jp/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet
  [ページングを使用したモバイル サービス クエリの改善]: /ja-jp/develop/mobile/tutorials/add-paging-to-data-dotnet
  [認証の使用]: /ja-jp/develop/mobile/tutorials/get-started-with-users-dotnet
  [プッシュ通知の使用]: /ja-jp/develop/mobile/tutorials/get-started-with-push-dotnet
<!-- Images. -->
[0]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-create-app-package1.png
[1]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-create-app-package2.png
[2]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-app1.png 
[3]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-app1-write1.png
[4]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-app1-write2.png
[5]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-app2-write2.png
[6]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-app2-write2-conflict.png
[7]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/mobile-services-selection.png
[8]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/mobile-portal-data-tables.png
[9]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/mobile-insert-script-users.png
[10]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-create-app-package3.png
[11]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-create-app-package4.png
[12]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-install-app-package.png
[13]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-app1-write3.png
[14]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-app2-write3.png
[15]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-write3.png
[16]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-checkbox.png
[17]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-2-items.png
[18]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-already-complete.png
[19]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/mobile-manage-nuget-packages-VS.png
[20]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/mobile-manage-nuget-packages-dialog.png