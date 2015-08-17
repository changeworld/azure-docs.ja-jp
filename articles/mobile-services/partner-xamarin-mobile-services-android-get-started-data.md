<properties 
	pageTitle="既存のアプリケーションへの Mobile Services の追加 (Xamarin.Android) - Azure Mobile Services" 
	description="Azure Mobile Services Xamarin.Android アプリからのデータを保存する方法、およびデータにアクセスする方法について説明します。" 
	documentationCenter="xamarin" 
	authors="ggailey777" 
	manager="dwrede" 
	services="mobile-services" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-xamarin-android" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="04/24/2015" 
	ms.author="ggailey777"/>

# 既存のアプリケーションへの Mobile Services の追加

[AZURE.INCLUDE [mobile-services-selector-get-started-data](../../includes/mobile-services-selector-get-started-data.md)]

このトピックでは、Azure Mobile Services を使用して Xamarin.Android アプリのデータを活用する方法について説明します。このチュートリアルでは、メモリにデータを格納するアプリケーションをダウンロードした後、新しいモバイル サービスを作成してそれをアプリケーションに統合します。その後、Azure 管理ポータルにログインして、アプリケーションの実行中にデータに加えられた変更を表示します。

> [AZURE.NOTE]このチュートリアルの目的は、Azure を使用して Xamarin.Android アプリのデータを格納および取得できるようにする Mobile Services のしくみを説明することにあります。したがって、このトピックでは、モバイル サービスのクイック スタートで完了している手順の多くについても説明します。モバイル サービスを初めて使用する場合は、最初にチュートリアル「[モバイル サービスの使用](/develop/mobile/tutorials/get-started-xamarin-android)」を完了することをお勧めします。

このチュートリアルでは、次の基本的な手順について説明します。

1. [Xamarin.Android アプリ プロジェクトのダウンロード][GitHub] 
2. [モバイル サービスの作成]
3. [ストレージのデータ テーブルの追加]
4. [Mobile Services 使用するためのアプリケーションの更新]
5. [Mobile Services に対するアプリケーションのテスト]

> [AZURE.IMPORTANT]このチュートリアルを完了するには、Azure アカウントが必要です。アカウントがない場合は、無料の試用アカウントを数分で作成することができます。詳細については、[Azure の無料評価版サイト](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A9C9624B5"%20target="_blank)を参照してください。

このチュートリアルには、[Azure Mobile Services コンポーネント]、[Xamarin.Android]、および Android SDK 4.2 以降のバージョンが必要です。

> [AZURE.NOTE]ダウンロードした GetStartedWithData プロジェクトは、Android 4.2 以降のバージョンをターゲットとしています。ただし、Mobile Services SDK は、Android 2.2 以降で動作します。

## <a name="download-app"></a>GetStartedWithData プロジェクトをダウンロードする

このチュートリアルは、Xamarin.Android アプリケーションである [GetStartedWithData アプリケーション][GitHub]に基づいています。このアプリケーションの UI は、追加された項目がメモリにローカルに格納される点を除き、モバイル サービス Android のクイック スタートで生成したアプリケーションと同じです。

1. サンプル アプリケーション `GetStartedWithData` をダウンロードし、コンピューター上でファイルを展開します。 

2. Xamarin Studio で、**[File]**、**[Open]** の順にクリックし、GetStartedWithData サンプル プロジェクトを展開した場所を参照します。**XamarinTodoQuickStart.Android.sln** を選択して開きます。

3. **TodoActivity** クラスを探して開きます。

   	`// TODO::` コメントに、このアプリケーションをモバイル サービスで実行するために必要な手順が指定されている点に注目してください。

5. **[Run]** メニューの **[Start Without Debugging]** をクリックします。エミュレーターまたは接続されている USB Android デバイスを選択するよう求めるメッセージが表示されます。

	> [AZURE.IMPORTANT]このプロジェクトは、Android フォンまたは Android エミュレーターを使用して実行できます。Android フォンで実行するには、機器に固有の USB ドライバーをダウンロードする必要があります。
	> 
	> プロジェクトを Android エミュレーターで実行するには、Android Virtual Device (AVD) を 1 つ以上定義する必要があります。これらのデバイスを作成および管理するには、AVD Manager を使用します。

6. アプリケーションで、意味のあるテキスト (たとえば、「_チュートリアルの完了_」) を入力し、**[Add]** をクリックします。

   	![][13]

   	保存したテキストがメモリ内のコレクションに格納され、下のリストに表示されます。

## <a name="create-service"></a>管理ポータルで新規モバイル サービスを作成する

[AZURE.INCLUDE [mobile-services-create-new-service-data](../../includes/mobile-services-create-new-service-data.md)]

## <a name="add-table"></a>新しいテーブルをモバイル サービスに追加する

アプリケーション データを新しいモバイル サービスに格納できるようにするには、最初に新しいテーブルを作成する必要があります。

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

  	これにより、モバイル サービス SDK コンポーネントがプロジェクトに追加されます。

2. **AndroidManifest.xml** ファイルを開き、次のアクセス許可行があることを確認します。

		<uses-permission android:name="android.permission.INTERNET" />

  	これにより、アプリケーションが Azure のモバイル サービスにアクセスできるようになります。

3. **[ソリューション]** ウィンドウで、**TodoActivity** クラスを開き、次のコード行をコメント解除します。

		using Microsoft.WindowsAzure.MobileServices;
 
4. 次に、現在アプリケーションで使用されているメモリ内のリストを削除して、モバイル サービスで置き換えます。**TodoActivity** クラスで、既存の **todoItemList** リストを定義している次のコード行をコメント解除します。

		public List<TodoItem> todoItemList = new ArrayList<TodoItem>();

5. 前の手順を完了すると、プロジェクトのビルド エラーが示されます。`todoItemList` 変数が使用されている残りの 3 か所を検索し、該当するセクションをコメント解除します。

6. 次に、モバイル サービスを追加します。次のコード行をコメント解除します。

        private MobileServiceClient client; // Mobile Service Client references
        private IMobileServiceTable<TodoItem> todoTable; // Mobile Service Table used to access data   

7. 管理ポータルで、**[モバイル サービス]** をクリックし、先ほど作成したモバイル サービスをクリックします。

8. **[ダッシュボード]** タブをクリックし、**サイトの URL** をメモに記録します。次に、**[キーの管理]** をクリックし、**アプリケーション キー**をメモに記録します。

   	![][8]

  	これらの値は、アプリケーション コードからモバイル サービスにアクセスするときに必要になります。

9. **Constants** クラスで、次のメンバー変数をコメント解除します。

        public const string ApplicationURL = @"AppUrl";
        public const string ApplicationKey = @"AppKey";
        
10. 前の変数の **AppUrl** と **AppKey** を、前の管理ポータルから取得した値で置き換えます。

11. **OnCreate** メソッドで、**MobileServiceClient** 変数を定義している次のコード行をコメント解除します。

		// Create the Mobile Service Client instance, using the provided
		// Mobile Service URL and key
		client = new MobileServiceClient(
			Constants.ApplicationURL,
			Constants.ApplicationKey).WithFilter(filter);

		// Get the Mobile Service Table instance to use
		todoTable = client.GetTable<TodoItem>();    

  	これで、モバイル サービスにアクセスするために使用される MobileServiceClient の新しいインスタンスが作成されます。また、モバイル サービス内のデータ ストレージをプロキシ経由で接続するために使用される MobileServiceTable の新しいインスタンスも作成されます。

12. ファイルの末尾にある ProgressFilter クラスを探し、コメント解除します。このクラスは、MobileServiceClient がネットワーク操作を実行しているときに "読み込み中" インジケーターを表示します。

13. 次に示す **CheckItem** メソッドの行をコメント解除します。

		try {
			await todoTable.UpdateAsync(item);
			if (item.Complete)
				adapter.Remove(item);
		} catch (Exception e) {
			CreateAndShowDialog(e, "Error");
		}

   	これにより、項目の更新がモバイル サービスに送信され、チェックされた項目がアダプターから削除されます。
    
14. 次に示す **AddItem** メソッドの行をコメント解除します。
	
		try 
		{
			// Insert the new item
			await todoTable.InsertAsync(item);

			if (!item.Complete) 
				adapter.Add(item);			
		} 
		catch (Exception e) 
		{
			CreateAndShowDialog(e, "Error");
		}   		

  	このコードでは、新しい項目を作成し、それをリモート モバイル サービスのテーブルに挿入します。

15. 次に示す **RefreshItemsFromTableAsync** メソッドの行をコメント解除します。

		try {
			// Get the items that weren't marked as completed and add them in the adapter
			var list = await todoTable.Where(item => item.Complete == false).ToListAsync ();

			adapter.Clear();

			foreach (TodoItem current in list)
				adapter.Add(current);
		} 
        catch (Exception e) 
        {
			CreateAndShowDialog(e, "Error");
		}

	このコードでは、モバイル サービスに対するクエリを実行して、完了マークが付けられていないすべての項目を取得します。項目は、バインド用にアダプターに追加されます。
		

バックエンド ストレージのモバイル サービスを使用するようにアプリケーションを更新した後は、モバイル サービスに対してアプリケーションをテストします。

## <a name="test-app"></a>新しいモバイル サービスに対するアプリケーションのテスト

1. **[Run]** メニューの **[Start Without Debugging]** をクリックして、プロジェクトを開始します。既存のエミュレーター イメージまたは接続されている USB Android デバイスを選択するよう求めるメッセージが表示されます。

	これにより、モバイル サービスから項目を取得するクエリを、クライアント ライブラリを使用して送信するアプリケーションが Xamarin.Android でビルドされ、実行されます。

5. 前回と同様に、意味のあるテキストを入力し、**[Add]** をクリックします。

   	これにより、新しい項目が挿入としてモバイル サービスに送信されます。

3. [管理ポータル]で、**[モバイル サービス]** をクリックし、対象のモバイル サービスをクリックします。

4. **[データ]** タブをクリックし、**[参照]** をクリックします。

   	![][9]
  
   	**TodoItem** テーブルに、モバイル サービスによって生成された ID 値を持つデータが含まれ、アプリケーションの TodoItem クラスに対応してその列が自動的にテーブルに追加されていることに注目してください。

これで、Xamarin.Android 向けの**データの使用**に関するチュートリアルは終了です。

## 完成したサンプルの入手
[完成したサンプル プロジェクト]をダウンロードします。**applicationURL** 変数と **applicationKey** 変数を独自の Azure 設定で更新してください。

## <a name="next-steps"> </a>次のステップ

このチュートリアルでは、Xamarin.Android アプリでモバイル サービスのデータを操作できるようにするための基本について説明しました。

次は、このチュートリアルで作成した GetStartedWithData アプリケーションに基づく次のいずれかのチュートリアルを行うことをお勧めします。

* [スクリプトを使用したデータの検証と変更]Mobile Services でサーバー スクリプトを使用して、アプリケーションから送信されたデータを検証および変更する方法について説明します。

* [ページングを使用したクエリの改善]クエリ内でページングを使用して、1 回の要求で渡されるデータの量を制御する方法について説明します。

データ シリーズを完了した後は、次に示す、Xamarin.Android に関する他のチュートリアルを行うことをお勧めします。

* [認証の使用]アプリケーションのユーザーを認証する方法について説明します。

* [プッシュ通知の使用]Mobile Services を使用して、アプリケーションにごく基本的なプッシュ通知を送信する方法について説明します。

<!-- Anchors. -->

[Get the Windows Store app]: #download-app
[モバイル サービスの作成]: #create-service
[ストレージのデータ テーブルの追加]: #add-table
[Mobile Services 使用するためのアプリケーションの更新]: #update-app
[Mobile Services に対するアプリケーションのテスト]: #test-app
[Next Steps]: #next-steps

<!-- Images. -->





[5]: ./media/partner-xamarin-mobile-services-android-get-started-data/mobile-data-tab-empty.png
[6]: ./media/partner-xamarin-mobile-services-android-get-started-data/mobile-create-todoitem-table.png
[8]: ./media/partner-xamarin-mobile-services-android-get-started-data/mobile-dashboard-tab.png
[9]: ./media/partner-xamarin-mobile-services-android-get-started-data/mobile-todoitem-data-browse.png
[13]: ./media/partner-xamarin-mobile-services-android-get-started-data/mobile-quickstart-startup-android.png

<!-- URLs. TODO:: update 'Download the Android app project' download link, 'GitHub', completed project, etc. -->
[スクリプトを使用したデータの検証と変更]: /develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-android
[ページングを使用したクエリの改善]: /develop/mobile/tutorials/add-paging-to-data-xamarin-android
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-xamarin-android
[Get started with data]: /develop/mobile/tutorials/get-started-with-data-xamarin-android
[認証の使用]: /develop/mobile/tutorials/get-started-with-users-xamarin-android
[プッシュ通知の使用]: /develop/mobile/tutorials/get-started-with-push-xamarin-android

[Azure Management Portal]: https://manage.windowsazure.com/
[管理ポータル]: https://manage.windowsazure.com/
[Azure Mobile Services コンポーネント]: http://components.xamarin.com/view/azure-mobile-services/
[Download the Android app project]: http://www.google.com/
[GitHub]: http://go.microsoft.com/fwlink/p/?LinkId=331302
[Android SDK]: https://go.microsoft.com/fwLink/p/?LinkID=280125

[完成したサンプル プロジェクト]: http://go.microsoft.com/fwlink/p/?LinkId=331302
 

<!---HONumber=August15_HO6-->