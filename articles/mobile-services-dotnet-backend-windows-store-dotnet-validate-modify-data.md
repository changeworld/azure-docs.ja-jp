<properties linkid="develop-mobile-tutorials-dotnet-backend-validate-modify-and-augment-data-dotnet" urlDisplayName="Validate and Modify Data" pageTitle="Use the .Net backend to validate and modify data (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to validate, modify, and augment data for your Windows Store app with .Net backend Windows Azure Mobile Services." metaCanonical="" services="" documentationCenter="Mobile" title="Validate and modify data in Mobile Services by using the .Net backend" authors="wesmc" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="09/23/2014" ms.author="wesmc"></tags>

# .NET バックエンドを使用したモバイル サービスのデータの検証および変更

<div class="dev-center-tutorial-selector sublanding">
<a href="/ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-validate-modify-data/" title="Windows ストア C#" class="current">Windows ストア C#</a>
<a href="/ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-validate-modify-data/" title="Windows ストア JavaScript">Windows ストア JavaScript</a>
<a href="/ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-phone-validate-modify-data/" title="Windows Phone">Windows Phone</a>
<a href="/ja-jp/develop/mobile/tutorials/validate-modify-and-augment-data-ios" title="iOS">iOS</a>
<a href="/ja-jp/develop/mobile/tutorials/validate-modify-and-augment-data-android" title="Android">Android</a>
<a href="/ja-jp/develop/mobile/tutorials/validate-modify-and-augment-data-html" title="HTML">HTML</a><a href="/ja-jp/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-ios" title="Xamarin.iOS">Xamarin.iOS</a>
<a href="/ja-jp/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-android" title="Xamarin.Android">Xamarin.Android</a>
</div>

<div class="dev-center-tutorial-subselector">
<a href="/ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-validate-modify-data/" title=".NET バックエンド" class="current">.NET バックエンド</a> | 
<a href="/ja-jp/documentation/articles/mobile-services-windows-store-dotnet-validate-modify-data-server-scripts/"  title="JavaScript バックエンド">JavaScript バックエンド</a>
</div>

このトピックでは、.NET バックエンド Azure モバイル サービス内でコードを使用してデータを検証および変更する方法について説明します。.NET バックエンド サービスは、Web API フレームワークと Entity Framework を使用して構築した HTTP サービスです。Web API フレームワークで定義されている `ApiController` クラスについて理解している場合、モバイル サービスによって提供されている `TableController` クラスは非常に直感的に把握できます。`TableController` は、`ApiController` クラスから派生したもので、データベース テーブルとやり取りするための追加機能を提供します。挿入や更新の対象となるデータを操作する目的で使用でき、このチュートリアルで示す検証やデータの変更もその中に含まれます。

このチュートリアルでは、次の基本的な手順について説明します。

1.  [文字列の長さの検証の追加][文字列の長さの検証の追加]
2.  [検証をサポートするためのクライアントの更新][検証をサポートするためのクライアントの更新]
3.  [長さの検証テスト][長さの検証テスト]
4.  [CompleteDate に関するタイムスタンプ フィールドの追加][CompleteDate に関するタイムスタンプ フィールドの追加]
5.  [CompleteDate を表示するためのクライアントの更新][CompleteDate を表示するためのクライアントの更新]

このチュートリアルは、以前のチュートリアルである「[作業の開始][作業の開始]」または「[データの使用][データの使用]」に関する手順およびサンプル コードを基に作成されています。このチュートリアルを開始する前に、「[作業の開始][作業の開始]」または「[データの使用][データの使用]」を完了している必要があります。

## <a name="string-length-validation"></a>検証の追加

[WACOM.INCLUDE [mobile-services-dotnet-backend-add-validation][mobile-services-dotnet-backend-add-validation]]

## <a name="update-client-validation"></a>クライアントの更新

今度は、データを検証し、テキストの長さが無効な場合はエラー応答を送信するようにモバイル サービスを設定するため、検証からのエラー応答を処理できるようにアプリケーションを更新する必要があります。エラーは、クライアント アプリケーションによる `MobileServiceInvalidOperationException` の呼び出しからの `IMobileServiceTable<TodoItem].InsertAsync()` としてキャッチされます。

1.  Visual Studio のソリューション エクスプローラー ウィンドウで、クライアント プロジェクトに移動し、MainPage.xaml.cs ファイルを開きます。ファイルに次の **using** ステートメントを追加します。

        using Windows.UI.Popups;
        using Newtonsoft.Json;
        using Newtonsoft.Json.Linq;

2.  MainPage.xaml.cs で、既存の **InsertTodoItem** メソッドを次のコードで置き換えます。

        private async void InsertTodoItem(TodoItem todoItem)
        {
            // This code inserts a new TodoItem into the database. When the operation completes
            // and Mobile Services has assigned an Id, the item is added to the CollectionView
            MobileServiceInvalidOperationException invalidOpException = null;
            try
            {
                await todoTable.InsertAsync(todoItem);
                items.Add(todoItem);
            }
            catch(MobileServiceInvalidOperationException e)
            {
                invalidOpException = e;
            }
            if (invalidOpException != null)
            {
                string strJsonContent = await invalidOpException.Response.Content.ReadAsStringAsync();
                var responseContent = JObject.Parse(strJsonContent);
                MessageDialog errormsg = new MessageDialog(string.Format("{0} (HTTP {1})", 
                    (string)responseContent["message"],(int)invalidOpException.Response.StatusCode), 
                    invalidOpException.Message);
                var ignoreAsyncOpResult = errormsg.ShowAsync();
            }
        }

    このバージョンのメソッドには、**MobileServiceInvalidOperationException** に対するエラー処理が含まれていて、応答内容から取得した逆シリアル化エラー メッセージをメッセージ ダイアログ内に表示します。

## <a name="test-length-validation"></a>長さの検証テスト

1.  Visual Studio のソリューション エクスプローラーで、クライアント アプリケーション プロジェクトを右クリックし、**[スタートアップ プロジェクトに設定]** をクリックします。その後 **F5** キーを押して、IIS Express 内で、サービスをホストするアプリケーションをローカルで開始します。

2.  新しい todo 項目に対して 10 文字を超える長さのテキストを入力し、**[保存]** をクリックします。

    ![][]

3.  無効なテキストへの応答として、次のようなメッセージ ダイアログが表示されます。

    ![][1]

## <a name="add-timestamp"></a>CompleteDate に関するタイムスタンプ フィールドの追加

[WACOM.INCLUDE [mobile-services-dotnet-backend-add-completedate][mobile-services-dotnet-backend-add-completedate]]

## <a name="update-client-timestamp"></a>CompleteDate を表示するためのクライアントの更新

最後の手順は、クライアントを更新して新しい **CompleteDate** データを表示することです。

1.  Visual Studio のソリューション エクスプローラーで todolist クライアント プロジェクトを使用し、MainPage.xaml ファイルを開き、その中の **CheckBoxComplete** 要素を、次に示す定義で置き換えます。その後、ファイルを保存します。これにより、**CheckBoxComplete** に対応するイベント ハンドラーが変更され、`click` イベントをコード内で処理することになります。また、チェック ボックスの横にテキスト ブロックを追加して、完全な日付タイムスタンプにバインドします。

        <CheckBox Name="CheckBoxComplete" IsChecked="{Binding Complete, Mode=TwoWay}" 
          Click="CheckBoxComplete_Clicked" Content="{Binding Text}" Margin="10,5" VerticalAlignment="Center"/>
        <TextBlock Name="textCompleteDate" Text="{Binding CompleteDate}" VerticalAlignment="Center"/>

2.  Visual Studio のソリューション エクスプローラーで todolist クライアント プロジェクトを使用し、MainPage.xaml.cs ファイルを開き、`CheckBoxComplete_Checked` イベント ハンドラーを、次に示す `CheckBoxComplete_Clicked` に置き換えます。これにより、項目が完成した後、完全な日付を確認できます。

        private void CheckBoxComplete_Clicked(object sender, RoutedEventArgs e)
        {
            CheckBox cb = (CheckBox)sender;
            TodoItem item = cb.DataContext as TodoItem;
            UpdateCheckedTodoItem(item);
        }

3.  MainPage.xaml.cs ファイルで、既存の **TodoItem** クラスを、null 許容型として新しい **CompleteDate** プロパティを含む、次の定義に置き換えます。

        public class TodoItem
        {
            public string Id { get; set; }
            [JsonProperty(PropertyName = "text")]
            public string Text { get; set; }
            [JsonProperty(PropertyName = "complete")]
            public bool Complete { get; set; }        
            [JsonProperty(PropertyName = "CompleteDate")]
            public DateTime? CompleteDate { get; set; }
        }

    > [WACOM.NOTE] `DataMemberAttribute` は、アプリケーション内の新しい `CompleteDate` プロパティを、TodoItem テーブル内で定義された `CompleteDate` 列にマップするようにクライアントに指示します。この属性を使用することにより、アプリケーションでは、SQL データベース内の列名と異なるプロパティ名をオブジェクトに対して使用することができます。

4.  MainPage.xaml.cs で、既存の **RefreshTodoItems** メソッド内にある `.Where` 句関数を削除またはコメント アウトします。その結果、完了した todoitems が結果の中に含まれるようになります。

            // This query filters out completed TodoItems and 
            // items without a timestamp. 
            items = await todoTable
               //.Where(todoItem => todoItem.Complete == false)
               .ToCollectionAsync();

5.  MainPage.xaml.cs 内で、**UpdateCheckedTodoItem** メソッドを次のように更新します。その結果、更新を行った後に項目の表示が更新され、完了した項目はリストから削除されなくなります。その後、ファイルを保存します。

        private async void UpdateCheckedTodoItem(TodoItem item)
        {
            // This code takes a freshly completed TodoItem and updates the database.
            await todoTable.UpdateAsync(item);
            //items.Remove(item);
            RefreshTodoItems();
        }

6.  Visual Studio の ソリューション エクスプローラー ウィンドウで **[ソリューション]** を右クリックし、**[ソリューションのリビルド]** をクリックして、クライアントと .NET バックエンド サービスの両方をリビルドします。両方のプロジェクトがエラーなしでビルドされることを確認します。

    ![][2]

7.  **F5** キーを押して、クライアント アプリケーションとサービスをローカルで実行します。いくつかの新しい項目を追加し、いくつかの項目に完了マークを付けて、**CompleteDate** タイムスタンプが更新されていることを確認します。

    ![][3]

8.  Visual Studio のソリューション エクスプローラーで、todolist サービス プロジェクトを右クリックし、**[発行]** をクリックします。Azure ポータルからダウンロードした発行設定ファイルを使用して、.NET バックエンド サービスを Microsoft Azure に発行します。

9.  モバイル サービス アドレスへの接続をコメント解除して、クライアント プロジェクトに対応する App.xaml.cs ファイルを更新します。Azure アカウント内でホストされている .NET バックエンドに対してアプリケーションをテストします。

## <a name="next-steps"> </a>次のステップ

このチュートリアルが完了したため、データ シリーズの最終チュートリアルに進むことを検討してください。[ページングを使用したクエリの改善][ページングを使用したクエリの改善]。

サーバー スクリプトは、ユーザーを認証するときに、およびプッシュ通知の送信のためにも使用されます。詳細については、次のチュートリアルを参照してください。

-   [ユーザーのサービス側の認証][ユーザーのサービス側の認証]
    認証されたユーザーの ID に基づきデータをフィルター処理する方法について説明します。

-   [プッシュ通知の使用][プッシュ通知の使用]
    アプリケーションにごく基本的なプッシュ通知を送信する方法について説明します。

-   [モバイル サービス .NET の使用方法の概念リファレンス][モバイル サービス .NET の使用方法の概念リファレンス]
    .NET で Mobile Services を使用する方法について説明します。

<!-- Anchors. --> 
<!-- Images. --> 
<!-- URLs. -->

  [Windows ストア C#]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-validate-modify-data/ "Windows ストア C#"
  [Windows ストア JavaScript]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-validate-modify-data/ "Windows ストア JavaScript"
  [Windows Phone]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-phone-validate-modify-data/ "Windows Phone"
  [iOS]: /ja-jp/develop/mobile/tutorials/validate-modify-and-augment-data-ios "iOS"
  [Android]: /ja-jp/develop/mobile/tutorials/validate-modify-and-augment-data-android "Android"
  [HTML]: /ja-jp/develop/mobile/tutorials/validate-modify-and-augment-data-html "HTML"
  [Xamarin.iOS]: /ja-jp/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-ios "Xamarin.iOS"
  [Xamarin.Android]: /ja-jp/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-android "Xamarin.Android"
  [.NET バックエンド]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-validate-modify-data/ ".NET バックエンド"
  [JavaScript バックエンド]: /ja-jp/documentation/articles/mobile-services-windows-store-dotnet-validate-modify-data-server-scripts/ "JavaScript バックエンド"
  [文字列の長さの検証の追加]: #string-length-validation
  [検証をサポートするためのクライアントの更新]: #update-client-validation
  [長さの検証テスト]: #test-length-validation
  [CompleteDate に関するタイムスタンプ フィールドの追加]: #add-timestamp
  [CompleteDate を表示するためのクライアントの更新]: #update-client-timestamp
  [作業の開始]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started/
  [データの使用]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/
  [mobile-services-dotnet-backend-add-validation]: ../includes/mobile-services-dotnet-backend-add-validation.md
  []: ./media/mobile-services-dotnet-backend-windows-store-dotnet-validate-modify-data/mobile-services-invalid-text-length.png
  [1]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-validate-modify-data/mobile-services-invalid-text-length-exception-dialog.png
  [mobile-services-dotnet-backend-add-completedate]: ../includes/mobile-services-dotnet-backend-add-completedate.md
  [2]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-validate-modify-data/mobile-services-rebuild-solution.png
  [3]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-validate-modify-data/mobile-services-final-local-app-run.png
  [ページングを使用したクエリの改善]: /ja-jp/develop/mobile/tutorials/add-paging-to-data-dotnet
  [ユーザーのサービス側の認証]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-authorize-users-in-scripts/
  [プッシュ通知の使用]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push/
  [モバイル サービス .NET の使用方法の概念リファレンス]: /ja-jp/develop/mobile/how-to-guides/work-with-net-client-library
