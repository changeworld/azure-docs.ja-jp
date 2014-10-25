<properties linkid="develop-mobile-tutorials-validate-modify-and-augment-data-dotnet" urlDisplayName="Validate and Modify Data" pageTitle="User server scripts to validate and modify data (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to use server scripts to validate, modify, and augment data for your Windows Store app with Azure Mobile Services." metaCanonical="" services="" documentationCenter="Mobile" title="Validate and modify data in Mobile Services by using server scripts" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="glenga"></tags>

# サーバー スクリプトを使用したモバイル サービスのデータの検証および変更

<div class="dev-center-tutorial-selector sublanding"><a href="/ja-jp/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet" title="Windows ストア C#" class="current">Windows ストア C#</a><a href="/ja-jp/develop/mobile/tutorials/validate-modify-and-augment-data-js" title="Windows ストア JavaScript">Windows ストア JavaScript</a><a href="/ja-jp/develop/mobile/tutorials/validate-modify-and-augment-data-wp8" title="Windows Phone">Windows Phone</a><a href="/ja-jp/develop/mobile/tutorials/validate-modify-and-augment-data-ios" title="iOS">iOS</a><a href="/ja-jp/develop/mobile/tutorials/validate-modify-and-augment-data-android" title="Android">Android</a><a href="/ja-jp/develop/mobile/tutorials/validate-modify-and-augment-data-html" title="HTML">HTML</a><a href="/ja-jp/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-ios" title="Xamarin.iOS">Xamarin.iOS</a><a href="/ja-jp/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-android" title="Xamarin.Android">Xamarin.Android</a></div>
<div class="dev-center-tutorial-subselector">
<a href="/ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-validate-modify-data/" title=".NET バックエンド">.NET バックエンド</a> | 
<a href="/ja-jp/documentation/articles/mobile-services-windows-store-dotnet-validate-modify-data-server-scripts/"  title="JavaScript バックエンド" class="current">JavaScript バックエンド</a>
</div>
<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>このトピックでは、Azure のモバイル サービスでサーバー スクリプトを活用する方法について説明します。サーバー スクリプトは、モバイル サービスに登録され、挿入や更新が行われるデータでの広範な操作 (検証やデータの修正を含む) の実行に使用できます。このチュートリアルでは、検証およびデータの修正を行うサーバー スクリプトを定義して登録します。多くの場合、サーバー側スクリプトの動作がクライアントに影響を与えるため、これらの新しい動作の利点を活用できるように Windows ストア アプリケーションも更新します。</p>
</div>
<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Windows-Store-app-Validate-and-Modify-Data-with-Server-Scripts-in-Windows-Azure-Mobile-Services" target="_blank" class="label">チュートリアルを見る</a> <a style="background-image: url('/media/devcenter/mobile/videos/validate-data-windows-store-180x120.png') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Windows-Store-app-Validate-and-Modify-Data-with-Server-Scripts-in-Windows-Azure-Mobile-Services" target="_blank" class="dev-onpage-video"><span class="icon">ビデオを再生する</span></a> <span class="time">9:54:00</span></div>
</div>


このチュートリアルでは、次の基本的な手順について説明します。

1.  [文字列の長さの検証の追加][文字列の長さの検証の追加]
2.  [検証をサポートするためのクライアントの更新][検証をサポートするためのクライアントの更新]
3.  [挿入時のタイムスタンプの追加][挿入時のタイムスタンプの追加]
4.  [タイムスタンプを表示するためのクライアントの更新][タイムスタンプを表示するためのクライアントの更新]

このチュートリアルは、前の[データの使用][データの使用]に関するチュートリアルの手順およびサンプル アプリケーションを基に作成されています。このチュートリアルを開始する前に、[データの使用][データの使用]に関するチュートリアルを完了している必要があります。

## <a name="string-length-validation"></a>検証の追加

ユーザーにより送信されたデータの長さを検証することをお勧めします。最初に、モバイル サービスに送信された文字列データの長さを検証するスクリプトを登録し、長すぎる文字列 (この場合は 10 文字を超える) を拒否します。

1.  [Azure の管理ポータル][Azure の管理ポータル]にログインし、**[モバイル サービス]** をクリックして、アプリケーションをクリックします。

    ![][]

2.  **[データ]** タブをクリックし、**TodoItem** テーブルをクリックします。

    ![][1]

3.  **[スクリプト]** をクリックし、**[挿入]** 操作を選択します。

    ![][2]

4.  既存のスクリプトを次の関数に置き換え、**[保存]** をクリックします。

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
<br/>
<br/>
    登録したスクリプトを <b>[スクリプト]</b> タブで削除できます。<b>[クリア]</b> をクリックし、<b>[保存]</b> をクリックします。

    </div>

## <a name="update-client-validation"></a>クライアントの更新

モバイル サービスはデータを検証してエラー応答を送信するため、検証からのエラー応答を処理できるようにアプリケーションを更新する必要があります。

1.  Visual Studio 2012 Express for Windows 8 で、[データの使用][データの使用]に関するチュートリアルを実行したときに変更したプロジェクトを開きます。

2.  **F5** キーを押してアプリケーションを実行し、**[Insert a TodoItem]** ボックスに 10 文字を超える長さのテキストを入力して、**[Save]** をクリックします。

    モバイル サービスから返された応答 400 (正しくない要求) の結果として、処理されない **MobileServiceInvalidOperationException** が発生します。

3.  MainPage.xaml.cs ファイルを開き、次の **using** ステートメントを追加します。

        using Windows.UI.Popups;

4.  既存の **InsertTodoItem** メソッドを次のコードに置き換えます。

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
                MessageDialog errormsg = new MessageDialog(e.Message, 
                    string.Format("{0} (HTTP {1})",                     
                    e.Response.ReasonPhrase,
                    (int)e.Response.StatusCode));
                var ignoreAsyncOpResult = errormsg.ShowAsync();
            }
        }

    このバージョンのメソッドには、エラー応答をポップアップに表示する、**MobileServiceInvalidOperationException** に対するエラー処理が含まれています。

## <a name="add-timestamp"></a>タイムスタンプの追加

前のタスクでは、挿入を検証して、受け入れるか拒否しました。ここでは、オブジェクトへの挿入前にタイムスタンプ プロパティをそのオブジェクトに追加するサーバー スクリプトを使用して、挿入されたデータを更新します。

<div class="dev-callout">

    <b>注</b>
<br/>
<br/>
    ここで紹介する <b>createdAt</b> タイムスタンプ プロパティは、現在は冗長になっています。モバイル サービスによって、各テーブルに対応する <b>__createdAt</b> システム プロパティが自動的に作成されます。このシステム プロパティをアプリケーションで利用するには、単純に次のメンバーを TodoItem クラスに追加します。
<pre><code>
[JsonProperty(PropertyName = "__createdAt")]
public DateTime createdAt { set; get; }
</code></pre>

</div>

1.  [管理ポータル][Azure の管理ポータル]の **[スクリプト]** タブで、現在の **[挿入]** スクリプトを次の関数で置き換え、**[保存]** をクリックします。

        function insert(item, user, request) {
            if (item.text.length > 10) {
                request.respond(statusCodes.BAD_REQUEST, 'Text length must be under 10');
            } else {
                item.createdAt = new Date();
                request.execute();
            }
        }

    この関数は、**request**.**execute** の呼び出しで、オブジェクトへの挿入前に、新しい **createdAt** タイムスタンプ プロパティをそのオブジェクトに追加することにより、前の挿入スクリプトを強化しています。

    <div class="dev-callout">

    <b>注</b>
<br/>
<br/>
    挿入スクリプトを初めて実行するときには、動的スキーマを必ず有効にしてください。動的スキーマが有効になっていると、挿入スクリプトを最初に実行した時点でモバイル サービスによって <b>TodoItem</b> テーブルに <b>createdAt</b> 列が自動で追加されます。動的スキーマは、新しいモバイル サービスでは既定で有効になっているため、アプリケーションを Windows ストアに発行する前に無効にする必要があります。

    </div>

2.  Visual Studio で、**F5** キーを押してアプリケーションを実行し、**[Insert a TodoItem]** に 10 文字未満のテキストを入力して、**[Save]** をクリックします。

    新しいタイムスタンプがアプリケーション UI に表示されないことを確認します。

3.  管理ポータルに戻り、**todoitem** テーブルの **[参照]** タブをクリックします。

    **[createdAt]** 列が存在し、新しく挿入された項目にタイムスタンプ値が保持されていることを確認します。

次に、Windows ストア アプリを更新してこの新しい列を表示する必要があります。

## <a name="update-client-timestamp"></a>クライアントの再更新

モバイル サービス クライアントは、定義された型のプロパティにシリアル化できない応答内のデータを無視します。最後の手順は、クライアントを更新してこの新しいデータを表示することです。

1.  Visual Studio で、MainPage.xaml.cs ファイルを開き、既存の **TodoItem** クラスを次の定義に置き換えます。

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

    新しいクラス定義には、null 値を許容する DateTime 型として新しいタイムスタンプ プロパティが含まれます。

    <div class="dev-callout">

    <b>注</b>
<p><code data-inline="1">DataMemberAttribute</code> は、アプリケーション内の新しい <code data-inline="1">CreatedAt</code> プロパティを、TodoItem テーブルで定義された (大文字と小文字が異なる) <code data-inline="1">createdAt</code> 列にマップするように、クライアントに指示します。この属性を使用することにより、アプリケーションでは、SQL データベース内の列名と異なるプロパティ名をオブジェクトに対して使用することができます。この属性がなければ、大文字と小文字が異なるため、エラーが発生します。</p>


    </div>

2.  MainPage.xaml ファイル内の **CheckBoxComplete** 要素のすぐ下に、次の XAML 要素を追加します。

        <TextBlock Name="WhenCreated" Text="{Binding CreatedAt}" VerticalAlignment="Center"/>

    これにより、新しい **CreatedAt** プロパティがテキスト ボックスに表示されます。

3.  **F5** キーを押してアプリケーションを実行します。

    挿入スクリプトを更新した後で、タイムスタンプが挿入された項目にのみ表示されることに注目してください。

4.  既存の **RefreshTodoItems** メソッドを次のコードに置き換えます。

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

5.  **F5** キーを押してアプリケーションを実行します。

    タイムスタンプ値なしで作成されたすべての項目が UI から消去されていることに注目してください。

これで、データの使用に関するチュートリアルは終了です。

## <a name="next-steps"> </a>次のステップ

このチュートリアルが完了したため、データ シリーズの最終チュートリアルに進むことを検討してください。[ページングを使用したクエリの改善][ページングを使用したクエリの改善]。

サーバー スクリプトは、ユーザーを認証するときに、およびプッシュ通知の送信のためにも使用されます。詳細については、次のチュートリアルを参照してください。

-   [スクリプトを使用したユーザーの認証][スクリプトを使用したユーザーの認証]

    認証されたユーザーの ID に基づきデータをフィルター処理する方法について説明します。

-   [プッシュ通知の使用][プッシュ通知の使用]

    アプリケーションにごく基本的なプッシュ通知を送信する方法について説明します。

-   [モバイル サービスのサーバー スクリプト リファレンス][モバイル サービスのサーバー スクリプト リファレンス]

    サーバー スクリプトの登録および使用について説明します。

-   [モバイル サービス .NET の使用方法の概念リファレンス][モバイル サービス .NET の使用方法の概念リファレンス]

    .NET で Mobile Services を使用する方法について説明します。



  [Windows ストア C#]: /ja-jp/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet "Windows ストア C#"
  [Windows ストア JavaScript]: /ja-jp/develop/mobile/tutorials/validate-modify-and-augment-data-js "Windows ストア JavaScript"
  [Windows Phone]: /ja-jp/develop/mobile/tutorials/validate-modify-and-augment-data-wp8 "Windows Phone"
  [iOS]: /ja-jp/develop/mobile/tutorials/validate-modify-and-augment-data-ios "iOS"
  [Android]: /ja-jp/develop/mobile/tutorials/validate-modify-and-augment-data-android "Android"
  [HTML]: /ja-jp/develop/mobile/tutorials/validate-modify-and-augment-data-html "HTML"
  [Xamarin.iOS]: /ja-jp/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-ios "Xamarin.iOS"
  [Xamarin.Android]: /ja-jp/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-android "Xamarin.Android"
  [.NET バックエンド]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-validate-modify-data/ ".NET バックエンド"
  [JavaScript バックエンド]: /ja-jp/documentation/articles/mobile-services-windows-store-dotnet-validate-modify-data-server-scripts/ "JavaScript バックエンド"
  [チュートリアルを見る]: http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Windows-Store-app-Validate-and-Modify-Data-with-Server-Scripts-in-Windows-Azure-Mobile-Services
  [文字列の長さの検証の追加]: #string-length-validation
  [検証をサポートするためのクライアントの更新]: #update-client-validation
  [挿入時のタイムスタンプの追加]: #add-timestamp
  [タイムスタンプを表示するためのクライアントの更新]: #update-client-timestamp
  [データの使用]: /ja-jp/develop/mobile/tutorials/get-started-with-data-dotnet
  [Azure の管理ポータル]: https://manage.windowsazure.com/
  []: ./media/mobile-services-windows-store-dotnet-validate-modify-data-server-scripts/mobile-services-selection.png
  [1]: ./media/mobile-services-windows-store-dotnet-validate-modify-data-server-scripts/mobile-portal-data-tables.png
  [2]: ./media/mobile-services-windows-store-dotnet-validate-modify-data-server-scripts/mobile-insert-script-users.png
  [ページングを使用したクエリの改善]: /ja-jp/develop/mobile/tutorials/add-paging-to-data-dotnet
  [スクリプトを使用したユーザーの認証]: /ja-jp/develop/mobile/tutorials/authorize-users-in-scripts-dotnet
  [プッシュ通知の使用]: /ja-jp/develop/mobile/tutorials/get-started-with-push-dotnet
  [モバイル サービスのサーバー スクリプト リファレンス]: http://go.microsoft.com/fwlink/?LinkId=262293
  [モバイル サービス .NET の使用方法の概念リファレンス]: /ja-jp/develop/mobile/how-to-guides/work-with-net-client-library
