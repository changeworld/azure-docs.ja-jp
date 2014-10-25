<properties linkid="develop-mobile-tutorials-get-started-with-data-xamarin-ios" urlDisplayName="Get Started with Data" pageTitle="Get started with data (Xamarin.iOS) - Azure Mobile Services" metaKeywords="Azure Xamarin.iOS data, Azure mobile services data, " description="Learn how to store and access data from your Azure Mobile Services Xamarin.iOS app." metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Get started with data in Mobile Services" authors="donnam" manager="dwrede" services="mobile-services"/>

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-xamarin-ios" ms.devlang="dotnet" ms.topic="article" ms.date="09/24/2014" ms.author="donnam"></tags>

# モバイル サービスでのデータの使用

[WACOM.INCLUDE [mobile-services-selector-get-started-data][mobile-services-selector-get-started-data]]

このトピックでは、Azure モバイル サービスを使用して Xamarin.iOS アプリのデータを活用する方法について説明します。このチュートリアルでは、メモリにデータを格納するアプリケーションをダウンロードした後、新しいモバイル サービスを作成してそれをアプリケーションに統合します。その後、Azure 管理ポータルにログインして、アプリケーションの実行中にデータに加えられた変更を表示します。

<div class="dev-callout">

<b>注<b/>
<p>このチュートリアルの目的は、Azure を使用して Xamarin.iOS アプリのデータを格納および取得できるようにするモバイル サービスのしくみを説明することにあります。したがって、このトピックでは、モバイル サービスのクイック スタートで完了している手順の多くについても説明します。モバイル サービスを初めて使用する場合は、最初にチュートリアル「[モバイル サービスの使用][モバイル サービスの使用]」を完了することをお勧めします。<p/>

</div>

このチュートリアルでは、次の基本的な手順について説明します。

1.  [Xamarin.iOS アプリケーション プロジェクトのダウンロード][Xamarin.iOS アプリケーション プロジェクトのダウンロード]
2.  [モバイル サービスの作成][モバイル サービスの作成]
3.  [ストレージのデータ テーブルの追加][ストレージのデータ テーブルの追加]
4.  [モバイル サービスを使用するためのアプリケーションの更新][モバイル サービスを使用するためのアプリケーションの更新]
5.  [モバイル サービスに対するアプリケーションのテスト][モバイル サービスに対するアプリケーションのテスト]

このチュートリアルには、[Azure モバイル サービス コンポーネント][Azure モバイル サービス コンポーネント]、[XCode 5.0][XCode 5.0]、[Xamarin.iOS]、および iOS 5.0 以降のバージョンが必要です。

<div class="dev-callout">

<b>注<b/>
<p>このチュートリアルを完了するには、Azure アカウントが必要です。アカウントがない場合は、無料の試用アカウントを数分で作成することができます。詳細については、<a href="http://www.windowsazure.com/ja-jp/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fja-jp%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-with-data-xamarin-ios%2F" target="_blank">[Azure の無料評価版サイト]</a>を参照してください。

</div>

## <a name="download-app"></a><span class="short-header">プロジェクトのダウンロード</span>GetStartedWithData プロジェクトのダウンロード

このチュートリアルは、Xamarin.iOS アプリケーションである [GetStartedWithData アプリケーション][Xamarin.iOS アプリケーション プロジェクトのダウンロード]に基づいています。このアプリケーションの UI は、追加された項目がメモリにローカルに格納される点を除き、モバイル サービス Xamarin.iOS のクイック スタートで生成したアプリケーションと同じです。

1.  [GetStartedWithData アプリケーション][Xamarin.iOS アプリケーション プロジェクトのダウンロード]をダウンロードします。

2.  Xamarin Studio で、ダウンロードしたプロジェクトを開き、**TodoService** クラスを確認します。

    複数の **// TODO** コメントに、このアプリケーションをモバイル サービスで実行するために必要な手順が指定されている点に注目してください。

3.  **[Run]** メニューの **[Start Without Debugging]** を選択して、アプリケーションを開始します。

4.  アプリケーションで、テキスト ボックスにテキストを入力し、**[+]** をクリックします。

    ![][]

    保存されたテキストが下のリストに表示されます。

## <a name="create-service"></a><span class="short-header">モバイル サービスの作成</span>管理ポータルでの新しいモバイル サービスの作成

[WACOM.INCLUDE [mobile-services-create-new-service-data][mobile-services-create-new-service-data]]

## <a name="add-table"></a><span class="short-header">新しいテーブルの追加</span>モバイル サービスへの新しいテーブルの追加

アプリケーション データを新しいモバイル サービスに格納できるようにするには、最初に新しいテーブルを関連付けられた SQL データベース インスタンスに作成する必要があります。

1.  管理ポータルで、**[モバイル サービス]** をクリックし、先ほど作成したモバイル サービスをクリックします。

2.  **[データ]** タブをクリックし、**[作成]** をクリックします。

    ![][1]

    **[新しいテーブルの作成]** ダイアログ ボックスが表示されます。

3.  **[テーブル名]** に「*TodoItem*」と入力し、チェック ボタンをクリックします。

    ![][2]

    これにより、既定のアクセス許可が設定された新しいストレージ テーブル **TodoItem** が作成されます。既定のアクセス許可とは、アプリケーションの任意のユーザーがテーブル内のデータにアクセスし、変更を加えることができることを表します。

    <div class="dev-callout">

    <b>注</b>
    <p>Mobile Services のクイック スタートでも同じテーブル名が使用されています。ただし、それぞれのテーブルは、特定のモバイル サービスに固有のスキーマ内で作成されています。これには、複数のモバイル サービスで同じデータベースを使用した場合にデータの競合を回避する目的があります。

    </div>

4.  新しい **TodoItem** テーブルをクリックし、データ行がないことを確認します。

5.  **[列]** タブをクリックし、自動的に作成される **id** 列のみがあることを確認します。

    これは、モバイル サービスのテーブルの最小要件です。

    <div class="dev-callout">

    <b>注</b>
    <p>モバイル サービスで動的スキーマを有効にすると、挿入操作または更新操作によって JSON オブジェクトがモバイル サービスに送信されるときに新しい列が自動的に作成されます。

    </div>

これで、新しいモバイル サービスをアプリケーションのデータ ストレージとして使用する準備が整いました。

## <a name="update-app"></a><span class="short-header">アプリケーションの更新</span>モバイル サービスをデータ アクセスに使用するためのアプリケーションの更新

モバイル サービスの準備が整ったら、ローカル コレクションの代わりにモバイル サービスに項目を格納するようにアプリケーションを更新します。

1.  **Azure Mobile Services** が Components フォルダーに含まれていない場合は、**[コンポーネント]** を右クリックし、**[コンポーネントの取得]** を選択して、**Azure Mobile Services** を探します。

2.  Xamarin Studio の [Solution] ビューで、**TodoService** クラスを開き、次の `using` ステートメントをコメント解除します。

        using Microsoft.WindowsAzure.MobileServices;

3.  管理ポータルで、**[モバイル サービス]** をクリックし、先ほど作成したモバイル サービスをクリックします。

4.  **[ダッシュボード]** タブをクリックし、**サイトの URL** をメモに記録します。次に、**[キーの管理]** をクリックし、**アプリケーション キー**をメモに記録します。

    ![][3]

5.  **Constants** クラスで、次の定数をコメント解除します。

        public const string ApplicationURL = @"AppUrl";
        public const string ApplicationKey = @"AppKey";

6.  上記の定数の **AppUrl** と **AppKey** を、管理ポータルから取得した上記の値で置き換えます。

7.  **TodoService** クラス内で、次のコード行をコメント解除します。

        private MobileServiceClient client;

    これで、サービスに接続する MobileServiceClient を表すプロパティが作成されます。

8.  **TodoService** クラス内で、次のコード行をコメント解除します。

        private IMobileServiceTable<TodoItem> todoTable;  

    これで、モバイル サービス テーブルのプロパティ表現が作成されます。

9.  **TodoService** コンストラクター内で、次の行をコメント解除します。

        // TODO:: Uncomment these lines to use Mobile Services
        client = new MobileServiceClient(Constants.ApplicationURL, Constants.ApplicationKey).WithFilter(this); 
        todoTable = client.GetTable<TodoItem>(); 

    これで、モバイル サービス クライアントのインスタンスと TodoItem テーブルのインスタンスが作成されます。

10. **TodoService** の **RefreshDataAsync** メソッド内で、次の行をコメント解除します。

        // TODO:: Uncomment these lines to use Mobile Services

    try
     {
     // このコードは、TodoItems テーブルを照会することによって、リスト ビュー内のエントリを更新します。
    // この照会では完了した TodoItems は除外されます
     Items = await todoTable
     .Where (todoItem =\> todoItem.Complete == false).ToListAsync();
     }
     catch (MobileServiceInvalidOperationException e)
     {
     Console.Error.WriteLine (@"ERROR {0}", e.Message);
     return null;
     }

    これで、まだ完了していないタスクをすべて返すクエリが作成されます。

11. **InsertTodoItemAsync** メソッドを探し、次の行をコメント解除します。

        await todoTable.InsertAsync(todoItem);

    このコードにより、挿入要求がモバイル サービスに送信されます。

12. **CompleteItemAsync** メソッドを探し、次の行をコメント解除します。

        await todoTable.UpdateAsync(item);

    このコードにより、完了マークが付けられた後で TodoItems が削除されます。

バックエンド ストレージのモバイル サービスを使用するようにアプリケーションを更新した後は、モバイル サービスに対してアプリケーションをテストします。

## <a name="test-app"></a><span class="short-header">アプリケーションのテスト</span>新しいモバイル サービスに対するアプリケーションのテスト

1.  Xamarin Studio で、展開先のエミュレーターまたはデバイスをプライマリ コンボボックスの 1 つから選択します。次に、**[Run]** メニューの **[Start Without Debugging]** を選択してアプリケーションを開始します。

    これにより、モバイル サービスから項目を照会する Azure モバイル サービス クライアント (Xamarin.iOS でビルドされています) が、実行されます。

2.  前と同様に、テキスト ボックスにテキストを入力し、**[+]** をクリックします。

    これにより、新しい項目が挿入としてモバイル サービスに送信されます。

3.  [管理ポータル][管理ポータル]で、**[モバイル サービス]** をクリックし、対象のモバイル サービスをクリックします。

4.  **[データ]** タブをクリックし、**[参照]** をクリックします。

    ![][4]

    **TodoItem** テーブルに、モバイル サービスによって生成された ID 値を持つデータが含まれ、アプリケーションの TodoItem クラスに対応してその列が自動的にテーブルに追加されていることに注目してください。

これで、Xamarin.iOS 向けの**データの使用**に関するチュートリアルは終了です。

## 完成したサンプルの入手

[完成したサンプル プロジェクト][Xamarin.iOS アプリケーション プロジェクトのダウンロード]をダウンロードします。**applicationURL** 変数と **applicationKey** 変数を独自の Azure 設定で更新してください。

## <a name="next-steps"> </a>次のステップ

このチュートリアルでは、iOS アプリでモバイル サービスのデータを操作できるようにするための基本について説明しました。

次は、このチュートリアルで作成した GetStartedWithData アプリケーションに基づく次のいずれかのチュートリアルを行うことをお勧めします。

-   [サーバー スクリプトを使用したモバイル サービスのデータの検証および変更][サーバー スクリプトを使用したモバイル サービスのデータの検証および変更]
    <br/>Mobile Services でサーバー スクリプトを使用して、アプリケーションから送信されたデータを検証および変更する方法について説明します。

-   [ページングを使用したモバイル サービス クエリの改善][ページングを使用したモバイル サービス クエリの改善]
    <br/>クエリ内でページングを使用して、1 回の要求で渡されるデータの量を制御する方法について説明します。

データ シリーズを完了した後は、iOS に関する他のチュートリアルを参照してください。

-   [認証の使用][認証の使用]
    <br/>アプリケーションのユーザーを認証する方法について説明します。

-   [プッシュ通知の使用][プッシュ通知の使用]
    <br/>Mobile Services を使用してアプリケーションにごく基本的なプッシュ通知を送信する方法について説明します。

<!-- Anchors. --> 
<!-- Images. --> 
<!-- URLs. TODO:: update download link, github link, and completed example project with new Xamarin.iOs projects -->

  [mobile-services-selector-get-started-data]: ../includes/mobile-services-selector-get-started-data.md
  [モバイル サービスの使用]: /ja-jp/develop/mobile/tutorials/get-started-xamarin-ios
  [Xamarin.iOS アプリケーション プロジェクトのダウンロード]: http://go.microsoft.com/fwlink/p/?LinkId=331302
  [モバイル サービスの作成]: #create-service
  [ストレージのデータ テーブルの追加]: #add-table
  [モバイル サービスを使用するためのアプリケーションの更新]: #update-app
  [モバイル サービスに対するアプリケーションのテスト]: #test-app
  [Azure モバイル サービス コンポーネント]: http://components.xamarin.com/view/azure-mobile-services/
  [XCode 5.0]: https://go.microsoft.com/fwLink/p/?LinkID=266532
  [Azure の無料評価版サイト]: http://www.windowsazure.com/ja-jp/pricing/free-trial/?WT.mc_id=A643EE910&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fja-jp%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-with-data-xamarin-ios%2F
  []: ./media/partner-xamarin-mobile-services-ios-get-started-data/mobile-quickstart-startup-ios.png
  [mobile-services-create-new-service-data]: ../includes/mobile-services-create-new-service-data.md
  [1]: ./media/partner-xamarin-mobile-services-ios-get-started-data/mobile-data-tab-empty.png
  [2]: ./media/partner-xamarin-mobile-services-ios-get-started-data/mobile-create-todoitem-table.png
  [3]: ./media/partner-xamarin-mobile-services-ios-get-started-data/mobile-dashboard-tab.png
  [管理ポータル]: https://manage.windowsazure.com/
  [4]: ./media/partner-xamarin-mobile-services-ios-get-started-data/mobile-todoitem-data-browse.png
  [サーバー スクリプトを使用したモバイル サービスのデータの検証および変更]: /ja-jp/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-ios
  [ページングを使用したモバイル サービス クエリの改善]: /ja-jp/develop/mobile/tutorials/add-paging-to-data-xamarin-ios
  [認証の使用]: /ja-jp/develop/mobile/tutorials/get-started-with-users-xamarin-ios
  [プッシュ通知の使用]: /ja-jp/develop/mobile/tutorials/get-started-with-push-xamarin-ios
