<properties linkid="develop-mobile-tutorials-get-started-with-data-html" urlDisplayName="Get Started with Data (HTML5)" pageTitle="Get started with data (HTML 5) | Mobile Dev Center" metaKeywords="" description="Learn how to get started using Mobile Services to leverage data in your HTML app." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with data in Mobile Services" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-html" ms.devlang="javascript" ms.topic="article" ms.date="09/24/2014" ms.author="glenga" />

# モバイル サービスでのデータの使用

[WACOM.INCLUDE [mobile-services-selector-get-started-data](../includes/mobile-services-selector-get-started-data.md)]

このトピックでは、Azure のモバイル サービスを使用して HTML アプリケーションのデータを活用する方法について説明します。このチュートリアルでは、メモリにデータを格納するアプリケーションをダウンロードした後、新しいモバイル サービスを作成してそれをアプリケーションに統合します。その後、Azure 管理ポータルにログインして、アプリケーションの実行中にデータに加えられた変更を表示します。

> [WACOM.NOTE] このチュートリアルの目的は、モバイル サービスが HTML アプリケーションのデータを Azure に格納および取得できるようにするしくみを説明することにあります。したがって、このトピックでは、モバイル サービスのクイック スタートで完了している手順の多くについても説明します。モバイル サービスを初めて使用する場合は、最初にチュートリアル「[モバイル サービスの使用][モバイル サービスの使用]」を完了することをお勧めします。

このチュートリアルでは、次の基本的な手順について説明します。

1.  [HTML アプリ プロジェクトのダウンロード][HTML アプリ プロジェクトのダウンロード]
2.  [モバイル サービスの作成][モバイル サービスの作成]
3.  [ストレージのデータ テーブルの追加][ストレージのデータ テーブルの追加]
4.  [モバイル サービスを使用するためのアプリケーションの更新][モバイル サービスを使用するためのアプリケーションの更新]
5.  [モバイル サービスに対するアプリケーションのテスト][モバイル サービスに対するアプリケーションのテスト]

<div class="dev-callout"><strong>注</strong> <p>このチュートリアルを完了するには、Azure アカウントが必要です。アカウントがない場合は、無料の試用アカウントを数分で作成することができます。詳細については、<a href="http://www.windowsazure.com/ja-jp/pricing/free-trial/?WT.mc_id=A756A2826&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fja-jp%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-with-data-html%2F" target="_blank">Azure の無料評価版サイト</a>を参照してください。</p></div>

### その他の要件

GetStartedWithData アプリケーションは、任意の Web サーバーでホストできます。ただし、便宜を図るために、アプリケーションを `http://localhost:8000` で実行できるようにするスクリプトが提供されています。

-   localhost を使用するには、ローカル コンピューター上で次のいずれかの Web サーバーが実行されている必要があります。

    -   **Windows**: IIS Express。IIS Express は、[Microsoft Web プラットフォーム インストーラー] によってインストールされます。
    -   **MacOS X**: Python。既にインストールされています。
    -   **Linux**: Python。[最新バージョンの Python] をインストールする必要があります。

    このアプリケーションは任意の Web サーバーを使用してホストすることができますが、これらの Web サーバーは、ダウンロードしたスクリプトでサポートされています。

-   HTML5 をサポートする Web ブラウザー。

## <a name="download-app"></a><span class="short-header">プロジェクトのダウンロード</span>GetStartedWithData プロジェクトのダウンロード

このチュートリアルは、HTML5 アプリケーションの [GetStartedWithData アプリケーション][GetStartedWithData アプリケーション]に基づいています。このアプリケーションの UI は、追加された項目がメモリにローカルに格納される点を除き、モバイル サービスのクイック スタートで生成したアプリケーションと同じです。

1.  [HTML アプリケーション プロジェクト ファイルをダウンロードします][GetStartedWithData アプリケーション]。

2.  HTML エディターで、ダウンロードしたプロジェクトを開き、app.js ファイルを確認します。

    追加された項目はメモリ内の **Array** オブジェクト (**staticItems**) に格納される点に注目してください。ページを更新すると、データは消えます。データは保持されません。

3.  **server** サブフォルダーから次のいずれかのコマンド ファイルを実行します。

    -   **launch-windows** (Windows コンピューター)
    -   **launch-mac.command** (Mac OS X コンピューター)
    -   **launch-linux.sh** (Linux コンピューター)

    <div class="dev-callout"><b>注</b>
<p>Windows コンピューターでは、PowerShell からスクリプトの実行の確認を求められた場合は、「R」と入力します。Web ブラウザーでは、インターネットからダウンロードしたスクリプトであるため、実行しないよう警告されることがあります。その場合は、ブラウザーがスクリプトの読み込みを開始するよう要求する必要があります。</p>
</div>

    これにより、新しいアプリケーションをホストする Web サーバーがローカル コンピューター上で起動します。

4.  Web ブラウザーで URL <http://localhost:8000/> を開いて、アプリケーションを開始します。

5.  アプリケーションで、意味のあるテキスト (たとえば、*チュートリアルの完了*) を **[Enter new task]** に入力し、**[追加]** をクリックします。

    ![][0]

    保存したテキストが **staticItems** 配列に追加され、ページが更新されて新しい項目が表示されます。

## <a name="create-service"></a><span class="short-header">モバイル サービスの作成</span>管理ポータルでの新しいモバイル サービスの作成

[WACOM.INCLUDE [mobile-services-create-new-service-data](../includes/mobile-services-create-new-service-data.md)]

## <a name="add-table"></a><span class="short-header">新しいテーブルの追加</span>モバイル サービスへの新しいテーブルの追加

アプリケーション データを新しいモバイル サービスに格納できるようにするには、最初に新しいテーブルを関連付けられた SQL データベース インスタンスに作成する必要があります。

1.  管理ポータルで、**[モバイル サービス]** をクリックし、先ほど作成したモバイル サービスをクリックします。

2.  **[データ]** タブをクリックし、**[作成]** をクリックします。

    ![][1]

    **[新しいテーブルの作成]** ダイアログ ボックスが表示されます。

3.  **[テーブル名]** に「*TodoItem*」と入力し、チェック ボタンをクリックします。

    ![][2]

    これにより、既定のアクセス許可が設定された新しいストレージ テーブル **TodoItem** が作成されます。これは、アプリケーションと共に配布されるアプリケーション キーを使用してだれもがテーブル内のデータにアクセスし、変更を加えることができることを表します。

    <div class="dev-callout"> 
<b>注</b> 
<p>Mobile Services のクイック スタートでも同じテーブル名が使用されています。ただし、それぞれのテーブルは、特定のモバイル サービスに固有のスキーマ内で作成されています。これには、複数のモバイル サービスで同じデータベースを使用した場合にデータの競合を回避する目的があります。</p> 
</div>

4.  新しい **TodoItem** テーブルをクリックし、データ行がないことを確認します。

5.  **[列]** タブをクリックします。次の既定の列が自動的に作成されていることを確認します。
<table border="1" cellpadding="10">
<tr>
<th>列名</th>
<th>種類</th>
<th>インデックス</th>
</tr>
<tr>
<td>id</td>
<td>string</td>
<td>インデックス設定済み</td>
</tr>
<tr>
<td>__createdAt</td>
<td>date</td>
<td>インデックス設定済み</td>
</tr>
<tr>
<td>__updatedAt</td>
<td>date</td>
<td><font color="transparent">-</font></td>
</tr>
<tr>
<td>__version</td>
<td>timestamp (MSSQL)</td>
<td><font color="transparent">-</font></td>
</tr>   
</table>

    これは、モバイル サービスのテーブルの最小要件です。

    <div class="dev-callout"><b>注</b>
<p>モバイル サービスで動的スキーマを有効にすると、挿入操作または更新操作によって JSON オブジェクトがモバイル サービスに送信されるときに新しい列が自動的に作成されます。</p>
</div>

6.  **[構成]** タブで、**[クロス オリジン リソース共有 (CORS)]** の **[ホスト名からの要求を許可する]** に `localhost` が既に表示されていることを確認します。表示されていない場合は、**[ホスト名]** フィールドに「`localhost`」と入力して、**[保存]** をクリックします。

    ![][3]

    <div class="dev-callout"><b>注</b>
<p>localhost 以外の Web サーバーにクイック スタート アプリケーションを展開する場合は、<strong>[ホスト名からの要求を許可する]</strong> の一覧に Web サーバーのホスト名を追加する必要があります。詳細については、「<a href="http://msdn.microsoft.com/ja-jp/library/windowsazure/dn155871.aspx" target="_blank">クロス オリジン リソース共有</a>」を参照してください。</p>
</div>

これで、新しいモバイル サービスをアプリケーションのデータ ストレージとして使用する準備が整いました。

## <a name="update-app"></a><span class="short-header">アプリケーションの更新</span>モバイル サービスをデータ アクセスに使用するためのアプリケーションの更新

モバイル サービスの準備が整ったら、ローカル コレクションの代わりにモバイル サービスに項目を格納するようにアプリケーションを更新します。

1.  管理ポータルで、**[モバイル サービス]** をクリックし、先ほど作成したモバイル サービスをクリックします。

2.  **[ダッシュボード]** タブをクリックし、**サイトの URL** をメモに記録します。次に、**[キーの管理]** をクリックし、**アプリケーション キー**をメモに記録します。

    ![][4]

    これらの値は、アプリケーション コードからモバイル サービスにアクセスするときに必要になります。

3.  Web エディターで、index.html プロジェクト ファイルを開き、次のコードをページのスクリプト参照に追加します。

        <script src='http://ajax.aspnetcdn.com/ajax/mobileservices/MobileServices.Web-1.1.2.min.js'></script>

4.  エディターで、app.js ファイルを開き、**MobileServiceClient** 変数を定義している次のコードをコメント解除します。さらに、モバイル サービスの URL とアプリケーション キーを **MobileServiceClient** コンストラクターに順に指定します。

        var MobileServiceClient = WindowsAzure.MobileServiceClient,
            client = new MobileServiceClient('AppUrl', 'AppKey'),               

    これで、モバイル サービスにアクセスするために使用される MobileServiceClient の新しいインスタンスが作成されます。

5.  次のコード行をコメント アウトします。

        var itemCount = 0;
        var staticItems = [];

    データは、メモリ内の配列ではなく、モバイル サービスに格納されます。

6.  次のコード行をコメント解除します。

        todoItemTable = client.getTable('todoitem');

    このコードでは、SQL データベース **TodoItem** のプロキシ オブジェクト (**todoItemTable**) を作成します。

7.  **$('\#add-item').submit** イベント ハンドラーを次のコードで置き換えます。

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

8.  **refreshTodoItems** メソッドを次のコードで置き換えます。

    	function refreshTodoItems() {
    	var query = todoItemTable;
    	query.read().then(function(todoItems) {
        	listItems = $.map(todoItems, function(item) {
            	return $(&#39;&lt;li&gt;&#39;)
                	.attr(&#39;data-todoitem-id&#39;, item.id)
                	.append($(&#39;&lt;button class=&quot;item-delete&quot;&gt;Delete&lt;/button&gt;&#39;))
                	.append($(&#39;&lt;input type=&quot;checkbox&quot; class=&quot;item-complete&quot;&gt;&#39;).prop(&#39;checked&#39;, item.complete))
                	.append($(&#39;&lt;div&gt;&#39;).append($(&#39;&lt;input class=&quot;item-text&quot;&gt;&#39;).val(item.text)));
        	});
    		$(&#39;#todo-items&#39;).empty().append(listItems).toggle(listItems.length &gt; 0);
    		$(&#39;#summary&#39;).html(&#39;&lt;strong&gt;&#39; + todoItems.length + &#39;&lt;/strong&gt; item(s)&#39;);
    	});
		}

	これにより、すべての項目を返すクエリがモバイル サービスに送信されます。結果は繰り返し処理され、データがページに表示されます。

9.  <strong>$(document.body).on('change', '.item-text')</strong> イベント ハンドラーと <strong>$(document.body).on('change', '.item-complete')</strong> イベント ハンドラーを次のコードで置き換えます。\\
		
    	$(document.body).on(&#39;change&#39;, &#39;.item-text&#39;, function() {
    		var newText = $(this).val();
    		todoItemTable.update({ id: getTodoItemId(this), text: newText });
		});

		$(document.body).on(&#39;change&#39;, &#39;.item-complete&#39;, function() {
    		var isComplete = $(this).prop(&#39;checked&#39;);
    		todoItemTable.update({ id: getTodoItemId(this), complete: isComplete })
        		.then(refreshTodoItems);
		});
	これにより、テキストを変更したとき、またはチェック ボックスをオンにしたときに、項目の更新がモバイル サービスに送信されます。
10. <strong>$(document.body).on('click', '.item-delete')</strong> イベント ハンドラーを次のコードで置き換えます。

		$(document.body).on(&#39;click&#39;, &#39;.item-delete&#39;, function () {
    		todoItemTable.del({ id: getTodoItemId(this) }).then(refreshTodoItems);
		});
	これにより、<strong>[Delete]</strong> ボタンがクリックされたときに削除がモバイル サービスに送信されます。

<p>バックエンド ストレージのモバイル サービスを使用するようにアプリケーションを更新した後は、モバイル サービスに対してアプリケーションをテストします。</p>
<h2><a name="test-app"></a><span class="short-header">アプリケーションのテスト</span>新しいモバイル サービスに対するアプリケーションのテスト</h2>

1.  Web ブラウザーで URL <a href="http://localhost:8000/" target="_blank">http://localhost:8000/</a> を再読み込みしてアプリケーションを開始します。
<div class="dev-callout"><b>注</b>
<p>Web サーバーを再起動する必要がある場合は、最初のセクションの手順を繰り返してください。</p>
</div>

2.  前回と同様、**[Enter new task]** にテキストを入力し、**[追加]** をクリックします。

    これにより、新しい項目が挿入としてモバイル サービスに送信されます。

3.  [管理ポータル][管理ポータル]で、**[モバイル サービス]** をクリックし、対象のモバイル サービスをクリックします。

4.  **[データ]** タブをクリックし、**[参照]** をクリックします。

    ![][5]

    **TodoItem** テーブルに、モバイル サービスによって生成された ID 値を持つデータが含まれ、アプリケーションの TodoItem クラスに対応してその列が自動的にテーブルに追加されていることに注目してください。

5.  アプリケーションで、リストの項目の 1 つをチェックします。ポータルの **[参照]** タブに戻り、[最新の情報に更新] をクリックします。

    "complete" 値が **false** から **true** に変更されます。

6.  app.js プロジェクト ファイルで、**RefreshTodoItems** メソッドを見つけ、`query` を定義しているコード行を次のコード行で置き換えます。

        var query = todoItemTable.where({ complete: false });

7.  ページをもう一度読み込み、リスト内の別の項目の 1 つをチェックします。

    リスト内のチェックされた項目が非表示になります。更新のたびにモバイル サービスへの往復が発生し、その結果、フィルター処理されたデータが返されます。

これで、**データの使用**に関するチュートリアルはおしまいです。

## <a name="next-steps"> </a>次のステップ

このチュートリアルでは、HTML アプリケーションでモバイル サービスのデータを操作できるようにするための基本について説明しました。次は、このチュートリアルで作成した GetStartedWithData アプリケーションに基づく次のいずれかのチュートリアルを行うことをお勧めします。

-   [サーバー スクリプトを使用したモバイル サービスのデータの検証および変更][サーバー スクリプトを使用したモバイル サービスのデータの検証および変更]
    Mobile Services でサーバー スクリプトを使用して、アプリケーションから送信されたデータを検証および変更する方法について説明します。

-   [ページングを使用したモバイル サービス クエリの改善][ページングを使用したモバイル サービス クエリの改善]
    クエリ内でページングを使用して、1 回の要求で渡されるデータの量を制御する方法について説明します。

データ シリーズを完了した後は、アプリのユーザーを認証する方法について学習してください。[認証の使用][認証の使用]に関するチュートリアルを完了して、他のチュートリアルの 1 つを試してください。

 
 


  [モバイル サービスの使用]: /ja-jp/develop/mobile/tutorials/get-started-html
  [HTML アプリ プロジェクトのダウンロード]: #download-app
  [モバイル サービスの作成]: #create-service
  [ストレージのデータ テーブルの追加]: #add-table
  [モバイル サービスを使用するためのアプリケーションの更新]: #update-app
  [モバイル サービスに対するアプリケーションのテスト]: #test-app
  [GetStartedWithData アプリケーション]: http://go.microsoft.com/fwlink/?LinkID=286345
  [0]: ./media/mobile-services-html-get-started-data/mobile-quickstart-startup-html.png
  [1]: ./media/mobile-services-html-get-started-data/mobile-data-tab-empty.png
  [2]: ./media/mobile-services-html-get-started-data/mobile-create-todoitem-table.png
  [3]: ./media/mobile-services-html-get-started-data/mobile-services-set-cors-localhost.png
  [4]: ./media/mobile-services-html-get-started-data/mobile-dashboard-tab.png
  [管理ポータル]: https://manage.windowsazure.com/
  [5]: ./media/mobile-services-html-get-started-data/mobile-todoitem-data-browse.png
  [サーバー スクリプトを使用したモバイル サービスのデータの検証および変更]: /ja-jp/develop/mobile/tutorials/validate-modify-and-augment-data-html
  [ページングを使用したモバイル サービス クエリの改善]: /ja-jp/develop/mobile/tutorials/add-paging-to-data-html
  [認証の使用]: /ja-jp/develop/mobile/tutorials/get-started-with-users-html
