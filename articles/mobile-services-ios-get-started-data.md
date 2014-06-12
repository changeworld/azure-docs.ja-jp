<properties linkid="develop-mobile-tutorials-get-started-with-data-ios" urlDisplayName="データの使用" pageTitle="データの使用 (iOS) | モバイル デベロッパー センター" metaKeywords="Azure iOS のデータ, Azure モバイル サービスのデータ, " description="モバイル サービスを使用して iOS アプリのデータを活用する方法について説明します。" metaCanonical="" services="" documentationCenter="Mobile" title="モバイル サービスでのデータの使用" authors=""  solutions="" writer="glenga" manager="" editor=""  />




# モバイル サービスでのデータの使用
<div class="dev-center-tutorial-selector sublanding"> 
	<a href="/ja-jp/develop/mobile/tutorials/get-started-with-data-dotnet" title="Windows ストア C#">Windows ストア C##</a><a href="/ja-jp/develop/mobile/tutorials/get-started-with-data-js" title="Windows ストア JavaScript">Windows ストア JavaScript</a><a href="/ja-jp/develop/mobile/tutorials/get-started-with-data-wp8" title="Windows Phone">Windows Phone</a><a href="/ja-jp/develop/mobile/tutorials/get-started-with-data-ios" title="iOS" class="current">iOS</a><a href="/ja-jp/develop/mobile/tutorials/get-started-with-data-android" title="Android">Android</a><a href="/ja-jp/develop/mobile/tutorials/get-started-with-data-html" title="HTML">HTML</a><a href="/ja-jp/develop/mobile/tutorials/get-started-with-data-xamarin-ios" title="Xamarin.iOS">Xamarin.iOS</a><a href="/ja-jp/develop/mobile/tutorials/get-started-with-data-xamarin-android" title="Xamarin.Android">Xamarin.Android</a>
</div>	

このトピックでは、Azure のモバイル サービスを使用して iOS アプリのデータを活用する方法について説明します。このチュートリアルでは、メモリにデータを格納するアプリケーションをダウンロードした後、新しいモバイル サービスを作成してそれをアプリケーションに統合します。その後、Azure の管理ポータルにログインして、アプリケーションの実行中にデータに加えられた変更を表示します。

<div class="dev-callout"><b>注</b>
<p>このチュートリアルの目的は、モバイル サービスが iOS アプリケーションのデータを Azure に格納および取得できるようにするしくみを説明することにあります。したがって、このトピックでは、モバイル サービスのクイック スタートで完了している手順の多くについても説明します。モバイル サービスを初めて使用する場合は、最初にチュートリアル「<a href="/ja-jp/develop/mobile/tutorials/get-started-ios">モバイル サービスの使用</a>」を完了することをお勧めします。</p>
</div>

このチュートリアルでは、次の基本的な手順について説明します。

1. [iOS アプリ プロジェクトのダウンロード]
2. [モバイル サービスの作成]
3. [ストレージのデータ テーブルの追加]
4. [モバイル サービスを使用するためのアプリケーションの更新]
5. [モバイル サービスに対するアプリケーションのテスト]

このチュートリアルには、[モバイル サービス iOS SDK] と [XCode 4.5][Install Xcode] および iOS 5.0 以降のバージョンが必要です。

<div class="dev-callout"><strong></strong>注<p> このチュートリアルを完了するには、Azure アカウントが必要です。アカウントがない場合は、無料の試用アカウントを数分で作成することができます。詳細については、<a href="http://www.windowsazure.com/ja-jp/pricing/free-trial/?WT.mc_id=A756A2826&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fen-us%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-with-data-ios%2F" target="_blank">Azure の無料評価版サイト</a>を参照してください。</p></div>

<h2><a name="download-app"></a><span class="short-header">プロジェクトのダウンロード</span>GetStartedWithData プロジェクトをダウンロードする</h2>
 
このチュートリアルは、iOS アプリの [GetStartedWithData][GitHub] アプリケーションに基づいています。このアプリケーションの UI は、追加された項目がメモリにローカルに格納される点を除き、モバイル サービス iOS のクイック スタートで生成したアプリケーションと同じです。

1. GetStartedWithData [サンプル アプリ][GitHub]をダウンロードします。

2. Xcode で、ダウンロードしたプロジェクトを開き、QSTodoService.m ファイルを確認します。

   	8 つの **// TODO** コメントに、このアプリケーションをモバイル サービスで実行するために必要な手順が指定されている点に注目してください。

3. **[Run]** ボタン (または Command + R キー) を押してプロジェクトをリビルドし、アプリケーションを起動します。

4. アプリケーションで、テキスト ボックスにテキストを入力し、**[+]** をクリックします。

   	![][0]  

   	保存されたテキストが下のリストに表示されます。

<h2><a name="create-service"></a><span class="short-header">モバイル サービスの作成</span>管理ポータルでの新しいモバイル サービスの作成</h2>

[WACOM.INCLUDE [mobile-services-create-new-service-data](../includes/mobile-services-create-new-service-data.md)]

<h2><a name="add-table"></a><span class="short-header">新しいテーブルの追加</span>モバイル サービスへの新しいテーブルの追加</h2>

[WACOM.INCLUDE [mobile-services-create-new-service-data-2](../includes/mobile-services-create-new-service-data-2.md)]

<h2><a name="update-app"></a><span class="short-header">アプリケーションの更新</span>モバイル サービスをデータ アクセスに使用するためのアプリケーションの更新</h2>

モバイル サービスの準備が整ったら、ローカル コレクションの代わりにモバイル サービスに項目を格納するようにアプリケーションを更新します。

1. [モバイル サービス iOS SDK] をまだインストールしていない場合は、ここでインストールしてください。

2. Xcode の Project Navigator で、Quickstart フォルダーにある TodoService.m および TodoService.h ファイルを開き、次の import ステートメントを追加します。

        #import <WindowsAzureMobileServices/WindowsAzureMobileServices.h>  

3. ToDoService.h ファイルで、次のコメントがあるコード行を見つけます。

        // Create an MSClient property comment in the #interface declaration for the TodoService. 

   	このコメントの後ろに次のコード行を追加します。

        @property (nonatomic, strong)   MSClient *client;

   	これで、サービスに接続された MSClient を表すプロパティが作成されます。

4. TodoService.m ファイルで、次のコメントがあるコード行を見つけます。

        // Create an MSTable property for your items. 

   	このコメントの後ろに、@interface 宣言の内部に次のコード行を追加します。

        @property (nonatomic, strong)   MSTable *table;

   	これで、モバイル サービス テーブルのプロパティ表現が作成されます。

5. 管理ポータルで、**[モバイル サービス]** をクリックし、先ほど作成したモバイル サービスをクリックします。

6. **[ダッシュボード]** タブをクリックし、**サイトの URL** をメモに記録します。次に、**[キーの管理]** をクリックし、**アプリケーション キー**をメモに記録します。

   	![][8]

  	これらの値は、アプリケーション コードからモバイル サービスにアクセスするときに必要になります。

7. Xcode に戻り、TodoService.m ファイルを開いて、次のコメントがあるコード行を見つけます。

        // Initialize the Mobile Service client with your URL and key.

    このコメントの後ろに次のコード行を追加します。

        self.client = [MSClient clientWithApplicationURLString:@"APPURL" applicationKey:@"APPKEY"];

    これで、モバイル サービス クライアントのインスタンスが作成されます。

8. このコードの **APPURL** と **APPKEY** の値を、手順 6 で取得したモバイル サービスの URL とアプリケーション キーで置き換えます。

9. 次のコメントがあるコード行を見つけます。

        // Create an MSTable instance to allow us to work with the TodoItem table.

    このコメントの後ろに次のコード行を追加します。

        self.table = [self.client tableWithName:@"TodoItem"];

    これで、TodoItem テーブルのインスタンスが作成されます。

10. 次のコメントがあるコード行を見つけます。

 	    // Create a predicate that finds items where complete is false comment in the refreshDataOnSuccess method. 

    このコメントの後ろに次のコード行を追加します。

        NSPredicate * predicate = [NSPredicate predicateWithFormat:@"complete == NO"];

    これで、まだ完了していないタスクをすべて返すクエリが作成されます。

11. 次のコメントがあるコード行を見つけます。

        // Query the TodoItem table and update the items property with the results from the service.

   	そのコメントとそれに続く **completion** ブロックの呼び出しを、次のコードに置き換えます。

        // Query the TodoItem table and update the items property with the results from the service
        [self.table readWithPredicate:predicate completion:^(NSArray *results, NSInteger totalCount, NSError *error) 
        {
           self.items = [results mutableCopy];
           completion();
        }]; 

12. **addItem** メソッドを見つけ、メソッドの本体を次のコードに置き換えます。

        // Insert the item into the TodoItem table and add to the items array on completion
        [self.table insert:item completion:^(NSDictionary *result, NSError *error) {
            NSUInteger index = [items count];
            [(NSMutableArray *)items insertObject:item atIndex:index];
 
            // Let the caller know that we finished
            completion(index);
        }];

    このコードにより、挿入要求がモバイル サービスに送信されます。

13. **completeItem** メソッドを見つけ、メソッドの本体を次のコードに置き換えます。

        // Update the item in the TodoItem table and remove from the items array on completion
        [self.table update:mutable completion:^(NSDictionary *item, NSError *error) {
    
            // TODO
            // Get a fresh index in case the list has changed
            NSUInteger index = [items indexOfObjectIdenticalTo:mutable];
    
            [mutableItems removeObjectAtIndex:index];
    
            // Let the caller know that we have finished
            completion(index);
	    }]; 

   	このコードにより、完了マークが付けられた後で TodoItems が削除されます。

バックエンド ストレージのモバイル サービスを使用するようにアプリケーションを更新した後は、モバイル サービスに対してアプリケーションをテストします。

<h2><a name="test-app"></a><span class="short-header">アプリケーションのテスト</span>新しいモバイル サービスに対するアプリケーションのテスト</h2>

1. Xcode で、iPhone または iPad にデプロイするエミュレーターを選択し、**[Run]** ボタン (または Command + R キー) を押してプロジェクトをリビルドし、アプリケーションを起動します。

   	これにより、モバイル サービスから項目にクエリを行う Azure のモバイル サービス クライアントが、iOS SDK でビルドされ、実行されます。

2. 前と同様に、テキスト ボックスにテキストを入力し、[**+**] をクリックします。

   	これにより、新しい項目が挿入としてモバイル サービスに送信されます。

3. [管理ポータル]で、**[モバイル サービス]** をクリックし、対象のモバイル サービスをクリックします。

4. **[データ]** タブをクリックし、**[参照]** をクリックします。

   	![][9]
  
   	**TodoItem** テーブルに、モバイル サービスによって生成された ID 値を持つデータが含まれ、アプリケーションの TodoItem クラスに対応してその列が自動的にテーブルに追加されていることに注目してください。

これで、iOS 向けの**データの使用**に関するチュートリアルは終了です。

## <a name="next-steps"> </a>次のステップ

このチュートリアルでは、iOS アプリケーションでモバイル サービスのデータを操作できるようにするための基本について説明しました。

次は、このチュートリアルで作成した GetStartedWithData アプリケーションに基づく次のいずれかのチュートリアルを行うことをお勧めします。

* [サーバー スクリプトを使用したモバイル サービスのデータの検証および変更]
  <br/>モバイル サービスでサーバー スクリプトを使用して、アプリケーションから送信されたデータを検証および変更する方法について説明します。

* [ページングを使用したモバイル サービス クエリの改善]
  <br/>クエリ内でページングを使用して、単一の要求で渡されるデータの量を制御する方法について説明します。

データ シリーズを完了した後は、iOS に関する他のチュートリアルを参照してください。

* [認証の使用]
	<br/>アプリケーションのユーザーを認証する方法について説明します。

* [プッシュ通知の使用]
  <br/>モバイル サービスを使用してアプリケーションにごく基本的なプッシュ通知を送信する方法について説明します。

<!-- Anchors. -->
[iOS アプリ プロジェクトのダウンロード]: #download-app
[モバイル サービスの作成]: #create-service
[ストレージのデータ テーブルの追加]: #add-table
[モバイル サービスを使用するためのアプリケーションの更新]: #update-app
[モバイル サービスに対するアプリケーションのテスト]: #test-app
[次のステップ]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-ios-get-started-data/mobile-quickstart-startup-ios.png







[8]: ./media/mobile-services-ios-get-started-data/mobile-dashboard-tab.png
[9]: ./media/mobile-services-ios-get-started-data/mobile-todoitem-data-browse.png



<!-- URLs. -->
[サーバー スクリプトを使用したモバイル サービスのデータの検証および変更]: /ja-jp/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet
[ページングを使用したモバイル サービス クエリの改善]: /ja-jp/develop/mobile/tutorials/add-paging-to-data-ios
[モバイル サービスの使用]: /ja-jp/develop/mobile/tutorials/get-started-ios
[データの使用]: /ja-jp/develop/mobile/tutorials/get-started-with-data-ios
[認証の使用]: /ja-jp/develop/mobile/tutorials/get-started-with-users-ios
[プッシュ通知の使用]: /ja-jp/develop/mobile/tutorials/get-started-with-push-ios

[Azure の管理ポータル]: https://manage.windowsazure.com/
[管理ポータル]: https://manage.windowsazure.com/
[Xcode のインストール]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[モバイル サービス iOS SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[GitHub]:  http://go.microsoft.com/fwlink/p/?LinkId=268622
[GitHub のリポジトリ]: http://go.microsoft.com/fwlink/p/?LinkId=268784

