<properties urlDisplayName="Using Offline Data" pageTitle="Mobile Services でのオフライン データの使用 (Xamarin Android) | モバイル デベロッパー センター" metakeywords="" description="Learn how to use Azure Mobile Services to cache and sync offline data in your Xamarin Android application" metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Using offline data in Mobile Services" authors="donnam" editor="wesmc" manager="dwrede"/>

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-xamarin-android" ms.devlang="dotnet" ms.topic="article" ms.date="09/25/2014" ms.author="donnam" />

# Mobile Services でのオフライン データの同期の使用

[WACOM.INCLUDE [mobile-services-selector-offline](../includes/mobile-services-selector-offline.md)]

このトピックでは、Azure Mobile Services のオフライン機能を使用する方法を説明します。Azure モバイル サービスのオフライン機能を使用すると、モバイル サービスに対してオフラインになっている状況でも、ローカル データベースとやり取りすることができます。再びオンライン状態に復帰したときに、オフライン機能により、ローカルの変更をモバイル サービスに同期させることができます。 

このチュートリアルでは、「 [モバイル サービスの使用]」または「[データの使用]」のチュートリアルで使用したアプリケーションを更新し、Azure Mobile Services のオフライン機能をサポートできるようにします。その後、切断されたオフラインの状況でデータを追加し、それらの項目をオンライン データベースに同期してから、Azure の管理ポータルにログインして、アプリケーションを実行したときにデータに加えた変更を表示します。

>[WACOM.NOTE] このチュートリアルの目的は、モバイル サービスが Windows ストア アプリのデータを Azure に格納および取得できるようにするしくみを説明することにあります。したがって、このトピックでは、モバイル サービスのクイック スタートで完了している手順の多くについても説明します。モバイル サービスを初めて使用する場合は、最初にチュートリアル「[モバイル サービスの使用]」を完了することをお勧めします。
>
> このチュートリアルを完了するには、Azure アカウントが必要です。アカウントがない場合、Azure 評価版にサインアップして、最大 10 件の無料モバイル サービスを入手できます。このサービスは評価終了後も使用できます。詳細については、<a href="http://www.windowsazure.com/ja-jp/pricing/free-trial/?WT.mc_id=AE564AB28" target="_blank">Azure の無料評価版サイト</a>を参照してください。 

このチュートリアルの完了済みの状態のプロジェクトは、[こちら](https://github.com/Azure/mobile-services-samples/tree/master/TodoOffline/Xamarin.Android)から入手できます。

このチュートリアルでは、次の基本的な手順について説明します。

1. [オフライン機能をサポートするようにアプリケーションを更新する]
2. [モバイル サービスに接続されているアプリケーションをテストする]

このチュートリアルには、次のものが必要です。

* Visual Studio と [Xamarin 拡張機能]または ****[Xamarin Studio] 
* 「[モバイル サービスの使用]」または「[データの使用]」を完了している。
* [Azure Mobile Services SDK バージョン 1.3.0-beta2][Mobile Services SDK Nuget]
* [Azure Mobile Services SQLite Store バージョン 1.0.0-beta2][SQLite store nuget]

>[WACOM.NOTE] 以下に示す手順では、Visual Studio 2012 以降と Xamarin 拡張機能を使用していると想定しています。Xamarin Studio を使用している場合は、組み込みの NuGet パッケージ マネージャーのサポートを使用します。

## <a name="enable-offline-app"></a>オフライン機能をサポートするようにアプリケーションを更新する

Azure Mobile Services のオフライン同期を使用すると、ネットワークにアクセスできない場合でも、エンド ユーザーはローカル データベースとのやり取りができます。アプリケーションでこれらの機能を使用するには、`MobileServiceClient.SyncContext` をローカル ストアに初期化します。その後、`IMobileServiceSyncTable`  インターフェイスを使用してテーブルを参照します。

1. Visual Studio で、「[モバイル サービスの使用]」または「[データの使用]」のチュートリアルで完成させたプロジェクトを開きます。ソリューション エクスプローラーで、**[コンポーネント]** の **Azure Mobile Services SDK** の参照を削除します。

2. パッケージ マネージャー コンソールで次のコマンドを使用して、Mobile Services SQLiteStore のプレリリース パッケージをインストールします。 
    
        install-package WindowsAzure.MobileServices.SQLiteStore -Pre

    これで、必須の依存関係もすべてインストールされます。
    
3. 参照ノードで、`System.IO`、`System.Runtime`、および `System.Threading.Tasks` への参照を削除します。

### ToDoActivity.cs を編集する

1. 宣言を追加します。

		using Microsoft.WindowsAzure.MobileServices.Sync;
		using Microsoft.WindowsAzure.MobileServices.SQLiteStore;
		using System.IO;

2. メンバー `ToDoActivity.toDoTable` の型を `IMobileServiceTable<>` から `IMobileServiceSyncTable<>` に変更します。

3. メソッド `OnCreate(Bundle)` で、メンバー `client` を初期化する行の後に次のコードを追加します。

	    // existing initializer
	    client = new MobileServiceClient (applicationURL, applicationKey, progressHandler);
	
		// new code to initialize the SQLite store
	    string path = Path.Combine(System.Environment.GetFolderPath(System.Environment.SpecialFolder.Personal), "test1.db");
	
	    if (!File.Exists(path))
	    {
	        File.Create(path).Dispose();
	    }

	    var store = new MobileServiceSQLiteStore(path);
	    store.DefineTable<ToDoItem>();
	
	    await client.SyncContext.InitializeAsync(store, new TodoSyncHandler(this));

4. 同じメソッド内で、`toDoTable` を初期化する行を変更して、`GetTable<>` の代わりにメソッド `GetSyncTable<>` を使用します。

		toDoTable = client.GetSyncTable <ToDoItem> ();

5. メソッド `OnRefreshItemsSelected` を変更して、`PushAsync`  および `PullAsync` の呼び出しを追加します。

		async void OnRefreshItemsSelected ()
		{
		    await client.SyncContext.PushAsync();
			await this.todoTable.PullAsync("todoItems", todoTable.CreateQuery());
		    await RefreshItemsFromTableAsync();
		}

### ToDoItem.cs を編集する 

1. using ステートメントを追加します。 

        using Microsoft.WindowsAzure.MobileServices; 


2. 次のメンバーをクラス "ToDoItem" に追加します。
 
		[Version]
		public string Version { get; set; }
		
		
		public override string ToString()
		{
		    return "Text: " + Text + "\nComplete: " + Complete + "\n";
		}

## <a name="test-online-app"></a>アプリケーションをテストする 

このセクションでは、ローカル ストアをモバイル サービス データベースに同期する `SyncAsync` メソッドをテストします。

1. Visual Studio で、**[実行]** をクリックしてプロジェクトをビルドし、このプロジェクトの既定である iPhone エミュレーターでアプリケーションを起動します。

2. アプリケーションの項目一覧が空であることに注意してください。前のセクションでコードを変更したため、アプリケーションは、モバイル サービスからではなく、ローカル ストアから項目を読み取ります。 

3. To Do リストに項目を追加します。

    ![][1]


4. Microsoft Azure 管理ポータルにログインし、モバイル サービスに対応するデータベースを参照します。開発中のサービスが、モバイル サービスとして JavaScript バックエンドを使用している場合は、モバイル サービスの **[データ]** タブからデータを参照できます。モバイル サービスとして .NET バックエンドを使用している場合は、SQL Azure 拡張機能内にあるデータベースに対応する **[管理]** ボタンをクリックして、テーブルに対してクエリを実行することができます。

    データベースとローカル ストアの間でデータがまだ同期されていないことを確認します。

5. アプリケーションで、**[更新]** をクリックします。これにより、アプリケーションは `MobileServiceClient.SyncContext.PushAsync` および `IMobileServiceSyncTable.PullAsync()` を呼び出し、次に `RefreshTodoItems` を呼び出して、ローカル ストアから取得した項目を使用してアプリケーション内のデータを更新します。 

    このプッシュ操作により、モバイル サービス データベースはストアからデータを受信します。`IMobileServicesSyncTable` の代わりに `MobileServiceClient.SyncContext` を実行し、同期コンテキストに関連付けられているすべてのテーブルに対して変更をプッシュします。これは、テーブル間にリレーションシップがある状況に対応することを目的としています。
    
    これに対して、プル操作は、指定されたテーブルからだけレコードを取得します。同期コンテキストでこのテーブルに対する操作が保留になると、PushAsync 操作が Mobile Services SDK によって暗示的に呼び出されます。
        
    ![][3] 



    ![][2]


  

##まとめ

モバイル サービスのオフライン機能をサポートするために、`IMobileServiceSyncTable` インターフェイスを使用して、ローカル ストアにより `MobileServiceClient.SyncContext` を初期化しました。この場合は、ローカル ストアは、SQLite データベースでした。

モバイル サービスに対する通常の CRUD 操作は、まるでアプリケーションが接続されているかのように機能しますが、すべての操作はローカル ストアに対して実施されます。

ローカル ストアをサーバーと同期しようとする場合は、`IMobileServiceSyncTable.PullAsync` と `MobileServiceClient.SyncContext.PushAsync` の各メソッドを使用しました。

*    変更内容をサーバーにプッシュするために、`IMobileServiceSyncContext.PushAsync()` を呼び出しました。このメソッドは、すべてのテーブルに対して変更をプッシュするため、同期テーブルではなく `IMobileServicesSyncContext` のメンバーです。

    何らかの方法で (CUD 操作により) ローカルで変更されたレコードだけが、サーバー宛てに送信されます。
   
*  サーバー上のテーブルからアプリケーションにデータをプルするために、`IMobileServiceSyncTable.PullAsync` を呼び出しました。

    プルは必ず、最初にプッシュを実行します。  

    このサンプルは、クエリ キーとクエリを指定できる **PullAsync()** のオーバーロードを使用します。クエリ キーは、増分同期に使用されます。Mobile Services SDK は、成功したプル操作ごとに、最終更新のタイムスタンプを追跡します。次回のプルでは、新しいレコードのみが取得されます。クエリ キーが指定されない場合は、同期テーブルに対して完全同期が実行されます。

## 次のステップ

このチュートリアルの完全版は、[GitHub のサンプル リポジトリ](https://github.com/Azure/mobile-services-samples/tree/master/TodoOffline/Xamarin.Android)のページからダウンロードできます。

<!--* [Handling conflicts with offline support for Mobile Services]
-->
* [Azure モバイル サービス向け Xamarin コンポーネント クライアントを使用する方法]

<!-- Anchors. -->
[オフライン機能をサポートするようにアプリケーションを更新する]: #enable-offline-app
[モバイル サービスに接続されているアプリケーションをテストする]: #test-online-app
[次のステップ]:#next-steps

<!-- Images -->
[1]: ./media/mobile-services-xamarin-android-get-started-offline-data/mobile-quickstart-startup-android.png
[2]: ./media/mobile-services-xamarin-android-get-started-offline-data/mobile-data-browse.png
[3]: ./media/mobile-services-xamarin-android-get-started-offline-data/mobile-quickstart-completed-android.png



<!-- URLs. -->
[モバイル サービスのオフライン サポートでの競合を処理する]: /ja-jp/documentation/articles/mobile-services-xamarin-android-handling-conflicts-offline-data/ 
[データの使用]: /ja-jp/documentation/articles/partner-xamarin-mobile-services-android-get-started-data/
[モバイル サービスの使用]: /ja-jp/documentation/articles/partner-xamarin-mobile-services-android-get-started/
[Azure モバイル サービス向け Xamarin コンポーネント クライアントを使用する方法]: /ja-jp/documentation/articles/partner-xamarin-mobile-services-how-to-use-client-library/

[Mobile Services SDK Nuget]: http://www.nuget.org/packages/WindowsAzure.MobileServices/1.3.0-beta2
[SQLite store nuget]: http://www.nuget.org/packages/WindowsAzure.MobileServices.SQLiteStore/1.0.0-beta2
[Xamarin Studio]: http://xamarin.com/download
[Xamarin 拡張機能]: http://xamarin.com/visual-studio
[Xamarin の NuGet アドインに関する GitHub ページ]: https://github.com/mrward/monodevelop-nuget-addin
