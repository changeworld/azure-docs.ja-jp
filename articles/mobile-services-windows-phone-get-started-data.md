<properties linkid="develop-mobile-tutorials-get-started-with-data-wp8" urlDisplayName="Get Started with Data" pageTitle="Get started with data (WP8) - Azure Mobile Services" metaKeywords="" description="Learn how to get started using data from your Azure Mobile Services Windows Phone 8 app." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with data in Mobile Services" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-phone" ms.devlang="dotnet" ms.topic="article" ms.date="09/19/2014" ms.author="glenga" />

# モバイル サービスでのデータの使用

[WACOM.INCLUDE [mobile-services-selector-get-started-data-legacy](../includes/mobile-services-selector-get-started-data-legacy.md)]

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">

<p>このトピックでは、Azure のモバイル サービスを使用して Windows Phone 8 アプリケーションのデータを活用する方法について説明します。このチュートリアルでは、メモリにデータを格納するアプリケーションをダウンロードした後、新しいモバイル サービスを作成してそれをアプリケーションに統合します。その後、Azure 管理ポータルにログインして、アプリケーションの実行中にデータに加えられた変更を表示します。</p>
</div>

<div class="dev-onpage-video-wrapper"><a href="http://go.microsoft.com/fwlink/?LinkID=298628" target="_blank" class="label">チュートリアルを見る</a> <a style="background-image: url('/media/devcenter/mobile/videos/mobile-wp8-get-started-data-180x120.png') !important;" href="http://go.microsoft.com/fwlink/?LinkID=298628" target="_blank" class="dev-onpage-video"><span class="icon">ビデオを再生する</span></a> <span class="time">12:54:00</span></div>

</div>

<div class="dev-callout"><b>注</b>
<p>このチュートリアルの目的は、Azure を使用して Windows Phone 8 アプリケーションのデータを格納および取得できるようにするためのモバイル サービスのしくみを説明することにあります。したがって、このトピックでは、モバイル サービスのクイック スタートで完了している手順の多くについても説明します。モバイル サービスを初めて使用する場合は、最初にチュートリアル「<a href="/ja-jp/develop/mobile/tutorials/get-started-wp8">モバイル サービスの使用</a>」を完了することをお勧めします。</p>
</div>

このチュートリアルでは、次の基本的な手順について説明します。

1.  [Windows Phone 8 アプリ プロジェクトのダウンロード][Windows Phone 8 アプリ プロジェクトのダウンロード]
2.  [モバイル サービスの作成][モバイル サービスの作成]
3.  [ストレージのデータ テーブルの追加][ストレージのデータ テーブルの追加]
4.  [モバイル サービスを使用するためのアプリケーションの更新][モバイル サービスを使用するためのアプリケーションの更新]
5.  [モバイル サービスに対するアプリケーションのテスト][モバイル サービスに対するアプリケーションのテスト]

このチュートリアルでは、Visual Studio 2012 Express for Windows Phone 8 および Windows 8 で実行する [Windows Phone 8 SDK][Windows Phone 8 SDK] が必要になります。このチュートリアルを完了して、Windows Phone 8.1 アプリケーションを作成するには、Visual Studio 2013 Update 2 以降が必要です。

> [WACOM.NOTE] このチュートリアルを完了するには、Azure アカウントが必要です。アカウントがない場合は、無料の試用アカウントを数分で作成することができます。詳細については、[Azure の無料評価版サイト][Azure の無料評価版サイト]を参照してください。

## <a name="download-app"></a>GetStartedWithData プロジェクトをダウンロードする

このチュートリアルは、Windows Phone Silverlight 8 アプリケーション プロジェクトである [GetStartedWithData app][GetStartedWithData app] アプリケーションに基づいています。このアプリケーションの UI は、追加された項目がメモリにローカルに格納される点を除き、モバイル サービスのクイック スタートで生成したアプリケーションと同じです。

1.  GetStartedWithData サンプル アプリケーション プロジェクトを[デベロッパー サンプル コード集のサイト][GetStartedWithData app]からダウンロードします。

    > [WACOM.NOTE] Windows Phone Silverlght 8.1 アプリケーションを作成するには、ダウンロードした Windows Phone Silverlight 8 アプリケーション プロジェクトの対象 OS を Windows Phone 8.1 に変更します。Windows Phone Store アプリケーションを作成するには、GetStartedWithData サンプル アプリケーション プロジェクトの [Windows Phone Store アプリケーション バージョン][Windows Phone Store アプリケーション バージョン]をダウンロードします。

2.  Visual Studio で、ダウンロードしたプロジェクトを開き、MainPage.xaml.cs ファイルを確認します。

    追加された **TodoItem** オブジェクトはメモリ内の **ObservableCollection\<TodoItem\>** に格納されます。

3.  **F5** キーを押してプロジェクトをリビルドし、アプリケーションを開始します。

4.  アプリケーションで、テキスト ボックスに任意のテキストを入力し、**[Save]** をクリックします。

    ![][0]

    保存されたテキストが下のリストに表示されます。

## <a name="create-service"></a><span class="short-header">モバイル サービスの作成</span>管理ポータルでの新しいモバイル サービスの作成

[WACOM.INCLUDE [mobile-services-create-new-service-data](../includes/mobile-services-create-new-service-data.md)]

## <a name="add-table"></a><span class="short-header">新しいテーブルの追加</span>モバイル サービスへの新しいテーブルの追加

[WACOM.INCLUDE [mobile-services-create-new-service-data-2](../includes/mobile-services-create-new-service-data-2.md)]

## <a name="update-app"></a><span class="short-header">アプリケーションの更新</span>モバイル サービスをデータ アクセスに使用するためのアプリケーションの更新

モバイル サービスの準備が整ったら、ローカル コレクションの代わりにモバイル サービスに項目を格納するようにアプリケーションを更新します。

1.  Visual Studio の**ソリューション エクスプローラー**で、プロジェクト名を右クリックし、**[NuGet パッケージの管理]** をクリックします。

2.  左側のウィンドウで、**[オンライン]** カテゴリを選択し、`WindowsAzure.MobileServices` を探します。**Azure Mobile Services** パッケージで **[インストール]** をクリックし、使用許諾契約に同意します。

    ![][1]

    これにより、モバイル サービス クライアント ライブラリがプロジェクトに追加されます。

3.  管理ポータルで、**[モバイル サービス]** をクリックし、先ほど作成したモバイル サービスをクリックします。

4.  **[ダッシュボード]** タブをクリックし、**サイトの URL** をメモに記録します。次に、**[キーの管理]** をクリックし、**アプリケーション キー**をメモに記録します。

    ![][2]

    これらの値は、アプリケーション コードからモバイル サービスにアクセスするときに必要になります。

5.  Visual Studio で App.xaml.cs ファイルを開き、次の `using` ステートメントを追加またはコメント解除します。

        using Microsoft.WindowsAzure.MobileServices;

6.  この同じファイルで、**MobileService** 変数を定義するコードをコメント解除します。また、**MobileServiceClient** コンストラクターに、モバイル サービスで入手した URL とアプリケーション キーを (この順序で) 指定します。

        //public static MobileServiceClient MobileService = new MobileServiceClient( 
        //    "AppUrl", 
        //    "AppKey" 
        //); 

    これで、モバイル サービスへのアクセスに使用される **MobileServiceClient** の新しいインスタンスが作成されます。

7.  MainPage.xaml.cs ファイルで、次の `using` ステートメントを追加またはコメント解除します。

        using Microsoft.WindowsAzure.MobileServices;
        using Newtonsoft.Json;

8.  この同じファイルで、**TodoItem** クラス定義を次のコードに置き換えます。

        public class TodoItem
        {
            public string Id { get; set; }

            [JsonProperty(PropertyName = "text")]
            public string Text { get; set; }

            [JsonProperty(PropertyName = "complete")]
            public bool Complete { get; set; }
        }

9.  既存の **items** コレクションを定義する行をコメント アウトし、次の行をコメント解除します。

        private MobileServiceCollection<TodoItem, TodoItem> items;
        private IMobileServiceTable<TodoItem> todoTable = 
            App.MobileService.GetTable<TodoItem>();

    このコードは、モバイル サービス対応のバインディング コレクション (**items**) と SQL データベース テーブルのプロキシ クラス **TodoItem** (**todoTable**) を作成します。

10. **InsertTodoItem** メソッド内で、**TodoItem**.**Id** プロパティを設定するコード行を削除し、**async** 修飾子をメソッドに追加して、次のコード行をコメント解除します。

        await todoTable.InsertAsync(todoItem);

    このコードでは、新しい項目をテーブルに挿入します。

11. **RefreshTodoItems** メソッド内で、**async** 修飾子をメソッドに追加して、次のコード行をコメント解除します。

        items = await todoTable.ToCollectionAsync();

    これにより、todoTable 内で、モバイル サービスから返されたすべての TodoItem オブジェクトが格納される項目のコレクションへのバインディングが設定されます。

12. **UpdateCheckedTodoItem** メソッド内で、**async** 修飾子をメソッドに追加して、次のコード行をコメント解除します。

         await todoTable.UpdateAsync(item);

    これにより、項目の更新がモバイル サービスに送信されます。

バックエンド ストレージのモバイル サービスを使用するようにアプリケーションを更新した後は、モバイル サービスに対してアプリケーションをテストします。

## <a name="test-app"></a><span class="short-header">アプリケーションのテスト</span>新しいモバイル サービスに対するアプリケーションのテスト

1.  Visual Studio で、F5 キーを押してアプリケーションを実行します。

2.  前と同様に、テキスト ボックスにテキストを入力し、**[Save]** をクリックします。

    これにより、新しい項目が挿入としてモバイル サービスに送信されます。

3.  [管理ポータル][管理ポータル]で、**[モバイル サービス]** をクリックし、対象のモバイル サービスをクリックします。

4.  **[データ]** タブをクリックし、**[参照]** をクリックします。

    ![][3]

    **TodoItem** テーブルに、モバイル サービスによって生成された ID 値を持つデータが含まれ、アプリケーションの TodoItem クラスに対応してその列が自動的にテーブルに追加されていることに注目してください。

これで、Windows Phone 8 向けの**データの使用**に関するチュートリアルは終了です。

## <a name="next-steps"> </a>次のステップ

このチュートリアルでは、Windows Phone 8 アプリでモバイル サービスのデータを操作できるようにするための基本について説明しました。次は、このチュートリアルで作成した GetStartedWithData アプリケーションに基づく次のチュートリアルを行うことをお勧めします。

-   [サーバー スクリプトを使用したモバイル サービスのデータの検証および変更][サーバー スクリプトを使用したモバイル サービスのデータの検証および変更]
    Mobile Services でサーバー スクリプトを使用して、アプリケーションから送信されたデータを検証および変更する方法について説明します。

-   [ページングを使用したモバイル サービス クエリの改善][ページングを使用したモバイル サービス クエリの改善]
    クエリ内でページングを使用して、1 回の要求で渡されるデータの量を制御する方法について説明します。

データ シリーズを完了した後は、次に示すいずれかの Windows Phone 8 チュートリアルを行うことができます。

-   [認証の使用][認証の使用]
    アプリケーションのユーザーを認証する方法について説明します。

-   [プッシュ通知の使用][プッシュ通知の使用]
    Mobile Services を使用してアプリケーションにごく基本的なプッシュ通知を送信する方法について説明します。

 
 


  [Windows Phone 8 アプリ プロジェクトのダウンロード]: #download-app
  [モバイル サービスの作成]: #create-service
  [ストレージのデータ テーブルの追加]: #add-table
  [モバイル サービスを使用するためのアプリケーションの更新]: #update-app
  [モバイル サービスに対するアプリケーションのテスト]: #test-app
  [Windows Phone 8 SDK]: http://go.microsoft.com/fwlink/p/?LinkID=268374
  [Azure の無料評価版サイト]: http://www.windowsazure.com/ja-jp/pricing/free-trial/?WT.mc_id=A756A2826&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fja-jp%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-with-data-wp8%2F
  [GetStartedWithData app]: http://go.microsoft.com/fwlink/p/?LinkId=271146
  [Windows Phone Store アプリケーション バージョン]: http://go.microsoft.com/fwlink/p/?LinkId=397372
  [0]: ./media/mobile-services-windows-phone-get-started-data/mobile-quickstart-startup-wp8.png
  [1]: ./media/mobile-services-windows-phone-get-started-data/mobile-add-nuget-package-wp.png
  [2]: ./media/mobile-services-windows-phone-get-started-data/mobile-dashboard-tab.png
  [管理ポータル]: https://manage.windowsazure.com/
  [3]: ./media/mobile-services-windows-phone-get-started-data/mobile-todoitem-data-browse.png
  [サーバー スクリプトを使用したモバイル サービスのデータの検証および変更]: /ja-jp/develop/mobile/tutorials/validate-modify-and-augment-data-wp8
  [ページングを使用したモバイル サービス クエリの改善]: /ja-jp/develop/mobile/tutorials/add-paging-to-data-wp8
  [認証の使用]: /ja-jp/develop/mobile/tutorials/get-started-with-users-wp8
  [プッシュ通知の使用]: /ja-jp/develop/mobile/tutorials/get-started-with-push-wp8
