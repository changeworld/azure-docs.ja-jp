<properties linkid="develop-mobile-tutorials-get-started-with-data-dotnet-vs2013" urlDisplayName="Get Started with Data" pageTitle="Get started with data (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to get started using Mobile Services to leverage data in your Windows Store app." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with data in Mobile Services (universal Windows apps)" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="glenga"></tags>

# Mobile Services でのデータの使用 (ユニバーサル Windows アプリケーション)

<div class="dev-center-tutorial-selector sublanding"><a href="/ja-jp/documentation/articles/mobile-services-windows-store-dotnet-get-started-data/" title="Windows ストア C#">Windows ストア C#</a><a href="/ja-jp/documentation/articles/mobile-services-windows-store-javascript-get-started-data/" title="Windows ストア JavaScript">Windows ストア JavaScript</a><a href="/ja-jp/documentation/articles/mobile-services-windows-phone-get-started-data/" title="Windows Phone">Windows Phone</a><a href="/ja-jp/documentation/articles/mobile-services-ios-get-started-data/" title="iOS">iOS</a><a href="/ja-jp/documentation/articles/mobile-services-android-get-started-data/" title="Android">Android</a><a href="/ja-jp/documentation/articles/mobile-services-html-get-started-data/" title="HTML">HTML</a><a href="/ja-jp/documentation/articles/partner-xamarin-mobile-services-ios-get-started-data/" title="Xamarin.iOS">Xamarin.iOS</a><a href="/ja-jp/documentation/articles/partner-xamarin-mobile-services-android-get-started-data/" title="Xamarin.Android">Xamarin.Android</a></div>

<!---<div class="dev-center-tutorial-subselector"><a href="/ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/" title=".NET backend">.NET backend</a> | <a href="/ja-jp/documentation/articles/mobile-services-windows-store-dotnet-get-started-data/"  title="JavaScript backend" class="current">JavaScript backend</a></div>-->

このトピックでは、Azure Mobile Services を使用してユニバーサル Windows 8.1 アプリケーションのデータを活用する方法について説明します。このチュートリアルでは、メモリにデータを格納するユニバーサル Windows アプリケーションの Visual Studio プロジェクトをダウンロードし、新しいモバイル サービスを作成してそれをアプリケーションに統合します。その後、Azure 管理ポータルにログインして、アプリケーションの実行中にデータに加えられた変更を表示します。

> [WACOM.NOTE] このチュートリアルには、Visual Studio 2013 Update 2 が必要です。

このチュートリアルでは、次の基本的な手順について説明します。

1.  [Windows ストア アプリ プロジェクトのダウンロード][Windows ストア アプリ プロジェクトのダウンロード]
2.  [Visual Studio からのモバイル サービスの作成][Visual Studio からのモバイル サービスの作成]
3.  [ストレージのデータ テーブルの追加とアプリケーションの更新][ストレージのデータ テーブルの追加とアプリケーションの更新]
4.  [モバイル サービスに対するアプリケーションのテスト][モバイル サービスに対するアプリケーションのテスト]

> [WACOM.NOTE] このチュートリアルを完了するには、Azure アカウントが必要です。アカウントがない場合は、無料の試用アカウントを数分で作成することができます。詳細については、[Azure の無料評価版サイト][Azure の無料評価版サイト]を参照してください。

## <a name="download-app"></a>GetStartedWithData プロジェクトをダウンロードする

このチュートリアルは、Visual Studio 2013 の Windows ストア アプリ プロジェクトの [GetStartedWithMobileServices アプリケーション][GetStartedWithMobileServices アプリケーション]に基づいています。このアプリケーションの UI は、追加された項目がメモリにローカルに格納される点を除き、モバイル サービスのクイック スタートで生成したアプリケーションと同じです。

1.  C# バージョンの GetStartedWithMobileServices サンプル アプリケーションを[デベロッパー サンプル コード集のサイト][GetStartedWithMobileServices アプリケーション]からダウンロードします。

    ![][]

2.  Visual Studio 2013 で、ダウンロードしたプロジェクトを開き、MainPage.xaml.cs ファイルを確認します。

    追加された **TodoItem** オブジェクトはメモリ内の **ObservableCollection\<TodoItem\>** に格納されます。

3.  **F5** キーを押してプロジェクトをリビルドし、アプリケーションを開始します。

4.  アプリケーションで、**[Insert a TodoItem]** に任意のテキストを入力し、**[Save]** をクリックします。

    ![][1]

    保存したテキストが、**[Query and update data]** の下の 2 番目の列に表示されます。

## <a name="create-service"></a><span class="short-header">モバイル サービスの作成</span>Visual Studio から新しいモバイル サービスを作成する

[WACOM.INCLUDE [mobile-services-create-new-service-vs2013][mobile-services-create-new-service-vs2013]]

1.  ソリューション エクスプローラーで、App.xaml.cs コード ファイルを開き、\*\*App\*\* クラスに追加された新しい静的フィールドを確認します。次に例を示します。

        public static Microsoft.WindowsAzure.MobileServices.MobileServiceClient 
                    todolistClient = new Microsoft.WindowsAzure.MobileServices.MobileServiceClient(
                        "https://todolist.azure-mobile.net/",
                        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX");
                

    このコードは、[MobileServiceClient クラス][MobileServiceClient クラス]のインスタンスを使用して、アプリケーションの新しいモバイル サービスへのアクセスを提供します。クライアントは、新しいモバイル サービスの URI とアプリケーション キーを提供することによって作成されます。この静的フィールドは、アプリケーションのすべてのページで使用できます。

## <a name="add-table"></a><span class="short-header">新しいテーブルの追加</span>モバイル サービスに新しいテーブルを追加してアプリケーションを更新する

[WACOM.INCLUDE [mobile-services-create-new-table-vs2013][mobile-services-create-new-table-vs2013]]

1.  MainPage.xaml.cs ファイルで、次の using ステートメントを追加またはコメント解除します。

        using Microsoft.WindowsAzure.MobileServices;
        using Newtonsoft.Json;

2.  この同じファイルで、TodoItem クラス定義を次のコードに置き換えます。

        public class TodoItem
        {
            public string Id { get; set; }

            [JsonProperty(PropertyName = "text")]
            public string Text { get; set; }

            [JsonProperty(PropertyName = "complete")] 
            public bool Complete { get; set; }
        }

    **JsonPropertyAttribute** は、クライアント型のプロパティ名と基になるデータ テーブル内の列名とのマッピングを定義します。

3.  既存の items コレクションを定義する行をコメント アウトし、次の行のコメント アウトを解除するか、次の行を追加します。ここで *\<yourClient\>* は、プロジェクトをモバイル サービスに接続したときに App.xaml.cs ファイルに追加された `MobileServiceClient` フィールドと置き換えます。

        private MobileServiceCollection<TodoItem, TodoItem> items;
        private IMobileServiceTable<TodoItem> todoTable = 
            App.<yourClient>.GetTable<TodoItem>();

    このコードは、モバイル サービス対応のバインディング コレクション (items) とデータベース テーブルのプロキシ クラス (todoTable) を作成します。

4.  **InsertTodoItem** メソッド内で、**TodoItem.Id** プロパティを設定するコード行を削除し、**async** 修飾子をメソッドに追加して、次のコード行をコメント解除します。

        await todoTable.InsertAsync(todoItem);

    このコードでは、新しい項目をテーブルに挿入します。

    <div class="dev-callout"><strong>注</strong><p>Id、__createdAt、__updatedAt、および __version の列を含む新しいテーブルが作成されます。動的スキーマが有効な場合、挿入または更新の要求に含まれる JSON オブジェクトに基づいて、モバイル サービスによって自動的に新しい列が生成されます。詳細については、「<a href="http://msdn.microsoft.com/ja-jp/library/windowsazure/jj193175.aspx">動的スキーマ</a>」を参照してください。</p></div>

5.  **RefreshTodoItems** メソッド内で、**async** 修飾子をメソッドに追加して、次のコード行をコメント解除します。

        items = await todoTable.ToCollectionAsync();

    これにより、`todoTable` 内で、モバイル サービスから返されたすべての **TodoItem** オブジェクトが格納される項目のコレクションへのバインディングが設定されます。

6.  **UpdateCheckedTodoItem** メソッド内で、**async** 修飾子をメソッドに追加して、次のコード行をコメント解除します。

        await todoTable.UpdateAsync(item);

    これにより、項目の更新がモバイル サービスに送信されます。

バックエンド ストレージのモバイル サービスを使用するようにアプリケーションを更新した後は、モバイル サービスに対してアプリケーションをテストします。

## <a name="test-app"></a><span class="short-header">アプリケーションのテスト</span>新しいモバイル サービスに対するアプリケーションのテスト

1.  Visual Studio で、F5 キーを押してアプリケーションを実行します。

2.  ここでも、**[Insert a TodoItem]** にテキストを入力し、**[Save]** をクリックします。

    これにより、新しい項目が挿入としてモバイル サービスに送信されます。

3.  [管理ポータル][管理ポータル]で、**[モバイル サービス]** をクリックし、対象のモバイル サービスをクリックします。

4.  **[データ]** タブをクリックし、**[参照]** をクリックします。

    ![][2]

    **TodoItem** テーブルに、モバイル サービスによって生成された ID 値を持つデータが含まれ、アプリケーションの TodoItem クラスに対応してその列が自動的にテーブルに追加されていることに注目してください。

5.  アプリケーションで、リストの項目の 1 つをチェックします。ポータルの **[参照]** タブに戻り、[最新の情報に更新] をクリックします。

    "complete" 値が **false** から **true** に変更されます。

6.  MainPage.xaml.cs プロジェクト ファイルで、既存の **RefreshTodoItems** メソッドを、完了済み項目をフィルター処理で除外する次のコード行で置き換えます。

        private async void RefreshTodoItems()
        {                       
            // This query filters out completed TodoItems. 
            items = await todoTable
               .Where(todoItem => todoItem.Complete == false)
               .ToCollectionAsync();

            ListItems.ItemsSource = items;            
        }

7.  アプリケーションで、リストの項目をもう 1 つチェックし、**[最新の情報に更新]** をクリックします。

    リスト内のチェックされた項目が非表示になります。最新の情報への更新のたびにモバイル サービスへの往復が発生し、その結果、フィルター処理されたデータが返されます。

これで、**データの使用**に関するチュートリアルはおしまいです。

## <a name="next-steps"> </a>次のステップ

このチュートリアルでは、Windows ストア アプリでモバイル サービスのデータを操作できるようにするための基本について説明しました。次は、このチュートリアルで作成した GetStartedWithData アプリケーションに基づく次のいずれかのチュートリアルを行うことをお勧めします。

-   [サーバー スクリプトを使用したモバイル サービスのデータの検証および変更][サーバー スクリプトを使用したモバイル サービスのデータの検証および変更]
    
    Mobile Services でサーバー スクリプトを使用して、アプリケーションから送信されたデータを検証および変更する方法について説明します。

-   [ページングを使用したモバイル サービス クエリの改善][ページングを使用したモバイル サービス クエリの改善]
    
    クエリ内でページングを使用して、1 回の要求で渡されるデータの量を制御する方法について説明します。

データ シリーズを完了した後は、次に示す他のチュートリアルのいずれかを行うことをお勧めします。

-   [認証の使用][認証の使用]
    
    アプリケーションのユーザーを認証する方法について説明します。

-   [プッシュ通知の使用][プッシュ通知の使用]
    
    アプリケーションにごく基本的なプッシュ通知を送信する方法について説明します。

-   [モバイル サービス .NET の使用方法の概念リファレンス][モバイル サービス .NET の使用方法の概念リファレンス]
    
    .NET で Mobile Services を使用する方法について説明します。



  [Windows ストア C#]: /ja-jp/documentation/articles/mobile-services-windows-store-dotnet-get-started-data/ "Windows ストア C#"
  [Windows ストア JavaScript]: /ja-jp/documentation/articles/mobile-services-windows-store-javascript-get-started-data/ "Windows ストア JavaScript"
  [Windows Phone]: /ja-jp/documentation/articles/mobile-services-windows-phone-get-started-data/ "Windows Phone"
  [iOS]: /ja-jp/documentation/articles/mobile-services-ios-get-started-data/ "iOS"
  [Android]: /ja-jp/documentation/articles/mobile-services-android-get-started-data/ "Android"
  [HTML]: /ja-jp/documentation/articles/mobile-services-html-get-started-data/ "HTML"
  [Xamarin.iOS]: /ja-jp/documentation/articles/partner-xamarin-mobile-services-ios-get-started-data/ "Xamarin.iOS"
  [Xamarin.Android]: /ja-jp/documentation/articles/partner-xamarin-mobile-services-android-get-started-data/ "Xamarin.Android"
  [Windows ストア アプリ プロジェクトのダウンロード]: #download-app
  [Visual Studio からのモバイル サービスの作成]: #create-service
  [ストレージのデータ テーブルの追加とアプリケーションの更新]: #add-table
  [モバイル サービスに対するアプリケーションのテスト]: #test-app
  [Azure の無料評価版サイト]: http://www.windowsazure.com/ja-jp/pricing/free-trial/?WT.mc_id=AE564AB28
  [GetStartedWithMobileServices アプリケーション]: http://go.microsoft.com/fwlink/p/?LinkId=328660
  []: ./media/mobile-services-windows-store-dotnet-get-started-data-vs2013/mobile-data-sample-download-dotnet-vs12.png
  [1]: ./media/mobile-services-windows-store-dotnet-get-started-data-vs2013/mobile-quickstart-startup.png
  [mobile-services-create-new-service-vs2013]: ../includes/mobile-services-create-new-service-vs2013.md
  [MobileServiceClient クラス]: http://go.microsoft.com/fwlink/p/?LinkId=302030
  [mobile-services-create-new-table-vs2013]: ../includes/mobile-services-create-new-table-vs2013.md
  [動的スキーマ]: http://msdn.microsoft.com/ja-jp/library/windowsazure/jj193175.aspx
  [管理ポータル]: https://manage.windowsazure.com/
  [2]: ./media/mobile-services-windows-store-dotnet-get-started-data-vs2013/mobile-todoitem-data-browse.png
  [サーバー スクリプトを使用したモバイル サービスのデータの検証および変更]: /ja-jp/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet
  [ページングを使用したモバイル サービス クエリの改善]: /ja-jp/develop/mobile/tutorials/add-paging-to-data-dotnet
  [認証の使用]: /ja-jp/develop/mobile/tutorials/get-started-with-users-dotnet
  [プッシュ通知の使用]: ../mobile-services-windows-store-dotnet-get-started-push/
  [モバイル サービス .NET の使用方法の概念リファレンス]: /ja-jp/develop/mobile/how-to-guides/work-with-net-client-library
