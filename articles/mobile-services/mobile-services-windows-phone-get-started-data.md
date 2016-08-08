<properties
	pageTitle="既存のアプリケーションへの Mobile Services の追加 (WP8) | Microsoft Azure"
	description="Azure Mobile Services Windows Phone 8 アプリからのデータを使用する方法について説明します。"
	services="mobile-services"
	documentationCenter="windows"
	authors="ggailey777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows-phone"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="07/21/2016"
	ms.author="glenga"/>


# 既存のアプリケーションへの Mobile Services の追加

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


[AZURE.INCLUDE [mobile-services-selector-get-started-data](../../includes/mobile-services-selector-get-started-data.md)]

##概要

このトピックでは、Azure Mobile Services を使用して Windows Phone 8 アプリケーションのデータを活用する方法について説明します。このチュートリアルでは、メモリにデータを格納するアプリケーションをダウンロードした後、新しいモバイル サービスを作成してそれをアプリケーションに統合します。その後、[Azure クラシック ポータル]にログインして、アプリケーションの実行中にデータに加えられた変更を表示します。

次のビデオで、Nick Harris によるこのチュートリアルのデモをご覧いただけます。
>[AZURE.VIDEO mobile-get-started-with-data-windows-phone]

##前提条件

+ Visual Studio 2012 Express for Windows Phone 8 および Windows 8 で実行する [Windows Phone 8 SDK] が必要になります。このチュートリアルを完了して、Windows Phone 8.1 アプリケーションを作成するには、Visual Studio 2013 Update 2 以降が必要です。

+ Azure アカウント。アカウントがない場合は、無料試用アカウントを数分で作成することができます。詳細については、[Azure の無料評価版サイト](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A756A2826&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Farticles%2Fdocumentation%2Fmobile-services-windows-phone-get-started-data%2F)を参照してください。

##<a name="download-app"></a>GetStartedWithData プロジェクトをダウンロードする

このチュートリアルは、Windows Phone Silverlight 8 アプリケーション プロジェクトである [GetStartedWithData app][Developer Code Samples site] アプリケーションに基づいています。

1. GetStartedWithData サンプル アプリケーション プロジェクトを[デベロッパー サンプル コード集のサイト]からダウンロードします。

	>[AZURE.NOTE]Windows Phone Silverlght 8.1 アプリケーションを作成するには、ダウンロードした Windows Phone Silverlight 8 アプリケーション プロジェクトの対象 OS を Windows Phone 8.1 に変更します。Windows Phone Store アプリケーションを作成するには、GetStartedWithData サンプル アプリケーション プロジェクトの [Windows Phone Store アプリケーション バージョン](http://go.microsoft.com/fwlink/p/?LinkId=397372)をダウンロードします。

2. Visual Studio で、ダウンロードしたプロジェクトを開き、MainPage.xaml.cs ファイルを確認します。

   	追加された **TodoItem** オブジェクトはメモリ内の **ObservableCollection&lt;TodoItem&gt;** に格納されます。

3. **F5** キーを押してプロジェクトをリビルドし、アプリケーションを開始します。

4. アプリケーションで、テキスト ボックスに任意のテキストを入力し、**[Save]** をクリックします。

   	![][0]

   	保存されたテキストが下のリストに表示されます。

##<a name="create-service"></a>Azure クラシック ポータルで新しいモバイル サービスを作成します。

[AZURE.INCLUDE [mobile-services-create-new-service-data](../../includes/mobile-services-create-new-service-data.md)]

##<a name="add-table"></a>新しいテーブルをモバイル サービスに追加する

[AZURE.INCLUDE [mobile-services-create-new-service-data-2](../../includes/mobile-services-create-new-service-data-2.md)]

##<a name="update-app"></a>モバイル サービスをデータ アクセスに使用するためにアプリケーションを更新する

モバイル サービスの準備が整ったら、ローカル コレクションの代わりに Mobile Services に項目を格納するようにアプリケーションを更新します。

1. Visual Studio の**ソリューション エクスプローラー**で、プロジェクト名を右クリックし、**[NuGet パッケージの管理]** をクリックします。

2. 左側のウィンドウで、[**オンライン**] カテゴリを選択し、`WindowsAzure.MobileServices` を探します。**Azure Mobile Services** パッケージで [**インストール**] をクリックし、使用許諾契約に同意します。

  	![][7]

  	これにより、Mobile Services クライアント ライブラリがプロジェクトに追加されます。

3. [Azure クラシック ポータル]で、**[Mobile Services]** をクリックし、先ほど作成したモバイル サービスをクリックします。

4. **[ダッシュボード]** タブをクリックし、**サイトの URL** をメモに記録します。次に、**[キーの管理]** をクリックし、**アプリケーション キー**をメモに記録します。

   	![][8]

  	これらの値は、アプリケーション コードからモバイル サービスにアクセスするときに必要になります。

5. Visual Studio で App.xaml.cs ファイルを開き、次の `using` ステートメントを追加またはコメント解除します。

       	using Microsoft.WindowsAzure.MobileServices;

6. この同じファイルで、**MobileService** 変数を定義する次のコードをコメント解除します。また、**MobileServiceClient** コンストラクターに、モバイル サービスで入手した URL とアプリケーション キーを (この順序で) 指定します。

		//public static MobileServiceClient MobileService = new MobileServiceClient(
        //    "AppUrl",
        //    "AppKey"
        //);

  	これで、モバイル サービスへのアクセスに使用される **MobileServiceClient** の新しいインスタンスが作成されます。

6. MainPage.cs ファイルで、次の `using` ステートメントを追加またはコメント解除します。

       	using Microsoft.WindowsAzure.MobileServices;
		using Newtonsoft.Json;

7. この DataModel フォルダーで、**TodoItem** クラス定義を次のコードに置き換えます。

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

バックエンド ストレージの Mobile Services を使用するようにアプリケーションを更新した後は、Mobile Services に対してアプリケーションをテストします。

##<a name="test-app"></a>新しいモバイル サービスに対するアプリケーションのテスト

1. Visual Studio で、F5 キーを押してアプリケーションを実行します。

2. 前と同様に、テキスト ボックスにテキストを入力し、**[Save]** をクリックします。

   	これにより、新しい項目が挿入としてモバイル サービスに送信されます。

3. [Azure クラシック ポータル]で、**[Mobile Services]** をクリックし、目的のモバイル サービスをクリックします。

4. **[データ]** タブをクリックし、**[参照]** をクリックします。

   	![][9]

   	**TodoItem** テーブルに、Mobile Services によって生成された ID 値を持つデータが含まれ、アプリケーションの TodoItem クラスに対応してその列が自動的にテーブルに追加されていることに注目してください。

これでチュートリアルは終了します。

## <a name="next-steps"> </a>次のステップ

このチュートリアルでは、Windows Phone 8 アプリで Mobile Services のデータを操作できるようにするための基本について説明しました。次に、以下のその他のトピックのいずれかを読むことを検討してください。

* [アプリへの認証の追加](mobile-services-windows-phone-get-started-users.md) <br/>アプリケーションのユーザーを認証する方法について説明します。

* [アプリへのプッシュ通知の追加](mobile-services-javascript-backend-windows-phone-get-started-push.md) <br/>Mobile Services を使用してアプリケーションにごく基本的なプッシュ通知を送信する方法について説明します。

* [Mobile Services C# の使用方法の概念リファレンス](mobile-services-dotnet-how-to-use-client-library.md) <br/>.NET で Mobile Services を使用する方法について説明します。

<!-- Anchors. -->
[Download the Windows Phone 8 app project]: #download-app
[Create the mobile service]: #create-service
[Add a data table for storage]: #add-table
[Update the app to use Mobile Services]: #update-app
[Test the app against Mobile Services]: #test-app
[Next Steps]: #next-steps

<!-- Images. -->
[0]: ./media/mobile-services-windows-phone-get-started-data/mobile-quickstart-startup-wp8.png
[7]: ./media/mobile-services-windows-phone-get-started-data/mobile-add-nuget-package-wp.png
[8]: ./media/mobile-services-windows-phone-get-started-data/mobile-dashboard-tab.png
[9]: ./media/mobile-services-windows-phone-get-started-data/mobile-todoitem-data-browse.png

<!-- URLs. -->

[Azure クラシック ポータル]: https://manage.windowsazure.com/
[Windows Phone 8 SDK]: http://go.microsoft.com/fwlink/p/?LinkID=268374
[Mobile Services SDK]: http://go.microsoft.com/fwlink/p/?LinkID=268375
[Developer Code Samples site]: http://go.microsoft.com/fwlink/p/?LinkId=271146
[デベロッパー サンプル コード集のサイト]: http://go.microsoft.com/fwlink/p/?LinkId=271146

<!---HONumber=AcomDC_0727_2016-->