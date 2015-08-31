<properties
	pageTitle="既存のアプリケーションへの Mobile Services の追加 (Xamarin.iOS) | Microsoft Azure"
	description="Azure Mobile Services Xamarin.iOS アプリからのデータを保存する方法、およびデータにアクセスする方法について説明します。"
	documentationCenter="xamarin"
	authors="ggailey777"
	manager="dwrede"
	services="mobile-services"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-xamarin-ios"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="08/18/2015"
	ms.author="ggailey777"/>

# 既存のアプリケーションへの Mobile Services の追加

[AZURE.INCLUDE [mobile-services-selector-get-started-data](../../includes/mobile-services-selector-get-started-data.md)]

このトピックでは、Azure モバイル サービスを使用して Xamarin.iOS アプリのデータを活用する方法について説明します。このチュートリアルでは、メモリにデータを格納するアプリケーションをダウンロードした後、新しいモバイル サービスを作成してそれをアプリケーションに統合します。その後、Azure 管理ポータルにログインして、アプリケーションの実行中にデータに加えられた変更を表示します。

> [AZURE.NOTE]このチュートリアルの目的は、Azure を使用して Xamarin.iOS アプリのデータを格納および取得できるようにするモバイル サービスのしくみを説明することにあります。したがって、このトピックでは、モバイル サービスのクイック スタートで完了している手順の多くについても説明します。モバイル サービスを初めて使用する場合は、最初にチュートリアル「[モバイル サービスの使用](/develop/mobile/tutorials/get-started-xamarin-ios)」を完了することをお勧めします。

このチュートリアルでは、次の基本的な手順について説明します。

1. [Xamarin.iOS アプリケーション プロジェクトのダウンロード][GitHub]
2. [モバイル サービスの作成]
3. [ストレージのデータ テーブルの追加]
4. [Mobile Services 使用するためのアプリケーションの更新]
5. [Mobile Services に対するアプリケーションのテスト]

このチュートリアルには、[Azure Mobile Services コンポーネント]、[XCode 6.0][Install Xcode]、[Xamarin.iOS]、および iOS 7.0 以降のバージョンが必要です。

> [AZURE.IMPORTANT]このチュートリアルを完了するには、Azure アカウントが必要です。アカウントがない場合は、無料の試用アカウントを数分で作成することができます。詳細については、[Azure の無料評価版サイト](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fja-jp%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-with-data-xamarin-ios%2F"%20target="_blank)を参照してください。

## <a name="download-app"></a>GetStartedWithData プロジェクトをダウンロードする

このチュートリアルは、Xamarin.iOS アプリケーションである [GetStartedWithData アプリケーション][GitHub]に基づいています。このアプリケーションの UI は、追加された項目がメモリにローカルに格納される点を除き、モバイル サービス Xamarin.iOS のクイック スタートで生成したアプリケーションと同じです。

1. [GetStartedWithData アプリケーション][GitHub]をダウンロードします。

2. Xamarin Studio で、ダウンロードしたプロジェクトを開き、**TodoService** クラスを確認します。

   	複数の **// TODO** コメントに、このアプリケーションをモバイル サービスで実行するために必要な手順が指定されている点に注目してください。

3. **[Run]** メニューの **[Start Without Debugging]** を選択して、アプリケーションを開始します。

4. アプリケーションで、テキスト ボックスにテキストを入力し、**[+]** をクリックします。

   	![][0]

   	保存されたテキストが下のリストに表示されます。

## <a name="create-service"></a>管理ポータルで新規モバイル サービスを作成する

[AZURE.INCLUDE [mobile-services-create-new-service-data](../../includes/mobile-services-create-new-service-data.md)]

## <a name="add-table"></a>新しいテーブルをモバイル サービスに追加する

アプリケーション データを新しいモバイル サービスに格納できるようにするには、最初に新しいテーブルを関連付けられた SQL データベース インスタンスに作成する必要があります。

1. 管理ポータルで、**[モバイル サービス]** をクリックし、先ほど作成したモバイル サービスをクリックします。

2. **[データ]** タブをクリックし、**[作成]** をクリックします。

   	![][5]

   	**[新しいテーブルの作成]** ダイアログ ボックスが表示されます。

3. **[テーブル名]** に「_TodoItem_」と入力し、チェック ボタンをクリックします。

  	![][6]

  	これにより、既定のアクセス許可が設定された新しいストレージ テーブル **TodoItem** が作成されます。既定のアクセス許可とは、アプリケーションの任意のユーザーがテーブル内のデータにアクセスし、変更を加えることができることを表します。

    > [AZURE.NOTE]Mobile Services のクイック スタートでも同じテーブル名が使用されています。ただし、それぞれのテーブルは、特定のモバイル サービスに固有のスキーマ内で作成されています。これには、複数のモバイル サービスで同じデータベースを使用した場合にデータの競合を回避する目的があります。

4. 新しい **TodoItem** テーブルをクリックし、データ行がないことを確認します。

5. **[列]** タブをクリックし、自動的に作成される **id** 列のみがあることを確認します。

	  これは、Mobile Services のテーブルの最小要件です。

    > [AZURE.NOTE]モバイル サービスで動的スキーマを有効にすると、挿入操作または更新操作によって JSON オブジェクトがモバイル サービスに送信されるときに新しい列が自動的に作成されます。

これで、新しいモバイル サービスをアプリケーションのデータ ストレージとして使用する準備が整いました。

## <a name="update-app"></a>モバイル サービスをデータ アクセスに使用するためにアプリケーションを更新する

モバイル サービスの準備が整ったら、ローカル コレクションの代わりにモバイル サービスに項目を格納するようにアプリケーションを更新します。

1. **Azure Mobile Services** が Components フォルダーに含まれていない場合は、**[コンポーネント]** を右クリックし、**[コンポーネントの取得]** を選択して、**Azure Mobile Services** を探します。

2. Xamarin Studio の [Solution] ビューで、**TodoService** クラスを開き、次の `using` ステートメントをコメント解除します。

        using Microsoft.WindowsAzure.MobileServices;

3. 管理ポータルで、**[モバイル サービス]** をクリックし、先ほど作成したモバイル サービスをクリックします。

4. **[ダッシュボード]** タブをクリックし、**サイトの URL** をメモに記録します。次に、**[キーの管理]** をクリックし、**アプリケーション キー**をメモに記録します。

   	![][8]

5. **Constants** クラスで、次の定数をコメント解除します。

        public const string ApplicationURL = @"AppUrl";
        public const string ApplicationKey = @"AppKey";

6. 上記の定数の **AppUrl** と **AppKey** を、管理ポータルから取得した上記の値で置き換えます。

7. **TodoService** クラス内で、次のコード行をコメント解除します。

        private MobileServiceClient client;

   	これで、サービスに接続する MobileServiceClient を表すプロパティが作成されます。

8. **TodoService** クラス内で、次のコード行をコメント解除します。

        private IMobileServiceTable<TodoItem> todoTable;  

   	これで、モバイル サービス テーブルのプロパティ表現が作成されます。

9. **TodoService** コンストラクター内で、次の行をコメント解除します。

		// TODO:: Uncomment these lines to use Mobile Services
		client = new MobileServiceClient(Constants.ApplicationURL, Constants.ApplicationKey).WithFilter(this);
		todoTable = client.GetTable<TodoItem>();

    これで、モバイル サービス クライアントのインスタンスと TodoItem テーブルのインスタンスが作成されます。

10. **TodoService** の **RefreshDataAsync** メソッド内で、次の行をコメント解除します。

			// TODO:: Uncomment these lines to use Mobile Services
			try
	    {
				// This code refreshes the entries in the list view by querying the TodoItems table.
				// The query excludes completed TodoItems
				Items = await todoTable
					.Where (todoItem => todoItem.Complete == false).ToListAsync();
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

13. **CompleteItemAsync** メソッドを探し、次の行をコメント解除します。

		await todoTable.UpdateAsync(item);

   	このコードにより、完了マークが付けられた後で TodoItems が削除されます。

バックエンド ストレージのモバイル サービスを使用するようにアプリケーションを更新した後は、モバイル サービスに対してアプリケーションをテストします。

## <a name="test-app"></a>新しいモバイル サービスに対するアプリケーションのテスト

1. Xamarin Studio で、展開先のエミュレーターまたはデバイスをプライマリ コンボボックスの 1 つから選択します。次に、**[Run]** メニューの **[Start Without Debugging]** を選択してアプリケーションを開始します。

   	これにより、モバイル サービスから項目を照会する Azure モバイル サービス クライアント (Xamarin.iOS でビルドされています) が、実行されます。

2. 前と同様に、テキスト ボックスにテキストを入力し、**[+]** をクリックします。

   	これにより、新しい項目が挿入としてモバイル サービスに送信されます。

3. [管理ポータル]で、**[モバイル サービス]** をクリックし、対象のモバイル サービスをクリックします。

4. **[データ]** タブをクリックし、**[参照]** をクリックします。

   	![][9]

   	**TodoItem** テーブルに、モバイル サービスによって生成された ID 値を持つデータが含まれ、アプリケーションの TodoItem クラスに対応してその列が自動的にテーブルに追加されていることに注目してください。

これで、Xamarin.iOS 向けの**データの使用**に関するチュートリアルは終了です。

## 完成したサンプルの入手
[完成したサンプル プロジェクト]をダウンロードします。**applicationURL** 変数と **applicationKey** 変数を独自の Azure 設定で更新してください。

## <a name="next-steps"> </a>次のステップ

このチュートリアルでは、iOS アプリでモバイル サービスのデータを操作できるようにするための基本について説明しました。

次は、このチュートリアルで作成した GetStartedWithData アプリケーションに基づく次のいずれかのチュートリアルを行うことをお勧めします。

* [サーバー スクリプトを使用したモバイル サービスのデータの検証および変更] <br/>Mobile Services でサーバー スクリプトを使用して、アプリケーションから送信されたデータを検証および変更する方法について説明します。

* [ページングを使用したクエリの改善] <br/>クエリ内でページングを使用して、1 回の要求で渡されるデータの量を制御する方法について説明します。

データ シリーズを完了した後は、iOS に関する他のチュートリアルを参照してください。

* [認証の使用] <br/>アプリケーションのユーザーを認証する方法について説明します。

* [プッシュ通知の使用] <br/>Mobile Services を使用してアプリケーションにごく基本的なプッシュ通知を送信する方法について説明します。

<!-- Anchors. -->

[Get the Windows Store app]: #download-app
[モバイル サービスの作成]: #create-service
[ストレージのデータ テーブルの追加]: #add-table
[Mobile Services 使用するためのアプリケーションの更新]: #update-app
[Mobile Services に対するアプリケーションのテスト]: #test-app
[Next Steps]: #next-steps

<!-- Images. -->
[0]: ./media/partner-xamarin-mobile-services-ios-get-started-data/mobile-quickstart-startup-ios.png




[5]: ./media/partner-xamarin-mobile-services-ios-get-started-data/mobile-data-tab-empty.png
[6]: ./media/partner-xamarin-mobile-services-ios-get-started-data/mobile-create-todoitem-table.png
[8]: ./media/partner-xamarin-mobile-services-ios-get-started-data/mobile-dashboard-tab.png
[9]: ./media/partner-xamarin-mobile-services-ios-get-started-data/mobile-todoitem-data-browse.png

<!-- URLs. TODO:: update download link, github link, and completed example project with new Xamarin.iOs projects -->
[サーバー スクリプトを使用したモバイル サービスのデータの検証および変更]: /develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-ios
[ページングを使用したクエリの改善]: /develop/mobile/tutorials/add-paging-to-data-xamarin-ios
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-xamarin-ios
[Get started with data]: /develop/mobile/tutorials/get-started-with-data-xamarin-ios
[認証の使用]: /develop/mobile/tutorials/get-started-with-users-xamarin-ios
[プッシュ通知の使用]: /develop/mobile/tutorials/get-started-with-push-xamarin-ios

[Azure Management Portal]: https://manage.windowsazure.com/
[管理ポータル]: https://manage.windowsazure.com/
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Mobile Services iOS SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[GitHub]: http://go.microsoft.com/fwlink/p/?LinkId=331302
[GitHub repo]: http://go.microsoft.com/fwlink/p/?LinkId=268784
[Azure Mobile Services コンポーネント]: http://components.xamarin.com/view/azure-mobile-services/

[完成したサンプル プロジェクト]: http://go.microsoft.com/fwlink/p/?LinkId=331302
[Xamarin.iOS]: http://xamarin.com/download

<!---HONumber=August15_HO8-->