<properties pageTitle="オプティミスティック同時実行制御でデータベース書き込み競合を処理 (Windows ストア) | モバイル デベロッパー センター" writer="wesmc" description="サーバーと Windows ストア アプリケーションの両方でデータベースへの書き込みの競合を処理する方法について説明します。" documentationCenter="windows" authors="wesmc7777" manager="dwrede" editor="" services=""/>

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="09/23/2014" ms.author="wesmc"/>

# データベースの書き込み競合の処理

<div class="dev-center-tutorial-selector sublanding">
<a href="/ja-jp/develop/mobile/tutorials/handle-database-write-conflicts-dotnet/" title="Windows Store C#">Windows ストア C#</a>
<a href="/ja-jp/documentation/articles/mobile-services-windows-store-javascript-handle-database-conflicts/" title="Windows Store JavaScript" class="current">Windows ストア JavaScript</a>
<a href="/ja-jp/develop/mobile/tutorials/handle-database-write-conflicts-wp8/" title="Windows Phone">Windows Phone</a></div>	

このチュートリアルでは、Windows ストア アプリケーションの同じデータベース レコードに複数のクライアントが書き込みを行ったときに発生する競合を処理する方法について説明します。シナリオによっては、複数のクライアントが同じ項目に対して同時に変更を書き込む場合があります。競合を検知しない場合、それを意図していなくても、最後に行われた書き込みによってそれ以前の更新がすべて上書きされます。Azure モバイル サービスには、このような競合を検出して解決する機能がサポートされています。このトピックでは、サーバー上とアプリケーション内でデータベース書き込み競合を処理する手順を紹介します。

このチュートリアルでは、クイック スタート アプリケーションに機能を追加して、TodoItem データベースを更新するときに発生する競合を処理します。このチュートリアルでは、次の基本的な手順について説明します。

1. [更新を実行できるようにアプリケーションを更新する]
2. [アプリケーションでの競合検出を有効にする]
3. [アプリケーションでデータベース書き込み競合をテストする]
4. [サーバー スクリプトで競合の解決を自動的に処理する]


このチュートリアルには、次のものが必要です。

+ Microsoft Visual Studio 2013 Express for Windows 以降。
+ このチュートリアルは、モバイル サービスのクイック スタートに基づいています。このチュートリアルを開始する前に、「[Mobile Services の使用]」を完了してスタート プロジェクトの JavaScript 言語バージョンをダウンロードしている必要があります。 
+ [Azure アカウント]
+ Windows Azure Mobile Services NuGet パッケージ 1.1.5 以降。最新バージョンを入手するには、次の手順に従います。
	1. Visual Studio のソリューション エクスプローラーで、プロジェクトを開いて右クリックし、**[NuGet パッケージの管理]** をクリックします。 


	2. **[オンライン]** を展開し、**[Microsoft and .NET]** をクリックします。検索ボックスに「**WindowsAzure.MobileServices.WinJS**」と入力します。**WinJS 用の Windows Azure Mobile Services** NuGet パッケージで **[インストール]** をクリックします。

		![][20]


 

<h2><a name="uiupdate"></a>更新を実行できるようにアプリケーションを更新する</h2>

このセクションでは、ユーザー インターフェイスを更新して、各項目のテキストを更新できるようにします。バインド テンプレートには、データベース テーブルの各項目に対応するチェックボックスおよびテキスト クラス コントロールが含まれます。TodoItem のテキスト フィールドを更新できるようになります。アプリケーションは  `keydown` イベントを処理します。項目は **Enter** キーを押すと更新されます。


1. Visual Studio で、「[モバイル サービスの使用]」チュートリアルでダウンロードした TodoList プロジェクトの JavaScript 言語バージョンを開きます。
2. Visual Studio ソリューション エクスプローラーで default.html を開き、その中の `TemplateItem` div タグ定義を、次に示す div タグで置き換え、変更を保存します。これによってテキスト ボックス コントロールが追加され、TodoItem のテキストを編集できるようになります。

        <div id="TemplateItem" data-win-control="WinJS.Binding.Template">
          <div style="display: -ms-grid; -ms-grid-columns: auto 1fr">
            <input class="itemCheckbox" type="checkbox" data-win-bind="checked: complete; dataContext: this" />
              <div style="-ms-grid-column: 2; -ms-grid-row-align: center; margin-left: 5px">
                <input id="modifytextbox" class="text win-interactive" data-win-bind="value: text; dataContext: this" />
              </div>
          </div>
        </div>


3. Visual Studio のソリューション エクスプローラーで、**js** フォルダーを展開します。default.js ファイルを開いて `updateTodoItem` 関数を次の定義で置き換えますが、ユーザー インターフェイスの更新された項目は削除されません。

        var updateTodoItem = function (todoItem) {
          // This code takes a freshly completed TodoItem and updates the database. 
          todoTable.update(todoItem);
          };


4. default.js ファイルで、 `keydown` イベントに次のイベント ハンドラーを追加します。**Enter** キーを押すと項目が更新されます。

        listItems.onkeydown = function (eventArgs) {
          if (eventArgs.key == "Enter") {
            var todoItem = eventArgs.target.dataContext.backingData;
            todoItem.text = eventArgs.target.value;
            updateTodoItem(todoItem);
            }
          };

これで、**Enter** キーが押されたときに各項目のテキストの変更がデータベースに書き戻されるようになります。

<h2><a name="enableOC"></a>アプリケーションでの競合検出を有効にする</h2>

Azure Mobile Services はオプティミスティック同時実行制御をサポートしており、各テーブルに追加されている `__version` システム プロパティ列を使用して各項目の変更を追跡します。このセクションでは、アプリケーションで `__version` システム プロパティを利用してこのような書き込み競合を検出できるようにします。このシステム プロパティが todoTable で有効になると、前回のクエリ以降にレコードが変更されていた場合、更新中にアプリケーションに対して `MobileServicePreconditionFailedException` が通知されます。その際、アプリケーションではデータベースに変更をコミットするか、前回の変更をそのままデータベースに残すかというどちらかの処理を選択することができます。モバイル サービスのシステム プロパティの詳細については、[システム プロパティ]に関するページを参照してください。

1. default.js ファイルの `todoTable` 変数の宣言の下に次のコードを追加します。このコードには **__version** システム プロパティが含まれており、書き込み競合の検出がサポートされます。

        var todoTable = client.getTable('TodoItem');
        todoTable.systemProperties |= WindowsAzure.MobileServiceTable.SystemProperties.Version;


2. テーブルのシステム プロパティに `Version` システム プロパティを追加することで、前回のクエリ以降にレコードが変更されていた場合、更新中にアプリケーションに対して  `MobileServicePreconditionFailedException` 例外が通知されます。この例外は JavaScript でエラー関数を使用して捕捉されます。エラーには、競合を解決するために使用されるサーバーの項目の最新バージョンが含まれます。default.js で `updateTodoItem` 関数を更新してエラーを補足し `resolveDatabaseConflict` 関数を呼び出します。

        var updateTodoItem = function (todoItem) {
          // This code takes a freshly completed TodoItem and updates the database. 
          // If the server version of the record has been updated, we get the updated
          // record from the Precondition Failed error in order to resolve the conflict.
          var serverItem = null;
          todoTable.update(todoItem).then(null, function (error) {
            if (error.message == "Precondition Failed") {
              serverItem = error.serverInstance;
            }
            else {
              var msgDialog =
                new Windows.UI.Popups.MessageDialog(error.request.responseText,"Update Failed");
                msgDialog.showAsync();
            }
          }).done(function () {
            if (serverItem != null)
              resolveDatabaseConflict(todoItem, serverItem);
          });
        };


3. default.js で、 `updateTodoItem` 関数で参照される `resolveDatabaseConflict()` 関数の定義を追加します。競合を解決する順序に注意してください。ローカル項目のバージョンをサーバーから取得された更新後のバージョンに設定した後で、データベースの項目を更新します。この順序に従わない場合、競合が次々と発生します。


        var resolveDatabaseConflict = function (localItem, serverItem) {
          var content = "This record has been changed as follows on the server already..\n\n" +
              "id : " + serverItem.id + "\n" +
              "text : " + serverItem.text + "\n" +
              "complete : " + serverItem.complete + "\n\n" +
              "Do you want to overwrite the server instance with your data?";
          var msgDialog = new Windows.UI.Popups.MessageDialog(content, "Resolve Database Conflict");
          msgDialog.commands.append(new Windows.UI.Popups.UICommand("Yes"));
          msgDialog.commands.append(new Windows.UI.Popups.UICommand("No"));
          msgDialog.showAsync().done(function (command) {
              if (command.label == "Yes") {
                  localItem.__version = serverItem.__version;
                  updateTodoItem(localItem);
              }
          });
        }


<h2><a name="test-app"></a>アプリケーションでデータベース書き込み競合をテストする</h2>

このセクションでは、Windows ストア アプリケーション パッケージをビルドして、そのアプリケーションを 2 台目のコンピューターまたは仮想マシンにインストールします。その後、2 台のコンピューターでアプリケーションを実行し、書き込み競合を発生させて、コードをテストします。2 つのアプリケーション インスタンスが同じ項目の `text` プロパティを更新しようとして、ユーザーに競合の解決を要求します。


1. Windows ストア アプリケーション パッケージを作成し、2 台目のコンピューターまたは仮想マシンにインストールします。それには、Visual Studio で **[プロジェクト]**、**[ストア]**、**[アプリケーション パッケージの作成]** の順にクリックします。

	![][0]

2. [パッケージの作成] 画面で、Windows ストアにこのパッケージをアップロードするかどうかを確認する質問に対して **[いいえ]** をクリックします。その後、**[次へ]** をクリックします。

	![][1]

3. [パッケージの選択と構成] 画面で、既定の設定をそのまま適用し、**[作成]** をクリックします。

	![][10]

4. [パッケージの作成が完了しました] 画面で、**[出力場所]** リンクをクリックして、パッケージの出力場所を開きます。

   	![][11]

5. パッケージ フォルダー "todolist_1.0.0.0_AnyCPU_Debug_Test" を 2 台目のコンピューターにコピーします。このコンピューター上でこのパッケージ フォルダーを開き、次に示すように、**Add-AppDevPackage.ps1** PowerShell スクリプトを右クリックして **[PowerShell で実行]** をクリックします。表示される手順に従ってアプリケーションをインストールします。

	![][12]
  
6. Visual Studio で **[デバッグ]**、**[デバッグの開始]** の順にクリックして、アプリケーション インスタンス 1 を実行します。2 台目のコンピューターの [スタート] 画面で、下向きの矢印ボタンをクリックし、[アプリ 名前順] を表示します。**todolist** アプリケーションをクリックして、アプリケーション インスタンス 2 を実行します。 

	アプリケーション インスタンス 1	
	![][2]

	アプリケーション インスタンス 2	
	![][2]


7. アプリケーション インスタンス 1 で、最後の項目のテキストを **Test Write 1** に更新した後、**Enter** キーを押してデータベースを更新します。次のスクリーンショットのようになります。
	
	アプリケーション インスタンス 1	
	![][3]

	アプリケーション インスタンス 2	
	![][2]

8. この時点で、アプリケーション インスタンス 2 内の最後の項目には項目の古いバージョンが含まれています。そのアプリケーション インスタンスで、最後の項目の `text` プロパティに「**Test Write 2**」を入力し、**Enter** キーを押して、使用していない `_version` プロパティでデータベースを更新します。

	アプリケーション インスタンス 1	
	![][4]

	アプリケーション インスタンス 2	
	![][5]

9. この更新の試行で使用された `__version` の値は、サーバー側の `__version` 値と一致していないため、アプリケーション側でこの競合を解決できるように Mobile Services SDK から  `updateTodoItem` 関数のエラーとして  `MobileServicePreconditionFailedException` がスローされます。競合を解決するには **[はい]** をクリックし、インスタンス 2 の値をコミットします。または、**[いいえ]** をクリックしてインスタンス 2 の値を削除し、コミットしたアプリケーションのインスタンス 1 の値をそのまま残します。 

	アプリケーション インスタンス 1	
	![][4]

	アプリケーション インスタンス 2	
	![][6]



<h2><a name="scriptsexample"></a>サーバー スクリプトで競合の解決を自動的に処理する</h2>

サーバー スクリプトで書き込み競合を検出し、解決することができます。ユーザーの操作ではなくスクリプト ロジックによって競合を解決できる場合、この方法を使用することをお勧めします。このセクションでは、アプリケーションで使用する TodoItem テーブルにサーバー側スクリプトを追加します。このスクリプトで使用されるロジックでは、次の方法で競合を解決します。

+  TodoItem の ` complete` フィールドが true に設定されている場合、これは完了していると見なされ、 `text` を変更できなくなります。
+  TodoItem の ` complete` フィールドが false のままである場合、 `text` の更新の試行がコミットされます。

次の手順で、サーバー更新スクリプトの追加とテストの方法を説明します。

1. [Azure 管理ポータル] にログインし、**[モバイル サービス]** をクリックして、アプリケーションをクリックします。 

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
5. 両方のコンピューターで **todolist** アプリケーションを実行します。インスタンス 2 で、最後の項目の TodoItem `text` を変更し、**Enter** キーを押してデータベースが更新されるようにします。

	アプリケーション インスタンス 1	
	![][4]

	アプリケーション インスタンス 2	
	![][5]

6. アプリケーション インスタンス 1 で、最後の text プロパティに別の値を入力し、**Enter** キーを押します。アプリケーションは不適切な `__version` プロパティでデータベースを更新しようとします。

	アプリケーション インスタンス 1	
	![][13]

	アプリケーション インスタンス 2	
	![][14]

7. この項目は完了としてマークされていないため、サーバー側スクリプトによって更新が許可され、競合が解決されることから、アプリケーション内では例外が発生しません。更新が本当に成功したかどうか確認するには、インスタンス 2 の **[Refresh]** をクリックして、データベースに再びクエリを実行してください。

	アプリケーション インスタンス 1	
	![][15]

	アプリケーション インスタンス 2	
	![][15]

8. インスタンス 1 で、最後の Todo 項目のチェック ボックスをオンにして、完了としてマークします。

	アプリケーション インスタンス 1	
	![][16]

	アプリケーション インスタンス 2	
	![][15]

9. インスタンス 2 で、最後の TodoItem のテキストを更新し、**Enter** キーを押します。[完了] フィールドの値が true に更新されたため、競合が発生します。この項目は既に完了しているため、スクリプトが更新を拒否することで競合が解決されます。スクリプトによって、応答でメッセージが示されました。  

	アプリケーション インスタンス 1	
	![][17]

	アプリケーション インスタンス 2	
	![][18]

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Windows ストア アプリケーションでモバイル サービスのデータを操作する際の書き込み競合を処理できるようにする方法について説明しました。次に、データ シリーズの次のチュートリアルのいずれかを行うことをお勧めします。

* [スクリプトを使用したデータの検証および変更]
  <br/>モバイル サービスでサーバー スクリプトを使用して、アプリケーションから送信されたデータを検証および変更する方法について説明します。

* [ページングを使用したクエリの改善]
  <br/>クエリ内でページングを使用して、単一の要求で渡されるデータの量を制御する方法について説明します。

データ シリーズを完了した後は、次に示すいずれかの Windows ストア チュートリアルを行うことができます。

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
[0]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-create-app-package1.png
[1]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-create-app-package2.png
[2]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-app1.png 
[3]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-app1-write1.png
[4]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-app1-write2.png
[5]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-app2-write2.png
[6]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-app2-write2-conflict.png
[7]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/mobile-services-selection.png
[8]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/mobile-portal-data-tables.png
[9]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/mobile-insert-script-users.png
[10]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-create-app-package3.png
[11]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-create-app-package4.png
[12]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-install-app-package.png
[13]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-app1-write3.png
[14]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-app2-write3.png
[15]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-write3.png
[16]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-checkbox.png
[17]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-2-items.png
[18]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-already-complete.png
[19]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/mobile-manage-nuget-packages-VS.png
[20]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/mobile-manage-nuget-packages-dialog.png

<!-- URLs. -->
[オプティミスティック同時実行制御]: http://go.microsoft.com/fwlink/?LinkId=330935
[Mobile Services の使用]: /ja-jp/develop/mobile/tutorials/get-started/#create-new-service
[Azure アカウント]: http://www.windowsazure.com/ja-jp/pricing/free-trial/
[スクリプトを使用したデータの検証および変更]: /ja-jp/documentation/articles/mobile-services-windows-store-javascript-validate-modify-data-server-scripts/
[ページングを使用したクエリの改善]: /ja-jp/documentation/articles/mobile-services-windows-store-javascript-add-paging-data/
[モバイル サービスの使用]: /ja-jp/develop/mobile/tutorials/get-started
[認証の使用]: /ja-jp/documentation/articles/mobile-services-windows-store-javascript-get-started-users/
[プッシュ通知の使用]: /ja-jp/documentation/articles/mobile-services-windows-store-javascript-get-started-push/

[Azure 管理ポータル]: https://manage.windowsazure.com/
[管理ポータル]: https://manage.windowsazure.com/
[Windows Phone 8 SDK]: http://go.microsoft.com/fwlink/p/?LinkID=268374
[モバイル サービス SDK]: http://go.microsoft.com/fwlink/p/?LinkID=268375
[デベロッパー サンプル コード集のサイト]:  http://go.microsoft.com/fwlink/p/?LinkId=271146
[システム プロパティ]: http://go.microsoft.com/fwlink/?LinkId=331143


<!--HONumber=42-->
