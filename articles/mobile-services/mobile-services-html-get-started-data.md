<properties 
	pageTitle="既存のアプリケーションへの Mobile Services の追加 (HTML 5) | Microsoft Azure" 
	description="既存の HTML アプリで Mobile Services を使用する方法について説明します。" 
	services="mobile-services" 
	documentationCenter="" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-html" 
	ms.devlang="javascript" 
	ms.topic="article" 
	ms.date="08/16/2015" 
	ms.author="glenga"/>

# 既存のアプリケーションへの Mobile Services の追加

[AZURE.INCLUDE [mobile-services-selector-get-started-data](../../includes/mobile-services-selector-get-started-data.md)]

##概要 

このトピックでは、Azure のモバイル サービスを使用して HTML アプリケーションのデータを活用する方法について説明します。このチュートリアルでは、メモリにデータを格納するアプリケーションをダウンロードした後、新しいモバイル サービスを作成してそれをアプリケーションに統合します。その後、Azure 管理ポータルにサインインして、アプリケーションの実行中にデータに加えられた変更を表示します。

>[AZURE.NOTE]このチュートリアルの目的は、モバイル サービスが HTML アプリケーションのデータを Azure に格納および取得できるようにするしくみを説明することにあります。したがって、このトピックでは、モバイル サービスのクイック スタートで完了している手順の多くについても説明します。モバイル サービスを初めて使用する場合は、最初にチュートリアル「[モバイル サービスの使用]」を完了することをお勧めします。

> [AZURE.IMPORTANT]このチュートリアルを完了するには、Azure アカウントが必要です。アカウントがない場合は、無料の試用アカウントを数分で作成することができます。詳細については、[Azure の無料評価版サイト](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A756A2826&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2documentation%2Farticles%2Fmobile-services-html-get-started-data)を参照してください。

###その他の要件

GetStartedWithData アプリケーションは、任意の Web サーバーでホストできます。ただし、便宜を図るために、アプリケーションを `http://localhost:8000` で実行できるようにするスクリプトが提供されています。
 
+ localhost を使用するには、ローカル コンピューター上で次のいずれかの Web サーバーが実行されている必要があります。

	+  **Windows**: IIS ExpressIIS Express は、[Microsoft Web プラットフォーム インストーラー] によってインストールされます。   
	+  **MacOS X**: Python。既にインストールされています。
	+  **Linux**: Python。[最新バージョンの Python] をインストールする必要があります。 
	
	このアプリケーションは任意の Web サーバーを使用してホストすることができますが、これらの Web サーバーは、ダウンロードしたスクリプトでサポートされています。

+ HTML5 をサポートする Web ブラウザー。

##<a name="download-app"></a>GetStartedWithData プロジェクトをダウンロードする

このチュートリアルは、HTML5 アプリケーションの [GetStartedWithData アプリケーション]に基づいています。このアプリケーションの UI は、追加された項目がメモリにローカルに格納される点を除き、モバイル サービスのクイック スタートで生成したアプリケーションと同じです。

1. [HTML アプリケーション プロジェクト ファイルをダウンロードします][GetStartedWithData app]。

2. HTML エディターで、ダウンロードしたプロジェクトを開き、app.js ファイルを確認します。

   	追加された項目はメモリ内の **Array** オブジェクト (**staticItems**) に格納される点に注目してください。ページを更新すると、データは消えます。データは保持されません。

3. **server** サブフォルダーから次のいずれかのコマンド ファイルを実行します。

	+ **launch-windows** (Windows コンピューター) 
	+ **launch-mac.command** (Mac OS X コンピューター)
	+ **launch-linux.sh** (Linux コンピューター)

	> [AZURE.NOTE]Windows コンピューターでは、PowerShell からスクリプトの実行の確認を求められた場合は、「`R`」と入力します。Web ブラウザーでは、インターネットからダウンロードしたスクリプトであるため、実行しないよう警告されることがあります。その場合は、ブラウザーがスクリプトの読み込みを開始するよう要求する必要があります。
	
	これにより、新しいアプリケーションをホストする Web サーバーがローカル コンピューター上で起動します。

4. Web ブラウザーで URL <a href="http://localhost:8000/" target="_blank">http://localhost:8000/</a> を開いて、アプリケーションを開始します。

5. アプリケーションで、意味のあるテキスト (たとえば、_チュートリアルの完了_) を **[Enter new task]** に入力し、**[追加]** をクリックします。

   	![][0]

   	保存したテキストが **staticItems** 配列に追加され、ページが更新されて新しい項目が表示されます。

##<a name="create-service"></a>管理ポータルで新規モバイル サービスを作成する

[AZURE.INCLUDE [mobile-services-create-new-service-data](../../includes/mobile-services-create-new-service-data.md)]

##<a name="add-table"></a>新しいテーブルをモバイル サービスに追加する

アプリケーション データを新しいモバイル サービスに格納できるようにするには、最初に新しいテーブルを関連付けられた SQL データベース インスタンスに作成する必要があります。

1. 管理ポータルで、**[モバイル サービス]** をクリックし、先ほど作成したモバイル サービスをクリックします。

2. **[データ]** タブをクリックし、**[作成]** をクリックします。

   	**[新しいテーブルの作成]** ダイアログ ボックスが表示されます。

3. **[テーブル名]** に「_TodoItem_」と入力し、チェック ボタンをクリックします。

  	これにより、既定のアクセス許可が設定された新しいストレージ テーブル **TodoItem** が作成されます。これは、アプリケーションと共に配布されるアプリケーション キーを使用してだれもがテーブル内のデータにアクセスし、変更を加えることができることを表します。テーブルは、特定のモバイル サービスに固有のスキーマ内で作成されています。これには、複数のモバイル サービスで同じデータベースを使用した場合にデータの競合を回避する目的があります。

4. 新しい **TodoItem** テーブルをクリックし、データ行がないことを確認します。

	>[AZURE.NOTE]Id、\_\_createdAt、\_\_updatedAt、および \_\_version の列を含む新しいテーブルが作成されます。動的スキーマが有効な場合、挿入または更新の要求に含まれる JSON オブジェクトに基づいて、モバイル サービスによって自動的に新しい列が生成されます。詳細については、「[動的スキーマ](http://msdn.microsoft.com/library/windowsazure/jj193175.aspx)」を参照してください。

6. **[構成]** タブで、**[クロス オリジン リソース共有 (CORS)]** の **[ホスト名からの要求を許可する]** に `localhost` が既に表示されていることを確認します。表示されていない場合は、**[ホスト名]** フィールドに「`localhost`」と入力して、**[保存]** をクリックします。


	> [AZURE.IMPORTANT]localhost 以外の Web サーバーにクイック スタート アプリケーションを展開する場合は、**[ホスト名からの要求を許可する]** の一覧に Web サーバーのホスト名を追加する必要があります。詳細については、「[クロス オリジン リソース共有](http://msdn.microsoft.com/library/windowsazure/dn155871.aspx"%20target="_blank)」を参照してください。

これで、新しいモバイル サービスをアプリケーションのデータ ストレージとして使用する準備が整いました。

##<a name="update-app"></a>モバイル サービスをデータ アクセスに使用するためにアプリケーションを更新する

モバイル サービスの準備が整ったら、ローカル コレクションの代わりにモバイル サービスに項目を格納するようにアプリケーションを更新します。

3. 管理ポータルで、**[モバイル サービス]** をクリックし、先ほど作成したモバイル サービスをクリックします。

4. **[ダッシュボード]** タブをクリックし、**[Mobile Service URL]** をメモに記録します。次に、**[キーの管理]** をクリックし、**[アプリケーション キー]** をメモに記録します。

  	これらの値は、アプリケーション コードからモバイル サービスにアクセスするときに必要になります。

1. Web エディターで、index.html プロジェクト ファイルを開き、次のコードをページのスクリプト参照に追加します。

        <script src="http://ajax.aspnetcdn.com/ajax/mobileservices/MobileServices.Web-1.2.5.min.js"></script>

5. エディターで、app.js ファイルを開き、**MobileServiceClient** 変数を定義している次のコードをコメント解除します。さらに、モバイル サービスの URL とアプリケーション キーを **MobileServiceClient** コンストラクターに順に指定します。

	    var MobileServiceClient = WindowsAzure.MobileServiceClient,
			client = new MobileServiceClient('AppUrl', 'AppKey'),   		    

  	これで、モバイル サービスにアクセスするために使用される MobileServiceClient の新しいインスタンスが作成されます。

6. 次のコード行をコメント アウトします。

		var itemCount = 0;
		var staticItems = [];

	データは、メモリ内の配列ではなく、モバイル サービスに格納されます。

6. 次のコード行をコメント解除します。

        todoItemTable = client.getTable('todoitem');

   	このコードでは、SQL データベース **TodoItem** のプロキシ オブジェクト (**todoItemTable**) を作成します。

7. **$('\#add-item').submit** イベント ハンドラーを次のコードで置き換えます。

		$('#add-item').submit(function(evt) {
			var textbox = $('#new-item-text'),
				itemText = textbox.val();
			if (itemText !== '') {
				todoItemTable.insert({ text: itemText, complete: false })
					.then(refreshTodoItems);
			}
			textbox.val('').focus();
			evt.preventDefault();
		});


  	このコードでは、新しい項目をテーブルに挿入します。

8. **refreshTodoItems** メソッドを次のコードで置き換えます。

		function refreshTodoItems() {

			var query = todoItemTable;

			query.read().then(function(todoItems) {
				listItems = $.map(todoItems, function(item) {
					return $('<li>')
						.attr('data-todoitem-id', item.id)
						.append($('<button class="item-delete">Delete</button>'))
						.append($('<input type="checkbox" class="item-complete">').prop('checked', item.complete))
						.append($('<div>').append($('<input class="item-text">').val(item.text)));
				});
					   
				$('#todo-items').empty().append(listItems).toggle(listItems.length > 0);
				$('#summary').html('<strong>' + todoItems.length + '</strong> item(s)');
			});
		}
	   

   これにより、すべての項目を返すクエリがモバイル サービスに送信されます。結果は繰り返し処理され、データがページに表示されます。

9. **$(document.body).on('change', '.item-text')** イベント ハンドラーと **$(document.body).on('change', '.item-complete')** イベント ハンドラーを次のコードで置き換えます。
        
		$(document.body).on('change', '.item-text', function() {
			var newText = $(this).val();
			todoItemTable.update({ id: getTodoItemId(this), text: newText });
		});

		$(document.body).on('change', '.item-complete', function() {
			var isComplete = $(this).prop('checked');
			todoItemTable.update({ id: getTodoItemId(this), complete: isComplete })
				.then(refreshTodoItems);
		});
 
   	これにより、テキストを変更したとき、またはチェック ボックスをオンにしたときに、項目の更新がモバイル サービスに送信されます。

10. **$(document.body).on('click', '.item-delete')** イベント ハンドラーを次のコードで置き換えます。

		$(document.body).on('click', '.item-delete', function () {
			todoItemTable.del({ id: getTodoItemId(this) }).then(refreshTodoItems);
		});

	これにより、**[Delete]** ボタンがクリックされたときに削除がモバイル サービスに送信されます。

バックエンド ストレージのモバイル サービスを使用するようにアプリケーションを更新した後は、モバイル サービスに対してアプリケーションをテストします。

##<a name="test-app"></a>新しいモバイル サービスに対するアプリケーションのテスト

4. Web ブラウザーで URL <a href="http://localhost:8000/" target="_blank">http://localhost:8000/</a> を再読み込みしてアプリケーションを開始します。

    > [AZURE.NOTE]Web サーバーを再起動する必要がある場合は、最初のセクションの手順を繰り返してください。

2. 前回と同様、**[Enter new task]** にテキストを入力し、**[追加]** をクリックします。

   	これにより、新しい項目が挿入としてモバイル サービスに送信されます。

3. [管理ポータル]で、**[モバイル サービス]** をクリックし、対象のモバイル サービスをクリックします。

4. **[データ]** タブをクリックし、**[参照]** をクリックします。
  
   	**TodoItem** テーブルに、モバイル サービスによって生成された ID 値を持つデータが含まれ、アプリケーションの TodoItem クラスに対応してその列が自動的にテーブルに追加されていることに注目してください。

5. アプリケーションで、リストの項目の 1 つをチェックします。ポータルの **[参照]** タブに戻り、[最新の情報に更新] をクリックします。

  	"complete" 値が **false** から **true** に変更されます。

6. app.js プロジェクト ファイルで、**RefreshTodoItems** メソッドを見つけ、`query` を定義しているコード行を次のコード行で置き換えます。

   		var query = todoItemTable.where({ complete: false });

7. ページをもう一度読み込み、リスト内の別の項目の 1 つをチェックします。

   	リスト内のチェックされた項目が非表示になります。更新のたびにモバイル サービスへの往復が発生し、その結果、フィルター処理されたデータが返されます。

これで、**データの使用**に関するチュートリアルはおしまいです。

## <a name="next-steps"> </a>次のステップ

このチュートリアルでは、HTML アプリケーションでモバイル サービスのデータを操作できるようにするための基本について説明しました。次に、アプリのユーザーを認証する方法について学習してください。「[アプリへの認証の追加]」を完了して、他のチュートリアルの 1 つを試してください。Cordova または PhoneGap アプリ プロジェクトを使用している場合、プッシュ通知について詳しくは、「[Push Notifications to Cordova Apps with Microsoft Azure (Microsoft Azure での Cordova アプリへのプッシュ通知)](https://msdn.microsoft.com/magazine/dn879353.aspx)」を参照してください。

<!-- Anchors. -->
[Download the HTML app project]: #download-app
[Create the mobile service]: #create-service
[Add a data table for storage]: #add-table
[Update the app to use Mobile Services]: #update-app
[Test the app against Mobile Services]: #test-app
[Next Steps]: #next-steps

<!-- Images. -->
[0]: ./media/mobile-services-html-get-started-data/mobile-quickstart-startup-html.png

<!-- URLs. -->
[モバイル サービスの使用]: mobile-services-html-get-started.md
[アプリへの認証の追加]: mobile-services-html-get-started-users.md

[Azure Management Portal]: https://manage.windowsazure.com/
[管理ポータル]: https://manage.windowsazure.com/
[GetStartedWithData app]: http://go.microsoft.com/fwlink/?LinkID=286345
[GetStartedWithData アプリケーション]: http://go.microsoft.com/fwlink/?LinkID=286345

[Mobile Services HTML/JavaScript How-to Conceptual Reference]: mobile-services-html-how-to-use-client-library.md

[Cross-origin resource sharing]: http://msdn.microsoft.com/library/azure/dn155871.aspx

 

<!---HONumber=August15_HO8-->