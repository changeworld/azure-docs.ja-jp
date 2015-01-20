<properties urlDisplayName="Get Started with Data" pageTitle="データの使用 (WP8) - Azure モバイル サービス" metaKeywords="" description="Azure Mobile Services Windows Phone 8 アプリからのデータを使用する方法について説明します。" metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with data in Mobile Services" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-phone" ms.devlang="dotnet" ms.topic="article" ms.date="09/19/2014" ms.author="glenga" />


# 既存のアプリケーションへの Mobile Services の追加

[WACOM.INCLUDE [mobile-services-selector-get-started-data-legacy](../includes/mobile-services-selector-get-started-data-legacy.md)]

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">

<p>このトピックでは、Azure のモバイル サービスを使用して Windows Phone 8 アプリケーションのデータを活用する方法について説明します。このチュートリアルでは、メモリにデータを格納するアプリケーションをダウンロードした後、新しいモバイル サービスを作成してそれをアプリケーションに統合します。その後、Azure 管理ポータルにログインして、アプリケーションの実行中にデータに加えられた変更を表示します。</p>
</div>
<div class="dev-onpage-video-wrapper"><a href="http://go.microsoft.com/fwlink/?LinkID=298628" target="_blank" class="label">チュートリアルを見る</a> <a style="background-image: url('/media/devcenter/mobile/videos/mobile-wp8-get-started-data-180x120.png') !important;" href="http://go.microsoft.com/fwlink/?LinkID=298628" target="_blank" class="dev-onpage-video"><span class="icon">ビデオを再生する</span></a> <span class="time">12:54</span></div>
</div>

このチュートリアルでは、次の基本的な手順について説明します。

1. [Windows Phone 8 アプリ プロジェクトのダウンロード]
2. [モバイル サービスの作成]
3. [ストレージへのデータ テーブルの追加]
4. [モバイル サービスを使用するためのアプリケーションの更新]
5. [モバイル サービスに対するアプリケーションのテスト]

このチュートリアルでは、Visual Studio 2012 Express for Windows Phone 8 および Windows 8 で実行する [Windows Phone 8 SDK] が必要になります。このチュートリアルを完了して、Windows Phone 8.1 アプリケーションを作成するには、Visual Studio 2013 Update 2 以降が必要です。

>[WACOM.NOTE]このチュートリアルを完了するには、Azure アカウントが必要です。アカウントがない場合は、無料の試用アカウントを数分で作成することができます。詳細については、「 <a href="http://www.windowsazure.com/ja-jp/pricing/free-trial/?WT.mc_id=A756A2826&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fja-jp%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-with-data-wp8%2F" target="_blank">Azure 無料評価版</a>」を参照してください。

##<a name="download-app"></a>GetStartedWithData プロジェクトをダウンロードする

このチュートリアルは、Windows Phone Silverlight 8 アプリケーション プロジェクトである [GetStartedWithData app アプリケーション][Developer Code Samples site] に基づいています。  

1. GetStartedWithData サンプル アプリケーション プロジェクトを [デベロッパー サンプル コード集のサイト] からダウンロードします。 

	>[WACOM.NOTE]Windows Phone Silverlght 8.1 アプリケーションを作成するには、ダウンロードした Windows Phone Silverlight 8 アプリケーション プロジェクトの対象 OS を Windows Phone 8.1 に変更します。Windows Phone Store アプリケーションを作成するには、GetStartedWithData サンプル アプリケーション プロジェクトの [Windows Phone Store アプリケーション バージョン](http://go.microsoft.com/fwlink/p/?LinkId=397372) をダウンロードします。 

2. Visual Studio で、ダウンロードしたプロジェクトを開き、MainPage.xaml.cs ファイルを確認します。

   	追加された **TodoItem** オブジェクトはメモリ内の **ObservableCollection<TodoItem>** に格納されます。

3. **F5** キーを押してプロジェクトをリビルドし、アプリケーションを開始します。

4. アプリケーションで、テキスト ボックスに任意のテキストを入力し、**[Save]** をクリックします。

   	![][0]  

   	保存されたテキストが下のリストに表示されます。

<h2><a name="create-service"></a>管理ポータルで新規モバイル サービスを作成する</h2>

[WACOM.INCLUDE [mobile-services-create-new-service-data](../includes/mobile-services-create-new-service-data.md)]

<h2><a name="add-table"></a>新しいテーブルをモバイル サービスに追加する</h2>

[WACOM.INCLUDE [mobile-services-create-new-service-data-2](../includes/mobile-services-create-new-service-data-2.md)]

<h2><a name="update-app"></a>モバイル サービスをデータ アクセスに使用するためにアプリケーションを更新する</h2>

モバイル サービスの準備が整ったら、ローカル コレクションの代わりにモバイル サービスに項目を格納するようにアプリケーションを更新します。 

1. Visual Studio の**ソリューション エクスプローラー**で、プロジェクト名を右クリックし、**[NuGet パッケージの管理]** をクリックします。

2. 左側のウィンドウで、**[オンライン]** カテゴリを選択し、WindowsAzure.MobileServices を探します。**Azure モバイル サービス** パッケージで **[インストール]** をクリックし、使用許諾契約に同意します。

  	![][7]

  	これにより、モバイル サービス クライアント ライブラリがプロジェクトに追加されます。

3. 管理ポータルで、**[モバイル サービス]** をクリックし、先ほど作成したモバイル サービスをクリックします。

4. **[ダッシュボード]** タブをクリックし、**サイトの URL** をメモに記録します。次に、**[キーの管理]** をクリックし、**アプリケーション キー**をメモに記録します。

   	![][8]

  	これらの値は、アプリケーション コードからモバイル サービスにアクセスするときに必要になります。

5. Visual Studio で App.xaml.cs ファイルを開き、次の using ステートメントを追加またはコメント解除します。

       	using Microsoft.WindowsAzure.MobileServices;

6. この同じファイルで、**MobileService** 変数を定義するコードをコメント解除します。また、**MobileServiceClient** コンストラクターに、モバイル サービスで入手した URL とアプリケーション キーを (この順序で) 指定します。

		//public static MobileServiceClient MobileService = new MobileServiceClient( 
        //    "AppUrl", 
        //    "AppKey" 
        //); 

  	これで、モバイル サービスへのアクセスに使用される **MobileServiceClient** の新しいインスタンスが作成されます。

6. MainPage.xaml.cs ファイルで、次の using ステートメントを追加またはコメント解除します。

       	using Microsoft.WindowsAzure.MobileServices;
		using Newtonsoft.Json;

7. この同じファイルで、**TodoItem** クラス定義を次のコードに置き換えます。

        public class TodoItem
        {
            public string Id { get; set; }

            [JsonProperty(PropertyName = "text")]
            public string Text { get; set; }

            [JsonProperty(PropertyName = "complete")]
            public bool Complete { get; set; }
        }

7. 既存の **items** コレクションを定義する行をコメント アウトし、次の行をコメント解除します。

        private MobileServiceCollection<TodoItem, TodoItem> items;
        private IMobileServiceTable<TodoItem> todoTable = 
			App.MobileService.GetTable<TodoItem>();

   	このコードは、モバイル サービス対応のバインディング コレクション (**items**) と SQL データベース テーブルのプロキシ クラス **TodoItem** (**todoTable**) を作成します。 

7. **InsertTodoItem** メソッド内で、**TodoItem**.**Id** プロパティを設定するコード行を削除し、**async** 修飾子をメソッドに追加して、次のコード行をコメント解除します。

        await todoTable.InsertAsync(todoItem);

  	このコードでは、新しい項目をテーブルに挿入します。

8. **RefreshTodoItems** メソッド内で、**async** 修飾子をメソッドに追加して、次のコード行をコメント解除します。

        items = await todoTable.ToCollectionAsync();

   	これにより、todoTable 内で、モバイル サービスから返されたすべての TodoItem オブジェクトが格納される項目のコレクションへのバインディングが設定されます。 

9. **UpdateCheckedTodoItem** メソッド内で、**async** 修飾子をメソッドに追加して、次のコード行をコメント解除します。

         await todoTable.UpdateAsync(item);

   	これにより、項目の更新がモバイル サービスに送信されます。

バックエンド ストレージのモバイル サービスを使用するようにアプリケーションを更新した後は、モバイル サービスに対してアプリケーションをテストします。

<h2><a name="test-app"></a>新しいモバイル サービスに対するアプリケーションのテスト</h2>

1. Visual Studio で、F5 キーを押してアプリケーションを実行します。

2. 前と同様に、テキスト ボックスにテキストを入力し、**[Save]** をクリックします。

   	これにより、新しい項目が挿入としてモバイル サービスに送信されます。

3. [管理ポータル]で、**[モバイル サービス]** をクリックし、対象のモバイル サービスをクリックします。

4. **[データ]** タブをクリックし、**[参照]** をクリックします。

   	![][9]
  
   	**TodoItem** テーブルに、モバイル サービスによって生成された ID 値を持つデータが含まれ、アプリケーションの TodoItem クラスに対応してその列が自動的にテーブルに追加されていることに注目してください。

これで、Windows Phone 8 向けの**データの使用**に関するチュートリアルは終了です。

## <a name="next-steps"> </a>次のステップ

このチュートリアルでは、Windows Phone 8 アプリでモバイル サービスのデータを操作できるようにするための基本について説明しました。次は、このチュートリアルで作成した GetStartedWithData アプリケーションに基づく次のチュートリアルを行うことをお勧めします。

* [スクリプトでデータ検証および変更]
  <br/>モバイル サービスでサーバー スクリプトを使用して、アプリケーションから送信されたデータを検証および変更する方法について説明します。

* [ページングを使用したクエリの改善]
  <br/>クエリ内でページングを使用して、単一の要求で渡されるデータの量を制御する方法について説明します。

データ シリーズを完了した後は、次に示すいずれかの Windows Phone 8 チュートリアルを行うことができます。

* [認証の使用]
  <br/>アプリケーションのユーザーを認証する方法について説明します。

* [プッシュ通知の使用]
  <br/>モバイル サービスを使用してアプリケーションにごく基本的なプッシュ通知を送信する方法について説明します。
 
<!-- Anchors. -->
[Windows Phone 8 アプリ プロジェクトのダウンロード]: #download-app
[モバイル サービスの作成]: #create-service
[ストレージへのデータ テーブルの追加]: #add-table
[モバイル サービスを使用するためのアプリケーションの更新]: #update-app
[モバイル サービスに対するアプリケーションのテスト]: #test-app
[次のステップ]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-windows-phone-get-started-data/mobile-quickstart-startup-wp8.png






[7]: ./media/mobile-services-windows-phone-get-started-data/mobile-add-nuget-package-wp.png
[8]: ./media/mobile-services-windows-phone-get-started-data/mobile-dashboard-tab.png
[9]: ./media/mobile-services-windows-phone-get-started-data/mobile-todoitem-data-browse.png



<!-- URLs. -->
[スクリプトでデータ検証および変更]: /ja-jp/develop/mobile/tutorials/validate-modify-and-augment-data-wp8
[ページングを使用したクエリの改善]: /ja-jp/develop/mobile/tutorials/add-paging-to-data-wp8
[モバイル サービスの使用]: /ja-jp/develop/mobile/tutorials/get-started-wp8
[データの使用]: /ja-jp/develop/mobile/tutorials/get-started-with-data-wp8
[認証の使用]: /ja-jp/develop/mobile/tutorials/get-started-with-users-wp8
[プッシュ通知の使用]: /ja-jp/develop/mobile/tutorials/get-started-with-push-wp8

[Azure 管理ポータル]: https://manage.windowsazure.com/
[管理ポータル]: https://manage.windowsazure.com/
[Windows Phone 8 SDK]: http://go.microsoft.com/fwlink/p/?LinkID=268374
[Mobile Services SDK]: http://go.microsoft.com/fwlink/p/?LinkID=268375
[デベロッパー サンプル コード集のサイト]:  http://go.microsoft.com/fwlink/p/?LinkId=271146

<!--HONumber=35.2-->
