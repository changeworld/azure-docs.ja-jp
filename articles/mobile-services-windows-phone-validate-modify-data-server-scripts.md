<properties linkid="develop-mobile-tutorials-validate-modify-and-augment-data-wp8" urlDisplayName="データの検証" pageTitle="サーバー スクリプトを使用したデータの検証 (Windows Phone) | モバイル デベロッパー センター" metaKeywords="" description="Windows Phone 8 アプリケーションからサーバー スクリプトを使用して送信されたデータを検証および変更する方法について説明します。" metaCanonical="" services="" documentationCenter="Mobile" title="サーバー スクリプトを使用したモバイル サービスのデータの検証および変更" authors="glenga" solutions="" manager="" editor="" />




# サーバー スクリプトを使用したモバイル サービスのデータの検証および変更
<div class="dev-center-tutorial-selector sublanding"> 
<a href="/ja-jp/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet" title="Windows ストア C#">Windows ストア C#</a>
<a href="/ja-jp/develop/mobile/tutorials/validate-modify-and-augment-data-js" title="Windows ストア JavaScript">Windows ストア JavaScript</a>
<a href="/ja-jp/develop/mobile/tutorials/validate-modify-and-augment-data-wp8" title="Windows Phone" class="current">Windows Phone</a>
<a href="/ja-jp/develop/mobile/tutorials/validate-modify-and-augment-data-ios" title="iOS">iOS</a>
<a href="/ja-jp/develop/mobile/tutorials/validate-modify-and-augment-data-android" title="Android">Android</a>
<a href="/ja-jp/develop/mobile/tutorials/validate-modify-and-augment-data-html" title="HTML">HTML</a>
<a href="/ja-jp/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-ios" title="Xamarin.iOS">Xamarin.iOS</a>
<a href="/ja-jp/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-android" title="Xamarin.Android">Xamarin.Android</a>
</div>

<div class="dev-center-tutorial-subselector">
	<a href="/ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-phone-validate-modify-data/" title=".NET バックエンド">.NET バックエンド</a> |
	<a href="/ja-jp/develop/mobile/tutorials/validate-modify-and-augment-data-wp8"  title="JavaScript バックエンド" class="current">JavaScript バックエンド</a>
</div>


<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">

<p>このトピックでは、Azure のモバイル サービスでサーバー スクリプトを活用する方法について説明します。サーバー スクリプトは、モバイル サービスに登録され、挿入や更新が行われるデータでの広範な操作 (検証やデータの修正を含む) の実行に使用できます。このチュートリアルでは、検証およびデータの修正を行うサーバー スクリプトを定義して登録します。多くの場合、サーバー側スクリプトの動作がクライアントに影響を与えるため、これらの新しい動作の利点を活用できるように Windows Phone 8 アプリも更新します。</p>
</div>
<div class="dev-onpage-video-wrapper"><a href="http://go.microsoft.com/fwlink/?LinkId=298629" target="_blank" class="label">チュートリアルを見る</a> <a style="background-image: url('/media/devcenter/mobile/videos/mobile-wp8-validate-modify-data-180x120.png') !important;" href="http://go.microsoft.com/fwlink/?LinkId=298629" target="_blank" class="dev-onpage-video"><span class="icon">ビデオを再生する</span></a> <span class="time">11:36</span></div>
</div>

このチュートリアルでは、次の基本的な手順について説明します。

1. [文字列の長さの検証の追加]
2. [検証をサポートするためのクライアントの更新]
3. [挿入時のタイムスタンプの追加]
4. [タイムスタンプを表示するためのクライアントの更新]

このチュートリアルは、前の[データの使用]に関するチュートリアルの手順およびサンプル アプリケーションを基に作成されています。このチュートリアルを開始する前に、[データの使用]に関するチュートリアルを完了している必要があります。

## <a name="string-length-validation"></a>検証の追加

ユーザーにより送信されたデータの長さを検証することをお勧めします。最初に、モバイル サービスに送信された文字列データの長さを検証するスクリプトを登録し、長すぎる文字列 (この場合は 10 文字を超える) を拒否します。

1. [Azure 管理ポータル]にログインし、**[モバイル サービス]** をクリックして、アプリケーションをクリックします。

	![][0]

2. **[データ]** タブをクリックし、**TodoItem** テーブルをクリックします。

	![][1]

3. **[スクリプト]** をクリックし、**[挿入]** 操作を選択します。

	![][2]

4. 既存のスクリプトを次の関数に置き換え、**[保存]** をクリックします。

        function insert(item, user, request) {
            if (item.text.length > 10) {
                request.respond(statusCodes.BAD_REQUEST, 'Text length must be under 10');
            } else {
                request.execute();
            }
        }

    このスクリプトは、**TodoItem.text** プロパティの長さをチェックし、長さが 10 文字を超えた場合にエラー応答を送信します。それ以外の場合、**execute** メソッドが呼び出されて挿入を完了します。

    <div class="dev-callout"> 
	<b>注</b>
	<p>登録したスクリプトを <strong>[スクリプト]</strong> タブで削除できます。<strong>[クリア]</strong> をクリックし、<strong>[保存]</strong> をクリックします。</p></div>	

## <a name="update-client-validation"></a>クライアントの更新

モバイル サービスはデータを検証してエラー応答を送信するため、検証からのエラー応答を処理できるようにアプリケーションを更新する必要があります。

1. Visual Studio 2012 Express for Windows Phone で、チュートリアル「「[データの使用]」を実行したときに変更したプロジェクトを開きます。

2. **F5** キーを押してアプリケーションを実行し、テキスト ボックスに 10 文字を超える長さのテキストを入力して、**[Save]** をクリックします。

   	モバイル サービスから返された応答 400 (正しくない要求) の結果として、処理されない **MobileServiceInvalidOperationException** が発生します。

6. 	MainPage.xaml.cs ファイルを開き、既存の **InsertTodoItem** メソッドを次のコードに置き換えます。

        private async void InsertTodoItem(TodoItem todoItem)
        {
            // This code inserts a new TodoItem into the database. 
			// When the operation completes and Mobile Services has 
			// assigned an Id, the item is added to the collection.
            try
            {
                await todoTable.InsertAsync(todoItem);
                items.Add(todoItem);
            }
            catch (MobileServiceInvalidOperationException e)
            {
                MessageBox.Show(e.Message,
                    string.Format("{0} (HTTP {1})",
                    e.Response.ReasonPhrase,
                    (int)e.Response.StatusCode), 
					MessageBoxButton.OK);
            }
        }

   	このバージョンのメソッドには、エラー応答を MessageBox に表示する、**MobileServiceInvalidOperationException** に対するエラー処理が含まれています。

## <a name="add-timestamp"></a>タイムスタンプの追加

前のタスクでは、挿入を検証して、受け入れるか拒否しました。ここでは、オブジェクトへの挿入前にタイムスタンプ プロパティをそのオブジェクトに追加するサーバー スクリプトを使用して、挿入されたデータを更新します。

<div class="dev-callout"><b>注</b>
<p>ここで紹介する <b>createdAt</b> タイムスタンプ プロパティは、現在は冗長になっています。モバイル サービスによって、各テーブルに対応する <b>__createdAt</b> システム プロパティが自動的に作成されます。このシステム プロパティをアプリケーションで利用するには、単純に次のメンバーを TodoItem クラスに追加します。</p>
<pre><code>
[JsonProperty(PropertyName = "__createdAt")]
public DateTime createdAt { set; get; }
</code></pre>
</div>



1. [管理ポータル]の **[スクリプト]** タブで、現在の **[挿入]** スクリプトを次の関数で置き換え、**[保存]** をクリックします。

        function insert(item, user, request) {
            if (item.text.length > 10) {
                request.respond(statusCodes.BAD_REQUEST, 'Text length must be under 10');
            } else {
                item.createdAt = new Date();
                request.execute();
            }
        }

    この関数は、**request**.**execute** の呼び出しで、オブジェクトへの挿入前に、新しい **createdAt** タイムスタンプ プロパティをそのオブジェクトに追加することにより、前の挿入スクリプトを強化しています。

    <div class="dev-callout"><b>注</b>
	<p>挿入スクリプトを初めて実行するときには、動的スキーマを必ず有効にしてください。動的スキーマが有効になっていると、挿入スクリプトを最初に実行した時点でモバイル サービスによって <strong>TodoItem</strong> テーブルに <strong>createdAt</strong> 列が自動で追加されます。動的スキーマは、新しいモバイル サービスでは既定で有効になっているため、アプリケーションを Windows Phone ストアに発行する前に無効にする必要があります。</p>
    </div>

2. Visual Studio で、**F5** キーを押してアプリケーションを実行し、テキスト ボックスに 10 文字未満のテキストを入力して、**[Save]** をクリックします。

   	新しいタイムスタンプがアプリケーション UI に表示されないことを確認します。

3. 管理ポータルに戻り、**todoitem** テーブルの **[参照]** タブをクリックします。
   
   	**[createdAt]** 列が存在し、新しく挿入された項目にタイムスタンプ値が保持されていることを確認します。
  
次に、Windows Phone アプリケーションを更新してこの新しい列を表示する必要があります。

## <a name="update-client-timestamp"></a>クライアントの再更新

モバイル サービス クライアントは、定義された型のプロパティにシリアル化できない応答内のデータを無視します。最後の手順は、クライアントを更新してこの新しいデータを表示することです。

1. Visual Studio で、MainPage.xaml.cs ファイルを開き、既存の **TodoItem** クラスを次の定義に置き換えます。

        public class TodoItem
        {
            public string Id { get; set; }

            [JsonProperty(PropertyName = "text")]
            public string Text { get; set; }

            [JsonProperty(PropertyName = "complete")]
            public bool Complete { get; set; }
        
            [JsonProperty(PropertyName = "createdAt")]
            public DateTime? CreatedAt { get; set; }
        }
	
    This new class definition includes the new timestamp property, as a nullable DateTime type.
  
    <div class="dev-callout"><b>注</b>
	<p><strong>DataMemberAttribute</strong> は、アプリケーション内の新しい <strong>CreatedAt</strong> プロパティを、TodoItem テーブルに定義された (大文字と小文字が異なる) <strong>createdAt</strong> 列にマップするように、クライアントに指示します。この属性を使用することにより、アプリケーションでは、SQL データベース内の列名と異なるプロパティ名をオブジェクトに対して使用することができます。この属性がなければ、大文字と小文字が異なるため、エラーが発生します。</p>
    </div>

5. MainPage.xaml ファイル内の **CheckBoxComplete** 要素のすぐ下に、次の XAML 要素を追加します。
	      
        <TextBlock Name="WhenCreated" Text="{Binding CreatedAt}" VerticalAlignment="Center"/>

   	これにより、新しい **CreatedAt** プロパティがテキスト ボックスに表示されます。
	
6. **F5** キーを押してアプリケーションを実行します。

   	挿入スクリプトを更新した後で、タイムスタンプが挿入された項目にのみ表示されることに注目してください。

7. 既存の **RefreshTodoItems** メソッドを次のコードに置き換えます。

        private async void RefreshTodoItems()
        {
            // This query filters out completed TodoItems and 
            // items without a timestamp. 
            items = await todoTable
               .Where(todoItem => todoItem.Complete == false
                   && todoItem.CreatedAt != null)
               .ToCollectionAsync();

            ListItems.ItemsSource = items;
        }

   	このメソッドは、クエリを更新し、タイムスタンプ値を保持しない項目をフィルターで除きます。
	
8. **F5** キーを押してアプリケーションを実行します。

   	タイムスタンプ値なしで作成されたすべての項目が UI から消去されていることに注目してください。

これで、データの使用に関するチュートリアルは終了です。

## <a name="next-steps"> </a>次のステップ

このチュートリアルが完了したため、データ シリーズの最終チュートリアルに進むことを検討してください。

サーバー スクリプトは、ユーザーを認証するときに、およびプッシュ通知の送信のためにも使用されます。詳細については、「[ページングを使用したモバイル サービス クエリの改善]」を参照してください。

* [スクリプトを使用したユーザーの承認]
  <br/>認証されたユーザーの ID に基づきデータをフィルター処理する方法について説明します。

* [プッシュ通知の使用] 
  <br/>アプリケーションにごく基本的なプッシュ通知を送信する方法について説明します。

* [モバイル サービスのサーバー スクリプト リファレンス]
  <br/>サーバー スクリプトの登録および使用について説明します。

<!-- Anchors. -->
[文字列の長さの検証の追加]: #string-length-validation
[検証をサポートするためのクライアントの更新]: #update-client-validation
[挿入時のタイムスタンプの追加]: #add-timestamp
[タイムスタンプを表示するためのクライアントの更新]: #update-client-timestamp
[次のステップ]: #next-steps

<!-- Images. -->
[0]: ./media/mobile-services-windows-phone-validate-modify-data-server-scripts/mobile-services-selection.png
[1]: ./media/mobile-services-windows-phone-validate-modify-data-server-scripts/mobile-portal-data-tables.png
[2]: ./media/mobile-services-windows-phone-validate-modify-data-server-scripts/mobile-insert-script-users.png


<!-- URLs. -->
[モバイル サービスのサーバー スクリプト リファレンス]: http://go.microsoft.com/fwlink/?LinkId=262293
[モバイル サービスの使用]: /ja-jp/develop/mobile/tutorials/get-started/#create-new-service
[スクリプトを使用したユーザーの認証]: /ja-jp/develop/mobile/tutorials/authorize-users-in-scripts-wp8
[ページングを使用したクエリの改善]: /ja-jp/develop/mobile/tutorials/add-paging-to-data-wp8
[データの使用]: /ja-jp/develop/mobile/tutorials/get-started-with-data-wp8
[認証の使用]: /ja-jp/develop/mobile/tutorials/get-started-with-users-wp8
[プッシュ通知の使用]: /ja-jp/develop/mobile/tutorials/get-started-with-push-wp8

[管理ポータル]: https://manage.windowsazure.com/
[Azure の管理ポータル]: https://manage.windowsazure.com/

